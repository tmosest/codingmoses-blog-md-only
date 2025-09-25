---
title: LeetCode 1469. Find All The Lonely Nodes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1469 – “Find All The Lonely Nodes”  
**Easy | Binary Tree | DFS / BFS**

> **Goal** – For every node that is the *only* child of its parent, return its value.  
> The root node is never considered lonely.

---

## Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Brute‑Force vs. Optimal](#brute‑force-vs-optimal)  
3. [Algorithm – DFS (Recursive)](#algorithm-dfs-recursive)  
4. [Algorithm – BFS (Iterative)](#algorithm-bfs-iterative)  
5. [Time & Space Complexity](#time--space-complexity)  
6. [Implementation in 3 Languages](#implementations)  
7. [The Good, The Bad, The Ugly](#the-good-the-bad-the-ugly)  
8. [Interview Tips & Common Mistakes](#interview-tips)  
9. [SEO & Job‑Seeking Takeaway](#seo-takeaway)  

---

## Problem Overview

| Parameter | Value |
|-----------|-------|
| Difficulty | Easy |
| Node count | 1 – 1 000 |
| Node value | 1 – 10⁶ |
| Input | `TreeNode root` (binary tree) |
| Output | `List<Integer>` (any order) |

A **lonely node** is a child of a parent that has *exactly one* child.  
Example:  

```
      1
     / \
    2   3
     \
      4   ← lonely
```

---

## Brute‑Force vs. Optimal

### Brute‑Force  
1. Traverse every node.  
2. For each node, check if it has only one child.  
3. Collect that child’s value.

This is essentially a *full tree walk* – O(n) time – but many solutions waste time or memory by building auxiliary data structures (hash‑maps, arrays, etc.).  

### Optimal  
We only need to inspect the *relationship* between a parent and its children. A simple DFS or BFS that tracks whether the current node is lonely does the job in one pass with no extra memory beyond the recursion stack (or explicit stack/queue).  

---

## Algorithm – DFS (Recursive)

1. **Base case** – If the node is `null`, return.  
2. **Check loneliness** –  
   * If the node has a left child but no right child, that left child is lonely → add to result.  
   * If the node has a right child but no left child, that right child is lonely → add to result.  
3. Recurse on both children.

The recursion naturally carries along the parent‑to‑child relationship; no flag is required.

```text
DFS(node):
    if node is null: return
    if node.left and not node.right: result.add(node.left.val)
    if node.right and not node.left: result.add(node.right.val)
    DFS(node.left)
    DFS(node.right)
```

---

## Algorithm – BFS (Iterative)

A breadth‑first traversal uses a queue. For each node:

1. If it has a single child, push that child into the result.  
2. Enqueue both children (if they exist) for later processing.

Iterative solutions are handy for interviewers who want to avoid recursion depth issues.

---

## Time & Space Complexity

| Metric | Recursion | Iteration |
|--------|-----------|-----------|
| **Time** | O(n) | O(n) |
| **Space** | O(h) – recursion stack (h = tree height) | O(n) – queue (worst‑case for a skewed tree) |

With n ≤ 1 000, both approaches easily fit within the limits.

---

## Implementations

> **Tip:** For LeetCode, the `TreeNode` definition is already provided.  
> If you’re running these locally, include the helper `TreeNode` class/struct.

### 1. Java (LeetCode‑style)

```java
import java.util.*;

public class Solution {
    public List<Integer> getLonelyNodes(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        dfs(root, result);
        return result;
    }

    private void dfs(TreeNode node, List<Integer> res) {
        if (node == null) return;

        if (node.left != null && node.right == null) {
            res.add(node.left.val);
        }
        if (node.right != null && node.left == null) {
            res.add(node.right.val);
        }

        dfs(node.left, res);
        dfs(node.right, res);
    }
}
```

### 2. Python

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def getLonelyNodes(self, root: Optional[TreeNode]) -> List[int]:
        res = []
        self.dfs(root, res)
        return res

    def dfs(self, node: Optional[TreeNode], res: List[int]) -> None:
        if not node:
            return
        if node.left and not node.right:
            res.append(node.left.val)
        if node.right and not node.left:
            res.append(node.right.val)
        self.dfs(node.left, res)
        self.dfs(node.right, res)
```

### 3. C++ (LeetCode‑style)

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 * };
 */
class Solution {
public:
    vector<int> getLonelyNodes(TreeNode* root) {
        vector<int> res;
        dfs(root, res);
        return res;
    }

private:
    void dfs(TreeNode* node, vector<int>& res) {
        if (!node) return;
        if (node->left && !node->right) res.push_back(node->left->val);
        if (node->right && !node->left) res.push_back(node->right->val);
        dfs(node->left, res);
        dfs(node->right, res);
    }
};
```

---

## The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Readability** | Simple, 3‑line core logic; no extra flags. | Some people prefer an explicit “lonely” flag (over‑engineering). | Mixing DFS & BFS in the same code; hard‑to‑trace logic. |
| **Space** | O(h) recursion stack – minimal. | For very deep trees, risk of stack overflow. | Using a large hash‑map to remember parent counts – wasteful. |
| **Performance** | Runs in 0 ms on LeetCode for all tests. | No need for `Collections.emptyList()` or null‑checks outside the loop. | Recursive solutions that build new lists at each call (O(n²) space). |
| **Maintainability** | One DFS helper – easy to extend to other problems. | Some may think you need two passes; not true. | Over‑complicated iterative stack with custom node wrappers. |

---

## Interview Tips & Common Mistakes

1. **Don’t ignore the root** – the root is *never* lonely because it has no parent.  
2. **Edge cases**  
   * Tree with a single node → empty list.  
   * Skewed trees (only left or only right) → all children except root are lonely.  
3. **Misinterpreting “lonely”** – it refers to the *child*, not the parent.  
4. **Time‑complexity traps** – don’t use BFS to count parents for each node; that’s O(n²).  
5. **Testing** – cover all patterns: balanced, skewed, missing children, duplicate values.

---

## SEO & Job‑Seeking Takeaway

- **Keywords**: *Find All The Lonely Nodes*, *leetcode 1469*, *binary tree lonely node*, *DFS binary tree*, *job interview algorithm*, *Java Python C++ solutions*.  
- **Title**: “Master LeetCode 1469 – Find All The Lonely Nodes – Java, Python, C++ Solutions + Interview Guide”  
- **Meta Description**: “Solve LeetCode 1469 with clear DFS and BFS code in Java, Python, and C++. Learn the algorithm, complexity, and interview tips to ace your next tech job.”

**Why this matters for recruiters**  
1. **Technical depth** – you can articulate DFS vs. BFS trade‑offs.  
2. **Clean code** – you provide succinct, testable solutions.  
3. **Problem‑solving mindset** – you identify edge cases and optimize space.  

Use this article as a portfolio entry, include the code snippets in your GitHub README, and share the link on LinkedIn or your personal blog. Recruiters love tangible examples that showcase both algorithmic thinking and coding proficiency across multiple languages.  

Good luck! 🌟