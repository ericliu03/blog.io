---
title: "Uncrossed Lines"
date: 2019-04-28T10:46:05-07:00
description: ""
dropCap: false
displayInMenu: false
displayInList: true
draft: false
---

## Question

[Leetcode 1035: Uncrossed Lines](https://leetcode.com/problems/uncrossed-lines/)

We write the integers of `A` and `B` (in the order they are given) on two separate horizontal lines.

Now, we may draw a straight line connecting two numbers `A[i]` and `B[j]` as long as `A[i] == B[j]`, and the line we draw does not intersect any other connecting (non-horizontal) line.

Return the maximum number of connecting lines we can draw in this way.

### Examples

```
1 4 2
|  \
1 2 2
```
Example 1:

```
Input: A = [1,4,2], B = [1,2,4]
Output: 2
Explanation: We can draw 2 uncrossed lines as in the diagram.
We cannot draw 3 uncrossed lines, 
because the line from A[1]=4 to B[2]=4 will intersect the line from A[2]=2 to B[1]=2.
```
Example 2:
```
Input: A = [2,5,1,2,5], B = [10,5,2,1,5,2]
Output: 3
```
Example 3:
```
Input: A = [1,3,7,1,7,5], B = [1,9,2,5,1]
Output: 2
```
#### Note:
1. 1 <= A.length <= 500
1. 1 <= B.length <= 500
1. 1 <= A[i], B[i] <= 2000

## Solution

### Method 1 （错误想法）
这个题最直接的想法就是使用recursion来遍历，可能会想到下面这种解法：对于A中每一个数字`i`，for loop B找到其中一样的那些数字`j`。对于每个match的`i，j`，找到他们之后的，也就是`A[i+1:]`和`B[j+1:]`能最多match的数量。这种方法有一个问题就是把for loop和recursion的思想混用了，会大大提高把自己绕晕的概率。。。

### Method 2
我们上述recursion的定义是对于从i, j开始的A，B，最多match的数量，我们可以写作`longest_match(i, j, A, B)`。那么有两种情况：1）`A[i] == B[j]`, 所以`longest_match(i, j, A, B)` 结果之一等于 `longest_match(i+1, j+1, A, B)` + 1（因为现在多match了一个）。2）不相等，两种子情况就是`longest_match(i+1, j, A, B)` 和`longest_match(i, j+1, A, B)`。这三种情况都考虑到，也就意味着所有的可能性都考虑到了，只是我们返回结果时值保留了最大的那种情况的数字。回过头来看上面带forloop的方法，就做了很多重复运算了。
### Method 3
方法2依然存在一些重复运算，比如 当然是`i,j`,我们需要拿到`i+1,j+1`，`i+1,j`和`i,j+1`，那么到了`i+1,j`时，我们需要拿到`i+2,j+1`，`i+1,j+1`和`i+2,j`。所以可以使用DP：1）用memo存中间结果，2）用DP数组直接算结果。（[关于从recursion到DP的文章](https://ericliu03.github.io/posts/2019/04/longest_increasing_subsequence/))

关于输出序列可以参考ref 3和4.

本题其实就是[longest common subsequence](https://www.google.com/search?newwindow=1&q=longest+common+subsequence)。

#### Code
```python
def maxUncrossedLines(A, B):
    dp = [[0] * (len(B)+1) for _ in range(len(A)+1)]
    
    for i in range(1, len(A) + 1):
        for j in range(1, len(B) + 1):
            dp[i][j] = dp[i-1][j-1] + 1 if A[i-1] == B[j-1] else 0
            dp[i][j] = max(dp[i][j], dp[i-1][j], dp[i][j-1])

    return dp[-1][-1]
```

## Thoughts

对比最简单的DP，比如[爬梯子](https://leetcode.com/problems/climbing-stairs/)，我想错的地方（method1）就是对于每个`i`我需要loop所有`j`，然后再recursion，没有想清楚recursion的输入输出是什么，也就是转移方程是什么。就像梯子题，对于`i`，我只需要看`i-1`和`i-2`，具体到第`i-1`阶的时候有多少种可能性， 都是怎么来的不care。题目规定只能从那两个地方过来，但是本题没有这种明显的规定，需要想清楚`i,j`只能从那3个地方过来，而不是可以从所有j过来。

有一点令我耿耿于怀的就是`i,j`只能从`i+1,j+1`，`i+1,j`和`i,j+1`这三个地方过来，那如果如果我是一个`A[i]`和B`[j+100]`match怎么办，看着好像没考虑进去。但实际上是算在里面了的，因为`i,j`可以到`i,j+1`，`i,j+1`也考虑到了`i, j+2`的情况。所以在`i,j`的位置上，我们看`i,j+1`的时候就已经包含了`i,j+100`的情况。如果这种情况match的最多，那么就是被保留下来的那一个。只不过在结果中我们不知道到底是match到了`j+?`。想要知道的话还需要回溯去找出他们。

## Reference
1. [wikipedia](https://en.wikipedia.org/wiki/Longest_common_subsequence_problem)
1. [Longest Common Subsequence | DP-4](https://www.geeksforgeeks.org/longest-common-subsequence-dp-4/)
1. [LCS+路径还原](https://blog.csdn.net/qq_36368339/article/details/76944448)
1. [【动态规划】输出所有的最长公共子序列](https://songlee24.github.io/2014/11/29/print-all-LCS/)
