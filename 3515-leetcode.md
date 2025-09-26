---
title: LeetCode 3515. Shortest Path in a Weighted Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌  Leetcode 3515 – Shortest Path in a Weighted Tree  
> **Java | Python | C++ – Full Code + Interview‑Style Blog**

---

### 1️⃣ Problem Recap  

You’re given a weighted tree rooted at node 1.  
Two types of queries arrive online:

| Query | Meaning |
|-------|---------|
| `[1, u, v, w]` | **Update** the weight of the edge `(u, v)` to `w`. The pair is guaranteed to be an existing edge. |
| `[2, x]` | Return the **shortest distance** from the root (node 1) to node `x`. |

`n, q ≤ 10⁵`, edge weights `≤ 10⁴`.  
The task is to answer all `[2, x]` queries efficiently while supporting edge updates.

---

### 2️⃣ Naïve Solution (Why It Won’t Pass)

* For every `[1, …]` change rebuild all distances with DFS/BFS – **O(n)** per update.  
* For every `[2, …]` read the pre‑computed distance – **O(1)**.  

With up to `10⁵` updates this becomes `O(n·q)` ≈ `10¹⁰`, far beyond the time limit.

---

### 3️⃣ The Elegant Idea – Euler Tour + Fenwick Tree  

1. **DFS from the root**  
   * Compute the initial distance `dist[v]` from node 1 to every node `v`.  
   * Record entry (`tin[v]`) and exit (`tout[v]`) times – the classic **Euler tour**.  
   * For a rooted tree the entire subtree of `v` occupies the continuous segment `[tin[v], tout[v]]`.

2. **Map each edge to the child**  
   * For an edge `(u, v)` the node with larger `tin` is the child.  
   * Keep the current weight in a map `edgeWeight[(parent, child)]`.

3. **Fenwick (Binary Indexed) Tree**  
   * We need *range add* (update whole subtree) and *point query* (distance to a node).  
   * A Fenwick tree that supports range updates with two point updates works in `O(log n)`.

4. **Processing a query**  

   * **Update `[1, u, v, w]`**  
     * Identify the child (`c`).  
     * `delta = w - oldWeight`.  
     * Apply `delta` to the segment `[tin[c], tout[c]]` via `rangeAdd`.  
     * Store `newWeight` in the map.

   * **Distance `[2, x]`**  
     * Answer = `dist[x] + fenwick.pointQuery(tin[x])`.  
     * `dist[x]` is the baseline distance, `fenwick` contains all accumulated deltas.

**Complexities**  

| Operation | Time | Space |
|-----------|------|-------|
| DFS (pre‑process) | `O(n)` | `O(n)` |
| Each query | `O(log n)` | `O(1)` |

That’s fast enough for `10⁵` nodes and queries.

---

## 🧑‍💻 Code Walkthrough  

Below are clean, ready‑to‑paste implementations in **Java**, **Python**, and **C++** following the Leetcode style.

---

### 🟨 Java Solution  

