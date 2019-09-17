---
title: "[Leetcode 138]: Copy List with Random Pointer"
description: ""
date: "2019-09-16T20:09:42-07:00"
thumbnail: ""
categories:
  - ""
tags:
  - "medium"
---


## Question

[Leetcode 138: Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)

A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.

Return a **deep copy** of the list.



### Note:
1. You must return the copy of the given head as a reference to the cloned list.
1. Node definition:

```python
class Node:
    def __init__(self, val, next, random):
        self.val = val
        self.next = next
        self.random = random
```

### Examples:
```
Input:
{"$id":"1","next":{"$id":"2","next":null,"random":{"$ref":"2"},"val":2},"random":{"$ref":"2"},"val":1}

Explanation:
Node 1's value is 1, both of its next and random pointer points to Node 2.
Node 2's value is 2, its next pointer points to null and its random pointer points to itself.
```


## Solution
### Method 1
Treat as a graph. We do dfs/bfs in recursive/iterative ways.
```python
class Solution:
    def copyRandomList(self, head: 'Node') -> 'Node':
        self.visited = {}
        return self.clone(head)

    def clone(self, node):
        if not node:
            return None
        if node.val not in self.visited:
            n = Node(node.val, None, None)
            self.visited[node.val] = n

            n.next = self.clone(node.next)
            n.random = self.clone(node.random)

        return self.visited[node.val]
```
### Method 2
Treat it like a linked list. Use `next` pointer to navigate, and setup `random` along the way.
```python
class Solution:
    def copyRandomList(self, head: 'Node') -> 'Node':
        if not head:
            return None
        
        new_head = Node(head.val, None, None)
        nodes = {new_head.val:new_head}

        old_temp = head
        new_temp = new_head
        def get_node(node):
            if node is None:
                return None
            val = node.val
            if val in nodes:
                return nodes[val]
            else:
                result = Node(val, None, None)
                nodes[val] = result
                return result
            
        while old_temp:
            new_temp.next = get_node(old_temp.next)
            new_temp.random = get_node(old_temp.random)
            
            old_temp = old_temp.next
            new_temp = new_temp.next
        
        return new_head
```   