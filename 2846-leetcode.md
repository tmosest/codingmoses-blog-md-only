---
title: LeetCode 2846. Minimum Edge Weight Equilibrium Queries in a Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap

**LeetCode 2846 – Minimum Edge Weight Equilibrium Queries in a Tree**

You’re given a rooted tree with `n ≤ 10⁴` nodes.  
Each edge has an integer weight `1 … 26`.  
`m ≤ 2·10⁴` independent queries are asked:  

```
(ai , bi)  –  make all edges on the path ai↔bi equal
```

In one operation you may change the weight of **any** edge to any value.  
For every query return the minimum number of operations needed.

> **Answer** = length of the path – maximum frequency of a weight on that path.



--------------------------------------------------------------------

## 2.  Intuition

* The path between two nodes can be split into two monotone chains  
  (`a → LCA(a,b)` and `b → LCA(a,b)`).

* The only thing that matters is **how many times each weight occurs** on that path.  
  If a weight `z` occurs `k` times, we can keep those `k` edges and change the rest  
  → `len – k` operations.

* The range of weights is tiny (1 … 26).  
  We can pre‑compute, for every node, how many edges of each weight appear on the path
  from the root to that node.

* With these prefix counts we can query *any* node pair in `O(1)` for each weight.

* We still need an efficient LCA routine – binary lifting is the classic choice for
  `O(log n)` per query.



--------------------------------------------------------------------

## 3.  Algorithm

| Step | What we build | Why it’s useful |
|------|---------------|----------------|
| **Adjacency list** | `g[u] = [(v, w)]` | DFS traversal |
| **DFS from root (0)** | `depth[u]`, `parent[0][u]` (the 2⁰‑th ancestor), `cnt[u][w]` | We copy the parent’s counter, bump the edge weight, then recurse |
| **Binary lifting table** | `parent[k][u] = 2ᵏ‑th ancestor` | Classic LCA preprocessing in `O(n log n)` |
| **LCA(a,b)** | Bring the deeper node up, then lift both together until parents match | `O(log n)` |
| **Answer for a query** | `len = depth[a] + depth[b] – 2·depth[l]`  <br> `max_z = max_{1..26}(cnt[a][z] + cnt[b][z] – 2·cnt[l][z])` | `len – max_z` |


--------------------------------------------------------------------

## 3.1  Prefix counters for every weight

Let `cnt[u][w]` be the number of edges of weight `w` on the path from the root to `u`.  
During DFS we do:

```
cnt[child] = cnt[parent]          // copy
cnt[child][edgeWeight] += 1       // the edge we just crossed
```

Because `n` is only 10⁴ and we have 27 possible weights,  
`cnt` can be stored as an `int[27]` for each node  
→ 27 × 10⁴ ≈ 270 kB per language – trivial.



--------------------------------------------------------------------

## 3.2  LCA by Binary Lifting

For `LOG = ⌊log₂ n⌋ + 1`:

```
parent[0][v]  –  direct parent
parent[k][v]  –  2ᵏ‑th ancestor of v
```

Pre‑compute the table in `O(n log n)`.

The `lca(x,y)` routine:

1. Bring `y` up to `x`’s depth.  
2. Lift `x` and `y` simultaneously from the highest jump downwards.  
   The first moment their parents differ is just below the LCA.  
3. Return the parent of either node.

All steps are standard; see the code below.



--------------------------------------------------------------------

## 4.  Pseudocode

```
build adjacency list g
dfs(0, 0, 0):
    parent[0][u] = p
    depth[u]    = d
    cnt[u]      = cnt[p]          // copy parent’s array
    if p != 0:   cnt[u][weight_of_edge(u,p)] += 1

pre‑compute parent[k][u] for k > 0

function lca(x, y):
    if depth[x] > depth[y]  swap
    lift y up by depth diff
    for k from LOG-1 downto 0:
        if parent[k][x] != parent[k][y]:
            x = parent[k][x]
            y = parent[k][y]
    return (x==y) ? x : parent[0][x]

answer = vector<int> res
for each query (a,b):
    l = lca(a,b)
    len = depth[a] + depth[b] - 2*depth[l]
    maxfreq = 0
    for w in 1..26:
        cntw = cnt[a][w] + cnt[b][w] - 2*cnt[l][w]
        maxfreq = max(maxfreq, cntw)
    res.push_back(len - maxfreq)
return res
```

--------------------------------------------------------------------

## 5.  Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| DFS + prefix counter | `O(n)` | `O(n)` (the counter arrays) |
| Binary lifting build | `O(n log n)` | `O(n log n)` |
| Query processing | `O(m · 26)` (the weight loop) | `O(1)` per query |

With the constraints (`n = 10⁴`, `m = 2·10⁴`) this is far below the limits.

--------------------------------------------------------------------

## 6.  Reference Implementations

Below are clean, self‑contained solutions in **Java**, **Python** and **C++**.  
All of them use the same idea – binary lifting + per‑weight prefix counts –  
but the syntax is adapted to the language idioms.

> **Tip:**  
> In interviews you can first present the idea in words, then sketch a sketch of the DFS + LCA table, and finally show the code.  
> This demonstrates clear thinking and an organized approach.



