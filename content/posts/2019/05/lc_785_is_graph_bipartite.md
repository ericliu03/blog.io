---
title: "[Leetcode 785] Is Graph Bipartite"
description: ""
date: "2019-05-17T11:51:24-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "bfs"
  - "dfs"
---




## Question

[Leetcode 785: Is Graph Bipartite?](https://leetcode.com/problems/is-graph-bipartite/)

Given an undirected `graph`, return `true` if and only if it is bipartite.

Recall that a graph is bipartite if we can split it's set of nodes into two independent subsets A and B such that every edge in the graph has one node in A and another node in B.

The graph is given in the following form: `graph[i]` is a list of indexes `j` for which the edge between nodes `i` and `j` exists.  Each node is an integer between `0` and `graph.length - 1`.  There are no self edges or parallel edges: `graph[i]` does not contain `i`, and it doesn't contain any element twice.

### Note:
1. graph will have length in range [1, 100].
1. graph[i] will contain integers in range [0, graph.length - 1].
1. graph[i] will not contain i or duplicate values.
1. The graph is undirected: if any element j is in graph[i], then i will be in graph[j].

### Examples

Example 1:
```
Input: [[1,3], [0,2], [1,3], [0,2]]
Output: true
Explanation: 
The graph looks like this:
0----1
|    |
|    |
3----2
We can divide the vertices into two groups: {0, 2} and {1, 3}.
```

Example 2:
```
Input: [[1,2,3], [0,2], [0,1,3], [0,2]]
Output: false
Explanation: 
The graph looks like this:
0----1
| \  |
|  \ |
3----2
We cannot find a way to divide the set of nodes into two independent subsets.
```

## Solution
### Method 1

Key:每个edge的两个vertex在不同的group -> 任意一个vertex, `v`, 的所有neighbor都应该是与`v`在不同的group。

所以只需要dfs或者bfs走一遍，每次往neighbor走一个level时看看是不是访问过，如果有看下是不是相同group。bfs或者dfs的过程中，我们可以用一个map记录已经访问的vertex的group是什么。

我认为需要注意的点:

1. 有可能存在多个联通分量，所以需要从每个点都开始一次
1. 题目给的就是每个点的neighbor id，所以无需处理



#### Code
```python
class Solution(object):
    def isBipartite(self, graph):
        group = {}
        for i in range(len(graph)):
            if i in group:
                continue
            
            queue = [i]
            group[i] = 0
            while queue:
                curr_node = queue.pop(0)
                for neighbor in graph[curr_node]:
                    if neighbor in group:
                        if group[neighbor] == group[curr_node]:
                            return False
                    else:
                        queue.append(neighbor)
                        group[neighbor] = ~group[curr_node]
        return True
```
