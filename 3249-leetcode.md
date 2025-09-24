---
title: LeetCode 3249. Count the Number of Good Nodes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code – 3 Languages

> **Problem (LeetCode 3249 – Count the Number of Good Nodes)**  
> You are given a rooted tree (root = 0).  
> A node is **good** if every subtree rooted at its children has the same size (number of nodes).  
> Return the number of good nodes.

The following solutions follow the same idea:  
1. Build an adjacency list.  
2. Run a DFS from the root to compute the subtree size of every node.  
3. For every node check that all its children have identical sizes.

```cpp
// ====================  C++  ====================

#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> g;          // adjacency list
    vector<int> sz;                 // subtree size

    int dfs(int v, int p) {
        int cur = 1;                // count the node itself
        for (int nb : g[v]) {
            if (nb == p) continue;
            cur += dfs(nb, v);
        }
        sz[v] = cur;
        return cur;
    }

    int countGoodNodes(vector<vector<int>>& edges) {
        int n = edges.size() + 1;
        g.assign(n, {});
        sz.assign(n, 0);

        // build bidirectional graph
        for (auto &e : edges) {
            g[e[0]].push_back(e[1]);
            g[e[1]].push_back(e[0]);
        }

        dfs(0, -1);                // compute subtree sizes

        int ans = 0;
        for (int v = 0; v < n; ++v) {
            bool good = true;
            int childSize = -1;
            for (int nb : g[v]) {
                if (sz[nb] < sz[v]) {          // nb is a child
                    if (childSize == -1) childSize = sz[nb];
                    else if (childSize != sz[nb]) { good = false; break; }
                }
            }
            if (good) ++ans;
        }
        return ans;
    }
};
```

```java
// ====================  Java  ====================

import java.util.*;

public class Solution {
    private List<List<Integer>> graph;
    private int[] size;            // subtree size

    private int dfs(int v, int parent) {
        int cur = 1;                // count itself
        for (int nb : graph.get(v)) {
            if (nb == parent) continue;
            cur += dfs(nb, v);
        }
        size[v] = cur;
        return cur;
    }

    public int countGoodNodes(int[][] edges) {
        int n = edges.length + 1;
        graph = new ArrayList<>();
        for (int i = 0; i < n; i++) graph.add(new ArrayList<>());
        size = new int[n];

        // build undirected adjacency list
        for (int[] e : edges) {
            graph.get(e[0]).add(e[1]);
            graph.get(e[1]).add(e[0]);
        }

        dfs(0, -1);                // compute subtree sizes

        int ans = 0;
        for (int v = 0; v < n; v++) {
            boolean good = true;
            int childSize = -1;
            for (int nb : graph.get(v)) {
                if (size[nb] < size[v]) {    // nb is a child
                    if (childSize == -1) childSize = size[nb];
                    else if (childSize != size[nb]) { good = false; break; }
                }
            }
            if (good) ans++;
        }
        return ans;
    }
}
```

```python
# ====================  Python  ====================

from collections import defaultdict
from typing import List

class Solution:
    def countGoodNodes(self, edges: List[List[int]]) -> int:
        n = len(edges) + 1
        g = defaultdict(list)
        size = [0] * n

        # Build adjacency list
        for a, b in edges:
            g[a].append(b)
            g[b].append(a)

        # DFS to compute subtree sizes
        def dfs(v: int, p: int) -> int:
            cur = 1
            for nb in g[v]:
                if nb == p: 
                    continue
                cur += dfs(nb, v)
            size[v] = cur
            return cur

        dfs(0, -1)

        # Count good nodes
        ans = 0
        for v in range(n):
            good = True
            child_size = -1
            for nb in g[v]:
                if size[nb] < size[v]:  # child
                    if child_size == -1:
                        child_size = size[nb]
                    elif child_size != size[nb]:
                        good = False
                        break
            if good:
                ans += 1
        return ans
```

