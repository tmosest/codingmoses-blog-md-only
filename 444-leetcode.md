---
title: LeetCode 444. Sequence Reconstruction - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 444 – Sequence Reconstruction  
**A Topological‑Sort + Uniqueness Check that Works in Java, Python & C++**

> **Keywords**: LeetCode 444, Sequence Reconstruction, Topological Sort, Unique Topological Order, BFS/Kahn, DFS, Graph Theory, Coding Interview, Java, Python, C++.

---

## 1. Problem Recap

> **Given**  
> - `nums` – a permutation of `[1 … n]`  
> - `sequences` – a list of subsequences that appear in `nums`  

> **Return** `true` **iff** `nums` is *the only* shortest super‑sequence that contains every subsequence in `sequences`.

The challenge is to decide whether the ordering in `nums` is *uniquely determined* by the pairwise precedence constraints implied by `sequences`. If it is, the only valid shortest super‑sequence is exactly `nums`; otherwise there is at least one other valid ordering.

---

## 2. Why a Topological Sort?

Each subsequence `[a, b, c]` tells us that  
```
a → b → c
```
is a directed edge set in a graph of `n` nodes (the numbers).  
The problem asks whether **the graph has a unique topological ordering** that equals `nums`.  

If the graph’s topological order is unique, it must be the same as `nums`.  
If it isn’t unique, there exist at least two valid super‑sequences and `nums` can’t be the only shortest one.

---

## 3. Algorithm Overview

1. **Build the graph & indegree array**  
   For every pair of consecutive elements in each subsequence, add a directed edge and update indegrees.

2. **Kahn’s BFS topological sort with uniqueness check**  
   * Maintain a queue of nodes with indegree 0.  
   * At each step, if the queue has **more than one** node, the ordering is *not* unique → return `false`.  
   * Pop the single node, append it to `order`, and decrease indegrees of its neighbors, enqueueing any that drop to 0.

3. **Validate**  
   * If the final `order` length ≠ `n`, the graph was incomplete (some nodes never appear in `sequences`), return `false`.  
   * Compare `order` with `nums`. If they match, `true`; otherwise `false`.

---

## 4. Correctness Proof

We prove that the algorithm returns `true` **iff** `nums` is the only shortest super‑sequence.

### Lemma 1  
If the algorithm returns `false` because the queue ever contains ≥ 2 nodes, then the partial order defined by `sequences` admits more than one topological ordering.

*Proof.*  
At that step, two distinct nodes `x` and `y` have indegree 0, meaning no precedence constraints dictate their relative order. Therefore both `[ …, x, y, … ]` and `[ …, y, x, … ]` are valid extensions, yielding two different super‑sequences. ∎

### Lemma 2  
If the algorithm finishes with a unique order `order`, then `order` is the *only* topological ordering of the graph.

*Proof.*  
Kahn’s algorithm explores all edges. The uniqueness check ensures that at each step only one node is eligible, so the relative order of any two nodes is fixed by earlier decisions. Thus no alternative ordering exists. ∎

### Lemma 3  
If the unique topological order equals `nums`, then `nums` is the only shortest super‑sequence.

*Proof.*  
Any super‑sequence must respect the edges. A topological ordering is a shortest super‑sequence of the graph because it contains every vertex exactly once. Since there is only one such ordering and it equals `nums`, no other shortest super‑sequence can exist. ∎

### Lemma 4  
If `nums` is the only shortest super‑sequence, then the algorithm will return `true`.

*Proof.*  
Assume the contrary: algorithm returns `false`.  
- Either queue had ≥ 2 nodes ⇒ by Lemma 1 more than one super‑sequence exists ⇒ contradiction.  
- Or final order length ≠ n ⇒ some vertex never appears in `sequences`; then we could insert that vertex anywhere without breaking subsequences, giving another super‑sequence ⇒ contradiction.  
Thus algorithm must return `true`. ∎

Combining Lemmas 2–4 gives the *if and only if* statement. ∎

---

## 5. Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Build graph (all edges) | **O(∑|sequences[i]|)** ≤ 10⁵ | **O(n + E)** |
| Kahn’s BFS | **O(n + E)** | **O(n + E)** |
| Total | **O(n + E)** | **O(n + E)** |

With `n ≤ 10⁴` and `∑|sequences[i]| ≤ 10⁵`, the solution comfortably fits within limits.

---

## 6. Edge‑Case Handling

| Case | Why it matters | Code guard |
|------|----------------|------------|
| `nums` not permutation | Problem guarantees, but guard avoids crashes | None |
| A number never appears in any subsequence | Graph node has indegree 0 and outdegree 0 → algorithm returns `false` | `if (order.size() != n) return false;` |
| Empty `sequences` | No edges → any ordering valid → algorithm returns `false` | Same guard above |
| Duplicate edges | We use a `Set` per node or check before adding | `if (adj[u].add(v)) indeg[v]++;` |

