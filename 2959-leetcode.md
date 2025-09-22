---
title: LeetCode 2959. Number of Possible Sets of Closing Branches - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Number of Possible Sets of Closing Branches – The Good, The Bad, and the Ugly  
## A Deep‑Dive LeetCode Hard Solution in Java, Python & C++  

> **SEO Keywords**: LeetCode, Number of Possible Sets of Closing Branches, algorithm, Floyd‑Warshall, backtracking, graph theory, Java, Python, C++, interview, coding challenge, solution

---

## 1️⃣ Introduction  

When I first saw LeetCode **2959 – “Number of Possible Sets of Closing Branches”**, my eyes widened. The statement looks simple, but the underlying combinatorics + shortest‑path twist makes it a *hard* problem.  

The goal:  
> Count all subsets of closed branches such that every remaining branch can reach every other within a maximum allowed distance (`maxDistance`).  

The constraints (`n ≤ 10`) hint that a brute‑force over subsets is viable, but we still need an efficient shortest‑path check for each subset.  

Below I’ll walk through the *good*, the *bad*, and the *ugly* parts of tackling this problem, and then present clean, production‑ready code for **Java, Python, and C++**.

---

## 2️⃣ Problem Recap

| Item | Description |
|------|-------------|
| **n** | Number of branches (0‑indexed). `1 ≤ n ≤ 10`. |
| **roads** | Undirected weighted edges. Multiple edges allowed. |
| **maxDistance** | All remaining branches must be ≤ this distance apart. |
| **Return** | Count of *valid* closing‑sets (bitmasks) that satisfy the condition. |
| **Edge case** | Empty set of active branches (all closed) is always valid. |

---

## 3️⃣ The Core Challenge

1. **Subset Explosion** – `2ⁿ` possible closing‑sets (`n ≤ 10` → 1024).  
2. **Dynamic Graph** – each subset removes some vertices and incident edges.  
3. **Shortest Path** – we must know the all‑pairs distances on the *remaining* graph quickly.

The most straightforward brute force would run Dijkstra or Floyd‑Warshall *per* subset, costing `O(2ⁿ * (n³))` – still fine for `n=10`, but we can do better by pre‑computing all‑pairs distances once on the full graph and then *filtering* distances for each subset.

---

## 4️⃣ Approach 1 – Full Floyd‑Warshall + Subset Check (Easy but Clear)

1. **Compute full all‑pairs shortest distances** (`dist[i][j]`) on the complete graph using Floyd‑Warshall.  
   * Complexity: `O(n³)` with `n ≤ 10`.  
2. **Iterate over all subsets** (bitmask).  
   * For each subset, we just need to ensure that *for every pair of active nodes* `i` and `j`, `dist[i][j] ≤ maxDistance`.  
3. Count the subsets that satisfy the condition.  
   * The empty subset (all nodes closed) automatically passes.  

### Why This Works  

Because removing vertices *cannot* create a shorter path than the one already computed on the full graph. If a pair is within `maxDistance` in the full graph, it remains within that distance when we close other branches (unless we close one of the endpoints). Conversely, if a pair is *already* farther than `maxDistance`, the subset is invalid regardless of what else we close.

Thus we don’t need to recompute distances for each subset—just filter.

---

## 5️⃣ Complexity Analysis

| Step | Complexity |
|------|------------|
| Floyd‑Warshall on full graph | `O(n³)`  (`≤ 1000` operations for `n=10`) |
| Enumerate subsets | `O(2ⁿ)`  (`≤ 1024`) |
| Check all pairs in subset | `O(n²)` per subset → `O(2ⁿ * n²)` |
| **Total** | `O(n³ + 2ⁿ * n²)` → effectively `≈ 1.3e6` ops for worst case. |

Memory: `O(n²)` for the distance matrix.

---

## 6️⃣ Implementation

Below are clean, commented implementations in **Java**, **Python**, and **C++** that follow the approach above. Each uses a single distance matrix and bitmask enumeration.

> **Tip for Interviews** – When you explain the solution, highlight the *observation*: closing vertices can’t shorten any shortest path, so pre‑computing all pairs once is safe. That’s often the key insight interviewers look for.

---

### 6.1 Java

```java
import java.util.*;

class Solution {
    public int numberOfSets(int n, int maxDistance, int[][] roads) {
        final int INF = (int)1e9;
        int[][] dist = new int[n][n];

        // initialise distances
        for (int i = 0; i < n; ++i) {
            Arrays.fill(dist[i], INF);
            dist[i][i] = 0;
        }

        // build graph edges
        for (int[] e : roads) {
            int u = e[0], v = e[1], w = e[2];
            if (w < dist[u][v]) {
                dist[u][v] = dist[v][u] = w;
            }
        }

        // Floyd‑Warshall on full graph
        for (int k = 0; k < n; ++k)
            for (int i = 0; i < n; ++i)
                for (int j = 0; j < n; ++j)
                    if (dist[i][k] + dist[k][j] < dist[i][j])
                        dist[i][j] = dist[i][k] + dist[k][j];

        int totalMasks = 1 << n;
        int answer = 0;

        // iterate all subsets of *closed* nodes
        for (int mask = 0; mask < totalMasks; ++mask) {
            boolean ok = true;
            for (int i = 0; i < n && ok; ++i)
                for (int j = i + 1; j < n; ++j)
                    if (((mask >> i) & 1) == 0 && ((mask >> j) & 1) == 0) {
                        if (dist[i][j] > maxDistance) ok = false;
                    }
            if (ok) answer++;
        }
        return answer;
    }
}
```

