---
title: LeetCode 743. Network Delay Time - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Mastering LeetCode 743 – Network Delay Time  
**The Good, The Bad, and The Ugly – A Complete Guide for Interview‑Ready Coders**  

---

## 🚀 Why This Problem Matters

- **LeetCode Rank:** #743 (Medium) – a classic interview favorite.  
- **Concepts Tested:** Graph representation, Dijkstra’s shortest‑path, priority queues, edge‑weight handling.  
- **Real‑World Analog:** Signal propagation in a communication network, or message delivery latency in distributed systems.  

If you want to **land a software‑engineering role** that values graph algorithms, solving this problem cleanly will set you apart from the crowd. Below you’ll find:

1. **A clear, interview‑friendly explanation** of the algorithm.  
2. **Bug‑free code in Java, Python, and C++** – ready for copy‑paste.  
3. **SEO‑optimized blog content** that will rank high on search engines and help recruiters find you.  

---

## 📄 Problem Restatement

> **Network Delay Time**  
> Given a directed graph where `times[i] = [uᵢ, vᵢ, wᵢ]` represents an edge from node `uᵢ` to `vᵢ` with weight `wᵢ` (travel time), find the minimum time required for a signal sent from node `k` to reach **all** nodes.  
> Return `-1` if any node is unreachable.  
>  
> **Constraints**  
> * `1 ≤ n ≤ 100`  
> * `1 ≤ times.length ≤ 6000`  
> * `0 ≤ wᵢ ≤ 100`  

---

## 📈 Intuition – The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Graph Representation** | Adjacency list → O(E) space | Forget to convert 1‑based to 0‑based indices → IndexError | Using a 2‑D array of size `n²` → O(n²) memory waste |
| **Shortest‑Path Algorithm** | Dijkstra with a min‑heap → O(E log V) | Using BFS for weighted edges → Wrong answer for non‑uniform weights | Ignoring edge weights (treating as unit) → O(E + V) but incorrect |
| **Edge Cases** | Check for `INF` → return `-1` | Forget to initialise distance for source → Wrong max value | Not handling isolated nodes → RuntimeError |
| **Performance** | Priority queue guarantees the next closest node → Optimal | O(V²) Dijkstra (array scan) → Acceptable for n=100 but risky | O(E) using SPFA with bad worst‑case → Time limit exceeded on large graphs |

---

## 📊 Algorithm Overview – Dijkstra’s Shortest‑Path

1. **Build an adjacency list**  
   `adj[u] = [(v, w), …]`  
   The graph is directed; keep the original orientation.

2. **Initialize distances**  
   `dist[i] = ∞` for all nodes, `dist[k] = 0`.

3. **Priority Queue (Min‑Heap)**  
   Each element is a pair `(current_distance, node)`.  
   Pop the node with the smallest tentative distance.

4. **Relaxation**  
   For every neighbor `(v, w)` of the popped node:  
   ```
   if dist[u] + w < dist[v]:
        dist[v] = dist[u] + w
        push (dist[v], v) into heap
   ```

5. **Answer**  
   After the heap is empty, if any `dist[i] == ∞` → return `-1`.  
   Otherwise return `max(dist)` – the time when the last node receives the signal.

---

## 🧑‍💻 Code Snippets

### 1️⃣ Java

```java
import java.util.*;

class Solution {
    public int networkDelayTime(int[][] times, int n, int k) {
        // 1‑based → 0‑based conversion handled in the loops
        List<List<int[]>> adj = new ArrayList<>(n + 1);
        for (int i = 0; i <= n; i++) adj.add(new ArrayList<>());

        for (int[] t : times) {
            adj.get(t[0]).add(new int[]{t[1], t[2]}); // [neighbor, weight]
        }

        int[] dist = new int[n + 1];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[k] = 0;

        // PriorityQueue of (distance, node)
        PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));
        pq.offer(new int[]{0, k});

        while (!pq.isEmpty()) {
            int[] cur = pq.poll();
            int d = cur[0], u = cur[1];

            // Skip stale entries
            if (d != dist[u]) continue;

            for (int[] edge : adj.get(u)) {
                int v = edge[0], w = edge[1];
                if (d + w < dist[v]) {
                    dist[v] = d + w;
                    pq.offer(new int[]{dist[v], v});
                }
            }
        }

        int ans = 0;
        for (int i = 1; i <= n; i++) {
            if (dist[i] == Integer.MAX_VALUE) return -1;
            ans = Math.max(ans, dist[i]);
        }
        return ans;
    }
}
```

### 2️⃣ Python

