---
title: LeetCode 1494. Parallel Courses II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Parallel Courses II (Leetcode 1494) – From the Interview to the Job

> **TL;DR**  
> *Parallel Courses II* is a hard DP‑bitmask problem that tests your ability to think in states, generate subsets efficiently, and prune the search space.  
> The key to nailing the interview is to understand **state representation**, **transition logic**, and **pruning tricks**.  
> The following article explains the problem, shows working solutions in **Java**, **Python**, and **C++**, and discusses the *good, the bad, and the ugly* of the DP‑bitmask approach.  
> Bonus: SEO‑optimized copy that will make your blog or portfolio rank on Google for the exact interview keywords!

---

### 1. Problem Statement (Leetcode 1494)

```
There are n courses labeled from 1 to n.  
relations[i] = [a, b] means course a must be taken before course b.

Each semester you can take at most k courses,
but only if all of their prerequisites have already been taken.

Return the minimum number of semesters required to finish all courses.
The test cases are guaranteed to be solvable (the graph is a DAG).
```

| 1 ≤ n ≤ 15 | 1 ≤ k ≤ n | 0 ≤ relations.length ≤ n(n‑1)/2 |

---

### 2. Intuition – Why Bitmask DP?

- **State** = “set of courses already completed”.  
  With *n* ≤ 15, a 16‑bit integer can encode all subsets → **2ⁿ states**.
- In every semester we pick a subset of *available* courses (indegree = 0) with size ≤ k.
- Recursively compute the answer for the new state.
- Memoize to avoid recomputing the same subset.

This is the classic *DP over subsets* pattern that appears in problems like “Course Schedule III”, “Minimum Number of Taps”, etc.

---

### 3. Algorithm Overview

1. **Build adjacency list** and indegree array.  
2. **DFS + memoization** on the bitmask state:
   * If all courses taken → return 0.
   * Identify *available* courses: indegree = 0 and not in mask.
   * Enumerate all subsets of available courses whose size ≤ k.  
     For each subset:
        * Mark courses as taken → newMask.  
        * Update indegrees for children → newIndegree.  
        * Recurse: `1 + dfs(newMask, newIndegree)`.  
   * Take the minimum over all subsets.
3. Return the result of the initial call.

#### Optimisations

| Problem | Solution |
|---------|----------|
| Enumerating **k**‑subsets naively (O(2^m)) | Pre‑generate all combinations of size 1…k for each `m` (≤ n) or use bit tricks (`__builtin_popcount` in C++). |
| Re‑allocating indegree array each recursion | Pass it by reference and revert changes after recursion. (For clarity we copy – still fast for n ≤ 15.) |
| Duplicate work when many courses are available | If `m <= k` just take all available courses in one go (no branching). |

---

### 4. Correctness Proof Sketch

We prove by induction on the number of remaining courses that the algorithm returns the optimal number of semesters.

*Base*: When the mask equals `(1<<n)-1`, all courses are taken. The algorithm returns 0, which is optimal.

*Induction Step*: Assume for any mask with *t* remaining courses the algorithm returns the optimum. Consider a state with *t+1* remaining courses.  
Let `S` be the set of available courses at this state. Any optimal schedule must choose some subset `X ⊆ S` with |X| ≤ k to take in the next semester, because we cannot take a course whose prerequisites are incomplete.  

The algorithm enumerates **every** such `X`.  
For each `X`, it computes `1 + optimal(S', X)` where `S'` is the new mask.  
By the induction hypothesis, `optimal(S', X)` is the optimal number of semesters for the remaining courses after taking `X`.  
Hence the algorithm considers all possible optimal first steps and returns the minimum, which equals the optimal value for the current state. ∎

---

### 5. Complexity Analysis

- **States**: `2ⁿ` (≤ 32 768 for n = 15).  
- **Per state**: enumerate subsets of available courses (`C(m,1)+…+C(m,k)`), worst‑case `C(15,7) ≈ 6435`.  
- **Time**: `O(2ⁿ * C(n, k))` → ≈ 10⁶ operations, easily under 1 s.  
- **Space**: `O(2ⁿ)` for memoization + `O(n)` for adjacency + indegree arrays.  
  ≤ ~ 100 kB.

---

### 6. Code

> **All three implementations use the same logic but differ only in syntax.**  
> Each file is self‑contained and can be copied into the corresponding Leetcode editor.

#### 6.1 Java (Leetcode style)

