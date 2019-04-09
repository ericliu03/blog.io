---
title: "Video Stitching"
date: 2019-04-08T20:01:29-07:00
description: ""
dropCap: false
displayInMenu: false
displayInList: true
draft: false
tags: ["array", "loop"]

---

This is a question from [leetcode.com](https://leetcode.com/problems/video-stitching/).

## Problem
### Question
You are given a series of video clips from a sporting event that lasted T seconds.  These video clips can be overlapping with each other and have varied lengths.

Each video clip `clips[i]` is an interval: it starts at time `clips[i][0]` and ends at time `clips[i][1]`.  We can cut these clips into segments freely: for example, a clip `[0, 7]` can be cut into segments `[0, 1] + [1, 3] + [3, 7]`.

Return the minimum number of clips needed so that we can cut the clips into segments that cover the entire sporting event (`[0, T]`).  If the task is impossible, return `-1`.

### Examples
Example 1:
```
Input: clips = [[0,2],[4,6],[8,10],[1,9],[1,5],[5,9]], T = 10
Output: 3
Explanation: 
We take the clips [0,2], [8,10], [1,9]; a total of 3 clips.
Then, we can reconstruct the sporting event as follows:
We cut [1,9] into segments [1,2] + [2,8] + [8,9].
Now we have segments [0,2] + [2,8] + [8,10] which cover the sporting event [0, 10].
```
Example 2:
```
Input: clips = [[0,1],[1,2]], T = 5
Output: -1
Explanation: 
We can't cover [0,5] with only [0,1] and [0,2].
```
Example 3:
```
Input: clips = [[0,1],[6,8],[0,2],[5,6],[0,4],[0,3],[6,7],[1,3],[4,7],[1,4],[2,5],[2,6],[3,4],[4,5],[5,7],[6,9]], T = 9
Output: 3
Explanation: 
We can take clips [0,4], [4,7], and [6,9].
```
Example 4:
```
Input: clips = [[0,4],[2,8]], T = 5
Output: 2
Explanation: 
Notice you can have extra video after the event ends.
```

Note:
```
1 <= clips.length <= 100
0 <= clips[i][0], clips[i][1] <= 100
0 <= T <= 100
```


## Analysis

If we take the core of the problem out, what we need to find is minimum number of ranges that covers [0, T]. 

We can start with 0, since 0 is the starting point (left-most position) and we have to cover it. By checking all the sections that starts <= 0, we will know the how long we can reach to the right. For example, in example 3, we have `[0,1], [0,2], [0,3], [0,4]`. Then in next step, we could reach any sections that start <= 4. So we will loop through all the sections again and find how long we can reach to the right. If we have `[2,9]` and `[4,8]`, we will know we can reach 9.

So in each scan, we will know how far we can reach and once we reach further than T or we can't move, we finished scan and return the result.

## Algorithm
### Method 1:
```python
def video_stitching(clips, T):
    curr_reached = 0
    count = 0

    while curr_reached < T:
        next_reached = curr_reached
        for start, end in clips:
            if start <= curr_reached and end > next_reached:
                next_reached = end
        count += 1
        if next_reached == curr_reached:
            count = -1
            break
        else:
            curr_reached = next_reached

    return count
```

Time complexity: in the worst case, we move one section by one section and need to loop through every section for each section, so {{< raw >}}\( O(N^2) \){{< /raw >}}

### Method 2:
First we sort this clips based on start time (end time doesn't matter because we will scan all of them). Then during the loop, we will keep record of how far we can go in next round and stop at the section whose start is greater than our current reached point (so that we can concatenate). So in next round, we start at where we stopped and do the same thing.

```python
def video_stitching2(clips, T):
    clips.sort()
    curr_reached = 0
    count = 0
    pos = 0

    while pos < len(clips) and curr_reached < T:
        next_reached = curr_reached
        while pos < len(clips) and clips[pos][0] <= curr_reached:
            next_reached = max(next_reached, clips[pos][1])
            pos += 1
        count += 1
        if next_reached == curr_reached:
            break
        else:
            curr_reached = next_reached

    return count if curr_reached >= T else -1
 ```

 Time complexity: the scan is {{< raw >}}\( O(N) \){{< /raw >}} but sorting is {{< raw >}}\( O(NlogN) \){{< /raw >}}. So overall the time complexity is {{< raw >}}\( O(NlogN) \){{< /raw >}}