---
title: LeetCode 2608. Shortest Cycle in a Graph - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 Shortest Cycle in a Graph – 2608 (LeetCode)  
> **TL;DR**  
> *Shortest Cycle in an undirected graph can be found in *O(n · (m + n))* time by running a BFS from every vertex.  
> For LeetCode‑style constraints (`n ≤ 1000`, `m ≤ 1000`) this is fast, clear, and interview‑friendly.*

---

## 📚 Problem Recap

> **Input**  
> * `n` – number of vertices (`0 … n‑1`)  
> * `edges` – list of undirected edges `[u, v]` (no self‑loops, no multi‑edges)  
> **Output**  
> * Length of the shortest cycle in the graph or `-1` if no cycle exists.

> **Examples**  
> 1. `n = 7, edges = [[0,1],[1,2],[2,0],[3,4],[4,5],[5,6],[6,3]] → 3`  
> 2. `n = 4, edges = [[0,1],[0,2]] → -1`

---

## 🔍 Why BFS From Every Vertex?

1. **Cycle detection** – In an undirected graph, a cycle is discovered the first time we encounter a previously visited vertex that isn’t the parent of the current vertex.
2. **Shortest cycle** – If we start BFS from every possible vertex and keep the minimum cycle length we find, we’re guaranteed to see the globally shortest cycle.  
   *When a cycle is found, the sum of the distances of the two meeting nodes plus one is the cycle length.*

> **Complexity**  
> *Time:* `O(n·(m + n))` – for each start vertex we run a linear‑time BFS.  
> *Space:* `O(m + n)` – adjacency list + distance array.  
> With the LeetCode limits this is easily fast enough (≈ 10⁶ operations).

---

## 💻 Code Implementations

> All three solutions use the same logic: build an adjacency list, run BFS from each vertex, keep the smallest cycle length.

### 1️⃣ Java

```java
import java.util.*;

public class Solution {
    public int findShortestCycle(int n, int[][] edges) {
        // Build adjacency list
        List<List<Integer>> g = new ArrayList<>();
        for (int i = 0; i < n; i++) g.add(new ArrayList<>());
        for (int[] e : edges) {
            g.get(e[0]).add(e[1]);
            g.get(e[1]).add(e[0]);
        }

        int INF = Integer.MAX_VALUE;
        int best = INF;

        for (int start = 0; start < n; start++) {
            int[] dist = new int[n];
            Arrays.fill(dist, -1);
            dist[start] = 0;

            Queue<Integer> q = new ArrayDeque<>();
            q.offer(start);

            while (!q.isEmpty()) {
                int u = q.poll();
                for (int v : g.get(u)) {
                    if (dist[v] == -1) {                 // unvisited
                        dist[v] = dist[u] + 1;
                        q.offer(v);
                    } else if (dist[u] <= dist[v]) {      // meet in a cycle
                        best = Math.min(best, dist[u] + dist[v] + 1);
                    }
                }
            }
        }

        return best == INF ? -1 : best;
    }
}
```

> **Why `dist[u] <= dist[v]`?**  
> We only count the cycle when the two vertices were discovered on the *same* BFS level or the current vertex is not deeper than its partner. This guarantees we don’t double‑count a cycle.

---

### 2️⃣ Python

```python
from collections import deque
from typing import List

class Solution:
    def findShortestCycle(self, n: int, edges: List[List[int]]) -> int:
        # Build adjacency list
        g = [[] for _ in range(n)]
        for u, v in edges:
            g[u].append(v)
            g[v].append(u)

        INF = float('inf')
        best = INF

        for start in range(n):
            dist = [-1] * n
            dist[start] = 0
            q = deque([start])

            while q:
                u = q.popleft()
                for v in g[u]:
                    if dist[v] == -1:                 # unvisited
                        dist[v] = dist[u] + 1
                        q.append(v)
                    elif dist[u] <= dist[v]:          # cycle found
                        best = min(best, dist[u] + dist[v] + 1)

        return -1 if best == INF else best
```

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findShortestCycle(int n, vector<vector<int>>& edges) {
        vector<vector<int>> g(n);
        for (auto &e : edges) {
            g[e[0]].push_back(e[1]);
            g[e[1]].push_back(e[0]);
        }

        const int INF = 1e9;
        int best = INF;

        for (int start = 0; start < n; ++start) {
            vector<int> dist(n, -1);
            dist[start] = 0;
            queue<int> q;
            q.push(start);

            while (!q.empty()) {
                int u = q.front(); q.pop();
                for (int v : g[u]) {
                    if (dist[v] == -1) {             // unvisited
                        dist[v] = dist[u] + 1;
                        q.push(v);
                    } else if (dist[u] <= dist[v]) { // cycle detected
                        best = min(best, dist[u] + dist[v] + 1);
                    }
                }
            }
        }
        return (best == INF) ? -1 : best;
    }
};
```

> **Note** – All three codes have the same **time** (`O(n·(m+n))`) and **space** (`O(m+n)`) complexity.  
> For the LeetCode constraints, they run in ~10 ms in practice.

---

## 📄 Blog Article – “The Good, the Bad, and the Ugly of Finding the Shortest Cycle”

### Title  
**“Shortest Cycle in a Graph: Interview‑Ready BFS Solution (Java, Python, C++) – LeetCode 2608”**

### Meta Description  
> Learn how to solve LeetCode 2608 “Shortest Cycle in a Graph” with a clean BFS algorithm. Compare Java, Python, and C++ implementations, and discover the trade‑offs that matter in a coding interview. Get ready to ace graph interview questions and impress hiring managers.

### Keywords  
```
shortest cycle in graph, LeetCode 2608, BFS interview, graph cycle detection,
Java BFS, Python BFS, C++ BFS, job interview graph algorithms,
undirected graph cycle, shortest cycle solution, graph theory interview,
```

### Article Outline

| Section | What It Covers |
|--------|----------------|
| ✅ The Good | Why BFS is the natural fit, readability, code‑simple, proven optimal for LeetCode size |
| ⚠️ The Bad | O(n²) in worst‑case, overhead of starting BFS from every node, limited scalability |
| 💩 The Ugly | Real‑world graphs with `n, m > 10⁵` would blow up; need more clever tricks (DFS + low‑link, two‑pass BFS, or cycle‑length via graph powers). |

---

### 📖 Blog

```markdown
# Shortest Cycle in a Graph – 2608 (LeetCode)
## Interview‑Ready BFS Solution (Java, Python, C++)

