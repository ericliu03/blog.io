---
title: "[Leetcode 1039] Minimum Score Triangulation of Polygon"
description: ""
date: "2019-05-05T10:52:39-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "dp"
---


## Question

[Leetcode 1039: Minimum Score Triangulation of Polygon](https://leetcode.com/problems/frog-jump/)

Given `N`, consider a convex `N`-sided polygon with vertices labelled `A[0], A[i], ..., A[N-1]` in clockwise order.

Suppose you triangulate the polygon into `N-2` triangles.  For each triangle, the value of that triangle is the product of the labels of the vertices, and the total score of the triangulation is the sum of these values over all `N-2` triangles in the triangulation.

Return the smallest possible total score that you can achieve with some triangulation of the polygon.

### Note:

1. 3 <= A.length <= 50
1. 1 <= A[i] <= 100

### Examples

Example 1:
```
Input: [1,2,3]
Output: 6
Explanation: The polygon is already triangulated, and the score of the only triangle is 6.
```
Example 2:
```
Input: [3,7,4,5]
Output: 144
Explanation: There are two triangulations, with possible scores: 3*7*5 + 4*5*7 = 245, or 3*4*5 + 3*4*7 = 144.  The minimum score is 144.
```
Example 3:
```
Input: [1,3,1,4,1,5]
Output: 13
Explanation: The minimum score triangulation has score 1*1*3 + 1*1*4 + 1*1*5 + 1*1*1 = 13.
```

## Solution
### Method 1

对于一个多边形，无论怎样划分，我们得到一个三角形后便可以计算他的score。与此同时，这个三角形会把这个原本的多边形分成两个新的多边形。那么接下来我们只需要继续计算，直到多边形边长为0，也即不存在新的多边形为止。

除了划分之外，我们题目的要求是找最小，所以需要scan一遍所有可能的拆分方法，找到最小的那种。由于很多sub problem会遇到很多次，所以我们可以存下中间结果减少计算次数。

这其实是一个典型的DP问题，因为通过对解法的描述我们可以想到原本的problem被很清晰的分成了两个sub problem。

#### Code
```python
class Solution:
    def minScoreTriangulation(self, A: List[int]) -> int:
        self.memo = {}
        self.helper(0, len(A)-1, A)
        return self.memo[(0, len(A)-1)]
        
    def helper(self, i, j, A):
        if j - i < 2:
            return 0
        if (i, j) not in self.memo:
            self.memo[(i, j)] = float('inf')
            for k in range(i+1, j):
                s = A[i] * A[k] * A[j]
                s += self.helper(i, k, A) + self.helper(k, j, A)
                self.memo[(i, j)] = min(self.memo[(i, j)], s)
        return self.memo[(i, j)]
        
```

### Method 2
如果想用2维数组的方式写DP我们就需要考虑一下数组中每个element需要的前置结果有多少。先从状态转移方程来看
设`dp[i][j]`为`A[i...j]`包含的所有定点的最小score。那么转移方程为`dp[i][j] = min(dp[i][k] + dp[k][j] + score(i,k,j)) (i<k<j)`。每个`[i,j]`需要的是`[i,k]`和`[k,j]`，即`dp[i][i->j]`和`dp[i->j][j]`。换句话说，我们在计算大的多边形的时候，我们必须先把它包含的所有小的多边形计算一边。

直观来看（如下图所示），需要的是所有在`dp[i][j]`左边的和下的。所以计算的时候我们是每次计算一条从左上往右下的对角线，也即`[0,0]->[n-1,n-1]`，`[0,1]->[n-2,n-1]`....到`[0,n-1]->[0,n-1]`（本题`m = n`）



#### Code
```python
class Solution:
    def minScoreTriangulation(self, A: List[int]) -> int:
        dp = [[0] * len(A) for _ in range(len(A))] 

        # number of vertices minus 1 in the sub polygon
        for n in range(2, len(A)):
            # i is start vertex and j is end vertex
            for i in range(len(A)-n):
                j = i + n
                score = float('inf')
                for k in range(i+1, j):
                    score = min(score, A[i] * A[k] * A[j] + dp[i][k] + dp[k][j])
                dp[i][j] = score
        return dp[0][-1]
```


## Thoughts

1. 做题时其实想到了recursion，或者说DP的方向，就是选取的建模对象错了。
 1. 我自己在做题时虽然也想到了要划线，分成两份计算然后求和，但是分法想的是从i到k画一条线，分成了`i->k`和`k->i`两部分，这样带来了很多问题，比如 1)三角形如何划分，我考虑了很多情况。。2）数组中最后一个元素和第一个元素组成的边如何连接等等。 
 1. 题目的划分方法是把i到j范围内的点看做多边形所有的定点，与此同时把i->j作为一条边，再选点k组成三角形
1. 感觉是时候系统的看一下DP的总结了：[夜深人静写算法（二） - 动态规划](http://cppblog.com/menjitianya/archive/2015/10/23/212084.html)

## Reference
1. [算法笔记——【动态规划】凸多边形最优三角剖分](https://blog.csdn.net/u014755255/article/details/51076932)
1. [夜深人静写算法（二） - 动态规划](http://cppblog.com/menjitianya/archive/2015/10/23/212084.html)
1. [动态规划--凸多边形最优三角剖分](https://www.cnblogs.com/Jason-Damon/p/3298172.html)






