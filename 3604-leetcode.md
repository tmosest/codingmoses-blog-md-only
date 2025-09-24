---
title: LeetCode 3604. Minimum Time to Reach Destination in Directed Graph - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution Overview

**Problem** –  
Given a directed graph with `n` nodes (0 … n‑1).  
An edge is defined by `[u, v, start, end]` and can only be traversed at an integer time `t` such that `start ≤ t ≤ end`.  
You start at node `0` at time `0`.  
In one time unit you can either wait on the current node or traverse a valid outgoing edge.  
Find the earliest possible time you can reach node `n‑1`.  
Return `-1` if it is impossible.

---

### 1.1  Why Dijkstra Works Here

The key observation is that the “cost” of moving from a node `u` at time `t` to a node `v` is **always exactly 1 time unit** (the act of traversing the edge).  
The only extra twist is that you might have to *wait* until the edge becomes available, i.e. until `max(start, t)`.

For a node `u` that we reach at time `t`:

```
if t ≤ end:
      depart_time = max(start, t)          // wait if necessary
      arrive_time = depart_time + 1        // edge traversal takes 1 unit
```

The arrival time is a monotonic function of the departure time, therefore the earliest arrival time to any node is always better than any later arrival.  
Hence we can treat the graph as a weighted graph where the weight from `u` to `v` depends on the current time, but the optimality still holds – we just need a **priority‑queue** (min‑heap) that always expands the node that can be reached the earliest.

That is exactly Dijkstra’s algorithm with a time‑dependent relaxation step.

---

### 1.2  Algorithm Steps

| Step | Description |
|------|-------------|
| 1 | Build an adjacency list `G[u]` containing tuples `(v, start, end)` for every outgoing edge of `u`. |
| 2 | `dist[i]` – earliest known arrival time at node `i`. Initialize all to `∞` except `dist[0] = 0`. |
| 3 | Use a min‑heap storing `(time, node)`. Push `(0, 0)` initially. |
| 4 | While the heap is not empty: |
|   | * Pop `(t, u)` – the node that can be reached the earliest. |
|   | * If we already have a better known time for `u` (`t > dist[u]`), skip. |
|   | * If `u == n-1`, we are done – return `t`. |
|   | * For every edge `(v, s, e)` from `u`: |
|   |   * If `t > e` → the edge is already closed, ignore. |
|   |   * `depart = max(s, t)`  (wait if needed). |
|   |   * `arrival = depart + 1`. |
|   |   * If `arrival < dist[v]`, update `dist[v]` and push `(arrival, v)` onto the heap. |
| 5 | If the loop ends without reaching node `n-1`, return `-1`. |

---

### 1.3  Correctness Proof Sketch

We prove that the algorithm returns the minimum possible arrival time at node `n-1`.

**Lemma 1 (Monotonicity)**  
If a node `u` can be reached at time `t1` and `t1 < t2`, then any path that starts from `u` at time `t1` cannot arrive later at a destination than a path that starts at time `t2`.

*Proof.* The only operation that can delay progress is waiting. Waiting more can only push the departure time of the next edge forward, never earlier. Therefore the earliest arrival from a later start time is ≥ the earliest arrival from an earlier start time. ∎

**Lemma 2 (Optimal Substructure)**  
Let `π` be an optimal path from node `0` to node `n-1`. The prefix of `π` up to any intermediate node `u` is also an optimal path from `0` to `u`.

*Proof.* Assume the contrary – that there exists a better path to `u`. Concatenating that better prefix with the suffix of `π` from `u` to `n-1` would give a better overall path, contradicting the optimality of `π`. ∎

**Theorem (Algorithm correctness)**  
When the algorithm extracts a node `u` from the priority queue with time `t`, `t` equals the minimal possible arrival time at `u`.

*Proof by induction on the number of extracted nodes.*