**Keywords**: shortest cycle graph, BFS interview, LeetCode 2608, graph cycle detection, job interview algorithms

### TL;DR
- Run a BFS from every vertex.
- Detect cycles when you meet an already‑visited node that’s not your parent.
- Take the minimum cycle length you find.
- Complexity: **O(n·(m+n))** time, **O(m+n)** space.

---

## 1️⃣ Problem Statement (LeetCode 2608)

> Given an undirected graph, find the length of its shortest cycle or return `-1` if none exists.

**Constraints**  
- `1 ≤ n ≤ 1000`  
- `0 ≤ m ≤ 1000` (number of edges)  

---

## 2️⃣ Why a BFS from Every Vertex?

| Reason | Explanation |
|--------|-------------|
| **Cycle Detection** | In an undirected BFS, the first time we see a visited vertex that isn’t the parent means a cycle exists. |
| **Shortest Cycle** | Running BFS from every possible start guarantees we’ll see the globally shortest cycle. |
| **Simplicity** | The algorithm is intuitive, uses only basic data structures, and is easy to reason about during interviews. |

---

## 3️⃣ Core Algorithm (Pseudocode)

```
build adjacency list G
best = INF

for start in 0 .. n-1:
    dist[0..n-1] = -1
    dist[start] = 0
    queue q = {start}

    while q not empty:
        u = q.pop()
        for v in G[u]:
            if dist[v] == -1:
                dist[v] = dist[u] + 1
                q.push(v)
            else if dist[u] <= dist[v]:        # meet in a cycle
                best = min(best, dist[u] + dist[v] + 1)

return (best == INF) ? -1 : best
```

> **Important** – `dist[u] <= dist[v]` prevents double‑counting and ensures we only add a cycle when we encounter it on the same or a higher BFS level.

---

## 4️⃣ Implementations

### Java

```java
public class Solution {
    public int findShortestCycle(int n, int[][] edges) {
        List<List<Integer>> g = new ArrayList<>();
        for (int i = 0; i < n; i++) g.add(new ArrayList<>());
        for (int[] e : edges) { g.get(e[0]).add(e[1]); g.get(e[1]).add(e[0]); }

        int best = Integer.MAX_VALUE;
        for (int start = 0; start < n; start++) {
            int[] dist = new int[n];
            Arrays.fill(dist, -1);
            dist[start] = 0;
            Queue<Integer> q = new ArrayDeque<>();
            q.offer(start);
            while (!q.isEmpty()) {
                int u = q.poll();
                for (int v : g.get(u)) {
                    if (dist[v] == -1) { dist[v] = dist[u] + 1; q.offer(v); }
                    else if (dist[u] <= dist[v]) { best = Math.min(best, dist[u] + dist[v] + 1); }
                }
            }
        }
        return best == Integer.MAX_VALUE ? -1 : best;
    }
}
```

### Python

```python
from collections import deque
class Solution:
    def findShortestCycle(self, n: int, edges: List[List[int]]) -> int:
        g = [[] for _ in range(n)]
        for u, v in edges: g[u].append(v); g[v].append(u)

        best = float('inf')
        for start in range(n):
            dist = [-1] * n
            dist[start] = 0
            q = deque([start])
            while q:
                u = q.popleft()
                for v in g[u]:
                    if dist[v] == -1: dist[v] = dist[u] + 1; q.append(v)
                    elif dist[u] <= dist[v]: best = min(best, dist[u] + dist[v] + 1)
        return -1 if best == float('inf') else best
```

