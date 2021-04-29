---
layout: 剑指
title: 剑指 Offer 34. 二叉树中和为某一值的路径
date: 2021-04-28 17:59:13
author: 黄丕舜
tags: 算法
categories: Leetcode
comments:
---
# 一、题目
打印出二叉树中节点值的和为输入整数的所有路径（从根节点一直到叶子节点的路径）
**输入**：
给定如下二叉树，以及目标和 target = 22
```
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1
```
**输出**：
```
[
   [5,4,11,2],
   [5,8,4,5]
]
```

# 二、解法
由根节点出发，首先想到先序遍历的思路
### 2.1 回溯
```cpp
class Solution {
public:
    vector<vector<int>> pathSum(TreeNode* root, int target) {
      if (root == nullptr) {
        return {};
      }
      vector<int> path;
      vector<vector<int>> ans;
      int currentSum = 0;
      FindPath(root, target, path, currentSum, ans);
      return ans;
    }

    void FindPath(TreeNode* root, int target, vector<int>& path, int currentSum, vector<vector<int>>& ans) {
      // 先序遍历
      currentSum += root->val;  // 更新当前路径
      path.push_back(root->val);
      // 如果是叶子节点，并且路径上的节点的和等于输入的值，则打印这条路径
      bool isLeaf = root->left == nullptr && root->right == nullptr;
      if (currentSum == target && isLeaf) {
        ans.push_back(path);
      }
      // 若不是叶子节点，则遍历它的子节点
      if (root->left != nullptr) {
        FindPath(root->left, target, path, currentSum, ans);
      }
      if (root->right != nullptr) {
        FindPath(root->right, target, path, currentSum, ans);
      }
      // 在返回父节点之前，在路径上删除当前节点
      path.pop_back();  // 回溯
    }
};
```
### 复杂度分析
- **时间复杂度**：O(N)，N为二叉树的节点数，需要遍历所有节点
- **空间复杂度**：O(N)，最差情况下即树退化成链表，path存储所有树节点，使用O(N)额外空间