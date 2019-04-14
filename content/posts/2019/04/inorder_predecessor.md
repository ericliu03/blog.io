---
title: "Inorder Predecessor"
date: 2019-04-13T21:55:51-07:00
description: ""
dropCap: false
displayInMenu: false
displayInList: true
draft: false
---

## Question
Given a node in a binary tree, return the inorder predecessor of that node. If it doesn't exist, return null. Both input and output should be node objects.

Node is given as an object with pointers to it's left child, right child and parent.

### Examples

#### Example 1
```
     A
    / \
   B   C
  / \
 D   E
    /
   F

In order traversal: DBFEAC
If give A, then return should be E. 
If give C, should return A. 
If give F, should return B. 
If give D, return null.
```


## Algorithm
#### Method 1
#### Analysis

First, there are [several ways](https://en.wikipedia.org/wiki/Tree_traversal) to traverse a tree, and in the problem it's using in-order traversal. Basically, in-order traversal is to "display root node at *in* position", i.e. 1. display left subtree, display root and display right sub tree. Something like this: **[left-subtree-inorder-list] + root + [right-subtree-inorder-list]**

For this question, the easiest way is to find the root of the tree using the pointer to parent in each node, and do a in-order traversal to generate a list. The time and space complexity would be O(n). Can we do better?


#### Method 2
#### Analysis
We have the pointer to parents, so we should be able to go anywhere in the graph. The question is where to go? 

Let's say **X** is the node given:

- Case 1: if X has left child as **A** in example. Then we are sure it's predecessor is in it's left subtree, and it should be the last node of the subtree's inorder list. The last node in the in-order traversal of a tree will be the right most node in the tree. As a result, **we go left, and go right until no right child.** 
- Case 2: if X doesn't have left child, it means in the subtree where X is the root, X is the first node in it's inorder list. We have to find it's predecessor outside of this subtree. So thinking about X's parent **Y**, 
 - Case 2.1: if X is Y's right child as **C** in example, Y will the X's predecessor: X is the first node (in-order) in Y's right subtree.
 - Case 2.2: if X is Y's left child as **F** in example, then Y will not be the predecessor: Y's right subtree plus Y will be after X in the in-order list. **X will still be the first node in the inorder list that generated from the subtree with Y as the root**. Then if we go one level up to Y's parent **Z**, it will be a similar situation: if Y is Z's left child, Z plus all the nodes in Z's right subtree will be after Y, and thus X, in the inorder list. We have to go up, until we meet a case like case 2.1, where **Y\*** is **Z\*'s** right child. In this case, in subtree with Z\* as root, Z\* will be in the middle, and X is the first node in the inorder list of Z\*'s right subtree, which means X's predecessor is Z\*.
 - Case 2.3: As **D** in example, If we can't find a node like Z\* and we don't have a "parent" to go up any more, then it means X is the first node in the inorder list and we should return null.

#### Code
```python
def inorder_predecessor(node):
    if node is None:
        return None

    if node.left is not None:
        curr = node.left
        while curr.right:
            curr = curr.right
        return curr
    else:
        while node.parent:
            if node.parent.right == node:
                return node.parent
            else:
                node = node.parent
        return None

class Node:
    def __init__(self, val, parent):
        self.val = val
        self.parent = parent
        self.left = None
        self.right = None

    def __str__(self):
        return self.val
```