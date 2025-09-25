---
title: LeetCode 2277. Closest Node to Path in Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🛠️ Code Solutions  
Below are clean, production‑ready implementations of the **LeetCode 2277 – “Closest Node to Path in Tree”** problem in **Java, Python, and C++**.  
Each solution follows the same logic:  

1. Build an adjacency list for the tree.  
2. For every query  
   * Find the nodes on the path from `start` to `end` using DFS.  
   * Perform a BFS from `node` until the first node that lies on the path is reached.  

The complexity is `O(n + q·n)` (worst‑case), which is more than fast enough for the constraints (`n, q ≤ 1000`).  

---  

### Java (DFS + BFS)

```java
import java.util.*;

public class Solution {
    // adjacency list
    private List<List<Integer>> graph;

    public int[] closestNode(int n, int[][] edges, int[][] query) {
        buildGraph(n, edges);
        int[] res = new int[query.length];

        for (int i = 0; i < query.length; i++) {
            int start = query[i][0];
            int end   = query[i][1];
            int node  = query[i][2];

            Set<Integer> path = new HashSet<>();
            findPath(start, end, path, -1);          // DFS
            res[i] = nearestOnPath(node, path);     // BFS
        }
        return res;
    }

    /* ---------- Helper Methods ---------- */

    // Build adjacency list
    private void buildGraph(int n, int[][] edges) {
        graph = new ArrayList<>();
        for (int i = 0; i < n; ++i) graph.add(new ArrayList<>());
        for (int[] e : edges) {
            graph.get(e[0]).add(e[1]);
            graph.get(e[1]).add(e[0]);
        }
    }

    // DFS that records all nodes on the unique path from 'cur' to 'target'
    private boolean findPath(int cur, int target, Set<Integer> path, int parent) {
        path.add(cur);
        if (cur == target) return true;

        for (int nb : graph.get(cur)) {
            if (nb == parent) continue;
            if (findPath(nb, target, path, cur)) return true;
        }
        path.remove(cur);               // backtrack – not on the path
        return false;
    }

    // BFS that returns the first node on 'path' encountered from 'start'
    private int nearestOnPath(int start, Set<Integer> path) {
        Queue<Integer> q = new ArrayDeque<>();
        boolean[] vis = new boolean[graph.size()];

        q.add(start);
        vis[start] = true;

        while (!q.isEmpty()) {
            int v = q.poll();
            if (path.contains(v)) return v;

            for (int nb : graph.get(v)) {
                if (!vis[nb]) {
                    vis[nb] = true;
                    q.add(nb);
                }
            }
        }
        return -1;  // should never happen because the tree is connected
    }
}
```

---

### Python (DFS + BFS)

```python
from collections import defaultdict, deque
from typing import List

class Solution:
    def closestNode(self, n: int, edges: List[List[int]],
                    query: List[List[int]]) -> List[int]:
        # adjacency list
        graph = defaultdict(list)
        for a, b in edges:
            graph[a].append(b)
            graph[b].append(a)

        def dfs_path(start: int, end: int) -> set:
            """Return all nodes on the unique path from start to end."""
            path = set()
            stack = [(start, -1)]
            parent = {start: -1}

            while stack:
                node, par = stack.pop()
                parent[node] = par
                if node == end:
                    # backtrack to build the path
                    cur = node
                    while cur != -1:
                        path.add(cur)
                        cur = parent[cur]
                    break
                for nb in graph[node]:
                    if nb == par:
                        continue
                    stack.append((nb, node))
            return path

        def bfs_nearest(start: int, target_set: set) -> int:
            """Return the first node in target_set found during BFS from start."""
            q = deque([start])
            visited = {start}
            while q:
                v = q.popleft()
                if v in target_set:
                    return v
                for nb in graph[v]:
                    if nb not in visited:
                        visited.add(nb)
                        q.append(nb)
            return -1

        ans = []
        for start, end, node in query:
            path_nodes = dfs_path(start, end)
            ans.append(bfs_nearest(node, path_nodes))
        return ans
```

---

