---
title: "Pour Water"
date: 2019-04-07T14:51:09-07:00
description: ""
dropCap: true
displayInMenu: true
displayInList: true
draft: false
tags: []
---

This is a question from Airbnb onsite interview, also could be found on [leetcode](https://leetcode.com/problems/pour-water/).

## Question:

We are given an elevation map, `heights[i]` representing the height of the terrain at that index. The width at each index is 1. After V units of water fall at index K, how much water is at each index?

### Assumptions:
Now, you should ask some clarify questions and make some assumptions of this questions. There are a lot of good solutions and explanations on [leetcode](https://leetcode.com/problems/pour-water/solution/) so I will have a different version of the assumption here.

1. If the droplet would eventually fall by moving **right**, then move **right**.
1. Otherwise, if the droplet would eventually fall by moving **left**, then move **left**.
1. Water will move as many as possible.
1. However, if the eventually fall position is the first or last position of the map, the water drop will overflow and disappear.

Here, **"eventually fall"** means that the droplet will eventually be at a lower level if it moves in that direction. Also, "level" means the height of the terrain plus any water in that column.

If there's no place for water to eventually fall without overflow, then 

1. The water drop will overflow either overflow from left or right.
1. Otherwise, it will stay at the right most possible position.

### Examples:
```
Input: heights = [2,1,1,2,1,2,2], V = 4, K = 3
Output: [2,2,2,3,2,2,2]
Steps:
1. [2,1,1,2,2,2,2]
2. [2,2,1,2,2,2,2]
3. [2,2,2,2,2,2,2]
4. [2,2,2,2,2,2,2] (overflow)

Input: heights = [1,2,3,4], V = 1, K = 2
Output: [1,2,3,4]
Steps:
1. [1,2,3,4] (overflow)

Input: heights = [2,1,1,4], V = 3, K = 1
Output: [2,2,2,4]
Steps:
1. [2,1,2,4]
1. [2,2,2,4]
1. [2,2,2,4] (overflow)
```

## Analysis
With these assumptions, this question is a bit complicated than the one in leetcode. We need to explicitly deal with some conditions. For example, instead of overflow, we have high walls on both sides, we don't need to care about whether we will overflow or not.

### Conditions
1. On right side find the 1. right most position, 2. lowest level, 3.water can flow there (water can't flow up) 
1. Do the same on the left side.
1. Check if we overflow: checking the level of the position we find on both left and right sides. One in overflow position and the other is either in overflow position or the level of that position is not lower than dropping position(V), then we will overflow.
1. Otherwise, find the correct position, it should be the one on right side, unless the right position is not a **'eventually fall'** position while the one on the left is.

## Coding

```python
def pour_water(elevation, drop_count, drop_pos):
    n = len(elevation)
    for _ in range(drop_count):
        right_pos = drop_pos
        left_pos = drop_pos

        while right_pos + 1 < n and elevation[right_pos + 1] <= elevation[right_pos]:
            right_pos += 1
        while left_pos - 1 >= 0 and elevation[left_pos - 1] <= elevation[left_pos]:
            left_pos -= 1

        next_pos = right_pos
        if (right_pos == n - 1 and left_pos == 0) \
                or (right_pos == n - 1 and elevation[left_pos] == elevation[drop_pos]) \
                or (left_pos == 0 and elevation[right_pos] == elevation[drop_pos]):
            continue
        elif elevation[right_pos] == elevation[drop_pos] and elevation[left_pos] < elevation[drop_pos]:
            next_pos = left_pos

        elevation[next_pos] += 1
    return elevation
```

### Tips

When scanning using while loop, we can check the value on next index before moving forward. In this way, our result index will be the last one that satisfy the while condition. Be attention that the first index is not checked.






























