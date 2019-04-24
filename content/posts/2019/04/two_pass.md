---
title: "Two Pass for Min/Max/Sum/Product"
date: 2019-04-23T21:23:24-07:00
description: ""
dropCap: false
displayInMenu: false
displayInList: true
draft: false
---

今天我们讲解一种算法思想。这种方法一般使用在需要把整个数组分成2份的情况，求这两份组成的是最大、最小、和最大之类的。下面我们来看三道由易到难的题目。


## Question

[Leetcode 238: Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self)

Given an array `nums` of n integers where n > 1,  return an array `output` such that `output[i]` is equal to the product of all the elements of `nums` except `nums[i]`.

### Examples

```
Input:  [1,2,3,4]
Output: [24,12,8,6]
```

#### Follow up:
1. Please solve it without division and in O(n).
1. Could you solve it with constant space complexity? (The output array does not count as extra space for the purpose of space complexity analysis.)

## Solution
### Method 1
求全部数字的乘积`mul`，然后生成output的时候把`mul`除以当前数字就是结果。time complexity是O(n)，但是用到了除法。

### Method 2
为了不用除法我们可以对每个数字都遍历一边他左边和右边的数字求乘积，但是是O(n2)。

### Method 3
我们扫两遍数组，计算出两个数组`left[i]` 和 `right[i]`，分别是从0到i和从i到n-1的累积乘积。在计算结果时对于每个数计算`left[i-1]*right[i+1]`即可。没有使用除法，也是O(n)。但是用了O(n)的space

### Method 4
既然output不算做extra space，那么我们可以利用他当做left，然后在计算right数组的同时计算结果。这样就满足了所有follow up 条件。

#### Code
我们这里只写method 4 的code了。
```python
def productExceptSelf(nums):
    left = []
    
    curr = 1
    for i in range(len(nums)):
        curr *= nums[i]
        left.append(curr)
    
    curr = 1
    for i in range(len(nums)-1,0,-1):
        left[i] = left[i-1] * curr
        curr *= nums[i]
    left[0] = curr
    
    return left

```

