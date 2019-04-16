---
title: "Longest Increasing Subsequence (Chinese)"
date: 2019-04-15T20:03:06-07:00
description: ""
dropCap: false
displayInMenu: false
displayInList: true
draft: false
---

## Question
This question is from [Leetcode 300](https://leetcode.com/problems/longest-increasing-subsequence/)

Given an unsorted array of integers, find the length of longest increasing subsequence.

### Examples
```
Input: [10,9,2,5,3,7,101,18]
Output: 4 
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4. 
```
### Note
1. There may be more than one LIS combination, it is only necessary for you to return the length.
1. Your algorithm should run in {{< raw >}}\( O(N^2) \){{< /raw >}} complexity.

### Method 1
#### Analysis
可以使用Brute force的方法来解决这个问题。
具体来说我们需要试一下所有可能的组合：对于每一个数**x**，他本身加上他后面的任意一个比他大的数**y**都可以组成一个increasing subsequence。如果我们已经知道了以**y**开头（对，就是recursion的思想），最长能组成的subsequence的长度，我们就知道了以**x**和**y**开头的最长的长度。但是选y只是以x开头的increasing subsequence的可能性之一。或许有一个数字**z**也符合条件（比x大，在x的后面），如果以z开头的increasing subsequence 的最长长度比以y开头的长，那么对于x来说，更好地选项就是接上z，以xz开头。 所以，为了知道最长的到底是那个，需要把后面所有符合条件的数都拿来试一下，然后选那个最长的。
时间复杂度是{{< raw >}}\( O(N!) \){{< /raw >}}：对于第一个数字，需要进行 (n-1) * (n-2) * ... * 1，第二个是 (n-2) * (n-3) * ... * 1, 第n个是1。

Leetcode也提供了[另一种BF](https://leetcode.com/problems/longest-increasing-subsequence/solution/)的方法。那个方法对于recursion function的定义不同，时间复杂度也只有O(2^n)。原因是相比于上面的方法，在每个点只进行了两次recursion call，而不是n次。

你应该能发现我们recursion function的定义，是求以当前数字作为开头，最长的increasing subsequence的长度。既然是最长，那么也就是说只要给定start位置和list，这个结果就只有一个。然而在上面的算法中，对于同一个点我们计算了很多次（对于每个数，我们计算的次数等于他前面比他小的数字的数量）。所以我们可以把计算的结果存起来，只计算一次。

那么时间复杂度就变成了{{< raw >}}\( O(N^2) \){{< /raw >}}，因为对于每个点，我们依然要看一下剩下符合要求的点的最长长度然后计算出自己的长度。

#### Code
```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        if not nums:
            return 0
        self.memo = {}
        return max([self.helper(i, nums) for i in range(len(nums))])
    
    def helper(self, start, nums):
        if start not in self.memo:
            result = 0
            for i in range(start+1, len(nums)):
                if nums[i] > nums[start]:
                    result = max(result, self.helper(i, nums))
            self.memo[start] = result+1
            
        return self.memo[start]

```

### Method 2
#### Analysis
一般来说recursion的问题都可以优化成DP的解法，此题就是如此。我们上面说**这里recursion function的定义，是求以当前数字作为开头，最长的increasing subsequence的长度**。在recursion中， 我们是从前往后call recursion function，但是实际计算是从后往前。换句话说，我们每一层recursion都需要后面一层的结果，所以第一个真正计算出的结果是最后一层，也就是**以最后一个数字作为开头的最长的长度**（那便是1啦）。如果按照真正计算的顺序（即从右向左）计算，然后记录下来计算结果后给后面的计算使用，就是DP的解法。

在LC中提供的DP解法是反过来，从左向右计算，随之而变化的是存的每个点的数据的定义，也变为**以当前数字为结尾，最长increasing subsequence的长度**。所以对于每一个数字，我们都需要看一下符合条件的数，也即在他前面且比他小的数（可以看到是正好反过来的），找出最长的作为当前数字的结果。从前往后是自然而然的顺序，但是如果是用BF的方法推导过来，却是需要转换一下才可。


#### Code
```python
def lengthOfLIS(nums):
    if not nums:
        return 0

    dp = [1] * len(nums)

    for hi in range(len(nums)):
        for lo in range(hi):
            if nums[lo] < nums[hi]:
                dp[hi] = max(dp[hi], dp[lo] + 1)
    return max(dp)
```


### Follow up 1
#### Question
From [Leetcode 673](https://leetcode.com/problems/number-of-longest-increasing-subsequence/): 
Given an unsorted array of integers, find the number of longest increasing subsequence.

#### Examples
```
Example 1:
Input: [1,3,5,4,7]
Output: 2
Explanation: The two longest increasing subsequence are [1, 3, 4, 7] and [1, 3, 5, 7].
Example 2:
Input: [2,2,2,2,2]
Output: 5
Explanation: The length of longest continuous increasing subsequence is 1, and there are 5 subsequences' length is 1, so output 5.
```
#### Analysis

这个题目和上面题目唯一不同的地方就是之前的是求最长的长度，这个是求拥有最长长度的subsequence有多少个。如果你walk through了之前的DP解法就会发现，同样长度的结果的确可能遇到过多个。比如我们在例子1中最后一个数字7的时候，在index为2和3（就是数字5和4）时，DP[2]和DP[3]都应该等于3，因为135和134长度都为3。那么在上面DP解法第十行的`dp[j] = max(dp[j], dp[i] + 1)`就是说明dp[j]等于dp[i]+1。 

在本题目中我们需要记录下来遇到这种情况的数量，也就是在每一位不仅记录以当前为结尾数字的subsequence的最大长度，也记录有此长度的不同subsequence有多少个。这样一来，最后的时候我们就可以使用整个dp数组中最长的那个长度，作为整个数组的最长度，再用这个长度去找总共这个长度的subsequence有多少个。

#### Code
```python
def numberOfLIS(nums):
        if not nums:
            return 0
        
        dp = [1] * len(nums)
        counts = [1] * len(nums)
        
        for hi in range(len(nums)):
            for lo in range(hi):
                if nums[lo] < nums[hi]:
                    if dp[hi] < dp[lo] + 1:
                        dp[hi] = dp[lo] + 1
                        counts[hi] = counts[lo]
                    elif dp[hi] == dp[lo] + 1:
                        counts[hi] += counts[lo]

        max_len  = max(dp)
        return sum([counts[i] for i in range(len(nums)) if dp[i] == max_len])
```

### Follow up 2
#### Question
From [Leetcode 1027](https://leetcode.com/problems/longest-arithmetic-sequence/):
Given an array A of integers, return the length of the longest arithmetic subsequence in A.

Recall that a subsequence of A is a list `A[i_1], A[i_2], ..., A[i_k]` with `0 <= i_1 < i_2 < ... < i_k <= A.length - 1`, and that a sequence B is arithmetic if `B[i+1] - B[i]` are all the same value (for `0 <= i < B.length - 1`).

#### Examples
```
Example 1
Input: [9,4,7,2,10]
Output: 3
Explanation: 
The longest arithmetic subsequence is [4,7,10].

Example 2:
Input: [20,1,15,3,10,5,8]
Output: 4
Explanation: 
The longest arithmetic subsequence is [20,15,10,5].
```

#### Analysis

看过了上面这么多题目和解法，看到这应该有感觉到需要用类似的方法做。和原题唯一的区别就是条件：原题的条件是右边的数大于左边的数，此题的条件是右边的数减左边的数等于一个差值。这个差值是由subsequence的头两个数决定的。同样的，我们可以用一个数组`dp[]`来存以当前数字为结尾的subsequence的最长长度，但不同的是对于不一样的差值可以会有相同或者不同的最长长度，比如例子1中，以7结尾，差值为-2时，长度为2，差值为3时长度也为2（未出现的差值我们可以不记录）。在比如当到了10的时候，差值为3是就是3（接上了7），其他的（比如1，6等等）最长的只有2了。所以我们可以想到，对于每一个数字的每一种可能的差值，我们都需要记录下来其最长subsequence的长度，以备后面的数字使用。

```python
def longestArithSeqLength(nums):
    dp = [collections.defaultdict(int) for _ in range(len(nums))]
    result = 0
    
    for hi in range(len(nums)):
        for lo in range(hi):
            diff = nums[hi] - nums[lo]
            dp[hi][diff] = max(dp[hi][diff], dp[lo][diff] + 1)
            result = max(result, dp[hi][diff])        
    return result + 1	

```

### 后记
这篇文章其实是收到了最后一个题目（follow up2）的启发，是在做LC contest的时候遇到的。回忆起来，当时感觉题目有点熟悉，但没想到和原题的关联。当时只想到了BF算法，自然也超时了。理论上用BF加memo的方法也可以做，但是因为加入了diff的缘故导致稍微有点复杂，就被绕晕了。想要思路清晰的快速解题，还是要熟练啊