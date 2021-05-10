---
layout: post
title: "algorithm_树的问题"
date: 2021-05-07
categories: algorithm
---

#### 树的问题 不要跳进递归 完成要完成的事情就可以了  leetcode 代码暂存

### 116
``` c++
class Solution {
public:
    Node* connect(Node* root) {
        if (root == NULL) {
            return NULL;
        }
        connectTwoNode(root->left, root->right);
        return root;
    }

    void connectTwoNode(Node* rootL, Node* rootR) {
        if (rootL == NULL || rootR == NULL) {
            return;
        }
        // 两个子节点连在一起
        rootL->next = rootR;
        //两个子节点各自的子节点
        connectTwoNode(rootL->left, rootL->right);
        connectTwoNode(rootR->left, rootR->right);
        //两个不同父节点的点
        connectTwoNode(rootL->right, rootR->left);
    }
};
```

### 226
``` c++
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        // 容错条件
        if (root == nullptr) {
            return nullptr;
        }
        // root 节点需要交换它的左右子节点
        TreeNode* tmp = root->left;
        root->left = root->right;
        root->right = tmp;

        // 让左右子节点继续翻转它们的子节点 递归
        invertTree(root->left);
        invertTree(root->right);

        return root;
    }
};
```
### 114
```c++
class Solution {
public:
    void flatten(TreeNode* root) {
        if (root == nullptr) {
            return;
        }
        // 递归
        flatten(root->left);
        flatten(root->right);

        // 把左子树给右子树 保存右子树
        TreeNode* rootL = root->left;
        TreeNode* rootR = root->right;
        root->left = nullptr;
        root->right = rootL;

        // 遍历找到现在右边子树的根节点
        // 为了保证root还是root 再输出没问题 使用临时变量查找
        TreeNode *temp = root;
        while(temp->right != nullptr) {
            temp = temp->right;
        }

        // 最后再把原来的右子树接过去
        temp->right = rootR;
    }
};
```















