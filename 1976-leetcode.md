---
title: LeetCode 1976. Number of Ways to Arrive at Destination - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. 3‑Language Solution for LeetCode 1976  
**Problem** – *Number of Ways to Arrive at Destination* (Medium)

> Given an undirected weighted graph, count how many different shortest
> paths exist from node **0** to node **n‑1**.  
> Return the count modulo **1 000 000 007**.

---

### 1.1  Algorithm Recap

1. **Graph construction** – adjacency list  
2. **Dijkstra + DP**  
   * `dist[u]` – shortest known distance from 0 to *u*  
   * `ways[u]` – number of shortest paths to *u*  
   * When a strictly shorter distance to a neighbor is found, update
     both `dist` and `ways`.  
   * When a path with *equal* distance is found, add the number of ways
     of the current node to the neighbor’s ways (modulo M).
3. Return `ways[n-1]`.

Time complexity: **O(E log V)**  
Space complexity: **O(V + E)**

---

## 2. Code

Below you will find a **clean, idiomatic** implementation in **Python 3**, **Java 17**, and **C++17**.

> All codes are self‑contained, compile on the standard LeetCode environment,
> and contain a single method `countPaths` that matches the problem signature.

---

### 2.1  Python 3

```python
import heapq
from typing import List

class Solution:
    MOD = 10 ** 9 + 7

    def countPaths(self, n: int, roads: List[List[int]]) -> int:
        # Build adjacency list
        graph = [[] for _ in range(n)]
        for u, v, w in roads:
            graph[u].append((v, w))
            graph[v].append((u, w))

        dist = [float('inf')] * n
        ways = [0] * n
        dist[0], ways[0] = 0, 1

        pq = [(0, 0)]                     # (distance, node)

        while pq:
            d, u = heapq.heappop(pq)
            if d != dist[u]:              # stale entry
                continue

            for v, w in graph[u]:
                nd = d + w
                if nd < dist[v]:          # better distance
                    dist[v] = nd
                    ways[v] = ways[u]
                    heapq.heappush(pq, (nd, v))
                elif nd == dist[v]:       # another shortest path
                    ways[v] = (ways[v] + ways[u]) % self.MOD

        return ways[n - 1] % self.MOD
```

---

### 2.2  Java 17

```java
import java.util.*;

class Solution {
    private static final long MOD = 1_000_000_007L;

    public int countPaths(int n, int[][] roads) {
        // adjacency list
        List<int[]>[] g = new ArrayList[n];
        for (int i = 0; i < n; i++) g[i] = new ArrayList<>();
        for (int[] e : roads) {
            int u = e[0], v = e[1], w = e[2];
            g[u].add(new int[]{v, w});
            g[v].add(new int[]{u, w});
        }

        long[] dist = new long[n];
        long[] ways = new long[n];
        Arrays.fill(dist, Long.MAX_VALUE);
        dist[0] = 0;
        ways[0] = 1;

        // min‑heap (distance, node)
        PriorityQueue<long[]> pq = new PriorityQueue<>(Comparator.comparingLong(a -> a[0]));
        pq.offer(new long[]{0, 0});

        while (!pq.isEmpty()) {
            long[] cur = pq.poll();
            long d = cur[0];
            int u = (int) cur[1];
            if (d != dist[u]) continue;          // stale entry

            for (int[] e : g[u]) {
                int v = e[0];
                long w = e[1];
                long nd = d + w;
                if (nd < dist[v]) {
                    dist[v] = nd;
                    ways[v] = ways[u];
                    pq.offer(new long[]{nd, v});
                } else if (nd == dist[v]) {
                    ways[v] = (ways[v] + ways[u]) % MOD;
                }
            }
        }

        return (int) (ways[n - 1] % MOD);
    }
}
```

---

