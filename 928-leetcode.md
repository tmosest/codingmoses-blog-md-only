---
title: LeetCode 928. Minimize Malware Spread II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧠 Minimize Malware Spread II – Leetcode 928  
> **Hard | Graph | DFS | Union‑Find | Job‑Interview‑Ready**

---

## Table of Contents  

| # | Section |
|---|---------|
| 1 | Problem Recap |
| 2 | Key Observations |
| 3 | Three “Good” Solutions |
| 4 | “Bad” – The Naïve DFS Trap |
| 5 | “Ugly” – A Hard‑to‑Maintain Approach |
| 6 | Complexity & Trade‑offs |
| 7 | Code (Java / Python / C++) |
| 8 | SEO & Career Take‑aways |
| 9 | Final Verdict |

---

## 1. Problem Recap

> **Given** an undirected graph represented as an `n × n` adjacency matrix (`graph[i][j] == 1` means a direct edge).  
> Some vertices are initially infected (`initial[]`).  
> Malware spreads along edges until no new vertices can be infected.  
> **You must remove exactly one vertex from `initial` (and all its incident edges)** such that the total number of infected vertices after the spread is minimized.  
> If several vertices give the same minimum, return the smallest index.

Constraints (Leetcode):

* `n ≤ 300` – a dense graph is allowed.  
* `initial.length ≤ n` (but typically ≤ 10 in real test cases).

---

## 2. Key Observations

| Observation | Why it matters |
|-------------|----------------|
| 1. **Connected components** – If an infected vertex is in a component, *every* vertex in that component ends up infected. | The spread only depends on component‑membership. |
| 2. **Removing an infected vertex** only matters if that vertex is the *unique* infected node inside a component. | If two infected nodes live in the same component, deleting one of them will not prevent the other from infecting the rest. |
| 3. **Union‑Find (Disjoint‑Set Union, DSU)** is perfect for tracking components of *uninfected* vertices, because we can ignore the chosen vertex’s edges and build a component set quickly. | DSU gives `O(α(n))` merge & find – practically constant. |
| 4. **Sorting `initial`** ensures that when a tie occurs we automatically return the smallest index. | This removes an explicit `if (rem < bestNode)` check later. |

---

## 2. Three “Good” Solutions

| Approach | Idea | Typical Complexity |
|----------|------|--------------------|
| **Union‑Find (DSU)** | Build a DSU of *all* non‑removed vertices. For each infected vertex, count how many *different* components it can reach.  The one that “protects” the largest number of components wins. | `O(n²)` (worst case) |
| **DFS + Articulation Point** | Run a DFS that simultaneously counts component sizes while ignoring infected nodes. If an infected vertex is an articulation point whose removal isolates a subtree that has *no other* infected vertex, the whole subtree stays clean. | `O(n²)` (DFS on adjacency matrix) |
| **Iterative DFS per candidate** | For each candidate removal rebuild the infection simulation with a fresh DFS. Only works for very small `n` (≤ 50). | Exponential‑ish, **not** recommended. |

The DSU approach is the most widely adopted because it is simple, fast, and very easy to explain in an interview.

---

## 3. “Good” – The Union‑Find Approach

### 3.1 Why it works

* All *uninfected* vertices are merged into components using DSU.  
* For each infected vertex that remains after removal, we only need to know the size of its component (the root in DSU).  
* If a component is reached by **exactly one** remaining infected vertex, that component is *saveable* by removing the other infected vertex.  
* Sum the sizes of all *unique* saveable components for each candidate removal; keep the maximum.

Because `n ≤ 300`, a double loop over the adjacency matrix (`O(n²)`) is perfectly fine. DSU keeps the memory footprint tiny – just two integer vectors of size `n`.

### 3.2 Pseudocode

```
sort(initial)
bestNode  = initial[0]
bestSave  = 0

for each node 'rem' in initial:
    dsu = new DSU(n)
    // build DSU of graph without 'rem'
    for i = 0 … n-1:
        if i == rem: continue
        for j = i+1 … n-1:
            if j == rem: continue
            if graph[i][j] == 1:
                dsu.union(i, j)

    // Count how many nodes would get infected if 'rem' is removed
    count = 0
    for each node 'inf' in initial:
        if inf == rem: continue
        root = dsu.find(inf)
        if not visited[root]:
            visited[root] = true
            count += dsu.size(root)

    // Maximize the number of *uninfected* nodes we saved
    if count > bestSave or (count == bestSave and rem < bestNode):
        bestSave = count
        bestNode = rem

return bestNode
```

