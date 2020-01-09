---
title: "Lc_955_delete_columns_to_make_sorted_ii"
description: ""
date: "2020-01-08T21:24:40-08:00"
thumbnail: ""
categories:
  - ""
tags:
  - ""
---


## Question

[955. Delete Columns to Make Sorted II](https://leetcode.com/problems/delete-columns-to-make-sorted-ii/)

We are given an array A of N lowercase letter strings, all of the same length.

Now, we may choose any set of deletion indices, and for each string, we delete all the characters in those indices.

For example, if we have an array `A = ["abcdef","uvwxyz"]` and deletion indices {0, 2, 3}, then the final array after deletions is `["bef","vyz"]`.

Suppose we chose a set of deletion indices D such that after deletions, the final array has its elements in lexicographic order (`A[0] <= A[1] <= A[2] ... <= A[A.length - 1]`).

Return the minimum possible value of D.length.



### Note:

1. 1 <= A.length <= 100
1. 1 <= A[i].length <= 100


### Examples:

```
Example 1:

Input: ["ca","bb","ac"]
Output: 1
Explanation: 
After deleting the first column, A = ["a", "b", "c"].
Now A is in lexicographic order (ie. A[0] <= A[1] <= A[2]).
We require at least 1 deletion since initially A was not in lexicographic order, so the answer is 1.
Example 2:

Input: ["xc","yb","za"]
Output: 0
Explanation: 
A is already in lexicographic order, so we don't need to delete anything.
Note that the rows of A are not necessarily in lexicographic order:
ie. it is NOT necessarily true that (A[0][0] <= A[0][1] <= ...)
Example 3:

Input: ["zyx","wvu","tsr"]
Output: 3
Explanation: 
We have to delete every column.
```


## Solution


### Method 1
最开始的想法就是一位一位的，从上往下一个单词一个单词的，来比。如果是第一列，`A[i][col]>A[i+1][col]`了，这一列是无论如何都要删掉的。但是问题就在于后面的时候，需要根据前面的情况来看是否该删

1. 所以每一列单独看是不行的，因为如果是 azb和bxa，那么删掉第二位之后因为第一位a>b了，所以第二位怎么样都没关系。如果是azb和aza，那么就要比第三位了。
2. 就想到如果前i位已经确定了排好序了，那么只用看后面的排没排好就行了，运用divide and conquer，把第i位都相等的那些word作为一个group来看。但是问题是对后面col的删除会影响一些已经排好的情况，比如，s开头的单词们需要删掉第二位，但是前面的a开头的不需要删，如果也删了第二位那么就不sorted了（比如abb和aca)。也就是说删掉一列对所有word都有影响，不能分开算。

其实这时候离答案已经很近了，不过走了歪路。顺着第一点的想法走，当遇到`A[i][col]<A[i+1][col]`，我可以说A[i]和A[i+1]到col这个位置的时候我就可以确定是，是一定A[i]>A[i+1]，我们称作`is_sorted`。当遇到`A[i][col]==A[i+1][col]`，我不能确定，要看后边（比如aaab和aaac），除非前面已经排好了，也就是is_sorted。当遇到`A[i][col]>A[i+1][col]`，只有前面是is_sorted，否则就要删除。


本题做的时候几种condition没有想清楚，最关键的就是确定在当前col，已经确定is_sorted有哪些，以此来判断增加了col+1这一位后，两个word是否是sorted，再一步一步走。


```python
class Solution:
    def minDeletionSize(self, A: List[str]) -> int:
        res = 0
        is_sorted = [False] * len(A)
        
        for col in range(len(A[0])):
            is_sorted2 = is_sorted[:]
            for i in range(len(A) - 1):
                if A[i][col] > A[i+1][col] and not is_sorted[i]:
                    res += 1
                    break
                
                is_sorted2[i] = is_sorted[i] or A[i][col] < A[i+1][col]
            else:
                is_sorted = is_sorted2

        return res
```

### Method 2
From reference 1，这个解法更加纯粹的把题目的关键点提取了出来。

```python
def minDeletionSize(self, A):
        res, n, m = 0, len(A), len(A[0])
        unsorted = set(range(n - 1))
        for j in range(m):
            if any(A[i][j] > A[i + 1][j] for i in unsorted):
                res += 1
            else:
                unsorted -= {i for i in unsorted if A[i][j] < A[i + 1][j]}
        return res
```


### Thoughts
1. string可以看做array，方便计算复杂度
2. min deletion可以看做max addition，反向思考。本题可以看做新的一列如果有问题我们就不add。或者说一列字母是否add要看一些条件。
3. 关于字符串的 *lexicographic order*永远只有和紧挨的上下两个有关

## Reference
1. [[Java/C++/Python] Greedy Solution, O(MN)
](https://leetcode.com/problems/delete-columns-to-make-sorted-ii/discuss/203182/JavaC%2B%2BPython-Greedy-Solution-O(MN))