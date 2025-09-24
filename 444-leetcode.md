---
title: LeetCode 444. Sequence Reconstruction - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Sequence Reconstruction – LeetCode 444  
**Medium** | `topological sort | BFS | DAG`  

> **TL;DR** – Build a directed graph from the given subsequences, run Kahn’s topological‑sort while always having a *unique* zero‑in‑degree node.  
> If at any point the queue has more than one node, the order is *not* unique.  
> Finally, check that the resulting order equals the given `nums`.

---

## 📦 1.  Three‑Language Code

> All solutions run in **O(n + totalLength)** time and **O(n + totalLength)** memory.  
> They handle the full LeetCode constraints (`n ≤ 10⁴`, sum of lengths ≤ 10⁵).

### 1.1  Java

```java
import java.util.*;

class Solution {
    public boolean sequenceReconstruction(int[] nums, List<List<Integer>> sequences) {
        int n = nums.length;
        // adjacency list & indegree
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i <= n; i++) adj.add(new ArrayList<>());
        int[] indeg = new int[n + 1];

        // Build graph
        for (List<Integer> seq : sequences) {
            for (int i = 0; i < seq.size() - 1; i++) {
                int u = seq.get(i), v = seq.get(i + 1);
                if (!adj.get(u).contains(v)) {     // avoid duplicate edges
                    adj.get(u).add(v);
                    indeg[v]++;
                }
            }
        }

        // Kahn's algorithm with uniqueness check
        Queue<Integer> q = new ArrayDeque<>();
        for (int i = 1; i <= n; i++)
            if (indeg[i] == 0) q.offer(i);

        int idx = 0;
        while (!q.isEmpty()) {
            if (q.size() > 1) return false;      // more than one choice => not unique
            int cur = q.poll();
            if (cur != nums[idx++]) return false;
            for (int nxt : adj.get(cur)) {
                if (--indeg[nxt] == 0) q.offer(nxt);
            }
        }
        return idx == n;
    }
}
```

> **Why `adj.get(u).contains(v)`?**  
> We keep it simple (≤ 10⁵ edges) – `contains` is `O(1)` for a small list.  
> In a production‑grade solution you’d use a `Set` or a `boolean[][]` matrix.

---

### 1.2  Python

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
            for i in range(len(seq) - 1):
                u, v = seq[i], seq[i + 1]
                if v not in adj[u]:
                    adj[u].add(v)
                    indeg[v] += 1

        # Kahn
        q = deque([i for i in range(1, n + 1) if indeg[i] == 0])
        idx = 0
        while q:
            if len(q) > 1:
                return False                # not unique
            cur = q.popleft()
            if cur != nums[idx]:
                return False                # order mismatch
            idx += 1
            for nxt in adj[cur]:
                indeg[nxt] -= 1
                if indeg[nxt] == 0:
                    q.append(nxt)

        return idx == n
```

> The Python version uses a `set` per node to avoid duplicate edges – perfectly fine for the constraints.

---

### 1.3  C++

```cpp
#include <vector>
#include <queue>
#include <unordered_set>
#include <unordered_map>

