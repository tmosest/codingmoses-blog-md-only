---
title: LeetCode 2714. Find Shortest Path with K Hops - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The problem in a nutshell  

> **LeetCode 2714 – “Find Shortest Path with K Hops”**  
> You’re given a connected, undirected, weighted graph with `n` vertices (0‑indexed).  
> Each edge has a positive weight `w`.  
> Two distinct vertices `s` and `d` are supplied, and you may **zero‑out at most `k` edges** (i.e., change their weight to 0).  
> What is the minimum possible total weight of a path from `s` to `d` after you have applied up to `k` “free hops”?

```
n  <= 500
edges.length <= 10⁴
w <= 10⁶
k <= n-1
```

The input guarantees the graph is connected, has no self‑loops, and no duplicate edges.

---

## 2.  Why a simple Dijkstra won’t cut it

If you could only **skip** edges but **not** alter them, a normal Dijkstra would find the shortest weighted path.  
In this problem, you may *replace* the weight of up to `k` edges with `0`.  
You must decide *which* edges to zero out and *where* in the path you do it – the choice is coupled with the path itself.  

That means the state of the algorithm is no longer “just the vertex”; it also needs to remember how many free hops you have already used.  
This is a classic “DP + Dijkstra” (or “multi‑state Dijkstra”) pattern.

---

## 3.  The optimal solution

### 3.1  Idea

Treat each pair  
```
(state)  = (vertex, usedHops)
```
as a node in a **larger, virtual graph**.  
From a state `(u, h)` you can:

| Action | New state | Edge cost |
|--------|-----------|-----------|
| Traverse edge `(u, v, w)` **normally** | `(v, h)` | `w` |
| Traverse edge `(u, v, w)` **with a hop** | `(v, h+1)` | `0` (only if `h < k`) |

Run ordinary Dijkstra over these states.  
The first time you pop a state whose vertex is `d` you have the optimal answer, because Dijkstra’s “first‑visit‑to‑destination” property holds in this expanded graph as well.

### 3.2  Correctness proof sketch

1. **All feasible paths are represented** –  
   Every path in the original graph can be mapped to a path in the expanded graph by deciding for each edge whether it is used with a hop or not.  
   The number of hop edges used never exceeds `k` because we never transition to a state with `h > k`.

2. **Optimality preserved** –  
   Dijkstra’s algorithm guarantees that the first time a state is removed from the priority queue it has the minimum possible accumulated cost from the source.  
   Therefore, when we first encounter a state with vertex `d`, its cost is the minimum possible among *all* paths that respect the hop limit.

3. **No shorter path exists** –  
   Suppose there existed a better path to `d`.  It would correspond to a state `(d, h')` with a smaller cost that Dijkstra should have removed earlier, contradicting the algorithm’s property.

Hence the algorithm returns the exact shortest distance.

### 3.3  Complexity

```
Number of states  :  n * (k+1)   (at most 500 * 501 ≈ 250k)
Transitions per state :  degree(u)  (≤ n-1)
Total edges in virtual graph :  O((k+1) * m)
```

Dijkstra runs in  
```
O( (n*(k+1) + (k+1)*m) log(n*(k+1)) )
```
With the given limits this is well below one second in all major languages.

Space: `O(n*(k+1))` for the distance / visited tables plus the original graph `O(m)`.

---

## 4.  Reference implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.  
All three use the same state‑ful Dijkstra pattern described above.

---

### 4.1  Java (LeetCode style)

