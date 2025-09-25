---
title: LeetCode 2764. Is Array a Preorder of Some Binary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2764. Is Array a Preorder of Some Binary Tree  
## 3‑Way Solution (Java | Python | C++) + SEO‑Optimized Blog Post  

---

## TL;DR  
> **Problem** – Given a 2‑D array `nodes = [[id, parentId], …]` that represents a binary tree, determine whether the array is a *preorder* traversal of **any** binary tree.  
> **Answer** – Yes – the solution is to simulate a preorder walk with a stack and verify that the parent of each visited node is still on the stack.  
> **Complexities** – `O(N)` time, `O(N)` extra space (`N` = number of nodes).  

The following sections contain a full walkthrough, a “good, bad, ugly” analysis, and the code in **Java, Python, and C++**. At the end you’ll find a SEO‑optimized blog article that you can copy‑paste to Medium, Dev.to, or your own blog to land your next interview gig.

---

## 📌 Problem Summary

You are given a list of `nodes`.  
Each entry is `[id, parentId]` where:

| index | `id` | `parentId` |
|-------|------|------------|
| i     | node identifier (unique) | identifier of its parent or `-1` if it is the root |

The input is guaranteed to describe a binary tree (every node has at most two children).  
Your task: **Return `true` if the array is a preorder traversal of *some* binary tree, otherwise return `false`.**

> **Preorder** – visit current node → traverse left subtree → traverse right subtree.

---

## 🔍 Key Insight

During a preorder walk you always keep the *ancestors* of the current node on a stack.  
When you visit a node:

1. All its ancestors are already on the stack (in order from root to the node’s parent).  
2. The node’s parent must be **exactly** the *top* element of that stack (unless it’s the root).

So we can validate the array by:

1. Pushing the first node (root) onto a stack.  
2. For each subsequent node `[cur, parent]`  
   * Pop stack elements until the top equals `parent`.  
   * If the stack becomes empty before finding `parent`, the array cannot be a preorder.  
   * Push `cur` onto the stack.

If we finish without failure, the array is a valid preorder traversal.

---

## 📦 Code

> **Important** – All three implementations are written for **LeetCode’s** Java, Python, and C++ templates.  
> They use the same logic but follow language‑specific idioms.

### Java

```java
import java.util.*;

class Solution {
    public boolean isPreorder(List<List<Integer>> nodes) {
        // The first node must be the root.
        Deque<Integer> stack = new ArrayDeque<>();
        stack.addLast(nodes.get(0).get(0));

        for (int i = 1; i < nodes.size(); i++) {
            int cur = nodes.get(i).get(0);
            int parent = nodes.get(i).get(1);

            // Pop until the stack's top is the parent.
            while (!stack.isEmpty() && stack.peekLast() != parent) {
                stack.pollLast();
            }

            // If we couldn't find the parent, not a valid preorder.
            if (stack.isEmpty()) {
                return false;
            }

            // Current node becomes the new active node.
            stack.addLast(cur);
        }

        return true;
    }
}
```

### Python

```python
class Solution:
    def isPreorder(self, nodes: List[List[int]]) -> bool:
        stack = [nodes[0][0]]                # root id

        for cur, parent in nodes[1:]:
            # Pop until the top equals the parent
            while stack and stack[-1] != parent:
                stack.pop()

            if not stack:                    # parent not found
                return False

            stack.append(cur)

        return True
```

### C++

```cpp
class Solution {
public:
    bool isPreorder(vector<vector<int>>& nodes) {
        vector<int> stack;
        stack.push_back(nodes[0][0]);               // root

        for (size_t i = 1; i < nodes.size(); ++i) {
            int cur = nodes[i][0];
            int parent = nodes[i][1];

            while (!stack.empty() && stack.back() != parent) {
                stack.pop_back();
            }

            if (stack.empty()) return false;       // parent missing

            stack.push_back(cur);
        }
        return true;
    }
};
```

---

## 🚀 Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(N)` – each node is processed once. | `O(N)` | `O(N)` |
| **Space** | `O(N)` – stack holds at most the current ancestor chain. | `O(N)` | `O(N)` |
| **Auxiliary** | Stack | Stack | Stack |

---

## 🧩 “Good, Bad, Ugly” Discussion

| Category | What Worked | What Didn’t Work | Lessons Learned |
|----------|-------------|------------------|-----------------|
| **Good** | *Linear* algorithm – perfect for `n <= 10^5`. <br> *Simple* stack logic – easy to reason and debug. <br> *Deterministic* – no back‑tracking, no recursion stack overflow. |  |  |
| **Bad** | The stack may grow to depth `n` in a degenerate tree (all nodes on one side). This still fits within `O(N)` space but can hit memory limits on extreme test cases. |  | Use an iterative stack; avoid recursion. |
| **Ugly** | The problem statement guarantees a *valid* binary tree, but if it were not, we’d need to verify binary‑ness (max 2 children) too. |  | Add child count checks if required. |

