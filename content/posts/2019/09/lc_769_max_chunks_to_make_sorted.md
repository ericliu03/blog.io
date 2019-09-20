---
title: "[Leetcode 768/769] Max Chunks To Make Sorted"
description: ""
date: "2019-09-20T14:08:46-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "hard"
  - "medium"
---

 
## Question 1
[Leetcode 769: Max Chunks To Make Sorted II](https://leetcode.com/problems/max-chunks-to-make-sorted-ii/)


Given an array `arr` of integers (**not necessarily distinct**), we split the array into some number of "chunks" (partitions), and individually sort each chunk.  After concatenating them, the result equals the sorted array.

What is the most number of chunks we could have made?


### Note:

1. arr will have length in range [1, 2000].
1. arr[i] will be an integer in range [0, 10**8].


### Examples
```
Example 1:

Input: arr = [5,4,3,2,1]
Output: 1
Explanation:
Splitting into two or more chunks will not return the required result.
For example, splitting into [5, 4], [3, 2, 1] will result in [4, 5, 1, 2, 3], which isn't sorted.
Example 2:

Input: arr = [2,1,3,4,4]
Output: 4
Explanation:
We can split into two chunks, such as [2, 1], [3, 4, 4].
However, splitting into [2, 1], [3], [4], [4] is the highest number of chunks possible.
```

## Solution
### Method 1
切分需要知道能把arr切断的条件，假设我们把index `i`作为一个chunk的右边界，那么就需要一个chunk里的所有数字都 <= chunk右边的数字（等于是因为会有重复）。下面的代码中：

1. `min_to_right` 是每个位置右边所有数字的最小值
2. curr_min被用来记录当前chunk的最大值，如果找到了chunk，就init为下一个chunk的第一个值。

### Code
```python
class Solution:
    def maxChunksToSorted(self, arr: List[int]) -> int:
        min_to_right = []
        curr_min = float('inf')
        r = 0
        for i in range(len(arr) - 1, -1, -1):
            min_to_right.append(curr_min)
            curr_min = min( arr[i], curr_min)
        min_to_right = min_to_right[::-1]
        
        curr_max = arr[0]
        for i in range(len(arr)):
            if  arr[i] <= min_to_right[i] and curr_max <= min_to_right[i]:
                r += 1
                if i + 1 < len(arr):
                    curr_max = arr[i+1]
            else:
                curr_max = max(arr[i], curr_max)
        return r
```

### Method 2
chunk中的数字小于chunk右边的所有数字，那么chunk左边的所有数字一定小于chunk中的任意数字。
如果我们以chunk的右边界，也就是切割位置来考虑的话，切的位置一定保证了右边的所有数字比左边的所有数字都要大。
换句话说：

1. a = i右边的所有数字中的最小值
2. b = arr[i]和其左边所有数字中的最大值
如果满足 a <= b，那么我们就可以把i作为一个chunk右边界来切割arr。

#### Code

```python
class Solution:
    def maxChunksToSorted(self, arr: List[int]) -> int:
        import math
        right_min = [math.inf] * len(arr)
        left_max = arr.copy()
        for i in range(len(arr) - 2, -1, -1):
            right_min[i] = min(arr[i + 1], right_min[i + 1])
        for i in range(1, len(arr)):
            left_max[i] = max(left_max[i - 1], arr[i])
        chunks = 0
        for i in range(len(arr)):
            if right_min[i] >= left_max[i]:
                chunks += 1
        return chunks
```
## Question 2
[Leetcode 769: Max Chunks To Make Sorted](https://leetcode.com/problems/max-chunks-to-make-sorted/)

与上题不同的是这里的数字限制为[0,len(arr)-1].

## Solution
### Method 1
上题的两个解法都能用在这里。除此之外，我们还可以用本题的条件：数字的范围决定了排好序的数组一定是`arr[i] = i`。所以我们可以省去right_min这个信息，只看左边最大的数字。一旦最大的数字与当前i相等，那么我们就能确定一个chunk。

#### Code
```python
class Solution:
    def maxChunksToSorted(self, nums: List[int]) -> int:
        curr_max = 0
        result = 0
        for i in range(len(nums)):
            if nums[i] > curr_max:
                curr_max = nums[i]
            if i == curr_max:
                result += 1
        return result
```




### Reference
1. 两个解法使用了`two pass`的思想，可以参考： [Two Pass for Min/Max/Sum/Product]({{<ref "/posts/2019/04/two_pass.md">}})
1. [Java solution, left max and right min](https://leetcode.com/problems/max-chunks-to-make-sorted-ii/discuss/113462/Java-solution-left-max-and-right-min.)

