---
title: "[Leetcode 1124] Longest Well Performing Interval"
description: ""
date: "2020-01-06T21:34:46-08:00"
thumbnail: ""
categories:
  - ""
tags:
  - "medium"
---


## Question

[Leetcode 1124. Longest Well-Performing Interval](https://leetcode.com/problems/longest-well-performing-interval/)

We are given hours, a list of the number of hours worked per day for a given employee.

A day is considered to be a tiring day if and only if the number of hours worked is (strictly) greater than 8.

A well-performing interval is an interval of days for which the number of tiring days is strictly larger than the number of non-tiring days.

Return the length of the longest well-performing interval.



### Note:

1. 1 <= hours.length <= 10000
1. 0 <= hours[i] <= 16


### Examples:

```
Example 1:

Input: hours = [9,9,6,0,6,6,9]
Output: 3
Explanation: The longest well-performing interval is [9,9,6].
```


## Solution


### Method 1
我最开始想的用sliding window，但是本题无法用此方法解决的原因是sliding window划过去了，信息就丢了。但是本题很有可能还需要前面的信息：可以这样想像，每出现一个累天，我们就可以往前多带一个非累天来增加总天数。

之后就使用了accumulate sum，进行了O(n2)的算法来都试一遍，对于每个位置`i`，都看一下从开始到`i`的最长的符合条件的。accu_sum简化了检查这个步骤，但是n2少不了。TLE了。

```python
class Solution:
    def longestWPI(self, hours: List[int]) -> int:
        tiring_days = [0]
        curr_sum = 0
        for hour in hours:
            curr_sum += 1 if hour > 8 else 0
            tiring_days.append(curr_sum)
        
        result = 0
        for i in range(1, len(tiring_days)):
            for j in range(i):
                if i - j < (tiring_days[i] - tiring_days[j] ) * 2:
                    result = max(result, i - j)
        return result
```
### Method 2
看了别人的解法之后学习到：我们可以把题目抽象为，累天+1，非累-1，求最长subarray that sum > 0. 其中引用了一个数据结构 prefixSum，其实和accumulate sum是一个意思，保存了从起始到`i`的accumulate的和，但是和解法1的区别是，这里的和 >0 就意味着subarray是valid的 （而之前是代表累天的总数量，还需要进一步计算是否valid）。

而对于算法复杂度最关键的点就是，通过每次把这个和存下来，我们可以通过找之前的和来计算当前位置i的最长的valid的subarray的长度。

1. 对于位置`i`的`sum[i]`如果<=0，就意味着前面包含的`-1`比`+1`多，如果位置 `j`（ `j<i`）的`sum[j]`比位置`sum[i]`的还小，就说明`j`之前的*`-1`比`+1`多的数量*比`i`之前的更多，也就意味这`j->i`之间的*`-1`比`+1`少*。
2. 我们把每一种和的最小的位置存在map, `seen = {}`里，这样对array scan的时候，从左往右，如果当前`i`的和`sum[i]>0`那么当前就是最长的。如果小于等于0，我们找`sum[i] - 1`的位置`seen[sum[i]-1] -> j`。那么subarray的长度就是`i-j`
3. 之所以只看 `sum[i]-1`就够了，因为`sum[i]-x`，意味着从起始算起，-1比+1多了x个。但是能走到x一定是从1,2,...,x-1个加上去的。既然我们是找最长，自然就看最左边的那个，也就是只多了1的情况了。


```python
class Solution:
    def longestWPI(self, hours: List[int]) -> int:
        first_saw = {}

        result = 0
        curr_sum = 0
        for i, hour in enumerate(hours):
            curr_sum += 1 if hour > 8 else -1
            if curr_sum > 0:
                result = i + 1
            if curr_sum-1 in first_saw:
                result = max(result, i - first_saw[curr_sum-1])

            first_saw.setdefault(curr_sum, i)
        return result
```   

`setdefault(key, def_value): get(key) then if key not in map: map[key] = def_value`

### Thoughts
简化，抽象问题。本题一开始我用数数的方式来记录i到j之间超出的日子是否符合规范没有问题，没有剥离出本质，导致带的无用信息太多，容易把自己绕晕了。比如我用了
```
1.accumulate sum来记录当前正数天数有多少
2.再用 s[i] - s[j]来计算中间的累天
3. 最后再算累天*2是否小于i-j。
```
如果将题目抽象为，累天+1，非累天-1，那么就是sum[i]- sum[j]>0。

综合来看像是accumulate sum的变形应用。

## Reference
1. [[Java/C++/Python] O(N) Solution, Life needs 996 and 669](https://leetcode.com/problems/longest-well-performing-interval/discuss/334565/JavaC%2B%2BPython-O(N)-Solution-Life-needs-996-and-669)
1. [Longest Subarray with Sum greater than Equal to Zero](https://www.geeksforgeeks.org/longest-subarray-with-sum-greater-than-equal-to-zero/)
