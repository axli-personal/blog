---
title: "7-重建二叉树"
date: 2022-01-08T11:40:00+08:00
tags: ["interview"]
categories: ["algorithm"]
summary: "剑指Offer面试题7的归纳"
---

## 问题介绍

根据二叉树的前序和中序遍历结果重建二叉树.

## 问题解答

```cpp
#include "util/tree.h" // https://github.com/axli-personal/algo

BTNode* construct_binary_tree_core(const int* preorder, const int* inorder, int len) {
    if (len == 0) return nullptr;

    // 根节点在中序序列中的位置.
    int find = -1;
    for (int i = 0; i < len; i++) {
        if (preorder[0] == inorder[i]) {
            find = i;
            break; // 找到后停止.
        }
    }

    // 对于没找到根节点的情况: 抛出异常.
    if (find == -1) {
        throw runtime_error("preorder mismatch with inorder");
    }

    auto root = new BTNode();
    auto skip = find + 1; // 提高可读性.

    root->val   = preorder[0];
    root->left  = construct_binary_tree_core(preorder + 1, inorder, find);
    root->right = construct_binary_tree_core(preorder + skip, inorder + skip, len - skip);

    return root;
}

BTNode* construct_binary_tree(const int* preorder, const int* inorder, int len) {
    if ((preorder == nullptr || inorder == nullptr) && len != 0) {
        throw length_error("nullptr order with nonzero length");
    }
    if (len < 0) {
        throw length_error("negative length");
    }

    return construct_binary_tree_core(preorder, inorder, len);
}

void test() {
    int preorder[]{ 1, 2, 4, 7, 3, 5, 6, 8 };
    int inorder[]{ 4, 7, 2, 1, 5, 3, 8, 6 };

    BTNode::display(construct_binary_tree(preorder, inorder, 8));
    BTNode::display(construct_binary_tree(nullptr, nullptr, 0));
    BTNode::display(construct_binary_tree(preorder, inorder, -5));
}
```