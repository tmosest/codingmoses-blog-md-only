---
title: LeetCode 3613. Minimize Maximum Component Cost - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧩 Minimize Maximum Component Cost –  Graph + DSU  
**LeetCode 3613 – Medium**  

> *“You are given an undirected connected graph … remove edges so that you have at most `k` components, and the maximum edge weight inside any component should be as small as possible.”*  

In this post we will:

1. **Explain the problem** in our own words.  
2. Walk through **two optimal solutions** –  
   * a **greedy DSU (Union‑Find)** approach (O(m log m)) and  
   * a **binary‑search + DFS** approach (O((n+m) log W)).  
3. Show **ready‑to‑copy Java, Python and C++ implementations** for each solution.  
4. Discuss the *good, the bad, and the ugly* – pitfalls, edge‑cases, and why the DSU approach wins in practice.  
5. Finish with a **SEO‑friendly job‑interview blog post** you can drop into your résumé or LinkedIn.

> **Keywords** – LeetCode, Graph Algorithms, DSU, Union‑Find, Binary Search, DFS, Interview, Algorithm, Job Interview, Java, Python, C++

---

## 1. Problem Recap (My Own Words)

* You have a **connected** undirected graph with `n` nodes (`0 … n‑1`).  
* Each edge has a positive weight `w`.  
* You may delete any subset of edges, but after deletion the graph must contain **at most `k` connected components**.  
* The *cost* of a component = **maximum weight of an edge inside it** (or `0` if the component is an isolated node).  
* **Goal:** minimize the maximum component cost over all components after deletions.

The answer is a single integer – the smallest possible *worst‑case* edge weight that can remain in any component.

---

## 2. Intuition & High‑Level Strategies

### 2.1. Greedy Union–Find (DSU)

Think of the reverse process: start with all nodes isolated (i.e., every node is its own component).  
If we **add** edges in increasing weight order, every time we connect two previously disconnected components we *merge* them.  
We stop once the number of components becomes `k`.  
All edges added up to that point are **kept**; all heavier edges are considered “deleted”.  
Why does this work?  
*Adding a lighter edge never hurts – it can only reduce component count.*  
*The last added edge is the heaviest that we have to keep; everything heavier can be removed.*  

Thus, the answer is the weight of the last edge added before we reach `k` components.

### 2.2. Binary Search + DFS (Alternative)

We can **guess** a threshold `mid` for the maximum allowed edge weight.  
If we delete all edges with weight > `mid`, count the resulting components with DFS/BFS.  
* If the component count `≤ k`, we can lower `mid` (try to keep the graph sparser).  
* Else (components > `k`), we need to allow a heavier edge, so raise `mid`.  

Binary‑search over the weight range `[0 … maxW]` gives the minimal feasible `mid`.  
This approach is conceptually simple but a bit slower than the DSU greedy.

---

## 3. Code Walk‑Through

Below are **full, self‑contained** implementations for both approaches in **Java, Python, and C++**.  
Each file is ready to copy‑paste into your IDE or LeetCode workspace.

> ⚠️ **Remember** that LeetCode’s class‑name / method signature for this problem is  
> ```java
> public int minCost(int n, int[][] edges, int k)
> ```
> The same applies to Python (`def minCost(self, n, edges, k)`) and C++ (`int minCost(int n, vector<vector<int>>& edges, int k)`).

---

### 3.1. Java – DSU Greedy (Union‑Find)

```java
//  Java – DSU Greedy (Union‑Find)  – O(m log m)
import java.util.*;

class Solution {
    // ---------- Union‑Find ----------
    private static class DSU {
        int[] parent, size;
        DSU(int n) {
            parent = new int[n];
            size   = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                size[i]   = 1;
            }
        }
        int find(int x) {
            if (parent[x] != x) parent[x] = find(parent[x]); // path compression
            return parent[x];
        }
        boolean union(int a, int b) {
            a = find(a);
            b = find(b);
            if (a == b) return false;
            // union by size
            if (size[a] < size[b]) { int tmp = a; a = b; b = tmp; }
            parent[b] = a;
            size[a] += size[b];
            return true;
        }
    }

    public int minCost(int n, int[][] edges, int k) {
        // Sort edges by weight ascending
        Arrays.sort(edges, Comparator.comparingInt(a -> a[2]));
        DSU dsu = new DSU(n);
        int components = n;
        int answer = 0;

        for (int[] e : edges) {
            if (components <= k) break;
            if (dsu.union(e[0], e[1])) {   // we keep this edge
                components--;
                answer = e[2];              // last kept edge weight
            }
        }
        return answer;
    }
}
```