--------------------------------------------------------------------

### 6.1  Java 17

```java
import java.util.*;
import java.io.*;

public class Solution {
    // constants
    private static final int MAX_W = 27;   // weights are 1 … 26

    public List<Integer> minOperationsQueries(int n,
            List<List<Integer>> edges, List<List<Integer>> queries) {

        // ---------- 1. Build graph ----------
        List<List<int[]>> g = new ArrayList<>(n);
        for (int i = 0; i < n; ++i) g.add(new ArrayList<>());
        for (List<Integer> e : edges) {
            int u = e.get(0), v = e.get(1), w = e.get(2);
            g.get(u).add(new int[]{v, w});
            g.get(v).add(new int[]{u, w});
        }

        // ---------- 2. DFS – depth, parent[0], prefix counts ----------
        int LOG = 17;                     // ceil(log2 10^4) + 1
        int[][] parent = new int[LOG][n];
        int[][] depth  = new int[n][1];
        int[][] pref   = new int[n][MAX_W];  // pref[u][w] = #edges weight w from root to u

        int[] stack = new int[n];
        int[] ptr   = new int[n];
        int sp = 0;
        stack[sp] = 0;          // root
        parent[0][0] = 0;
        depth[0][0] = 0;

        while (sp >= 0) {
            int u = stack[sp];
            if (ptr[u] == 0) {          // first time visiting u
                // copy parent's pref and increment current edge weight
                if (u != 0) {
                    System.arraycopy(pref[parent[0][u]], 0, pref[u], 0, MAX_W);
                    int w = edgeWeight(parent[0][u], u, g);
                    pref[u][w]++;
                }
            }

            if (ptr[u] < g.get(u).size()) {
                int[] nxt = g.get(u).get(ptr[u]++);
                int v = nxt[0], w = nxt[1];
                if (v == parent[0][u]) continue;
                parent[0][v] = u;
                depth[v][0]  = depth[u][0] + 1;
                stack[++sp] = v;
                ptr[v] = 0;
            } else {
                sp--;   // backtrack
            }
        }

        // ---------- 3. Build binary lifting table ----------
        for (int k = 1; k < LOG; ++k) {
            for (int v = 0; v < n; ++v) {
                parent[k][v] = parent[k-1][ parent[k-1][v] ];
            }
        }

        // ---------- 4. LCA helper ----------
        int lca(int a, int b) {
            if (depth[a][0] > depth[b][0]) { int t = a; a = b; b = t; }
            int diff = depth[b][0] - depth[a][0];
            for (int k = 0; k < LOG; ++k) {
                if (((diff >> k) & 1) == 1) b = parent[k][b];
            }
            if (a == b) return a;
            for (int k = LOG-1; k >= 0; --k) {
                if (parent[k][a] != parent[k][b]) {
                    a = parent[k][a];
                    b = parent[k][b];
                }
            }
            return parent[0][a];
        }

        // ---------- 5. Process queries ----------
        List<Integer> ans = new ArrayList<>(queries.size());
        for (List<Integer> q : queries) {
            int a = q.get(0), b = q.get(1);
            int l = lca(a,b);
            int len = depth[a][0] + depth[b][0] - 2*depth[l][0];
            int best = 0;
            for (int w = 1; w < MAX_W; ++w) {
                int cnt = pref[a][w] + pref[b][w] - 2*pref[l][w];
                if (cnt > best) best = cnt;
            }
            ans.add(len - best);
        }
        return ans;
    }

    // helper to get weight of edge (u,v)
    private int edgeWeight(int u, int v, List<List<int[]>> g) {
        for (int[] e : g.get(u)) if (e[0] == v) return e[1];
        return 0;
    }
}
```

> **Note:**  
> The helper `edgeWeight` is trivial but necessary because DFS copies parent’s pref.  
> In real code you could store the weight in an additional array.



--------------------------------------------------------------------

### 6.2  Python 3.10

```python
import sys
import math
from typing import List

MAX_W = 27   # weights 1..26

class Solution:
    def minOperationsQueries(
            self,
            n: int,
            edges: List[List[int]],
            queries: List[List[int]]) -> List[int]:

        # 1. Build adjacency
        g = [[] for _ in range(n)]
        for u, v, w in edges:
            g[u].append((v, w))
            g[v].append((u, w))

        # 2. DFS – depth, parent[0], prefix counters
        LOG = (n-1).bit_length() + 1
        parent = [[0]*n for _ in range(LOG)]
        depth  = [0]*n
        pref   = [[0]*MAX_W for _ in range(n)]   # pref[u][w]

        stack = [(0, 0, 0)]      # node, parent, edgeWeightFromParent
        while stack:
            u, p, w = stack.pop()
            if u != 0:
                pref[u] = pref[p].copy()
                pref[u][w] += 1
            parent[0][u] = p
            depth[u] = depth[p] + 1 if u else 0
            for v, ww in g[u]:
                if v == p: continue
                stack.append((v, u, ww))

        # 3. Build binary lifting table
        for k in range(1, LOG):
            for v in range(n):
                parent[k][v] = parent[k-1][parent[k-1][v]]

        # 4. LCA
        def lca(a: int, b: int) -> int:
            if depth[a] > depth[b]:
                a, b = b, a
            diff = depth[b] - depth[a]
            k = 0
            while diff:
                if diff & 1:
                    b = parent[k][b]
                diff >>= 1
                k += 1
            if a == b:
                return a
            for k in range(LOG-1, -1, -1):
                if parent[k][a] != parent[k][b]:
                    a = parent[k][a]
                    b = parent[k][b]
            return parent[0][a]

        # 5. Process
        res = []
        for a, b in queries:
            l = lca(a,b)
            length = depth[a] + depth[b] - 2*depth[l]
            best = 0
            for w in range(1, MAX_W):
                cnt = pref[a][w] + pref[b][w] - 2*pref[l][w]
                if cnt > best:
                    best = cnt
            res.append(length - best)
        return res
```