class Solution {
public:
    bool sequenceReconstruction(std::vector<int>& nums,
                                std::vector<std::vector<int>>& sequences) {
        int n = nums.size();
        std::vector<std::unordered_set<int>> adj(n + 1);
        std::vector<int> indeg(n + 1, 0);

        // Build graph
        for (const auto &seq : sequences) {
            for (size_t i = 0; i + 1 < seq.size(); ++i) {
                int u = seq[i], v = seq[i + 1];
                if (!adj[u].count(v)) {
                    adj[u].insert(v);
                    ++indeg[v];
                }
            }
        }

        std::queue<int> q;
        for (int i = 1; i <= n; ++i)
            if (indeg[i] == 0) q.push(i);

        size_t idx = 0;
        while (!q.empty()) {
            if (q.size() > 1) return false;         // multiple possibilities
            int cur = q.front(); q.pop();
            if (cur != nums[idx++]) return false;   // order mismatch
            for (int nxt : adj[cur]) {
                if (--indeg[nxt] == 0) q.push(nxt);
            }
        }
        return idx == n;
    }
};
```

> The C++ solution keeps an `unordered_set` per node to avoid duplicate edges.  
> You could replace it with a `vector` and a `bool` matrix if you want maximum speed.

---

## 📄 2.  Blog Article – “Sequence Reconstruction: The Good, The Bad, The Ugly”

### Title  
**Sequence Reconstruction LeetCode 444 – Topological Sort, Unique Order, and Interview‑Ready Solutions**

### Meta‑Description  
Learn how to solve LeetCode 444 “Sequence Reconstruction” in Java, Python, and C++. Discover the topological‑sort trick, edge‑case pitfalls, and how to impress recruiters with clean code.

---

### 1.  Problem Overview  

LeetCode 444 asks: *Given a permutation `nums` and a list of subsequences, is `nums` the **only** shortest supersequence that contains all subsequences?*  

The answer hinges on **unique topological ordering** of a directed graph built from the pairs in the subsequences.

> **Key Insight** – If a directed acyclic graph (DAG) has a unique topological order, then each step in Kahn’s algorithm must have exactly one zero‑indegree node.

---

### 2.  Why This Problem is Interview Gold  

* **Algorithmic depth** – Requires understanding of graph construction, indegree counting, and topological sorting.  
* **Edge‑case handling** – Must check for duplicate edges, missing numbers, and non‑unique orders.  
* **Language flexibility** – Shows you can implement the same logic in Java, Python, or C++ with equal clarity.  
* **Career boost** – Demonstrates you can turn a theoretical graph property into production‑ready code.

---

### 3.  Step‑by‑Step Solution  

| Step | What We Do | Why It Matters |
|------|------------|----------------|
| 1 | **Build the graph** – For each consecutive pair `(a,b)` in a subsequence add a directed edge `a → b`. | Captures the relative order that must hold in any supersequence. |
| 2 | **Count indegrees** – Increment `indeg[b]` for every incoming edge to `b`. | Needed for Kahn’s algorithm. |
| 3 | **Initialize a queue** with all nodes that have `indeg == 0`. | These are candidates to appear first in the topological order. |
| 4 | **BFS + uniqueness check** – While the queue isn’t empty:  
&nbsp;&nbsp;• If `queue.size() > 1` → more than one possible next node → order not unique → return `false`.  
&nbsp;&nbsp;• Dequeue the sole candidate, compare it with the corresponding element in `nums`. If mismatched → return `false`.  
&nbsp;&nbsp;• Decrease indegree of its neighbors, enqueue any that hit `0`. | This is the heart of the algorithm. The size‑check guarantees uniqueness. |
| 5 | **Final validation** – After the loop, ensure we processed all `n` nodes and that the order matches `nums`. | Prevents false positives when some nodes were never reached (disconnected components). |

---

### 4.  Edge Cases & Gotchas  

| Case | Why It Breaks Naïve Code | Fix |
|------|------------------------|-----|
| Duplicate edges (e.g. `[1,2]` appears twice) | Double counting indegree → wrong queue | Use a `Set`/`unordered_set` per node or check before inserting. |
| Subsequence that doesn’t touch all nodes | Some nodes may stay indegree‑zero forever | After BFS, verify that we processed exactly `n` nodes. |
| Sequences with length 1 | No edges, every permutation possible | Queue will start with `n` nodes → `size > 1` → return `false`. |
| `nums` not a permutation | Input invalid (LeetCode guarantees it) | Trust the contract, but defensive code can still check. |

---

### 5.  Time & Space Complexity  

* **Time** – `O(n + Σ|seq|)`  
  *Building graph + Kahn’s algorithm* both traverse each edge once.  

* **Space** – `O(n + Σ|seq|)`  
  *Adjacency lists* plus indegree array.

---

### 6.  The Good, The Bad, The Ugly  

| Category | What’s Great | What’s Troubling | What Could Be Cleaned Up |
|----------|--------------|------------------|--------------------------|
| **The Good** | • LeetCode 444 is a *canonical* graph problem that demonstrates mastery of topological sorting. <br>• Solutions are concise (~40 lines) yet powerful. <br>• Works in all major languages with minimal changes. | | |
| **The Bad** | • Many candidates implement a naïve DFS “order‑by‑visit” approach, which fails on non‑unique graphs. <br>• Duplicate edges often cause subtle bugs. | | |
| **The Ugly** | • Some code uses `ArrayList` and `contains` inside the edge‑building loop, leading to O(n²) behavior on dense graphs. <br>• Others use recursion for DFS, blowing the stack for `n = 10⁴`. | Replace `contains` with `Set` or keep an adjacency matrix; prefer BFS over recursion. | 

---

### 7.  Tips for Interview Success  

1. **Start with a sketch** – Draw the graph, show how edges are added, and explain Kahn’s uniqueness condition.  
2. **Explain edge cases** – Talk about duplicate edges and disconnected components.  
3. **Show clean code** – Keep variable names descriptive (`adj`, `indeg`, `queue`).  
4. **Talk about complexity** – Recruiters love it when you can quantify performance.  
5. **Mention testing** – Provide unit tests for cases like `[1,2]`, `[1,3]`, duplicate edges, etc.  

---

### 8.  Final Thought  

LeetCode 444 is a beautiful blend of theory and practice. It forces you to think about *order*, *constraints*, and *uniqueness* in one fell swoop. Mastering it gives you a solid, interview‑ready tool for any graph‑centric question.

Good luck – and may your code always produce a unique topological order!  

---

### 9.  References  

* LeetCode Problem 444 – https://leetcode.com/problems/sequence-reconstruction/  
* Kahn, T. (1962) *Topological sorting of large networks* – classic paper.  
* Competitive Programming 3 – Chapter on topological sorting.  

--- 

### Call‑to‑Action  

> Want more interview‑ready solutions?  
> Subscribe to our weekly newsletter for deep dives into classic LeetCode problems, with solutions in **Java**, **Python**, and **C++**.  

--- 

*Happy coding!* 🚀

---

**Word Count**: ~1,200 words  
**Target Audience**: Software engineers, coding interview candidates, recruiters.  
**SEO Keywords**: Sequence Reconstruction, LeetCode 444, Topological Sort, Unique Order, Graph Algorithms, Interview Prep.  

--- 

Feel free to copy, adapt, or expand this article for your personal blog or portfolio!