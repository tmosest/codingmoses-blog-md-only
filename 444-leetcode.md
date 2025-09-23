---
title: LeetCode 444. Sequence Reconstruction - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Sequence Reconstruction (LeetCode 444) – A Complete Multi‑Language Guide  
*(Java | Python | C++)*  

> **TL;DR**  
> The problem reduces to *topological sorting with a uniqueness check*.  
> Build a graph from all consecutive pairs in every subsequence.  
> Run Kahn’s algorithm: if at any step there are two nodes ready to be placed, the ordering is *not unique*.  
> Finally, compare the unique order to the given `nums`.  
> Complexity: **O(n + Σlen)** time, **O(n)** memory.  

---

## 📑 Table of Contents  
1. [Problem Overview](#1-problem-overview)  
2. [Approach & Algorithm](#2-approach--algorithm)  
   * 2.1 [Graph Construction](#21-graph-construction)  
   * 2.2 [Kahn’s Topological Sort + Uniqueness](#22-kahns-topological-sort--uniqueness)  
   * 2.3 [Why the Shortest Supersequence == Unique Topological Order](#23-why-the-shortest-supersequence--unique-topological-order)  
3. [Edge Cases & Pitfalls](#3-edge-cases--pitfalls)  
4. [Complexity Analysis](#4-complexity-analysis)  
5. [Code Implementations](#5-code-implementations)  
   * 5.1 [Java](#51-java)  
   * 5.2 [Python](#52-python)  
   * 5.3 [C++](#53-c++)  
6. [Good, Bad & Ugly](#6-good-bad--ugly)  
7. [SEO‑Optimized Blog Recap](#7-seo‑optimized-blog-recap)  
8. [Conclusion & Interview Tips](#8-conclusion--interview-tips)  

---

## 1️⃣ Problem Overview  

You’re given a permutation `nums` of `[1…n]` and a list `sequences` where each `sequences[i]` is a subsequence of `nums`.  
Return `true` **iff** `nums` is *the only shortest supersequence* that contains every subsequence in `sequences`.

> *Example*  
> `nums = [1,2,3]`  
> `sequences = [[1,2],[1,3],[2,3]]` → **true** (the only valid shortest supersequence is `[1,2,3]`).  

The challenge is to deduce whether the relative order of every pair of numbers is forced by the given subsequences.

---

## 2️⃣ Approach & Algorithm  

### 2.1 📐 Graph Construction  

Every consecutive pair `(a, b)` in a subsequence implies `a` must appear **before** `b`.  
We build a directed graph:

* **Nodes** – integers `1…n`.  
* **Edges** – from `a` to `b` for every consecutive pair in every subsequence.  
* **Indegrees** – number of incoming edges per node.  

Duplicates are harmless: we use a `Set` (or a boolean matrix for small `n`) to avoid counting the same edge twice.

### 2.2 🏃‍♂️ Kahn’s Topological Sort + Uniqueness Check  

1. **Initialize a queue** with all nodes having indegree `0`.  
2. While the queue isn’t empty:  
   * If the queue size > 1 → **more than one node can be placed next** → ordering isn’t unique → return `false`.  
   * Pop the sole element `u`, append to `order`.  
   * For every neighbor `v` of `u`: decrement `indegree[v]`; if it becomes `0`, push to queue.  
3. After processing, if the resulting `order` length is `< n`, there was a cycle → impossible → return `false`.  

The queue size check is the key to ensuring *uniqueness*. If the graph forces a single choice at every step, the resulting order is the *only* topological ordering.

### 2.3 🔑 Why Shortest Supersequence == Unique Topological Order  

A supersequence that respects all edges can be any topological order of the graph.  
The *shortest* such supersequence is simply a topological order of all `n` nodes.  
If there are two different topological orders, there exist at least two shortest supersequences → `nums` cannot be the unique one.  
Thus the uniqueness check of Kahn’s algorithm suffices.

---

## 3️⃣ Edge Cases & Pitfalls  

| Case | Why It Matters | Handling |
|------|----------------|----------|
| **Empty sequences** | No constraints → any order is valid | Return `false` unless `nums` is empty (but per constraints n≥1). |
| **Missing numbers in sequences** | Some numbers never appear → indegree 0 from start | They will be placed early; uniqueness depends on whether other nodes constrain them. |
| **Duplicate edges** | Can inflate indegree count | Use a `Set` per node or a `boolean[][]` to ignore duplicates. |
| **Large `n` (10⁴)** | Memory for adjacency list must be O(n) | Use `ArrayList<int[]>` or `List<Set<Integer>>`. |
| **Total length (10⁵)** | Must build graph in linear time | Iterate over each subsequence only once. |
| **Cyclic graph** | No valid supersequence | Kahn will finish with fewer than `n` nodes → return `false`. |

---

## 4️⃣ Complexity Analysis  

| Step | Time | Space |
|------|------|-------|
| Graph build (all pairs) | **O(Σlen)** | **O(n + Σlen)** (edges) |
| Kahn’s algorithm | **O(n + Σlen)** | **O(n)** (queue + indegree) |
| Final comparison | **O(n)** | – |

Total: **O(n + Σlen)** time, **O(n + Σlen)** space.

---

## 5️⃣ Code Implementations  

### 5.1 📄 Java  

```java
import java.util.*;

class Solution {
    public boolean sequenceReconstruction(int[] nums, List<List<Integer>> sequences) {
        int n = nums.length;
        // adjacency list + indegree array
        List<Set<Integer>> graph = new ArrayList<>(n + 1);
        int[] indeg = new int[n + 1];
        for (int i = 0; i <= n; i++) graph.add(new HashSet<>());

        // Build graph
        for (List<Integer> seq : sequences) {
            for (int i = 0; i + 1 < seq.size(); i++) {
                int u = seq.get(i), v = seq.get(i + 1);
                if (!graph.get(u).contains(v)) {
                    graph.get(u).add(v);
                    indeg[v]++;
                }
            }
        }

        // Kahn's algorithm with uniqueness check
        Deque<Integer> queue = new ArrayDeque<>();
        for (int i = 1; i <= n; i++) {
            if (indeg[i] == 0) queue.offer(i);
        }

        int idx = 0; // position in nums
        while (!queue.isEmpty()) {
            if (queue.size() > 1) return false;   // more than one choice -> not unique
            int cur = queue.poll();
            if (cur != nums[idx++]) return false; // order differs from given nums

            for (int nxt : graph.get(cur)) {
                if (--indeg[nxt] == 0) queue.offer(nxt);
            }
        }
        return idx == n; // all numbers placed
    }
}
```

> **Why a `Set` per node?**  
> Avoids counting duplicate edges which would inflate indegrees and break uniqueness.

---

### 5.2 🐍 Python  

```python
from collections import defaultdict, deque
from typing import List

class Solution:
    def sequenceReconstruction(self, nums: List[int], sequences: List[List[int]]) -> bool:
        n = len(nums)
        graph = defaultdict(set)
        indeg = [0] * (n + 1)

        # Build graph
        for seq in sequences:
            for u, v in zip(seq, seq[1:]):
                if v not in graph[u]:
                    graph[u].add(v)
                    indeg[v] += 1

        # Initialize queue
        q = deque([i for i in range(1, n + 1) if indeg[i] == 0])

        idx = 0
        while q:
            if len(q) > 1:          # multiple choices -> not unique
                return False
            cur = q.popleft()
            if cur != nums[idx]:    # order mismatch
                return False
            idx += 1
            for nxt in graph[cur]:
                indeg[nxt] -= 1
                if indeg[nxt] == 0:
                    q.append(nxt)

        return idx == n
```

---

### 5.3 🧩 C++  

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool sequenceReconstruction(vector<int>& nums, vector<vector<int>>& sequences) {
        int n = nums.size();
        vector<unordered_set<int>> graph(n + 1);
        vector<int> indeg(n + 1, 0);

        // Build graph
        for (auto &seq : sequences) {
            for (size_t i = 0; i + 1 < seq.size(); ++i) {
                int u = seq[i], v = seq[i + 1];
                if (!graph[u].count(v)) {
                    graph[u].insert(v);
                    ++indeg[v];
                }
            }
        }

        // Kahn's algorithm
        queue<int> q;
        for (int i = 1; i <= n; ++i)
            if (indeg[i] == 0) q.push(i);

        int idx = 0;
        while (!q.empty()) {
            if (q.size() > 1) return false;   // not unique
            int cur = q.front(); q.pop();
            if (cur != nums[idx++]) return false; // order differs

            for (int nxt : graph[cur]) {
                if (--indeg[nxt] == 0) q.push(nxt);
            }
        }
        return idx == n;
    }
};
```

> **Why `unordered_set`?**  
> Ensures edges are counted once without extra memory overhead.

---

## 6️⃣ Good, Bad & Ugly  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Efficiency** | Linear in input size | None | None |
| **Space Efficiency** | O(n + Σlen) | Using `Set` per node may use extra memory for dense graphs | A boolean matrix (n×n) is impossible for n=10⁴ |
| **Readability** | Clear separation of graph build & Kahn | Too many nested loops can be confusing | Recursive DFS for uniqueness can overflow stack on large `n` |
| **Maintainability** | Self‑contained class, easy to unit‑test | Edge cases (empty sequences) require explicit handling | Forgetting to compare order with `nums` leads to wrong answers |

---

## 7️⃣ SEO‑Optimized Blog Recap  

> **Title:** *Master LeetCode 444 – Sequence Reconstruction: Top‑Down Topological Sort + Uniqueness*  
> **Meta Description:** *Solve LeetCode 444 in Java, Python, and C++ with a concise topological‑sort algorithm. Learn why uniqueness matters and how to handle cycles. Ideal for coding interview prep.*  

### Highlights  

1. **Problem Breakdown** – Understanding “unique shortest supersequence” in graph terms.  
2. **Graph Construction** – Convert consecutive pairs to directed edges.  
3. **Kahn’s Algorithm + Queue Size Check** – The secret to uniqueness.  
4. **Implementation** – Three code snippets: Java, Python, C++ – ready for copy‑paste.  
5. **Complexity** – O(n + Σlen) time, O(n + Σlen) space.  
6. **Edge‑Case Guide** – Avoid pitfalls like duplicate edges and cycles.  

**Keywords**: LeetCode 444, Sequence Reconstruction, Topological Sort, Uniqueness Check, Kahn's Algorithm, Java solution, Python solution, C++ solution, graph theory, coding interview.

---

## 8️⃣ Closing Thoughts  

LeetCode 444 is a textbook illustration of how *graph constraints* collapse a vast set of permutations into a single forced ordering.  
By building the graph from consecutive pairs and running Kahn’s algorithm with a queue‑size uniqueness check, we get a clean, linear solution that works across Java, Python, and C++.

Happy coding—and may your `nums` always be the *unique* shortest supersequence! 🚀

---