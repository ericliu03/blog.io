---
title: "Minimum Point to Traverse Graph"
date: 2019-04-10T15:38:14-07:00
description: ""
dropCap: false
displayInMenu: false
displayInList: true
draft: false
---

## Question
There is a directed graph and you can traverse the nodes in this graph by following the directed edges. You can start from any node in this graph and you can pick nodes as start points as many as you needed. What is the minimum number of nodes as start points you need to traverse all the nodes?

You will be given a number n as number of vertices in the graph, and a list contains all the edges in the graph.
### Examples
Example 1:
```
input: 4, [[0,1], [1,2],[2,3]]
output: 1. we just need to pick 1 as start point and will be able to traverse the whole graph
```

Example 2:
```
input: 4, [[2,1], [3,1],[0,1]]
output: 3. we have to start from 2,3 and 4 to reach all the nodes
```

Example 3:
```
input: 6, [[0,1], [1,2], [2,0], [2,3], [3,4], [4,5], [5,3]]
output: 1, we could start from 3, then we could go through 0->1->2->0, and 2->3->4->5->3
```

Example 4:
```
input: 5, [[0, 1], [1, 2], [2, 3], [3, 0], [4, 0]]
output: 1, we start from 4
```

## Algorithm

### Method 1
#### Analysis
Starting from the problem description, the most straightforward is to use a recursive method. We could have a loop of all the nodes in the graph. We treat each node as a start point of a DFS and see if we can traverse the whole graph. If yes, then we return 1 since we used 1 node as start point. If not, then we go 1 level deeper in the recursive function, which is another loop of the nodes, but here instead of all the nodes, we only use the nodes we can't reach in the previous DFS. We should be able to get one result for each node that picked as starting point. We choose the smallest number among those and plus one to return as the nodes needed to cover all the nodes.

#### Code
```python
import collections

def minimum_nodes(n, edges):
    outdegrees = collections.defaultdict(set)
    for u, v in edges:
        outdegrees[u].add(v)

    result = float('inf')
    unvisited = set([i for i in range(n)])
    for i in range(n):
        result = min(result, dfs(i, outdegrees, unvisited))
        print(result, i)
    return result


def dfs(start, outdegrees, unvisited):
    unvisited_set = unvisited.copy()
    queue = [start]
    while queue:
        curr_node = queue.pop(0)
        if curr_node in unvisited_set:
            unvisited_set.remove(curr_node)
            for neighbor in outdegrees[curr_node]:
                queue.append(neighbor)

    if len(unvisited_set) == 0:
        return 1
    else:
        result = float('inf')
        for unvisited in unvisited_set:
            result = min(result, dfs(unvisited, outdegrees, unvisited_set))
        return result + 1
 ```