```java
import java.util.*;

/**
 * LeetCode 2714 – Find Shortest Path with K Hops
 */
public class Solution {

    /* ---------- Graph helper ---------- */
    private List<int[]>[] buildGraph(int n, int[][] edges) {
        List<int[]>[] g = new ArrayList[n];
        for (int i = 0; i < n; ++i) g[i] = new ArrayList<>();
        for (int[] e : edges) {
            int u = e[0], v = e[1], w = e[2];
            g[u].add(new int[]{v, w});
            g[v].add(new int[]{u, w});
        }
        return g;
    }

    /* ---------- State for priority queue ---------- */
    private static class State implements Comparable<State> {
        int vertex, used;
        long dist;

        State(int vertex, int used, long dist) {
            this.vertex = vertex;
            this.used   = used;
            this.dist   = dist;
        }

        @Override
        public int compareTo(State other) {
            return Long.compare(this.dist, other.dist);
        }
    }

    /* ---------- Main Dijkstra ---------- */
    public int shortestPathWithHops(int n, int[][] edges, int s, int d, int k) {
        List<int[]>[] g = buildGraph(n, edges);

        // distance[vertex][hopsUsed]
        long[][] dist = new long[n][k + 1];
        for (long[] row : dist) Arrays.fill(row, Long.MAX_VALUE);

        PriorityQueue<State> pq = new PriorityQueue<>();
        dist[s][0] = 0;
        pq.offer(new State(s, 0, 0));

        while (!pq.isEmpty()) {
            State cur = pq.poll();

            if (cur.vertex == d) return (int) cur.dist;   // first arrival is optimal

            if (cur.dist != dist[cur.vertex][cur.used]) continue; // outdated

            for (int[] e : g[cur.vertex]) {
                int nb  = e[0];
                int w   = e[1];

                // 1) traverse normally
                if (cur.dist + w < dist[nb][cur.used]) {
                    dist[nb][cur.used] = cur.dist + w;
                    pq.offer(new State(nb, cur.used, dist[nb][cur.used]));
                }

                // 2) use a hop (if we still have hops left)
                if (cur.used < k && cur.dist < dist[nb][cur.used + 1]) {
                    dist[nb][cur.used + 1] = cur.dist;
                    pq.offer(new State(nb, cur.used + 1, dist[nb][cur.used + 1]));
                }
            }
        }

        // graph is guaranteed connected, so this line never reached
        return -1;
    }
}
```

---

### 4.2  Python 3 (LeetCode style)

```python
import heapq
from typing import List

class Solution:
    def shortestPathWithHops(
        self, n: int, edges: List[List[int]],
        s: int, d: int, k: int
    ) -> int:
        # build adjacency list
        graph = [[] for _ in range(n)]
        for u, v, w in edges:
            graph[u].append((v, w))
            graph[v].append((u, w))

        INF = 10**18
        dist = [[INF] * (k + 1) for _ in range(n)]
        dist[s][0] = 0

        # (dist, vertex, hopsUsed)
        pq = [(0, s, 0)]
        while pq:
            cur, u, used = heapq.heappop(pq)
            if cur != dist[u][used]:
                continue          # stale entry

            if u == d:
                return cur       # first visit is optimal

            for v, w in graph[u]:
                # normal traversal
                if cur + w < dist[v][used]:
                    dist[v][used] = cur + w
                    heapq.heappush(pq, (dist[v][used], v, used))

                # hop (if allowed)
                if used < k and cur < dist[v][used + 1]:
                    dist[v][used + 1] = cur
                    heapq.heappush(pq, (cur, v, used + 1))

        return -1
```

---

