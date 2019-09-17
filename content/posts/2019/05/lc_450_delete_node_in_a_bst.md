---
title: "[Leetcode 450] Delete Node in a BST"
description: ""
date: "2019-05-31T15:22:33-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "medium"
---


## Question

[Leetcode 450: Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/)

Given a root node reference of a BST and a key, delete the node with the given key in the BST. Return the root node reference (possibly updated) of the BST.

Basically, the deletion can be divided into two stages:

1. Search for a node to remove.
1. If the node is found, delete the node.

### Note:
Time complexity should be O(height of tree).

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None
```

### Examples
```
root = [5,3,6,2,4,null,7]
key = 3

    5
   / \
  3   6
 / \   \
2   4   7

Given key to delete is 3. So we find the node with value 3 and delete it.

One valid answer is [5,4,6,2,null,null,7], shown in the following BST.

    5
   / \
  4   6
 /     \
2       7

Another valid answer is [5,2,6,null,4,null,7].

    5
   / \
  2   6
   \   \
    4   7
```

## Solution
### Method 1

第一步用dfs来找到相应的node。 第二步删除node，基本上就是使用node的parent来连接上node的child。为了处理node就是树的root这种情况，我们可以自建一个dummyroot。
这里需要分几种情况：

1. node没有child，那么直接删掉就可以了
1. node有一个child，那么需要把这个child连接到parent上，使用原本node的位置
2. node有两个child，把左边child像2一样链接，然后把右child放到做child的右下的位置。（或者是反过来）

#### Code
```python
class Solution:
    def deleteNode(self, root: TreeNode, key: int) -> TreeNode:
        dummy = TreeNode(0)
        dummy.left = root
    
        def find_node(node, parent, is_left):
            if node is None:
                return None, None, False
            elif node.val == key:
                return (node, parent, is_left)
            elif node.val > key:
                return find_node(node.left, node, True)
            else:
                return find_node(node.right, node, False)
        
        node, parent, is_left = find_node(root, dummy, True)
        
        if node is not None:
            child = node.left if node.left else node.right
            if is_left:
                parent.left = child
            else:
                parent.right = child

            if child and node.left:
                while child and child.right:
                    child = child.right
                child.right = node.right
        return dummy.left
```

### Method 2
另一种是纯recursive的方法。基本上就是说在dfs找node同时进行删除。输入是root和寻找的key，所以在dfs中，如果是小于或者大于，就把左或者右子树的root和key进行recursive dfs。return的依然还是左右子树的root。

如果是等于key的话，还是需要分几种情况
1. 当前node没有子树，返回None
1. 如果有子树，就需要找到当前node的predecessor或者successor来替代当前的值，然后recursively，按照我们删除题目中的Key的方法来删除子树里那个predecessor或者successor。


#### Code

```python
class Solution:
    def find_successor(self, root):
        successor = root.right
        while successor.left:
            successor = successor.left
        return successor

    def find_predecessor(self, root):
        predecessor = root.left
        while predecessor.right:
            predecessor = predecessor.right
        return predecessor
        
    def deleteNode(self, root: TreeNode, key: int) -> TreeNode:
        if not root:
            return None
        elif root.val > key:
            root.left = self.deleteNode(root.left, key)
        elif root.val < key:
            root.right = self.deleteNode(root.right, key)
        else:
            if root.right:
                root.val = self.find_successor(root).val
                root.right = self.deleteNode(root.right, root.val)
            elif root.left:
                root.val = self.find_predecessor(root).val
                root.left = self.deleteNode(root.left, root.val)
            else:
                return None
        return root
```

### Reference
1. [Recursive Easy to Understand Java Solution](https://leetcode.com/problems/delete-node-in-a-bst/discuss/93296/Recursive-Easy-to-Understand-Java-Solution)