---

## 7. Implementation

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.  
All three use the same logic and run in the same asymptotic time.

---

### 7.1 Java

```java
import java.util.*;

public class Solution {
    public boolean sequenceReconstruction(int[] nums, List<List<Integer>> sequences) {
        int n = nums.length;
        List<Set<Integer>> adj = new ArrayList<>(n + 1);
        int[] indeg = new int[n + 1];
        for (int i = 0; i <= n; i++) adj.add(new HashSet<>());

        // Build graph
        for (List<Integer> seq : sequences) {
            for (int i = 0; i + 1 < seq.size(); i++) {
                int u = seq.get(i);
                int v = seq.get(i + 1);
                if (adj.get(u).add(v)) indeg[v]++;
            }
        }

        // Kahn with uniqueness check
        Deque<Integer> q = new ArrayDeque<>();
        for (int i = 1; i <= n; i++) {
            if (indeg[i] == 0) q.offer(i);
        }

        int idx = 0;
        while (!q.isEmpty()) {
            if (q.size() > 1) return false;   // multiple choices -> not unique
            int cur = q.poll();
            if (cur != nums[idx]) return false; // order mismatch
            idx++;
            for (int nei : adj.get(cur)) {
                if (--indeg[nei] == 0) q.offer(nei);
            }
        }
        return idx == n; // ensure all nodes processed
    }
}
```

---

### 7.2 Python

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

        # Kahn with uniqueness check
        q = deque([i for i in range(1, n + 1) if indeg[i] == 0])
        idx = 0

        while q:
            if len(q) > 1:
                return False                      # non‑unique ordering
            cur = q.popleft()
            if cur != nums[idx]:
                return False                      # order mismatch
            idx += 1
            for nei in adj[cur]:
                indeg[nei] -= 1
                if indeg[nei] == 0:
                    q.append(nei)

        return idx == n
```

---

### 7.3 C++

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
            for (size_t i = 0; i + 1 < seq.size(); ++i) {
                int u = seq[i], v = seq[i + 1];
                if (adj[u].insert(v).second)   // inserted successfully
                    ++indeg[v];
            }
        }

        // Kahn with uniqueness check
        queue<int> q;
        for (int i = 1; i <= n; ++i)
            if (indeg[i] == 0) q.push(i);

        int idx = 0;
        while (!q.empty()) {
            if (q.size() > 1) return false;          // multiple candidates
            int cur = q.front(); q.pop();
            if (cur != nums[idx]) return false;      // order mismatch
            ++idx;
            for (int nei : adj[cur]) {
                if (--indeg[nei] == 0) q.push(nei);
            }
        }
        return idx == n;
    }
};
```

---

## 8. Good, Bad, & Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time** | Linear in `n + E` | None | None |
| **Space** | `O(n + E)` | None | None |
| **Readability** | Clear separation of graph build & Kahn | Slight verbosity in Java (sets, arrays) | None |
| **Edge‑Case Handling** | Explicit guard for `order.size() != n` | None | None |
| **Common Pitfall** | Forgetting to compare with `nums` during BFS | Overlooking the uniqueness check (`q.size() > 1`) | Using DFS without cycle detection → false positives |

---

## 9. Why This Matters in Interviews

- **Graph fundamentals**: Understanding directed edges, indegree, topological sort.
- **Uniqueness check**: A subtle twist beyond a plain topological sort.
- **Complexity**: Real‑world data may reach 10⁵ edges; you must stay linear.
- **Coding skills**: Clean Java/Python/C++ implementations show mastery of language features (sets, deques, unordered_set).

Show this solution on your portfolio or during a technical interview to demonstrate both algorithmic insight and practical coding skill.

---

## 10. SEO‑Optimized Wrap‑Up

If you’re prepping for a coding interview, mastering **LeetCode 444 – Sequence Reconstruction** gives you a **unique advantage**.  
- **Java** lovers: see a clean `Set`‑based adjacency list.  
- **Python** pros: a one‑liner `deque` plus `defaultdict`.  
- **C++** gurus: `unordered_set` and `queue` make the logic crisp.

Remember the **two‑step** process:  
1. **Build a directed graph from subsequences**.  
2. **Apply Kahn’s algorithm with a uniqueness guard**.

Your final answer will be fast, memory‑efficient, and robust against all edge cases.  
Add this to your résumé, GitHub repo, or interview notes—*sequence* your way to success!

Happy coding! 🚀

--- 

*End of solution.*