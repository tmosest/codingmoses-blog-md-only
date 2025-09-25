---
title: LeetCode 1245. Tree Diameter - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1245. Tree Diameter – Solutions in **Java, Python, C++** + a Full‑Stack Blog Post

---

## 1.  Problem Recap  

> **Tree Diameter**  
> In an undirected tree with `n` nodes (`0 … n-1`) you’re given an array `edges` of length `n‑1`, each element `[a, b]` denotes an undirected edge between nodes `a` and `b`.  
> Return the **diameter** of the tree – the number of edges on the longest simple path between any two nodes.

**Constraints**

| | |
|---|---|
|`n` | `1 ≤ n ≤ 10^4` |
|`edges.length` | `n - 1` |
|`0 ≤ ai, bi < n` |
|`ai ≠ bi` |

The input is guaranteed to represent a tree (connected & acyclic).

---

## 2.  Preferred Approach – Two‑Pass DFS (or BFS)

The classic “tree diameter” trick is to do **two** depth‑first (or breadth‑first) traversals:

1. **First pass** – pick any node (e.g. 0), run DFS/BFS to find the farthest node `A`.  
2. **Second pass** – run DFS/BFS from `A` to find the farthest node `B`.  
   The distance `dist(A, B)` is the diameter.

Why does it work?  
The farthest node from an arbitrary start is guaranteed to lie on some longest path.  
A second traversal from that node yields the other endpoint of that longest path.  
Proof can be found in many algorithm texts or the classic “double‑end BFS” argument.

**Time Complexity**: `O(n)`  
**Space Complexity**: `O(n)` for adjacency lists + recursion stack (or queue)

---

## 3.  Code

Below you’ll find clean, production‑ready implementations in **Java, Python, and C++**.  
All three build an adjacency list and run two BFS passes.

---

### 3.1 Java

```java
import java.util.*;

public class Solution {
    // Main method as expected by LeetCode
    public int treeDiameter(int[][] edges) {
        if (edges == null || edges.length == 0) return 0;

        int n = edges.length + 1;
        List<List<Integer>> adj = buildAdjacency(n, edges);

        // First BFS from any node (0) -> find farthest node A
        int A = bfs(adj, 0).farthestNode;

        // Second BFS from A -> find farthest node B and distance
        return bfs(adj, A).maxDistance;
    }

    // Helper: adjacency list builder
    private List<List<Integer>> buildAdjacency(int n, int[][] edges) {
        List<List<Integer>> adj = new ArrayList<>(n);
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for (int[] e : edges) {
            adj.get(e[0]).add(e[1]);
            adj.get(e[1]).add(e[0]);
        }
        return adj;
    }

    // Result of BFS: farthest node & its distance
    private static class BFSResult {
        int farthestNode;
        int maxDistance;
        BFSResult(int node, int dist) { farthestNode = node; maxDistance = dist; }
    }

    private BFSResult bfs(List<List<Integer>> adj, int start) {
        int n = adj.size();
        int[] dist = new int[n];
        Arrays.fill(dist, -1);
        Queue<Integer> q = new ArrayDeque<>();
        q.offer(start);
        dist[start] = 0;
        int farthestNode = start;
        int maxDistance = 0;

        while (!q.isEmpty()) {
            int u = q.poll();
            for (int v : adj.get(u)) {
                if (dist[v] == -1) {
                    dist[v] = dist[u] + 1;
                    q.offer(v);
                    if (dist[v] > maxDistance) {
                        maxDistance = dist[v];
                        farthestNode = v;
                    }
                }
            }
        }
        return new BFSResult(farthestNode, maxDistance);
    }
}
```

---

### 3.2 Python

