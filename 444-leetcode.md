---
title: LeetCode 444. Sequence Reconstruction - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 444. **Sequence Reconstruction** – A Complete Interview‑Ready Guide  

> **LeetCode 444 – Sequence Reconstruction (Medium)**  
> **Keywords**: LeetCode, Sequence Reconstruction, Topological Sort, Graph Algorithms, Java, Python, C++, Interview Prep, Algorithmic Thinking, Job Interview  

---

### 1. Problem Summary

Given

| Input | Description |
|-------|-------------|
| `nums` | A permutation of `[1 … n]` (length `n`). |
| `sequences` | An array of subsequences of `nums`. Each `sequences[i]` is a list of integers that appears in the same order inside `nums`. |

**Goal**:  
Return `true` if `nums` is *the only* shortest supersequence that contains **every** subsequence in `sequences`; otherwise return `false`.

> **Examples**  
> *Input* `nums = [1,2,3]`, `sequences = [[1,2],[1,3]]` → **false**  
> *Input* `nums = [1,2,3]`, `sequences = [[1,2],[1,3],[2,3]]` → **true**

---

### 2. Why This Problem Rocks the Interview

- **Graph‑theory meets ordering** – It forces you to think of permutations as a directed acyclic graph (DAG).
- **Topological sort uniqueness** – Many candidates confuse *valid* ordering with *unique* ordering.  
- **Large constraints** – `n` and total length of all subsequences can reach `10⁵`, so a linear‑time solution is essential.
- **Real‑world relevance** – Topological ordering appears in build systems, task scheduling, and data‑pipeline orchestration.

---

### 3. High‑Level Insight

Each pair of consecutive elements in a subsequence gives a *directed edge* in the DAG:

```
1 → 2   (from subsequence [1,2])
```

If we can build the DAG and then perform **Kahn’s algorithm** (BFS topological sort) while ensuring that at every step only **one** node has indegree `0`, we guarantee:

1. The order produced is a valid topological order of the graph.
2. The order is *unique* – no alternative shortest supersequence exists.
3. The produced order must equal the given `nums`; otherwise `nums` is not the only shortest supersequence.

If we encounter a node with indegree `0` that is **not** the next element of `nums`, we know `nums` is not the unique shortest supersequence.

---

### 4. Algorithm in Detail

| Step | Action |
|------|--------|
| **1. Build adjacency & indegree** | For each subsequence `seq`: for every consecutive pair `(u, v)` add `v` to `adj[u]` and increment `indeg[v]`. |
| **2. Initialize queue** | Put all nodes with `indeg == 0` into a queue. |
| **3. Process nodes** | For index `i` from `0` to `n-1`:  
&nbsp;&nbsp;• If queue size > 1 → multiple choices → return `false`.  
&nbsp;&nbsp;• Dequeue `cur`. If `cur != nums[i]` → wrong ordering → return `false`.  
&nbsp;&nbsp;• Decrease indegree of all neighbors; enqueue those that become zero. |
| **4. Final check** | If we processed all `n` nodes → return `true`; otherwise a cycle or missing edge → return `false`. |

**Time Complexity**: `O(n + E)` where `E` is total edges (`<= sum of lengths of sequences` ≤ `10⁵`).  
**Space Complexity**: `O(n + E)` for adjacency list and indegree array.

---

### 5. Edge Cases & Pitfalls

| Common Mistake | Why it breaks | Fix |
|----------------|---------------|-----|
| Not handling repeated edges | `adj` may grow unnecessarily, wasting time | Use a `Set` or guard against adding duplicate edges (e.g., `if (!adj[u].contains(v))`). |
| Assuming the first element of `nums` has indegree 0 | Some inputs may start with an element that has incoming edges | Kahn’s algorithm naturally handles this; the queue will contain the correct starting nodes. |
| Failing to check `nums` alignment during BFS | You might return `true` even if `nums` is different | Explicitly compare `cur` to `nums[i]` at each step. |
| Ignoring isolated nodes | If a number never appears in any subsequence, it will have indegree 0 but still must match `nums` | Kahn’s algorithm covers this – isolated nodes will appear in the queue, and we still compare order. |

