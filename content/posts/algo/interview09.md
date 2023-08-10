---
title: "9-用两个栈实现队列"
date: 2022-01-08T11:42:00+08:00
tags: ["interview"]
categories: ["algorithm"]
summary: "剑指Offer面试题9的归纳"
---

## 问题介绍

用两个栈实现一个队列, 队列需要含有`push_back`和`delete_head`两个方法.

## 问题解答

一个栈专门用作输入, 一个栈专门用作输出, 输出栈为空时需要从输入栈获取元素.

![9-1](https://dl.axlis.cn/blog/interview/9-1.svg)

```c++
template<typename T>
class Queue {
public:
    void push_back(const T& val) {
        input.push(val);
    }

    // 当队列为空时删除队头元素会引发异常.
    T delete_head() {
        shift();
        if (output.empty()) {
            throw runtime_error("delete element from empty queue");
        }
        T res = output.top();
        output.pop();
        return res;
    }

private:
    // shift函数将所有来自input的元素转运到output中, 并保证了队列顺序要求.
    void shift() {
        if (output.empty()) {
            int i = 0;
            while (!input.empty() && i < SHIFT_LIMIT) {
                output.push(input.top());
                input.pop();
                i++;
            }
        }
    }

    // SHIFT_LIMIT可以加速单次shift函数的速度, 使得delete_head函数的性能得到了保证.
    static constexpr int SHIFT_LIMIT = 5;

    stack<T> input;
    stack<T> output;
};

void test() {
    Queue<int> queue;

    // 常规交替进出队列操作.
    for (int i = 0; i < 5; i++) {
        queue.push_back(i);
    }
    for (int i = 0; i < 3; i++) {
        cout << (queue.delete_head() == i ? "pass" : "fail") << ends;
    }
    for (int i = 5; i < 10; i++) {
        queue.push_back(i);
    }
    for (int i = 3; i < 10; i++) {
        cout << (queue.delete_head() == i ? "pass" : "fail") << ends;
    }
    cout << endl;

    // 从空队列中删除元素.
    queue.delete_head();
}
```