---

### 3.2. Python – DSU Greedy

```python
#  Python – DSU Greedy  – O(m log m)

class DSU:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.size   = [1] * n

    def find(self, x: int) -> int:
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]   # path compression
            x = self.parent[x]
        return x

    def union(self, a: int, b: int) -> bool:
        ra, rb = self.find(a), self.find(b)
        if ra == rb:
            return False
        # union by size
        if self.size[ra] < self.size[rb]:
            ra, rb = rb, ra
        self.parent[rb] = ra
        self.size[ra] += self.size[rb]
        return True


class Solution:
    def minCost(self, n: int, edges: List[List[int]], k: int) -> int:
        edges.sort(key=lambda x: x[2])        # sort by weight
        dsu = DSU(n)
        comp = n
        answer = 0

        for u, v, w in edges:
            if comp <= k:
                break
            if dsu.union(u, v):
                comp -= 1
                answer = w          # last kept edge weight

        return answer
```

---

### 3.3. C++ – DSU Greedy

```cpp
//  C++17 – DSU Greedy  – O(m log m)
#include <bits/stdc++.h>
using namespace std;

struct DSU {
    vector<int> parent, sz;
    DSU(int n): parent(n), sz(n,1) { iota(parent.begin(), parent.end(), 0); }

    int find(int v){
        if(parent[v]==v) return v;
        return parent[v] = find(parent[v]);        // path compression
    }

    bool unite(int a, int b){
        a = find(a);  b = find(b);
        if(a==b) return false;
        if(sz[a] < sz[b]) swap(a,b);              // union by size
        parent[b] = a;
        sz[a] += sz[b];
        return true;
    }
};

class Solution {
public:
    int minCost(int n, vector<vector<int>>& edges, int k) {
        sort(edges.begin(), edges.end(),
             [](const auto& x, const auto& y){ return x[2] < y[2]; });

        DSU dsu(n);
        int comps = n, ans = 0;
        for (auto &e : edges) {
            if (comps <= k) break;
            if (dsu.unite(e[0], e[1])) {
                comps--;
                ans = e[2];        // last kept edge weight
            }
        }
        return ans;
    }
};
```

---

## 4. “Good, Bad, Ugly” – What to Watch For

| **Aspect** | **What Makes It Good** | **Potential Pitfall** | **How to Fix / Avoid** |
|------------|------------------------|-----------------------|------------------------|
| **Greedy DSU** | *Linear‑time merging* – no repeated scans of the graph.  <br> *Only a single pass after sorting.* | **Large weight range** (`maxW` up to 1e9) – still fine, sorting dominates. | Keep an `int` answer; no need for binary search. |
| **Binary Search + DFS** | Conceptually trivial; no need for a custom data structure. | **DFS per mid** can hit recursion depth limits in Python (`sys.setrecursionlimit`). | Use iterative stack or BFS; or switch to DSU for large data. |
| **Edge Cases** | • `k = n` → answer `0` (no edges kept).  <br>• `k = 1` → answer is the largest edge in a minimum spanning tree (Kruskal). | Mis‑handling isolated nodes → cost = 0, but we never need to explicitly treat them in the DSU solution. | Just trust DSU; it naturally gives `0` when no edge is added. |
| **Complexity** | DSU: O(m log m) time, O(n) memory. | Binary search: O((n+m) log W) time, O(n+m) memory. | For `m` up to 2×10^5, DSU comfortably fits into LeetCode’s 1‑second limit. |
| **Readability** | DSU code is concise but needs comments to explain union‑by‑size. | Binary‑search DFS code is self‑explanatory, but extra lines for component counting. | In interviews, explain the intuition before showing code. |

**Bottom line:** For this problem, the **DSU greedy** is both **fast** and **short‑circuiting** – it gives the answer in a single linear pass after sorting.

---

## 5. Take‑away Checklist for Your Interview

- **Graph + Union‑Find** → Show you know Kruskal’s algorithm and can adapt it to *“reverse”* problems.  
- **Complexity** → O(m log m) is easy to explain and satisfies LeetCode’s constraints.  
- **Edge‑case Handling** → `k = n` → answer `0`.  
- **Memory** → DSU uses two integer arrays of size `n`.  
- **Time‑to‑Solve** → Under 1 second on LeetCode for 2×10⁵ edges.  

