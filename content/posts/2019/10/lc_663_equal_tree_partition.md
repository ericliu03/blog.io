---
title: "[Leetcode 663] Equal Tree Partition"
description: ""
date: "2019-10-15T20:30:23-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "medium"
---

## Question

[Leetcode 663: Equal Tree Partition](https://leetcode.com/problems/equal-tree-partition/)

Given a binary tree with `n` nodes, your task is to check if it's possible to partition the tree to two trees which have the equal sum of values after removing `exactly one` edge on the original tree.



```
Return true if there exists i, j, k
such that arr[i] < arr[j] < arr[k] given 0 ≤ i < j < k ≤ n-1 else return false.
```


### Note:

1. The range of tree node value is in the range of [-100000, 100000].
1. 1 <= n <= 10000
1. 
```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None
```


### Examples:
```
Example 1:
Input:     
    5
   / \
  10 10
    /  \
   2   3

Output: True
Explanation: 
    5
   / 
  10
      
Sum: 15

   10
  /  \
 2    3

Sum: 15
Example 2:
Input:     
    1
   / \
  2  10
    /  \
   2   20

Output: False
Explanation: You can't split the tree into two trees with equal sum after removing exactly one edge on the tree.
```


## Solution
### Method 1

去掉一个edge分成两个树和相等，等于找到一个子树whose和等于原本树的和的一半。所以我们必须知道整个树的和是多少。

```python


class Solution:
    def checkEqualTree(self, root: TreeNode) -> bool:

        def sum(root):
            if not root:
                return 0
            return root.val + sum(root.left) + sum(root.right)

        def traverse(root):
            if not root:
                return 0
            left = traverse(root.left)
            right = traverse(root.right)

            # in case the condition that root.left not exist (and thus left == 0), and sum == 0
            if (root.left and left == sum_tree) or (root.right and right == sum_tree):
                nonlocal found
                found = True

            return left + right + root.val

        found = False
        sum_tree = sum(root)/2

        traverse(root)

        return found
```
### Method 2
上面的代码有点冗余。既然题目是说存不存在，我们就不需要在意遍历的顺序。只需要在遍历的时候记录下来以当前点为root的子树的和，最后看看是否存在符合的数就好了。

注意要去掉原本树的和。
```python
class Solution:
    def checkEqualTree(self, root):
        def sum(root):
            if not root:
                return 0
            sums.append(root.val + sum(root.left) + sum(root.right))
            return sums[-1]
        sums = []
        sum(root)
        return  sums.pop()/ 2 in sums
```   