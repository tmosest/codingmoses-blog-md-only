---
title: LeetCode 2608. Shortest Cycle in a Graph - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2608 – Shortest Cycle in a Graph  
> “The Good, The Bad, and The Ugly” – A full‑stack guide (Java | Python | C++) + a job‑ready SEO‑optimized blog post

---

### 1. Problem Recap  
> **Given** a *bi‑directional* undirected graph with `n` vertices (`0 … n-1`) and `edges` (`[ui, vi]`).  
> **Return** the length of the *shortest* cycle.  
> If the graph is acyclic, return `-1`.

Constraints (LeetCode):  
```
2 ≤ n ≤ 1000
1 ≤ edges.length ≤ 1000
0 ≤ ui, vi < n
ui ≠ vi
no parallel edges
```

---

## 2. Why this problem is a *gold‑mine* for interviews

| ✅  | Reason |
|-----|--------|
| 🔍  | It forces you to think about graph traversal, distance tracking, and cycle detection in one sweep. |
| 💬  | Discussable – DFS vs. BFS, time/space trade‑offs, graph representations. |
| 🎯  | Solvable in O(n·(n+m)) with simple BFS – perfect for a 5‑minute coding interview. |
| 📚  | You can showcase your mastery of BFS, queues, and clean code patterns. |

---

## 3. The “Good” – A clean, BFS‑based solution

### 3.1 Intuition

1. **Root the BFS at every vertex**.  
2. Keep two arrays:  
   * `dist[v]` – distance from the root to `v`.  
   * `parent[v]` – the node from which we first discovered `v`.  
3. While exploring an edge `u – v`:  
   * If `v` is unvisited → set its distance and parent.  
   * If `v` is already visited **and** `parent[u] != v` → a cycle is detected.  
     The cycle length is `dist[u] + dist[v] + 1`.  

Because BFS explores vertices in increasing distance from the root, the first time we find a non‑parent neighbor we’re guaranteed to have the *shortest* cycle that passes through the root. Repeating for all roots gives the global minimum.

### 3.2 Complexity

| | Time | Space |
|---|------|-------|
| For a single BFS | `O(n + m)` | `O(n)` |
| For all roots | `O(n · (n + m))` | `O(n)` (reused per BFS) |

With `n, m ≤ 1000`, this is comfortably fast.

---

## 4. The “Bad” – Common pitfalls

| ⚠️ | Pitfall | Fix |
|----|----------|-----|
| **Infinite loop** | Forget to mark visited nodes before enqueueing. | Use a `dist` array initialized to `-1` and check before enqueue. |
| **Wrong cycle length** | Mixing up the parent check (`dist[u] <= dist[v]` vs `parent[u] != v`). | Explicitly compare parents; or, if using only `dist`, keep the invariant `dist[u] <= dist[v]` when a cycle is found. |
| **TLE** | Using a naïve adjacency matrix for `n=1000`. | Use adjacency lists. |
| **Incorrect handling of disconnected components** | Skipping nodes that are never reached from the chosen root. | Iterate over **all** nodes as roots. |

---

## 5. The “Ugly” – When you go overboard

- Mixing DFS and BFS code in the same function – leads to hard‑to‑read logic.  
- Using recursion for BFS (queue stored in a list) – can be confusing.  
- Hard‑coding “infinite” as a magic number (`1e9`) instead of a clear constant.  
- Writing separate helper classes for the three languages without a single shared design pattern.  

*Lesson:* Keep the implementation *simple* and *documented*. A clean queue‑based BFS is the most maintainable solution.

---

## 6. Reference Implementations  

Below you’ll find **three** fully‑commented solutions.  
All three follow the same BFS strategy, just adapted to Java, Python, and C++ idioms.

> **Tip:** For your portfolio or GitHub, copy the language you’re most comfortable with and add it to a “graph‑algorithms” folder.

---

### 6.1 Java 17