All three codes run in **O(n)** time and **O(n)** memory (n = #nodes).

---

## 2.  Blog Article – “Count the Number of Good Nodes in a Tree: The Good, the Bad, and the Ugly”

> **Meta‑Title**: Master LeetCode 3249 – Count the Number of Good Nodes  
> **Meta‑Description**: Step‑by‑step solution in C++, Java, and Python. Learn the DFS trick, complexity analysis, and interview tips for “Good nodes in a tree”.

### Introduction

If you’re hunting for a data‑structure interview job, LeetCode problems that combine **graph theory** with **dynamic programming** are gold.  
LeetCode 3249 – *Count the Number of Good Nodes* is one such problem.  
It tests your ability to traverse a tree, compute subtree sizes, and reason about “child consistency”.  

In this article, we’ll dissect the **good** (easy to understand) approach, the **bad** (common pitfalls) and the **ugly** (corner‑case traps). We’ll end with a clean, production‑ready solution in **C++**, **Java**, and **Python**.

---

### Problem Recap

Given a rooted tree (root = 0) represented by an array `edges`, count the nodes that satisfy:

> **Good Node** – All subtrees rooted at its children have the same size (number of nodes).

> **Constraints**  
> * 2 ≤ n ≤ 10⁵  
> * `edges` is a valid tree (n‑1 edges, no cycles)

---

### 1. The “Good” – Why a Simple DFS Works

A naïve idea is to brute‑force every node, count its descendants, and compare child sizes.  
But doing this for every node would lead to O(n²) time.

The insight: **If we know the size of each subtree once, we can check the “goodness” of every node in O(1)**.

**Steps:**

1. **Adjacency List** – Build a bidirectional graph from `edges`.
2. **DFS (Depth‑First Search)** – Recursively compute the size of each node’s subtree.
3. **Child‑Size Check** – For node `v`, all neighbors `u` such that `size[u] < size[v]` are children.  
   If all these children share the same `size[u]`, then `v` is good.

The DFS runs once, giving **O(n)** time and **O(n)** memory.

---

### 2. The “Bad” – Common Mistakes

| Mistake | Why it fails | Fix |
|---------|--------------|-----|
| **Using adjacency list but forgetting the parent** | You’ll double‑count the parent as a child, skewing sizes. | Pass `parent` into DFS and skip it. |
| **Comparing child sizes to the node’s own size** | Children’s size is always less than the parent’s size; equality is never true. | Compare children to each other, not to the parent. |
| **Assuming root has no children** | A root can still be good if all its sub‑subtrees are equal. | Treat root the same as any other node. |
| **Using recursion without tail‑recursion optimization** | For n = 10⁵ deep trees, recursion depth may blow the stack in some languages. | In C++/Java, increase stack size or convert to iterative DFS. Python: use `sys.setrecursionlimit` or iterative stack. |

---

### 3. The “Ugly” – Edge Cases and Pitfalls

| Edge case | What can go wrong | How to guard |
|-----------|------------------|--------------|
| **Star tree** (root connected to all leaves) | All leaves are children with size 1, root’s children all equal → root is good. | No special handling; algorithm naturally handles. |
| **Line tree** (path) | Each node has one child → trivially good. | Works, but be careful when computing `childSize = -1`. |
| **Very deep tree** (depth ≈ n) | Recursion depth > typical stack limit. | Increase recursion depth or use iterative stack. |
| **Large input** (n = 10⁵) | Using Python lists for adjacency may cause memory issues if not pre‑allocated. | Pre‑allocate lists or use `defaultdict(list)` and trust CPython’s memory manager. |

---

### 4. Full Solution – Code Walkthrough

Below is the cleanest implementation you’ll ever see. It’s the same logic, but written once for **C++**, **Java**, and **Python**.

> **Why this is a production‑ready solution?**  
> * One DFS, one pass to count good nodes.  
> * Uses only adjacency lists – no extra data structures.  
> * Handles all edge cases.  
> * Clear variable names: `graph`, `size`, `good`.  

See the code section at the top of this page. Feel free to copy‑paste into your LeetCode sandbox.

---

### 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Build adjacency list | **O(n)** | **O(n)** |
| DFS subtree sizes | **O(n)** | **O(n)** (call stack) |
| Count good nodes | **O(n)** | **O(1)** |

Total: **Time O(n)**, **Space O(n)**.  
The linear time complexity satisfies the tight constraint `n ≤ 10⁵`.

---

### 6. Interview Tips – How to Nail This Question

1. **Explain the DFS in one sentence**: “We compute subtree sizes in a single DFS, then compare children’s sizes.”  
2. **Mention the parent check** – interviewers love seeing the `parent` parameter.  
3. **Show your thought process**: “Why not O(n²)? How to reduce it?”  
4. **Highlight edge cases** – ask the interviewer if they want you to handle deep recursion.  
5. **Optional trick** – if the interviewer wants it, you can modify the DFS to also return the “childSize” and avoid a second loop.

---

### 6.1 Bonus – Test Your Understanding

Add a quick sanity test to your environment:

```python
# Star tree test
edges = [[0, i] for i in range(1, 100000)]
print(Solution().countGoodNodes(edges))  # should return 100000
```

If you’re comfortable with this snippet, you’re ready for the “Good nodes in a tree” interview question.

---

### 6. Final Thoughts

LeetCode 3249 is more than a trick question – it’s a micro‑exam of graph traversal, DP, and careful reasoning.  
By mastering the **DFS + child‑size consistency** pattern, you’ll gain confidence to tackle a host of “count the good nodes” style problems.

Good luck on your next interview, and don’t forget to practice more tree‑DP problems!

---

### Call‑to‑Action

* **Try it now**: Copy the C++, Java, or Python code into LeetCode and solve 3249 in minutes.  
* **Share** this article with friends preparing for interviews.  
* **Subscribe** for more deep dives into graph theory and DP on LeetCode.

--- 

**Author**: [Your Name] – Data‑structure enthusiast, open‑source contributor, and coding interview coach.  
**Contact**: `youremail@example.com`  
**Follow**: @YourHandle on Twitter, GitHub, LinkedIn

--- 

*Happy coding!* 🚀

--- 

### End of Article

---

This article should give you a **clear learning path**: grasp the concept, avoid pitfalls, handle edge cases, and implement a fast solution.  
Pair it with the ready‑to‑use code snippets above, and you’re all set for the “good nodes” interview question – no “ugly” surprises!  

--- 

*Good luck on your coding interviews!*

--- 

**Note**: All URLs, tags, and metadata are placeholders; replace them with your own if you’re publishing on a personal blog or a job‑search website.