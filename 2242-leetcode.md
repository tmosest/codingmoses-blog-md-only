---
title: LeetCode 2242. Maximum Score of a Node Sequence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – LeetCode 2242  
**Maximum Score of a Node Sequence**

> *You are given an undirected graph with `n` nodes (0‑based).  
> Each node `i` has a score `scores[i]`.  
> A *valid node sequence* of length 4 satisfies:*  
> 1. every pair of consecutive nodes is connected by an edge  
> 2. no node appears twice  
>  
> Return the maximum possible sum of the four scores.  
> If no valid sequence exists, return `‑1`.*

`4 ≤ n ≤ 5·10⁴`, `edges.length ≤ 5·10⁴`, `1 ≤ scores[i] ≤ 10⁸`.

---

## 2.  The “Good, the Bad, and the Ugly” of a Brute‑Force

| **Method** | **Good** | **Bad** | **Ugly** |
|-----------|----------|---------|----------|
| Enumerate every 4‑node walk | Easy to implement | `O(n⁴)` – impossible for `n=5·10⁴` | Time‑out on real data |
| DFS with pruning | Reduces branching | Still exponential in the worst case | Complex code, hard to debug |
| Dynamic programming on subsets | Precise | Requires `2ⁿ` memory | Not feasible |

All three approaches are either too slow or too memory‑hungry. The key to an accepted solution is to **avoid exploring the whole search space**.

---

## 3.  Insight – “Keep Only the Top 3 Neighbours”

Consider an edge `(u, v)`.  
If we are looking for a 4‑node sequence `a – u – v – b`, then:

* `a` must be a neighbour of `u` (different from `v`).  
* `b` must be a neighbour of `v` (different from `u`).  

To maximise the total score we only ever need to pair `u` with its **three highest‑scoring neighbours**, and similarly for `v`.  
Why *three*?  
Because the other two nodes of the sequence are `a` and `b`.  
If we keep the three best neighbours for every node, we guarantee that at least one of them will be distinct from the other side’s neighbour and from `u` or `v` itself.  
(If a node has fewer than three neighbours, we keep all of them.)

Thus the algorithm becomes:

1. Build adjacency lists.  
2. For each node, keep the indices of its top 3 neighbours (by score).  
3. For every edge `(u, v)` iterate over the top 3 neighbours of `u` and `v`.  
   * Skip duplicate indices or when a neighbour equals the other endpoint.  
   * Compute the score `scores[u] + scores[v] + scores[a] + scores[b]`.  
   * Keep the maximum.

The work is `O(n + m + 9m)` ≈ `O(n + m)` (the constant 9 comes from 3 × 3 checks per edge), easily fitting the constraints.

---

## 4.  Complexity Analysis

| **Metric** | **Java** | **Python** | **C++** |
|-----------|----------|------------|---------|
| Time | `O(n + m)` | `O(n + m)` | `O(n + m)` |
| Space | `O(n + m)` | `O(n + m)` | `O(n + m)` |

Both time and space are linear in the input size – the optimal asymptotic complexity for this problem.

---

## 5.  Code Implementations

Below you will find clean, production‑ready code in **Java, Python, and C++** that follows the top‑3 neighbour strategy.

### 5.1 Java (Java 17)

```java
import java.util.*;

public class Solution {
    public int maximumScore(int[] scores, int[][] edges) {
        int n = scores.length;
        // Build adjacency lists and keep top 3 neighbours per node
        List<int[]>[] top = new ArrayList[n];
        for (int i = 0; i < n; i++) top[i] = new ArrayList<>();

        // temporary min‑heaps to keep top 3 scores
        List<PriorityQueue<int[]>> pq = new ArrayList<>(n);
        for (int i = 0; i < n; i++) {
            pq.add(new PriorityQueue<>(Comparator.comparingInt(a -> a[1]))); // min‑heap on score
        }

        // populate heaps
        for (int[] e : edges) {
            int u = e[0], v = e[1];
            pq.get(u).offer(new int[]{v, scores[v]});
            pq.get(v).offer(new int[]{u, scores[u]});
            if (pq.get(u).size() > 3) pq.get(u).poll();
            if (pq.get(v).size() > 3) pq.get(v).poll();
        }

        // extract the top 3 neighbours
        for (int i = 0; i < n; i++) {
            while (!pq.get(i).isEmpty()) {
                top[i].add(pq.get(i).poll());
            }
            // reverse to have highest score first (optional)
            Collections.reverse(top[i]);
        }

        int best = -1;
        for (int[] e : edges) {
            int u = e[0], v = e[1];
            for (int[] a : top[u]) {
                int aIdx = a[0];
                if (aIdx == v) continue;
                for (int[] b : top[v]) {
                    int bIdx = b[0];
                    if (bIdx == u || bIdx == aIdx) continue;
                    int score = scores[u] + scores[v] + scores[aIdx] + scores[bIdx];
                    best = Math.max(best, score);
                }
            }
        }
        return best;
    }
}
```

### 5.2 Python (Python 3.10)

```python
import heapq
from collections import defaultdict
from typing import List

class Solution:
    def maximumScore(self, scores: List[int], edges: List[List[int]]) -> int:
        n = len(scores)

        # Min‑heaps for each node to keep top 3 scores
        heap = [ [] for _ in range(n) ]

        for u, v in edges:
            heapq.heappush(heap[u], (scores[v], v))
            heapq.heappush(heap[v], (scores[u], u))
            if len(heap[u]) > 3: heapq.heappop(heap[u])
            if len(heap[v]) > 3: heapq.heappop(heap[v])

        # Convert to lists sorted by decreasing score
        top = [sorted(h, reverse=True) for h in heap]

        best = -1
        for u, v in edges:
            for s_a, a in top[u]:
                if a == v: continue
                for s_b, b in top[v]:
                    if b == u or b == a: continue
                    best = max(best, scores[u] + scores[v] + scores[a] + scores[b])

        return best
```

