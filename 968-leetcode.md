---
title: LeetCode 968. Binary Tree Cameras - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Binary Tree Cameras – LeetCode 968  
> *Hard – Post‑Order Greedy DFS*  

Solve the problem in **Java, Python, and C++** and read a short, SEO‑friendly blog post that explains the idea, the “good, the bad, and the ugly,” and why mastering this problem helps you land a tech‑job.

---

## 1.  Code

> **Tip:** The solution is the same idea in all three languages:  
>  *Post‑order* traversal that returns one of three states  
>  – **-1** : node is **not monitored**  
>  – **0** : node is **monitored** (no camera here)  
>  – **1** : node **has a camera**

> The parent uses these values to decide whether it needs a camera.

---

### 1.1  Java

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    private int cameras = 0;

    public int minCameraCover(TreeNode root) {
        // If root is unmonitored after DFS we need one more camera.
        return dfs(root) == -1 ? cameras + 1 : cameras;
    }

    /**
     * @return  -1 : node is NOT monitored
     *           0 : node is monitored (no camera)
     *           1 : node HAS a camera
     */
    private int dfs(TreeNode node) {
        if (node == null) return 0;          // null is considered monitored

        int left  = dfs(node.left);
        int right = dfs(node.right);

        // If any child is not monitored, place camera here.
        if (left == -1 || right == -1) {
            cameras++;
            return 1;                       // this node now has a camera
        }

        // If any child has a camera, this node is monitored.
        if (left == 1 || right == 1) {
            return 0;                       // monitored, no camera
        }

        // None of the children have a camera and all are monitored.
        return -1;                          // not monitored, parent must watch
    }
}
```

---

### 1.2  Python

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def minCameraCover(self, root: TreeNode) -> int:
        self.cameras = 0

        if self.dfs(root) == -1:          # root is still not monitored
            return self.cameras + 1
        return self.cameras

    def dfs(self, node: TreeNode) -> int:
        """
        Return:
            -1 : node is NOT monitored
             0 : node is monitored
             1 : node HAS a camera
        """
        if not node:
            return 0                      # null is considered monitored

        left  = self.dfs(node.left)
        right = self.dfs(node.right)

        if left == -1 or right == -1:
            self.cameras += 1
            return 1

        if left == 1 or right == 1:
            return 0

        return -1
```

---

### 1.3  C++

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
    int minCameraCover(TreeNode* root) {
        cameras = 0;
        if (dfs(root) == -1) return cameras + 1;   // root not covered
        return cameras;
    }