*The code uses lists of lists for simplicity;  
the recursion depth is avoided by iterative DFS.*



--------------------------------------------------------------------

### 6.3  C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // ----------------------  constants ----------------------
    static const int MAX_W = 27;    // weights 1 .. 26

    vector<int> minOperationsQueries(int n,
          const vector<vector<int>>& edges,
          const vector<vector<int>>& queries) {

        // ---------- 1. Graph ----------
        vector<vector<pair<int,int>>> g(n);
        for (auto &e : edges) {
            int u=e[0], v=e[1], w=e[2];
            g[u].push_back({v,w});
            g[v].push_back({u,w});
        }

        // ---------- 2. DFS: depth, parent[0], pref ----------
        int LOG = 17;                     // ceil(log2 10^4)+1
        vector<vector<int>> parent(LOG, vector<int>(n,0));
        vector<int> depth(n,0);
        vector<array<int,MAX_W>> pref(n);
        pref[0].fill(0);                  // root

        // iterative stack to avoid recursion limits
        vector<int> st, it, par;
        st.reserve(n);  it.assign(n,0);   // pointer to adjacency list
        st.push_back(0);                 // root

        while (!st.empty()) {
            int u = st.back();
            if (it[u]==0) {   // first time
                if (u!=0) {
                    pref[u] = pref[parent[0][u]];   // copy
                    pref[u][ edgeWeight(parent[0][u], u, g) ]++;
                }
            }

            if (it[u] < (int)g[u].size()) {
                auto [v,w] = g[u][it[u]++];
                if (v==parent[0][u]) continue;
                parent[0][v] = u;
                depth[v] = depth[u] + 1;
                st.push_back(v);
            } else {
                st.pop_back();
            }
        }

        // ---------- 3. Build binary lifting ----------
        for (int k=1;k<LOG;k++)
            for (int v=0;v<n;v++)
                parent[k][v] = parent[k-1][ parent[k-1][v] ];

        // ---------- 4. LCA ----------
        auto lca = [&](int a, int b)->int{
            if (depth[a] > depth[b]) swap(a,b);
            int diff = depth[b]-depth[a];
            for (int k=0; diff; ++k, diff>>=1)
                if (diff&1) b = parent[k][b];
            if (a==b) return a;
            for (int k=LOG-1;k>=0;--k)
                if (parent[k][a]!=parent[k][b]) {
                    a=parent[k][a];
                    b=parent[k][b];
                }
            return parent[0][a];
        };

        // ---------- 5. Queries ----------
        vector<int> ans;
        ans.reserve(queries.size());
        for (auto &q: queries) {
            int a=q[0], b=q[1];
            int l = lca(a,b);
            int len = depth[a]+depth[b]-2*depth[l];
            int best=0;
            for (int w=1; w<MAX_W; ++w) {
                int cnt = pref[a][w] + pref[b][w] - 2*pref[l][w];
                best = max(best, cnt);
            }
            ans.push_back(len - best);
        }
        return ans;
    }

private:
    // helper to return the weight of the edge (u,p) in graph g
    int edgeWeight(int p, int u,
        const vector<vector<pair<int,int>>>& g) const {
        for (auto &e: g[p])
            if (e.first==u) return e.second;
        return 0;   // root
    }
};
```

> **Why `edgeWeight`?**  
> During DFS we need the weight of the edge we just traversed.  
> Since the graph is small, a linear search is fine.



--------------------------------------------------------------------

## 7.  Your Next Step

1. **Practice the DFS + prefix counter** on a small hand‑written example.  
   Write out `cnt[u][w]` for a few nodes.  
2. **Show the binary lifting table** for a tiny tree (e.g. 7 nodes).  
3. **Explain how the query loop uses the counters** to compute `cntw`.  
4. Finally, present one of the clean reference codes – the interviewer will appreciate the clarity.



--------------------------------------------------------------------

## 8.  Concluding Remarks

- **Conceptually simple:** LCA + counters – easy to explain.  
- **Efficient and robust:** Works for all constraints and is straightforward to implement.  
- **Interview‑friendly:** Shows algorithmic maturity and careful planning.  

Give this solution a try on LeetCode, and you’ll be ready for both the coding and the explanation phase of the interview!