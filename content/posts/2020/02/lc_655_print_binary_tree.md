---
title: "[Leetcode 655] Print Binary Tree"
description: ""
date: "2020-02-24T21:45:10-08:00"
thumbnail: ""
categories:
  - ""
tags:
  - "dfs"
  - "binary"
  - "tree"
---





## Question

[655. Print Binary Tree](https://leetcode.com/problems/print-binary-tree/)

Print a binary tree in an `m*n` 2D string array following these rules:

1. The row number m should be equal to the height of the given binary tree.
1. The column number n should always be an odd number.
1. The root node's value (in string format) should be put in the exactly middle of the first row it can be put. The column and the row where the root node belongs will separate the rest space into two parts (left-bottom part and right-bottom part). You should print the left subtree in the left-bottom part and print the right subtree in the right-bottom part. The left-bottom part and the right-bottom part should have the same size. Even if one subtree is none while the other is not, you don't need to print anything for the none subtree but still need to leave the space as large as that for the other subtree. However, if two subtrees are none, then you don't need to leave space for both of them.
1. Each unused space should contain an empty string "".
1. Print the subtrees following the same rules.




### Note:
The height of binary tree is in the range of [1, 10].


### Examples:

```
Input:
     1
    / \
   2   3
    \
     4
Output:
[["", "", "", "1", "", "", ""],
 ["", "2", "", "", "", "3", ""],
 ["", "", "4", "", "", "", ""]]
```


## Solution


### Method 1
一开始感觉就是需要recursive来搞，有个想法是分开生成左右子树的2d array再组合。不过这样组合的情况太多，数组操作也很cost。

题目的关键点在于，树高决定了数组的m和n。也就是`2**d-1`。所以我们可以生成好数组之后往里填。

有一点点trick的地方就是position的计算：

1. 最开始的position是中心点，假设树高`d`，那么长度为`2**d - 1 = 2 * (2**(d-1) - 1) + 1`所以中心点是第`(2**(d-1) - 1) + 1`个数，所以坐标就是`2**(d-1) - 1`。
1. 在每次往下一层走的时候，要给出下一层的深度和起始位置，这个位置与当前层的位置中间有`2**(d-1) - 1`个数字，所以位置相差了`(2**(d-1) - 1) + 1`。

```python
class Solution:
    def printTree(self, root: TreeNode) -> List[List[str]]:
        if not root:
            return ['']
                
        def depth(node):
            if not node:
                return 0
            return max(depth(node.left), depth(node.right)) + 1
        
        d = depth(root)
        result = [[''] * (2**d-1) for _ in range(d)]

        def fill(node, dep, pos):
            if node:
                result[d-1 - dep][pos] = str(node.val)
                fill(node.left, dep-1, pos - 2**(dep-1)) # 这里的位置挪动是相对的
                fill(node.right, dep-1, pos + 2**(dep-1)) # 所以挪动的数量是
        
        fill(root, d-1, 2**(d-1) - 1)
        return result
```



## Reference
1. [Python Straight Forward Solution](https://leetcode.com/problems/print-binary-tree/discuss/106250/Python-Straight-Forward-Solution)