### 4.3  C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    struct Edge { int to; int w; };
    struct State {
        int v, used; long long dist;
        bool operator<(State const& o) const { return dist > o.dist; } // min‑heap
    };

    int shortestPathWithHops(int n, vector<vector<int>>& edges,
                            int s, int d, int k) {
        vector<vector<Edge>> g(n);
        for (auto &e: edges) {
            int u = e[0], v = e[1], w = e[2];
            g[u].push_back({v,w});
            g[v].push_back({u,w});
        }

        const long long INF = 4e18;
        vector<vector<long long>> dist(n, vector<long long>(k+1, INF));
        priority_queue<State> pq;

        dist[s][0] = 0;
        pq.push({s, 0, 0});

        while (!pq.empty()) {
            State cur = pq.top(); pq.pop();
            if (cur.v == d) return (int)cur.dist;   // optimal when first popped

            if (cur.dist != dist[cur.v][cur.used]) continue; // stale

            for (auto &e : g[cur.v]) {
                int nb = e.to, w = e.w;

                // normal traversal
                if (cur.dist + w < dist[nb][cur.used]) {
                    dist[nb][cur.used] = cur.dist + w;
                    pq.push({nb, cur.used, dist[nb][cur.used]});
                }

                // hop
                if (cur.used < k && cur.dist < dist[nb][cur.used+1]) {
                    dist[nb][cur.used+1] = cur.dist;
                    pq.push({nb, cur.used+1, dist[nb][cur.used+1]});
                }
            }
        }
        return -1; // unreachable – graph is connected, so never happens
    }
};
```

---

## 5.  “Good, Bad & Ugly” – What interviewers really care about  

| Phase | What a candidate **should** do | What a candidate **should not** do |
|-------|--------------------------------|-----------------------------------|
| **Good** | *Stateful Dijkstra* – concise, uses only a single priority queue, O((k+1)n) memory. |  |
| **Bad** | BFS or DP that iterates over all `k`‑combinations of edges (exponential). |  |
| **Ugly** | Over‑engineering: writing a custom graph class that leaks memory, or mixing BFS + DFS incorrectly, or ignoring the “first‑visit‑to‑destination” property. |  |

**Why this pattern is interview‑friendly**  
- It showcases a clear understanding of **Dijkstra**, **DP**, and **state compression**.  
- It proves you can reason about *“augmented state”* problems – a frequent interview theme.  
- The solution is **linear in `k`**: you can easily explain “if `k` were 0 we’d just be Dijkstra”.  
- The code is clean, uses standard library containers, and is short enough to fit on a whiteboard.

---

## 6.  Testing your solution

| Edge cases | Expected behaviour |
|------------|--------------------|
| `k = 0` – no hops | Standard shortest path. |
| `k = n-1` – you can zero out *any* edge | The answer will always be the minimum weight edge on a *any* path; Dijkstra still works. |
| Graph with a single edge between `s` and `d` | Either use the hop (cost 0) or keep the weight. |
| Multiple equal‑weight paths | Dijkstra will automatically pick the one with the cheapest hop usage. |
| `n = 1` is impossible because `s` and `d` must be distinct – the statement guarantees `n ≥ 2`. |

Use the sample tests from LeetCode plus some custom ones:

```python
# Python snippet
print(Solution().shortestPathWithHops(
    5,
    [[0,1,5],[1,2,3],[0,2,10],[2,3,1],[3,4,4]],
    0, 4, 2
))  # Expected: 0 (skip edges 0-1 and 2-3)
```

---

## 7.  SEO‑friendly blog article

Below is a full‑length article you can copy‑paste into a Medium post, Dev.to, or your own blog.  
Feel free to tweak the title, add your own voice, or drop the `#` tags that work with most blogging platforms.

---

### 🔍 “Find Shortest Path with K Hops” – A LeetCode 2714 Deep Dive (Graph + DP)

> **Keywords**: *LeetCode 2714*, *Find Shortest Path with K Hops*, *graph algorithm interview*, *Dijkstra stateful*, *software engineer interview prep*, *free hops algorithm*, *dynamic programming + Dijkstra*, *network latency optimization*, *edge zeroing*, *graph theory data structures*

---

#### 1.  Why this problem is a “gold‑mine” for interviewers

- **Graph fundamentals** – edges, weights, connectivity.  
- **Advanced Dijkstra** – multi‑state relaxation, priority queue.  
- **Dynamic programming on graph** – the `usedHops` dimension.  
- **Complexity analysis** – log‑based runtime, tight memory usage.  
- **Edge‑case thinking** – what if you hit the hop limit?  What if all edges are zero?  

These topics surface in *software‑engineer* interviews at Google, Microsoft, Amazon, and many data‑science teams.  

---

#### 2.  Problem recap (from LeetCode)

> Given a weighted graph, two distinct vertices `s` and `d`, and an integer `k`, zero‑out *at most* `k` edges.  
> Compute the minimal total weight of any path from `s` to `d` after the operation.

Constraints guarantee a dense graph with up to 10 000 edges and 500 vertices – a sweet spot for Dijkstra.

---

#### 3.  “Good” – The clean stateful Dijkstra

1. **State = (vertex, hopsUsed)**  
   Each state is a node in a virtual graph.  
2. **Two relaxations** – normal edge vs hop edge (cost 0).  
3. **Priority queue** – `min‑heap` ordered by accumulated distance.  
4. **Early exit** – the first time we pop a state whose vertex is `d` is the optimal answer.  

**Why it’s good**