```python
from collections import defaultdict, deque
from typing import List

class Solution:
    def treeDiameter(self, edges: List[List[int]]) -> int:
        if not edges:
            return 0

        n = len(edges) + 1
        adj = defaultdict(list)
        for a, b in edges:
            adj[a].append(b)
            adj[b].append(a)

        # First BFS
        far_node = self._bfs(adj, 0)[0]
        # Second BFS – returns diameter
        return self._bfs(adj, far_node)[1]

    def _bfs(self, adj, start):
        n = len(adj) + 1
        dist = [-1] * n
        q = deque([start])
        dist[start] = 0
        farthest = start
        maxdist = 0

        while q:
            u = q.popleft()
            for v in adj[u]:
                if dist[v] == -1:
                    dist[v] = dist[u] + 1
                    q.append(v)
                    if dist[v] > maxdist:
                        maxdist = dist[v]
                        farthest = v
        return farthest, maxdist
```

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int treeDiameter(vector<vector<int>>& edges) {
        if (edges.empty()) return 0;
        int n = edges.size() + 1;
        vector<vector<int>> adj(n);
        for (auto& e : edges) {
            adj[e[0]].push_back(e[1]);
            adj[e[1]].push_back(e[0]);
        }

        auto bfs = [&](int start) {
            vector<int> dist(n, -1);
            queue<int> q;
            q.push(start);
            dist[start] = 0;
            int farthest = start, maxd = 0;
            while (!q.empty()) {
                int u = q.front(); q.pop();
                for (int v : adj[u]) {
                    if (dist[v] == -1) {
                        dist[v] = dist[u] + 1;
                        q.push(v);
                        if (dist[v] > maxd) {
                            maxd = dist[v];
                            farthest = v;
                        }
                    }
                }
            }
            return pair<int,int>{farthest, maxd};
        };

        int far = bfs(0).first;
        return bfs(far).second;
    }
};
```

---

## 4.  A Blog‑Ready Guide: “Tree Diameter – The Good, The Bad, and The Ugly”

> **SEO Keywords**: *LeetCode Tree Diameter, tree diameter algorithm, tree diameter interview, two‑pass DFS, BFS, graph diameter, job interview algorithm, data structures interview, tree traversal, Java Python C++ code.*

---

### 4.1 Introduction  

When you see **“Tree Diameter”** on LeetCode, most of us immediately feel the classic “longest path in a tree” buzz. It’s a common interview question that tests your understanding of graph traversal, recursion, and optimization. In this article we’ll dissect the problem, explore the **good** approaches, warn you of the **bad** pitfalls, and show you the **ugly** edge‑cases you might miss.

---

### 4.2 The Problem in Plain English  

You have a tree (connected, undirected, acyclic graph).  
Edges are given as pairs `[a, b]`.  
Return the *length* of the longest simple path – that is, the number of edges on the path that visits two nodes farthest apart.

---

### 4.3 The Classic Two‑Pass Strategy – The *Good*  

1. **Pick any node** (e.g., 0).  
2. Run **DFS or BFS** to find the farthest node **A** from it.  
3. Run DFS/BFS again from **A** to find the farthest node **B**.  
4. The distance between **A** and **B** is the diameter.

Why it’s good:

- **Linear time** `O(n)` and linear space `O(n)` – perfect for `n ≤ 10⁴`.  
- Very easy to reason about and implement in any language.  
- Works for *any* tree – balanced, skewed, star‑shaped, etc.  
- No extra data structures beyond adjacency lists and a queue/stack.

**Implementation note**: BFS is often preferred because it avoids recursion depth limits in languages like Python. DFS is just as fast in Java/C++ with recursion or an explicit stack.

---

### 4.4 Alternative Approaches – The *Bad*  

| Approach | Time | Space | Drawbacks |
|----------|------|-------|-----------|
| **Topological peel (centroid)** | `O(n)` | `O(n)` | Complex to implement, harder to reason, error‑prone in interview settings. |
| **Naïve all‑pairs longest path** | `O(n²)` | `O(n)` | Too slow for `n=10⁴`. |
| **Dynamic programming on tree** | `O(n)` | `O(n)` | Works but overkill; two‑pass BFS is simpler. |

If you try a topological sort or centroid approach, you’ll spend valuable interview time explaining the logic. Stick to the two‑pass method unless you have extra time.

---

### 4.5 Edge Cases – The *Ugly*  

| Edge case | Why it matters | How to handle |
|-----------|----------------|---------------|
| **Single node** (`n=1`) | No edges, diameter should be `0`. | Return `0` immediately. |
| **Star shape** (one center, many leaves) | Farthest nodes are two leaves; diameter = 2. | Two‑pass BFS still works. |
| **Skewed tree** (like a linked list) | Depth of recursion could hit recursion limit in Python. | Prefer BFS or iterative DFS. |
| **Duplicate edges** (not allowed by constraints) | Could break the tree property. | Problem guarantees no duplicates. |
| **Disconnected input** (invalid tree) | Our algorithm would still produce some number but is undefined. | Not needed; input is guaranteed to be a tree. |

Always start your solution with a guard for `edges == null || edges.length == 0`.

---

### 4.6 Code Samples (Java / Python / C++)

(See the code section above for fully‑commented implementations.)

Feel free to copy‑paste into your editor, run tests, and tweak. The patterns are identical: build adjacency list → BFS → BFS.

---

### 4.7 Why This Matters for Your Job Hunt  

- **Tree diameter** is a *classic* interview problem that demonstrates mastery of graph fundamentals.  
- Solving it efficiently showcases **time‑space trade‑offs** and **algorithmic thinking**.  
- The two‑pass BFS approach is often the *one‑liner* that interviewers love to see.  
- Providing clean, language‑specific code (Java, Python, C++) shows you can **communicate** across stacks.  

Include this solution in your portfolio; link to the LeetCode discussion; brag about passing the interview at speed. The more polished your write‑up, the higher the signal to recruiters.

---

### 4.8 Final Thoughts  

- The two‑pass method is **best‑in‑class** for LeetCode 1245.  
- Keep the code short, clear, and well‑commented.  
- Highlight the algorithmic insight in your résumé or LinkedIn: “Reduced tree diameter from naive O(n²) to O(n) with a two‑pass BFS”.  
- Practice explaining the logic *in one minute* – that’s what matters on the day.

Happy coding, and may your interview tree paths always be the longest!

--- 

> **End of Article**  

--- 

### 5.  Closing  

Feel free to adapt the article into your blog or LinkedIn post. The structure is optimized for readability and SEO, with embedded code for Java, Python, and C++ – perfect for a coding interview showcase.