```java
import java.util.*;
import java.io.*;

public class Solution {
    /* ---------- Fenwick Tree (1‑based) ---------- */
    private static class Fenwick {
        private final long[] bit;
        private final int n;
        Fenwick(int n) { this.n = n; bit = new long[n + 2]; }
        void add(int idx, long val) {
            for (int i = idx; i <= n; i += i & -i) bit[i] += val;
        }
        long sum(int idx) {
            long res = 0;
            for (int i = idx; i > 0; i -= i & -i) res += bit[i];
            return res;
        }
        /* range add: add val to [l, r] */
        void rangeAdd(int l, int r, long val) {
            add(l, val);
            add(r + 1, -val);
        }
    }

    /* ---------- Edge key helper ---------- */
    private static long key(int a, int b) {
        return ((long) a << 32) | (b & 0xffffffffL);
    }

    /* ---------- Main solution ---------- */
    public List<Integer> treeQueries(int n, List<List<Integer>> edges,
                                     List<List<Integer>> queries) {
        /* adjacency list */
        List<List<int[]>> g = new ArrayList<>();
        for (int i = 0; i <= n; i++) g.add(new ArrayList<>());
        for (List<Integer> e : edges) {
            int u = e.get(0), v = e.get(1), w = e.get(2);
            g.get(u).add(new int[]{v, w});
            g.get(v).add(new int[]{u, w});
        }

        /* arrays */
        long[] dist = new long[n + 1];
        int[] tin = new int[n + 1];
        int[] tout = new int[n + 1];
        long[] timer = new long[1];          // to emulate mutable integer
        Map<Long, Integer> weightMap = new HashMap<>();

        /* DFS to compute dist, tin/tout and child weights */
        dfs(1, 0, 0, g, dist, tin, tout, timer, weightMap);

        Fenwick fenwick = new Fenwick(n);
        List<Integer> ans = new ArrayList<>();

        for (List<Integer> q : queries) {
            if (q.get(0) == 1) {                // UPDATE
                int u = q.get(1), v = q.get(2), w = q.get(3);
                int parent, child;
                if (tin[u] < tin[v]) { parent = u; child = v; }
                else                 { parent = v; child = u; }

                long old = weightMap.get(key(parent, child));
                long delta = (long) w - old;
                fenwick.rangeAdd(tin[child], tout[child], delta);
                weightMap.put(key(parent, child), w);
            } else {                            // DISTANCE
                int x = q.get(1);
                long cur = dist[x] + fenwick.sum(tin[x]);
                ans.add((int) cur);            // fits in int under constraints
            }
        }
        return ans;
    }

    /* ---------- DFS implementation ---------- */
    private void dfs(int u, int parent, int depth, List<List<int[]>> g,
                     long[] dist, int[] tin, int[] tout, long[] timer,
                     Map<Long, Integer> weightMap) {
        dist[u] = depth;
        tin[u] = (int) timer[0] + 1;           // 1‑based index
        timer[0]++;

        for (int[] nb : g.get(u)) {
            int v = nb[0], w = nb[1];
            if (v == parent) continue;
            weightMap.put(key(u, v), w);      // store parent→child weight
            dfs(v, u, depth + w, g, dist, tin, tout, timer, weightMap);
        }
        tout[u] = (int) timer[0];
    }
}
```

**Key Points**

* `long` everywhere – avoids overflow if you decide to relax constraints.  
* The edge key packs two `int`s into a `long` for a fast `HashMap`.  
* `Fenwick.rangeAdd` is a classic *difference array* trick.  

---

### 🟪 Python Solution  

```python
import sys
from collections import defaultdict
from typing import List

# ---------- Fenwick tree for range add + point query ----------
class Fenwick:
    def __init__(self, n: int):
        self.n = n
        self.bit = [0] * (n + 2)

    def _add(self, i: int, delta: int) -> None:
        while i <= self.n:
            self.bit[i] += delta
            i += i & -i

    def range_add(self, l: int, r: int, delta: int) -> None:
        self._add(l, delta)
        self._add(r + 1, -delta)

    def point_query(self, i: int) -> int:
        s = 0
        while i > 0:
            s += self.bit[i]
            i -= i & -i
        return s


class Solution:
    def treeQueries(self, n: int,
                    edges: List[List[int]],
                    queries: List[List[int]]) -> List[int]:
        # adjacency list
        g = [[] for _ in range(n + 1)]
        for u, v, w in edges:
            g[u].append((v, w))
            g[v].append((u, w))

        dist = [0] * (n + 1)
        tin = [0] * (n + 1)
        tout = [0] * (n + 1)
        timer = 1

        # map: (parent, child) -> weight
        edge_w = {}

        def dfs(u: int, p: int, d: int):
            nonlocal timer
            dist[u] = d
            tin[u] = timer
            timer += 1
            for v, w in g[u]:
                if v == p: continue
                edge_w[(u, v)] = w          # store parent→child weight
                dfs(v, u, d + w)
            tout[u] = timer - 1

        dfs(1, 0, 0)

        bit = Fenwick(n)
        ans = []

        for q in queries:
            if q[0] == 1:                    # update
                _, u, v, new_w = q
                child = v if tin[u] < tin[v] else u
                parent = u if child == v else v
                old = edge_w[(parent, child)]
                delta = new_w - old
                bit.range_add(tin[child], tout[child], delta)
                edge_w[(parent, child)] = new_w
            else:                            # distance
                _, x = q
                cur = dist[x] + bit.point_query(tin[x])
                ans.append(cur)

        return ans
```

**Why it’s fast**

* `timer` is 1‑based so Fenwick indices line up with Euler entry times.  
* `edge_w` keeps the *exact* child mapping – no repeated DFS.  

---

### 🟪 C++ Solution  

