---
title: BST
date: 2023-05-21 19:09:10
tags: Algorithm
categories: 算法
---

## BST

### 性质

对于树中的每个节点X，它的左子树中所有关键字值小于X的关键字值，同时右子树中所有关键字值大于X的关键字值。这意味着树中所有的元素可以用某种统一的方式排序。

### 结构

```c
struct TreeNode {
    struct TreeNode* left;
    struct TreeNode* right;
    int value;
}
typedef struct TreeNode TreeNode;
```

### 查找

```c
TreeNode* find(TreeNode* root, int x) {
    if (!root) return NULL;
    if (x < root->value) {
        return find(root->left, x);
    } else if (value > root->value) {
        return find(root->right, x);
    }
    return root;
}
TreeNode* findMin(TreeNode* root) {
    if (!root) return NULL;
    if (!root->left) {
        return root;
    } else {
        return findMin(root->left);
    }
}
TreeNode* findMax(TreeNode* root) {
    if (!root) return NULL;
    if (!root->right) {
        return root;
    } else {
        return findMax(root->right);
    }
}
```

### 插入

```c
TreeNode* insert(TreeNode* root, int x) {
    if (!root) {
        root = (TreeNode*) malloc(sizeof(TreeNode));
        if (!root) {
            printf("Out of space");
        } else {
            root->value = x;
            root->left = root->right = NULL;
        }
    } else {
        if (x < root->value) {
            root->left = insert(root->left, x);
        } else if (x > root->value) {
            root->right = insert(root->right, x);
        }
    }
    return root;
}
```

### 删除

```c
TreeNode* delete(TreeNode* root, int x) {
    if (!root) {
        return NULL;
    }
    if (x < root->value) {
        root->left = delete(root->left, x);
    } else if (x > root->value) {
        root->right = delete(root->right, x);
    } else {
        if (root->left && root->right) {
            TreeNode* tmp = findMin(root->right);
            root->value = tmp->value;
            root->right = delete(root->right, tmp->value);
        } else {
            TreeNode* tmp = root;
            if (!root->left) {
                root = root->right;
            } else {
                root- = root->left;
            }
            free(tmp);
        }
    }
    return root;
}
```
