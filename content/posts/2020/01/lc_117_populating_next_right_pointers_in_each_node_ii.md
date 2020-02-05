---
title: "[Leetcode 117] Populating Next Right Pointers in Each Node II"
description: ""
date: "2020-01-31T12:55:12-08:00"
thumbnail: ""
categories:
  - ""
tags:
  - "dfs"
---




## Question

[117. Populating Next Right Pointers in Each Node II](https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/)

Given a binary tree

```
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```
Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.

Initially, all next pointers are set to NULL.

### Follow up:

1. You may only use constant extra space.
1. Recursive approach is fine, you may assume implicit stack space does not count as extra space for this problem.
 


### Notes:

1. The number of nodes in the given tree is less than 6000.
1. -100 <= node.val <= 100


### Examples:

```
Input: root = [1,2,3,4,5,null,7]
Output: [1,#,2,3,#,4,5,7,#]
Explanation: Given the above binary tree (Figure A), your function should populate each next pointer to point to its next right node, just like in Figure B. The serialized output is in level order as connected by the next pointers, with '#' signifying the end of each level.
```


## Solution


### Method 1
最简单的想法就是我们把每一层存成一个list。方法是对tree进行遍历（pre-order或者in-order才能从左往右的顺序），记录当前层数，存到dict的list里。key是level。空间需要O(n)

```python
"""
# Definition for a Node.
class Node:
    def __init__(self, val: int = 0, left: 'Node' = None, right: 'Node' = None, next: 'Node' = None):
        self.val = val
        self.left = left
        self.right = right
        self.next = next
"""
class Solution:
    def connect(self, root: 'Node') -> 'Node':
        d = {}
        
        def pre_order(root, level):
            if not root:
                return
            if level in d:
                d[level].next = root
            d[level] = root
            pre_order(root.left, level + 1)
            pre_order(root.right, level + 1)
        
        pre_order(root, 0)
        return root
```
### Method 2
深入思考一下可以想到，我们知道了root，也就知道了root的左右子树，那么把他们连起来的同时，我们1可以利用这个连起来的指针来横向在同一level遍历，2知道他们的分别的左右子树，就可以连接下一层。利用这个原理，我们就可以以O(1)的空间完成连接

```python
class Solution:
    def connect(self, root: 'Node') -> 'Node':
        next_head = root

        def 
        while next_head:
            curr_node = next_head
            next_head = None
            next_node = None
                
            while curr_node:
                node = curr_node.left
                if node:
                    if not next_node:
                        next_node = node
                        next_head = next_node
                    else:
                        next_node.next = node
                        next_node = node
                node = curr_node.right
                if node:
                    if not next_node:
                        next_node = node
                        next_head = next_node
                    else:
                        next_node.next = node
                        next_node = node

                curr_node = curr_node.next
                    
        return root    
```   


### Thoughts
1. 要看出题目的特性，可以利用的点在哪里。

## Reference
1. [Share some of my ideas.](https://leetcode.com/problems/gas-station/discuss/42568/Share-some-of-my-ideas.)
1. [My AC is O(1) space O(n) running time solution](https://leetcode.com/problems/gas-station/discuss/42565/My-AC-is-O(1)-space-O(n)-running-time-solution.-Does-anybody-have-posted-this-solution)
