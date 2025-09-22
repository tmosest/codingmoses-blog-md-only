---
title: LeetCode 1192. Critical Connections in a Network - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Critical Connections in a Network – LeetCode 1192  
**A deep dive into bridges, Tarjan’s algorithm, and how to ace this interview question**  

> **Keywords:** *Critical Connections in a Network, LeetCode 1192, bridge finding, Tarjan algorithm, network reliability, Java solution, Python solution, C++ solution, software engineer interview, job interview preparation*

---

## 1. Problem Overview  

> **LeetCode 1192 – Critical Connections in a Network**  
> **Difficulty:** Hard  

You’re given an undirected, connected graph with **`n`** nodes (servers) numbered from **0** to **n‑1** and a list of **`connections`** where each connection is a 2‑element array `[a, b]`.  
A **critical connection** (or **bridge**) is an edge that, if removed, increases the number of connected components.  
Return all critical connections in any order.

> **Constraints**  
> * 2 ≤ n ≤ 10⁵  
> * n - 1 ≤ connections.length ≤ 10⁵  
> * 0 ≤ ai, bi < n  
> * ai ≠ bi  
> * No duplicate edges

---

## 2. Why Is This Problem Hard?  

| Good | Bad | Ugly |
|------|-----|------|
| **Requires understanding of graph theory** – not a simple DFS or BFS | **Time complexity** must be **O(V + E)** – brute force removal of each edge is O(V·E) | **Stack overflow** risk for deep recursion on skewed graphs |
| **Algorithmic elegance** – Tarjan’s algorithm is a classic | **Large input sizes** – recursion depth may reach 10⁵ | **Edge cases** – isolated nodes, multiple components, self‑loops (not allowed here but worth guarding) |
| **Interviews love it** – showcases problem‑solving, data structures | **Need for adjacency list** – misuse can lead to memory blow | **Testing** – many subtle cases; must validate against the LeetCode test suite |

---

## 3. Core Idea – Tarjan’s Bridge‑Finding Algorithm  

1. **DFS traversal** with timestamps (`tin`) when a node is first visited.  
2. **Lowlink value** (`low[u]`) = smallest timestamp reachable from `u` using **at most one back edge**.  
3. When exploring an edge `u → v` (tree edge), after returning from `v`, if `low[v] > tin[u]`, the edge `(u, v)` is a bridge.  
4. For back edges, update `low[u]` with `tin[v]`.

This runs in **O(V + E)** time and **O(V + E)** memory (adjacency list + recursion stack).

---

## 4. Code Implementations  

Below you’ll find clean, production‑ready solutions in **Java, Python, and C++**.  
All three use the same Tarjan logic, are commented for clarity, and handle the maximum constraints safely.

---

### 4.1 Java 17  

```java
import java.util.*;

/**
 * LeetCode 1192 – Critical Connections in a Network
 *
 * Time   : O(V + E)
 * Space  : O(V + E)   (adjacency list + recursion stack)
 */
class Solution {
    private List<List<Integer>> result;   // list of bridges
    private List<Integer>[] graph;        // adjacency list
    private int[] tin;                    // entry time
    private int[] low;                    // lowlink values
    private int timer;

    public List<List<Integer>> criticalConnections(int n, List<List<Integer>> connections) {
        // Build adjacency list
        graph = new ArrayList[n];
        for (int i = 0; i < n; i++) graph[i] = new ArrayList<>();
        for (List<Integer> conn : connections) {
            int u = conn.get(0), v = conn.get(1);
            graph[u].add(v);
            graph[v].add(u);
        }

        tin = new int[n];
        low = new int[n];
        Arrays.fill(tin, -1);  // -1 = unvisited
        timer = 0;
        result = new ArrayList<>();

        // The graph is guaranteed to be connected, but we run DFS from 0
        dfs(0, -1);

        return result;
    }

    private void dfs(int u, int parent) {
        tin[u] = low[u] = timer++;          // set timestamps
        for (int v : graph[u]) {
            if (v == parent) continue;      // don't go back to parent
            if (tin[v] == -1) {             // tree edge
                dfs(v, u);
                low[u] = Math.min(low[u], low[v]);
                if (low[v] > tin[u]) {
                    result.add(Arrays.asList(u, v));  // (u, v) is a bridge
                }
            } else {                        // back edge
                low[u] = Math.min(low[u], tin[v]);
            }
        }
    }
}
```

---

### 4.2 Python 3.10+  

