---
title: "[Leetcode 735] Asteroid Collision"
description: ""
date: "2019-10-09T11:27:59-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "medium"
---

## Question

[Leetcode 735: Asteroid Collision](https://leetcode.com/problems/asteroid-collision/)

We are given an array `asteroids` of integers representing asteroids in a row.

For each asteroid, the absolute value represents its size, and the sign represents its direction (positive meaning right, negative meaning left). Each asteroid moves at the same speed.

Find out the state of the asteroids after all collisions. If two asteroids meet, the smaller one will explode. If both are the same size, both will explode. Two asteroids moving in the same direction will never meet.


### Note:
1. The length of asteroids will be at most `10000`
1. Each asteroid will be a non-zero integer in the range `[-1000, 1000]`
### Examples:
```
Example 1:
Input: 
asteroids = [5, 10, -5]
Output: [5, 10]
Explanation: 
The 10 and -5 collide resulting in 10.  The 5 and 10 never collide.

Example 2:
Input: 
asteroids = [8, -8]
Output: []
Explanation: 
The 8 and -8 collide exploding each other.

Example 3:
Input: 
asteroids = [10, 2, -5]
Output: [10]
Explanation: 
The 2 and -5 collide resulting in -5.  The 10 and -5 collide resulting in 10.

Example 4:
Input: 
asteroids = [-2, -1, 1, 2]
Output: [-2, -1, 1, 2]
Explanation: 
The -2 and -1 are moving left, while the 1 and 2 are moving right.
Asteroids moving the same direction never meet, so no asteroids will meet each other.
```


## Solution
### Method 1
读完题其实就能感觉到需要用stack。总的来说和之前写过的mono stack有些类似，都是讲下一个数字与stack顶部数字对比来进行操作。
本题区别在于有多种情况要处理：

1. 新的数字 > 0 或者 stack为空：直接放入
1. 新的数字 < 0:
 1. 与栈顶的和 < 0: 栈顶小星星消失，继续看下一个
 1. =0：两个都消失，结束
 1. >0: 新数字消失，结束

python中while也可以加else，在通过 while 条件结束时会经过，如果是break出来的就不会跑else里面的代码
```python
class Solution:
    def asteroidCollision(self, asteroids: List[int]) -> List[int]:
        stack = []
        for asteroid in asteroids:
            while stack and stack[-1] > 0 > asteroid:
                    r = stack[-1] + asteroid
                    if r >= 0:
                        if r == 0:
                            stack.pop()
                        break
                    else:
                        stack.pop()
            else:
                stack.append(asteroid)
        return stack
```