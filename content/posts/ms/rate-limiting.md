---
title: "限流"
date: 2023-07-01T17:17:09+08:00
categories: ["Microservices"]
---

限流已经成为微服务中很重要的一块内容, 本次我们总计一下常见的限流策略和算法.

<!--more-->

## 限流策略

### 用户限流

每个用户每秒只能请求`N`次.

### 并发限流

每个服务只能并发处`N`个请求, 实现并发限流有两种模式:

* 在请求过多时, 返回429(Too Many Requests).
* 在请求过多时, 将请求放在队列中, 等待后续处理.

### 优先级限流

每个服务会**预留**一部分资源用于处理高优先级请求.

当高优先级请求过多时, 会逐步切断低优先级流量, 使更多的资源能够用来处理高优先级请求.

## 限流算法

常见的限流算法: 滑动窗口、漏桶.

### 漏桶算法

[Uber Ratelimit](https://github.com/uber-go/ratelimit)实现了基于请求时间的漏桶算法, 没有使用定时器.

```go
func (t *atomicInt64Limiter) Take() time.Time {
	var (
		issueTime int64
		now       int64
	)
	for {
		now = t.clock.Now().UnixNano()
		lastIssueTime := atomic.LoadInt64(&t.state)

		switch {
		case lastIssueTime == 0 || (t.maxSlack == 0 && now-lastIssueTime > int64(t.perRequest)):
			issueTime = now
		case t.maxSlack > 0 && now-lastIssueTime > int64(t.maxSlack):
			issueTime = now - int64(t.maxSlack)
		default:
			issueTime = lastIssueTime + int64(t.perRequest)
		}

		if atomic.CompareAndSwapInt64(&t.state, lastIssueTime, issueTime) {
			break
		}
	}

	sleepDuration := time.Duration(issueTime - now)
	if sleepDuration > 0 {
		t.clock.Sleep(sleepDuration)
		return time.Unix(0, issueTime)
	}
	
	return time.Unix(0, now)
}
```

### 自适应限流

[Kratos Ratelimit](https://go-kratos.dev/docs/component/middleware/ratelimit)实现了一个自适应限流算法.

`Allow`主要维护维护一些指标, 包括: RT (Response Time)、Pass、InFlight.

```go
func (l *BBR) Allow() (ratelimit.DoneFunc, error) {
	if l.shouldDrop() {
		return nil, ratelimit.ErrLimitExceed
	}

	atomic.AddInt64(&l.inFlight, 1)
	start := time.Now().UnixNano()

	return func(ratelimit.DoneInfo) {
		if rt := int64(math.Ceil(float64(time.Now().UnixNano()-start)) / float64(time.Millisecond)); rt > 0 {
			l.rtStat.Add(rt)
		}
		atomic.AddInt64(&l.inFlight, -1)
		l.passStat.Add(1)
	}, nil
}
```

`shouldDrop`主要根据CPU使用率和`InFlight`判断是否能够通过.

```go
func (l *BBR) shouldDrop() bool {
	now := time.Duration(time.Now().UnixNano())
	if l.cpu() < l.opts.CPUThreshold {
        // 一般在低CPU使用率的情况下允许通过.
		prevDropTime, _ := l.prevDropTime.Load().(time.Duration)
		if prevDropTime == 0 {
			return false
		}
        // 如果最近丢弃过消息, 需要判断正在处理的请求是否过多.
		if time.Duration(now-prevDropTime) <= time.Second {
			inFlight := atomic.LoadInt64(&l.inFlight)
			return inFlight > 1 && inFlight > l.maxInFlight()
		}

        // 如果已经在低CPU使用率下运行了一段时间, 则不需要判断正在处理的请求是否过多.
		l.prevDropTime.Store(time.Duration(0))
		return false
	}

	// 在高CPU使用率的情况下需要判断正在处理的请求是否过多.
	inFlight := atomic.LoadInt64(&l.inFlight)
	drop := inFlight > 1 && inFlight > l.maxInFlight()
	if drop {
		prevDrop, _ := l.prevDropTime.Load().(time.Duration)
		if prevDrop != 0 {
			return drop
		}
		l.prevDropTime.Store(now)
	}

	return drop
}
```

`maxInFlight`主要用于控制正在处理的请求数, 最大限制 $\approx$ 请求通过数 * 响应时间.

```go
func (l *BBR) maxInFlight() int64 {
	return int64(math.Floor(float64(l.maxPASS()*l.minRT()*l.bucketPerSecond)/1000.0) + 0.5)
}
```
