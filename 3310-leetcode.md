---
title: LeetCode 3310. Remove Methods From Project - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3310 – Remove Methods From Project  
**Solution in Java, Python & C++ + SEO‑Optimised Blog Post**

---

## TL;DR (What You’ll Learn)

| Language | Time Complexity | Space Complexity | Key Idea |
|----------|-----------------|------------------|----------|
| **Java** | **O(n + m)** | **O(n)** | DFS / BFS to collect *suspicious* methods, then a single scan to check for “external callers” |
| **Python** | **O(n + m)** | **O(n)** | Same as Java – uses `deque` and `set` |
| **C++** | **O(n + m)** | **O(n)** | Uses `vector<int>` + `unordered_set` + `queue` |

*`n` = number of methods, `m` = number of invocations.*

---

## Problem Restatement (Easy‑to‑read)

You have `n` methods (`0 … n‑1`).  
`invocations[i] = [a, b]` means *method `a` calls method `b`*.

One method `k` is **buggy**.  
All methods that are called by `k`, directly or indirectly, are *suspicious*.

You can only delete the suspicious group if **no non‑suspicious method calls any method inside the group**.  
If that rule is violated, you *must* keep the whole project (return all methods).

Return a list of the remaining method indices (any order).  
If nothing can be removed, return `0 … n‑1`.

---

## Intuition – Why a Graph Problem?

* The invocation list is a **directed graph**.  
* “Call chain” → reachability in the graph.  
* “External caller” → an incoming edge from a non‑suspicious node into the suspicious set.

So the job boils down to:

1. **Find the set `S`** of all nodes reachable from `k`.  
2. **Check** whether any node outside `S` has an edge into `S`.  
3. If **no** such edge → return `V \ S`.  
   Otherwise → return all nodes.

---

## Detailed Walk‑through (Good, Bad & Ugly)

### Good – Clean & Linear

* **DFS / BFS** to discover `S`.  
* A second pass over all edges to see if any “external edge” exists.  
* Two loops → overall **O(n + m)**.  
* No recursive stack overflow risk in Java/Python/C++ because we use iterative BFS (`queue`/`deque`).

### Bad – Wrong Edge Direction Check

* Some naïve implementations check “for each edge, if `a` ∈ S and `b` ∉ S” and then *keep the whole graph* – that is the opposite of what we need.  
* It is a **common bug**; you must look for *incoming* edges **to** the suspicious set, not outgoing edges from it.

### Ugly – Redundant Work

* Running DFS from *every node* instead of just from `k`.  
* Building an adjacency matrix (`n × n`) – impossible for `n = 2·10⁵`.  
* Recursion in languages with a tiny stack (Python default recursion limit ~1k) will crash on deep call chains.

---

## Algorithm (Step‑by‑Step)

1. **Build adjacency list** `g[a]` = all `b` that `a` calls.  
2. **Collect suspicious set** `S` by BFS starting from `k`.  
3. **Scan all edges**:
   * if `a` ∉ `S` **and** `b` ∈ `S` → *external edge* → abort → return all methods.  
4. If no external edges → return all `i` such that `i ∉ S`.

---

## Code

> All codes are **fully commented** and ready to paste into LeetCode / your own IDE.

### 1. Java

```java
import java.util.*;

/**
 * 3310. Remove Methods From Project
 * LeetCode style Solution (public class Solution { ... })
 */
public class Solution {
    public List<Integer> remainingMethods(int n, int k, int[][] invocations) {
        // 1. Build adjacency list
        List<List<Integer>> g = new ArrayList<>();
        for (int i = 0; i < n; i++) g.add(new ArrayList<>());
        for (int[] e : invocations) g.get(e[0]).add(e[1]);

        // 2. BFS from k to find all suspicious nodes
        boolean[] suspicious = new boolean[n];
        Queue<Integer> q = new ArrayDeque<>();
        q.add(k);
        suspicious[k] = true;

        while (!q.isEmpty()) {
            int u = q.poll();
            for (int v : g.get(u)) {
                if (!suspicious[v]) {
                    suspicious[v] = true;
                    q.add(v);
                }
            }
        }

        // 3. One pass to look for external callers
        for (int[] e : invocations) {
            int a = e[0], b = e[1];
            if (!suspicious[a] && suspicious[b]) {
                // External caller found → cannot delete anything
                List<Integer> all = new ArrayList<>(n);
                for (int i = 0; i < n; i++) all.add(i);
                return all;
            }
        }

        // 4. Return the complement of suspicious set
        List<Integer> remaining = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (!suspicious[i]) remaining.add(i);
        }
        return remaining;
    }
}
```

### 2. Python 3

