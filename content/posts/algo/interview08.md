---
title: "9-二叉树的下一个节点"
date: 2022-01-05T22:27:24+08:00
tags: ["interview"]
categories: ["algorithm"]
summary: "剑指Offer面试题8的归纳"
---

# 二叉树遍历的下一个节点

## 问题介绍

给出一个节点在中序变量中的下一个节点.

## 问题解答

首先我们对最简单的二叉树进行分析.

<img src="https://axlis.oss-cn-hangzhou.aliyuncs.com/note/algo/1.svg" style="margin: 0">

然后探讨一些一般情况下没有遇到的一些问题.

<img src="https://axlis.oss-cn-hangzhou.aliyuncs.com/note/algo/2.svg" style="margin: 0">

<img src="https://axlis.oss-cn-hangzhou.aliyuncs.com/note/algo/3.svg" style="margin: 0">

最后根据分析写出简洁的实现.

```cpp
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

// 测试有点简单.
void test() {
    BTNode root, left, right;
    root.val  = 1;
    left.val  = 2;
    right.val = 3;

    root.left = &left;
    root.right = &right;
    left.parent = &root;
    right.parent = &root;

    cout << inorder_next(&root)->val << endl;
    cout << inorder_next(&left)->val << endl;
    cout << inorder_next(&right)     << endl;
    cout << inorder_next(nullptr)    << endl;
}
```
