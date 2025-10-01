---
title: LeetCode 3313. Find the Last Marked Nodes in Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # LeetCode 3313 – Find the Last Marked Nodes in a Tree  
**Hard | Java | Python | C++**  

> **TL;DR** – The last node to be marked when you start from node *i* is always one of the two endpoints of the *diameter* of the tree.  
> Compute the diameter twice (two BFS/DFS) → you get the two endpoints *a* and *b*.  
> For every node *i*, compare its distances to *a* and *b*; the farther endpoint is the answer.

Below you will find:

1. A **blog‑style explanation** that you can paste on LinkedIn, Medium, or your personal site to impress recruiters.  
2. **Three full implementations** (Java, Python, C++) with clear comments.  
3. A quick **SEO cheat‑sheet** to help your post rank for interview‑related searches.

---

## 1. Problem Restatement

You are given a tree with *n* nodes (`0 … n-1`).  
Initially all nodes are unmarked.  
At `t = 0` you mark one node `i`.  
At each subsequent second you simultaneously mark all unmarked nodes that are adjacent to at least one marked node.

> *Question*: For every starting node *i*, which node will be **last** to become marked?  
> If several nodes are tied, any one of them is accepted.

---

## 2. Intuition

The process is simply a *breadth‑first search* from the starting node.  
The distance (in edges) from the start to each node determines the *time* when that node gets marked.

For a fixed start *s*, the *last* node to be marked is the node that is farthest from *s* – the one with the maximum distance.  
So for each *s* we need the farthest node.

**Key Observation**  
In a tree, all nodes that are farthest from any node belong to the *diameter* (the longest path).  
Hence for every start *s*, the farthest node must be **one of the two endpoints of the diameter**.

> **Proof Sketch**  
> Let `d1` and `d2` be the endpoints of the diameter.  
> For any node *s*, its farthest node is either on the path from *s* to `d1` or to `d2`.  
> If it were somewhere else, you could extend the diameter further – contradiction.  

Thus the answer for node *i* is the diameter endpoint that is farther from *i*.

---

## 3. Algorithm

1. **Build adjacency list** from `edges`.  
2. **Find one diameter endpoint**:  
   * Run DFS/BFS from arbitrary node `0`.  
   * The farthest node found (`p1`) is one endpoint.  
3. **Find the other endpoint**:  
   * Run DFS/BFS from `p1` → farthest node (`p2`).  
4. **Compute distances from both endpoints**:  
   * Run DFS/BFS from `p1` → array `dist1`.  
   * Run DFS/BFS from `p2` → array `dist2`.  
5. **Answer array**:  
   * For each node `i`  
     ```  
     answer[i] = (dist1[i] > dist2[i]) ? p1 : p2;
     ```  
     (if equal, any endpoint is fine).  

All operations are linear in *n*.

---

## 4. Correctness Proof

We prove that the algorithm returns a valid last‑marked node for every start *s*.

**Lemma 1** – For any node *s*, its farthest node is an endpoint of the diameter.

*Proof.*  
Let `p1`–`p2` be a diameter path.  
Take any node *s*.  
Let `x` be the farthest node from *s*.  
Assume `x` is not `p1` or `p2`.  
Consider the unique paths `s–x` and `p1–p2`.  
The path `s–x` must meet `p1–p2` at some node `y`.  
The length of `p1–p2` is greater or equal to each of `s–y` and `y–p2`.  
But `s–x` extends `s–y` by at least one edge, so `x` lies on or beyond `p2`, contradicting the maximality of the diameter. ∎

**Lemma 2** – The algorithm correctly identifies the two diameter endpoints `p1` and `p2`.

*Proof.*  
Running BFS/DFS from an arbitrary node yields the farthest node `p1`.  
Any farthest node from any node is an endpoint of a diameter (by definition).  
Running a second BFS/DFS from `p1` yields the farthest node `p2`.  
Since the farthest from an endpoint is the other endpoint, `p2` is the opposite end. ∎

**Lemma 3** – For any node *s*, `max(dist1[s], dist2[s])` equals the distance from *s* to its farthest node.