### C++ (DFS + BFS)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> closestNode(int n, vector<vector<int>>& edges,
                            vector<vector<int>>& query) {
        // build adjacency list
        vector<vector<int>> g(n);
        for (auto &e : edges) {
            g[e[0]].push_back(e[1]);
            g[e[1]].push_back(e[0]);
        }

        vector<int> res;
        for (auto &q : query) {
            int start = q[0], end = q[1], node = q[2];

            unordered_set<int> path = findPath(g, start, end);
            res.push_back(nearestOnPath(g, node, path));
        }
        return res;
    }

private:
    // DFS that collects all nodes on the path from 'cur' to 'target'
    unordered_set<int> findPath(const vector<vector<int>>& g,
                                int cur, int target) {
        unordered_set<int> path;
        vector<int> parent(g.size(), -1);
        stack<int> st;
        st.push(cur);
        parent[cur] = -2;          // mark as visited

        while (!st.empty()) {
            int v = st.top(); st.pop();
            if (v == target) break;
            for (int nb : g[v]) {
                if (parent[nb] == -1) {
                    parent[nb] = v;
                    st.push(nb);
                }
            }
        }

        // backtrack to build the path
        for (int v = target; v != -2; v = parent[v])
            path.insert(v);
        return path;
    }

    // BFS from 'start' until a node in 'target_set' is found
    int nearestOnPath(const vector<vector<int>>& g, int start,
                      const unordered_set<int>& target_set) {
        queue<int> q;
        vector<bool> vis(g.size(), false);
        q.push(start);
        vis[start] = true;

        while (!q.empty()) {
            int v = q.front(); q.pop();
            if (target_set.count(v)) return v;
            for (int nb : g[v]) {
                if (!vis[nb]) {
                    vis[nb] = true;
                    q.push(nb);
                }
            }
        }
        return -1;   // never reached because tree is connected
    }
};
```

---

## 📚 Blog Article  
### The Good, The Bad, and The Ugly of Solving LeetCode 2277  
#### *“Closest Node to Path in Tree” – From Brainstorm to Production‑Ready Code*  

---

#### 1. Problem Recap  
> **LeetCode 2277 – Closest Node to Path in Tree**  
> Given a tree with `n` nodes (0‑based), answer `m` queries. Each query `[start, end, node]` asks: *which node on the path from `start` to `end` is closest to `node`?*  

Constraints are small (`n, m ≤ 1000`), but the problem tests your ability to combine graph traversal, path extraction, and distance calculation.

---

#### 2. The Good – Straight‑Forward Intuition  

1. **Unique Path Property**  
   In a tree, there is exactly one simple path between any two nodes. This means that once we know that path, all we need is the nearest point on it.

2. **Decoupling the Tasks**  
   * **Find the Path** – A DFS (or BFS) that stops as soon as the destination is reached.  
   * **Find the Nearest Node on that Path** – A BFS starting from `node` that stops when it lands on any node from the path set.

3. **Why This Works**  
   * The DFS visits each edge at most twice → `O(n)`.  
   * The BFS explores nodes until the first match → worst case `O(n)` again.  
   * Total per query: `O(n)` → `O(n·m)` overall, which is acceptable for the given limits.

4. **Simplicity in Code**  
   The algorithm is clean, readable, and easy to test. You can write it in **Java**, **Python**, or **C++** with just a handful of helper functions.  

---

#### 3. The Bad – Common Pitfalls  

| Pitfall | Why It Breaks | Quick Fix |
|---------|---------------|-----------|
| **Assuming the first node found is the closest** | BFS on an undirected tree may reach a farther node first if not careful with distance calculation. | Use a distance array or stop the BFS **exactly** on the first path node. |
| **Using recursion without a parent guard** | Leads to infinite loops or revisiting the parent. | Pass the parent index in DFS/BFS or keep a visited array. |
| **Storing the path as a list and doing linear scans** | Linear scans on each BFS step cost `O(path_len)` → extra overhead. | Store the path in a `HashSet` / `unordered_set` for O(1) membership checks. |
| **Ignoring the unique‑path guarantee** | Thinking there might be multiple shortest paths misleads on distance calculations. | Rely on the tree property; you never need Dijkstra. |

---

#### 4. The Ugly – When Edge Cases Bite  

1. **Large Depth (skewed tree)**  
   DFS recursion depth can hit 1000, which is fine in most languages but can cause stack overflow in some interview environments.  
   *Solution:* Use an explicit stack (iterative DFS) or increase recursion limits in Python.

2. **Duplicate Queries**  
   The naive implementation recomputes the same path for identical `[start, end]`.  
   *Solution:* Cache path sets per `(start, end)` pair if you anticipate many repeats.

3. **Incorrect Visitation Order**  
   If BFS from `node` is not level‑ordered, you may return a non‑minimal distance node.  
   *Solution:* Use a queue (`deque` in Python, `std::queue` in C++) – it guarantees level order.

---

#### 5. Complexity Snapshot  

| Operation | Complexity | Notes |
|-----------|------------|-------|
| Build graph | `O(n)` | Single pass over `edges`. |
| DFS per query | `O(n)` | Visits each edge once (early exit). |
| BFS per query | `O(n)` | Stops at the first match. |
| Total | **`O(n · m)`** | ≤ 1 000 000 operations for the worst case – trivial for modern CPUs. |

Memory: `O(n)` for the adjacency list and a few auxiliary arrays per query.

---

#### 6. Implementation Highlights  

* **Java** – Uses `List<List<Integer>>` for the adjacency list and a boolean array for visited nodes.  
* **Python** – Uses `defaultdict(list)` for adjacency, `deque` for BFS, and a manual stack for DFS.  
* **C++** – Uses `vector<vector<int>>`, `unordered_set`, and `queue`.  

All three versions expose the same structure: build → find path → BFS for nearest node.  

---

#### 7. How to Nail This Question in an Interview  

1. **Ask Clarifying Questions**  
   * “Are `start`, `end`, and `node` guaranteed to be distinct?”  
   * “Is it acceptable to do `O(n)` per query?”  

2. **Show the Decoupled Strategy**  
   Sketch the idea on paper: “DFS to get path, BFS to find the first intersection.”  
   Mention the *unique path* property upfront – it demonstrates deep understanding.

3. **Mention Edge Cases**  
   * Very deep trees (chain).  
   * Queries where `node` itself lies on the path.  
   * Repeated queries (possible optimization via memoization).

4. **Time‑Space Trade‑Off**  
   * If the interviewer pushes for better than `O(n·m)`, discuss pre‑computing all pairwise distances (`O(n^2)`) or using LCA + binary lifting to answer in `O(log n)` per query.  
   * For the given constraints, the simple DFS+BFS is the gold standard: correct, maintainable, and fast.

5. **Testing**  
   * Build your own test harness with random trees and queries.  
   * Verify that the answer matches a brute‑force checker that computes distances explicitly.

---

#### 8. Final Takeaway  

> **When in doubt, break the problem into smaller, independent parts.**  
> For LeetCode 2277, the tree’s single‑path guarantee means you can safely separate “find path” from “find nearest node.”  
> A clean DFS + BFS solution, written in **Java**, **Python**, or **C++**, will pass all tests and impress interviewers with its clarity and correctness.

---

#### 9. SEO Keywords  

| Keyword | Why it matters |
|---------|----------------|
| LeetCode 2277 | Core search term |
| Closest Node to Path in Tree | Specific problem name |
| Tree queries | Algorithmic category |
| Java DFS solution | Language + algorithm |
| Python BFS solution | Language + algorithm |
| C++ interview code | Language + interview context |
| Job interview algorithms | Career‑relevant search term |

Use these tags when sharing your solution on LinkedIn, Medium, or a personal blog to reach recruiters and fellow interviewees.

---

### 🚀 Bottom line  
- **Good**: The tree’s unique path property makes the problem conceptually simple.  
- **Bad**: Mistakes in traversal order or visited checks can silently produce wrong answers.  
- **Ugly**: Ignoring depth limits or caching can cause stack overflows and TLE in larger constraints.  

Stick to the **DFS + BFS** pattern, keep your code clean, and you’ll solve LeetCode 2277 in no time – and you’ll be ready to showcase this exact pattern in a coding interview!