- **Base**: The first extracted node is `(0,0)`. Clearly `0` is minimal for node `0`.  
- **Induction step**: Assume the statement holds for all nodes extracted before `u`.  
  Suppose, for contradiction, there exists a path to `u` that arrives at time `t' < t`.  
  Let `p` be the predecessor of `u` on that optimal path, arriving at time `t_p`.  
  By induction hypothesis, `t_p` is minimal, thus when `p` was extracted, the algorithm considered the edge `p → u`.  
  It would have calculated `arrival = max(start, t_p) + 1 ≤ t'` and inserted `(arrival, u)` into the heap.  
  Since `arrival ≤ t' < t`, the heap would have popped `(arrival, u)` before `(t, u)`, contradicting the assumption that `(t,u)` was extracted next. ∎

Finally, the first time the algorithm extracts node `n-1`, the time is the optimal arrival time. If the heap empties before that, no path exists. ∎

---

### 1.4  Complexity Analysis

| Operation | Time | Explanation |
|-----------|------|-------------|
| Build adjacency list | **O(E)** | `E` = number of edges |
| Heap operations | Each node may be inserted multiple times, but at most once per relaxation that improves `dist`. Each insertion/extraction costs **O(log V)**. In the worst case we process every edge once → **O((V+E) log V)** |
| Total time | **O((V + E) log V)** |
| Space | **O(V + E)** for adjacency list, distance array, heap, and visited flags. |

With constraints `n ≤ 10^5` and `edges.length ≤ 10^5`, this comfortably fits in time and memory limits.

---

## 2.  Code Implementations

Below are concise, production‑ready solutions in **Java**, **Python**, and **C++**.

> **Tip** – For interview practice, implement the algorithm from scratch (no library “shortcuts”) to demonstrate understanding.

### 2.1  Java

```java
import java.util.*;

public class Solution {
    public int minTime(int n, int[][] edges) {
        // adjacency list: node -> list of {to, start, end}
        List<List<int[]>> graph = new ArrayList<>(n);
        for (int i = 0; i < n; ++i) graph.add(new ArrayList<>());

        for (int[] e : edges) {
            graph.get(e[0]).add(new int[]{e[1], e[2], e[3]});
        }

        // earliest known arrival times
        long[] dist = new long[n];
        Arrays.fill(dist, Long.MAX_VALUE);
        dist[0] = 0;

        // min‑heap of (time, node)
        PriorityQueue<long[]> pq = new PriorityQueue<>(Comparator.comparingLong(a -> a[0]));
        pq.offer(new long[]{0, 0});

        while (!pq.isEmpty()) {
            long[] cur = pq.poll();
            long t = cur[0];
            int u = (int) cur[1];

            if (t != dist[u]) continue;          // stale entry
            if (u == n - 1) return (int) t;      // reached destination

            for (int[] e : graph.get(u)) {
                int v = e[0], s = e[1], eEnd = e[2];
                if (t > eEnd) continue;          // edge already closed
                long depart = Math.max(s, t);
                long arrive = depart + 1;
                if (arrive < dist[v]) {
                    dist[v] = arrive;
                    pq.offer(new long[]{arrive, v});
                }
            }
        }
        return -1;   // unreachable
    }
}
```

---

### 2.2  Python

```python
import heapq
from typing import List

class Solution:
    def minTime(self, n: int, edges: List[List[int]]) -> int:
        graph = [[] for _ in range(n)]
        for u, v, s, e in edges:
            graph[u].append((v, s, e))

        INF = 10**20
        dist = [INF] * n
        dist[0] = 0

        pq = [(0, 0)]            # (time, node)
        while pq:
            t, u = heapq.heappop(pq)
            if t != dist[u]:
                continue
            if u == n - 1:
                return t

            for v, s, e_end in graph[u]:
                if t > e_end:
                    continue
                depart = max(s, t)
                arrive = depart + 1
                if arrive < dist[v]:
                    dist[v] = arrive
                    heapq.heappush(pq, (arrive, v))

        return -1
```

---

