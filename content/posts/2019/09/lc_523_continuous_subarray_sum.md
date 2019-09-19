---
title: "[Leetcode 523] Continuous Subarray Sum"
description: ""
date: "2019-09-18T20:47:07-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "medium"
---


## Question

[Leetcode 523: Continuous Subarray Sum](https://leetcode.com/problems/continuous-subarray-sum/)

Given a list of **non-negative** numbers and a target **integer** k, write a function to check if the array has a continuous subarray of size at least 2 that sums up to a multiple of **k**, that is, sums up to n\*k where n is also an integer.


### Note:

1. The length of the array won't exceed 10,000.
1. You may assume the sum of all the numbers is in the range of a signed 32-bit integer.


### Examples
```
Example 1:

Input: [23, 2, 4, 6, 7],  k=6
Output: True
Explanation: Because [2, 4] is a continuous subarray of size 2 and sums up to 6.
Example 2:

Input: [23, 2, 6, 4, 7],  k=6
Output: True
Explanation: Because [23, 2, 6, 4, 7] is an continuous subarray of size 5 and sums up to 42.
```

## Solution
### Method 1
Brute Force.  对于每个数字，我们去看一下以他结尾和以所有他之前的数字开头的组合有没有符合题目条件的。

### Method 2
可以注意到题目是multiple 乘以 k，所以就可以想到mod计算。我们这里利用了下面的math：
```
let a % k = x and b % k = x
then (a - b) % k = (a % k) - (b % k) = x - x = 0
```
如果a，b分别是从0到i,j的 cumulative sum，那么通过看他们mod k 之后是否相等，就能判断`a - b`，也即`sum(nums[i,j+1])`是否能被k整除。

优化一下，我们不存cumulative sum，而是存求余之后的值，那么就能通过等式中第二段的式子来判断在`j`之前，`x`有没有出现过，就能够判断是否有可能出现题目所求的情况。

有两点edge case需要注意

1. 至少连续两个值求和，所以我们还要存`x`出现的位置。
1. `k = 0`这种情况，其实就是求和为0。所以我们需要找的就是有没有连续的两个0出现。

#### Code

```python
class Solution:
    def checkSubarraySum(self, nums: List[int], k: int) -> bool:
        if k == 0:
            return any(nums[i] == 0 and nums[i+1] == 0 for i in range(len(nums) - 1))
        modulos = {0: -1}
        c_sum = 0
        for i, num in enumerate(nums):
            c_sum = (c_sum + num)  % k              
            if c_sum in modulos and i - modulos[c_sum] >= 2:
                return True
            if c_sum not in modulos:
                modulos[c_sum] = i
        return False
```

### Reference
1. [Simple Python (10 lines) with Explanation 58ms O(n) time - O(k) space](https://leetcode.com/problems/continuous-subarray-sum/discuss/99566/Simple-Python-(10-lines)-with-Explanation-58ms-O(n)-time-O(k)-space)

