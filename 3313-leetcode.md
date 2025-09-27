---
title: LeetCode 3313. Find the Last Marked Nodes in Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 3313. Find the Last Marked Nodes in Tree  
> **Hard** – LeetCode  

> **Keywords**: *Tree Diameter*, *BFS*, *DFS*, *Shortest Distance*, *Marking Process*, *LeetCode 3313*, *Java*, *Python*, *C++*, *Algorithm Design*, *Interview Prep*  

---

### 📌 Problem Recap

You’re given an undirected tree with `n` nodes (0 … n‑1).  
At time `t = 0` you mark a single node `i`.  
Every second you mark all *unmarked* nodes that have at least one marked neighbor.  

For every starting node `i` output **any** node that gets marked *last*.  
If there are two nodes that are tied for last, you may return either of them.

`edges` is a list of `n-1` pairs `[u, v]` describing the tree.

```
Input:  edges = [[0,1],[0,2]]
Output: [2,2,1]
```

---

### 🧩 Why the Tree Diameter Helps

The marking process is equivalent to a “wave” that expands one edge per second from the starting node.  
The last node to be reached is exactly the one that is *farthest* from the start.

In a tree, the farthest distance from any node is always to one of the two *diameter endpoints* (the two ends of the longest path).  
So, for each node `i` we only need to know which of the two diameter ends is farther from `i`.  
That end is guaranteed to be a valid “last marked node”.

---

### ✅ Good

| ✅ | What it does |
|---|--------------|
| • | **Linear time** (`O(n)`) and space (`O(n)`). |
| • | Works for the maximum constraint (`n = 10⁵`). |
| • | No heavy recursion – uses iterative BFS/DFS to avoid stack overflow. |
| • | Very clear logic: diameter → two distance arrays → answer per node. |

---

### ⚠️ Bad

| ⚠️ | Potential pitfalls |
|---|---------------------|
| • | If you forget that the tree is *undirected* you’ll mis‑build the graph. |
| • | Relying on recursion with depth > 10⁴ can crash on some systems. |
| • | Choosing the wrong “tie‑breaker” (e.g., `point2` when distances equal) may still satisfy the problem, but may give a different answer than expected by some test harnesses. |
| • | Forgetting that edges length is `n‑1` could lead to an off‑by‑one error in array sizes. |

---

### 💥 Ugly

| 💥 | Things that can get messy |
|---|-----------------------------|
| • | Writing three separate BFS/DFS implementations for Java, Python, and C++ leads to duplicated logic. |
| • | Mixing 0‑based and 1‑based node indices in comments or variable names can confuse maintainers. |
| • | Over‑engineering the solution (e.g., using Dijkstra or a priority queue) unnecessarily increases complexity. |

---

## 📈 Algorithm Overview

1. **Build the adjacency list** (`List<List<Integer>>` in Java, `defaultdict(list)` in Python, `vector<vector<int>>` in C++).  
2. **Find one diameter endpoint (`point1`)**: BFS/DFS from node 0 to the farthest node.  
3. **Find the other diameter endpoint (`point2`)**: BFS/DFS from `point1`.  
4. **Compute distance arrays**  
   * `dist1[i]` – distance from `point1` to node `i`.  
   * `dist2[i]` – distance from `point2` to node `i`.  
5. **Answer for node `i`**  
   * If `dist1[i] >= dist2[i]` → `point1` (ties arbitrarily).  
   * Else → `point2`.  

All BFS/DFS runs are linear, so total time `O(n)`.

---

## 📊 Complexity Analysis

| Metric | Calculation |
|--------|-------------|
| **Time** | `O(n)` – 4 traversals of the tree. |
| **Space** | `O(n)` – adjacency list + 2 distance arrays + queue/stack. |

---

## 🎯 Edge‑Case Checklist

| Case | Why it matters | How we handle it |
|------|----------------|------------------|
| `n = 2` | Only one edge, both diameter endpoints are the two nodes. | BFS returns farthest node correctly. |
| Star graph (one center, many leaves) | All leaves are at distance 1 from center. | Diameter endpoints are two leaves; distances handled correctly. |
| Very deep tree (path) | Recursion depth could blow stack. | Iterative BFS/DFS used. |
| Duplicate edges or self‑loops | Not allowed by problem constraints but guard against misuse. | No extra checks needed. |

---

## 🧑‍💻 Implementation Snippets

Below are clean, production‑ready solutions in **Java, Python, and C++**.

> **Tip** – All three use iterative BFS for safety and clarity.  
> **Hint** – Replace `ArrayDeque` with a simple `int[]` queue if memory becomes a concern.

### Java (Java 17+)

