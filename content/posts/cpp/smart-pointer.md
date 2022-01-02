---
title: "C++智能指针"
date: 2021-12-10T16:12:22+08:00
tags: ["Pointer"]
categories: ["CPP"]
summary: "一些智能指针的总结"
---

## `unique_ptr`

`unique_ptr`独占它所管理的对象, 不允许拷贝和赋值, 并且开销十分的小.

```c++
auto num = make_unique<int>(10); // 构造unique_ptr.
auto ptr = num.release();        // 放弃控制权, 返回底层指针.

num.reset(nullptr); // 释放底层对象.
num.reset(ptr);     // 更换底层对象.
```

我们可以拷贝和赋值一个将要被销毁的`unique_ptr`, 编译器会执行特殊的拷贝.

```c++
unique_ptr<int> increase(unique_ptr<int> ptr) {
	(*ptr)++;
	return ptr;
}

cout << *increase(make_unique<int>(0)) << endl;
```