private:
    int cameras = 0;

    // -1: NOT monitored, 0: monitored, 1: has camera
    int dfs(TreeNode* node) {
        if (!node) return 0;                       // null is monitored

        int left  = dfs(node->left);
        int right = dfs(node->right);

        if (left == -1 || right == -1) {           // child unmonitored
            ++cameras;
            return 1;                              // place camera here
        }

        if (left == 1 || right == 1) {             // child has camera
            return 0;                              // monitored
        }

        return -1;                                 // still not monitored
    }
};
```

---

## 2.  Blog Article

> **Title:** *Binary Tree Cameras – LeetCode 968 | Hard Problem Solved in Java, Python & C++*  
> **Keywords:** binary tree cameras, LeetCode 968, hard problem, interview algorithm, post‑order DFS, greedy, job interview, tech recruiter

---

### 2.1  Introduction

If you’re preparing for a **software engineering interview**, you’ll find yourself tackling hard problems that push your understanding of trees, dynamic programming, and greedy strategies.  
LeetCode 968 – **Binary Tree Cameras** – is one of those signature problems.  

> **Why it matters**  
> • Tests your ability to reason with tree states.  
> • Requires a clean, bottom‑up DP solution.  
> • Shows recruiters you can write efficient, readable code in multiple languages.

In this article we walk through the **good**, the **bad**, and the **ugly** of this problem, share a **greedy post‑order** solution, and show how you can implement it in **Java, Python, and C++**.

---

### 2.2  Problem Statement

> **Given** a binary tree, you may place a camera on any node.  
> A camera can monitor its parent, itself, and its immediate children.  
> **Return the minimum number of cameras needed to monitor every node** of the tree.

*Constraints:*  
- 1 ≤ number of nodes ≤ 1000  
- Node values are all 0 (value is irrelevant).

---

### 2.3  The Good – Why the Greedy Idea Works

- **Bottom‑up reasoning:** A node’s state is fully determined by the states of its children.  
- **Only 3 states needed** (unmonitored, monitored, camera) – keeps the DP small.  
- **Linear time** (O(n)) and **O(h)** auxiliary stack (recursion depth).  
- **Elegant code**: just one `dfs` function and a counter.

**State diagram**

| Left | Right | Current |
|------|-------|---------|
| -1 (unmonitored) | * | **camera** (place here) |
| 1 (camera) | * | monitored |
| 0 (monitored) | 0 | unmonitored (needs parent to watch) |

---

### 2.4  The Bad – Common Pitfalls

| Pitfall | Why it hurts | Fix |
|---------|--------------|-----|
| **Treat null as unmonitored** | Causes infinite cameras in leaf‑only trees | Return 0 for null (considered monitored) |
| **Return value misuse** | Confusing `-1` with `0` in Java’s primitive ints | Keep a clear comment or enum |
| **Recursive depth** | Stack overflow for 1000‑node deep skewed tree | Use iterative post‑order or increase stack limit (Java: `-Xss256m`) |
| **Not checking root after DFS** | May miss camera on root when whole tree unmonitored | Add `if (dfs(root) == -1) cameras++` |

---

### 2.5  The Ugly – When Things Go Wrong

- **Mis‑ordering of conditions**: Placing camera before checking children leads to sub‑optimal counts.  
- **Over‑complicated DP**: Trying to store a list of camera positions or using BFS instead of DFS turns the elegant solution into a mess.  
- **Hard‑coding tree depth**: Some solutions pre‑compute the depth, then run another loop – unnecessary.

---

### 2.6  Full Solution – Post‑Order Greedy DFS

1. **DFS** returns **state** of node.  
2. If any child is **unmonitored** (`-1`), place camera on current node (`cameras++`) and return **camera** (`1`).  
3. If any child has a **camera** (`1`), node is **monitored** (`0`).  
4. Otherwise node is **unmonitored** (`-1`).  
5. After DFS, if the **root** is unmonitored, add one more camera.

> **Time**: O(n) – each node visited once.  
> **Space**: O(h) – recursion stack, where `h` is tree height.

---

### 2.7  Code Samples

*(See the code section above for Java, Python, C++ implementations.)*

---

### 2.8  How This Helps You Get a Job

1. **Demonstrates Tree DP** – Many interviewers ask “what’s the state?” and “why bottom‑up?”  
2. **Shows Coding Cleanliness** – Three languages, identical logic → great portfolio snippet.  
3. **Highlights Problem‑Solving Mindset** – Recognizing the greedy property reduces complexity dramatically.  
4. **Prepares for Variations** – If they ask “what if cameras cost differently?” you can extend the DP.  

Add this problem to your **LeetCode Top 100 Hard** list, post the solutions on GitHub, and discuss the trade‑offs in your interview.

---

### 2.9  Quick Reference Cheat‑Sheet

| Language | Counter | DFS Signature |
|----------|---------|---------------|
| Java     | `int cameras` | `int dfs(TreeNode node)` |
| Python   | `self.cameras` | `def dfs(self, node)` |
| C++      | `int cameras` | `int dfs(TreeNode* node)` |

---

### 2.10  Takeaway

> *Binary Tree Cameras* teaches you how to **model a problem with a tiny state machine** and solve it with a **single DFS pass**.  
> Master this, and you’ll be ready to tackle any tree‑related hard problem on the interview floor.

Good luck! 🚀

---

> **Resources:**  
> • LeetCode 968: https://leetcode.com/problems/binary-tree-cameras/  
> • Discuss on Reddit /r/cscareerquestions.  
> • GitHub repository for multi‑lang solutions.  

---

> **Disclaimer:** The code is written with Java 17, Python 3.10, and C++17 in mind.  
> Adaptations may be required for your particular compiler or runtime environment.

---


> **End of Article**



---

> **Note to readers:**  
> If you’d like a deeper dive into tree DP or need help with another LeetCode hard problem, drop a comment or email us at `interviewguru@example.com`. Happy coding!