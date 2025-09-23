---
title: LeetCode 444. Sequence Reconstruction - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Sequence Reconstruction – Leetcode 444  
**A Top‑Down Guide to the “Good, the Bad & the Ugly” of Topological Sorting**

---

### TL;DR

| Language | Time | Space | Link |
|---------|------|-------|------|
| **Java** | O(n + Σlen) | O(n + Σlen) | [Java Solution](#java-solution) |
| **Python** | O(n + Σlen) | O(n + Σlen) | [Python Solution](#python-solution) |
| **C++** | O(n + Σlen) | O(n + Σlen) | [C++ Solution](#cpp-solution) |

If you’re prepping for a software‑engineering interview, this problem is a perfect showcase of:

* **Topological Sorting** (Kahn’s algorithm)
* **Uniqueness Checking** of a DAG ordering
* **Permutation handling** in O(n)

> **Quick Win** – Return `true` iff the *only* topological order of the graph equals `nums`.

---

## 1. Problem Breakdown

We are given:
* `nums`: a permutation of `[1 … n]`
* `sequences`: a list of subsequences of `nums`

We must decide whether **`nums` is the unique shortest supersequence** that satisfies all subsequences in `sequences`.

### Why “Shortest” matters

The *shortest* supersequence is precisely the *topological order* of the partial order induced by all pairs `(a → b)` where `a` appears before `b` in some `sequence`.  
If the DAG has multiple topological sorts, we have more than one shortest supersequence – `nums` is *not* unique.

---

## 2. Core Idea – Graph + Kahn’s Algorithm

1. **Build the graph**  
   For every `sequence`:
   ```
   for i in 0 … len-2:
       u = sequence[i]
       v = sequence[i+1]
       add edge u → v
   ```

2. **Compute indegrees**  
   Count how many incoming edges each node has.

3. **Topological sort with uniqueness check**  
   * Use a queue (or priority queue) of nodes with indegree 0.  
   * If at any step the queue size > 1 → multiple choices → the order isn’t unique → return `false`.  
   * Pop the node, decrement indegrees of its neighbors, push new indegree‑0 nodes.

4. **Validate against `nums`**  
   After sorting, if the generated order is exactly `nums`, return `true`; otherwise `false`.

---

## 3. Edge‑Case Checklist

| Edge Case | Why it matters | What to do |
|-----------|----------------|------------|
| `sequences` empty | No constraints → any order is valid | Return `false` (since `nums` not unique) |
| `sequences[i]` length 1 | No edges contributed | Skip when building edges |
| Duplicate edges | Increment indegree only once | Use adjacency set or allow duplicates – they don’t affect correctness |
| Nodes with no incoming or outgoing edges | Still part of the order | Must be in the graph and handled by indegree 0 queue |
| `nums` not a permutation of `[1…n]` | Invalid input (Leetcode guarantees otherwise) | No action needed |

---

## 4. Implementation

### 4.1 Java Solution

```java
// Java 17
import java.util.*;

public class Solution {
    public boolean sequenceReconstruction(int[] nums, List<List<Integer>> sequences) {
        int n = nums.length;
        // adjacency list & indegree array
        List<Set<Integer>> adj = new ArrayList<>(n + 1);
        int[] indeg = new int[n + 1];
        for (int i = 0; i <= n; i++) adj.add(new HashSet<>());

        // Build graph
        for (List<Integer> seq : sequences) {
            for (int i = 0; i < seq.size() - 1; i++) {
                int u = seq.get(i);
                int v = seq.get(i + 1);
                if (!adj.get(u).contains(v)) {   // avoid duplicate edges
                    adj.get(u).add(v);
                    indeg[v]++;
                }
            }
        }

        // Kahn's algorithm with uniqueness check
        Queue<Integer> q = new ArrayDeque<>();
        for (int i = 1; i <= n; i++) {
            if (indeg[i] == 0) q.offer(i);
        }

        int idx = 0;
        while (!q.isEmpty()) {
            if (q.size() > 1) return false;   // more than one choice -> not unique
            int node = q.poll();
            if (node != nums[idx]) return false; // order mismatch
            idx++;

            for (int nei : adj.get(node)) {
                if (--indeg[nei] == 0) q.offer(nei);
            }
        }
        return idx == n; // all nodes processed
    }
}
```

> **Why a `Set`?**  
> Prevents counting duplicate edges – they would otherwise inflate indegrees and break the algorithm.

---

### 4.2 Python Solution

```python
# Python 3.10
from collections import defaultdict, deque
from typing import List

class Solution:
    def sequenceReconstruction(self, nums: List[int], sequences: List[List[int]]) -> bool:
        n = len(nums)
        # adjacency list & indegree
        adj = defaultdict(set)
        indeg = [0] * (n + 1)

        for seq in sequences:
            for i in range(len(seq) - 1):
                u, v = seq[i], seq[i + 1]
                if v not in adj[u]:       # avoid duplicates
                    adj[u].add(v)
                    indeg[v] += 1

        q = deque([i for i in range(1, n + 1) if indeg[i] == 0])
        idx = 0

        while q:
            if len(q) > 1:   # multiple choices -> not unique
                return False
            node = q.popleft()
            if node != nums[idx]:
                return False
            idx += 1
            for nei in adj[node]:
                indeg[nei] -= 1
                if indeg[nei] == 0:
                    q.append(nei)

        return idx == n
```

> **Why a `defaultdict(set)`?**  
> Ensures we don’t count the same edge twice, keeping indegree counts correct.

---

### 4.3 C++ Solution

```cpp
// C++17
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool sequenceReconstruction(vector<int>& nums, vector<vector<int>>& sequences) {
        int n = nums.size();
        vector<unordered_set<int>> adj(n + 1);
        vector<int> indeg(n + 1, 0);

        // Build graph
        for (auto& seq : sequences) {
            for (int i = 0; i + 1 < seq.size(); ++i) {
                int u = seq[i], v = seq[i + 1];
                if (!adj[u].count(v)) {
                    adj[u].insert(v);
                    ++indeg[v];
                }
            }
        }

        queue<int> q;
        for (int i = 1; i <= n; ++i)
            if (indeg[i] == 0) q.push(i);

        int idx = 0;
        while (!q.empty()) {
            if (q.size() > 1) return false;  // not unique
            int node = q.front(); q.pop();
            if (node != nums[idx++]) return false;
            for (int nei : adj[node]) {
                if (--indeg[nei] == 0) q.push(nei);
            }
        }
        return idx == n;
    }
};
```

> **Why `unordered_set`?**  
> Keeps edge deduplication O(1) on average.

---

## 5. Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | O(n + Σlen) | O(n + Σlen) | O(n + Σlen) |
| **Space** | O(n + Σlen) | O(n + Σlen) | O(n + Σlen) |

* `n` – number of nodes (≤ 10⁴)  
* `Σlen` – total length of all sequences (≤ 10⁵)

---

## 6. The Good, the Bad, and the Ugly

| Aspect | The Good | The Bad | The Ugly |
|--------|----------|---------|----------|
| **Problem clarity** | Well‑defined constraints & examples | Some may misinterpret “shortest” | Misreading “unique supersequence” → returning `false` for valid cases |
| **Graph construction** | Simple edge‑building | Need to avoid duplicate edges | Forgetting duplicate handling inflates indegree and gives wrong answer |
| **Topological sort** | Kahn’s algorithm is clean | Must check uniqueness → a subtle extra condition | Not checking queue size leads to “false positives” |
| **Edge cases** | All handled by the algorithm | Rare cases like single‑element sequences | Empty `sequences` or isolated nodes – overlooked |
| **Performance** | Linear & fast | Python slower but still fine | C++ fastest but boilerplate heavy |
| **Interview value** | Demonstrates graph + algorithmic thinking | Shows attention to detail | Exposes deep understanding of DAG properties |

---

## 7. Take‑Home Messages

1. **Topological Sorting** is the core of this problem.  
2. **Uniqueness** is detected by ensuring *exactly one* zero‑indegree node at every step.  
3. **Validate the order** against the given permutation to confirm the *shortest* condition.  
4. **Avoid duplicate edges**; they inflate indegree counts and corrupt the algorithm.  
5. A clean implementation in any language will impress interviewers:  
   * Java: `Set` + `ArrayDeque`  
   * Python: `defaultdict(set)` + `deque`  
   * C++: `unordered_set` + `queue`

---

## 8. FAQ

| Q | A |
|---|---|
| Can I use DFS for uniqueness? | Yes, but DFS must check if a node has multiple children with zero indegree → Kahn’s algorithm is clearer. |
| What if `nums` contains numbers outside `[1…n]`? | Leetcode guarantees it’s a permutation; otherwise your solution would be invalid. |
| Is there a faster way? | Not asymptotically faster – linear time is optimal for this problem. |
| How to handle huge input in Python? | Use `sys.stdin.read().split()` for fast IO, but Leetcode handles it for you. |

---

## 9. SEO Checklist

* **Primary keyword**: “Sequence Reconstruction Leetcode 444”  
* **Secondary keywords**: “topological sort”, “Kahn’s algorithm”, “unique topological order”, “Java solution”, “Python solution”, “C++ solution”, “interview prep”, “coding interview”, “software engineering interview”  
* **Meta description** (≈155 chars):  
  “Master Leetcode 444 – Sequence Reconstruction. Learn topological sort, uniqueness check, and Java/Python/C++ solutions. Boost your coding interview prep!”
* **Headings**: H1 for the title, H2 for sections like “Problem Overview”, “Approach”, “Java Solution”, etc.  
* **Code snippets**: Use triple backticks for readability → keeps users on page.  
* **Internal links**: Link to related interview‑prep articles (e.g., “Top 10 Graph Problems”).
* **Alt text** for images (if added): “sequence reconstruction flow diagram”  

---

### Final Thought

Sequence Reconstruction is a textbook example of **graph theory in practice**. By delivering a concise, linear solution and understanding the subtlety of uniqueness, you’ll showcase algorithmic maturity—exactly what recruiters look for. Happy coding and good luck with your interviews!