---

### 6. The “Good, the Bad, the Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Clarity** | Kahn’s algorithm is intuitive once you see the graph representation. | The problem statement can be confusing about “shortest” vs. “only”. | Misreading “shortest” as “any supersequence” leads to wrong implementations. |
| **Efficiency** | Linear time; works for `10⁵` elements. | Implementing DFS for uniqueness is possible but more complex. | Over‑optimizing with bitsets or fancy data structures adds unnecessary code. |
| **Robustness** | Handles all corner cases: isolated nodes, multiple valid orders, cycles. | Requires careful indegree updates to avoid off‑by‑one errors. | Forgetting to convert 1‑based labels to 0‑based indices in some languages causes subtle bugs. |

---

## 7. Code Snapshots

Below you’ll find clean, ready‑to‑paste solutions in **Java, Python, and C++**. All follow the same algorithmic idea and include ample comments.

---

### 7.1 Java (LeetCode Signature)

```java
import java.util.*;

public class Solution {
    public boolean sequenceReconstruction(int[] nums, List<List<Integer>> sequences) {
        int n = nums.length;
        // adjacency list
        List<List<Integer>> adj = new ArrayList<>(n + 1);
        for (int i = 0; i <= n; i++) adj.add(new ArrayList<>());
        // indegree array
        int[] indeg = new int[n + 1];
        // Build graph
        for (List<Integer> seq : sequences) {
            for (int i = 0; i + 1 < seq.size(); i++) {
                int u = seq.get(i);
                int v = seq.get(i + 1);
                // avoid duplicate edges
                if (!adj.get(u).contains(v)) {
                    adj.get(u).add(v);
                    indeg[v]++;
                }
            }
        }

        // Queue of nodes with indegree 0
        Deque<Integer> q = new ArrayDeque<>();
        for (int i = 1; i <= n; i++) {
            if (indeg[i] == 0) q.add(i);
        }

        // Process nodes one by one
        for (int i = 0; i < n; i++) {
            if (q.size() != 1) return false; // multiple choices -> not unique
            int cur = q.poll();
            if (cur != nums[i]) return false; // order mismatch
            for (int nxt : adj.get(cur)) {
                if (--indeg[nxt] == 0) q.add(nxt);
            }
        }
        return true;
    }
}
```

---

### 7.2 Python 3

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

        # Queue of nodes with indegree 0
        q = deque([i for i in range(1, n + 1) if indeg[i] == 0])

        for i, expected in enumerate(nums):
            if len(q) != 1:
                return False          # ambiguous ordering
            cur = q.popleft()
            if cur != expected:
                return False          # wrong sequence
            for nxt in adj[cur]:
                indeg[nxt] -= 1
                if indeg[nxt] == 0:
                    q.append(nxt)

        return True
```

---

### 7.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool sequenceReconstruction(vector<int>& nums, vector<vector<int>>& sequences) {
        int n = nums.size();
        vector<vector<int>> adj(n + 1);
        vector<int> indeg(n + 1, 0);

        // Build graph
        for (const auto& seq : sequences) {
            for (size_t i = 0; i + 1 < seq.size(); ++i) {
                int u = seq[i], v = seq[i + 1];
                if (find(adj[u].begin(), adj[u].end(), v) == adj[u].end()) {
                    adj[u].push_back(v);
                    ++indeg[v];
                }
            }
        }

        queue<int> q;
        for (int i = 1; i <= n; ++i)
            if (indeg[i] == 0) q.push(i);

        for (int i = 0; i < n; ++i) {
            if (q.size() != 1) return false;          // ambiguous
            int cur = q.front(); q.pop();
            if (cur != nums[i]) return false;          // order mismatch
            for (int nxt : adj[cur]) {
                if (--indeg[nxt] == 0) q.push(nxt);
            }
        }
        return true;
    }
};
```

