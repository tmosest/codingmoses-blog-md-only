---
title: LeetCode 444. Sequence Reconstruction - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Sequence Reconstruction – LeetCode 444  
### *A deep dive into the “good, the bad, and the ugly” of a top‑level interview problem*  

> **Keywords** – Sequence Reconstruction LeetCode 444, Topological Sort, Java, Python, C++, interview algorithm, job interview, algorithmic problem solving, uniqueness check, graph theory  

---

### 1. The Problem (in plain English)

You’re given:
- `nums` – a permutation of `1 … n`.  
- `sequences` – an array of subsequences that all appear in the original order of `nums`.

**Goal:**  
Return `true` iff `nums` is the *only* shortest super‑sequence that contains every subsequence in `sequences`.  
Otherwise return `false`.

In other words, we must check if the order in `nums` can be uniquely reconstructed from the given subsequences.  
If the reconstruction is unique, the topological ordering of the graph built from the subsequences is forced to match `nums`.  

---

### 2. Why Is It a Topological Sort Problem?

Each pair of consecutive numbers in a subsequence gives a directed edge  
`u → v` (meaning `u` must appear before `v`).  
All edges together form a directed graph whose topological order represents a possible super‑sequence.

If that topological order is unique, then the order of vertices is forced.  
If it’s not unique, there exist at least two valid orders, so `nums` cannot be the *only* shortest super‑sequence.

---

### 3. The Algorithm (Kahn’s BFS + Uniqueness Check)

1. **Build the graph**  
   - `adj[u]` – list of nodes that come after `u`.  
   - `indeg[v]` – number of incoming edges to `v`.

2. **Topological sort (Kahn’s algorithm)**  
   - Initialise a queue with all nodes that have `indeg == 0`.  
   - **Uniqueness test**: if at any step the queue contains **more than one** node, there are multiple possible orders → return `false`.  
   - Pop from queue, append to `order`, decrement indegree of neighbours, push new zero‑indegree nodes.

3. **Verify**  
   - If we processed all `n` nodes (no cycle), compare `order` with `nums`.  
   - If they’re identical → `true`; otherwise → `false`.

4. **Complexities**  
   - Time: `O(n + m)` where `m` = total length of all sequences.  
   - Space: `O(n + m)` for adjacency lists and indegree array.

---

## 4. Code – Three Languages

> **Tip:** The logic is identical across languages; only syntax changes.

### 4.1 Java

```java
import java.util.*;

class Solution {
    public boolean sequenceReconstruction(int[] nums, List<List<Integer>> sequences) {
        int n = nums.length;
        // adjacency list & indegree
        List<Integer>[] adj = new ArrayList[n + 1];
        int[] indeg = new int[n + 1];
        for (int i = 1; i <= n; i++) adj[i] = new ArrayList<>();

        // Build the graph
        for (List<Integer> seq : sequences) {
            for (int i = 1; i < seq.size(); i++) {
                int u = seq.get(i - 1), v = seq.get(i);
                if (!adj[u].contains(v)) {          // avoid duplicate edges
                    adj[u].add(v);
                    indeg[v]++;
                }
            }
        }

        // Kahn's algorithm with uniqueness check
        Queue<Integer> q = new ArrayDeque<>();
        for (int i = 1; i <= n; i++) if (indeg[i] == 0) q.offer(i);

        int idx = 0;
        while (!q.isEmpty()) {
            if (q.size() > 1) return false;     // more than one choice -> not unique
            int cur = q.poll();
            if (cur != nums[idx++]) return false; // order mismatch
            for (int nxt : adj[cur]) {
                if (--indeg[nxt] == 0) q.offer(nxt);
            }
        }
        return idx == n; // all nodes processed
    }
}
```

### 4.2 Python

