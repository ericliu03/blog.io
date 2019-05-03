---
title: "K Empty Slots"
description: ""
date: "2019-05-03T14:09:47-07:00"
thumbnail: ""
tags:
  - "inverted index"
  - "array"
---


## Question

[Leetcode 683: K Empty Slots](https://leetcode.com/problems/k-empty-slots)

There is a garden with `N` slots. In each slot, there is a flower. The `N` flowers will bloom one by one in`N` days. In each day, there will be **exactly** one flower blooming and it will be in the status of blooming since then.

Given an array `flowers` consists of number from `1` to `N`. Each number in the array represents the place where the flower will open in that day.

For example, `flowers[i] = x` means that the unique flower that blooms at day i will be at position `x`, where `i` and `x` will be in the range from `1` to `N`.

Also given an integer `k`, you need to output in which day there exists two flowers in the status of blooming, and also the number of flowers between them is `k` and these flowers are not blooming.

If there isn't such day, output `-1`. 

### Note:
1. The given array will be in the range `[1, 20000]`.

在reference 1 中有人提到提示题目有两个条件没有说明：

1. flowers[i] = x should mean that the unique flower that blooms at day i+1 (not i) will be at position x.
1. If you can get multiple possible results, then you need to return the minimum one.


### Examples

Example 1:
```
Input: 
flowers: [1,3,2]
k: 1
Output: 2
Explanation: In the second day, the first and the third flower have become blooming.
```
Example 2:
```
Input: 
flowers: [1,2,3]
k: 1
Output: -1
```

## Solution

### Method 1
既然每天都会开花，那么我们可以开一个数组`slots`来存当前位置的花的开放情况，然后每天扫一遍这个数组来看看符合要求的情况有没有出现。

时间复杂度为O(n2)的，因为对于每一天都需要扫一遍所有的花。

#### Code
代码大概写了一下没有优化，在LC是TLE的。

```python
class Solution:
    def kEmptySlots(self, flowers: List[int], k: int) -> int:
        
        slots = [False] * len(flowers)
        
        for i, lo in enumerate(flowers):
            slots[lo-1] = True
            if self.helper(slots, k):
                return i+1
        return -1
        
    
    def helper(self, slots, k):
        slow = 0
        fast = 0
        count = 0
        while fast < len(slots) and not slots[fast]:
            fast += 1
        slow = fast
        
        while fast < len(slots):
            non_bloomed = fast - slow - 1
            if non_bloomed == k:
                return True
            slow = fast
            fast += 1
            while fast < len(slots) and not slots[fast]:
                fast += 1
        return False
            

```

### Method 2
既然给定的要求是基于位置（相距k朵花），那么我们就不难想到把提供的信息换一种方式储存：给的array`flowers`是第`i`天开花的位置`l`，我们转换一下变成另一个数组`slots`where第`l`个位置存的是开花的时间`i`。（这个转换有点像inverted index，倒排索引，后面可以研究一下）。基于这个数组我们需要的找的就是**长度为k+2的subarray**。我们定义这个subarray边界是`slots[left]`和`slots[right]`，那么题目的要求就是对于 `slots[i] where left < i < right`, `slots[i] < slots[left] and slots[i] < slots[right]`。

关于扫描的方法，我这里使用了从ref1中给出的方法。每当遇到slot[i]比left或者right还小，就把这个作为left。原因是这个slot开花的日子比left或者right开花的日子早，所以不能在subarray中间。（感觉有点马拉车算法的意思？）

#### Code
```python
class Solution:
    def kEmptySlots(self, flowers: List[int], k: int) -> int:
        slots = [-1] * len(flowers)
        for day, slot in enumerate(flowers):
            slots[slot-1] = day + 1
        
        left = 0
        right = k + 1
        result = float('inf')
        i = 0
        while i < len(slots) and right < len(slots):
            if slots[i] < slots[left] or slots[i] <= slots[right]:
                if i == right:
                    result = min(result, max(slots[left], slots[right]))
                left = i
                right = left + k + 1
            i += 1
        return -1 if result == float('inf') else result
```

### Thoughts
1. 有一点想错了，以为两朵花之间有k个没开的，其中可能夹杂着开了的花。而实际上是有k朵花，然后他们都没开。

### Reference
1. [[Java/C++] Simple O(n) solution](https://leetcode.com/problems/k-empty-slots/discuss/107931/JavaC%2B%2B-Simple-O(n)-solution)
1. [倒排索引](https://zh.wikipedia.org/wiki/%E5%80%92%E6%8E%92%E7%B4%A2%E5%BC%95)
