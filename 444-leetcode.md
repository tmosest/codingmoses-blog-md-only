---
title: LeetCode 444. Sequence Reconstruction - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 444. Sequence Reconstruction – One‑Pass Topological Sort

**Problem**

You are given a permutation `nums` of length `n` and a list of subsequences `sequences`.  
Each `sequences[i]` is a subsequence of `nums`.  
Return `true` iff `nums` is the *only* shortest possible supersequence that contains every
`sequences[i]` as a subsequence; otherwise return `false`.

---

## Why This Problem Rocks for Interviews

- **Graph Theory + Topology** – Shows you understand directed graphs and Kahn’s algorithm.
- **Uniqueness Check** – Many candidates only check “is it a supersequence”, not whether it is *the only* one.
- **Performance** – With `n ≤ 10⁴` and total length of sequences ≤ `10⁵`, an `O(n + totalLen)` solution is required.
- **Real‑world Analogy** – Think of sequencing DNA fragments or merging logs from distributed systems.

---

## The Core Idea

1. **Build a directed graph of precedence constraints**  
   For each consecutive pair `(u, v)` in a subsequence, add an edge `u → v`.

2. **Run Kahn’s topological sort**  
   * While processing nodes with indegree `0`:
     * If the queue ever contains **more than one** node, the ordering is **not unique** → `false`.
     * Append the node to the result and decrease indegree of its neighbors.

3. **Compare the produced ordering with `nums`**  
   If every step produced a unique node *and* the final ordering equals `nums`, return `true`.

The algorithm runs in linear time with respect to the number of nodes and edges.

---

## Time & Space Complexity

| Metric | Formula | Reason |
|--------|---------|--------|
| **Time** | `O(n + Σlen(sequences[i]))` | Each edge is processed once. |
| **Space** | `O(n + Σlen(sequences[i]))` | Adjacency list + indegree array + queue. |

---

## Reference Implementations

Below are clean, one‑pass solutions in **Java, Python, and C++**.  
All three use the same logic described above.

### 1️⃣ Java

```java
import java.util.*;

public class Solution {
    public boolean sequenceReconstruction(int[] nums, List<List<Integer>> sequences) {
        int n = nums.length;
        // 1-indexed nodes for simplicity
        List<Set<Integer>> graph = new ArrayList<>(n + 1);
        for (int i = 0; i <= n; i++) graph.add(new HashSet<>());
        int[] indeg = new int[n + 1];

        // Build graph
        for (List<Integer> seq : sequences) {
            for (int i = 0; i < seq.size() - 1; i++) {
                int u = seq.get(i), v = seq.get(i + 1);
                if (!graph.get(u).contains(v)) {
                    graph.get(u).add(v);
                    indeg[v]++;
                }
            }
        }

        // Kahn's algorithm with uniqueness check
        Queue<Integer> q = new ArrayDeque<>();
        for (int i = 1; i <= n; i++)
            if (indeg[i] == 0) q.offer(i);

        int index = 0; // position in nums
        while (!q.isEmpty()) {
            if (q.size() > 1) return false; // more than one choice => not unique
            int cur = q.poll();
            if (cur != nums[index++]) return false; // order must match nums

            for (int nxt : graph.get(cur)) {
                if (--indeg[nxt] == 0) q.offer(nxt);
            }
        }

        return index == n; // all numbers processed
    }
}
```

### 2️⃣ Python

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

        q = deque([i for i in range(1, n + 1) if indeg[i] == 0])
        idx = 0

        while q:
            if len(q) > 1:          # multiple choices → not unique
                return False
            cur = q.popleft()
            if cur != nums[idx++]:  # order must match nums
                return False
            for nxt in graph[cur]:
                indeg[nxt] -= 1
                if indeg[nxt] == 0:
                    q.append(nxt)

        return idx == n
```

### 3️⃣ C++

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
        for (auto& seq : sequences) {
            for (size_t i = 0; i + 1 < seq.size(); ++i) {
                int u = seq[i], v = seq[i + 1];
                if (!graph[u].count(v)) {
                    graph[u].insert(v);
                    ++indeg[v];
                }
            }
        }

        queue<int> q;
        for (int i = 1; i <= n; ++i)
            if (indeg[i] == 0) q.push(i);

        int idx = 0;
        while (!q.empty()) {
            if (q.size() > 1) return false;   // not unique
            int cur = q.front(); q.pop();
            if (cur != nums[idx++]) return false; // order mismatch
            for (int nxt : graph[cur]) {
                if (--indeg[nxt] == 0) q.push(nxt);
            }
        }
        return idx == n;
    }
};
```

---

## Blog Article – “Sequence Reconstruction: The Good, The Bad, and The Ugly”

> **Target Keywords**: Leetcode 444, Sequence Reconstruction, Topological Sort, Unique Ordering, Java, Python, C++, Interview Preparation, Graph Algorithms

---

### Introduction

If you’re hunting a software engineering role, one of the most dreaded yet rewarding interview problems is *Sequence Reconstruction*. Leetcode 444 forces you to think in terms of graphs, topological sorting, and **uniqueness**. In this article we dissect the problem, show why it’s a “golden” interview question, and walk through the clean solution in three languages. We’ll also share the pitfalls (“bad”) and the tricky edge cases (“ugly”) that can trip you up.