---

## 8. SEO‑Optimized Blog Post

> **Title**: “Master LeetCode 444 – Sequence Reconstruction | Topological Sort + Interview Strategy”

> **Meta Description**: “Learn how to crack LeetCode 444 – Sequence Reconstruction. Dive into topological sort, graph construction, and the unique ordering check. Get ready for your next coding interview with Java, Python, and C++ solutions.”

### 8.1 Introduction

When preparing for a technical interview, you’ll quickly encounter problems that look like puzzles but are actually **graph theory** in disguise. One such classic is LeetCode’s *Sequence Reconstruction* (Problem 444). It asks you to verify whether a given permutation is the **unique** shortest supersequence that respects a set of subsequences. If you can solve this in O(n) time and explain the logic clearly, you’ll impress any hiring manager.

### 8.2 Why This Problem Is Interview Gold

- **Graph Insight**: Turns an abstract “order” problem into a directed acyclic graph (DAG).  
- **Topological Sort**: Familiar to many candidates, but uniqueness is the twist.  
- **Multiple Languages**: Solved in Java, Python, and C++—showcasing versatility.  
- **Edge Cases**: A good candidate anticipates isolated nodes, duplicate edges, and cycles.

### 8.3 Walkthrough of the Optimal Solution

Start by imagining each number in `nums` as a node. For every subsequence, draw edges from each element to its successor. If `seq = [1, 3, 2]`, you’ll add edges `1→3` and `3→2`. After building the graph, Kahn’s algorithm gives you a topological order. To guarantee uniqueness, the queue must never contain more than one node at any step. Moreover, you must match this order with the input `nums`.

Here’s a **step‑by‑step** diagram:

1. Build adjacency list & indegree → O(n)  
2. Initialize queue with nodes of indegree 0  
3. For each position in `nums`:  
   - If queue > 1 → return false  
   - If dequeued node ≠ `nums[i]` → return false  
   - Update neighbors  

If you finish the loop without hitting a conflict, the answer is *true*.

### 8.3 Common Pitfalls (and How to Avoid Them)

- Duplicate edges can inflate your adjacency list.  
- Forgetting to compare with `nums` during the BFS.  
- Misinterpreting “shortest” as any supersequence.  

Highlight these in your answer; it shows you’re thinking about correctness, not just performance.

### 8.4 Ready‑to‑Use Code in Your Favorite Language

Insert the Java, Python, and C++ snippets here (see section 7). Provide them with a quick comment block so recruiters can copy‑paste and run.

### 8.5 Interview Tips

1. **Explain Your Thought Process**: Start with “I’ll treat each consecutive pair in a subsequence as a directed edge.”  
2. **Talk About Uniqueness**: “If at any step the queue has >1 node, that means there’s more than one valid topological ordering.”  
3. **Validate Alignment**: “I’ll compare the dequeued node to the corresponding element in the given `nums` array.”  
4. **Stress Test**: Show how you’d manually test cases with isolated nodes or duplicate edges.

### 8.6 Takeaway

LeetCode 444 isn’t just a problem—it’s a showcase of how you can **translate problem constraints into a graph**, apply a well‑known algorithm, and extend it with an additional correctness check. Mastering this not only gives you a solid O(n) solution but also demonstrates your ability to think critically about algorithmic nuances.

### 8.7 Call‑to‑Action

Ready to impress? Try implementing the solution on your local machine in Java, Python, or C++ and then explain it to a friend or a mock interview platform. Once you’re comfortable, add the solution to your portfolio and mention it in your next coding interview.

> **End of Blog Post**

---

### 9. Final Words

LeetCode 444 may look like a “reorder” problem at first glance, but once you recognize the underlying DAG and the uniqueness requirement, it becomes a straight‑forward Kahn’s algorithm application. By covering the edge cases and explaining your reasoning, you’ll deliver a concise, efficient, and interview‑ready answer—whether you code in Java, Python, or C++. Happy coding and good luck with your next interview!