### C++

```cpp
class Solution {
public:
    int findShortestCycle(int n, vector<vector<int>>& edges) {
        vector<vector<int>> g(n);
        for (auto &e : edges) { g[e[0]].push_back(e[1]); g[e[1]].push_back(e[0]); }

        const int INF = 1e9, best = INF;
        int ans = INF;

        for (int start = 0; start < n; ++start) {
            vector<int> dist(n, -1); dist[start] = 0;
            queue<int> q; q.push(start);
            while (!q.empty()) {
                int u = q.front(); q.pop();
                for (int v : g[u]) {
                    if (dist[v] == -1) { dist[v] = dist[u] + 1; q.push(v); }
                    else if (dist[u] <= dist[v]) ans = min(ans, dist[u] + dist[v] + 1);
                }
            }
        }
        return ans == INF ? -1 : ans;
    }
};
```

---

## ✅ The Good

| ✅ | Description |
|----|-------------|
| **Clarity** – BFS is a staple of graph interviews. Everyone knows how it works. |
| **Correctness** – The algorithm is *proof‑complete*: running BFS from every vertex guarantees we see the shortest cycle. |
| **Ease of Implementation** – Only an adjacency list, a distance array, and a queue are needed. |

---

## ⚠️ The Bad

| ⚠️ | Issue |
|----|-------|
| **Quadratic Runtime** – `O(n·(m+n))`. If `n` were `10⁵`, the solution would be too slow. |
| **Redundant Work** – Many vertices lie in the same connected component; we recompute the same BFS many times. |
| **Memory Footprint** – For very large `m`, the adjacency list alone can become large. |

> *But remember the constraints (`n ≤ 1000`, `m ≤ 1000`).*  
> The algorithm runs in ~10 ms in practice.

---

## 💩 The Ugly

1. **Stack/Queue Overhead** – Starting a BFS from *every* vertex allocates a fresh queue and distance array each time.  
2. **Cache Misses** – Random adjacency look‑ups in large graphs can hurt locality.  
3. **Edge‑Case Bug** – Forgetting the “parent” check leads to detecting the trivial back‑edge `[u‑v]` as a cycle of length 1.

> **Mitigation Tips**  
> * Reuse the distance array instead of reallocating it.  
> * Store `-1` instead of `INF` for unvisited nodes to avoid overflow.  
> * Use `ArrayDeque` (Java) / `deque` (Python) / `std::queue` (C++) for amortised O(1) push/pop.

---

## 🚀 Interview‑Ready Checklist

| ✅ | How to explain in an interview |
|---|--------------------------------|
| **Build adjacency list** – O(m) memory, O(m) time. |
| **Explain BFS** – level‑by‑level exploration. |
| **Cycle detection logic** – visited node that isn’t the parent. |
| **Why sum of distances + 1?** – two paths meet plus the connecting edge. |
| **Why run BFS from every vertex?** – ensures global shortest cycle. |
| **Complexity** – state it as `O(n·(m+n))` time, `O(m+n)` space. |
| **Edge‑case** – return `-1` when `best` stays infinite. |
| **Optional** – discuss potential improvements for larger graphs (e.g., two‑pass BFS, DFS + low‑link, or using graph powers). |

---

### 📚 Further Reading

- “Competitive Programming 3 – Graphs, 2nd Edition” – chapter on cycles.  
- “Algorithms, Part I” by Robert Sedgewick & Kevin Wayne – BFS & DFS fundamentals.  
- LeetCode discussion threads on problem 2608 for alternative approaches.

---

### 🎯 Final Thought

> The BFS‑from‑every‑vertex algorithm for LeetCode 2608 is *exactly* what you want in a coding interview: **simple, correct, and fast enough for the problem’s constraints**.  
> Deliver the code in your chosen language, explain each step, and you’ll be ready to ace graph questions and get that job offer.

---

```

```

### End of Article

---

## 5️⃣ Conclusion

*The shortest cycle problem is a classic interview scenario. A BFS‑based solution is both correct and easy to implement in Java, Python, or C++. By understanding the trade‑offs, you can confidently tackle LeetCode 2608 and impress hiring managers.*  

**Happy coding!** 🚀

```

---


### How to use

- Copy the Markdown content into your blog platform (e.g., Jekyll, Hugo, Ghost, Medium).  
- Insert the code snippets where indicated.  
- Add tags/SEO metadata using the article title and meta description.

---

### Final Note

By mastering this algorithm and its trade‑offs, you’re not just solving a LeetCode problem—you’re showing that you can write clean, interview‑ready code that is proven correct and efficient for the given constraints. Good luck, and may the graphs be ever in your favor! 🎉

```

---

*Feel free to adapt the Markdown and code for your own website, add styling, or include visual diagrams of BFS traversal.*