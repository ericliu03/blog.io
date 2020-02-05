---
title: "[Leetcode 134] Gas Station"
description: ""
date: "2020-01-29T12:52:36-08:00"
thumbnail: ""
categories:
  - ""
tags:
  - "two_pointer"
---



## Question

[134. Gas Station](https://leetcode.com/problems/gas-station/)

There are N gas stations along a circular route, where the amount of gas at station i is gas[i].

You have a car with an unlimited gas tank and it costs cost[i] of gas to travel from station i to its next station (i+1). You begin the journey with an empty tank at one of the gas stations.

Return the starting gas station's index if you can travel around the circuit once in the clockwise direction, otherwise return -1.




### Note:

1. If there exists a solution, it is guaranteed to be unique.
1. Both input arrays are non-empty and have the same length.
1. Each element in the input arrays is a non-negative integer.


### Examples:

```
Example 1:

Input: 
gas  = [1,2,3,4,5]
cost = [3,4,5,1,2]

Output: 3

Explanation:
Start at station 3 (index 3) and fill up with 4 unit of gas. Your tank = 0 + 4 = 4
Travel to station 4. Your tank = 4 - 1 + 5 = 8
Travel to station 0. Your tank = 8 - 2 + 1 = 7
Travel to station 1. Your tank = 7 - 3 + 2 = 6
Travel to station 2. Your tank = 6 - 4 + 3 = 5
Travel to station 3. The cost is 5. Your gas is just enough to travel back to station 3.
Therefore, return 3 as the starting index.
```


## Solution


### Method 1
题目的关键点在于，能绕一圈的前提是：每一步的油都足够走到下一步。从i开始，当我们发现j->j+1的油不够了的时候，我们就将i减一，把起点提前，如果存在一个起点走完全程，那么一定能找到一个起点使我们能够走过j->j+1。这是一种two pointer的解法。

```python
class Solution:
    def canCompleteCircuit(self, gas: List[int], cost: List[int]) -> int:
        gas_left = [gas[i] - cost[i] for i in range(len(gas))]
        length = len(gas)
        gas_left = gas_left[:] + gas_left[:]
        
        curr_sum = 0

        left = length
        right = left
        while right - left < length and right < len(gas_left) and left >= 0:
            if curr_sum >= 0:
                curr_sum += gas_left[right]
                right += 1
            else:
                left -= 1
                curr_sum += gas_left[left]

        return -1 if curr_sum < 0 else (left%length)
```
### Method 2
观察的再深入一些，就可以利用更多题目的特性：

1. 如果以i为起始点，走到j之后，发现从j->j+1的油不够了，那么从[i, j]之间的任意一个点都不可能是起始点。（因为从i到j之间的油一定都是>=0的）。
1. 如果所有的油和cost的加和>0，那么一定存在一个起始点可能绕场一周。我们把gas[i]-cost[i]>=0的称作绿点，<0的为红点，那么先把相邻的绿和红点合并就变成了红绿相间。如果总和大于0，那么，
    1. 只有1点：绿点
    1. 2个点：红绿，但是加和>=0
    1. 3个点：两个红或者绿可以合并，变成2点
    1. 4个点，同3

所以算法就是从0开始，往后走，一旦遇到了j->j+1的油不够的情况，就说明j+1之前的点都一定不是起点。我们就以j+1作为起点重新开始。如果走到了头，就说明j+1这个点是第一个有可能符合条件的点。原因：1. 如上所述前面的都不是，2. 假设在j+1之后的一个k点处是可以走完全程的点，那么既然j+1开始可以走到k，那么j+1也必定是可以走完全程的点。

所以如果一定存在一个点可以走完全程，那么j+1一定是第一个。


### Thoughts
1. 要看出题目的特性，可以利用的点在哪里。

## Reference
1. [Share some of my ideas.](https://leetcode.com/problems/gas-station/discuss/42568/Share-some-of-my-ideas.)
1. [My AC is O(1) space O(n) running time solution](https://leetcode.com/problems/gas-station/discuss/42565/My-AC-is-O(1)-space-O(n)-running-time-solution.-Does-anybody-have-posted-this-solution)