## Question 2
[Leetcode 42: Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

### Example
<img class="special-img-class" style="width:50%" src="/img/monotone_stack_img2.png" />
The above elevation map is represented by array `T=[0,1,0,2,1,0,1,3,2,1,2,1]`. In this case, 6 units of rain water (blue section) are being trapped. 

```
Input: [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
```

## Solution
### Method 1
这题我们之前在monotone stack中讲解了一种解法，我们这次来看two pass如何使用在这道题上。

从题目出发，我们需要找出每个点上可以存的水的数量（高度），能存多高取决于这个点左右两边最高的高度，也就是边界高度。但是相邻的左右两个点未必是其边界，我们需要找左右两边的最高的高度。所以我们可以扫描两边数组，找出对于位置`i`左边的和右边的最高的elevation的高度。

#### Code
```python
def trap(heights):
    n = len(heights)
    left = right = [0] * n
    l_max = r_max = 0
    result = 0
    
    for i in range(n):
        l_max = max(l_max, heights[i])
        r_max = max(r_max, heights[n-1 - i])
        left[i] = l_max
        right[n-1 - i] = r_max
    
    for i in range(n):
        water = min(left[i], right[i]) - heights[i]
        result += water if water > 0 else 0
    
    return result
```

## Question 3

[Leetcode 1031: Maximum Sum of Two Non-Overlapping Subarrays](https://leetcode.com/problems/maximum-sum-of-two-non-overlapping-subarrays/)

Given an array A of non-negative integers, return the maximum sum of elements in two non-overlapping (contiguous) subarrays, which have lengths L and M.  (For clarification, the L-length subarray could occur before or after the M-length subarray.)

Formally, return the largest V for which `V = (A[i] + A[i+1] + ... + A[i+L-1]) + (A[j] + A[j+1] + ... + A[j+M-1]) `and either:`0 <= i < i + L - 1 < j < j + M - 1 < A.length`, or `0 <= j < j + M - 1 < i < i + L - 1 < A.length`.

### Examples:
Example 1:
```
Input: A = [0,6,5,2,2,5,1,9,4], L = 1, M = 2
Output: 20
Explanation: One choice of subarrays is [9] with length 1, and [6,5] with length 2.
```
Example 2:
```
Input: A = [3,8,1,3,2,1,8,9,0], L = 3, M = 2
Output: 29
Explanation: One choice of subarrays is [3,8,1] with length 3, and [8,9] with length 2.
```
Example 3:
```
Input: A = [2,1,5,6,0,9,5,0,3,8], L = 4, M = 3
Output: 31
Explanation: One choice of subarrays is [5,6,0,9] with length 4, and [3,8] with length 3.
```

## Solution
### Method 1
题目形容的有点难懂，简单来说就是在给的array中找出两个不重叠的长度为L和M的subsequence，他们的和最大。L和M的两个subsequence元素的加入让题目复杂了一些，但是仔细一想两个不重叠的subsequence，其实也可以看做是在`index = i`处的左右两边的两个数组。那么这样理解之后解法其实就与上面的题目一样了，只是元素多了一点：

1. 我们从左往右，找出在每个点`i`处，左边为L长度和左边为M长度的最大值，放在两个数组里。
1. 再从右往左，找出左边为M和左边为L长度的放到另外两个数组中。

代码可以参考一下[这里](https://leetcode.com/problems/maximum-sum-of-two-non-overlapping-subarrays/discuss/278727/C%2B%2B-O(N)-buysell-stock-2-times)

### Method 2
虽然method 1我们存了4个数组出来，但其实和Q1M3的解法是相同的。下面我们做一些类似于Q1M4一样的优化。

1. 和Q1M4一样，在第二遍从右往左扫描时直接计算结果。（节省一半储存和计算结果的一遍扫描）
1. 不算最大值，算cumulative sum。subsequence的sum直接使用减法计算`sum_left_to_right = A[left] - A[right]`。最大值在扫描时计算。（再节省一半储存）

因为题目的特性，我们可以在第二遍依然从左往右走，不断update两个变量l_left, m_left,分别为左边长度为l和m的sequence的sum的最大值。扫描的同时计算出每一个A[i-L]和A[i-M]的值，作为右边对应的长度为M和L的值。求和即为可能结果，因为左边是max，右边又扫描到了每一个长度为L和M的sum
，所以最大的和一定就是结果。
#### Code
```python
def maxSumTwoNoOverlap(A, L, M):
    for i in range(1, len(A)):
        A[i] += A[i - 1]

    l_left = A[L - 1]
    m_left = A[M - 1]
    result = A[L + M - 1]

    for i in range(L + M, len(A)):
        l_left = max(l_left, A[i - M] - A[i - M - L])
        m_left = max(m_left, A[i - L] - A[i - L - M])

        l_right = A[i] - A[i - L]
        m_right = A[i] - A[i - M]

        result = max(result, l_left + m_right, m_left + l_right)

    return result
```

## Summary
总的来说这个解法就是对给定的数据做一些预处理，然后在处理之后的数据上求需要的结果。

## Reference 
1. more questions:
 1. [Leetcode 123: Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/), [solution](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/discuss/39665/Java-solution-with-just-two-traverses.)
1. [Q3解法1](https://leetcode.com/problems/maximum-sum-of-two-non-overlapping-subarrays/discuss/278727/C%2B%2B-O(N)-buysell-stock-2-times)
1. [Q3解法2](https://leetcode.com/problems/maximum-sum-of-two-non-overlapping-subarrays/discuss/278251/JavaC%2B%2BPython-O(N)Time-O(1)-Space)
1. [[LeetCode] Trapping Rain Water 收集雨水](https://www.cnblogs.com/grandyang/p/4402392.html)