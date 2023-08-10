---
title: "源码解析: sync.Map"
date: 2023-08-04T18:00:00+08:00
categories: ["Go"]
---

Golang中的`map`并不是线程安全的, 实现线程安全的`map`可以加读写锁, 也可以直接使用`sync.Map`.

由于`sync.Map`在读多写少情况下有着优异的性能, 本次我们分析一下`sync.Map`的底层实现原理.

<!--more-->

## 示例分析

通过一个简单读写场景, 来分析源码的实现:

```go
func main() {
	store := sync.Map{}
	
	store.Store(1, 1)
	store.Load(1)
	store.Store(2, 2)
	store.Load(2)
	store.Load(3)
}
```

`Store(1, 1)`的执行过程:

```go
func (m *Map) Swap(key, value any) (previous any, loaded bool) {
	read := m.loadReadOnly()
	if e, ok := read.m[key]; ok {
		// 从read中没找到 (ok == false).
	}

	m.mu.Lock()
	read = m.loadReadOnly()
	if e, ok := read.m[key]; ok {
		// 从read中没找到 (ok == false).
	} else if e, ok := m.dirty[key]; ok {
		// 从dirty中也没找到 (ok == false).
	} else {
		if !read.amended {
            // 当前read是没有修改过的 (amended = false).
            // 将read中的map拷贝到dirty.
			m.dirtyLocked()
            // 将read标记为修改过的 (amended = true).
			m.read.Store(&readOnly{m: read.m, amended: true})
		}
        // 向dirty中添加元素, 此时read和dirty中的内容才正真不相同.
		m.dirty[key] = newEntry(value)
	}
	m.mu.Unlock()
	return previous, loaded
}
```

`Load(1)`的执行过程:

```go
func (m *Map) Load(key any) (value any, ok bool) {
	read := m.loadReadOnly()
	e, ok := read.m[key]
    // 从read中没有找到 (ok == false), 并且read被修改过.
	if !ok && read.amended {
		m.mu.Lock()
		read = m.loadReadOnly()
		e, ok = read.m[key]
        // 从read中没有找到 (ok == false), 并且read被修改过.
		if !ok && read.amended {
            // 从dirty中找到了entry (ok == true).
			e, ok = m.dirty[key]
			// 记录一次缓存失效
			m.missLocked()
		}
		m.mu.Unlock()
	}
	if !ok {
		return nil, false
	}
	return e.load()
}

func (m *Map) missLocked() {
	m.misses++

    // 缓存失效次数和dirty中entry数量相等.
	if m.misses < len(m.dirty) {
		return
	}

    // 进行替换, 此时read没有没修改过.
	m.read.Store(&readOnly{m: m.dirty})
	m.dirty = nil
	m.misses = 0
}
```

`Store(2, 2)`和`Load(2)`的执行过程和上面类似.

`Load(3)`读取了一个不存在的`key`. 此时虽然在`dirty`中没找到, 但是还是需要增加`miss`的数量, 导致触发了替换.

## 总结

本次我们只是简单分析了一下`Load`和`Store`的执行过程, 没有涉及删除等操作, 以后有时间再补充.
