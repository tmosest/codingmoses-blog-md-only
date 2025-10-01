---
title: LeetCode 2421. Number of Good Paths - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  Quick‑Answer (for your application)

**What you need to know**

| Topic | Key Point |
|-------|-----------|
| **Problem** | Count the number of *good paths* in a tree (or undirected graph) where a *good path* is a path that starts and ends at nodes with the *same value* and all intermediate nodes have values **≤** that value. |
| **Optimal Strategy** | **Union‑Find (Disjoint Set Union, DSU)** – process nodes in increasing order of their values, connect each node with all neighbours that have an equal or smaller value, then count how many nodes of the current value fall into the same DSU component. |
| **Why it works** | While processing a value `v`, all edges that connect nodes with value ≤ `v` have already been “activated”. Every component now contains only nodes with value ≤ `v`. Two nodes of value `v` inside the same component can be paired to form a good path, and the number of new paths equals `C(k,2)` where `k` is the number of such nodes in that component. |
| **Time Complexity** | **O(n + m · α(n))** – linear in the number of nodes plus a very small inverse‑Ackermann factor from DSU operations. |
| **Space Complexity** | **O(n + m)** – adjacency list + DSU arrays. |
| **Typical Pitfalls** | 1. Forgetting to restrict edge activation to neighbours with value ≤ current node.<br>2. Not using path‑compression / union‑by‑size, which kills performance on large inputs.<br>3. Counting paths twice – add the initial `n` single‑node paths first, then add pairs for every component. |

---

### Python 3.8+ Implementation (Union‑Find)

```python
from collections import defaultdict
from typing import List

class UnionFind:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.size   = [1] * n           # component sizes

    def find(self, x: int) -> int:
        """Path‑compression find."""
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]
            x = self.parent[x]
        return x

    def union(self, a: int, b: int) -> None:
        """Union by size. Return new root."""
        ra, rb = self.find(a), self.find(b)
        if ra == rb:          # already merged
            return ra
        # make the larger component the root
        if self.size[ra] < self.size[rb]:
            ra, rb = rb, ra
        self.parent[rb] = ra
        self.size[ra] += self.size[rb]
        return ra


class Solution:
    def numberOfGoodPaths(self, vals: List[int], edges: List[List[int]]) -> int:
        n = len(vals)

        # build adjacency list (undirected)
        graph = defaultdict(list)
        for u, v in edges:
            graph[u].append(v)
            graph[v].append(u)

        # group nodes by their value
        val_to_nodes = defaultdict(list)
        for idx, val in enumerate(vals):
            val_to_nodes[val].append(idx)

        uf = UnionFind(n)
        ans = n                      # each single node is a trivial good path

        # process values in ascending order
        for val in sorted(val_to_nodes):
            # 1️⃣  Activate all edges from nodes with value == val
            for node in val_to_nodes[val]:
                for neigh in graph[node]:
                    if vals[neigh] <= val:        # only smaller or equal values
                        uf.union(node, neigh)

            # 2️⃣  Count nodes of this value that end up in the same component
            comp_count = defaultdict(int)
            for node in val_to_nodes[val]:
                comp_count[uf.find(node)] += 1

            # 3️⃣  Add new good paths: choose 2 among k nodes in each component
            for k in comp_count.values():
                if k > 1:
                    ans += k * (k - 1) // 2

        return ans
```

**Explanation of the key steps**

1. **Sorting by value** – ensures that when we process a value `v` we have already merged all edges whose endpoints are ≤ `v`.  
2. **Union** – connects a node of value `v` to any neighbour whose value is ≤ `v`.  
3. **Counting per component** – after all unions for value `v` are done, every component contains only nodes with value ≤ `v`.  
   For each component we count how many nodes have the *exact* value `v`. Any two of them can form a good path, hence `C(k,2)` new paths.

---

### Edge‑Case Checklist