- **Time‑optimal**: `O((k+1)·(n+m) log(n(k+1)))`.  
- **Space‑efficient**: `O(n(k+1))` distance matrix.  
- **Easy to understand** on a whiteboard: “We just added one more dimension”.

---

#### 4.  “Bad” – Why naive solutions fail

| Approach | Why it’s slow or wrong |
|----------|------------------------|
| **DFS + brute force** – try every combination of `k` hops (choose `k` edges out of `m`) | Exponential time, impossible for `m = 10⁴`. |
| **DP on edge list** – compute best distance for each vertex and each hop count in a nested loop | Still `O(n²k)` which is fine, but you can’t guarantee that you’re exploring edges with the cheapest cost first; the algorithm may revisit states unnecessarily. |
| **BFS on the expanded graph but without priority queue** | BFS assumes unit weights; here edge costs vary widely (`w` up to 10⁶). |

---

#### 5.  “Ugly” – What you *must* avoid

- **Memory leaks**: allocating `vector<vector<int>>` inside a loop, forgetting to reset.  
- **Custom priority queue** that mis‑orders elements (e.g., using a `max‑heap` by accident).  
- **Mixing data structures** (DFS stack + Dijkstra PQ) in one solution – leads to confusing reasoning on the board.  
- **Printing every state** to debug – slows the interview down and shows lack of focus.  

The takeaway: keep it simple. Use language‑standard containers; don’t over‑optimize before you know the problem structure.

---

#### 6.  Code walk‑through (Python example)

```python
class Solution:
    def shortestPathWithHops(self, n, edges, s, d, k):
        graph = [[] for _ in range(n)]
        for u, v, w in edges:
            graph[u].append((v, w))
            graph[v].append((u, w))

        INF = 10**18
        dist = [[INF]*(k+1) for _ in range(n)]
        dist[s][0] = 0
        pq = [(0, s, 0)]  # (dist, vertex, hops)

        while pq:
            cur, u, used = heapq.heappop(pq)
            if cur != dist[u][used]:
                continue
            if u == d:
                return cur
            for v, w in graph[u]:
                # normal
                if cur + w < dist[v][used]:
                    dist[v][used] = cur + w
                    heapq.heappush(pq, (dist[v][used], v, used))
                # hop
                if used < k and cur < dist[v][used+1]:
                    dist[v][used+1] = cur
                    heapq.heappush(pq, (cur, v, used+1))
        return -1
```

Feel free to paste this into your IDE and run it on LeetCode’s sample tests.

---

#### 6.  Deployment & real‑world parallels

Zero‑ing an edge is akin to **caching** or **reducing latency** in a network: you’re free to “bypass” certain costly links.  
The algorithm’s pattern is useful for:

- **Optimizing routing tables** in distributed systems.  
- **Simulating budgeted upgrades** in infrastructure (e.g., how many cables can you upgrade to reduce total latency?).  

---

#### 7.  Takeaway

- **Stateful Dijkstra** is a concise, powerful solution.  
- **Whiteboard‑ready**: explain the added dimension and the two relaxations.  
- **Interviews**: talk about why you can’t use BFS and why the priority queue guarantees optimality.  
- **Show the analysis**: log‑based runtime and how you derived it.  

Give it a shot on your next interview – you’ll impress with both *correctness* and *clarity*.

---

#### 📚 Further reading

- *Introduction to Algorithms* – Dijkstra’s algorithm.  
- *Algorithms (CLRS)* – DP on graphs, state compression.  
- *Competitive Programming 3* – advanced shortest‑path tricks.  

Happy coding, and may your paths always be the shortest! 🚀

---

### 🎤 Bonus: Adding a personal touch

- Add a short anecdote of your own experience tackling a LeetCode graph problem.  
- Share a screenshot of your code on a whiteboard.  
- End with a call‑to‑action: “Got a trick for a similar problem?  Drop it in the comments!”  

---

**Enjoy your blog post,** and best of luck with those coding interviews! 🚀

--- 

That’s all – a ready‑to‑share, interview‑ready explanation of LeetCode 2714 “Find Shortest Path with K Hops”, along with clean code in Java‑style pseudocode (Java omitted for brevity), and a thorough article to showcase your expertise to hiring managers. Happy coding!