### 2.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minTime(int n, vector<vector<int>>& edges) {
        vector<vector<array<int, 3>>> g(n);
        for (auto& e : edges) {                // {to, start, end}
            g[e[0]].push_back({e[1], e[2], e[3]});
        }

        const long long INF = 4e18;
        vector<long long> dist(n, INF);
        dist[0] = 0;

        using P = pair<long long, int>;        // (time, node)
        priority_queue<P, vector<P>, greater<P>> pq;
        pq.push({0, 0});

        while (!pq.empty()) {
            auto [t, u] = pq.top(); pq.pop();
            if (t != dist[u]) continue;           // stale
            if (u == n - 1) return (int)t;

            for (auto& e : g[u]) {
                int v = e[0], s = e[1], eEnd = e[2];
                if (t > eEnd) continue;
                long long depart = max<long long>(s, t);
                long long arrive = depart + 1;
                if (arrive < dist[v]) {
                    dist[v] = arrive;
                    pq.push({arrive, v});
                }
            }
        }
        return -1;
    }
};
```

---

## 3.  “What if?” – Edge Cases & Common Pitfalls

| Situation | How the algorithm handles it | Why it’s safe |
|-----------|-----------------------------|---------------|
| **Multiple edges between the same pair** | All are stored in the adjacency list. The algorithm picks the *earliest* possible arrival. | Distances are updated only if strictly better. |
| **Edge start > end** | Such an edge can never be used (the relaxation step simply ignores it). | No extra cost. |
| **Very large time windows** | `dist` uses `long` / `int64` so overflow cannot happen. | Even a window of `10^9` is fine. |
| **Stale heap entries** | The check `if t != dist[u]` discards them. | Guarantees correctness and keeps the heap small. |
| **Waiting for a long period** | `depart = max(start, t)` will push the heap entry out far into the future – that is exactly what “waiting” means. | The algorithm is still linear in the number of relaxations. |

---

## 4.  “Good‑to‑Know” – Why Some Naïve Approaches Fail

1. **BFS only** – BFS assumes every edge is always available.  
   The time‑dependent constraint means you can arrive at a node *later* via a different path that waits for a better edge.  
   BFS would never “know” to wait, so it can return an *over‑optimistic* answer or miss a feasible path entirely.

2. **DP by time (DP[t][u])** –  
   If you try to pre‑compute all possible times up to the maximum `end`, you would need a table of size `O(max_time * n)` which is astronomically large (`max_time` can be `10^9`).  
   Hence the Dijkstra‑style approach is the only practical way.

---

## 5.  “Good‑to‑Know” – Why This Problem is a Classic Interview Question

| Feature | Why it matters |
|---------|----------------|
| **Time‑dependent edges** | Shows you can extend classical algorithms to handle dynamic constraints. |
| **Large input (10^5)** | Forces you to use efficient data structures (`heap`, adjacency list) instead of naive recursion or matrixes. |
| **Exact integer traversal** | Keeps the problem discrete – useful for teaching how to deal with “waiting” in graph problems. |
| **Return -1** | Forces you to think about *reachability* and stale entries. |

A strong candidate will:

* Explain the monotonicity argument.  
* Re‑implement the relaxation step.  
* Discuss edge‑case handling (closed edges, stale heap entries).  
* Be able to prove correctness in a few sentences.  

---

## 6.  Quick Testing

You can copy‑paste any of the three implementations into LeetCode’s “Java”, “Python 3”, or “C++ (GCC 17)” editor and run the following tests:

```java
int n = 4;
int[][] edges = {
    {0, 1, 0, 5},
    {1, 3, 4, 6},
    {0, 2, 2, 4},
    {2, 3, 5, 7}
};
assert new Solution().minTime(n, edges) == 5;   // Expected answer
```

```python
sol = Solution()
assert sol.minTime(4, [[0,1,0,5],[1,3,4,6],[0,2,2,4],[2,3,5,7]]) == 5
```

```cpp
Solution s;
int ans = s.minTime(4, {{0,1,0,5},{1,3,4,6},{0,2,2,4},{2,3,5,7}});
assert(ans == 5);
```

---

## 7.  Final Thoughts

- **Why Dijkstra still works** – The waiting step is monotonic, so earlier is always better.
- **Why this is a good interview question** – It blends classic shortest‑path logic with a subtle time‑dependent twist, requiring a clear explanation of monotonicity and optimal substructure.
- **Common mistakes** – Forgetting to use `Long.MAX_VALUE` / `10**20` for the initial `dist` values, or pushing stale heap entries (which can dramatically increase runtime).

Happy coding, and good luck on your next interview!