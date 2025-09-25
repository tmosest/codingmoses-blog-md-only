---
title: LeetCode 261. Graph Valid Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# 🚀 Mastering LeetCode 261 – “Graph Valid Tree”

> **Problem**: Determine whether a given undirected graph with `n` nodes and `edges` forms a *valid tree*  
> **Languages**: Java | Python | C++  
> **Tag**: Graphs • Union‑Find • DFS • BFS

---

## Table of Contents  

1. [Problem Recap](#problem-recap)  
2. [Why It Matters in Interviews](#why-it-matters)  
3. [Solution Overview](#solution-overview)  
4. [Implementations](#implementations)  
   - [Java](#java)  
   - [Python](#python)  
   - [C++](#c++)  
5. [Analysis](#analysis)  
6. [Pitfalls: The Good, The Bad, and The Ugly](#pitfalls)  
7. [Alternative Approaches](#alternatives)  
8. [Take‑away for Your Resume & Interview](#takeaway)  

---

## Problem Recap

```text
Given n nodes labeled 0 … n-1 and an edge list edges, return true
iff the graph is a *valid tree*.

A valid tree must satisfy two conditions:
1. It is connected (every node reachable from any other).
2. It has no cycles.
```

*Constraints*  
- 1 ≤ n ≤ 2000  
- 0 ≤ edges.length ≤ 5000  
- edges[i] = [a, b] with 0 ≤ a, b < n, a ≠ b  
- No duplicate edges or self‑loops.

---

## Why It Matters in Interviews

- **Fundamental Graph Knowledge**: Valid trees are the building blocks of many advanced topics (spanning trees, network design, file‑system hierarchies).
- **Union‑Find Mastery**: The disjoint‑set data structure is a staple of *O(α(n))* operations, essential for solving connectivity & cycle‑detection problems.
- **DFS/BFS Basics**: Demonstrates proficiency with recursive traversal, adjacency lists, and cycle detection logic.
- **Time & Space Complexity**: Shows ability to reason about algorithmic efficiency.

> *Pro tip*: “LeetCode 261 is the *benchmark* problem for Union‑Find. Mastering it showcases you understand both graph theory and low‑level optimization.*

---

## Solution Overview

The graph is a tree **iff** it satisfies both properties above.  
We can check these in one pass using Union‑Find:

1. **Cycle detection** – While processing each edge `(u, v)`, if `u` and `v` already belong to the same set, a cycle exists → return `false`.
2. **Connectivity** – After all edges, the graph must have exactly `n‑1` edges (a tree with `n` nodes has `n‑1` edges). If not, the graph is disconnected → return `false`.

The algorithm runs in **O(n + m)** time (where `m = edges.length`) and **O(n)** space.

---

## Implementations

### 1. Java (Union‑Find)

```java
// LeetCode 261 - Graph Valid Tree
// Java 17

import java.util.*;

public class Solution {
    // Path‑compression + Union‑by‑Rank (optional but keeps find O(α(n)))
    private int[] parent;
    private int[] rank;

    public boolean validTree(int n, int[][] edges) {
        if (edges.length != n - 1) return false;     // quick connectivity check

        parent = new int[n];
        rank   = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;

        for (int[] e : edges) {
            int u = e[0], v = e[1];
            if (!union(u, v)) return false;           // cycle found
        }
        return true;
    }

    private int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]);   // path compression
        return parent[x];
    }

    // returns true if union performed, false if already in same set
    private boolean union(int x, int y) {
        int px = find(x), py = find(y);
        if (px == py) return false; // cycle

        if (rank[px] < rank[py]) parent[px] = py;
        else if (rank[px] > rank[py]) parent[py] = px;
        else { parent[py] = px; rank[px]++; }

        return true;
    }
}
```

> **Why `edges.length != n - 1`?**  
> This is a fast exit: a tree with `n` nodes must have exactly `n-1` edges. Any other count guarantees a disconnected graph or a cycle.

---

### 2. Python (Union‑Find)

```python
# LeetCode 261 - Graph Valid Tree
# Python 3

class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank   = [0] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])   # path compression
        return self.parent[x]

    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:              # already connected -> cycle
            return False
        # union by rank
        if self.rank[px] < self.rank[py]:
            self.parent[px] = py
        elif self.rank[px] > self.rank[py]:
            self.parent[py] = px
        else:
            self.parent[py] = px
            self.rank[px] += 1
        return True

class Solution:
    def validTree(self, n: int, edges: List[List[int]]) -> bool:
        if len(edges) != n - 1:     # connectivity fast‑fail
            return False

        uf = UnionFind(n)
        for u, v in edges:
            if not uf.union(u, v):  # cycle detected
                return False
        return True
```

---

### 3. C++ (Union‑Find)

```cpp
// LeetCode 261 - Graph Valid Tree
// C++17

class UnionFind {
    vector<int> parent, rank;
public:
    UnionFind(int n) : parent(n), rank(n, 0) {
        iota(parent.begin(), parent.end(), 0);
    }

    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]); // path compression
        return parent[x];
    }

    bool unite(int x, int y) {
        int px = find(x), py = find(y);
        if (px == py) return false; // cycle

        if (rank[px] < rank[py]) parent[px] = py;
        else if (rank[px] > rank[py]) parent[py] = px;
        else {
            parent[py] = px;
            ++rank[px];
        }
        return true;
    }
};

class Solution {
public:
    bool validTree(int n, vector<vector<int>>& edges) {
        if (edges.size() != n - 1) return false;  // quick connectivity check

        UnionFind uf(n);
        for (auto &e : edges) {
            if (!uf.unite(e[0], e[1])) return false; // cycle
        }
        return true;
    }
};
```

---

## Analysis

| Complexity | Java | Python | C++ |
|------------|------|--------|-----|
| **Time**   | O(n + m) | O(n + m) | O(n + m) |
| **Space**  | O(n) | O(n) | O(n) |

*`m = edges.length`*  
The dominant factor is the linear scan of all edges; the find/union operations are almost constant time due to path compression and union‑by‑rank.

---

## Pitfalls: The Good, The Bad, and The Ugly

| Category | What can go wrong | How to avoid |
|----------|------------------|--------------|
| **Good** | *Union‑Find* is perfect for this problem – handles both cycle detection and connectivity in one pass. | Use path compression & union‑by‑rank to stay near constant time. |
| **Bad**  | Recursive `find()` can hit recursion depth limits in very deep trees (Python). | Convert `find` to iterative loop or increase recursion limit (`sys.setrecursionlimit`). |
| **Ugly** | Ignoring the `n-1` edge rule leads to false positives on disconnected graphs. | Add the early exit: `if (edges.length != n - 1) return false;` |
| **Bad**  | BFS/DFS cycle detection without visited markers can incorrectly identify cycles in undirected graphs. | Always pass the parent node to avoid counting the back‑edge to the parent. |
| **Ugly** | Using adjacency lists without `HashSet` for neighbor look‑ups leads to O(n²) complexity on dense graphs. | Prefer `List<int[]>` or `int[][]` for edge list representation in this problem. |

---

## Alternative Approaches

| Method | Pros | Cons |
|--------|------|------|
| **DFS (recursive)** | Simple to write; cycle detection via parent. | Must track visited nodes; risk of stack overflow for deep graphs. |
| **BFS (queue)** | Same as DFS but avoids recursion. | Extra queue memory; same O(n+m) complexity. |
| **Adjacency List + Union‑Find** | Handles both cycle detection & connectivity in a single data structure. | Slightly more boilerplate than a plain DFS. |

> *Which to choose in an interview?*  
> - If the interviewer wants to test **graph traversal**, go with DFS/BFS.  
> - If they want to see **data‑structure design**, propose Union‑Find.  

---

## Take‑away for Your Resume & Interview

- **Core Concept**: A *valid tree* has exactly `n‑1` edges, is connected, and cycle‑free.
- **Algorithmic Trick**: Union‑Find simultaneously checks for cycles and connectivity.
- **Performance**: O(n + m) time, O(n) space—beat the naive O(n²) DFS for large inputs.
- **Interview Value**: Demonstrates mastery of disjoint‑set data structures, graph theory fundamentals, and algorithmic optimization—all hot topics for software engineering roles.

> **Keyword‑packed headline**:  
> *“Solved LeetCode 261 – Graph Valid Tree – Union‑Find O(n+m) – Java, Python, C++”*  
> Use this headline in your GitHub README or LinkedIn post to attract recruiters looking for graph‑savvy engineers.

---

## TL;DR – One‑Page Summary

1. **Problem**: Return `true` iff the undirected graph with `n` nodes and `edges` is a tree.  
2. **Key Properties**:  
   - Must have exactly `n-1` edges (quick fail).  
   - No cycles (detected by Union‑Find).  
   - Connected (implied by `n-1` edges + no cycle).  
3. **Union‑Find**:  
   ```text
   For each edge (u, v):
       if find(u) == find(v): cycle → false
       else union(u, v)
   ```
4. **Complexity**: `O(n + m)` time, `O(n)` space.  
5. **Implementation**: See Java, Python, C++ snippets above.  
6. **Interview Tip**: Mention both Union‑Find and DFS/BFS as alternate solutions.

---

### 🚀 Ready to impress hiring managers?  

- Post the code on GitHub with a clear README.  
- Write a short blog post (like this one) highlighting the algorithmic insight and trade‑offs.  
- Add the article to your portfolio or resume under “Data Structures & Algorithms”.  

Good luck, and may the tree logic be ever in your favor! 🌳💻

---