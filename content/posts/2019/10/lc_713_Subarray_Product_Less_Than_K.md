---
title: "[Leetcode 713] Subarray Product Less Than K"
description: ""
date: "2019-10-18T16:01:42-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "medium"
  - "sliding window"
---

## Question

[Leetcode 713: Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/)

Your are given an array of positive integers `nums`.

Count and print the number of (contiguous) subarrays where the product of all the elements in the subarray is less than `k`.



```
Return true if there exists i, j, k
such that arr[i] < arr[j] < arr[k] given 0 ≤ i < j < k ≤ n-1 else return false.
```


### Note:

1. 0 < nums.length <= 50000.
1. 0 < nums[i] < 1000.
1. 0 <= k < 10^6.


### Examples:
```
Input: nums = [10, 5, 2, 6], k = 100
Output: 8
Explanation: The 8 subarrays that have product less than 100 are: [10], [5], [2], [6], [10, 5], [5, 2], [2, 6], [5, 2, 6].
Note that [10, 5, 2] is not included as the product of 100 is not strictly less than k.
```


## Solution

本题一看就是双指针 sliding window。乘积小了right向右扩大window，大了left指针向右缩小window。问题在于

1. 怎么计算中间的可能乘积的数量。
  1. 每次left变动时计算，也即以left为开头的subarray可组成的乘积种类。比如right到了4时超过了k in `{1,2,3,4}`，那么这时我们需要变动left。可能的乘积组合就是 `{1,2,3}, {1,2}, {1}`。这样计算有个问题就是当right到头了之后，left依然需要往右，循环的condition很难搞清，或者需要两个循环。
  1. 每次right变动时计算。因为right是所有数字都过了一遍，所以就能保证考虑到了以所有数字作为有边界的情况。
1. 如何定义left和right和window的边界的关系。
  1. 一般来说，我们双指针的时候会把window的边界都定义在左边：左边界在left的左边，有边界在right的左边。这样`right - left`就等于window的长度。但是本题如果用此方法就必须分开处理当`right=len(nums)`的情况，因为这时我们即不能再`nums[right]`，但又必须去计算`right - left`因为window的边界还是valid的。
  1. window的边界在left的左边和right的右边。
1. sliding window 有时候会在循环里套循环来移动left，本质上和用if 然后在大循环中每次either移动left or right没有本质区别。

### Method 1
用了一般的边界定义，可以看到在while循环里有if check。

```python
class Solution:
    def numSubarrayProductLessThanK(self, nums: List[int], k: int) -> int:
        if k <= 1:
            return 0
        left = 0
        right = 0
        result = 0
        curr_product = 1
        while right <= len(nums):
            while curr_product >= k and left < right:
                curr_product /= nums[left]
                left += 1
            
            result += right - left
            if right < len(nums):
                curr_product *= nums[right]
            right += 1
        return result
```

### Method 2
我们在for loop里最开始就把当前的right指向的数字加了进去，也就意味这right所指数字是在window内部的。所以在内部的while循环中，用了`left <= right`，因为相等的时候window并不是空的。

```python
class Solution:
    def numSubarrayProductLessThanK(self, nums: List[int], k: int) -> int:
        if k <= 1:
            return 0
        left = 0
        right = 0
        result = 0
        curr_product = 1
        for right in range(len(nums)):
            curr_product *= nums[right]

            while curr_product >= k and left <= right:
                curr_product /= nums[left]
                left += 1
            result += right + 1 - left

        return result
```
