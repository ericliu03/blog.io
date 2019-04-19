---
title: "Monotone Stack"
date: 2019-04-19T12:45:10-07:00
description: ""
dropCap: false
displayInMenu: false
displayInList: true
draft: false
---

今天我们的题目会围绕一种数据结构（单调栈）和相应的解题方法来讲解。总共3道由易到难的题目。

## Question 
[Leetcode 739: Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)

Given a list of daily temperatures T, return a list such that, for each day in the input, tells you how many days you would have to wait until a warmer temperature. If there is no future day for which this is possible, put 0 instead.

### Example
given the list of temperatures `T = [73, 74, 75, 71, 69, 72, 76, 73]`, your output should be `[1, 1, 4, 2, 1, 1, 0, 0]`.

### Note
The length of temperatures will be in the range `[1, 30000]`. Each temperature will be an integer in the range `[30, 100]`.

## Solution
### Method 1
#### Analysis
这题去掉context就是找每个数的下一个比他大的数的位置。{{< raw >}}\( O(N^2) \){{< /raw >}} 的方法就简单说一下：对于每个数，都扫一遍他后面的所有数，找到第一个比他大的。


<img class="special-img-class" style="width:50%" src="/img/monotone_stack_img1.jpeg" />

那更快一点的方法呢？我们现在考虑如上图两种情况， 1中在a之前的点是单调递增的，所以对于每个数来说，下一个比他大的数就是他右边的数。假设b比a大，那么对于点a来说只有到了b点才能知道下一个大的数在哪。所以我们可以先把a存起来，直到遇到了比a大的b，再写到结果里说比a大的是b。对于图2，在b之前是单调递减的，所以直到b之前都不会遇到符合条件的数，所以我们都得存下来，等遇到b了，我们就可以把存下来的数中比b小的放到结果中，比b大的依然要等。

那么用什么数据结构来暂存这些数呢？关键点就是这些暂存的数字需要保持进入时的顺序，先进先出。所以我们用一个stack来存下暂时没有找到更大的数的数，每当来一个新数的时候就和stack中的数字比较一下，如果新数比stack的顶要大，那就说明栈顶的数的对应的数找到了，可以写入结果了。我们就把他pop出来，继续看栈顶，直到栈为空或者栈顶的数比新数大。可以看到这个stack里面的数单调递减的，原因是如果有increase的话，比如`a>b<c`，那么b的答案其实就是c，一开始就不会放到stack里。