### 2.3  C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static constexpr long long MOD = 1'000'000'007LL;

    int countPaths(int n, vector<vector<int>>& roads) {
        vector<vector<pair<int, int>>> g(n);
        for (auto &e : roads) {
            int u = e[0], v = e[1], w = e[2];
            g[u].push_back({v, w});
            g[v].push_back({u, w});
        }

        vector<long long> dist(n, LLONG_MAX);
        vector<long long> ways(n, 0);
        dist[0] = 0;
        ways[0] = 1;

        using P = pair<long long, int>;                // (dist, node)
        priority_queue<P, vector<P>, greater<P>> pq;
        pq.emplace(0, 0);

        while (!pq.empty()) {
            auto [d, u] = pq.top(); pq.pop();
            if (d != dist[u]) continue;                // stale

            for (auto &[v, w] : g[u]) {
                long long nd = d + w;
                if (nd < dist[v]) {
                    dist[v] = nd;
                    ways[v] = ways[u];
                    pq.emplace(nd, v);
                } else if (nd == dist[v]) {
                    ways[v] = (ways[v] + ways[u]) % MOD;
                }
            }
        }

        return static_cast<int>(ways[n - 1] % MOD);
    }
};
```

---

## 3. Blog Article – “The Good, The Bad, and The Ugly of Counting Shortest Paths”

> **Target Audience:** System‑design interview candidates, data‑structure enthusiasts, and job‑seekers looking for a standout LeetCode solution.

> **SEO Keywords:** LeetCode 1976, shortest path counting, Dijkstra DP, algorithm interview, Python LeetCode solutions, Java interview problems, C++ algorithm, counting shortest routes, job interview coding

---

### 3.1  Title & Meta

| Field | Content |
|-------|---------|
| **Title** | The Good, The Bad, & The Ugly of Counting Shortest Paths – LeetCode 1976 |
| **Meta Description** | Master LeetCode 1976 with a clean Dijkstra‑DP solution in Python, Java, and C++. Learn the trade‑offs, pitfalls, and interview‑ready code that will impress hiring managers. |
| **Keywords** | LeetCode 1976, shortest path, Dijkstra, dynamic programming, interview coding, counting routes, Python solution, Java solution, C++ solution |

---

### 3.2  Outline

1. **Introduction** – why this problem matters  
2. **The Good** – algorithmic brilliance of Dijkstra + DP  
3. **The Bad** – tricky edge cases and pitfalls  
4. **The Ugly** – common mistakes in interview settings  
5. **Code Walkthrough** – step‑by‑step for Python, Java, C++  
6. **Performance Checklist** – what hiring managers check  
7. **Take‑away** – how to talk about it in an interview  
8. **Bonus** – alternative approaches & when to use them  

---

### 3.3  Article Body

> *Feel free to copy‑paste this markdown into your blog platform.*

```markdown
# The Good, The Bad & The Ugly of Counting Shortest Paths – LeetCode 1976

When you see a problem that asks *“how many ways can I reach a destination in the least time?”* the instinct is to think Dijkstra or Floyd–Warshall.  
But the twist? **You have to count the paths, not just find the distance.**  
Below we dissect the problem, uncover hidden pitfalls, and present clean, interview‑ready solutions in **Python, Java, and C++**.

## 1. Why This Problem Is a Gold‑Mine for Interviews

- **Graph fundamentals** – nodes, edges, weights  
- **Shortest path algorithm** – Dijkstra’s classic use case  
- **Dynamic programming on graphs** – counting with memoization  
- **Modulo arithmetic** – handling big numbers  
- **Edge‑case sensitivity** – large weights, dense graphs, disconnected edges (but problem guarantees connectivity)

If you nail this, you demonstrate mastery of all core CS concepts that recruiters love.

## 2. The Good – The Algorithm That Wins

> **Idea**: Run Dijkstra once, *simultaneously* keep a counter of how many shortest paths reach each node.

| Step | Explanation | Why it works |
|------|-------------|--------------|
| **Build adjacency list** | O(E) space | Faster traversal, less overhead than matrix |
| **Initialize** | `dist[0]=0`, `ways[0]=1` | Base case: one way to stay at the start |
| **Priority queue** | Min‑heap of `(distance, node)` | Guarantees we always pop the globally shortest unsettled node |
| **Relaxation** | If new distance `< dist[v]` → update `dist[v]` and `ways[v]` = `ways[u]` | Classic Dijkstra improvement |
| **Equality case** | If new distance == `dist[v]` → `ways[v] += ways[u]` | Every shortest route to `u` extends to a new shortest route to `v` |

This **one‑pass** approach runs in `O(E log V)`—optimal for sparse graphs.

## 3. The Bad – Edge Cases That Break the “Straight‑Forward” Mindset

| Pitfall | What Happens | Fix |
|---------|--------------|-----|
| **Stale heap entries** | Multiple pushes for same node | Check `if d != dist[u]` before exploring |
| **Very large weights (1e6)** | Distances can overflow 32‑bit ints | Use `long`/`long long` (or `float('inf')` in Python) |
| **Modulo after addition only** | You might forget to mod after every addition | `ways[v] = (ways[v] + ways[u]) % MOD` |
| **Graph with parallel edges** | Problem states simple graph, but if not | You still need to keep all edges, Dijkstra handles it |

Being alert to these ensures your solution doesn’t crash on hidden tests.

## 4. The Ugly – Interview “Red Flags”

1. **Using `dist` as a boolean visited array**  
   - Confuses shortest‑distance logic; you’ll miss multiple shortest paths.  
2. **Forgetting to pop stale heap entries**  
   - Causes `O(V^2)` blow‑up in worst case.  
3. **Modding only at the very end**  
   - Integer overflow or incorrect modulo result.  
4. **Hard‑coding `int` for distances**  
   - Works for small weights but fails on `w=10^6` and many edges.  

If you see any of these in your own code, you’re risking a 0 in an interview.

## 5. Code Walkthrough – Three Languages

> Below you can copy the implementation directly into your IDE or LeetCode.

### Python 3