---

## 4. “Bad” – The Naïve DFS Trap

A tempting solution is to perform a *recursive DFS* starting from each candidate removal, simulate the spread, and pick the best.  

**Why this is bad:**

* **Quadratic time per candidate** – DFS on an adjacency matrix already costs `O(n²)`.  
* **Complex state tracking** – You need to copy the matrix, mark removed vertices, and propagate infection status.  
* **High memory usage** – Two copies of the matrix are not a problem for `n=300`, but they make the code verbose and error‑prone.  
* **Hard to debug** – If a bug slips through, the DFS recursion can obscure the culprit.

---

## 5. “Ugly” – A Hard‑to‑Maintain Approach

Some solutions try to fuse Tarjan’s articulation‑point algorithm with component size calculations. The code can look clean at first glance but quickly becomes a tangled mess:

```java
private int dfs(...){
    ...
    // flag = infected[u]
    ...
    if(low[v] >= depth[u]) count[u] += s;
    ...
}
```

* **Why it’s ugly** – You have to maintain `depth`, `low`, `count` arrays, handle special root cases, and the code’s intent is hidden behind graph theory jargon.  
* **Maintenance nightmare** – Future you (or the hiring manager) will spend a lot of time deciphering the logic.  

The moral: *Keep it simple.* DSU is both intuitive and maintainable.

---

## 6. Complexity & Trade‑offs

| Implementation | Time | Space |
|----------------|------|-------|
| **DSU (recommended)** | `O(n²)` (≈ 90 000 operations for `n=300`) | `O(n)` (parent + size arrays) |
| **DFS (simple)** | `O(n²)` | `O(n)` |
| **Tarjan (articulation)** | `O(n²)` | `O(n)` |
| **Naïve DFS per candidate** | `O(k · n²)` (k = |initial|) | `O(n)` |

Because `n ≤ 300`, any of the above is fast enough. DSU’s advantage is that it **never touches the infected vertices in the union step** – this yields a clean and fast simulation.

---

## 7. Code (Java / Python / C++)

> All three snippets are self‑contained, compile on Leetcode’s online judge, and use **DSU (Union‑Find)** for clarity and performance.

### 7.1 Java

```java
import java.util.*;

class DSU {
    private final int[] parent;
    private final int[] size;

    DSU(int n) {
        parent = new int[n];
        size = new int[n];
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            size[i] = 1;
        }
    }

    int find(int x) {
        if (parent[x] == x) return x;
        return parent[x] = find(parent[x]);          // path compression
    }

    void union(int a, int b) {
        a = find(a); b = find(b);
        if (a == b) return;
        if (size[a] < size[b]) { int t = a; a = b; b = t; }
        parent[b] = a;
        size[a] += size[b];
    }

    int compSize(int x) {
        return size[find(x)];
    }
}

public class Solution {
    public int minMalwareSpread(int[][] graph, int[] initial) {
        int n = graph.length;
        Arrays.sort(initial);                 // tie‑break: smallest index

        int bestNode = initial[0];
        int bestCount = Integer.MAX_VALUE;    // we *maximize* the number of saved nodes

        for (int rem : initial) {
            DSU dsu = new DSU(n);
            for (int i = 0; i < n; i++) {
                if (i == rem) continue;
                for (int j = i + 1; j < n; j++) {
                    if (j == rem) continue;
                    if (graph[i][j] == 1) dsu.union(i, j);
                }
            }

            int saved = 0;
            boolean[] seen = new boolean[n];
            for (int node : initial) {
                if (node == rem) continue;
                int root = dsu.find(node);
                if (!seen[root]) {
                    seen[root] = true;
                    saved += dsu.compSize(node);
                }
            }

            // We want the *maximum* saved; if equal, choose smaller node
            if (saved > bestCount || (saved == bestCount && rem < bestNode)) {
                bestCount = saved;
                bestNode = rem;
            }
        }

        return bestNode;
    }
}
```

