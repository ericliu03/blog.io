---
title: "Frog Jump"
date: 2019-05-01T16:44:51-07:00
description: ""
dropCap: false
displayInMenu: false
displayInList: true
draft: false
---

## Question

[Leetcode 403: Frog Jump](https://leetcode.com/problems/frog-jump/)

A frog is crossing a river. The river is divided into x units and at each unit there may or may not exist a stone. The frog can jump on a stone, but it must not jump into the water.

Given a list of stones' positions (in units) in sorted ascending order, determine if the frog is able to cross the river by landing on the last stone. Initially, the frog is on the first stone and assume the first jump must be 1 unit.

If the frog's last jump was k units, then its next jump must be either k - 1, k, or k + 1 units. Note that the frog can only jump in the forward direction.

### Note:
1. The number of stones is ≥ 2 and is < 1,100.
1. Each stone's position will be a non-negative integer < 231.
1. The first stone's position is always 0.


### Examples

Example 1:
```
[0,1,3,5,6,8,12,17]

There are a total of 8 stones.
The first stone at the 0th unit, second stone at the 1st unit,
third stone at the 3rd unit, and so on...
The last stone at the 17th unit.

Return true. The frog can jump to the last stone by jumping 
1 unit to the 2nd stone, then 2 units to the 3rd stone, then 
2 units to the 4th stone, then 3 units to the 6th stone, 
4 units to the 7th stone, and 5 units to the 8th stone.
```
Example 2:
```
[0,1,2,3,4,8,9,11]

Return false. There is no way to jump to the last stone as 
the gap between the 5th and 6th stone is too large.
```

## Solution
### Method 1

At first glance this question reminds me [longest increasing subsequence](https://github.com/ericliu03/blog.io/blob/master/content/posts/2019/04/longest_increasing_subsequence.md). We can translate the stones into the vertices in the graph and then what we need to find out are the edges and whether there's a path between start and end vertex. 

At each vertex, to calculate the edges to next vertices, we will use the rule given by the question: *the difference between previous vertex and current vertex (plus -1, 0, 1)*. 

Since we start from the start stone, so if there's any vertex that's connected to the end vertex then it means we find a path.

#### Code
```python
def canCross(stones) -> bool:
    # jump sizes are the distance between previous stone to current stone
    jump_sizes = {}
    for stone in stones:
        jump_sizes[stone] = set()
        
    jump_sizes[0].add(0)
    for stone in stones:
        for jump_size in jump_sizes[stone]:
            for delta in (-1, 0, 1):
                next_jump = jump_size + delta
                # we use the distance between prev and curr stone to calculate next stone
                # and add this new jump size to it's jump size set if next stone exists
                if stone + next_jump in jump_sizes and next_jump > 0:
                    jump_sizes[stone + next_jump].add(next_jump)

    return len(jump_sizes[stones[-1]]) != 0
```

### Thoughts
1. the information stored could be in different formats, such as 
 1. previous stone's location
 1. jump distances used to jump to this stone (the one i chose)
 1. jump distances could be used in this stone to next stones












