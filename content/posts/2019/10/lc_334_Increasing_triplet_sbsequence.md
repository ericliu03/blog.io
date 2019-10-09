---
title: "[Leetcode 334] Increasing Triplet Subsequence"
description: ""
date: "2019-10-03T11:25:01-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "medium"
---
## Question

[Leetcode 334: Increasing Triplet Subsequence](https://leetcode.com/problems/increasing-triplet-subsequence/)

Given an unsorted array return whether an increasing subsequence of length 3 exists or not in the array.

Formally the function should:

```
Return true if there exists i, j, k
such that arr[i] < arr[j] < arr[k] given 0 ≤ i < j < k ≤ n-1 else return false.
```


### Note:
1. Your algorithm should run in O(n) time complexity and O(1) space complexity.

### Examples:
```
Example 1:

Input: [1,2,3,4,5]
Output: true
Example 2:

Input: [5,4,3,2,1]
Output: false
```


## Solution
### Method 1

本题的突破点是increasing subsequence是固定的三个。所以我们可以用两个variable （first，second）来存前两个数字。当下一个数字比他们两个的某个小的时候就更新，如果比second大那么就找到了。

```python
class Solution:
    def increasingTriplet(self, nums: List[int]) -> bool:
        first = -1 
        second = -1
        for i, num in enumerate(nums):
            if first == -1 or num < nums[first]:
                first = i
            elif num > nums[first]:
                second = i if second == -1 or num < nums[second] else second
            
            if second != -1 and num > nums[second]:
                return True
        return False
```
### Method 2
上面的代码有点冗余。如果我们`first`和`second` init为`float('inf')`，我们就可以直接进行比较了。其次，我们用`if..else..`来限制每个新的数字的可比较范围。比如新的数字比first小，那么一定的他会取代first，而不会去比较second。最后，我们不需要保证second一定要大于first，这两个variable代表的分别是，目前为止最小的数字，和`前面有一个更小的数字的`最小的数字。所以只要有一个数字比second还大，就能保证题目要求了。
```python
class Solution:
    def increasingTriplet(self, nums: List[int]) -> bool:
        first = float('inf') 
        second = float('inf') 
        for num in nums:
            if num <= first:
                first = num
            elif num <= second:
                second = num
            else:
                return True
        return False
```   