*Proof.*  
By Lemma 1, the farthest node from *s* is either `p1` or `p2`.  
`dist1[s]` is the distance to `p1`; `dist2[s]` is the distance to `p2`.  
Hence the maximum of the two is exactly the distance to the farthest endpoint. ∎

**Theorem** – For every start node *s*, the algorithm outputs a node that is last to get marked.

*Proof.*  
Let `x` be the farthest node from *s*.  
By Lemma 3, `dist1[s] > dist2[s]` iff `x = p1`; otherwise `x = p2`.  
The algorithm picks exactly that endpoint as `answer[s]`.  
Since the time to mark a node equals its distance from *s*, the chosen node is indeed the last one. ∎

---

## 5. Complexity Analysis

| Step | Time | Memory |
|------|------|--------|
| Build adjacency list | **O(n)** | **O(n)** |
| Two DFS/BFS for diameter | **O(n)** | **O(n)** |
| Two BFS for distances | **O(n)** | **O(n)** |
| Final loop | **O(n)** | — |
| **Total** | **O(n)** | **O(n)** |

With *n* ≤ 10⁵ this comfortably fits within limits.

---

## 6. Edge Cases & Pitfalls

| Edge Case | What to Watch |
|-----------|---------------|
| Tree with only 2 nodes | Diameter endpoints are both nodes; answer alternates. |
| Star-shaped tree | Diameter endpoints are any two leaves; algorithm picks one arbitrarily but still valid. |
| Very deep tree (chain) | BFS depth can be up to 10⁵; avoid recursion depth issues – use iterative stack or BFS. |
| Duplicate endpoints | When `dist1[s] == dist2[s]`, any endpoint is acceptable – we choose `p2` for consistency. |

---

## 7. Reference Implementations

Below are clean, self‑contained solutions in **Java**, **Python**, and **C++**.

### 7.1 Java

```java
import java.util.*;

public class Solution {
    /** Build adjacency list */
    private List<Integer>[] buildGraph(int[][] edges) {
        int n = edges.length + 1;
        List<Integer>[] g = new ArrayList[n];
        for (int i = 0; i < n; i++) g[i] = new ArrayList<>();
        for (int[] e : edges) {
            g[e[0]].add(e[1]);
            g[e[1]].add(e[0]);
        }
        return g;
    }

    /** DFS that returns the farthest node and its distance */
    private int[] dfs(int start, List<Integer>[] g) {
        int n = g.length;
        int[] dist = new int[n];
        Arrays.fill(dist, -1);
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(start);
        dist[start] = 0;
        int farNode = start;

        while (!stack.isEmpty()) {
            int u = stack.pop();
            for (int v : g[u]) {
                if (dist[v] == -1) {
                    dist[v] = dist[u] + 1;
                    stack.push(v);
                    if (dist[v] > dist[farNode]) farNode = v;
                }
            }
        }
        return new int[]{farNode, dist[farNode]};
    }

    /** BFS that fills distance array from a root */
    private int[] bfs(int start, List<Integer>[] g) {
        int n = g.length;
        int[] dist = new int[n];
        Arrays.fill(dist, -1);
        Queue<Integer> q = new ArrayDeque<>();
        q.offer(start);
        dist[start] = 0;
        while (!q.isEmpty()) {
            int u = q.poll();
            for (int v : g[u]) {
                if (dist[v] == -1) {
                    dist[v] = dist[u] + 1;
                    q.offer(v);
                }
            }
        }
        return dist;
    }

    public int[] lastMarkedNodes(int[][] edges) {
        List<Integer>[] graph = buildGraph(edges);

        // 1st DFS: get one end of diameter
        int p1 = dfs(0, graph)[0];
        // 2nd DFS from p1: get the other end
        int p2 = dfs(p1, graph)[0];

        int[] dist1 = bfs(p1, graph);
        int[] dist2 = bfs(p2, graph);

        int n = graph.length;
        int[] ans = new int[n];
        for (int i = 0; i < n; i++) {
            ans[i] = (dist1[i] > dist2[i]) ? p1 : p2;
        }
        return ans;
    }
}
```

### 7.2 Python

