---
title: AVL树
date: 2023-05-21 19:02:10
tags: Algorithm
categories: 算法
---

## AVL

AVL树是带有平衡条件的二叉查找树，每个节点的左子树和右子树的高度最多差1。

### 结构

```c
struct TreeNode {
    struct TreeNode* left;
    struct TreeNode* right;
    int value;
    int height;
}
typedef struct TreeNode TreeNode;
```

### 插入

插入一个节点时可能会破坏AVL树的特性，导致高度不平衡，主要有以下4中情况：

- 对a的左儿子的左子树进行一次插入
- 对a的左儿子的右子树进行一次插入
- 对a的右儿子的右子树进行一次插入
- 对a的右儿子的左子树进行一次插入

对于左-左或右-右的情况，可以通过一次单旋转完成调整；对于左-右或右-左的情况，需要通过双旋转来完成调整

```c
TreeNode* leftRotate(TreeNode* root) {
    if (!root) return NULL;
    TreeNode* tmp = root->right;
    root->right = tmp->left;
    tmp->left = root;
    root->height = max(Height(root->left), Height(root->right)) + 1;
    tmp->height = max(Height(tmp->left), Height(tmp->right)) + 1;
    return tmp;
}
TreeNode* rightRotate(TreeNode* root) {
    if (!root) return NULL;
    TreeNode* tmp = root->left;
    root->left = tmp->right;
    tmp->right = root;
    root->height = max(Height(root->left), Height(root->right)) + 1;
    tmp->height = max(Height(tmp->left), Height(tmp->right)) + 1;
    return tmp;
}
int getBalance(TreeNode* root) {
    if (!root) return 0;
    return Height(root->left) - Height(root->right);
}
TreeNode* insert(TreeNode* root, int x) {
    if (!root) {
        root = (TreeNode*) malloc(sizeof(TreeNode));
        root->value = x;
        root->left = root->right = NULL;
        root->height = 1;
    } else {
        if (x < root->value) {
            root->left = insert(root->left, x);
        } else if (x > root->value) {
            root->right = insert(root->right, x);
        } else {
            return root;
        }
    }
    root->height = max(Height(root->left), Height(root->right)) + 1;
    int balance = getBalance(root);
    if (balance > 1) {
        if (getBalance(root->left) >= 0) {
            root = rightRotate(root);
        } else {
            root->left = leftRotate(root->left);
            root = rightRotate(root);
        }
    } else if (balance < -1) {
        if (getBalance(root->right) <= 0) {
            root = leftRotate(root);
        } else {
            root->right = rightRotate(root->right);
            root = leftRotate(root);
        }
    }
    return root;
}
```

### 删除

删除节点的逻辑也和BST类似，只是删除后，需要调整树的高度

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
            root = root->left ? root->left : root->right;
            free(tmp);
        }
    }
    if (!root) { // empty tree
        return root;
    }
    root->height = max(Height(root->left) , Height(root->right)) + 1;
    int balance = getBalance(root);
    if (balance > 1) {
        if (getBalance(root->left) >= 0) {
            root = rightRotate(root);
        } else {
            root->left = leftRotate(root->left);
            root = rightRotate(root);
        }
    } else if (balance < -1) {
        if (getBalance(root->right) <= 0) {
            root = leftRotate(root);
        } else {
            root->right = rightRotate(root->right);
            root = leftRotate(root);
        }
    }
    return root;
}
```