这就是monotone stack，[这篇文章](http://www.cnblogs.com/grandyang/p/8887985.html)可以作为参考。

需要注意的一个细节是stack中存的不是数字（温度），而是数字的位置，这样我们在能用位置拿到数字的同时还能知道结果应该存在result数组的哪个位置。
#### Code
```python
def dailyTemperatures(temps):
    stack = []
    result = [0] * len(temps)

    for i, temp in enumerate(temps):
        while stack and temp > temps[stack[-1]]:
            result[stack[-1]] = i - stack[-1]
            stack.pop()
        stack.append(i)

    return result
```

## Question 2
[Leetcode 42: Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.

### Example
<img class="special-img-class" style="width:50%" src="/img/monotone_stack_img2.png" />
The above elevation map is represented by array `T=[0,1,0,2,1,0,1,3,2,1,2,1]`. In this case, 6 units of rain water (blue section) are being trapped. 

```
Input: [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
```

## Solution
### Method 1
#### Analysis
本题有很多解法，这里还是讲monotone stack。与上题相同，stack应当还是monotonically decreasing的，因为只有遇到比栈顶高的bar，才有可能存住水。不同的地方在于当我们遇到比栈顶大的数的时候的所做的事情。

本题中，遇到比栈顶大的数之后我们就可以存水了。但是存水量的计算有点不intuitive，我们需要左右两遍的bar，和**中间的坑**。我们稍微改变一下题目的例子，把`T[6]`变成0。假设现在我们走到了`i = 3`，前面的数都比2小所以都pop出去了，stack是`[2]`。继续往后走直到`index = 6`stack变成`[2,1,0,0]`，下一步`T[7]=3`，比stack的顶要大，可以作为右边的bar。 栈顶元素（0）就要变做坑，然后我们就继续pop，直到遇到了比坑高的（1）作为左边的bar。水量就是坑的长度乘以坑的深度（`min（左bar，右bar）- 坑底部的高度）`。因为`T[3]=2`还是比`T[7]`小，我们再进行一次同样的水量计算。注意刚才左边的bar是没有取出来的，目的就是在这轮可以用作坑。或者从另一个角度理解，我们刚刚才左bar到右bar之间取出来水之后填上了土，所以坑的高度就变成了左bar。

不容易理解的地方就是，两次计算完毕你会发现水量是根据左bar或者右bar的高度横向计算的，在这个例子里从`T[4] -> T[7]` 高度`1-0`计算一次，然后`T[3] -> T[7]`高度`2-1`计算一次。双指针的解法中水量是竖向计算的。

#### Code
```python
def trap(heights):
    stack = []
    result = 0

    for i, h in enumerate(heights):
        while stack and h > heights[stack[-1]]:
            pit_h = heights[stack.pop()]
            # we have to have a left bar to trap water
            if stack:
                result += (min(h, heights[stack[-1]]) - pit_h) * (i - stack[-1] - 1)
        stack.append(i)

    return result
```


## Question 3
[Leetcode 84: Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)

Given n non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.

### Example

<img class="special-img-class" style="width:30%" src="/img/monotone_stack_img3.png" />
*Above is a histogram where width of each bar is 1, given height = [2,1,5,6,2,3].  The largest rectangle is shown in the shaded area, which has area = 10 unit.*

## Solution
### Method 1
#### Analysis
我们依然使用monotone stack的方式来解决这道题。与存水同样的思路，只有新数字小于栈顶时我们进行计算。计算的方式类似但是略有不同。以上图举例，当我们走到数字`2`时，stack中是`[1,5,6]`。`2<6`所以开始计算，计算的方式是`木板的块数 * 栈顶木板的高度`。计算完成后比较结果与目前的已知最大面积，然后pop 6出来，发现`2<5`所以再次计算，直到栈顶数字小于当前数字。之后我们把2push进入之后继续往后走直到结束。存的最大面积就是结果。

这里模板的块数的计算有点tricky。如上图所示，当我们走到最后的时候，stack中是`[1,2,3]`。以高度3为顶的计算就是`(6-5) * 3`，但是当pop出3之后，以2为顶计算时，左边界index不是4，而是2。因为我们知道2左边被pop出去的板子都比2高，所以我们计算面积时应该把那些板子都算进去。那么怎么找pop出了多少呢？如果stack中还有数字的话，那么2左边的那个就是第一个比2小的，如果没有就说明2就是最小的我们应该把0作为左边界。

有几点可能对理解有所帮助：

1. 木板面积可以这样计算的原因是我们进行了类似于*填坑* 的削顶，也就是说当把6拿出来之后，我们看到下一个数字是5，那么我们就知道 1.目前最矮的是5，2. pop出去的都比5高，所以形成的正方形肯定最高就是5，横向长度就是目前为止到这个高度为5的板子的距离。
1. 计算板子面积不需要像存水一样有左右bar和坑，我们需要左右边界。
 1. 在给定的输入最后填上一个0作为空的右边界，以保证最后一块板子也参与了计算。
 1. 计算面积时如果stack为空那么左边界就是0。
1. 每块板子的所有组成的方块其实都被显式或者隐式的计算过。比如上面计算6 * 1 的时候就是6作为方块的唯一一块板子，下一步5 * 2的时候6其实也包含在了板子中。

推荐看一下reference 4，博主配上了很多图片便于理解。但是博客给的图片关于计算面积是有错误的，他的左边界是选的当前栈顶的位置。

#### Code
```python
def largestRectangleArea(heights):
    heights.append(0)
    stack = []
    result = 0

    for i, h in enumerate(heights):
        while stack and heights[stack[-1]] > h:
            bar_i = stack.pop()
            l_i = stack[-1] + 1 if stack else 0
            result = max(result, (i - l_i) * heights[bar_i])
        stack.append(i)

    return result
```


## Reference
1. 其他可以使用Monotone stack解的题目
 1. [Leetcode 496](https://leetcode.com/problems/next-greater-element-i/)
 1. [Leetcode 1019](https://leetcode.com/problems/next-greater-node-in-linked-list/)
1. [LeetCode Monotone Stack Summary 单调栈小结](http://www.cnblogs.com/grandyang/p/8887985.html)
1. [解法：Trapping Rain Water 收集雨水](http://www.cnblogs.com/grandyang/p/4402392.html)
1. [LeetCode 笔记系列 17 Largest Rectangle in Histogram](http://www.cnblogs.com/lichen782/p/leetcode_Largest_Rectangle_in_Histogram.html)
1. [水中的鱼：Largest Rectangle in Histogram 解题报告](http://fisherlei.blogspot.com/2012/12/leetcode-largest-rectangle-in-histogram.html)