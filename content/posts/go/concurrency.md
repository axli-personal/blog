---
title: "Golang并发"
date: 2023-08-01T14:00:00+08:00
summary: "关于Golang并发的研究"
categories: ["Go"]
---

# 生产者消费者模型

Q: 写一个队列, 让生产者和消费者解耦, 并且队列长度可以调整.

针对这个问题, 首先可以先考虑定长的队列, 因为当队列大小变化时可能需要进行数据拷贝.

关于循环队列当`head == tail`时表示队列为空, 当`nextHead == tail`时表示队列已满.

在下面的实现中提供了两种接口, 一种当操作失败时返回错误; 另一种当操作失败时阻塞, 并在被唤醒后重试.

```go
type Channel struct {
	lock        *sync.Mutex
	buf         []int
	head        int
	tail        int
	consumeCond *sync.Cond
	produceCond *sync.Cond
}

func NewChannel(size int) *Channel {
	channel := &Channel{
		buf:  make([]int, size+1),
		head: 0,
		tail: 0,
	}

	channel.lock = new(sync.Mutex)
	channel.consumeCond = sync.NewCond(channel.lock)
	channel.produceCond = sync.NewCond(channel.lock)

	return channel
}

func (c *Channel) nextHead() int {
	return (c.head + 1) % len(c.buf)
}

func (c *Channel) nextTail() int {
	return (c.tail + 1) % len(c.buf)
}

func (c *Channel) empty() bool {
	return c.head == c.tail
}

func (c *Channel) full() bool {
	return c.nextHead() == c.tail
}

func (c *Channel) TryProduce(data int) error {
	c.lock.Lock()
	defer c.lock.Unlock()

	if c.full() {
		return errors.New("channel is full when produce data")
	}

	c.buf[c.head] = data
	c.head = c.nextHead()

	return nil
}

func (c *Channel) TryConsume() (int, error) {
	c.lock.Lock()
	defer c.lock.Unlock()

	if c.empty() {
		return 0, errors.New("channel is empty when consume data")
	}

	data := c.buf[c.tail]
	c.tail = c.nextTail()

	return data, nil
}

func (c *Channel) Produce(data int) {
	c.lock.Lock()
	defer c.lock.Unlock()

	for c.full() {
		c.produceCond.Wait()
	}

	c.buf[c.head] = data
	c.head = c.nextHead()

	c.consumeCond.Signal()
}

func (c *Channel) Consume() int {
	c.lock.Lock()
	defer c.lock.Unlock()

	for c.empty() {
		c.consumeCond.Wait()
	}

	data := c.buf[c.tail]
	c.tail = c.nextTail()

	c.produceCond.Signal()

	return data
}
```

另外队列还可以加入topic的功能, 这样就实现了消息总线, 在锁方面应该还有优化空间.

在调整队列长度时, 我们也可以借鉴kafka, 设计成有分区概念的队列.