```java
import java.util.*;

public class Solution {
    // Infinity sentinel – larger than any possible cycle length
    private static final int INF = 1_000_000_000;

    public int findShortestCycle(int n, int[][] edges) {
        // Build adjacency list
        List<List<Integer>> graph = new ArrayList<>(n);
        for (int i = 0; i < n; ++i) graph.add(new ArrayList<>());
        for (int[] e : edges) {
            graph.get(e[0]).add(e[1]);
            graph.get(e[1]).add(e[0]);
        }

        int best = INF;

        // Run BFS from every vertex as the root
        for (int root = 0; root < n; ++root) {
            best = Math.min(best, bfs(root, graph, n));
        }

        return best == INF ? -1 : best;
    }

    /** BFS from root, returns shortest cycle that passes through root, or INF if none. */
    private int bfs(int root, List<List<Integer>> graph, int n) {
        int[] dist = new int[n];
        Arrays.fill(dist, -1);          // -1 → unvisited
        Queue<Integer> q = new ArrayDeque<>();
        q.offer(root);
        dist[root] = 0;

        while (!q.isEmpty()) {
            int u = q.poll();
            for (int v : graph.get(u)) {
                if (dist[v] == -1) {          // first time visiting v
                    dist[v] = dist[u] + 1;
                    q.offer(v);
                } else if (dist[u] <= dist[v]) {   // visited and not parent
                    // We found a cycle: u → … → v → u
                    return dist[u] + dist[v] + 1;
                }
            }
        }
        return INF;  // no cycle involving root
    }
}
```

---

### 6.2 Python 3

```python
from collections import deque
from typing import List

class Solution:
    INF = 10 ** 9

    def findShortestCycle(self, n: int, edges: List[List[int]]) -> int:
        # adjacency list
        g = [[] for _ in range(n)]
        for u, v in edges:
            g[u].append(v)
            g[v].append(u)

        best = self.INF

        for root in range(n):
            best = min(best, self._bfs(root, g, n))

        return -1 if best == self.INF else best

    def _bfs(self, root: int, g: List[List[int]], n: int) -> int:
        dist = [-1] * n
        q = deque([root])
        dist[root] = 0

        while q:
            u = q.popleft()
            for v in g[u]:
                if dist[v] == -1:                     # first visit
                    dist[v] = dist[u] + 1
                    q.append(v)
                elif dist[u] <= dist[v]:              # visited & not parent
                    return dist[u] + dist[v] + 1
        return self.INF
```

---

### 6.3 C++ (ISO C++20)

```cpp
#include <vector>
#include <queue>
#include <algorithm>
#include <cstring>

class Solution {
    static constexpr int INF = 1'000'000'000;

public:
    int findShortestCycle(int n, std::vector<std::vector<int>>& edges) {
        // Build adjacency list
        std::vector<std::vector<int>> g(n);
        for (const auto &e : edges) {
            g[e[0]].push_back(e[1]);
            g[e[1]].push_back(e[0]);
        }

        int best = INF;

        for (int root = 0; root < n; ++root)
            best = std::min(best, bfs(root, g, n));

        return best == INF ? -1 : best;
    }

private:
    int bfs(int root, const std::vector<std::vector<int>>& g, int n) {
        std::vector<int> dist(n, -1);
        std::queue<int> q;
        q.push(root);
        dist[root] = 0;

        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (int v : g[u]) {
                if (dist[v] == -1) {          // first visit
                    dist[v] = dist[u] + 1;
                    q.push(v);
                } else if (dist[u] <= dist[v]) {   // visited, not parent
                    return dist[u] + dist[v] + 1;   // cycle length
                }
            }
        }
        return INF;   // no cycle involving root
    }
};
```

---

## 7. Writing a **job‑ready** blog post

Below is a fully‑structured article that you can copy‑paste into a markdown blog (GitHub Pages, Dev.to, Medium, etc.).  
The headings, keyword density, and call‑to‑action make it **search‑friendly** for recruiters looking for graph‑algorithm talent.