### Method 2
#### Analysis
Thinking about the recursive function, we should notice that we did a lot duplicated works there. (Actually, if the problem is not asking for all possible ways/permutations, then there should always be duplicated works.) In this way, this could be optimized using idea of [Dynamic Programing](https://en.wikipedia.org/wiki/Dynamic_programming). There are two general ways of doing DP, 1. use a map to remember what's the result from a start point (in this problem the start point is the node we choose as start), and go through the path using recursive function 2. use a n-Dimension array to store the result and derive results from previous results.
Here we could use the first way to remember the result of **the nodes we could reach when starting from node u**. Then each time we start the DFS, we first check if we have already examen that node as start point and if we do return the result.

#### Code
This is similar to method 1, just we remember the results we already have. But the code looks messier =|

```python
def minimum_nodes(n, edges):
    memo = {}
    outdegrees = collections.defaultdict(set)
    for u, v in edges:
        outdegrees[u].add(v)

    result = float('inf')
    unvisited = set([i for i in range(n)])
    for i in range(n):
        result = min(result, dfs(i, memo, outdegrees, unvisited))
        print(result, i)
    return result


def dfs(start, memo, outdegrees, unvisited):
    if start in memo:
        return memo[start]
    unvisited_set = unvisited.copy()
    queue = [start]
    while queue:
        curr_node = queue.pop(0)
        if curr_node in unvisited_set:
            unvisited_set.remove(curr_node)
            for neighbor in outdegrees[curr_node]:
                queue.append(neighbor)

    result = float('inf')
    if len(unvisited_set) == 0:
        result = 1
    else:
        for unvisited in unvisited_set:
            result = min(result, dfs(unvisited, memo, outdegrees, unvisited_set))
        result += 1

    memo[start] = result
    return result
 ```
### Method 3
#### Analysis

Instead of remembering everything in a map during the recursion, we could remember something else. There are two things we need, 1. the vertices we already visited in `visited`, 2. the start points we choose in `start_points`. Still, we loop through every vertex, say **u** in the graph that not in `visited`, do a search (either DFS or BFS) to get all the vertices that could be reached started with **u**. The following is the **key** to this algorithm: 1. we add those reached vertices to `visited`, so we won't do duplicated work, 2. if in the reached vertices, there's any **start points we chose, we remove those start points,** from `start_points`. 3. add **u** to `start_points`. This is because we could reach the **removed start points** from **u**, and thus the vertices that the old start vertex can reach. So there's no need to keep the old start point anymore.

#### Code

```python
def minimum_nodes3(n, edges):
    outdegrees = collections.defaultdict(set)
    for u, v in edges:
        outdegrees[u].add(v)

    visited = set()
    starts = set()
    for vertex in range(n):
        if vertex in visited:
            continue

        reached = get_reached(vertex, outdegrees)
        for s in list(starts):
            if s in reached:
                starts.remove(s)
        visited.update(reached)
        starts.add(vertex)

    return len(starts)


def get_reached(start, outdegrees):
    visited = set()
    queue = [start]
    while queue:
        curr_node = queue.pop(0)
        if curr_node in visited:
            continue

        visited.add(curr_node)
        for neighbor in outdegrees[curr_node]:
            queue.append(neighbor)

    return visited
 ```

### Method 4
#### Analysis
In the graph theory, there's a concept called [**Strongly Connected Component(SCC)**](https://en.wikipedia.org/wiki/Strongly_connected_component). If you don't know you should read about it. In short, all nodes in a SCC has a path to go to any other nodes in the same SCC. So what we need from it for this problem is that we want to treat the nodes in same SCC as one single node in a graph. After that the graph will be a DAG, and we just need to count how many nodes don't have incoming edges (i.e. 0 in-degree). Those nodes will be the start points. 

In reference 1, code for this solution is given, which used Tarjan Algorithm. I attached some great explanation in reference for this algorithm. I will write my understanding of Tarjan in the following. It uses a stack to represent the vertices that are currently visiting, and it uses `dfn[u]` to store IDs of vertices and `low[u]` to represent the vertex with lowest ID it can reach. As an example, is 6 can reach 4 (so `low[6] = 4`), and 4 can reach 1 (`low[4] = 1`). From this we know 6 can reach 1 but during the algorithm this will not be recorded directly in `low`. If `low[x]` equals to x itself, then it means it can't go back any further, and it will be recognized as *root* of this SCC. Thus all the vertices in the stack and pushed later then this *root* (i.e. we are visiting) will belongs to this SCC. This is because from this *root* we reached all those vertices, and somehow those vertices can comes back to this *root*.

In my opinion this is way more complicated in an interview, so I will just skip it here.



## Reference
1. [[树/链表/图] 求教Airbnb的graph traversal问题 ](https://www.1point3acres.com/bbs/forum.php?mod=viewthread&tid=319799)
1. [Tarjan算法寻找有向图的强连通分量](http://blog.miskcoo.com/2016/07/tarjan-algorithm-strongly-connected-components)
1. [全网最!详!细!Tarjan算法讲解](https://blog.csdn.net/hurmishine/article/details/75248876)
1. [Kosaraju算法解析: 求解图的强连通分量](https://www.cnblogs.com/nullzx/p/6437926.html)
1. [求解强连通分量算法之---Kosaraju算法](https://blog.csdn.net/dm_vincent/article/details/8554244)