```python
import heapq
from typing import List

class Solution:
    def networkDelayTime(self, times: List[List[int]], n: int, k: int) -> int:
        adj = [[] for _ in range(n + 1)]
        for u, v, w in times:
            adj[u].append((v, w))

        dist = [float('inf')] * (n + 1)
        dist[k] = 0

        heap = [(0, k)]  # (distance, node)
        while heap:
            d, u = heapq.heappop(heap)
            if d != dist[u]:
                continue
            for v, w in adj[u]:
                nd = d + w
                if nd < dist[v]:
                    dist[v] = nd
                    heapq.heappush(heap, (nd, v))

        max_time = max(dist[1:])  # skip index 0
        return -1 if max_time == float('inf') else max_time
```

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int networkDelayTime(vector<vector<int>>& times, int n, int k) {
        vector<vector<pair<int,int>>> adj(n + 1);
        for (auto &t : times)
            adj[t[0]].push_back({t[1], t[2]}); // {neighbor, weight}

        const int INF = INT_MAX / 2;        // Avoid overflow
        vector<int> dist(n + 1, INF);
        dist[k] = 0;

        using P = pair<int,int>;           // (distance, node)
        priority_queue<P, vector<P>, greater<P>> pq;
        pq.emplace(0, k);

        while (!pq.empty()) {
            auto [d, u] = pq.top(); pq.pop();
            if (d != dist[u]) continue;   // stale entry

            for (auto [v, w] : adj[u]) {
                if (d + w < dist[v]) {
                    dist[v] = d + w;
                    pq.emplace(dist[v], v);
                }
            }
        }

        int ans = 0;
        for (int i = 1; i <= n; ++i) {
            if (dist[i] == INF) return -1;
            ans = max(ans, dist[i]);
        }
        return ans;
    }
};
```

> **All three implementations** use a *binary heap* (`PriorityQueue` / `heapq`) and an adjacency list, giving the optimal `O(E log V)` time complexity.

---

## 📈 Complexity Analysis

| Language | Time | Space |
|----------|------|-------|
| Java | `O(E log V)` (heap operations) | `O(V + E)` (adjacency list + distance array) |
| Python | `O(E log V)` | `O(V + E)` |
| C++ | `O(E log V)` | `O(V + E)` |

With the problem limits (`n ≤ 100`, `E ≤ 6000`) all three solutions run comfortably under 10 ms on modern hardware.

---

## ❗️ Common Pitfalls (The Ugly)

| Pitfall | How to Avoid |
|---------|--------------|
| **Stale heap entries** | Check `if d != dist[u]` before relaxing. |
| **1‑based vs 0‑based indices** | Keep the graph as `n + 1` and never subtract 1 unless the problem explicitly says so. |
| **Using `int` for distances** | Edge weights ≤ 100, `n` ≤ 100 → max possible distance ≤ 100 * 99 = 9900. `int` is fine, but use `INF` large enough. |
| **Graph direction** | Remember that the edges are directed; swapping `u` and `v` changes the problem completely. |
| **Disconnected nodes** | After Dijkstra, scan the `dist` array for `INF` to detect unreachable nodes. |

---

## 🏆 Interview‑Ready Tips

1. **Explain the graph model first** – recruiters love to see you articulate the problem before jumping into code.  
2. **Discuss complexity upfront** – show you know the trade‑offs between Dijkstra, BFS, and Bellman‑Ford.  
3. **Mention alternative approaches** (e.g., SPFA, Floyd‑Warshall) and why they’re less suitable for this problem.  
4. **Talk about corner cases** – isolated nodes, zero‑weight edges, self‑loops.  
5. **Show confidence** – highlight that Dijkstra guarantees optimality for non‑negative weights.  

> *“Dijkstra is not just a coding trick; it’s the algorithm that guarantees correct shortest‑path for any directed graph with non‑negative edge weights.”*  

---

## 📢 SEO Meta Tags & Keywords

```html
<title>LeetCode 743 Network Delay Time – Dijkstra Algorithm Explained (Java, Python, C++)</title>
<meta name="description" content="Solve LeetCode 743 – Network Delay Time with Dijkstra. Get a clean Java, Python, and C++ solution for coding interviews.">
<meta name="keywords" content="LeetCode 743, Network Delay Time, Dijkstra algorithm, graph shortest path, coding interview, Java solution, Python solution, C++ solution, interview question, software engineer interview, data structures, algorithms">
```

These tags, combined with the article’s structure, will help your blog rank for queries like:

- “LeetCode 743 Network Delay Time solution”
- “Dijkstra graph algorithm interview”
- “Java Python C++ shortest path”
- “Coding interview problem solutions”

---

## 🎯 Final Thoughts – Land Your Next Job

1. **Master the algorithm** – know *why* Dijkstra works and when to use it.  
2. **Deliver clean, production‑ready code** – test locally, then paste into the LeetCode editor.  
3. **Speak to the interviewer** – describe the graph, the heap, the relaxation, and the edge‑case handling.  

By following the structure above, you’ll produce a *robust, optimal* solution that showcases your ability to solve non‑trivial graph problems—exactly the skill set recruiters hunt for in junior‑to‑mid‑level software‑engineers.

Good luck, and happy coding! 🚀