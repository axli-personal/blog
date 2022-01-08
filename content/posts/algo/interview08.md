---
title: "8-二叉树的下一个节点"
date: 2022-01-08T11:41:00+08:00
tags: ["interview"]
categories: ["algorithm"]
summary: "剑指Offer面试题8的归纳"
---

## 问题介绍

给出一个节点在中序变量中的下一个节点.

## 问题解答

首先我们对最简单的二叉树进行分析.

![8-1](https://axlis.oss-cn-hangzhou.aliyuncs.com/blog/interview/8-1.svg)

然后探讨一些一般情况下没有遇到的一些问题.

![8-2](https://axlis.oss-cn-hangzhou.aliyuncs.com/blog/interview/8-2.svg)

![8-3](https://axlis.oss-cn-hangzhou.aliyuncs.com/blog/interview/8-3.svg)

最后根据分析写出简洁的实现.

```cpp
#include "util/tree.h" // https://github.com/axli-personal/algo

BTNode* inorder_next(const BTNode* current) {
    if (current == nullptr) return nullptr;

    if (current->right != nullptr) {
        BTNode* next = current->right;
        while (next->left != nullptr) {
            next = next->left;
        }
        return next;
    }

    if (current->parent != nullptr) {
        BTNode* next = current->parent;
        while (next != nullptr && next->left != current) {
            current = next;
            next    = current->parent;
        }
        return next;
    }

    return nullptr;
}

void test() {
    istringstream in("1 2 4 -1 7 -1 -1 -1 3 5 -1 -1 6 8 -1 -1 -1");

    auto root = BTNode::build(in);

    BTNode::display(root);

    BTNode::print(inorder_next(root));
    cout << endl;
    BTNode::print(inorder_next(root->left));
    cout << endl;
    BTNode::print(inorder_next(root->right));
    cout << endl;
    BTNode::print(inorder_next(nullptr));
    cout << endl;
}
```
