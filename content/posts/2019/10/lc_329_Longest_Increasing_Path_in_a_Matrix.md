---
title: "[Leetcode 329] Longest Increasing Path in a Matrix"
description: ""
date: "2019-10-29T14:36:47-08:00"
thumbnail: ""
categories:
  - ""
tags:
  - "hard"
---

## Question

[Leetcode 329: Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)

Given an integer matrix, find the length of the longest increasing path.

From each cell, you can either move to four directions: left, right, up or down. You may NOT move diagonally or move outside of the boundary (i.e. wrap-around is not allowed).


### Examples:
```
Example 1:

Input: nums = 
[
  [9,9,4],
  [6,6,8],
  [2,1,1]
] 
Output: 4 
Explanation: The longest increasing path is [1, 2, 6, 9].
Example 2:

Input: nums = 
[
  [3,4,5],
  [3,2,6],
  [2,2,1]
] 
Output: 4 
Explanation: The longest increasing path is [3, 4, 5, 6]. Moving diagonally is not allowed.
```

## Solution
### Method 1
本题最明显的接法肯定是使用DFS，但是如果对于每个起点都进行一遍DFS肯定复杂度太高。用一点点DP的思想来存下已经计算的结果就好了。

注意的一点是找的是increasing path，所以如果在DFS的中间碰到之前走过的一个点，那么这个点存的最长长度的path一定和已经走的path没有重叠。
```python
class Solution:
    def longestIncreasingPath(self, matrix: List[List[int]]) -> int:
        dirs = [(1,0), (0,1), (-1,0), (0,-1)]    
        lengths =[[0] * len(matrix[0]) for _ in range(len(matrix))]
    
        def find_length(i,j):
            if lengths[i][j] != 0:
                return lengths[i][j]
    
            for dx, dy in dirs:
                x = i + dx
                y = j + dy
                if 0 <= x < len(matrix) and 0 <= y < len(matrix[0]) and matrix[x][y] > matrix[i][j]:
                    lengths[i][j] = max(lengths[i][j], find_length(x, y))
            lengths[i][j] += 1
            return lengths[i][j]
        
        max_len = 0
        for i in range(len(matrix)):
            for j in range(len(matrix[i])):
                max_len = max(max_len, find_length(i,j))
        return max_len
```
### Method 2
另一个想法是把matrix想成一个有向图 directed graph，每个数会指向它上下左右的比他大的数。那么这个最长的path的起点一定是一个只有出度的数。所以进行 topological sort，在每个round的中先找出所有入度为0的数， 根据他们的边更新graph，就会有新的入度为0的数出现。每个round代表了path的长度加一。