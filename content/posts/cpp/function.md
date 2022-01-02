---
title: "C++函数类型与函数指针"
date: 2021-12-01T22:29:56+08:00
tags: ["SourceCode", "Function"]
categories: ["CPP"]
summary: "基于FLTK源码分析的函数类型总结"
---

## 基于源码分析总结

```cpp
// 这是FLTK源码中回调函数的定义.
typedef void (Fl_Callback)(Fl_Widget*, void*);
```

其中`void`是返回值类型, `(Fl_Widget*, void*)`是参数类型, `Fl_Callback`是函数名.

```cpp
// 这是FLTK源码中Widget绑定方法的定义.
void callback(Fl_Callback* cb) { callback_ = cb; }
```

这里定义时直接使用了`Fl_Callback*`来表示函数指针, 十分简洁.

```cpp
// 我们实现Fl_Callback函数时, 没有用到的参数可以省略参数名.
void button_call_back(Fl_Widget* object, void*) {}
```