---

### The Problem (Restated)

Given:

- `nums`: a permutation of `1…n`.
- `sequences`: a list of subsequences of `nums`.

Return `true` iff `nums` is the **only** shortest supersequence that contains all `sequences[i]` as subsequences.

---

### Why This Problem Matters

| Aspect | Why it’s Important |
|--------|--------------------|
| **Graph Modelling** | Turns a seemingly simple ordering problem into a directed acyclic graph (DAG). |
| **Topological Sorting** | Requires you to know Kahn’s algorithm or DFS‑based topological sort. |
| **Uniqueness Check** | Many candidates only check for *any* valid ordering. The interviewer wants *uniqueness* (queue size > 1). |
| **Performance Constraints** | Large `n` and total sequence length forces an `O(n + totalLen)` solution, not `O(n²)` brute force. |

---

### The “Good” – The Elegant One‑Pass Solution

1. **Build the DAG**  
   For each consecutive pair `(u, v)` in a subsequence, add an edge `u → v`. Use a `Set` per node to avoid duplicate edges.

2. **Kahn’s Algorithm + Uniqueness**  
   * Start with all nodes of indegree `0`.  
   * If the queue ever contains more than one node → ordering is **not unique** → return `false`.  
   * Pop the single node, compare it to the current element in `nums`. Any mismatch means `nums` is not a valid ordering → `false`.  
   * Decrease indegrees of neighbors; add any that become zero to the queue.

3. **Final Check**  
   After processing all nodes, if we never hit a non‑unique step and the produced ordering equals `nums`, return `true`.

**Why One Pass?**  
All actions (building graph, Kahn’s loop, uniqueness test) are performed in a single sweep over the data. Memory usage stays linear.

---

### The “Bad” – Common Pitfalls

| Mistake | Why It Fails |
|---------|--------------|
| **Ignoring Duplicate Edges** | Incrementing indegree twice for the same edge corrupts the algorithm. |
| **Using DFS Without Uniqueness Check** | DFS only confirms a topological order; it doesn’t detect multiple valid orders. |
| **Checking Only Final Order** | Two different orders can produce the same `nums` if some elements are unconstrained—Kahn’s uniqueness rule is needed. |
| **Assuming All Elements Appear in `sequences`** | If some numbers are missing, the queue will contain multiple nodes → answer must be `false`. |
| **O(n²) Edge Construction** | Using a 2‑D array for edges when `n` is 10⁴ leads to memory/time blow‑up. |

---

### The “Ugly” – Edge Cases That Tricked Even the Smartest

1. **Partial Coverage**  
   ```
   nums = [1,2,3,4]
   sequences = [[1,2]]
   ```
   The shortest supersequence is `[1,2]`; `nums` is longer → answer `false`.

2. **Multiple Valid Orders**  
   ```
   nums = [1,2,3]
   sequences = [[1,2],[1,3]]
   ```
   Both `[1,2,3]` and `[1,3,2]` satisfy the constraints. Queue size > 1 at the first step → `false`.

3. **Cyclic Input (should not happen per constraints)**  
   Defensive programming: detect a cycle (i.e., not all nodes processed) and return `false`.

4. **Large `n` but Small `sequences`**  
   Ensure the queue is initialized correctly even when many nodes have indegree `0`.

5. **Repeated Numbers in a Sequence**  
   Input like `[[1,1,2]]` could create self‑loops or duplicate edges. Using a `Set` prevents `indeg[1]` from increasing twice.

---

### Full Reference Code (Three Languages)

*(See the “Reference Implementations” section above.)*

---

### How to Nail This Question in an Interview

1. **Start by Modelling**  
   Draw a tiny graph from a sample input. Identify nodes, edges, and indegrees.

2. **Talk Through Kahn’s Algorithm**  
   Walk through the queue and indegree updates. Highlight where uniqueness is checked.

3. **Edge Cases First**  
   Ask the interviewer if they want you to handle missing numbers or cycles; this shows thoroughness.

4. **Code One‑Pass**  
   Keep the graph building and topological sort in a single method. Use `HashSet`/`unordered_set` to prevent duplicate edges.

5. **Testing**  
   Run the code against the “good”, “bad”, and “ugly” test cases. Mention that `assert`‑style tests help catch subtle bugs early.

---

### Final Thoughts

Sequence Reconstruction is a **microcosm of interview algorithm design**. Mastering it demonstrates:

- *Graph intuition* (modeling constraints as edges).
- *Algorithmic rigor* (Kahn’s algorithm with a queue‑size check).
- *Coding discipline* (O(1) pass, linear memory, defensive programming).

Practice the three implementations, test against a battery of corner cases, and you’ll walk into your next technical interview with confidence.

> **Pro Tip**: In a live coding interview, describe your approach verbally *before* coding. It shows you understand the problem structure and gives the interviewer time to correct misunderstandings.

Happy coding, and good luck on your next interview!

---

### TL;DR

Sequence Reconstruction boils down to building a DAG from subsequences, performing Kahn’s algorithm while checking for uniqueness, and finally verifying that the produced order matches `nums`. The linear, one‑pass algorithm is the cleanest, fastest, and most interview‑friendly solution. We’ve provided ready‑to‑copy code in Java, Python, and C++—so pick your favorite language and ace that interview!