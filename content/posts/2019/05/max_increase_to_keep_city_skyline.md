---
title: "Max Increase to Keep City Skyline"
description: ""
date: "2019-05-03T16:22:13-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "array"
  - "matrix"
---

## Question

[Leetcode 807: Max Increase to Keep City Skyline](https://leetcode.com/problems/max-increase-to-keep-city-skyline/)

In a 2 dimensional array grid, each value `grid[i][j]` represents the height of a building located there. We are allowed to increase the height of any number of buildings, by any amount (the amounts can be different for different buildings). Height 0 is considered to be a building as well. 

At the end, the "skyline" when viewed from all four directions of the grid, i.e. top, bottom, left, and right, must be the same as the skyline of the original grid. A city's skyline is the outer contour of the rectangles formed by all the buildings when viewed from a distance. See the following example.

What is the maximum total sum that the height of the buildings can be increased?

### Examples

```
Input: grid = [[3,0,8,4],[2,4,5,7],[9,2,6,3],[0,3,1,0]]
Output: 35
Explanation: 
The grid is:
[ [3, 0, 8, 4], 
  [2, 4, 5, 7],
  [9, 2, 6, 3],
  [0, 3, 1, 0] ]

The skyline viewed from top or bottom is: [9, 4, 8, 7]
The skyline viewed from left or right is: [8, 7, 9, 3]

The grid after increasing the height of buildings without affecting skylines is:

gridNew = [ [8, 4, 8, 7],
            [7, 4, 7, 7],
            [9, 4, 8, 7],
            [3, 3, 3, 3] ]
```
### Notes:
1. `1 < grid.length = grid[0].length <= 50`.
1. All heights `grid[i][j]` are in the range `[0, 100]`.
1. All buildings in `grid[i][j]` occupy the entire grid cell: that is, they are a `1 x 1 x grid[i][j]` rectangular prism.


## Solution
### Method 1
本题本身并不难，想要不改变skyline那要求就是对于每个楼，它的高度不能超过他这行和这列的最大值。需要做的就是找出每一行和每一列的最大值，然后求差。

#### Code

```python
class Solution:
    def maxIncreaseKeepingSkyline(self, grid: List[List[int]]) -> int:
        row = [max(row) for row in grid]
        col = [grid[0][j] for j in range(len(grid[0]))]
        
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                col[j] = max(col[j], grid[i][j])
        
        r = 0
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                if grid[i][j] < min(col[j], row[i]):
                    r += min(col[j], row[i]) - grid[i][j]
        return r
```

### Method 2
但这个题我想说的点是看到了一个使用了python 语法糖的解法。其中使用了常见的`max`, `min`, `sum`。还使用了我不熟悉的[`map`](https://www.geeksforgeeks.org/python-map-function/) 和[`zip`](https://www.geeksforgeeks.org/zip-in-python/). 简单来说`map(func, iter)`就是把iter中的每个元素拿到func中处理一下，生成一个list结果。`zip(a,b,c)`是把a b c三个iter中同样位置的元素组合成一个tuple，然后生成一个list, `coupled`。还有unzip就是zip的反向操作：`zip(*coupled)`(注意这个 \*)，生成个list of list出来。

```python
def maxIncreaseKeepingSkyline(self, grid: List[List[int]]) -> int:
    row, col = list(map(max, grid)), list(map(max, zip(*grid)))
    return sum(min(i, j) for i in row for j in col) - sum(map(sum, grid))

```

我们来分析一下这个代码：
1. `map(max, grid)`是吧grid中的每个元素（也就是作为一行的一个list）求最大，生成的就是每行的最大值
1. `map(max, zip(*grid))`，和之前一样，只不过先做了一个unzip，就是把grid的n行看做n个元素，然后每个元素的第i个sub元素拿出来，各自组成一个list。