---

### 6.2 Python

```python
class Solution:
    def numberOfSets(self, n: int, maxDistance: int, roads: List[List[int]]) -> int:
        INF = 10**9
        dist = [[INF] * n for _ in range(n)]
        for i in range(n):
            dist[i][i] = 0

        for u, v, w in roads:
            if w < dist[u][v]:
                dist[u][v] = dist[v][u] = w

        # Floyd–Warshall
        for k in range(n):
            for i in range(n):
                for j in range(n):
                    if dist[i][k] + dist[k][j] < dist[i][j]:
                        dist[i][j] = dist[i][k] + dist[k][j]

        answer = 0
        for mask in range(1 << n):
            ok = True
            for i in range(n):
                if mask >> i & 1:          # node i closed
                    continue
                for j in range(i + 1, n):
                    if mask >> j & 1:      # node j closed
                        continue
                    if dist[i][j] > maxDistance:
                        ok = False
                        break
                if not ok:
                    break
            if ok:
                answer += 1
        return answer
```

---

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int numberOfSets(int n, int maxDistance, vector<vector<int>>& roads) {
        const int INF = 1e9;
        vector<vector<int>> dist(n, vector<int>(n, INF));

        for (int i = 0; i < n; ++i) dist[i][i] = 0;

        for (auto &e : roads) {
            int u = e[0], v = e[1], w = e[2];
            if (w < dist[u][v]) dist[u][v] = dist[v][u] = w;
        }

        // Floyd‑Warshall on full graph
        for (int k = 0; k < n; ++k)
            for (int i = 0; i < n; ++i)
                for (int j = 0; j < n; ++j)
                    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);

        int total = 1 << n, ans = 0;

        for (int mask = 0; mask < total; ++mask) {
            bool ok = true;
            for (int i = 0; i < n && ok; ++i)
                for (int j = i + 1; j < n; ++j)
                    if (((mask >> i) & 1) == 0 && ((mask >> j) & 1) == 0)
                        if (dist[i][j] > maxDistance) ok = false;
            if (ok) ++ans;
        }
        return ans;
    }
};
```

---

## 7️⃣ Good, Bad & Ugly in the Solution Space  

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Observational Insight** | *Key* – closing nodes can’t shorten any shortest path. | Hard to spot without thinking about graph contraction. | Interviewers often ask *why* pre‑computing distances is safe. |
| **Algorithmic Simplicity** | Clear `O(n³)` pre‑computation + bitmask filter. | Might seem “too many loops” to beginners. | The triple‑nested Floyd‑Warshall can feel heavy in other contexts. |
| **Scalability** | Works for `n ≤ 10`. | For larger `n`, the `2ⁿ` factor becomes impossible. | If `n` were 20+, we’d need a different strategy (e.g., DP + BFS). |
| **Implementation Readability** | Each language version is ~50 lines. | Too many temporary arrays if you recompute distances per subset. | Using adjacency lists for each subset (brute force) leads to code that is hard to debug. |
| **Memory Footprint** | `O(n²)` distances, trivial. | Same. | Same. |

---

## 8️⃣ Common Pitfalls & Edge Cases

| Pitfall | Fix |
|---------|-----|
| **Multiple edges** – keep the *minimum* weight. | In the initialization step, always `min(w, dist[u][v])`. |
| **Disjoint Graph** – Some pairs may never connect (`INF`). | Treat `INF` > `maxDistance` as invalid; the subset will be rejected. |
| **All nodes closed** – Ensure you count this subset. | In the subset loop, if *no active node* exists, `ok` remains `True`. |
| **Large `maxDistance`** – > 1e9 – make sure your `INF` is larger. | Use `1e9` or `Long.MAX_VALUE` accordingly. |

---

## 9️⃣ Interview Tips

1. **Start with the observation**: “Closing vertices can’t shorten a shortest path.”  
2. **Mention pre‑computation**: one Floyd‑Warshall on the full graph.  
3. **Show the bitmask logic** – iterate all subsets, filter pairwise distances.  
4. **Discuss Complexity** – `n` is tiny, so 1024 subsets are fine.  
5. **Ask clarifying questions** – e.g., “Is the empty active set considered valid?” – interviewers appreciate precision.

---

## 10️⃣ Conclusion  

LeetCode 2959 is a beautiful blend of combinatorics and graph theory.  
- The *good* part is the elegant insight that a pre‑computed all‑pairs matrix suffices.  
- The *bad* part is that it’s easy to overlook the “shortening‑path” impossibility and end up recomputing distances for every subset.  
- The *ugly* part is dealing with multiple edges and ensuring the `INF` value is set high enough.

With the code snippets above, you’re ready to submit or hand‑off a clean solution in **Java, Python, or C++**. Good luck in your next coding interview – this problem is a great showcase of how a small observation can turn a seemingly hard challenge into a perfectly manageable solution.