---

### 📄 Blog Post – “The Good, The Bad, and The Ugly of Finding the Shortest Cycle in a Graph”

```markdown
# The Good, The Bad, and The Ugly of Finding the Shortest Cycle in a Graph

> **LeetCode 2608 – Shortest Cycle in a Graph**  
> A deep dive into BFS, DFS, and interview‑ready coding patterns

---

## Why This Question Rocks Your Resume

- **Graph Traversal Mastery** – shows you can manipulate queues, adjacency lists, and distance matrices.
- **Trade‑off Discussion** – DFS vs. BFS, time‑complexity, space‑complexity.
- **Short‑Answer & Long‑Answer** – perfect for 5‑minute interviews or coding challenge sessions.

---

## ✅ The Good – A **BFS** Solution that Everyone Loves

The BFS approach is the cleanest way to detect cycles while simultaneously computing shortest paths.  

1. **Root the search** at every vertex (`0 … n-1`).  
2. **Track** `dist[]` (distance from the root) and implicitly the parent via the BFS order.  
3. **Detect** a cycle when you encounter an already‑visited node that is *not* the parent.  
4. **Compute** the cycle length as `dist[u] + dist[v] + 1`.

```java
// Java snippet (see section 6.1 above)
```

```python
# Python snippet (see section 6.2 above)
```

```cpp
// C++ snippet (see section 6.3 above)
```

**Time**: `O(n·(n + m))` – fast enough for `n, m ≤ 1000`.  
**Space**: `O(n)` – distance array reused for each BFS.

---

## ⚠️ The Bad – What to Avoid

| Pitfall | Why It Breaks | Quick Fix |
|---------|---------------|-----------|
| Not marking nodes visited before pushing to queue | BFS gets stuck in infinite loops | `dist[v] == -1` check |
| Using a parent array that gets overwritten | Wrong cycle length (parent vs. distance check) | Compare parents explicitly |
| Adjacency matrix for 1000 nodes | Memory blow‑up | Use adjacency lists |
| Skipping disconnected components | Missed cycles in separate components | Iterate all vertices as roots |

---

## 😱 The Ugly – Over‑engineering That Spoils Readability

- Mixing DFS & BFS logic in one method.  
- Writing recursive BFS that uses a list as a queue.  
- Hard‑coding “infinite” values instead of named constants.  
- Adding unrelated helper classes that clutter the repository.

> **Pro Tip:** Keep your code *single‑purpose* and *self‑documented*.  

---

## 🎯 How to Use This in Your Next Interview

1. **Start with a quick sketch** of the BFS idea.  
2. **Show your code** in a language you’re comfortable with.  
3. **Discuss** edge cases (disconnected components, no cycles).  
4. **Compare** BFS vs. DFS – highlight BFS’s guarantee of the shortest cycle.  
5. **Mention** possible optimisations (bidirectional BFS, early exit) – even if you don’t implement them.

---

## 📣 Final Thought

The *Shortest Cycle* problem is a *show‑case* for your graph‑algorithm chops.  
A clean BFS solution like the one above demonstrates:

- **Understanding of data structures** (adjacency lists, queues).  
- **Algorithmic thinking** – distance tracking + parent checks.  
- **Coding style** – clear constants, modular helper functions.

Add the snippet to your GitHub, commit it to a “Graph Algorithms” folder, and include a comment explaining the approach. Recruiters love seeing not just a working solution, but also the *why* behind your choices.

---

### 🚀 SEO Keywords to Sprinkle

- shortest cycle in a graph  
- LeetCode 2608 solution  
- graph algorithm interview  
- BFS cycle detection  
- software engineer interview questions  
- graph traversal algorithms  
- job interview graph problem  
- C++ graph algorithm  
- Python graph algorithm

Add these to your blog meta tags, headings, and naturally in the content – it’ll boost discoverability for recruiters hunting for graph‑savvy engineers.

---

Happy coding, and may your interview stack be bug‑free! 🎉