```python
from collections import defaultdict, deque
from typing import List

class Solution:
    def lastMarkedNodes(self, edges: List[List[int]]) -> List[int]:
        n = len(edges) + 1
        g = defaultdict(list)
        for a, b in edges:
            g[a].append(b)
            g[b].append(a)

        # Helper to find farthest node from a root
        def farthest(root: int):
            dist = [-1] * n
            dist[root] = 0
            q = deque([root])
            far = root
            while q:
                u = q.popleft()
                for v in g[u]:
                    if dist[v] == -1:
                        dist[v] = dist[u] + 1
                        q.append(v)
                        if dist[v] > dist[far]:
                            far = v
            return far, dist[far]

        # Diameter endpoints
        p1, _ = farthest(0)
        p2, _ = farthest(p1)

        # Distances from endpoints
        def bfs(root: int):
            dist = [-1] * n
            dist[root] = 0
            q = deque([root])
            while q:
                u = q.popleft()
                for v in g[u]:
                    if dist[v] == -1:
                        dist[v] = dist[u] + 1
                        q.append(v)
            return dist

        dist1 = bfs(p1)
        dist2 = bfs(p2)

        return [p1 if d1 > d2 else p2 for d1, d2 in zip(dist1, dist2)]
```

### 7.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> buildGraph(const vector<vector<int>>& edges) {
        int n = edges.size() + 1;
        vector<vector<int>> g(n);
        for (auto &e : edges) {
            g[e[0]].push_back(e[1]);
            g[e[1]].push_back(e[0]);
        }
        return g;
    }

    /** Return farthest node from start */
    pair<int,int> dfs(int start, const vector<vector<int>>& g) {
        int n = g.size();
        vector<int> dist(n, -1);
        stack<int> st;
        st.push(start);
        dist[start] = 0;
        int far = start;

        while (!st.empty()) {
            int u = st.top(); st.pop();
            for (int v : g[u]) if (dist[v]==-1) {
                dist[v] = dist[u] + 1;
                st.push(v);
                if (dist[v] > dist[far]) far = v;
            }
        }
        return {far, dist[far]};
    }

    vector<int> bfs(int start, const vector<vector<int>>& g) {
        int n = g.size();
        vector<int> dist(n, -1);
        queue<int> q;
        q.push(start);
        dist[start] = 0;
        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (int v : g[u]) if (dist[v]==-1) {
                dist[v] = dist[u] + 1;
                q.push(v);
            }
        }
        return dist;
    }

    vector<int> lastMarkedNodes(vector<vector<int>>& edges) {
        auto graph = buildGraph(edges);

        int p1 = dfs(0, graph).first;
        int p2 = dfs(p1, graph).first;

        auto dist1 = bfs(p1, graph);
        auto dist2 = bfs(p2, graph);

        int n = graph.size();
        vector<int> ans(n);
        for (int i = 0; i < n; ++i)
            ans[i] = (dist1[i] > dist2[i]) ? p1 : p2;
        return ans;
    }
};
```

---

## 8. Summary & Take‑away

- **Problem reduces to finding the farthest node for each start.**  
- **All farthest nodes belong to the diameter.**  
- **Two linear passes suffice** to discover diameter endpoints and distances.  
- **Answer per node** is simply the farther diameter endpoint.

This elegant linear‑time solution is ideal for interviews and contests, showcasing classic tree tricks: *diameter* + *distance comparison*.

---

## 9. Bonus: Where Else This Trick Helps?

| Problem | Why diameter trick works |
|---------|--------------------------|
| “Given a start, find the deepest node in a tree.” | Same reasoning – farthest is an endpoint. |
| “Compute the eccentricity of every node.” | Eccentricity = max distance to either endpoint. |
| “Find the tree’s center(s).” | Center is the middle of the diameter; the algorithm already has `p1`, `p2`. |
| “Last vertex to receive a flood fill.” | Same as last‑marked node. |

Feel free to adapt the code above for those variations!

---

### 10. Final Thought

A tree’s structure is surprisingly powerful: many seemingly complex queries collapse to simple diameter endpoint comparisons.  
Mastering this pattern saves time in contests and demonstrates a deep understanding of tree properties.

Good luck coding! 🚀

---

*End of the solution.*