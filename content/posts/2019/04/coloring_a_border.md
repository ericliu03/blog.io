---
title: "Coloring Border"
date: 2019-04-28T09:42:03-07:00
description: ""
dropCap: false
displayInMenu: false
displayInList: true
draft: false
---


## Question

[Leetcode 1034: Coloring A Border](https://leetcode.com/problems/coloring-a-border/)

Given a 2-dimensional grid of integers, each value in the `grid` represents the color of the grid square at that location.

Two squares belong to the same connected component if and only if they have the same color and are next to each other in any of the 4 directions.

The border of a connected component is all the squares in the connected component that are either 4-directionally adjacent to a square not in the component, or on the boundary of the grid (the first or last row or column).

Given a square at location `(r0, c0)` in the grid and a `color`, `color` the border of the connected component of that square with the given `color`, and return the final grid.


### Examples

Example 1:
```
Input: grid = [[1,1],[1,2]], r0 = 0, c0 = 0, color = 3
Output: [[3, 3], [3, 2]]
Example 2:
```
```
Input: grid = [[1,2,2],[2,3,2]], r0 = 0, c0 = 1, color = 3
Output: [[1, 3, 3], [2, 3, 3]]
Example 3:
```
```
Input: grid = [[1,1,1],[1,1,1],[1,1,1]], r0 = 1, c0 = 1, color = 2
Output: [[2, 2, 2], [2, 1, 2], [2, 2, 2]]
```
#### Notes:

1. `1 <= grid.length <= 50`
1. `1 <= grid[0].length <= 50`
1. `1 <= grid[i][j] <= 1000`
1. `0 <= r0 < grid.length`
1. `0 <= c0 < grid[0].length`
1. `1 <= color <= 1000`


## Solution
### Method 1

Use regular dfs/bfs to find the border squares and change them. 

1. There are two things needs to know 1) if current square is in original component, 2) if current square is border
1. I used `1/0` to indicate if a square is in original component, so by adding up dfs result from four adjacent squares, we know whether current one is border or not.
1. the **grid should not be changed** while doing dfs/bfs.


#### Code


```python
class Solution:
    def colorBorder(self, grid, r0, c0, color):

        visited = set()
        border = set()

        def dfs(x, y):
            # if current square is in original component, return 1, otherwise 0
            if 0 <= x < len(grid) and 0 <= y < len(grid[0]) and grid[x][y] == grid[r0][c0]:
                if (x, y) not in visited:
                    visited.add((x, y))
                    # if one of the adjacent square is not in the component, then this square is border
                    if dfs(x + 1, y) + dfs(x - 1, y) + dfs(x, y + 1) + dfs(x, y - 1) < 4:
                        border.add((x, y))
                return 1
            else:
                return 0

        dfs(r0, c0)
        for x, y in border:
            grid[x][y] = color
        return grid
```