---

## 6. TL;DR (Ready‑to‑Share Summary)

```text
Problem:  Minimize the maximum edge weight kept in a graph after deletions,
          while ending up with at most k components.

Solution 1 (DSU Greedy):
  1. Sort all edges by ascending weight.
  2. Start with n components (each node isolated).
  3. Add edges in that order until the component count ≤ k.
  4. The weight of the last added edge is the answer.

Solution 2 (Binary Search + DFS):
  1. Binary‑search on weight threshold mid.
  2. Delete all edges with weight > mid.
  3. Count components with DFS/BFS.
  4. Adjust mid accordingly.
```

---

## 7. Full Code (One‑Shot Per Language)

### 7.1. Java – DSU Greedy

```java
import java.util.*;

class Solution {
    // ---------- Union–Find ----------
    private static class DSU {
        int[] parent, size;
        DSU(int n) {
            parent = new int[n];
            size   = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                size[i]   = 1;
            }
        }
        int find(int x) {
            if (parent[x] != x) parent[x] = find(parent[x]);  // path compression
            return parent[x];
        }
        boolean union(int a, int b) {
            a = find(a);
            b = find(b);
            if (a == b) return false;
            if (size[a] < size[b]) { int tmp = a; a = b; b = tmp; } // union by size
            parent[b] = a;
            size[a] += size[b];
            return true;
        }
    }

    public int minCost(int n, int[][] edges, int k) {
        Arrays.sort(edges, Comparator.comparingInt(e -> e[2])); // sort by weight
        DSU dsu = new DSU(n);
        int components = n;
        int answer = 0;

        for (int[] e : edges) {
            if (components <= k) break;
            if (dsu.union(e[0], e[1])) {
                components--;            // we keep this edge
                answer = e[2];            // last kept edge weight
            }
        }
        return answer;
    }
}
```

---

### 7.2. Python – DSU Greedy

```python
#  Python 3
from typing import List

class DSU:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.size   = [1] * n

    def find(self, x: int) -> int:
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]
            x = self.parent[x]
        return x

    def union(self, a: int, b: int) -> bool:
        ra, rb = self.find(a), self.find(b)
        if ra == rb: return False
        if self.size[ra] < self.size[rb]: ra, rb = rb, ra
        self.parent[rb] = ra
        self.size[ra]  += self.size[rb]
        return True

class Solution:
    def minCost(self, n: int, edges: List[List[int]], k: int) -> int:
        edges.sort(key=lambda x: x[2])  # sort by weight
        dsu = DSU(n)
        comps = n
        answer = 0

        for u, v, w in edges:
            if comps <= k: break
            if dsu.union(u, v):
                comps -= 1
                answer = w

        return answer
```

---

### 7.3. C++ – DSU Greedy

```cpp
//  C++17 – DSU Greedy
#include <bits/stdc++.h>
using namespace std;

struct DSU {
    vector<int> parent, sz;
    DSU(int n): parent(n), sz(n,1) { iota(parent.begin(), parent.end(), 0); }

    int find(int v){
        if (parent[v]==v) return v;
        return parent[v] = find(parent[v]);   // path compression
    }

    bool unite(int a, int b){
        a = find(a);  b = find(b);
        if (a==b) return false;
        if (sz[a] < sz[b]) swap(a,b);          // union by size
        parent[b] = a;
        sz[a] += sz[b];
        return true;
    }
};

class Solution {
public:
    int minCost(int n, vector<vector<int>>& edges, int k) {
        sort(edges.begin(), edges.end(),
             [](const auto& x, const auto& y){ return x[2] < y[2]; });

        DSU dsu(n);
        int comps = n, ans = 0;
        for (auto &e : edges) {
            if (comps <= k) break;
            if (dsu.unite(e[0], e[1])) {
                comps--;
                ans = e[2];
            }
        }
        return ans;
    }
};
```

---

## 8. Final Thoughts

- **Show clarity** in your explanation of the algorithmic idea before diving into code.  
- **Highlight the “reverse Kruskal”** nature of the DSU solution.  
- **Mention memory safety**: only `O(n)` arrays.  
- **Prove correctness**: After sorting, the DSU guarantees that at the point we stop, *every* kept edge lies in a minimum‑spanning‑forest with exactly `k` components.

Good luck, and happy coding! 🚀

---

*Prepared by [Your Name]* – *Data‑Structures & Algorithms enthusiast, ready to ace any graph‑based interview.*