```java
import java.util.*;

class Solution {
    private List<Integer>[] graph;
    private int[] indegree;
    private int[] memo;
    private int n, k;
    private int FULL_MASK;

    public int minNumberOfSemesters(int n, int[][] relations, int k) {
        this.n = n; this.k = k;
        this.FULL_MASK = (1 << n) - 1;
        graph = new List[n];
        for (int i = 0; i < n; ++i) graph[i] = new ArrayList<>();
        indegree = new int[n];
        for (int[] rel : relations) {
            int u = rel[0] - 1, v = rel[1] - 1;
            graph[u].add(v);
            indegree[v]++;
        }
        memo = new int[1 << n];
        Arrays.fill(memo, -1);
        return dfs(0, indegree);
    }

    private int dfs(int mask, int[] curIndegree) {
        if (mask == FULL_MASK) return 0;
        if (memo[mask] != -1) return memo[mask];

        // Find available courses
        List<Integer> avail = new ArrayList<>();
        for (int i = 0; i < n; ++i)
            if (((mask >> i) & 1) == 0 && curIndegree[i] == 0)
                avail.add(i);

        int m = avail.size();
        if (m <= k) {          // take all at once
            int newMask = mask;
            int[] nextInd = curIndegree.clone();
            for (int v : avail) {
                newMask |= 1 << v;
                for (int child : graph[v]) nextInd[child]--;
            }
            return memo[mask] = 1 + dfs(newMask, nextInd);
        }

        // Enumerate all subsets of size 1..k
        int best = Integer.MAX_VALUE;
        for (int sub = 1; sub < (1 << m); ++sub) {
            if (Integer.bitCount(sub) > k) continue;
            int newMask = mask;
            int[] nextInd = curIndegree.clone();
            for (int idx = 0; idx < m; ++idx)
                if ((sub >> idx) & 1) {
                    int v = avail.get(idx);
                    newMask |= 1 << v;
                    for (int child : graph[v]) nextInd[child]--;
                }
            best = Math.min(best, 1 + dfs(newMask, nextInd));
        }
        return memo[mask] = best;
    }
}
```

> *Why this is a “good” Java solution*  
> - Clear state‑to‑state memoization (`int[] memo`).  
> - Uses `clone()` for brevity (n ≤ 15 → negligible cost).  
> - Handles the `m <= k` shortcut which removes a huge number of branches.

---

#### 6.2 Python (functional style, `functools.lru_cache`)

```python
from functools import lru_cache
from collections import defaultdict, deque
from typing import List

class Solution:
    def minNumberOfSemesters(self, n: int, relations: List[List[int]], k: int) -> int:
        graph = [[] for _ in range(n)]
        indeg = [0]*n
        for a, b in relations:
            graph[a-1].append(b-1)
            indeg[b-1] += 1

        FULL = (1 << n) - 1

        @lru_cache(None)
        def dfs(mask, indeg_tuple):
            if mask == FULL:
                return 0
            indeg = list(indeg_tuple)

            # available courses
            avail = [i for i in range(n)
                     if not (mask >> i) & 1 and indeg[i] == 0]
            m = len(avail)

            if m <= k:                 # take all
                new_mask = mask
                for v in avail:
                    new_mask |= 1 << v
                    for child in graph[v]:
                        indeg[child] -= 1
                return 1 + dfs(new_mask, tuple(indeg))

            best = float('inf')
            # enumerate subsets of size 1..k
            for sub in range(1, 1 << m):
                if sub.bit_count() > k:
                    continue
                new_mask = mask
                new_indeg = indeg[:]
                for i in range(m):
                    if sub >> i & 1:
                        v = avail[i]
                        new_mask |= 1 << v
                        for child in graph[v]:
                            new_indeg[child] -= 1
                best = min(best, 1 + dfs(new_mask, tuple(new_indeg)))
            return best

        return dfs(0, tuple(indeg))
```

> *Why this is a “bad” Python solution?*  
> Python's `clone()` and list copying are *slower* but still fine for *n* = 15.  
> For production code, you would revert indegrees instead of copying.

---

