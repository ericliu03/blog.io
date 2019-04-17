---
title: "Maximum Difference Between Node and Ancestor"
date: 2019-04-16T21:16:39-07:00
description: ""
dropCap: false
displayInMenu: false
displayInList: true
draft: false
---

## Question
[Leetcode 1026](https://leetcode.com/problems/maximum-difference-between-node-and-ancestor/):
Given the root of a binary tree, find the maximum value `V` for which there exists **different** nodes A and B where `V = |A.val - B.val|` and A is an ancestor of B.

*(A node A is an ancestor of B if either: any child of A is equal to B, or any child of A is an ancestor of B.)*

```python
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None
```
### Examples


<img class="special-img-class" style="width:50%" src="/img/maximum_diff_between_node_and_ancestor_img1.jpg" />


```
Input: [8,3,10,1,6,null,14,null,null,4,7,13]
Output: 7
Explanation: 
We have various ancestor-node differences, some of which are given below :
|8 - 3| = 5
|3 - 7| = 4
|8 - 1| = 7
|10 - 13| = 3
Among all possible differences, the maximum value of 7 is obtained by |8 - 1| = 7.
```

## Solution
### Method 1
#### Analysis
Bottom up solution: we collect results from a node's left and right child and calculate result of this node

1. each node will need three things from both left and right child
 11. min and max num in subtree (for calculating this node's difference with it's max and min decendents)
 11. max diff in subtree
1. combine the results given from children
1. calculate result with current node's value and return

#### Code
```python
def maxAncestorDiff(root):
    if root is None:
        return 0
    else:
        return helper(root)[2]


def helper(node):
    min_num = max_num = node.val
    diff = 0
    
    if node.left:
        temp = helper(node.left)
        min_num = min(min_num, temp[0])
        max_num = max(max_num, temp[1])
        diff = max(diff, temp[2])
    if node.right:
        temp = helper(node.right)
        min_num = min(min_num, temp[0])
        max_num = max(max_num, temp[1])
        diff = max(diff, temp[2])
    
    diff = max(diff, max(abs(node.val - min_num), abs(node.val - max_num)))
    
    return min_num, max_num, diff
```

### Method 2
#### Analysis
Top down solution: the question is about nodes and their ancestors so we don't need to gather and combine results from both children to calculate result

1. function is given min and max of numbers in the path from root to current node
1. update min or max with current node's value and pass it to it's children
1. when we reached the leaf, we calculate the result by using this min and max nums

```python
def maxAncestorDiff(root):
    return helper(root, root.val, root.val)


def helper(node, lo, hi):
    if node is None:
        return hi - lo

    lo = min(lo, node.val)
    hi = max(hi, node.val)

    return max(helper(node.left, lo, hi), helper(node.right, lo, hi))
```