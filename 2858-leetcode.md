---
title: LeetCode 2858. Minimum Edge Reversals So Every Node Is Reachable - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀  LeetCode 2858 – Minimum Edge Reversals So Every Node Is Reachable  
*An interview‑friendly, O(n) solution in **Java**, **Python** and **C++***  

---

### 1️⃣ Problem Recap

You’re given a **directed graph** that becomes an undirected tree if all edges are treated as bi‑directional.  
For every node `i` (0 ≤ i < n) you must compute the *minimum number of edge reversals* needed so that starting from `i` you can reach **every** other node.

```
n  – number of nodes (2 ≤ n ≤ 10⁵)
edges – (n‑1) directed edges: edges[i] = [ui, vi]  (ui → vi)
```

Return an array `answer` of length `n`, where `answer[i]` is the minimal reversals for root `i`.

---

### 2️⃣ Why O(n²) Is Too Slow

A naïve solution runs a BFS/DFS from each node and counts how many edges must be flipped – that’s O(n) per root → **O(n²)**, which is impossible for n = 10⁵.

---

### 3️⃣ Insight – *Re‑rooting DP*

Treat the underlying undirected tree as an **adjacency list** where each edge has two possible directions:

| Edge | Cost if traversed as original | Cost if traversed in reverse |
|------|------------------------------|-----------------------------|
| u → v | 0 | 1 |

**Step 1 – Pick any root** (say node 0) and run a DFS that adds the edge cost to the answer.  
This gives `cost[0]`, the number of reversals needed when 0 is the start.

**Step 2 – Re‑root**  
When we move the root from `u` to an adjacent node `v`, only the direction of the single edge (u,v) changes.  
- If the original direction was `u → v` (cost 0), moving the root to `v` forces us to traverse `v → u` → +1 reversal.
- If the original direction was `v → u` (cost 1), moving the root to `v` removes that reversal → –1.

Hence

```
cost[v] = cost[u] + (1 – 2 * originalCost(u → v))
```

A second DFS propagates `cost[]` to all nodes in **O(n)** time.

The whole algorithm is **O(n)** time, **O(n)** space – perfect for the constraints.

---

### 4️⃣ Edge Cases

| Edge | Why it matters | Handling |
|------|----------------|----------|
| Multiple children | None – the tree property guarantees a single path | The algorithm works regardless of branching |
| Large `n` | Recursion depth might overflow in languages with small stack | Use iterative DFS or increase recursion limit |
| Graph with “reverse” edges only | `cost[0]` may be non‑zero | No special handling – algorithm counts them correctly |

---

### 5️⃣ Code Implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.

> ⚠️ **Stack size**:  
> - Java: the recursion depth is at most 10⁵; use `-Xss1M` if you hit stack overflow.  
> - Python: `sys.setrecursionlimit(2_000_000)`  
> - C++: tail‑recursion optimisation is usually fine; otherwise use an explicit stack.

---

#### 5.1 Java

```java
import java.util.*;

public class Solution {
    // adjacency list: each element is (neighbor, cost)
    private List<int[]>[] graph;
    private int[] cost;

    public int[] minEdgeReversals(int n, int[][] edges) {
        buildGraph(n, edges);

        cost = new int[n];
        boolean[] visited = new boolean[n];

        // Step 1: DFS from node 0 – calculate cost[0]
        dfs(0, visited);

        // Step 2: Re-root to propagate costs
        Arrays.fill(visited, false);
        reroot(0, visited);

        return cost;
    }

    private void buildGraph(int n, int[][] edges) {
        graph = new List[n];
        for (int i = 0; i < n; i++) graph[i] = new ArrayList<>();

        for (int[] e : edges) {
            int u = e[0], v = e[1];
            graph[u].add(new int[]{v, 0}); // original direction
            graph[v].add(new int[]{u, 1}); // reversed direction
        }
    }

    // DFS that returns total reversals needed for subtree rooted at 'node'
    private int dfs(int node, boolean[] visited) {
        visited[node] = true;
        int total = 0;
        for (int[] nb : graph[node]) {
            int next = nb[0], costEdge = nb[1];
            if (!visited[next]) {
                total += costEdge + dfs(next, visited);
            }
        }
        cost[node] = total;
        return total;
    }

    // Re-rooting DFS – updates cost for all nodes
    private void reroot(int node, boolean[] visited) {
        visited[node] = true;
        for (int[] nb : graph[node]) {
            int next = nb[0], costEdge = nb[1];
            if (!visited[next]) {
                // cost[next] = cost[node] + 1 - 2 * costEdge
                cost[next] = cost[node] + 1 - 2 * costEdge;
                reroot(next, visited);
            }
        }
    }
}
```

