---
title: LeetCode 1245. Tree Diameter - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1245 – Tree Diameter  
**A “Good, Bad & Ugly” Walk‑through + 3‑Language Solutions + SEO‑Optimised Interview Blog**

---

## 1. Problem Overview

> **Tree Diameter**  
> You’re given an *undirected* tree with `n` nodes labeled `0 … n‑1`.  
> `edges[i] = [ai, bi]` describes an edge between `ai` and `bi`.  
> **Return the diameter** – the number of edges on the longest path in the tree.

> **Constraints**  
> * `1 ≤ n ≤ 10⁴`  
> * `edges.length == n‑1`  
> * `0 ≤ ai, bi < n`  
> * `ai != bi`  

Typical LeetCode format, perfect for a **software‑engineering interview**.

---

## 2. Good, Bad & Ugly

| Aspect | What’s *Good* | What’s *Bad* | What’s *Ugly* |
|--------|---------------|--------------|---------------|
| **Approach** | *Two‑pass DFS* (or BFS) is linear, easy to explain. | A naive “try all pairs” approach would be O(n²). | Using recursion for deep trees may hit stack limits in some languages. |
| **Edge Cases** | Tree with only one node → diameter 0. | Forgetting to handle isolated leaves during topological peel. | Mutating the adjacency list in place during DFS (dangerous if reused). |
| **Performance** | O(n) time, O(n) space. | DFS that tracks depth and updates global max. | BFS with a queue of size n but using an `unordered_set` for visited each level (extra overhead). |
| **Readability** | Clear variable names (`maxDepth`, `diameter`). | Boilerplate graph construction can clutter the solution. | Over‑complicated data‑structures (e.g., using `Map<Set<Integer>>` in Java for no benefit). |
| **Testing** | Unit tests for small trees, balanced, skewed, star, path. | Not testing the case where there are exactly two centroids. | Testing only the happy path (no invalid input handling). |

---

## 3. Three Implementation Highlights

Below you’ll find three idiomatic solutions:  
*Java* – classic DFS, stack‑free recursion.  
*Python* – DFS with `defaultdict(list)`.  
*C++* – iterative DFS using a stack (no recursion depth issues).

All code snippets are self‑contained, commented, and ready for copy‑paste into your IDE or LeetCode.

---

### 3.1 Java – Two‑Pass DFS

```java
import java.util.*;

public class Solution {
    // Build adjacency list
    private List<List<Integer>> buildGraph(int n, int[][] edges) {
        List<List<Integer>> g = new ArrayList<>(n);
        for (int i = 0; i < n; i++) g.add(new ArrayList<>());
        for (int[] e : edges) {
            g.get(e[0]).add(e[1]);
            g.get(e[1]).add(e[0]);
        }
        return g;
    }

    // Helper DFS that returns max depth from node
    private int dfs(int node, int parent, List<List<Integer>> g) {
        int max1 = 0, max2 = 0;          // two deepest children
        for (int nei : g.get(node)) {
            if (nei == parent) continue;
            int depth = dfs(nei, node, g);
            if (depth > max1) { max2 = max1; max1 = depth; }
            else if (depth > max2) { max2 = depth; }
        }
        // Update global diameter (path passes through node)
        diameter = Math.max(diameter, max1 + max2);
        return max1 + 1;                 // depth of subtree
    }

    private int diameter = 0;

    public int treeDiameter(int[][] edges) {
        if (edges == null || edges.length == 0) return 0;
        int n = edges.length + 1;
        List<List<Integer>> graph = buildGraph(n, edges);
        dfs(0, -1, graph);   // root at 0 (any node works)
        return diameter;
    }
}
```

*Complexities:*  
- **Time:** O(n)  
- **Space:** O(n) (adjacency list + recursion stack)

---

### 3.2 Python – Recursive DFS

```python
from collections import defaultdict
from typing import List

class Solution:
    def treeDiameter(self, edges: List[List[int]]) -> int:
        if not edges:
            return 0

        # Build adjacency list
        graph = defaultdict(list)
        for a, b in edges:
            graph[a].append(b)
            graph[b].append(a)

        self.diameter = 0

        def dfs(node: int, parent: int) -> int:
            depths = [0, 0]          # two longest child depths
            for nb in graph[node]:
                if nb == parent:
                    continue
                d = dfs(nb, node) + 1
                if d > depths[0]:
                    depths[1] = depths[0]
                    depths[0] = d
                elif d > depths[1]:
                    depths[1] = d
            self.diameter = max(self.diameter, depths[0] + depths[1])
            return depths[0]

        dfs(0, -1)  # start at arbitrary node
        return self.diameter
```

*Complexities:*  
- **Time:** O(n)  
- **Space:** O(n) (graph + recursion depth)

---