| Edge Case | What to watch out for | How the algorithm handles it |
|-----------|-----------------------|------------------------------|
| **All nodes same value** | Every pair of nodes forms a good path | After processing that value, all nodes belong to one component. `C(n,2)` added. |
| **No edges (m = 0)** | Only trivial single‑node paths | No unions, answer stays `n`. |
| **Negative values** | Works identically; ordering is by numeric value | Python `sorted` handles negatives naturally. |
| **Large graph (n = 10⁵, m ≈ 10⁵)** | Avoid O(n²) DFS; use DSU | DSU gives linear‑time behaviour. |
| **Tree vs general graph** | Problem statement originally says “tree” but many solutions work on any undirected graph | Our DSU logic remains valid as long as the graph is connected. |

---

## 2️⃣  Blog Post – “Good Paths” in a Graph: Why Union‑Find is the Gold Standard

> **Title** – *Counting Good Paths in a Graph: From Naïve DFS to Union‑Find Mastery*  
> **Author** – *Your Name*  
> **Date** – *2024‑03‑xx*  
> **Tags** – #graph #unionfind #disjointset #algorithm #codinginterview

---

### 1.  Problem Recap

Given:

* `vals[0…n−1]` – an integer value assigned to each vertex.  
* `edges` – an undirected edge list, describing a connected graph (often a tree).  

A **good path** is a simple path where:

1. The *start* and *end* vertices have the *same* value.  
2. All vertices along the path have values **≤** that value (i.e., the maximum value on the path equals the value of the endpoints).

*Goal:* Count how many distinct good paths exist.

---

### 2.  Why a Naïve DFS Fails

The most straightforward idea:

```python
for each vertex v:
    dfs(v, start=v)
```

While exploring, we count a path when we encounter another vertex `u` with `vals[u]==vals[v]`.  
However:

* **O(n²)** visits in the worst case (every pair of vertices).  
* Recursion depth can explode → **stack overflow**.  
* Python’s recursion limits and overhead make it *TLE* for the 10⁵‑node limit.

Thus we need a *linear* algorithm.

---

### 3.  The Core Insight

Sort the nodes by their values.  
When we consider a node of value `v`, all nodes with smaller values have already been processed.  
Hence we can “activate” edges that connect our current node to any already‑activated neighbour.

If we connect all such edges, we form **connected components** that contain only nodes with values ≤ `v`.  
Two nodes with the *same* value `v` belong to the same component **iff** a good path between them exists.

So the number of good paths for value `v` is just the number of unordered pairs of nodes of that value inside each component.

---

### 4.  Why Union‑Find Works

* **Disjoint Set Union (DSU)** keeps track of components dynamically.  
* `union(a, b)` merges two components when an edge `(a,b)` becomes *eligible*.  
* `find(x)` tells us the current component of vertex `x`.  
* With *path compression* + *union by size*, each operation is practically O(1).

The algorithm steps become:

1. **Prepare**  
   * Build an adjacency list (`O(n+m)`).  
   * Map each value → list of vertices (`O(n)`).
2. **Process values in ascending order**  
   * For every vertex `u` of the current value:  
     * For each neighbour `w` in adjacency list:  
       * If `vals[w] ≤ vals[u]`, `union(u, w)`.  
   * After all unions for this value, every component contains only values ≤ `current_value`.
3. **Count new paths**  
   * For each component, count how many vertices have the *exact* current value.  
   * Add `C(k, 2)` (i.e., `k * (k-1) // 2`) to the global answer.  
   * Remember to add the `n` trivial single‑node paths at the start.

Because we process each node once and each edge at most twice, the overall complexity is linear.

---

### 4.  Step‑by‑Step Walkthrough (Python)

