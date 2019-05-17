---
title: "[Leetcode 560] Subarray Sum Equals K"
description: ""
date: "2019-05-14T11:23:20-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "array"
  - "cumulative sum"
---


## Question

[Leetcode 560: Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)

Given an array of integers and an integer k, you need to find the total number of continuous subarrays whose sum equals to k.

### Note:
1. The length of the array is in range [1, 20,000].
1. The range of numbers in the array is [-1000, 1000] and the range of the integer k is [-1e7, 1e7].


### Examples

Example 1:
```
Input:nums = [1,1,1], k = 2
Output: 2
```

## Solution
### Method 1 (TLE)
我一开始想到的解法是使用一个存了dict的array，记录下每个位置上出现的所有可能的sum的数量。这样进行到每一位的时候只需要看一下他前面那个位置的dict，就能得到包含当前位置数字所有可能出现的sum。

这个方法有点DP的感觉，但实际上是走了 cumulative的路线。cumulative的路线更简单一些，就是在每个位置`i`存`cum_sum[i] = sum(num[:i+1])`。这样我们在每个位置就扫一遍前面的所有的sum，做个减法就知道是否有k出现。

#### Code
```python
from collections import defaultdict
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        n = len(nums)
        dp = [defaultdict(int) for _ in range(n+1)]
        
        for i in range(n):
            for number, count in dp[i].items():
                dp[i+1][number + nums[i]] += count
            dp[i+1][nums[i]] += 1
        
        return sum(dp[i+1][k] for i in range(n))
```

### Method 2
这个方法利用到了cumulative sum，hashmap，和题目的要求（只求数量）。

首先hashmap是用来存每一种cumulative sum出现了多少次，然后在每个位置看一下**当前位置`cum_sum[i]`减去k的值**是否在hashmap中存在。如果存在`hashmap[cum_sum[i]-k] = x`，就意味着之前有过`cum_sum[i] - cum_sum[某一位置] = k`的情况，而且出现了x次。换句话说，我们找到了x个以i结尾的subarray，他们的和等于k。



#### Code
```python
from collections import defaultdict
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        n = len(nums)
        cum_sum = defaultdict(int)
        cum_sum[0] = 1
        curr_sum = 0
        result = 0
        
        for num in nums:
            curr_sum += num
            result += cum_sum[curr_sum - k]
            # we don't want to use current cum_sum to minus itself
            # so we calculate before add it to hashmap
            # mostly for the case where k = 0
            cum_sum[curr_sum] += 1
            
        return result
```
