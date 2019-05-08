---
title: "[Leetcode 552] Student Attendance Record II"
description: ""
date: "2019-05-06T10:54:57-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "dp"
---


## Question

[Leetcode 552: Student Attendance Record II](https://leetcode.com/problems/frog-jump/)

Given a positive integer n, return the number of all possible attendance records with length n, which will be regarded as rewardable. The answer may be very large, return it after mod `10^9 + 7`.

A student attendance record is a string that only contains the following three characters:

1. 'A' : Absent.
1. 'L' : Late.
1. 'P' : Present.


A record is regarded as rewardable if it doesn't `contain more than one 'A' (absent)` or `more than two continuous 'L' (late)`.

### Note:

1.  The value of `n` won't exceed `100,000`. 

### Examples

Example 1:
```
Input: n = 2
Output: 8 
Explanation:
There are 8 records with length 2 will be regarded as rewardable:
"PP" , "AP", "PA", "LP", "PL", "AL", "LA", "LL"
Only "AA" won't be regarded as rewardable owing to more than one absent times. 
```

## Solution
### Method 1

为了得到长度为n的所有rewardable的string（定义为f(n))，我们可以从长度n-1的string中加上L和P来得到。但是因为不能存在3个连续的L，所以在所有rewardable且长度为n-1的string中，以LL结尾的我们不能要。如果你觉得长度为n-1且以LL结尾的string数量可以用`f(n-3) + LL`来表示就错了，因为f(n-3)包含了以L结尾的string，而以L结尾的是不能链接`LL`的，也就是说不会存在于f(n-1）包含的情况中。因此， f(n-1)中以LL结尾的情况就等于f(n-4) + PLL。所以f(n) = f(n-1) * 2 - f(n-4) * 1。

当我们找到这个transition function之后解法就和fibonacci数列没什么不同了。这题是把两种不同想法混合了起来，1是回溯计算的思想，2是组合

#### Code

```python
class Solution:
    def checkRecord(self, n: int) -> int:
        dp = [1,2,4,7]
        m = 10**9 + 7
        
        for i in range(4, n+1):
            dp.append((2 * dp[i-1] - dp[i-4])%m)
        
        r = dp[n]
        for i in range(1, n+1):
            r += (dp[i-1] * dp[n-i])

        return r %m
```