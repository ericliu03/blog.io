---
title: "[Leetcode 678] Valid Parenthesis String"
description: ""
date: "2019-10-25T11:34:38-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "medium"
---

## Question

[Leetcode 678: Valid Parenthesis String](https://leetcode.com/problems/valid-parenthesis-string/)

Given a string containing only three types of characters: `'(', ')' and '*'`, write a function to check whether this string is valid. We define the validity of a string by these rules:

1. Any left parenthesis `'('` must have a corresponding right parenthesis `')'`.
1. Any right parenthesis `')'` must have a corresponding left parenthesis `'('`.
1. Left parenthesis `'('` must go before the corresponding right parenthesis `')'`.
1. `'*'` could be treated as a single right parenthesis `')'` or a single left parenthesis `'('` or an empty string.
1. An empty string is also valid.


```
Return true if there exists i, j, k
such that arr[i] < arr[j] < arr[k] given 0 ≤ i < j < k ≤ n-1 else return false.
```

### Examples:
```
Example 1:
Input: "()"
Output: True
Example 2:
Input: "(*)"
Output: True
Example 3:
Input: "(*))"
Output: True
```

### Note:

1. The string size will be in the range [1, 100].



## Solution
### Method 1
本题和之前的陨石碰撞题目有点像，看到题目自然而然想到用stack，而tricky的地方是`*`可以变为empty。所以如果遇到右括号来pop stack的时候看到`*`了是无法确实是否应该与他配对。比如`(*)`。

有一个办法就是让右括号优先和左括号配对，不够了再用`*`。最后如果有剩左括号再与`*`配对。需要注意的一点是左括号配对时只能配其右边的`*`。

```python
from collections import defaultdict

class Solution:
    def checkValidString(self, s: str) -> bool:
        star_count = 0
        stack = defaultdict(list)
        
        for i, each in enumerate(s):
            if each == '(' or each == '*':
                stack[each].append(i)
            else:
                if stack['(']:
                    stack['('].pop()
                elif stack['*']:
                    stack['*'].pop()
                else:
                    return False
        while stack['*'] and stack['('] and stack['*'][-1] > stack['('][-1]:
            stack['('].pop()
            stack['*'].pop()

        return not stack['(']
```
### Method 2
上面的代码有点冗余。既然题目是说能不能配对，我们就不需要在意具体谁和谁配对（虽然我们上边找出的配对顺序也不对）。只需要数数就好了。

数的是未配对左括号可能的存在的数量。因为`*`存在的原因，`(*`可以是`()`，`(`（`*`代表空的时候）或者`((`。也就是说未配对左括号可能是0个，1个或者2个，也就是区间[0,2)。因为范围总是`*`带来的，而`*`又可以-1，0，+1未配对左括号的数量，所以区间一定是连续的。

注意lo和hi代表的是`未配对左括号数量`的区间边界。`checkValidString2()`更明显能看出来在不同情况下区间的变化情况。
```python
class Solution:
    def checkValidString(self, s: str) -> bool:
        lo = 0
        hi = 0
        for c in s:
            lo += 1 if c == '(' else -1
            lo = max(lo, 0)

            hi += 1 if c == '(' or c == '*' else -1
            if hi < 0:
                return False

        return lo == 0

    def checkValidString2(self, s: str) -> bool:
        lo = 0
        hi = 0
        for c in s:
            if c == '(':
                lo += 1
                hi += 1
            elif c == '*':
                hi += 1
                lo -= 1
            else:
                lo -= 1
                hi -= 1
            lo = max(lo, 0)
            if hi < 0:
                return False

        return lo == 0
```   

### Method 3
还有一种（2维）DP的方法，简单来说就是dp[i][j]代表s[i:j+1]是valid的括号组合。转移方程:

1. s[i] = * :dp[i][j] = dp[i+1][j]
1. s[i] = ( : dp[i][j] = 对于k 属于 [i+1:j]， 存在dp[i+1][k-1] == dp[k+1][j] == True
1. s[i] = )就不行啦