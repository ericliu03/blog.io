---
title: "[Leetcode 975] Odd Even Jump"
description: ""
date: "2019-05-10T09:59:57-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "dp"
  - "stack"
---


## Question

[Leetcode 975: Odd Even Jump](https://leetcode.com/problems/odd-even-jump/)

You are given an integer array `A`.  From some starting index, you can make a series of jumps.  The (1st, 3rd, 5th, ...) jumps in the series are called odd numbered jumps, and the (2nd, 4th, 6th, ...) jumps in the series are called even numbered jumps.

You may from index `i` jump forward to index `j` (with `i < j`) in the following way:

1. During odd numbered jumps (ie. jumps 1, 3, 5, ...), you jump to the index `j` such that `A[i] <= A[j]` and A[j] is the smallest possible value.  If there are multiple such indexes `j`, you can only jump to the smallest such index `j`.
1. During even numbered jumps (ie. jumps 2, 4, 6, ...), you jump to the index `j` such that `A[i] >= A[j]` and A[j] is the largest possible value.  If there are multiple such indexes `j`, you can only jump to the smallest such index `j`.
1. (It may be the case that for some index `i`, there are no legal jumps.)

A starting index is good if, starting from that index, you can reach the end of the array (index `A.length - 1`) by jumping some number of times (possibly 0 or more than once.)

Return the number of good starting indexes.


### Note:
1. `1 <= A.length <= 20000`
1. `0 <= A[i] < 100000`

### Examples

Example 1:
```
Input: [10,13,12,14,15]
Output: 2
```
Explanation: 

1. From starting index i = 0, we can jump to i = 2 (since A[2] is the smallest among A[1], A[2], A[3], A[4] that is greater or equal to A[0]), then we can't jump any more.
1. From starting index i = 1 and i = 2, we can jump to i = 3, then we can't jump any more.
1. From starting index i = 3, we can jump to i = 4, so we've reached the end.
1. From starting index i = 4, we've reached the end already.

In total, there are 2 different starting indexes (i = 3, i = 4) where we can reach the end with some number of jumps.


There are other examples in leetcode.


## Solution
### Method 1
本题还是DP的思路，记录的是每个位置奇数跳和偶数跳是否能够达到终点，然后数一下在奇数跳情况下（也就是作为起点时），能到终点的数量即可。

题目要求每个位置都需要考虑他后面所有点，找出符合情况的那个，所以我们需要从后往前开始。j的找法我这里用了for loop全部看一遍(TLE)。理论上寻找的过程可以sort一下用二分查找，就变成`O(nlogn)`的复杂度。

1. `dp[i][odd] = dp[j][even]`
1. `dp[i][even] = dp[j][odd]`

#### Code
```python
class Solution:
    def oddEvenJumps(self, A: List[int]) -> int:
        dp = [[0, 0] for _ in range(len(A))]
        
        dp[-1] = [1,1]
        for i in range(len(A) -2 , -1, -1):
            mmin = float('inf')
            mmax = float('-inf')
            # search for the number/index that meets requirement
            for j in range(i+1, len(A)):
                if A[i] <= A[j] and mmin > A[j]:
                    mmin = A[j]
                    dp[i][1] = dp[j][0]
                if A[i] >= A[j] and mmax < A[j]:
                    mmax = A[j]
                    dp[i][0] = dp[j][1]
        return list(map(sum, zip(*dp)))[1]
```

### Method 2
这里使用到了monotone stack的技巧。在之前我们讲过一次monotone stack，但是这个解法会更难想到，因为stack里面的存的东西不再是雨水数量，木板面积等等，而会更抽象。 

总的来说，我们需要寻找的依然是，在每个位置分别作为偶数跳和奇数跳能否到达终点的信息。原数列给出的是位置`i`处数值的大小，我们做一次*类似*invert index操作之后拿到另外一个list，存了数字大小和其位置`[(v1, i1),(v2, i2)...(vn, in)]`。这里我们先考虑奇数跳的情况，所以按照数值**从小到大**排序*O(nlogn)*。排好序后开始loop。每次loop拿到数据`(v2,i2)`,看一下**mono_stack栈顶的元素`(v1, i1)`所代表的*位置`i1`*是否小于当前位置(`i2`。如果小于，那么说明栈顶元素`i1`的奇数下一跳就是`i2`。**

为什么呢？奇数跳的要求是，对下一跳`i2`, `i2 > i1` and **`A[i2]`是所有 `i>i1`中最小的那个**。既然我们是从小到大排序了，那么`[v2...vn]`中v2一定是大于v1的最小的那个。所以这时候，如果v2在v1的后面，也就是`i2>i1`，那么i2就一定是i1的下一个奇数跳跳。

偶数跳只是条件有所变化，你可以自己思考一下！

可以看到，这个mono_stack是单调递减(Monotonically decreasing)的，因为里面存的是位置`i1`，如果下一个`i2`，大于`i1`，我们就找到了`i1`的下一跳的位置。反过来说，如果`i2`是小于`i1`（也即在数组中`i2`在`i1`前面，那么我们是不能确定`i2`的下一跳，所以要先保存在数组中）。

#### Code
```python
class Solution:
    def oddEvenJumps(self, A: List[int]) -> int:
        n = len(A)
        
        odd_next = [0] * n
        stack = []
        for v, i in sorted([(v, i) for i, v in enumerate(A)]):
            while stack and stack[-1] < i:
                odd_next[stack.pop()] = i
            stack.append(i)
        
        even_next = [0] * n
        stack = []
        # in order to get the smaller i first, we keep the order of i (increasing) when value is same
        for v, i in sorted([(-v, i) for i, v in enumerate(A)]):
            while stack and stack[-1] < i:
                even_next[stack.pop()] = i
            stack.append(i)
        
        
        odd_reached = [0] * n
        even_reached = [0] * n
        odd_reached[-1] = even_reached[-1] = 1
        for i in range(len(A) - 2, -1, -1):
            odd_reached[i] = even_reached[odd_next[i]]
            even_reached[i] = odd_reached[even_next[i]]
        return sum(odd_reached)
```