```python
from collections import defaultdict, deque
from typing import List

class Solution:
    def sequenceReconstruction(self, nums: List[int], sequences: List[List[int]]) -> bool:
        n = len(nums)
        adj = defaultdict(set)
        indeg = [0] * (n + 1)

        # Build graph
        for seq in sequences:
            for u, v in zip(seq, seq[1:]):
                if v not in adj[u]:
                    adj[u].add(v)
                    indeg[v] += 1

        q = deque([i for i in range(1, n + 1) if indeg[i] == 0])
        idx = 0

        while q:
            if len(q) > 1:
                return False   # multiple possible orders
            cur = q.popleft()
            if cur != nums[idx]:
                return False   # wrong ordering
            idx += 1
            for nxt in adj[cur]:
                indeg[nxt] -= 1
                if indeg[nxt] == 0:
                    q.append(nxt)

        return idx == n
```

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool sequenceReconstruction(vector<int>& nums, vector<vector<int>>& sequences) {
        int n = nums.size();
        vector<unordered_set<int>> adj(n + 1);
        vector<int> indeg(n + 1, 0);

        // Build graph
        for (auto &seq : sequences) {
            for (size_t i = 1; i < seq.size(); ++i) {
                int u = seq[i-1], v = seq[i];
                if (!adj[u].count(v)) {
                    adj[u].insert(v);
                    indeg[v]++;
                }
            }
        }

        queue<int> q;
        for (int i = 1; i <= n; ++i)
            if (indeg[i] == 0) q.push(i);

        int idx = 0;
        while (!q.empty()) {
            if (q.size() > 1) return false;          // multiple choices
            int cur = q.front(); q.pop();
            if (cur != nums[idx++]) return false;    // order mismatch
            for (int nxt : adj[cur]) {
                if (--indeg[nxt] == 0) q.push(nxt);
            }
        }
        return idx == n; // processed all nodes
    }
};
```

---

## 5. The Good

| Aspect | Why It’s Great |
|--------|----------------|
| **Clear Graph Modeling** | Each subsequence gives directed edges, turning a “reconstruction” problem into a classic graph problem. |
| **Kahn’s Algorithm** | Simple O(n+m) BFS that naturally checks uniqueness by queue size. |
| **Linear Time & Space** | Fits easily into the constraints (`n, m ≤ 10⁵`). |
| **Idiomatic Code** | Each language version uses idiomatic data structures (`deque` in Python, `unordered_set` in C++). |

---

## 6. The Bad

| Issue | Mitigation |
|-------|------------|
| **Duplicate Edge Handling** | In Python/Java we check for duplicates (`adj[u].count(v)`). If we skip, indegree counts become wrong → false positives. |
| **Large Input** | Using `ArrayList` + `contains()` in Java can degrade to O(n²) if many duplicates. Use `HashSet` or `boolean[][]` for small `n`. |
| **Unordered Map Overhead** | `unordered_set` per node in C++ adds overhead; for dense graphs an adjacency matrix may be faster but uses O(n²) memory. |

---

## 7. The Ugly

| Problem | Explanation |
|---------|-------------|
| **Cycle Detection Absence** | The algorithm implicitly checks for cycles by verifying we processed all nodes (`idx == n`). However, if there’s a cycle, the queue will become empty prematurely and we return `false`. Some solutions forget this check. |
| **Recursion Stack in DFS** | A DFS‑based uniqueness check can cause stack overflow for `n = 10⁴`. Prefer iterative Kahn’s BFS. |
| **Misunderstanding “Shortest Supersequence”** | Some interviewers confuse “shortest” with “minimal number of nodes” when actually it’s *unique* ordering. Ensure you state the uniqueness requirement explicitly. |

---

## 8. SEO‑Optimized Takeaway

If you’re preparing for a software engineering interview, mastering **Sequence Reconstruction (LeetCode 444)** showcases:

- **Graph theory** knowledge (topological sort, DAG properties).  
- **Algorithmic thinking** – transforming a verbal problem into a graph model.  
- **Coding proficiency** – writing clean Java, Python, or C++ solutions.  
- **Problem‑solving clarity** – articulating the *good*, *bad*, and *ugly* aspects, which interviewers love to hear.

**Remember:**  
> “Uniqueness is the real secret.”  
If you can prove that the topological order is forced, you nail the problem.

---

## 9. Final Thoughts

1. **Implement once, test against edge cases**:  
   - Single element `nums`.  
   - Multiple subsequences with overlapping edges.  
   - Subsequence lists that form a cycle.  
2. **Time your solution**:  
   - LeetCode’s 100 ms limit for Java, 80 ms for C++, and 40 ms for Python.  
3. **Explain during the interview**:  
   - Show the graph, explain Kahn’s algorithm, and why queue size > 1 → multiple valid sequences.  

With this knowledge, you’ll not only solve LeetCode 444 but also impress interviewers with a solid grasp of graph algorithms and interview‑ready coding style.

Good luck on your next job interview! 🌟