---
title: "8-重建二叉树"
date: 2022-01-05T22:27:24+08:00
tags: ["interview"]
categories: ["algorithm"]
summary: "剑指Offer面试题7的归纳"
---

# 构建二叉树

## 问题介绍

根据二叉树的前序和中序遍历结果重建二叉树.

## 问题解答

```cpp
BinaryTreeNode* construct_binary_tree_core(const int* preorder, const int* inorder, int len) {
    if (len == 0) return nullptr;

    int find = -1; // root index in inorder.
    for (int i = 0; i < len; i++) {
        if (preorder[0] == inorder[i]) {
            find = i;
            break; // stop when find the first one.
        }
    }

    // handle unpredictable error.
    if (find == -1) {
        throw runtime_error("preorder mismatch with inorder");
    }

    auto root = new BinaryTreeNode();
    auto skip = find + 1; // for readability.

    root->val   = preorder[0];
    root->left  = construct_binary_tree_core(preorder + 1, inorder, find);
    root->right = construct_binary_tree_core(preorder + skip, inorder + skip, len - skip);

    return root;
}

BinaryTreeNode* construct_binary_tree(const int* preorder, const int* inorder, int len) {
    if ((preorder == nullptr || inorder == nullptr) && len != 0) {
        throw length_error("nullptr order with nonzero length");
    }
    if (len < 0) {
        throw length_error("negative length");
    }

    return construct_binary_tree_core(preorder, inorder, len);
}

// 这个问题的测试还不是很好进行, 我使用了向控制台展示二叉树的方案.
void test() {
    int preorder[]{ 1, 2, 4, 7, 3, 5, 6, 8 };
    int inorder[]{ 4, 7, 2, 1, 5, 3, 8, 6 };

    BinaryTreeNode::display(construct_binary_tree(preorder, inorder, 8));
    BinaryTreeNode::display(construct_binary_tree(nullptr, nullptr, 0));
    BinaryTreeNode::display(construct_binary_tree(preorder, inorder, -5));
}
```