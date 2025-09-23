---
title: LeetCode 2737. Find the Closest Marked Node - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Find the Closest Marked Node (LeetCode 2737) – A Complete Guide  
**Keywords:** Find the Closest Marked Node, LeetCode 2737, Dijkstra algorithm, shortest path, graph traversal, Java solution, Python solution, C++ solution, interview coding, job interview, coding interview

---

## 🚀 1. What the Problem Is Asking

> **Goal:**  
> Given a **directed weighted graph** with `n` nodes (`0 … n‑1`) and an edge list `edges = [u, v, w]` (edge from `u` to `v` with weight `w`), a start node `s` and an array of **marked** nodes, return the **minimum distance** from `s` to any marked node.  
> If no marked node is reachable, return `-1`.

### Constraints Recap
| Item | Limits |
|------|--------|
| `n` | 2 – 500 |
| `edges.length` | 1 – 10⁴ |
| `edges[i][2]` | 1 – 10⁶ |
| `marked.length` | 1 – n‑1 |
| No self‑loops; repeated edges possible |
| All weights are positive |

---

## 🔎 2. Why Dijkstra Is the Natural Fit

* **Single‑source shortest path** – We need the shortest path **from `s`** to *any* marked node.  
* **Positive weights only** – Dijkstra’s algorithm guarantees optimality when all edge weights are non‑negative.  
* **Early‑stop** – We can stop as soon as we pop a marked node from the priority queue – we’re already at the *closest* one.

---

## 🏗️ 3. Algorithm Overview

1. **Build an adjacency list** from the edge list.  
   `adj[u]` → list of `(v, w)` pairs.  
2. **Maintain a min‑heap (priority queue)** of `(distance, node)`.  
3. **Keep a `dist[]` array** (initially `∞`) and a `visited[]` array.  
4. **Push** `(0, s)` into the queue, set `dist[s] = 0`.  
5. While the queue is not empty:  
   * Pop the smallest distance node `u`.  
   * If `u` is already visited, skip.  
   * Mark `u` as visited.  
   * **If `u` is a marked node → return `dist[u]`.**  
   * Relax all outgoing edges `u → v`:
     ```text
     if dist[u] + w < dist[v]:
         dist[v] = dist[u] + w
         push (dist[v], v)
     ```
6. If the loop finishes without hitting a marked node → return `-1`.

The algorithm runs in **O((n + m) log n)** time and **O(n + m)** space (with `m = edges.length`).

---

## ⚙️ 4. The “Good, the Bad, and the Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic soundness** | ✅ Dijkstra guarantees optimality with positive weights. | ❌ Not suitable for negative weights – would need Bellman‑Ford or SPFA. | 😱 Repeated edges could inflate the heap unnecessarily, but with `m ≤ 10⁴` it’s negligible. |
| **Early exit** | ✅ Stops as soon as the closest marked node is settled, saving time. | ❌ Requires careful visited check – if omitted you may return a sub‑optimal distance. | 😠 A buggy visited array (e.g., `dist` only) can cause wrong results. |
| **Memory footprint** | ✅ Only stores adjacency list, distance and visited arrays. | ❌ For very dense graphs the heap can grow large. | 😱 Over‑engineering a binary heap wrapper when the STL/collections library is enough. |

---

## 📚 5. Code Implementations

Below are clean, production‑ready snippets for **Java**, **Python**, and **C++**. All use the same approach described above.

> **Tip:** In all solutions the early exit (`if markedSet.contains(node)`) guarantees we always return the minimal distance to any marked node.

### 5.1 Java (Java 11+)

```java
import java.util.*;

public class Solution {
    public int minimumDistance(int n, List<List<Integer>> edges, int s, int[] marked) {
        // 1. Set of marked nodes for O(1) lookup
        Set<Integer> markedSet = new HashSet<>();
        for (int node : marked) markedSet.add(node);

        // 2. Adjacency list
        List<List<int[]>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for (List<Integer> e : edges) {
            int u = e.get(0), v = e.get(1), w = e.get(2);
            adj.get(u).add(new int[]{v, w});
        }

        // 3. Distances + visited
        int[] dist = new int[n];
        boolean[] visited = new boolean[n];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[s] = 0;

        // 4. Min‑heap priority queue
        PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));
        pq.offer(new int[]{0, s});

        // 5. Dijkstra with early exit
        while (!pq.isEmpty()) {
            int[] cur = pq.poll();
            int d = cur[0], u = cur[1];
            if (visited[u]) continue;
            visited[u] = true;
            if (markedSet.contains(u)) return dist[u];
            for (int[] nb : adj.get(u)) {
                int v = nb[0], w = nb[1];
                if (dist[u] + w < dist[v]) {
                    dist[v] = dist[u] + w;
                    pq.offer(new int[]{dist[v], v});
                }
            }
        }
        return -1;   // no marked node reachable
    }
}
```

### 5.2 Python 3

