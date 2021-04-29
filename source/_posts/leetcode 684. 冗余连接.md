---
layout: leetcode
title: leetcode 684. 冗余连接
author: 黄丕舜
date: 2021-04-28 17:57:58
tags: 算法
categories: Leetcode
comments:
---
# 一、题目
找出无向图中的冗余连接，即将无向图还原成二叉树
```
输入: [[1,2], [1,3], [2,3]]
输出: [2,3]
解释: 给定的无向图为:
  1
 / \
2 - 3
```

# 二、解法
### 2.1 并查集
思路：通过并查集寻找附加的边，初始时每个节点都属于不同的连通分量，遍历每一条边，判断这条边连接的两个顶点是否属于相同的连通分量
- 如果两个顶点属于不同的连通分量，则说明在遍历到当前的边之前，这两个顶点之间不连通，因此当前的边不会导致环出现，合并这两个顶点的连通分量。
- 如果两个顶点属于相同的连通分量，则说明在遍历到当前的边之前，这两个顶点之间已经连通，因此当前的边导致环出现，为附加的边，将当前的边作为答案返回。

```cpp
class Solution {
public:
    int Find(vector<int>& parent, int index) {  // 查找根节点
      if (index != parent[index])  // 若当前节点不为根节点
        parent[index] = Find(parent, parent[index]]); 
      return parent[index];
    }

    void Union(vector<int>& parent, int x, int y) {  // 合并两个集合
      parent[Find(parent, x)] = Find(parent, y);
    }
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
      int size = edges.size();
      vector<int> parent(size + 1);  // 为下列循环防止溢出
      iota(parent.begin(), parent.end(), 0);  // 0, 1, 2, 3...
      for (auto edge: edges) {
        int u = edge[0], v = edge[1];
        if (Find(parent, u) != Find(parent, v)) {  // 若两个集合的根节点不同
          Union(parent, u, v);  // 则将它们连接起来
        } else {
          return edge;  // 返回合并的森林
        }
      }
      return vector<int> {0, 0};
    }
};
```
### 复杂度分析
- 时间复杂度：O(klogk)，其中k是图中的节点个数
- 空间复杂度：O(n)

### 2.2 使用==按秩合并+路径压缩==的并查集
优化了空间复杂度
```cpp
class Solution {
public:
    int Find(vector<int>& parent, int index) {  // 查找根节点
      if (index != parent[index])  // 若当前节点不为根节点
        parent[index] = Find(parent, parent[parent[index]]);  // 路径压缩，使树的层数更少
      return parent[index];
    }

    void Union(vector<int>& parent, int x, int y) {  // 合并两个集合
      int i = Find(parent, x), j = Find(parent, y);
      // 按秩合并
      if (i > j) {  // 如果i的秩大于j的秩
        parent[j] = i;
      } else {
        parent[i] = j;
      }
    }
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
      int size = edges.size();
      vector<int> parent(size + 1);  // 为下列循环防止溢出
      iota(parent.begin(), parent.end(), 0);  // 0, 1, 2, 3...
      for (auto edge: edges) {
        int u = edge[0], v = edge[1];
        if (Find(parent, u) != Find(parent, v)) {  // 若两个集合的根节点不同
          Union(parent, u, v);  // 则将它们连接起来
        } else {
          return edge;  // 返回合并的森林
        }
      }
      return vector<int> {0, 0};
    }
};
```
### 复杂度分析
- 时间复杂度：O(klogk)
- 空间复杂度：O(1)