#### 6.3 C++ (Leetcode style)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minNumberOfSemesters(int n, vector<vector<int>>& relations, int k) {
        vector<vector<int>> g(n);
        vector<int> indeg(n, 0);
        for (auto &r : relations) {
            int u = r[0]-1, v = r[1]-1;
            g[u].push_back(v);
            indeg[v]++;
        }

        const int FULL = (1<<n)-1;
        vector<int> dp(1<<n, -1);

        function<int(int, vector<int>&)> dfs = [&](int mask, vector<int>& curIndeg) {
            if (mask == FULL) return 0;
            if (dp[mask] != -1) return dp[mask];

            vector<int> avail;
            for (int i=0;i<n;++i)
                if (!(mask>>i&1) && curIndeg[i]==0) avail.push_back(i);

            int m = avail.size();
            if (m <= k) {           // take all
                int newMask = mask;
                for (int v: avail) {
                    newMask |= 1<<v;
                    for (int child: g[v]) curIndeg[child]--;
                }
                return dp[mask] = 1 + dfs(newMask, curIndeg);
            }

            int best = INT_MAX;
            // Enumerate all subsets of avail whose size <= k
            for (int sub = 1; sub < (1<<m); ++sub) {
                if (__builtin_popcount(sub) > k) continue;
                int newMask = mask;
                // temporarily modify indegree
                vector<int> nextIndeg = curIndeg;
                for (int i=0;i<m;++i)
                    if (sub>>i & 1) {
                        int v = avail[i];
                        newMask |= 1<<v;
                        for (int child: g[v]) nextIndeg[child]--;
                    }
                best = min(best, 1 + dfs(newMask, nextIndeg));
            }
            return dp[mask] = best;
        };

        return dfs(0, indeg);
    }
};
```

> *Why this is a “good” C++ implementation?*  
> - Uses `__builtin_popcount` for fast subset size checks.  
> - Copies indegree only for the recursive call; copying is cheap for `n ≤ 15`.  
> - Clear separation of *state*, *transition*, and *memoization*.

---

### 7. Good, Bad & Ugly – DP‑Bitmask in Interviews

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **State size** (2ⁿ) | **Small** (`n ≤ 15`). | **Exponential** – careful not to over‑extend to `n = 30`. | **Unpredictable** for larger n. |
| **Branching factor** (subset enumeration) | Can be pruned by `m <= k` shortcut. | **O(2^m)** if you naively try every subset → exponential blow‑up. | **Backtracking without pruning** leads to TLE. |
| **Memoisation** | Clean `int[] memo` → O(2ⁿ). | Forgetting to store results → repeated work. | Using a *map* instead of an array leads to *memory overhead*. |
| **Indegree updates** | Clone array or revert changes → O(n) per recursion. | Updating globally in place can produce *inconsistent states*. | Too many copies can become a *hidden constant factor* slowdown. |
| **Complexity understanding** | Clear O(2ⁿ C(n,k)) analysis → interview‑friendly. | Mis‑stating the complexity can make the interviewer suspicious. | Over‑optimising (e.g., bitset tricks) can distract from the core idea. |

> **Takeaway:**  
> *Explain your pruning and combination generation before diving into code.*  
> Interviewers love candidates who show the *why* before the *what*.

---

### 8. Alternatives & Why We Picked This One

| Alternative | Description | Pros | Cons |
|-------------|-------------|------|------|
| **Topological DP + BFS** | Treat each semester as a level, use BFS to explore level‑by‑level states. | Simpler to understand. | Still needs subset enumeration → same O(2ⁿ C(n,k)). |
| **Heuristic / Greedy** (e.g., always take the largest available set) | Quick but *not optimal*. | Fast, but wrong. | Leetcode requires optimal solution. |
| **Bitset + DP** (iterative) | Use bitset to iterate over all states in increasing mask order. | Avoid recursion overhead. | Harder to manage indegree updates per state. |
| **Mixed DP + BFS** | DP on mask but BFS to handle indegree updates lazily. | Potentially less memory. | More complex code. |

> *Why the DP‑bitmask solution is the “standard” interview answer:*  
> It maps cleanly onto the interview question “states, transitions, memoisation” that many interviewers ask for.  
> It shows you can reason about subsets, handle combinatorial explosion, and still keep the code within the time limits.

---

### 9. Interview‑Ready Checklist

1. **Read the constraints carefully** → *n* is small, so exponential states are fine.
2. **Explain the state** (mask) and why it fits 15 courses.
3. **Show the transition**: identify available courses, enumerate ≤ k subsets.
4. **Discuss pruning**: take all when `m <= k`, use combination pre‑generation.
5. **State the complexity** in words (O(2ⁿ C(n,k))) and in numbers (≈ 1 M ops).
6. **Write the code** (preferably iterative for C++/Java, recursive with memo in Python).
7. **Ask questions**: “What if k were 1?” “Can we parallelise?” → demonstrates curiosity.
8. **Highlight edge cases**: empty `relations`, all courses independent, etc.
8. **Mention testing**: test on the provided examples, maybe a random test harness.

---

### 10. Closing Thoughts

- The **DP‑bitmask** approach is *clean, optimal, and fits perfectly within the given limits*.  
- **Implementation details** (combination enumeration, `m <= k` shortcut) are essential for passing the 1‑second limit.  
- **Communicating the idea** is more important than delivering a perfect code snippet; interviewers look for *thought processes*.

---

### 10‑Second Explanation (for a quick recap)

> *“We use a bitmask to represent which courses are finished. For each mask, we find all courses that can be taken (no unmet prerequisites). If the number of such courses is ≤ k, we take them all (no branching). Otherwise, we try every subset of those courses of size up to k, update the mask and prerequisites, and recurse. We memoise the minimal semesters for each mask. Complexity: O(2ⁿ C(n,k)), with n = 15 → about a million operations.”*

---

### 10. Final Words

- **Keep the code readable** – interviewers prefer clean solutions over “too clever” tricks.  
- **Explain your reasoning** – it builds trust.  
- **Practice**: run through a couple of random test cases on your machine to verify the runtime.  
- **Deploy**: once confident, push the code to Leetcode and submit.

Good luck, and happy coding! 🚀

---  

### 11. SEO & Keywords (for your blog)

- “Minimum number of semesters coding interview solution”  
- “DP bitmask courses scheduling problem”  
- “Leetcode 1850 minimum number of semesters solution”  
- “Topological sort with DP subsets”  
- “Python lru_cache DP mask”  
- “C++ __builtin_popcount subset enumeration”  
- “Interview explanation DP states transitions memoisation”  

> *These keywords help your article rank for both Leetcode users and interview candidates.*

--- 

*Happy coding, and best of luck on your next interview!* 🎉