```python
import heapq
from typing import List, Tuple

class Solution:
    def minimumDistance(self, n: int, edges: List[List[int]],
                        s: int, marked: List[int]) -> int:
        adj = [[] for _ in range(n)]
        for u, v, w in edges:
            adj[u].append((v, w))

        marked_set = set(marked)
        dist = [float('inf')] * n
        visited = [False] * n
        dist[s] = 0
        heap = [(0, s)]

        while heap:
            d, u = heapq.heappop(heap)
            if visited[u]:
                continue
            visited[u] = True
            if u in marked_set:
                return dist[u]
            for v, w in adj[u]:
                if dist[u] + w < dist[v]:
                    dist[v] = dist[u] + w
                    heapq.heappush(heap, (dist[v], v))
        return -1
```

### 5.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumDistance(int n, vector<vector<int>>& edges,
                        int s, vector<int>& marked) {
        // Build adjacency list
        vector<vector<pair<int,int>>> adj(n);
        for (auto& e : edges)
            adj[e[0]].emplace_back(e[1], e[2]);

        unordered_set<int> markSet(marked.begin(), marked.end());

        vector<long long> dist(n, LLONG_MAX);
        vector<bool> visited(n, false);
        dist[s] = 0;

        using P = pair<long long,int>;
        priority_queue<P, vector<P>, greater<P>> pq;
        pq.emplace(0, s);

        while (!pq.empty()) {
            auto [d, u] = pq.top(); pq.pop();
            if (visited[u]) continue;
            visited[u] = true;
            if (markSet.count(u)) return dist[u];

            for (auto [v,w] : adj[u]) {
                if (dist[u] + w < dist[v]) {
                    dist[v] = dist[u] + w;
                    pq.emplace(dist[v], v);
                }
            }
        }
        return -1;
    }
};
```

---

## 📈 6. Complexity Analysis

| Metric | Time | Space |
|--------|------|-------|
| **Best‑case** (marked node is a direct neighbor of `s`) | O(1) – we pop `s` and immediately exit | O(n + m) |
| **Worst‑case** (no marked node reachable) | O((n + m) log n) | O(n + m) |
| **Practical** (given constraints) | Fast enough for all test cases | Uses only adjacency list + heap |

---

## 🛠️ 6. Edge‑Case Checklist

| Edge Case | What to Watch For |
|-----------|-------------------|
| **Multiple edges between the same nodes** | All edges are relaxed – duplicates are fine; heap size may increase slightly. |
| **Start node `s` is already marked** | The algorithm will immediately pop `(0, s)` and return `0`. |
| **No reachable marked node** | The queue will empty; return `-1`. |
| **Large weights (up to 10⁶)** | Use `long long` in C++ or `long` in Java to avoid overflow. |
| **Graph disconnected** | Early exit won’t trigger; ensure final `-1` return. |

---

## 🔄 7. Variations & Extensions

| Variation | Approach |
|-----------|----------|
| **All edges undirected** | Build adjacency list in both directions – Dijkstra still applies. |
| **Negative weights allowed** | Switch to **Bellman‑Ford** or **SPFA**; early exit possible but slower. |
| **Multiple source nodes** | Run Dijkstra from each source or use **multi‑source Dijkstra** by inserting all sources with distance 0 into the heap. |
| **Weighted graph with zero‑weight edges** | Dijkstra works fine as long as no negative cycles exist. |
| **Goal: sum of distances to all marked nodes** | After settling all nodes, sum `dist[node]` for all `node` in marked set. |

---

## 🎯 8. Why Mastering This Problem Helps Your Job Hunt

1. **Showcases graph‑theoretic thinking** – Interviewers love to see you apply the right algorithm (Dijkstra vs. Floyd‑Warshall vs. BFS).  
2. **Highlights early‑exit optimization** – Indicates you can think about pruning and performance.  
3. **Demonstrates clean coding style** – All three language snippets use concise, idiomatic data structures (`PriorityQueue`, `heapq`, `std::priority_queue`).  
4. **Versatility** – You can pivot from Java → Python → C++ in the same interview, proving language agnosticism.  
5. **Real LeetCode example** – 2737 appears on the “Graph” difficulty set; solving it signals readiness for “Graph” problems in coding interviews.

---

## 📢 9. Final Takeaway

`2737. Find the Closest Marked Node` is a *classic* single‑source shortest‑path problem that maps perfectly to Dijkstra’s algorithm with an early exit. Mastering this solution:

* Gives you a **reusable template** for any shortest‑path interview question.  
* Shows you how to **optimize** by stopping at the first marked node.  
* Builds confidence in handling **edge cases** like repeated edges, unreachable targets, and large weights.  

Next time you hit a LeetCode “Graph” problem in an interview, remember the early‑stop Dijkstra trick – it’s your ticket to clean, efficient, and interview‑grade code.

---

## 📄 10. SEO Meta‑Data (Optional for blog hosting)

```html
<title>Find the Closest Marked Node – LeetCode 2737 | Dijkstra & Graph Traversal</title>
<meta name="description" content="Solve LeetCode 2737: Find the Closest Marked Node with Dijkstra. Read Java, Python & C++ solutions, time & space complexity, and interview tips.">
<meta name="keywords" content="Find the Closest Marked Node, LeetCode 2737, Dijkstra algorithm, graph traversal, Java solution, Python solution, C++ solution, shortest path, job interview coding">
```

Happy coding, and good luck landing that tech interview!