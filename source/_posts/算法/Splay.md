---
title: Splay
date: 2023-05-21 19:30:10
tags: Algorithm
categories: 算法
---

## Splay

伸展树是一种平衡二叉查找树，它通过伸展操作不断将某个节点旋转到根节点，使得整棵树仍然满足二叉查找树的性质，能够在均摊O(logN)时间内完成插入、查找和删除操作，并且保持平衡而不至于退化为链

### 定义

伸展树的基本想法是：当一个节点被访问后，它就要经过一系列AVL树的旋转被放到根上。如果一个节点很深，那么其路径上就存在许多的节点也相对较深，通过构造可以使所有这些节点的进一步访问所花费的时间较少

### 展开

#### 之字形

查询节点是祖父节点的左儿子的右儿子或者右儿子的左儿子，这种情况先将查询节点旋转到父节点的位置，然后再旋转到祖父节点位置

#### 一字形

查询节点是祖父节点的右儿子的右儿子或者左儿子的左儿子，这种情况先将父节点旋转到祖父节点的位置，然后再将查询节点旋转到祖父节点位置

```c
TreeNode* splay(TreeNode* root, int x) {
    if (!root || root->value == x) {
        return root;
    }
    if (x < root->value) {
        if (!root->left) return root;
        if (x < root->left->value) {
            // 一字型
            root->left->left = splay(root->left->left, x);
            if (root->left->left) {
                root = rightRotate(root);
            }
        } else if (x > root->left->value) {
            // 之字型
            root->left->right = splay(root->left->right, x);
            if (root->left->right) {
                root->left = leftRotate(root->left);
            }
        }
        return root->left ? rightRotate(root) : root;
    } else {
        if (!root->right) return root;
        if (x > root->right->value) {
            // 一字型
            root->right->right = splay(root->right->right, x);
            if (root->right->right) {
                root = leftRotate(root);
            }
        } else if (x < root->right->value) {
            // 之字型
            root->right->left = splay(root->right->left, x);
            if (root->right->left) {
                root->right = rightRotate(root->right);
            }
        }
        return root->right ? leftRotate(root) : root;
    }
}
```

### 插入

如果根节点为空，直接返回新生成的节点；如果不为空，则先对元素x进行伸展，如果根节点的值小于x，则直接将根节点的值作为x的左儿子，根节点的右儿子作为x的右儿子

```c
TreeNode* insert(TreeNode* root, int x) {
    if (!root) return NULL;
    root = splay(root, x);
    if (root->value == x) {
        return root;
    }
    TreeNode* node = (TreeNode*) malloc(sizeof(TreeNode));
    node->value = x;
    node->left = node->right = NULL;
    if (x < root->value) {
        node->right = root;
        node->left = root->left;
        root->left = NULL;
    } else {
        node->left = root;
        node->right = root->right;
        root->right = NULL;
    }
    return node;
}
```

### 删除

如果根节点为空，直接返回；如果不为空，则先对树进行伸展，如果伸展后的根节点就是要查找的节点，则再次对根节点的左子树伸展，拿到左子树的新根，将根节点的右节点，设置为之前的右子树

```c
TreeNode* delete(TreeNode* root, int x) {
    if (!root) return NULL;
    root = splay(root, x);
    if (x = ! root->value) {
        return root;
    }
    TreeNode* tmp = root;
    if (!root->left) {
        root = root->right;
    } else {
        root = splay(root->left, x);
        root->right = tmp->right;
    }
    free(tmp);
    return root;
}
```

### 查找

```c
TreeNode* find(TreeNode* root, int x) {
    TreeNode* tmp = splay(root, x);
    if (x == tmp->value) {
        return tmp;
    }
    return NULL;
}
```