### 3.3 C++ – Iterative DFS (no recursion)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int treeDiameter(vector<vector<int>>& edges) {
        if (edges.empty()) return 0;
        int n = edges.size() + 1;

        // adjacency list
        vector<vector<int>> g(n);
        for (auto &e : edges) {
            g[e[0]].push_back(e[1]);
            g[e[1]].push_back(e[0]);
        }

        int diameter = 0;
        vector<int> parent(n, -1);
        vector<int> depth(n, 0);
        stack<int> st;
        st.push(0);                 // arbitrary root

        // Post‑order traversal
        vector<int> order;
        while (!st.empty()) {
            int v = st.top(); st.pop();
            order.push_back(v);
            for (int nb : g[v]) if (nb != parent[v]) {
                parent[nb] = v;
                st.push(nb);
            }
        }

        // Process nodes in reverse (post‑order)
        for (int i = order.size() - 1; i >= 0; --i) {
            int v = order[i];
            int best1 = 0, best2 = 0;
            for (int nb : g[v]) if (nb != parent[v]) {
                int d = depth[nb] + 1;
                if (d > best1) { best2 = best1; best1 = d; }
                else if (d > best2) { best2 = d; }
            }
            diameter = max(diameter, best1 + best2);
            depth[v] = best1;
        }
        return diameter;
    }
};
```

*Complexities:*  
- **Time:** O(n)  
- **Space:** O(n) (adjacency list + stack + arrays)

---

## 4. Why the Two‑Pass (or One‑Pass) DFS Works

1. **Longest Path Involves Two Leaves**  
   The diameter is always a path between two leaf nodes.  
   During DFS we track the longest and second‑longest depths from each node – the sum of these two depths is the longest path that passes through that node.

2. **Global Max Update**  
   While unwinding the recursion we keep a global `diameter` variable updated with `max(diameter, depth1 + depth2)`.

3. **Single Pass**  
   The algorithm visits each edge twice (once from each side), so overall work is linear.

---

## 5. Common Pitfalls & How to Avoid Them

| Pitfall | Symptom | Fix |
|---------|---------|-----|
| **Stack Overflow** (deep skewed trees) | RuntimeError / RecursionError | Use iterative DFS (C++ example) or increase recursion limit in Python (`sys.setrecursionlimit`). |
| **Incorrect Root Choice** | Wrong diameter | Any node works; just pick 0 or the first node from `edges`. |
| **Not Updating Parent** | Revisiting edges | Keep a `parent` array or pass parent in DFS to avoid cycles. |
| **Mis‑counting Edges vs Nodes** | Diameter off by 1 | Remember diameter counts *edges*, so add 1 when moving to child (`depth = childDepth + 1`). |

---

## 6. Interview & Resume Value

* **Data Structures:** Graph, adjacency list, recursion/stack.  
* **Algorithms:** Depth‑first search, dynamic programming on trees.  
* **Complexity Analysis:** Linear time & space – crucial for performance interviews.  
* **Testing & Edge‑Case Handling:** Demonstrates attention to detail.  

When writing your résumé:

```
- Solved LeetCode #1245 “Tree Diameter” – implemented efficient O(n) DFS solution in Java, Python, and C++.
- Demonstrated ability to analyze time/space trade‑offs and handle deep recursion via iterative approaches.
- Showcased strong understanding of graph traversal and dynamic programming on trees.
```

---

## 7. SEO‑Optimized Blog Article (For Job Seekers)

### Title
**“Cracking LeetCode 1245 – Tree Diameter: Java, Python, & C++ Solutions & Interview Tips”**

### Meta Description
> Master LeetCode “Tree Diameter” (1245) with clean Java, Python, and C++ code. Learn the algorithm, edge‑case handling, and interview‑ready explanations to land your next software‑engineering job.

### Headings & Content

| Heading | Purpose | Keywords |
|---------|---------|----------|
| **What is Tree Diameter?** | Problem definition. | tree diameter, LeetCode 1245 |
| **Why is it a Classic Interview Problem?** | Discuss algorithmic relevance. | software interview, data structures |
| **Two‑Pass DFS Explained** | Core algorithm. | DFS, tree algorithms, O(n) |
| **Java Implementation** | Full code + explanation. | Java DFS, LeetCode Java |
| **Python Implementation** | Full code + explanation. | Python DFS, LeetCode Python |
| **C++ Implementation** | Full code + explanation. | C++ DFS, LeetCode C++ |
| **Common Mistakes & Debugging** | Pitfalls. | LeetCode pitfalls, recursion depth |
| **Performance & Complexity** | Time/space. | algorithm analysis, O(n) |
| **Interview Take‑aways** | How to discuss solution. | interview tips, algorithm talk |
| **Add to Your Resume** | How to phrase. | resume tips, coding interview |

### Sample Intro Paragraph
> *“The Tree Diameter problem (LeetCode #1245) is a staple for software‑engineering interviews. It tests your understanding of graph traversal, recursion, and dynamic programming on trees. In this post we’ll walk through the optimal O(n) solution, show you clean Java, Python, and C++ implementations, and give you interview‑ready talking points that will impress hiring managers.”*

### Closing CTA
> “Try the code on LeetCode, add it to your GitHub, and mention it in your next interview. Want more interview prep? Subscribe to our newsletter for weekly problem walkthroughs.”

---

## 8. Final Checklist for Interviewers

- **Explain the algorithm** in simple terms.  
- **Show the code** and highlight where diameter is updated.  
- **Discuss edge cases** (empty graph, single node).  
- **Talk about complexity** and potential stack limits.  
- **Mention trade‑offs** (recursive vs iterative).  

Good luck landing that role – you’ve just solved a classic problem in three major languages! 🚀

--- 

**Happy coding!**