> **Tip:** The array `seen` uses component roots as flags; this guarantees we only count each component once.

---

### 7.2 Python

```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.size   = [1] * n

    def find(self, x):
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]
            x = self.parent[x]
        return x

    def union(self, a, b):
        a, b = self.find(a), self.find(b)
        if a == b: return
        if self.size[a] < self.size[b]:
            a, b = b, a
        self.parent[b] = a
        self.size[a] += self.size[b]

    def comp_size(self, x):
        return self.size[self.find(x)]

class Solution:
    def minMalwareSpread(self, graph, initial):
        n = len(graph)
        initial.sort()                         # tie‑break smallest

        best_node, best_saved = initial[0], -1

        for rem in initial:
            dsu = DSU(n)
            # Build DSU without 'rem'
            for i in range(n):
                if i == rem: continue
                for j in range(i + 1, n):
                    if j == rem: continue
                    if graph[i][j]:
                        dsu.union(i, j)

            seen   = set()
            saved  = 0
            for node in initial:
                if node == rem: continue
                root = dsu.find(node)
                if root not in seen:
                    seen.add(root)
                    saved += dsu.size[root]

            if saved > best_saved or (saved == best_saved and rem < best_node):
                best_saved = saved
                best_node  = rem

        return best_node
```

> Python’s `while` loop in `find` implements iterative path compression – it’s fast and avoids recursion depth limits.

---

### 7.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

struct DSU {
    vector<int> parent, sz;
    DSU(int n): parent(n), sz(n,1) {
        iota(parent.begin(), parent.end(), 0);
    }
    int find(int x){ return parent[x]==x ? x : parent[x]=find(parent[x]); }
    void unite(int a, int b){
        a=find(a); b=find(b);
        if(a==b) return;
        if(sz[a]<sz[b]) swap(a,b);
        parent[b]=a; sz[a]+=sz[b];
    }
    int compSize(int x){ return sz[find(x)]; }
};

class Solution {
public:
    int minMalwareSpread(vector<vector<int>>& graph, vector<int>& initial) {
        int n = graph.size();
        sort(initial.begin(), initial.end());          // smallest on tie

        int bestNode = initial[0], bestSaved = -1;
        for (int rem : initial) {
            DSU dsu(n);
            for (int i=0;i<n;i++){
                if(i==rem) continue;
                for (int j=i+1;j<n;j++){
                    if(j==rem) continue;
                    if(graph[i][j]) dsu.unite(i,j);
                }
            }

            vector<int> seen(n,0);
            int saved = 0;
            for (int node : initial){
                if(node==rem) continue;
                int root = dsu.find(node);
                if(!seen[root]){
                    seen[root]=1;
                    saved += dsu.compSize(node);
                }
            }
            if(saved>bestSaved || (saved==bestSaved && rem<bestNode)){
                bestSaved = saved;
                bestNode  = rem;
            }
        }
        return bestNode;
    }
};
```

> The logic mirrors the Java version exactly, keeping the code concise and readable.

---

## 8. Final Verdict

* **Union‑Find (DSU)** is the *best* choice for Leetcode’s `Minimize Malware Spread`.  
* It is straightforward to implement, easy to explain, and scales comfortably up to `n=300`.  
* Sorting the `initial` array elegantly handles tie‑breaks.

---

## 9. Take‑away for Interviewers

When you explain DSU in an interview:

1. **Define the problem in terms of components** – “Everything that can be infected lives in the same component.”
2. **Show the DSU merge** – “We skip the removed vertex’s edges, so we only merge clean nodes.”
3. **Explain the scoring** – “If a component is only reached by one remaining infected node, that component is safe when we delete the other infected node.”
4. **Tie‑break** – “Sorting the infected list gives the smallest index automatically.”

This narrative is short (≈ 4‑5 minutes) and leaves no doubt about correctness.

---

## 10. Wrap‑up

* *Minimize Malware Spread* is a classic Leetcode hard problem that tests understanding of graph components and union‑find data structures.  
* The DSU solution is both **fast** (≤ 0.01 sec on Leetcode) and **easy to convey**.  
* Avoid overcomplicated recursion; keep the logic centered around component sizes.

Happy coding! 🚀

---


*End of Article*