```cpp
#include <bits/stdc++.h>
using namespace std;

class Fenwick {
    vector<long long> bit;
    int n;
public:
    Fenwick(int n=0){ init(n); }
    void init(int n_) { n = n_; bit.assign(n+2, 0); }
    void add(int idx, long long val){
        for(int i=idx;i<=n;i+=i&-i) bit[i] += val;
    }
    long long sum(int idx){
        long long res=0;
        for(int i=idx;i>0;i-=i&-i) res+=bit[i];
        return res;
    }
    // range add [l,r] += val
    void rangeAdd(int l,int r,long long val){
        add(l,val); add(r+1,-val);
    }
};

static inline long long key(int a,int b){
    return (static_cast<long long>(a)<<32) | (b & 0xffffffffLL);
}

class Solution {
public:
    vector<int> treeQueries(int n, vector<vector<int>>& edges,
                            vector<vector<int>>& queries) {
        /* adjacency */
        vector<vector<pair<int,int>>> g(n+1);
        for(auto &e: edges){
            int u=e[0],v=e[1],w=e[2];
            g[u].push_back({v,w});
            g[v].push_back({u,w});
        }

        vector<long long> dist(n+1,0);
        vector<int> tin(n+1), tout(n+1);
        int timer=0;

        unordered_map<long long,int> edgeW;   // parent->child weight

        function<void(int,int,long long)> dfs = [&](int u,int p,long long d){
            dist[u]=d;
            tin[u]=++timer;
            for(auto [v,w]: g[u]){
                if(v==p) continue;
                edgeW[key(u,v)] = w;
                dfs(v,u,d+w);
            }
            tout[u]=timer;
        };
        dfs(1,0,0);

        Fenwick fw(n);
        vector<int> ans;
        for(auto &q: queries){
            if(q[0]==1){                     // update
                int _,u,v,newW; tie(_,u,v,newW)=make_tuple(q[0],q[1],q[2],q[3]);
                int child = (tin[u] < tin[v])? v : u;
                int parent = (child==v)? u : v;
                long long oldW = edgeW[key(parent,child)];
                long long delta = newW - oldW;
                fw.rangeAdd(tin[child], tout[child], delta);
                edgeW[key(parent,child)] = newW;
            }else{                           // distance
                int _,x; tie(_,x)=make_tuple(q[0],q[1]);
                long long cur = dist[x] + fw.sum(tin[x]);
                ans.push_back((int)cur);
            }
        }
        return ans;
    }
};
```

> **Note:**  
> * The solution uses `long long` for internal sums to avoid overflow when the job‑interview environment changes constraints.  
> * `key()` packs a 32‑bit pair into a 64‑bit key – perfect for an `unordered_map`.

---

## 🚀 How to Present This Solution in an Interview  

1. **Show the tree is *static* in structure** – you can preprocess once.  
2. **Explain the Euler tour** – all subtrees become contiguous segments.  
3. **Explain the Fenwick trick** – range add via two point updates; query is just a prefix sum.  
4. **Stress the `O(log n)` per query** – meets the constraints.  
5. **Mention pitfalls** – e.g., forgetting to update the map, or using 0‑based indices with a 1‑based Fenwick tree.

---

## 🔍 Common Pitfalls & “Ugly” Quirks

| Problem | Typical Mistake | Fix |
|---------|-----------------|-----|
| **Edge direction** | Treat `(u, v)` and `(v, u)` as the same child → parent. | Always determine the child by comparing `tin[u]` and `tin[v]`. |
| **Fenwick indices** | Off‑by‑one errors (using 0 instead of 1). | Use `++timer` for entry time; `fw.sum(tin[x])`. |
| **Overflow** | Using `int` for sums → negative results on huge graphs. | Use `long long` or `long` everywhere. |
| **Map key collisions** | Using `unordered_map<int, int>` on pair → hash collision. | Pack pair into `long long` or use `map<pair<int,int>,int>`. |
| **Recursion depth** | Stack overflow for deep trees. | Convert DFS to iterative stack or increase recursion limit. |

---

## 📌 Takeaway

* **Preprocessing + difference array + Euler tour = elegant and efficient.**  
* The method is **language‑agnostic** – you can adapt it to Java, Go, Rust, etc.  
* In a job‑interview, always *justify* each design choice and **anticipate the interviewer’s questions** about complexity, memory, and edge cases.  

Good luck! 🎯