```python
from collections import defaultdict
from typing import List

class UnionFind:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.size   = [1] * n

    def find(self, x: int) -> int:
        # iterative path‑compression
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]
            x = self.parent[x]
        return x

    def union(self, a: int, b: int) -> None:
        ra, rb = self.find(a), self.find(b)
        if ra == rb: return
        if self.size[ra] < self.size[rb]:
            ra, rb = rb, ra
        self.parent[rb] = ra
        self.size[ra] += self.size[rb]


class Solution:
    def numberOfGoodPaths(self, vals: List[int], edges: List[List[int]]) -> int:
        n = len(vals)

        # adjacency list
        graph = defaultdict(list)
        for u, v in edges:
            graph[u].append(v)
            graph[v].append(u)

        # nodes grouped by value
        value_to_nodes = defaultdict(list)
        for idx, val in enumerate(vals):
            value_to_nodes[val].append(idx)

        uf = UnionFind(n)
        ans = n                      # single‑node good paths

        for val in sorted(value_to_nodes):
            # 1️⃣  Activate edges from all nodes of this value
            for node in value_to_nodes[val]:
                for nb in graph[node]:
                    if vals[nb] <= val:
                        uf.union(node, nb)

            # 2️⃣  Count same‑value nodes inside each component
            comp_counts = defaultdict(int)
            for node in value_to_nodes[val]:
                comp_counts[uf.find(node)] += 1

            # 3️⃣  Add new good paths
            for k in comp_counts.values():
                if k > 1:
                    ans += k * (k - 1) // 2

        return ans
```

---

### 5.  Complexity Analysis

| Step | Cost | Reason |
|------|------|--------|
| Build adjacency list | **O(n + m)** | Standard graph representation. |
| Group nodes by value | **O(n)** | One pass over `vals`. |
| Process each value | **O(n + m · α(n))** | Each edge is examined once; each `union`/`find` is amortised inverse Ackermann. |
| Memory | **O(n + m)** | Adjacency list + DSU arrays. |

**Total:** `O(n + m · α(n))` time, `O(n + m)` space – comfortably within limits for 10⁵ nodes / edges.

---

### 6.  Common Pitfalls & How to Avoid Them

| Pitfall | Why it breaks | Fix |
|---------|---------------|-----|
| **Not limiting neighbour activation** | Activating an edge to a node with a larger value would create a component that violates the “≤ v” rule. | Use `if vals[nb] <= current_val:` before `union`. |
| **Double‑counting paths** | Counting pairs inside each component **and** later when processing the same component again. | Add the initial `n` trivial paths *once*, then only add `C(k,2)` for each component per value. |
| **DSU inefficiency** | Linear‑time relies on both path compression *and* union‑by‑size/rank. | Implement both or use a library that guarantees them. |
| **Stack overflow** | Recursion on large graphs is unsafe. | Prefer iterative approaches or keep recursion depth bounded. |

---

### 7.  Best‑Practice Checklist

1. **Use an iterative DSU** – avoid recursion inside DSU for Python; Python’s recursion depth is limited.  
2. **Path compression** – essential; otherwise you’ll hit near‑quadratic behaviour.  
3. **Union by size/rank** – keeps trees shallow.  
4. **Early exit** – if all nodes have distinct values, answer = `n`.  
5. **Test with edge‑cases first** – small graphs, single‑node, all‑same values, sparse/dense graphs.  
6. **Profile** – If you implement in Java/C++ you’ll see the DSU perform *instantly* even for 10⁶ nodes.  

---

### 8.  Final Thoughts

*The “Good Paths” problem is a textbook example of how a greedy ordering combined with a union‑find structure can transform an exponential‑time DFS into a linear‑time solution.*

- **Ordering** ensures that when we process a value, all cheaper connections are already available.  
- **DSU** guarantees that we only need to know component membership to decide if two equal‑value nodes are connectable.  
- **Counting** is a simple combinatorial step (`C(k,2)`), but you must perform it *after* all relevant unions for that value.

If you’re preparing for a **Software Development Engineer in Test** interview, this problem showcases:

* **Graph fundamentals** (adjacency lists, DFS).  
* **Advanced data structures** (DSU, path‑compression).  
* **Algorithmic thinking** – turning a brute force idea into a production‑ready solution.  

Master it, and you’ll shine both in coding challenges and in real‑world test‑automation scenarios where efficient graph traversal is often required (e.g., dependency resolution, network reachability checks, etc.).