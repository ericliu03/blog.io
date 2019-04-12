+++
title =  "Create tree from tuples"
date = 2019-04-06T16:55:31-07:00
tags = ["graph", "topological sort", "tree", "dfs"]
featured_image = ""
description = ""
+++


This is a coding problem from Uber onsite interview found [here](https://www.1point3acres.com/bbs/thread-502555-1-1.html).

### Question:

Given a list of pairs (tuples), each of them represents a connection from parent to it's child in a tree. In this list, nodes will have edges not only for their direct children, but also all of it's descendants (i.e. children's children, and their children etc). 

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
 [(A,B), (A,D), (A,E), (A,C), (B,D), (B,E)]
```

You can assume the tuples given will result in a valid tree. Use this list to construct a tree.

### Analysis

Intuitively, what we want to do is to remove the connections *a -> b* while a is not the node's parent in the tree. The way to distinguish those connections is to find out if there's another way to connect a -> b. If there is any, saying a -> a' -> b, then we know b's parent is a' instead a then we should remove a -> b.

From a different perspective, if we imagine the tuples in the list are the edges in a directed graph, then we want is a [**transitive reduction**](https://en.wikipedia.org/wiki/Transitive_reduction) of the original graph (represented by all the edges). The transitive reduction of a graph contains *the fewest possible edges that has the same reachability relation as the original graph, by discarding the edges u → v for which G also contains a longer path connecting the same two vertices.* In this way, the transitive reduction we generated will be the tree the question asked.

1. list given is a **partially ordered set** of a [**DAG** (Directed Acyclic Graph)](https://en.wikipedia.org/wiki/Directed_acyclic_graph)
1. You can also find it [here](https://algs4.cs.princeton.edu/42digraph/) at item 38 in *Web Exercises* section.
1. This question is a **tree** problem, but actually it's asking for a solution that generates the transitive reduction of a **graph**. It's important to *look beyond the surface*.

### Algorithm

#### Method 0: DFS

Following the intuitive algorithm, we could loop through every node **u** in the graph and remove the edges **u->v** if there are other paths that connect from **u** to **v**.

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

We want to remove the edges that connects u->v if there are other longer paths. From another perspective, we want to keep the path from **root -> v** with longest path. From the description we a vertex will have a edge to all of it's decedents, which means a vertex will have all the edges from all of it's ancestors (indegree[v] = len(path_from_root). If we order the edges by the distance in the longest path, then we only keep the closest one. Here we can use [Topological sort](https://en.wikipedia.org/wiki/Topological_sorting) to get the order. In the process of sorting, in each round we will have node **u** that have no edge from others to u (i.e. 0 [indegree](https://en.wikipedia.org/wiki/Directed_graph#Indegree_and_outdegree)). For every one of it's neighbors **v** it connects *to*, if there's any other nodes **x** connects **to** v, then it means there will be longer path(s) that connects from **u** to **v**, then the edge u->v should be discarded. 

We will use two maps to keep track of the indegrees and outdegrees of a node, and do edits in the process of sorting. 


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