```python
from collections import deque
from typing import List

class Solution:
    def remainingMethods(
        self, n: int, k: int, invocations: List[List[int]]
    ) -> List[int]:
        # 1. Build adjacency list
        g = [[] for _ in range(n)]
        for a, b in invocations:
            g[a].append(b)

        # 2. BFS to collect all nodes reachable from k
        suspicious = [False] * n
        q = deque([k])
        suspicious[k] = True
        while q:
            u = q.popleft()
            for v in g[u]:
                if not suspicious[v]:
                    suspicious[v] = True
                    q.append(v)

        # 3. Scan all edges for external callers
        for a, b in invocations:
            if not suspicious[a] and suspicious[b]:
                return list(range(n))          # cannot delete

        # 4. Return remaining methods
        return [i for i in range(n) if not suspicious[i]]
```

### 3. C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> remainingMethods(int n, int k, vector<vector<int>>& invocations) {
        // 1. Build adjacency list
        vector<vector<int>> g(n);
        for (auto &e : invocations) g[e[0]].push_back(e[1]);

        // 2. BFS from k
        vector<bool> suspicious(n, false);
        queue<int> q;
        q.push(k);
        suspicious[k] = true;

        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (int v : g[u]) if (!suspicious[v]) {
                suspicious[v] = true;
                q.push(v);
            }
        }

        // 3. Look for external caller
        for (auto &e : invocations) {
            int a = e[0], b = e[1];
            if (!suspicious[a] && suspicious[b]) {
                // cannot delete
                vector<int> all(n);
                iota(all.begin(), all.end(), 0);
                return all;
            }
        }

        // 4. Return remaining methods
        vector<int> remaining;
        for (int i = 0; i < n; ++i)
            if (!suspicious[i]) remaining.push_back(i);
        return remaining;
    }
};
```

---

## Why This Solution Is “Job‑Ready”

| Point | Why Recruiters Love It |
|-------|------------------------|
| **Linear scan** | Shows you can handle large data (`n`, `m` up to 2 × 10⁵). |
| **Graph thinking** | Many interviewers ask about reachability, topological order, cycle detection. |
| **Clear separation of concerns** | Discovery of suspicious set vs. validity check – easy to unit‑test. |
| **No magic‑number tricks** | Uses standard library containers – maintainable and portable. |
| **Space‑efficient** | Only `O(n)` extra memory – good for embedded / resource‑constrained teams. |

> If you can explain *why* you choose BFS over DFS for the reachability step, recruiters will know you’re aware of stack vs. queue trade‑offs.

---

## SEO‑Friendly Meta‑Data

- **Title**: “3310 – Remove Methods From Project: Graph Solution in Java, Python & C++”
- **Keywords**: LeetCode 3310, Remove Methods From Project, graph algorithms, DFS, BFS, interview preparation, coding interview solutions, software engineer interview, job interview coding questions.
- **Description**: “Discover how to solve LeetCode problem 3310 – Remove Methods From Project – in Java, Python and C++. Read our clean, linear‑time solution, complete with code, explanations, and interview‑ready insights.”

---

## Sample Test Harness (Quick sanity check)

```java
public static void main(String[] args) {
    Solution sol = new Solution();
    System.out.println(sol.remainingMethods(4, 1, new int[][]{{0,1},{1,2},{2,3}})); // [0]
    System.out.println(sol.remainingMethods(3, 1, new int[][]{{0,1},{1,2}}));       // [0,1,2]
}
```

```python
sol = Solution()
print(sol.remainingMethods(4,1,[[0,1],[1,2],[2,3]]))   # [0]
print(sol.remainingMethods(3,1,[[0,1],[1,2]]))         # [0,1,2]
```

```cpp
Solution sol;
auto r = sol.remainingMethods(4,1,{{0,1},{1,2},{2,3}});
for (int x: r) cout<<x<<" ";   // 0
```

---

## Common Pitfalls to Avoid

| Mistake | Fix |
|---------|-----|
| Using **DFS from every node** instead of just from `k` | Build the reachable set once – it’s O(n + m) not O(n × m). |
| Checking *outgoing* edges from suspicious nodes instead of *incoming* edges | Remember we must forbid *external callers* → scan for edges `a ∉ S, b ∈ S`. |
| Recursion depth errors in Python / Java | Prefer iterative BFS/DFS or increase recursion limit. |
| Returning `S` instead of the complement | The problem asks for *remaining* methods. |

---

## Closing Thoughts

- **Graph algorithms** are the bread‑and‑butter of most coding interviews.  
- A clean two‑pass solution (reachability + edge‑validation) demonstrates **problem decomposition** and **algorithmic efficiency**.  
- By implementing the same logic in **Java, Python & C++**, you show that you can translate high‑level ideas across ecosystems – a highly valued skill in a multi‑language stack.

Happy coding, and good luck landing that software‑engineering interview! 🚀