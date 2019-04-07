+++
title =  "Create tree from tuples"
date = 2019-04-06T16:55:31-07:00
tags = ["graph", "topological sort", "tree", "dfs"]
featured_image = ""
description = ""
+++


This is a coding problem from Uber onsite interview found [here](https://www.1point3acres.com/bbs/thread-502555-1-1.html).

### Question:

Given a list of pairs (tuples), each of them represents as a edge that connects two nodes in a tree. However, a node will have edge for not only direct children but also children's children. 

If we have the following tree as an example:
```
    A
   / \
  B   C
 / \
 D  E
```
 Then the list given it will be
```
 [(A,A), (A,B), (A,D), (A,E), (A,C), (B,D), (B,E)]
```

You can assume the edges given will result in a valid tree, but it's possible that a node only have edges that connects to partial of it's not-direct children. For example, if we remove (A,D) from above list, we should still have a valid tree. 

Use this list to construct a tree.

### Analysis

Intuitively, what we want to do is to remove the edges that, from a tree's perspective, not connected from a node's parent. However, we can also imagine what we have in the given list are the edges for a directed graph, and then we want to discard edges if the graph contains a longer path connecting the same two vertices.

What we have from the list is a **partially ordered set**  (need to confirm if this is correct) of [**DAG** (Directed Acyclic Graph)](https://en.wikipedia.org/wiki/Directed_acyclic_graph), and what we generated is called the [**transitive reduction**](https://en.wikipedia.org/wiki/Transitive_reduction) of the graph. You can also find it [here](https://algs4.cs.princeton.edu/42digraph/) at item 38 in *Web Exercises* section.

The transitive reduction contains *the fewest possible edges that has the same reachability relation as the original graph by discarding the edges u → v for which G also contains a longer path connecting the same two vertices.* 

This question starts from the tree's perspective, but actually it's asking for an graph algorithm that generate the transitive reduction with a graph that only have 1 zero-degree node. 

### Coding

#### Method 0: DFS

Following the intuitive algorithm, we could loop through every node in the graph and remove a edge to a child if there are other paths (and will be longer) that connect to that child.

```python
import collections

def generate_tree0(edges):

    children = collections.defaultdict(set)
    nodes = set()
    for u, v in edges:
        children[u].add(v)
        nodes.add(u)
        nodes.add(v)

    def dfs(start, target):
        if start == target:
            return True
        result = False
        for next_node in children[start]:
            # in case we have edge that points to a node itself
            if next_node != start:
                result = result or dfs(next_node, target)
        return result

    for node in nodes:
        for child in list(children[node]):
            found = False
            for other_child in list(children[node]):
                if other_child != child and dfs(other_child, child):
                    found = True
                    break
            if found:
                children[node].remove(child)

    return children
 ```

 Complexity:
 If we have N nodes, for each node we have to go to every other nodes in worst case so time complexity should be {{< raw >}}\( O(N^2) \){{< /raw >}}. 

#### Method 1: Topological Sort

Since it's a direct graph and we want to remove the edges that connects u->v if there are other longer paths, we can use topological sort to go through each node in this graph. In each 0 in degree node u, for every child v, if there's any other in degree edge to v, then it means there will be longer path(s) that connects to v, then the edge u->v should be discarded. 


```python
import collections

def generate_tree0(edges):
    indegrees = collections.defaultdict(int)
    outdegrees = collections.defaultdict(set)

    for u, v in edges:
        indegrees[v] += 1
        if u not in indegrees:
            indegrees[u] = 0
        outdegrees[u].add(v)

    queue = [u for u, count in indegrees.items() if indegrees[u] == 0]
    while queue:
        curr_node = queue.pop(0)
        for neighbor in list(outdegrees[curr_node]):
            if indegrees[neighbor] == 1:
                queue.append(neighbor)
            else:
                indegrees[neighbor] -= 1
                outdegrees[curr_node]remove(neighbor)
    return outdegrees
```

 Complexity:
 If we have N nodes, for each node we have to go to every other nodes in worst case so time complexity should be {{< raw >}}\( O(N) \){{< /raw >}}. . 

#### other thoughts: bottom up

Goal is still the same, just this time we check our parent's children. We first pick the nodes with zero-outdegree (i.e leaf nodes) and check one by one. If the a node **v** is the only child of it's parent **u**, then we should keep that edge **u->v** and remove all other edges that connected to *v*. This could be seen as a variation of topological sort.

However, the problem is that we don't know which parent should we keep. For example, we have a leaf node v and it have parents u1 and u2, then which one should we keep? from the desription above, if we keep the parent that has only one child, then what about the situations that v has a sibling v2? i.e u2 -> v and u2 -> v2, then we should keep u2 as the parent. 