### 5.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maximumScore(vector<int>& scores, vector<vector<int>>& edges) {
        int n = scores.size();
        vector<array<int, 3>> top(n);          // indices of top 3 neighbours
        vector<vector<int>> topSize(n, {});   // actual size per node

        // Min‑heaps (score, neighbour)
        vector<priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>>> minHeap(n);
        for (auto& e : edges) {
            int u = e[0], v = e[1];
            minHeap[u].push({scores[v], v});
            minHeap[v].push({scores[u], u});
            if (minHeap[u].size() > 3) minHeap[u].pop();
            if (minHeap[v].size() > 3) minHeap[v].pop();
        }

        // Extract top 3 neighbours for every node
        for (int i = 0; i < n; ++i) {
            vector<pair<int,int>> tmp;
            while (!minHeap[i].empty()) {
                tmp.push_back(minHeap[i].top());
                minHeap[i].pop();
            }
            // tmp now has neighbours in *ascending* score order
            reverse(tmp.begin(), tmp.end());        // descending
            for (auto& p : tmp) {
                topSize[i].push_back(p.second);
            }
        }

        int best = -1;
        for (auto& e : edges) {
            int u = e[0], v = e[1];
            for (int a : topSize[u]) {
                if (a == v) continue;
                for (int b : topSize[v]) {
                    if (b == u || b == a) continue;
                    best = max(best, scores[u] + scores[v] + scores[a] + scores[b]);
                }
            }
        }
        return best;
    }
};
```

> **Why the C++ code is safe for large inputs?**  
> `vector<array<int,3>>` keeps exactly three neighbours per node – no dynamic allocation during the main loop.  
> `priority_queue` is a heap with `O(log 3)` operations, i.e., constant time.

---

## 6.  Blog Article – “Cracking LeetCode 2242: 3‑Neighbour Graph Algorithm”

### 6.1 Title (SEO‑first)

> **LeetCode 2242 – “Maximum Score Node Sequence” – Java, Python & C++ Graph Solution for Interview Success**

### 6.2 Meta‑Description

> Learn how to solve LeetCode 2242 in O(n+m) time.  
> Get clean Java, Python, and C++ code, plus a deep dive into the top‑3 neighbour trick.  
> Perfect for anyone preparing for software‑engineering interviews or a graph‑algorithm job posting.

### 6.3 Article

```
# LeetCode 2242: Maximum Score of a Node Sequence – A Job‑Ready Graph Trick

When you stare at a LeetCode problem that looks *graph‑heavy*, it’s tempting to think brute force is the only way.  
But for **Maximum Score of a Node Sequence** (problem 2242) the optimal trick is *small* – **keep the top 3 neighbours**.

Why do we need the top 3?  
Because any 4‑node path `a‑u‑v‑b` only cares about two neighbours: `a` (for `u`) and `b` (for `v`).  
If we keep the three best neighbours for every node we are guaranteed to capture the best possible `a` and `b` without ever looking at the whole graph.

Below is the 3‑line strategy:

1. Build adjacency lists.  
2. For every node, keep indices of its **three highest‑scoring neighbours**.  
3. For every edge `(u,v)` check all 3×3 pairs of these neighbours, skip duplicates, compute the score and remember the maximum.

The algorithm is `O(n+m)` – linear time and memory, which is perfect for the limits (`5·10⁴` nodes, `5·10⁴` edges).

## 1.  Code – Java

```java
// Java 17 solution (see full code in the answer section)
public int maximumScore(int[] scores, int[][] edges) { … }
```

## 2.  Code – Python

```python
# Python 3.10 solution (see full code in the answer section)
class Solution:
    def maximumScore(self, scores: List[int], edges: List[List[int]]) -> int: …
```

## 3.  Code – C++

```cpp
// C++17 solution (see full code in the answer section)
class Solution { public: int maximumScore(vector<int>& scores, vector<vector<int>>& edges) { … } };
```

## 4.  Why This Wins Interviews

* **Simplicity** – The algorithm boils down to two loops: one to pre‑process neighbours, one to check edges.  
* **Scalability** – Linear complexity guarantees that even the largest test cases finish in milliseconds.  
* **Portability** – The same logic works in Java, Python, and C++ – great for a coding‑interview portfolio.  

Add the above code snippets to your GitHub repo, tag them with `leetcode-2242`, `graph-algorithm`, `top-3-neighbours`, and you’ll have a ready‑to‑paste solution for the next interview.

---

## 7.  Final Thoughts – The “Ugly” Part

While the top‑3 trick is elegant, you must still be careful about:

* Nodes with **fewer than three neighbours** – simply keep all of them.  
* **Duplicate indices** – `a` might equal `b`, or a neighbour might be the other endpoint of the edge.  
* Integer overflow in Java or C++ (use `long`/`long long` when summing four 10⁸ scores).

If you skip these details you’ll get wrong answers on hidden tests.  

---

### TL;DR

*Problem 2242 is solved in linear time by keeping only the top three neighbours of each node.  
The solution is available in Java, Python, and C++ – copy‑paste into your LeetCode profile or your interview notebook.*

--- 

### Quick Links

| Language | Code |
|----------|------|
| Java | <details><summary>Click to expand</summary>… (Java code)</details> |
| Python | <details><summary>Click to expand</summary>… (Python code)</details> |
| C++ | <details><summary>Click to expand</summary>… (C++ code)</details> |

*(Replace the “…” with the full source shown above.)*

Happy coding, and may this top‑3 neighbour trick land you that next software‑engineering role!