---

#### 5.2 Python

```python
import sys
from collections import defaultdict

class Solution:
    def minEdgeReversals(self, n: int, edges: list[list[int]]) -> list[int]:
        sys.setrecursionlimit(2_000_000)

        # Build weighted adjacency list
        graph = defaultdict(list)
        for u, v in edges:
            graph[u].append((v, 0))  # original
            graph[v].append((u, 1))  # reverse

        cost = [0] * n
        visited = [False] * n

        def dfs(node: int) -> int:
            visited[node] = True
            total = 0
            for nxt, w in graph[node]:
                if not visited[nxt]:
                    total += w + dfs(nxt)
            cost[node] = total
            return total

        def reroot(node: int):
            visited[node] = True
            for nxt, w in graph[node]:
                if not visited[nxt]:
                    cost[nxt] = cost[node] + 1 - 2 * w
                    reroot(nxt)

        dfs(0)
        visited = [False] * n
        reroot(0)
        return cost
```

---

#### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    vector<vector<pair<int,int>>> g;  // (neighbor, cost)
    vector<int> cost;
public:
    vector<int> minEdgeReversals(int n, vector<vector<int>>& edges) {
        buildGraph(n, edges);
        cost.assign(n, 0);
        vector<char> vis(n, 0);

        // Step 1: DFS from 0
        dfs(0, vis);

        // Step 2: Re-rooting
        fill(vis.begin(), vis.end(), 0);
        reroot(0, vis);
        return cost;
    }

private:
    void buildGraph(int n, const vector<vector<int>>& edges) {
        g.assign(n, {});
        for (const auto& e : edges) {
            int u = e[0], v = e[1];
            g[u].push_back({v, 0}); // original
            g[v].push_back({u, 1}); // reverse
        }
    }

    int dfs(int node, vector<char>& vis) {
        vis[node] = 1;
        int tot = 0;
        for (auto [to, w] : g[node]) {
            if (!vis[to]) {
                tot += w + dfs(to, vis);
            }
        }
        cost[node] = tot;
        return tot;
    }

    void reroot(int node, vector<char>& vis) {
        vis[node] = 1;
        for (auto [to, w] : g[node]) {
            if (!vis[to]) {
                cost[to] = cost[node] + 1 - 2 * w;
                reroot(to, vis);
            }
        }
    }
};
```

---

### 6️⃣ Interview‑Ready Tips

| Tip | Why it matters |
|-----|----------------|
| **Explain the re‑rooting trick early** | Saves time and shows you understand tree DP |
| **Show the two‑pass DFS explicitly** | Many candidates write a single DFS and get stuck |
| **Mention stack limits** | Interviewers love it when you foresee practical issues |
| **Use a weighted adjacency list** | Keeps the code concise and avoids two separate arrays |

---

### 7️⃣ 📚 How the Blog Helps You

- **Clear structure** – Intro → Insight → Edge Cases → Code → Complexity.  
- **“Good, Bad, Ugly”** section tells you what makes the problem hard, why a naïve approach fails, and how re‑rooting turns it into a clean DP.  
- **SEO keywords** (`leetcode`, `minimum edge reversals`, `tree DP`, `re‑rooting`, `interview question`) help recruiters and search engines find this guide.  
- **Real‑world coding snippets** in 3 major languages.  

---

## 🎯 Final Thoughts

*LeetCode 2858* is a classic “tree + directed edge weights” problem.  
The key is to **turn a direction into a cost** and **propagate the root** with a single pass of the tree.  
This O(n) DP is not only fast but also elegant – a perfect candidate for both coding interviews and production code.

Good luck on your next interview, and happy coding!