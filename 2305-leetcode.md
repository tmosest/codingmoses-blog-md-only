---
title: LeetCode 2305. Fair Distribution of Cookies - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # LeetCode 2305 – *Fair Distribution of Cookies*  
> **The Good, The Bad, and The Ugly – A Deep‑Dive Guide for Interview Success**  

> *If you’re preparing for a software‑engineering interview, this post is your cheat‑sheet for one of the most popular LeetCode problems that tests recursion, pruning, and bitmask DP.*

---

## Table of Contents  
1. [Problem Summary](#problem-summary)  
2. [Key Observations & Constraints](#key-observations)  
3. [Solution Strategy](#solution-strategy)  
4. [Reference Implementations](#reference-implementations)  
   - [Python 3](#python-3)  
   - [Java 17](#java-17)  
   - [C++17](#c-17)  
5. [Time & Space Complexity](#complexity)  
6. [Common Pitfalls & “Ugly” Traps](#pitfalls)  
7. [Optimizations & “Good” Tricks](#optimizations)  
8. [Interview Take‑aways](#interview-takeaways)  
9. [SEO Meta Description](#meta)

---

## Problem Summary <a name="problem-summary"></a>
You’re given an array `cookies` where `cookies[i]` is the number of cookies in the *i*‑th bag.  
You must distribute **all** bags among `k` children. A bag cannot be split; each child receives whole bags.  

**Unfairness** of a distribution = maximum total cookies received by any single child.  
Your task: **Return the minimal possible unfairness**.

### Constraints
| Variable | Min | Max | Notes |
|----------|-----|-----|-------|
| `cookies.length` | 2 | 8 | Very small – perfect for backtracking |
| `cookies[i]` | 1 | 10⁵ | Large sums → use 64‑bit integers |
| `k` | 2 | `cookies.length` | |

---

## Key Observations & Constraints <a name="key-observations"></a>

| Observation | Why It Matters |
|-------------|----------------|
| **Only up to 8 bags** | Brute‑force enumeration of assignments (`kⁿ` where `n≤8`) is feasible if we prune wisely. |
| **Sum can be large** | Use `long` / `long long` to avoid overflow. |
| **Goal is minimax** | Classic “fairness” or “load balancing” problem. |
| **Symmetry** | Assigning child `i` to bag `j` is the same as assigning child `j` to bag `i`. We can prune identical states. |
| **Monotonicity** | If current max exceeds a known best, abandon the branch. |
| **Bitmask DP** | With `n≤8` we can keep a bitmask of bags already assigned. |

---

## Solution Strategy <a name="solution-strategy"></a>

1. **Sort the bags in descending order**.  
   - Larger bags placed first → early pruning (branching factor drops faster).  
2. **Backtracking** with pruning:  
   - Maintain an array `childSum[k]` of current cookies per child.  
   - Recursively assign the current bag to any child whose addition doesn’t exceed the best unfairness found so far.  
   - If at any point `max(childSum) >= best`, backtrack.  
3. **Bitmask DP (optional)** – to avoid repeated work when the same set of bags is distributed in different orders.  
   - DP[mask] = minimal possible maximal load for the subset of bags represented by `mask`.  
   - Iterate over submasks; complexity `O(3ⁿ)` which is fine for `n≤8`.  
4. **Return the best found unfairness**.

This approach is the standard “backtracking + pruning” pattern that is heavily favored on LeetCode discussions.

---

## Reference Implementations <a name="reference-implementations"></a>

### Python 3 <a name="python-3"></a>
```python
from typing import List

class Solution:
    def distributeCookies(self, cookies: List[int], k: int) -> int:
        cookies.sort(reverse=True)          # bigger bags first
        n = len(cookies)
        best = sum(cookies)                 # upper bound

        child = [0] * k

        def dfs(idx: int, cur_max: int):
            nonlocal best
            if idx == n:
                best = min(best, cur_max)
                return
            if cur_max >= best:            # pruning
                return
            bag = cookies[idx]
            used = set()
            for i in range(k):
                # skip identical child sums to avoid symmetric states
                if child[i] in used:
                    continue
                used.add(child[i])

                child[i] += bag
                dfs(idx + 1, max(cur_max, child[i]))
                child[i] -= bag

        dfs(0, 0)
        return best
```

**Why it works**  
- The `used` set skips placing a bag in a child that already has the same total, which eliminates permutations that produce the same state.  
- `cur_max` is updated lazily; as soon as it reaches or exceeds the best known, the branch is abandoned.

---

### Java 17 <a name="java-17"></a>
```java
import java.util.*;

class Solution {
    private int best;
    private int[] child;
    private int[] cookies;

    public int distributeCookies(int[] cookies, int k) {
        Arrays.sort(cookies);                  // ascending
        reverse(cookies);                      // descending
        this.cookies = cookies;
        child = new int[k];
        best = Arrays.stream(cookies).sum();    // upper bound

        dfs(0, 0);
        return best;
    }

    private void dfs(int idx, int curMax) {
        if (idx == cookies.length) {
            best = Math.min(best, curMax);
            return;
        }
        if (curMax >= best) return;            // pruning

        int bag = cookies[idx];
        Set<Integer> used = new HashSet<>();
        for (int i = 0; i < child.length; i++) {
            if (!used.add(child[i])) continue; // skip symmetric states

            child[i] += bag;
            dfs(idx + 1, Math.max(curMax, child[i]));
            child[i] -= bag;
        }
    }

    private void reverse(int[] arr) {
        for (int i = 0; i < arr.length / 2; i++) {
            int tmp = arr[i];
            arr[i] = arr[arr.length - 1 - i];
            arr[arr.length - 1 - i] = tmp;
        }
    }
}
```

**Highlights**  
- Uses a `Set` to skip symmetrical child sums.  
- `reverse` helper turns the sorted array into descending order.  
- `best` holds the minimal unfairness found so far.

---

### C++17 <a name="c-17"></a>
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int distributeCookies(vector<int>& cookies, int k) {
        sort(cookies.rbegin(), cookies.rend());   // descending
        int best = accumulate(cookies.begin(), cookies.end(), 0);
        vector<int> child(k, 0);
        dfs(0, 0, best, cookies, child);
        return best;
    }

private:
    void dfs(int idx, int curMax, int &best,
             const vector<int> &cookies, vector<int> &child) {
        if (idx == cookies.size()) {
            best = min(best, curMax);
            return;
        }
        if (curMax >= best) return;               // pruning

        int bag = cookies[idx];
        unordered_set<int> seen;
        for (int i = 0; i < child.size(); ++i) {
            if (!seen.insert(child[i]).second) continue;  // symmetry

            child[i] += bag;
            dfs(idx + 1, max(curMax, child[i]), best, cookies, child);
            child[i] -= bag;
        }
    }
};
```

**Why it’s efficient**  
- `unordered_set<int>` quickly eliminates symmetric states.  
- `accumulate` calculates the initial upper bound in one pass.  

---

## Time & Space Complexity <a name="complexity"></a>

| Approach | Worst‑Case Time | Space |
|----------|-----------------|-------|
| Brute‑force (`kⁿ`) | `O(kⁿ)` | `O(k)` |
| Backtracking with pruning (all three implementations) | `O(kⁿ)` but heavily pruned | `O(k)` |
| Bitmask DP | `O(3ⁿ)` (~ 6561 when n=8) | `O(2ⁿ)` |

Given `n ≤ 8`, all solutions comfortably run in < 10 ms on modern judges.  

---

## Common Pitfalls & “Ugly” Traps <a name="pitfalls"></a>

| Pitfall | Why it breaks | Fix |
|---------|----------------|-----|
| **Not sorting descending** | Small bags placed first leads to many useless branches. | Sort `cookies` in reverse order. |
| **Using `int` for sums** | Cookie counts up to 10⁵, and `k` up to 8 → sum can exceed 2³¹‑1. | Use `long`/`long long`. |
| **No pruning** | Exponential blow‑up on the worst case (`k=8`, `n=8`). | Compare `curMax` with current best. |
| **Ignoring symmetry** | Many equivalent assignments are explored. | Track seen child sums in each depth. |
| **Bitmask DP wrong subset enumeration** | Subset logic error leads to incorrect answer. | Iterate submasks properly (`for (int sub = mask; sub; sub = (sub-1)&mask)`). |

---

## Optimizations & “Good” Tricks <a name="optimizations"></a>

1. **Start with the largest bag** – reduces branching early.  
2. **Memoization on child sums** – store `dp[mask] = best unfairness` to reuse states.  
3. **Early termination** – if any child already has a sum equal to the best, further assignments to that child are useless.  
4. **Iterative deepening** – start searching with a tight bound and gradually relax.  
5. **Bitmask DP** – for interviewers who want to see DP knowledge, this is a neat alternative.

---

## Interview Take‑aways <a name="interview-takeaways"></a>

| Skill | How this problem demonstrates it |
|-------|----------------------------------|
| **Recursive thinking** | Solving with backtracking showcases ability to build depth‑first search. |
| **Pruning & optimization** | Discuss why sorting and symmetry pruning are critical. |
| **Complexity analysis** | Able to explain `O(kⁿ)` vs `O(3ⁿ)` and justify why both fit the constraints. |
| **Edge‑case handling** | Handling large sums (`long`/`long long`) shows attention to overflow. |
| **Code readability** | Clean helper functions (`reverse`, `dfs`) and comments impress interviewers. |

**Tip:** During an interview, always ask clarifying questions about constraints. Here, knowing `n ≤ 8` is the linchpin for backtracking.  

---

## SEO Meta Description <a name="meta"></a>
> Master LeetCode 2305 – Fair Distribution of Cookies. Read the comprehensive guide, Python/Java/C++ solutions, complexity analysis, and interview tips. Boost your coding interview scores!

---

### Final Thoughts

*The Fair Distribution of Cookies problem is deceptively simple, yet it tests your ability to combine recursion, pruning, and bitmask DP—all crucial for a senior‑level coding interview. By mastering the three reference implementations above and understanding the pitfalls, you’ll be ready to ace this problem and demonstrate deep algorithmic fluency.*