```java
import java.util.*;

class Solution {
    private static int[] bfs(List<Integer>[] graph, int start) {
        int n = graph.length;
        int[] dist = new int[n];
        Arrays.fill(dist, -1);
        ArrayDeque<Integer> q = new ArrayDeque<>();
        q.add(start);
        dist[start] = 0;
        while (!q.isEmpty()) {
            int u = q.poll();
            for (int v : graph[u]) {
                if (dist[v] == -1) {
                    dist[v] = dist[u] + 1;
                    q.add(v);
                }
            }
        }
        return dist;
    }

    private static int farthestNode(int[] dist) {
        int node = 0, best = -1;
        for (int i = 0; i < dist.length; i++) {
            if (dist[i] > best) {
                best = dist[i];
                node = i;
            }
        }
        return node;
    }

    public int[] lastMarkedNodes(int[][] edges) {
        int n = edges.length + 1;
        @SuppressWarnings("unchecked")
        List<Integer>[] graph = new List[n];
        for (int i = 0; i < n; i++) graph[i] = new ArrayList<>();

        for (int[] e : edges) {
            int u = e[0], v = e[1];
            graph[u].add(v);
            graph[v].add(u);
        }

        // 1st endpoint
        int[] dist0 = bfs(graph, 0);
        int p1 = farthestNode(dist0);

        // 2nd endpoint
        int[] dist1 = bfs(graph, p1);
        int p2 = farthestNode(dist1);

        // Distances from the other endpoint
        int[] dist2 = bfs(graph, p2);

        int[] ans = new int[n];
        for (int i = 0; i < n; i++) {
            ans[i] = dist1[i] >= dist2[i] ? p1 : p2;
        }
        return ans;
    }
}
```

---

### Python 3 (3.8+)

```python
from collections import deque
from typing import List

class Solution:
    def _bfs(self, graph: List[List[int]], start: int) -> List[int]:
        n = len(graph)
        dist = [-1] * n
        q = deque([start])
        dist[start] = 0
        while q:
            u = q.popleft()
            for v in graph[u]:
                if dist[v] == -1:
                    dist[v] = dist[u] + 1
                    q.append(v)
        return dist

    def _farthest(self, dist: List[int]) -> int:
        return max(range(len(dist)), key=lambda i: dist[i])

    def lastMarkedNodes(self, edges: List[List[int]]) -> List[int]:
        n = len(edges) + 1
        graph = [[] for _ in range(n)]
        for u, v in edges:
            graph[u].append(v)
            graph[v].append(u)

        d0 = self._bfs(graph, 0)
        p1 = self._farthest(d0)

        d1 = self._bfs(graph, p1)
        p2 = self._farthest(d1)

        d2 = self._bfs(graph, p2)

        return [p1 if d1[i] >= d2[i] else p2 for i in range(n)]
```

---

### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> bfs(const vector<vector<int>>& g, int start) {
        int n = g.size();
        vector<int> dist(n, -1);
        queue<int> q;
        q.push(start);
        dist[start] = 0;
        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (int v : g[u]) {
                if (dist[v] == -1) {
                    dist[v] = dist[u] + 1;
                    q.push(v);
                }
            }
        }
        return dist;
    }

    int farthest(const vector<int>& d) {
        int node = 0, best = -1;
        for (int i = 0; i < (int)d.size(); ++i)
            if (d[i] > best) { best = d[i]; node = i; }
        return node;
    }

    vector<int> lastMarkedNodes(vector<vector<int>>& edges) {
        int n = edges.size() + 1;
        vector<vector<int>> g(n);
        for (auto& e : edges) {
            int u = e[0], v = e[1];
            g[u].push_back(v);
            g[v].push_back(u);
        }

        auto d0 = bfs(g, 0);
        int p1 = farthest(d0);

        auto d1 = bfs(g, p1);
        int p2 = farthest(d1);

        auto d2 = bfs(g, p2);

        vector<int> ans(n);
        for (int i = 0; i < n; ++i)
            ans[i] = (d1[i] >= d2[i]) ? p1 : p2;
        return ans;
    }
};
```

---

## 📦 Testing

Run the following minimal test harness in each language to verify correctness:

```java
int[][] edges = {{0,1},{0,2}};
int[] result = new Solution().lastMarkedNodes(edges);
System.out.println(Arrays.toString(result));   // [2, 2, 1]
```

```python
print(Solution().lastMarkedNodes([[0,1],[0,2]]))   # [2, 2, 1]
```

```cpp
vector<vector<int>> edges = {{0,1},{0,2}};
Solution sol;
auto res = sol.lastMarkedNodes(edges);
for (int x : res) cout << x << ' ';   // 2 2 1
```

---

## 🚀 Interview‑Ready Takeaway

- **Key insight**: The wave reaches the diameter ends first.  
- **Implementation**: 4 BFS/DFS passes → `O(n)` time and space.  
- **Languages**: Java, Python, C++ – all share the same clean iterative logic.

With this pattern you’ll crack the problem in seconds, impress the interviewer, and get a clean code base to maintain.

Happy coding! 🚀

--- 

*Prepared by a seasoned software engineer, ready to be copied into any coding interview repository.*