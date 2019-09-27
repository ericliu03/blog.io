---
title: "[Leetcode 370] Range Addition"
description: ""
date: "2019-09-26T20:40:16-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "medium"
---

## Question

[Leetcode 370: Range Addition](https://leetcode.com/problems/copy-list-with-random-pointer/)

Assume you have an array of length n initialized with all 0's and are given k update operations.

Each operation is represented as a triplet: `[startIndex, endIndex, inc]` which increments each element of subarray `A[startIndex ... endIndex]` (startIndex and endIndex inclusive) with **inc**.

Return the modified array after all **k** operations were executed.



### Note:
1. You must return the copy of the given head as a reference to the cloned list.
1. Node definition:
```python
class Node:
    def __init__(self, val, next, random):
        self.val = val
        self.next = next
        self.random = random
```

### Examples:
```
Input: length = 5, updates = [[1,3,2],[2,4,3],[0,2,-2]]
Output: [-2,0,3,5,3]
```

```
Initial state:
[0,0,0,0,0]

After applying operation [1,3,2]:
[0,2,2,2,0]

After applying operation [2,4,3]:
[0,2,5,5,3]

After applying operation [0,2,-2]:
[-2,0,3,5,3]
```


## Solution
### Method 1 (TLE)
Just do what the problem says, one by one. worst case O(n*k*n)

```python
class Solution:
    def getModifiedArray(self, length: int, updates: List[List[int]]) -> List[int]:
        result = [0] * length
        
        for s, e, change in updates:
            for i in range(s, e+1):
                result[i] += change
        return result
        
```
### Method 2
题目是range increment，那本质上我们需要记录的数据就是再什么range上增加了多少。乍一看和input一样，但是因为我们的输出是整个最终的数组，所以我们可以丢弃的一部分信息就是每个change各是从哪到哪变了多少。

那么要把一个个change合起来，我们可以想到的一个方法就是以一种可以merge到一起的方式，记录从哪到哪增加了多少。回想一下cumulative sum，数组中每个数字代表的意思就是：从`i`起，后面的每个数字都加`a[i]`。所以`[s,d,i]`就意味着从s之后（包括s）全部加i，但是从d之后（不包括d）不加i，也就是再减去i。

```python
class Solution:
    def getModifiedArray(self, length: int, updates: List[List[int]]) -> List[int]:
        result = [0] * (length + 1)
        
        for s, e, change in updates:
            result[s] += change
            result[e+1] -= change
        
        curr_sum = 0
        for i in range(length):
            curr_sum += result[i]
            result[i] = curr_sum
        
        return result[:-1]
        
```   

### Reference
1. [Approach 2: Range Caching](https://leetcode.com/problems/range-addition/solution/)
1. [Detailed explanation if you don't understand, especially "put negative inc at [endIndex+1]"
](https://leetcode.com/problems/range-addition/discuss/84225/Detailed-explanation-if-you-don't-understand-especially-%22put-negative-inc-at-endIndex%2B1%22)