```python
import heapq
from typing import List

class Solution:
    MOD = 10 ** 9 + 7
    def countPaths(self, n: int, roads: List[List[int]]) -> int:
        # adjacency list
        graph = [[] for _ in range(n)]
        for u, v, w in roads:
            graph[u].append((v, w))
            graph[v].append((u, w))

        dist = [float('inf')] * n
        ways = [0] * n
        dist[0], ways[0] = 0, 1
        pq = [(0, 0)]

        while pq:
            d, u = heapq.heappop(pq)
            if d != dist[u]:  # stale
                continue
            for v, w in graph[u]:
                nd = d + w
                if nd < dist[v]:
                    dist[v] = nd
                    ways[v] = ways[u]
                    heapq.heappush(pq, (nd, v))
                elif nd == dist[v]:
                    ways[v] = (ways[v] + ways[u]) % self.MOD

        return ways[n-1] % self.MOD
```

*Why this is clean:*  
- One `if` per neighbor – no hidden loops.  
- All state updated together – no separate DP pass.

### Java 17

*(see code in section 2.2 above)*

### C++17

*(see code in section 2.3 above)*

## 6. Performance Checklist – What Recruiters Will Notice

| Checklist | How to answer |
|-----------|---------------|
| **Time Complexity** | `O(E log V)` – explain why the heap is needed |
| **Space Complexity** | `O(V + E)` – adjacency list, two arrays |
| **Large Weights** | Use `long`/`long long` or `float('inf')` – avoid overflow |
| **Modulo** | Mention that we take modulo **every** addition |
| **Heap Staleness** | Explain the stale‑check guard |

### 7. Interview‑Ready Talking Points

> “I’m using a *modified Dijkstra* where I keep an auxiliary `ways[]` array.  
> Each time we discover a new shortest distance to a vertex we overwrite its
> `ways[]`; if we find an alternative path of equal length, we accumulate the
> ways from the current node.  
> This guarantees that when we finally pop the target node, `ways[n-1]`
> already contains the total number of shortest paths.”

If the interviewer asks *“why not run BFS after Dijkstra?”*, explain that BFS would be `O(V+E)` per level and would still need the distance array; it doesn’t give a performance advantage here.

## 8. Take‑away

- **Show the dual role** of Dijkstra (distance) + DP (count).  
- **Mention modulo** early – recruiters will nod to your arithmetic skills.  
- **Explain why a min‑heap is necessary** – O(E log V) beats O(E V) for sparse graphs.

With the code snippets above, you have a ready‑to‑copy, *interview‑friendly* solution that covers the entire spectrum from graph fundamentals to big‑integer handling.

Happy coding—and happy interviewing!

```

---

### 3.4  Performance Checklist

| Metric | Target |
|--------|--------|
| **Runtime** | ≤ 0.3 s on LeetCode (Python) / ≤ 0.5 s (Java, C++) for worst‑case dense graph |
| **Memory** | < 64 MB – adjacency list is the preferred choice |
| **Code clarity** | No unnecessary helper functions; single pass, single loop |
| **Commenting** | Minimal but sufficient to explain key steps |

---

## 4. Final Thoughts

Counting shortest paths is more than a toy; it is a *signal* that you can **combine classical algorithms with DP**.  
Present the solution, walk through the code, and you’ll leave the interviewers with confidence that you can handle **any graph‑theory challenge**.

--- 
```

---

## 4. What to Emphasize During an Interview

| Topic | Talking Point |
|-------|----------------|
| **Why Dijkstra works** | Guarantees minimal distances in a graph with non‑negative weights |
| **Simultaneous counting** | DP on top of Dijkstra, no extra passes |  
| **Stale entries** | `if (d != dist[u]) continue;` protects against quadratic blow‑up |
| **Modulo** | Constant `1_000_000_007` – why we need it (int overflow, huge counts) |
| **Complexity** | O(E log V), fits within constraints |

---

### 5. Conclusion

You now possess:

- **Theoretical understanding** of a *hybrid* algorithm  
- **Practically verified code** in three major languages  
- **Interview narrative** that highlights strengths, avoids pitfalls, and shows confidence

Use this knowledge to ace LeetCode 1976 and *any* shortest‑path counting interview question that follows.

> Good luck – may your paths always be shortest and your counts always correct! 🚀
```

---

### 4.1  Optional Discussion: Alternatives

| Approach | When to Use | Pros | Cons |
|----------|-------------|------|------|
| **Bellman–Ford + DP** | Graph may contain negative edges (not in this problem) | Simpler to code, works with negative weights | O(VE) – slower on large graphs |
| **DP + Floyd–Warshall** | Small dense graph (n ≤ 500) | Gives all‑pairs shortest distances | O(n³) memory & time, not scalable |
| **Counting with BFS after Dijkstra** | You already know distances, only need counts | Simple two‑pass solution | Requires storing all distances first; still O(E log V) + O(V+E) |

In most interview contexts, **Dijkstra + DP** remains the gold standard.

---

### 5. End Note

Feel free to adapt the article for your personal portfolio or as a reference guide for fellow interviewees. The combination of a solid algorithm, clean code, and a thoughtful explanation will help you stand out in the competitive world of technical hiring. Happy coding! 🚀
```

---

> **Happy interviewing!** If you have any questions about the code or the article, drop a comment or reach out on LinkedIn – let’s solve more problems together. 🚀
```

--- 

> **Good luck,** and may all your paths be the shortest!