---

## 📚 Practical Interview Take‑away

1. **Always think about the traversal invariant**. Preorder keeps a stack of ancestors.  
2. **Avoid naive tree construction** – you can validate on the fly.  
3. **Consider edge cases** – root, single child, deep skewed tree.  
4. **Explain complexity** – interviewers love seeing `O(N)` reasoning.  

---

## 🎯 SEO‑Optimized Blog Post

> **Title:** *Master LeetCode 2764 – “Is Array a Preorder of Some Binary Tree” – A Complete Java/Python/C++ Solution*  
> **Meta Description:** Learn how to solve LeetCode 2764 in linear time. Full stack‑based solution in Java, Python, and C++. Understand the problem, algorithm, complexity, and interview tips.  

---

### Introduction

If you’re hunting for a new role as a software engineer or just sharpening your data‑structure skills, LeetCode problems are a staple. One of the trickier ones is **2764 – Is Array a Preorder of Some Binary Tree**. In this post, I’ll walk you through the problem, give you a clean stack‑based solution, and share some interview‑style insights. By the end, you’ll have a ready‑to‑copy code snippet in **Java, Python, and C++** – perfect for your next coding interview.

---

### Problem Recap

You’re given a list `nodes = [[id, parentId], …]` describing a binary tree.  
Your job: **Decide if the list is a preorder traversal of *any* binary tree.**  

Preorder means you visit the current node, then its left subtree, then its right subtree. The input is guaranteed to form a binary tree (each node has at most two children), but you don’t know the shape.

---

### Why the Stack Approach Works

During a preorder traversal you *always* visit an ancestor before any of its descendants.  
Thus, if you look at the current node you just visited, the stack of ancestors **must** contain its parent at the top.

By simulating this walk:

1. **Push the root** onto the stack.  
2. For each subsequent node:
   * While the stack’s top isn’t the node’s parent, pop – that means we finished exploring a subtree.  
   * If the stack becomes empty before we find the parent → the order is invalid.  
   * Push the current node onto the stack – it becomes the new “active” node.

If you finish processing all nodes, the order is valid.

---

### Code – Java, Python, C++

```java
// Java
class Solution {
    public boolean isPreorder(List<List<Integer>> nodes) {
        Deque<Integer> stack = new ArrayDeque<>();
        stack.addLast(nodes.get(0).get(0));          // root

        for (int i = 1; i < nodes.size(); i++) {
            int cur = nodes.get(i).get(0);
            int parent = nodes.get(i).get(1);

            while (!stack.isEmpty() && stack.peekLast() != parent) {
                stack.pollLast();
            }

            if (stack.isEmpty()) return false;      // parent missing
            stack.addLast(cur);
        }
        return true;
    }
}
```

```python
# Python
class Solution:
    def isPreorder(self, nodes: List[List[int]]) -> bool:
        stack = [nodes[0][0]]                    # root

        for cur, parent in nodes[1:]:
            while stack and stack[-1] != parent:
                stack.pop()
            if not stack:
                return False
            stack.append(cur)
        return True
```

```cpp
// C++
class Solution {
public:
    bool isPreorder(vector<vector<int>>& nodes) {
        vector<int> stack;
        stack.push_back(nodes[0][0]);             // root

        for (size_t i = 1; i < nodes.size(); ++i) {
            int cur = nodes[i][0], parent = nodes[i][1];
            while (!stack.empty() && stack.back() != parent) {
                stack.pop_back();
            }
            if (stack.empty()) return false;      // parent missing
            stack.push_back(cur);
        }
        return true;
    }
};
```

---

### Complexity Analysis

- **Time** – `O(N)`  
  Each node is processed once; stack operations are constant‑time.  
- **Space** – `O(N)`  
  In the worst case (skewed tree) the stack holds all nodes.

---

### “Good, Bad, Ugly” – A Quick Take

| Category | Good | Bad | Ugly |
|----------|------|-----|------|
| Algorithm | Linear, simple stack logic | Stack may reach depth N | If the input wasn’t guaranteed to be a tree, we’d need extra child‑count checks |
| Implementation | Clean, idiomatic for each language | None | None |
| Interview Insight | Demonstrates understanding of traversal invariants | None | None |

---

### Final Thoughts – How to Use This

- **Interview Prep** – Practice explaining the stack invariant; it’s a classic pattern that shows you understand tree traversals.  
- **Resume Boost** – “Solved LeetCode 2764 (Medium) – O(N) stack solution.”  
- **Learning Path** – Next steps: try “Binary Tree Inorder” or “Tree Reconstruction from Preorder and Inorder” to deepen your traversal knowledge.

Happy coding, and may the stack always be with you! 🚀

--- 

> **Keywords**: LeetCode 2764, binary tree preorder, interview algorithm, stack solution, Java, Python, C++, coding interview, data structures, O(N) time, job interview tips.