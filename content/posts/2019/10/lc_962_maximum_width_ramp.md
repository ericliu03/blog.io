---
title: "[Leetcode 962] Maximum Width Ramp"
description: ""
date: "2020-01-10T21:49:43-08:00"
thumbnail: ""
categories:
  - ""
tags:
  - "stack"
  - "monostack"
---

## Question

[Leetcode 962. Maximum Width Ramp](https://leetcode.com/problems/maximum-width-ramp/)

Given an array A of integers, a ramp is a tuple (i, j) for which i < j and A[i] <= A[j].  The width of such a ramp is j - i.

Find the maximum width of a ramp in A.  If one doesn't exist, return 0.



### Note:

1. 2 <= A.length <= 50000
1. 0 <= A[i] <= 50000


### Examples:

```
Example 1:

Input: [6,0,8,2,1,5]
Output: 4
Explanation: 
The maximum width ramp is achieved at (i, j) = (1, 5): A[1] = 0 and A[5] = 5.
Example 2:

Input: [9,8,1,0,1,9,4,0,4,1]
Output: 7
Explanation: 
The maximum width ramp is achieved at (i, j) = (2, 9): A[2] = 1 and A[9] = 1.
```


## Solution


### Method 1
最简单的就是按着题目给出的思路走，两层for loop一个一个试。


```python
class Solution:
    def maxWidthRamp(self, A: List[int]) -> int:
        
        result = 0
        for j in range(1, len(A)):
            for i in range(j):
                if A[i] <= A[j]:
                    result = max(result, j-i)
                    
        return result
```

### Method 2
提示要用stack，自然就想到了用monostack。但是这题和之前讲的monostack的有变化

1. 没有拿出的过程：对于每个来的新数，都要看一下是否比栈顶的小，如果小，那就是比stack所有的数字小，所以不可能和前面的数字配对，但是有可能会作为后面某个数的配对，所以要存下来。如果大，那么就要找出下标最小的又比新数小的那个数，（用二分可以logn），位置的距离就是可能的答案。但是，找到了也不能删除，因为有可能后边的数还要用到。
1. 每一个新的数据来了之后，都需要和stack中的所有数比较找出符合条件的那个，而不是只看栈顶。

有一个优化，来源是：因为我们是找最大，也就是说当我们的monostack包含了全部数字之后，我们从右往左看，假设`A[j] where j = len(A)-1` 的时候`i<j and A[i]<=A[j]`，那么j-1就没有必要和i再比较了，因为`j-1 - i < j-i`。所以对于j，我们找到最小的那个符合条件的i之后，在j到i之间的这些数字就不用在作为配对的左数来考虑了，因为一定小于`j-i`。

```python
def minDeletionSize(self, A):
        res, n, m = 0, len(A), len(A[0])
        unsorted = set(range(n - 1))
        for j in range(m):
            if any(A[i][j] > A[i + 1][j] for i in unsorted):
                res += 1
            else:
                unsorted -= {i for i in unsorted if A[i][j] < A[i + 1][j]}
        return res
```


### Thoughts
1. mono stack的活用，本题最终解法还是有些难度的。需要考虑
 1. stack存的数字代表了什么
 1. 每个新的数字来了如何处理
 1. 找最大的性质

## Reference
1. [[Java/C++/Python] O(N) Using Stack
](https://leetcode.com/problems/maximum-width-ramp/discuss/208348/JavaC%2B%2BPython-O(N)-Using-Stack)