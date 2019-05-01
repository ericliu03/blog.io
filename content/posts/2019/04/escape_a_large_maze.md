---
title: "Escape A Large Maze"
date: 2019-04-29T16:43:38-07:00
description: ""
dropCap: false
displayInMenu: false
displayInList: true
draft: false
---

## Question

[Leetcode 1036: Escape a Large Maze](https://leetcode.com/problems/escape-a-large-maze/)

In a 1 million by 1 million grid, the coordinates of each grid square are `(x, y)` with `0 <= x, y < 10^6`.

We start at the `source` square and want to reach the `target` square.  Each move, we can walk to a 4-directionally adjacent square in the grid that isn't in the given list of `blocked` squares.

Return `true` if and only if it is possible to reach the target square through a sequence of moves.

### Note:
1. `0 <= blocked.length <= 200`
1. `blocked[i].length == 2`
1. `0 <= blocked[i][j] < 10^6`
1. `source.length == target.length == 2`
1. `0 <= source[i][j], target[i][j] < 10^6`
1. `source != target`


### Examples

Example 1:
```
Input: blocked = [[0,1],[1,0]], source = [0,0], target = [0,2]
Output: false
Explanation: 
The target square is inaccessible starting from the source square, because we can't walk outside the grid.
```
Example 2:
```
Input: blocked = [], source = [0,0], target = [999999,999999]
Output: true
Explanation: 
Because there are no blocked cells, it's possible to reach the target square.
```


## Solution
### Method 1
We can do a DFS or BFS from start to see if target could be reached. But this will TLE since the matrix is too large.

### Method 2
After taking a closer look at the "Notes", you will be able to find the **key point** of this question: `0 <= blocked.length <= 200`. This means there are only at maximum `200` blocked in the matrix and `200` is a small number comparing to `10^6`.

This graph from reference 1 shows the insight: the maximum area that those blocked grid can enclose is `1+2+3+4+5+...+198+199=(1+199)*199/2=19900` (trapezoid sum)
```
0th      ______________________  200
         |------------------ X            
         |---------------- X
         .              .
         .           .
         .        . 
         | -  - X 
         | - X
200      |X
```
Then there are conditions:
1. there is not an enclosed area
1. there is an enclosed area
 1. source is inside, target is out
 1. target is in, source is out
 1. both in or both out

In order to cover all conditions, we run bfs/dfs from both source and target, so if
1. they find each other, return true
1. if none of them is in enclosed area, return true
1. otherwise return false

#### Code
```python
def isEscapePossible(blocked, source, target):
    max_area = ((1 + 199) * 199)//2
    n = 10**6
    visited = set([tuple(each) for each in blocked])
    
    def bfs(start, end):
        queue = [tuple(start)]
        visited.add(tuple(start))
        count = 1
        for x0, y0 in queue:
            count += 1
            if (x0, y0) == end:
                return True
            if count > max_area:
                return True
            for x, y in [(-1,0), (1, 0), (0, 1), (0,-1)]:
                next_node = (x0+x, y0+y)
                if next_node not in visited and 0 <= next_node[0] < n and 0 <= next_node[1] < n:
                    queue.append(next_node)
                    visited.add(next_node)
        return False

    return bfs(target, source) and bfs(source, target)
```

### Thoughts
1. don't neglect the constraints

