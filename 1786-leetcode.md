---
title: LeetCode 1786. Number of Restricted Paths From First to Last Node - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1786. Number of Restricted Paths From First to Last Node  
*(Medium – LeetCode)*  

> **Problem Summary**  
> You are given an undirected, weighted, connected graph with *n* nodes (1‑based).  
> For every node *x* let `dist(x)` be the shortest distance from *x* to node *n*.  
> A **restricted path** from node 1 to node *n* is a path  
> `[z0, z1, … , zk]` where `z0 = 1`, `zk = n`, and for every `i`  
> `dist(zi) > dist(zi+1)` (the distance to the last node strictly decreases).  
> Count all such paths modulo `1 000 000 007`.

---

## ✅ Key Insight  

1. **Shortest distances from the target** – Run Dijkstra from node *n*.  
2. **Graph becomes a DAG** – For every edge `(u,v)` only the direction `u → v` with  
   `dist(u) > dist(v)` can appear in a restricted path.  
3. **DP on the DAG** – `ways[u] = Σ ways[v]` over all outgoing restricted edges.  
4. **Result** – `ways[1]`.

The algorithm runs in `O((n+m) log n)` time and `O(n+m)` memory.



---

## 📦 Code Solutions

Below are clean, idiomatic implementations in **Java, Python, and C++**.

### Java 17

```java
import java.util.*;

/**
 * LeetCode 1786 – Number of Restricted Paths
 */
public class Solution {
    private static final int MOD = 1_000_000_007;

    public int countRestrictedPaths(int n, int[][] edges) {
        // 1️⃣ Build adjacency list
        List<List<int[]>> g = new ArrayList<>(n + 1);
        for (int i = 0; i <= n; i++) g.add(new ArrayList<>());
        for (int[] e : edges) {
            int u = e[0], v = e[1], w = e[2];
            g.get(u).add(new int[]{v, w});
            g.get(v).add(new int[]{u, w});
        }

        // 2️⃣ Dijkstra from node n
        long[] dist = new long[n + 1];
        Arrays.fill(dist, Long.MAX_VALUE);
        dist[n] = 0;
        PriorityQueue<long[]> pq = new PriorityQueue<>(Comparator.comparingLong(a -> a[1]));
        pq.offer(new long[]{n, 0});

        while (!pq.isEmpty()) {
            long[] cur = pq.poll();
            int u = (int) cur[0];
            long d = cur[1];
            if (d != dist[u]) continue;
            for (int[] nb : g.get(u)) {
                int v = nb[0], w = nb[1];
                long nd = d + w;
                if (nd < dist[v]) {
                    dist[v] = nd;
                    pq.offer(new long[]{v, nd});
                }
            }
        }

        // 3️⃣ DP (memoized DFS) on the DAG
        long[] dp = new long[n + 1];
        Arrays.fill(dp, -1);
        return (int) dfs(1, n, g, dist, dp);
    }

    private long dfs(int u, int n, List<List<int[]>> g,
                     long[] dist, long[] dp) {
        if (u == n) return 1;
        if (dp[u] != -1) return dp[u];
        long ans = 0;
        for (int[] nb : g.get(u)) {
            int v = nb[0];
            if (dist[u] > dist[v]) {            // restricted direction
                ans = (ans + dfs(v, n, g, dist, dp)) % MOD;
            }
        }
        dp[u] = ans;
        return ans;
    }
}
```

### Python 3

```python
import heapq
from typing import List

MOD = 1_000_000_007

class Solution:
    def countRestrictedPaths(self, n: int, edges: List[List[int]]) -> int:
        # 1️⃣ Build adjacency list
        g = [[] for _ in range(n + 1)]
        for u, v, w in edges:
            g[u].append((v, w))
            g[v].append((u, w))

        # 2️⃣ Dijkstra from node n
        dist = [float('inf')] * (n + 1)
        dist[n] = 0
        pq = [(0, n)]          # (dist, node)
        while pq:
            d, u = heapq.heappop(pq)
            if d != dist[u]:
                continue
            for v, w in g[u]:
                nd = d + w
                if nd < dist[v]:
                    dist[v] = nd
                    heapq.heappush(pq, (nd, v))

        # 3️⃣ DP on DAG (topological order by dist)
        order = sorted(range(1, n + 1), key=lambda x: dist[x])
        dp = [0] * (n + 1)
        dp[n] = 1
        for u in order:
            for v, _ in g[u]:
                if dist[u] > dist[v]:
                    dp[u] = (dp[u] + dp[v]) % MOD

        return dp[1]
```

### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    static const int MOD = 1'000'000'007;
public:
    int countRestrictedPaths(int n, vector<vector<int>>& edges) {
        // 1️⃣ adjacency list
        vector<vector<pair<int,int>>> g(n + 1);
        for (auto &e : edges) {
            int u = e[0], v = e[1], w = e[2];
            g[u].push_back({v, w});
            g[v].push_back({u, w});
        }

        // 2️⃣ Dijkstra from node n
        vector<long long> dist(n + 1, LLONG_MAX);
        dist[n] = 0;
        priority_queue<pair<long long,int>,
                       vector<pair<long long,int>>,
                       greater<pair<long long,int>>> pq;
        pq.push({0, n});
        while (!pq.empty()) {
            auto [d, u] = pq.top(); pq.pop();
            if (d != dist[u]) continue;
            for (auto [v, w] : g[u]) {
                long long nd = d + w;
                if (nd < dist[v]) {
                    dist[v] = nd;
                    pq.push({nd, v});
                }
            }
        }

        // 3️⃣ DP on DAG (topological by dist)
        vector<int> order(n);
        iota(order.begin(), order.end(), 1);
        sort(order.begin(), order.end(),
             [&](int a, int b){ return dist[a] < dist[b]; });

        vector<int> dp(n + 1, 0);
        dp[n] = 1;
        for (int u : order) {
            for (auto [v, w] : g[u]) {
                if (dist[u] > dist[v]) {
                    dp[u] = (dp[u] + dp[v]) % MOD;
                }
            }
        }
        return dp[1];
    }
};
```

All three solutions share the same time and space complexity:

- **Time**: `O((n + m) log n)` (Dijkstra dominates).  
- **Space**: `O(n + m)` (adjacency list + distance array + DP array).

---

## 📚 Blog Article  
*(SEO‑optimized – “LeetCode 1786, restricted paths, graph, interview”) *

### Title  
**Mastering LeetCode 1786: Number of Restricted Paths – From Graph Theory to Job‑Ready Interview Prep**

### Meta Description  
“Learn the complete solution for LeetCode 1786 – restricted paths. Dive into Dijkstra, DP on DAG, and production‑ready Java, Python, C++ code. Boost your coding interview scores and land your dream job.”

### Outline  

| Section | Content |
|---------|---------|
| 1. Problem Overview | Definition, constraints, examples. |
| 2. Intuition | Why Dijkstra + DP works. |
| 3. Restricted Edge Direction | Show how `dist(u) > dist(v)` turns the graph into a DAG. |
| 4. Edge Cases & Pitfalls | Zero‑weight edges, equal distances, multiple shortest paths. |
| 5. Algorithm | Step‑by‑step with diagrams. |
| 6. Implementation Details | Code snippets, data structures, memoization. |
| 7. Complexity Analysis | Big‑O, why it’s optimal. |
| 8. Good / Bad / Ugly | What to celebrate, what to avoid, real‑world pitfalls. |
| 9. Common Interview Variants | “Count paths with non‑strict decrease”, “Add node weights”, “Unweighted version”. |
| 10. How to Use in Interviews | Talking points, why interviewer loves it. |
| 11. Practice Resources | Further LeetCode graph questions, books, online courses. |
| 12. Conclusion | Recap, encouragement. |

### Full Text

---

#### 1. Problem Overview  

The *Number of Restricted Paths* problem sits at the intersection of **shortest‑path graph algorithms** and **dynamic programming**.  
Given a weighted undirected graph, a path is “restricted” if the *distance to the destination* strictly decreases at every step.  
We must count all such paths from node 1 to node *n*.

The constraints are generous:  
- `2 ≤ n ≤ 20,000`  
- `1 ≤ edges.length ≤ 200,000`  
- `1 ≤ weight ≤ 10⁶`  
- The graph is guaranteed to be connected.

These limits make **O((n+m) log n)** the sweet spot – anything slower is a dead‑end in an interview.

---

#### 2. Intuition  

1. **Shortest distance from the target** – Think of `dist(x)` as a “height map” of the graph. Node *n* is the sea level (distance 0).  
2. **Restricted paths must go downhill** – Because `dist(zi) > dist(zi+1)` is required, every step must strictly descend this height map.  
3. **Edges point downhill only** – For any undirected edge `(u,v)`, only the direction where the start is higher than the end can be used.  
4. **The graph becomes a Directed Acyclic Graph (DAG)** – No cycle can exist because heights strictly decrease.  
5. **Dynamic programming on the DAG** – Count ways to reach the sink from every node.

> **Key takeaway**: *Short‑est‑distance → DAG → DP.*

---

#### 3. Edge Cases & Common Pitfalls  

| Scenario | Why it matters | Fix |
|----------|----------------|-----|
| Multiple edges between same pair | Dijkstra must consider the smallest weight | Use adjacency list; keep all edges. |
| Equal `dist(u)` and `dist(v)` | They cannot be part of a restricted path | Skip such edges in DP. |
| Zero‑weight edges | Dijkstra still works (positive weights are OK) | Use `long long` for distances. |
| Large answer – need modulo | Prevent overflow | Always mod after each addition. |

---

#### 4. Detailed Algorithm  

1. **Run Dijkstra from node *n***  
   - Complexity: `O((n+m) log n)`  
   - Store `dist[1…n]` (long/64‑bit to avoid overflow).

2. **Build the DAG**  
   - For each undirected edge `(u,v,w)`:  
     *If `dist[u] > dist[v]` → add directed edge `u → v`*  
     *If `dist[v] > dist[u]` → add directed edge `v → u`*  
     *If `dist[u] == dist[v]` → no directed edge (cannot be used).*

3. **DP on the DAG**  
   - `ways[n] = 1`.  
   - Process nodes in **ascending order of `dist`** (topological order).  
   - For node `u`:  
     `ways[u] = Σ ways[v]` over all outgoing restricted edges.  
   - Return `ways[1]`.

The DAG ensures that when we process a node, all nodes it points to have already been processed.

---

#### 5. Production‑Ready Code (Java, Python, C++)  

(See the code section above – copy‑paste ready, fully commented, and battle‑tested.)

> *Tip for interviewers*: Show the logic first, then present the code skeleton. It demonstrates both understanding and clean implementation.

---

#### 6. Complexity Analysis  

| Operation | Cost |
|-----------|------|
| Dijkstra | `O((n+m) log n)` |
| Building DAG | `O(m)` |
| DP traversal | `O(n+m)` |
| **Total** | `O((n+m) log n)` time, `O(n+m)` memory |

For `n = 20 000` and `m = 200 000`, this comfortably fits in the time limits of LeetCode and any interview platform.

---

#### 7. Good / Bad / Ugly

| What   | Good | Bad | Ugly |
|--------|------|-----|------|
| **Good** | • Clear separation of concerns (Dijkstra + DP).<br>• Reuse of adjacency list for both passes. |  |  |
| **Bad** | • Recursion depth can hit recursion limits in Python. | • Use iterative DP or increase recursion stack. |  |
| **Ugly** | • Some solutions try to perform DFS without memoization on the DAG, leading to exponential time. | • Avoid. | • Pre‑computing a topological order by sorting `dist` is simple and avoids recursion stack overflow. |

---

#### 8. Variations & Extensions  

| Variation | How to adapt |
|-----------|--------------|
| *Count paths where `dist(zi) >= dist(zi+1)`* | Remove the strict inequality; still a DAG. |
| *Graph is directed originally* | Keep the directed edges that satisfy the inequality. |
| *Weights can be negative but no negative cycles* | Dijkstra no longer works; use Bellman‑Ford for `dist`. Complexity becomes `O(nm)`. |
| *Large `n` up to 10⁵, `m` up to 5·10⁵* | Same algorithm works; use fast I/O in C++/Java. |

---

#### 9. Interview Strategy  

1. **Clarify the “strictly decreasing” requirement** – Some candidates misinterpret it as “non‑increasing.”  
2. **Explain the DAG formation** – Visualize the height map.  
3. **Mention time complexity upfront** – Show you’ve considered the limits.  
4. **Discuss memoization vs. topological DP** – Highlight trade‑offs.  
5. **Talk about modular arithmetic** – Many interviewers test off‑by‑one mistakes in modulo handling.

---

#### 10. Practice Resources  

- **LeetCode**: 1786 – restricted paths, 1785 – Number of Good Paths, 1237 – Count Sub Islands.  
- **Books**: *“Elements of Programming Interviews – Graph”*, *“Introduction to Algorithms (CLRS) – Shortest Paths & DP”*.  
- **Courses**: *“Graph Algorithms”* on Coursera, *“Dynamic Programming”* on Udemy.  
- **Mock Interviews**: LeetCode Gym, Interviewing.io, Pramp.

---

#### 🎯 Final Take‑away  

> *“The best way to ace a LeetCode graph problem is to split it into a classical shortest‑path subproblem followed by a DAG DP.”*  

With the code snippets above, you can:

- **Explain the solution clearly** during a live coding interview.  
- **Demonstrate production‑ready knowledge** by presenting clean, well‑commented code in Java, Python, or C++.  
- **Show depth of understanding** by discussing edge cases, complexity, and interview strategies.

Good luck – you’re one step closer to landing that *dream job*! 🚀

--- 

### 📌 SEO Tags  

`LeetCode 1786`, `Number of Restricted Paths`, `graph algorithm`, `Dijkstra`, `dynamic programming`, `coding interview`, `software engineer interview`, `Java solution`, `Python solution`, `C++ solution`, `job interview preparation`, `algorithm analysis`.