```python
from typing import List

class Solution:
    """
    LeetCode 1192 – Critical Connections in a Network
    Time   : O(V + E)
    Space  : O(V + E)
    """
    def criticalConnections(self, n: int, connections: List[List[int]]) -> List[List[int]]:
        # Build adjacency list
        graph = [[] for _ in range(n)]
        for u, v in connections:
            graph[u].append(v)
            graph[v].append(u)

        tin = [-1] * n
        low = [0] * n
        timer = 0
        bridges = []

        def dfs(u: int, parent: int) -> None:
            nonlocal timer
            tin[u] = low[u] = timer
            timer += 1
            for v in graph[u]:
                if v == parent:
                    continue
                if tin[v] == -1:  # tree edge
                    dfs(v, u)
                    low[u] = min(low[u], low[v])
                    if low[v] > tin[u]:
                        bridges.append([u, v])
                else:  # back edge
                    low[u] = min(low[u], tin[v])

        dfs(0, -1)
        return bridges
```

> **Tip:** On LeetCode, set a higher recursion limit (`sys.setrecursionlimit(200000)`) if you hit a recursion depth error.

---

### 4.3 C++17  

```cpp
#include <bits/stdc++.h>
using namespace std;

/**
 * LeetCode 1192 – Critical Connections in a Network
 * Time   : O(V + E)
 * Space  : O(V + E)
 */
class Solution {
public:
    vector<vector<int>> criticalConnections(int n, vector<vector<int>>& connections) {
        // Build adjacency list
        vector<vector<int>> graph(n);
        for (auto &e : connections) {
            int u = e[0], v = e[1];
            graph[u].push_back(v);
            graph[v].push_back(u);
        }

        vector<int> tin(n, -1), low(n, 0);
        int timer = 0;
        vector<vector<int>> bridges;

        function<void(int,int)> dfs = [&](int u, int parent) {
            tin[u] = low[u] = timer++;
            for (int v : graph[u]) {
                if (v == parent) continue;
                if (tin[v] == -1) {               // tree edge
                    dfs(v, u);
                    low[u] = min(low[u], low[v]);
                    if (low[v] > tin[u])
                        bridges.push_back({u, v});
                } else {                           // back edge
                    low[u] = min(low[u], tin[v]);
                }
            }
        };

        dfs(0, -1);  // graph is connected
        return bridges;
    }
};
```

---

## 5. Running the Solutions on the Sample Tests  

| Language | Test | Result |
|----------|------|--------|
| Java | `n = 4`, `connections = [[0,1],[1,2],[2,0],[1,3]]` | `[[1,3]]` |
| Python | Same | `[[1,3]]` |
| C++ | Same | `[[1,3]]` |

All three produce the expected bridge(s) regardless of order.

---

## 6. Edge‑Case Checklist  

1. **Single edge** (`n = 2`, `[[0,1]]`) → always a bridge.  
2. **Fully connected graph** (complete graph) → no bridges.  
3. **Graph with multiple components** (though problem guarantees connectivity).  
4. **Deep recursion** (chain graph) → set recursion limit / stack size.  
5. **Large `n`** (10⁵) → iterative BFS is not required; recursion depth equals `n` in worst case; typical stack size (~1 MB) is enough for 10⁵ on most platforms.

---

## 7. Why This Algorithm Rocks in Interviews  

- **Classic algorithmic pattern** (DFS + lowlink) that showcases deep graph knowledge.  
- **Clear explanation**: you can walk a recruiter through `tin`, `low`, and the bridge condition.  
- **Scalable**: fits the LeetCode constraints and production systems.  
- **Reusable**: the same core works for *articulation points*, *strongly connected components*, etc.

---

## 8. SEO‑Optimized Takeaway Paragraph  

If you’re preparing for a software engineering interview, mastering **Critical Connections in a Network (LeetCode 1192)** is a must. This hard problem tests your understanding of **Tarjan’s bridge‑finding algorithm**, **DFS traversal**, and **lowlink values**. By implementing clean solutions in **Java, Python, and C++**, you demonstrate versatility and depth—key traits recruiters look for. Make sure to highlight your ability to handle large inputs, avoid stack overflows, and explain the algorithm’s intuition. These points will set you apart and help land that job.

---

### TL;DR

- Use **Tarjan’s algorithm** to find bridges in O(V + E).  
- Implement in **Java, Python, or C++** with adjacency lists.  
- Handle recursion depth and large inputs carefully.  
- Showcase the solution in your interview to impress hiring managers.  

Happy coding—and good luck on your next interview!