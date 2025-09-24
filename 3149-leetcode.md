---
title: LeetCode 3149. Find the Minimum Cost Array Permutation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 3149 – Find the Minimum Cost Array Permutation  
**Hard | LeetCode | Interview‑Ready | Java | Python | C++**

> **Why this problem matters**  
> *Bit‑mask DP + lexicographic tie‑breaking* is a classic interview pattern.  
> Mastering it shows you can:  
> * Design exponential‑time solutions with pruning.  
> * Handle subtle tie‑breaking rules.  
> * Write clean, idiomatic code in multiple languages.  

Below you’ll find:

1. A **complete, production‑ready solution** in Java, Python and C++  
2. A **SEO‑optimized blog post** that walks through the *good, the bad, and the ugly* of the problem  
3. Tips on how to use this challenge to land your next coding interview

---

## 1. Problem Recap

Given a permutation `nums` of `[0,1,…,n‑1]` (`2 ≤ n ≤ 14`), we need to output a permutation `perm` of the same set that **minimizes**

```
score(perm) = |perm[0] – nums[perm[1]]| + |perm[1] – nums[perm[2]]| + … + |perm[n‑1] – nums[perm[0]]|
```

If several permutations give the same minimal score, return the **lexicographically smallest** one.

---

## 2. High‑Level Idea

We treat the problem as a traveling‑salesman‑style tour on a complete directed graph:

* **Vertices** – the numbers `0 … n‑1`.  
* **Edge weight** from `a` to `b` – `|a – nums[b]|`.  
* We must find the **minimum‑weight Hamiltonian cycle** that starts at `0` (because any optimal cycle can be rotated to start at `0` and the score is invariant under rotation).  
* The cycle order will be the permutation `perm`.

With `n ≤ 14`, a **bit‑mask DP** (`O(n²·2ⁿ)`) is fast enough.

---

## 3. DP State

```
dp[last][mask] = minimal score of a path that:
                  • has already visited the vertices indicated by 'mask'
                  • ends at vertex 'last'
```

`mask` is a bitmask of length `n` (bit `i` is 1 if vertex `i` has been visited).  
Initially, we start at vertex `0` with only `0` visited (`mask = 1 << 0`).

**Transition**

```
dp[next][mask | (1 << next)] =
      min( dp[last][mask] + |last – nums[next]| )
      over all next that are not yet visited
```

**Base**

When all vertices are visited (`mask == (1 << n) - 1`), we close the cycle:

```
score = dp[last][mask] + |last – nums[0]|
```

We store the best *next* vertex to rebuild the optimal permutation.

---

## 4. Lexicographic Tie‑Breaking

If two choices give the same total score, we must prefer the smaller index because the permutation that picks the smaller number earlier is lexicographically smaller.

In the transition:

```python
if candidate_score < best_score or \
   (candidate_score == best_score and next < best_next):
        best_score = candidate_score
        best_next  = next
```

This rule propagates correctly to the final answer.

---

## 5. Code

### 5.1 Java

```java
import java.util.*;

public class Solution {
    public int[] findPermutation(int[] nums) {
        int n = nums.length;
        int FULL = (1 << n) - 1;
        int[][] dp   = new int[n][1 << n];
        int[][] next = new int[n][1 << n];
        for (int i = 0; i < n; i++) Arrays.fill(dp[i], -1);

        dfs(0, 1 << 0, nums, dp, next, FULL);

        // Reconstruct
        int[] perm = new int[n];
        int mask = 1 << 0;
        int last = 0;
        for (int i = 0; i < n; i++) {
            perm[i] = last;
            int nx = next[last][mask];
            mask |= 1 << nx;
            last = nx;
        }
        return perm;
    }

    private int dfs(int last, int mask, int[] nums,
                    int[][] dp, int[][] next, int FULL) {
        if (dp[last][mask] != -1) return dp[last][mask];
        if (mask == FULL) {                     // All visited → close cycle
            return Math.abs(last - nums[0]);
        }
        int bestScore = Integer.MAX_VALUE;
        int bestNext  = -1;
        for (int nxt = 0; nxt < nums.length; nxt++) {
            if ((mask & (1 << nxt)) != 0) continue;   // already visited
            int cur = Math.abs(last - nums[nxt]) + dfs(nxt, mask | (1 << nxt),
                                                        nums, dp, next, FULL);
            if (cur < bestScore || (cur == bestScore && nxt < bestNext)) {
                bestScore = cur;
                bestNext  = nxt;
            }
        }
        dp[last][mask] = bestScore;
        next[last][mask] = bestNext;
        return bestScore;
    }
}
```

---

### 5.2 Python

```python
from functools import lru_cache
from typing import List

class Solution:
    def findPermutation(self, nums: List[int]) -> List[int]:
        n = len(nums)
        FULL = (1 << n) - 1
        best_next = [[-1] * (1 << n) for _ in range(n)]

        @lru_cache(None)
        def dp(last: int, mask: int) -> int:
            if mask == FULL:                      # close cycle
                return abs(last - nums[0])

            best = float('inf')
            best_nxt = -1
            for nxt in range(n):
                if mask & (1 << nxt):
                    continue
                cand = abs(last - nums[nxt]) + dp(nxt, mask | (1 << nxt))
                if cand < best or (cand == best and nxt < best_nxt):
                    best = cand
                    best_nxt = nxt
            best_next[last][mask] = best_nxt
            return best

        dp(0, 1 << 0)          # start at 0

        # Reconstruct
        perm = []
        mask = 1 << 0
        last = 0
        for _ in range(n):
            perm.append(last)
            nxt = best_next[last][mask]
            mask |= 1 << nxt
            last = nxt
        return perm
```

---

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> findPermutation(vector<int>& nums) {
        int n = nums.size();
        int FULL = (1 << n) - 1;
        vector<vector<int>> dp(n, vector<int>(1 << n, -1));
        vector<vector<int>> nxt(n, vector<int>(1 << n, -1));

        function<int(int,int)> solve = [&](int last, int mask) -> int {
            if (dp[last][mask] != -1) return dp[last][mask];
            if (mask == FULL)                 // close cycle
                return abs(last - nums[0]);

            int bestScore = INT_MAX;
            int bestNext = -1;
            for (int next = 0; next < n; ++next) {
                if (mask & (1 << next)) continue;          // visited
                int cur = abs(last - nums[next]) + solve(next, mask | (1 << next));
                if (cur < bestScore || (cur == bestScore && next < bestNext)) {
                    bestScore = cur;
                    bestNext = next;
                }
            }
            dp[last][mask] = bestScore;
            nxt[last][mask] = bestNext;
            return bestScore;
        };

        solve(0, 1 << 0);          // start from 0

        // Reconstruct
        vector<int> perm;
        int mask = 1 << 0;
        int last = 0;
        for (int i = 0; i < n; ++i) {
            perm.push_back(last);
            int next = nxt[last][mask];
            mask |= 1 << next;
            last = next;
        }
        return perm;
    }
};
```

---

## 6. Complexity

| Language | Time | Memory |
|----------|------|--------|
| Java / Python / C++ | **O(n² · 2ⁿ)** → ~ 2 × 10⁶ operations when `n = 14` | **O(n · 2ⁿ)** integers (~ 1 MiB) |

Both run comfortably under LeetCode’s 2‑second limit.

---

## 7. Good, the Bad, the Ugly

| Phase | What went right | What went wrong | How we fixed it |
|-------|-----------------|-----------------|-----------------|
| **Good** | • `n ≤ 14` → exponential DP is viable. <br>• Lexicographic tie‑break is a small addition. <br>• The cycle is rotation‑invariant, so fixing the start to `0` simplifies reconstruction. | | |
| **Bad** | • Naïve recursion over 14! possibilities is hopeless. <br>• Forgetting the `nums[next]` term makes the edge weight wrong. | The wrong transition leads to **incorrect scores** and a wrong answer. |
| **Ugly** | • Some interviewers use the *reverse* of `nums` or a 1‑based array. <br>• Edge weights might be negative if you forget the absolute value. <br>• Tie‑breaking is easy to overlook – you may return a *sub‑lexicographically* minimal cycle. | We solved it by adding the tie‑break `(candidate == best && next < best_next)` during DP. |

---

## 8. Edge Cases to Test

| Case | Why it matters | Expected Output |
|------|----------------|-----------------|
| `nums = [0,1]` | Minimal `n`. | `[0,1]` |
| `nums = [3,0,1,2]` | Smallest cycle with ties. | `[0,1,2,3]` |
| All numbers identical to indices (`nums[i] = i`) | Edge weight simplifies to `|a – b|`. | `[0,1,2,…]` |
| `nums` in descending order | Heavy weights, check tie‑break. | `[0,1,2,…]` |

---

## 9. How to Use This Problem in Your Interview Prep

1. **Showcase Multi‑Language Skills** – Copy the same logic into Java, Python, and C++ (or any other language).  
2. **Discuss the “why”** – Interviewers love to hear your intuition before the code.  
3. **Highlight the Lexicographic Tie‑Break** – Many candidates miss this subtle rule; explain it clearly.  
4. **Mention the 2‑Second Limit** – It shows you care about performance and complexity analysis.  
5. **Prepare Follow‑Up Questions** – “What if `n` were 20?” or “What if the array had negative numbers?” – ready to dive deeper.

---

## 10. Call to Action

> **Want to ace the next coding interview?**  
> Add **LeetCode 3149** to your repo.  
> Push the solution to a public repo (GitHub, GitLab) and share the README with the exact code for Java, Python, and C++.  
> Write a blog post (like the one above) and pin it to **LinkedIn**, **Medium** or **Dev.to**.  
> Recruiters love seeing polished posts that walk through your thought process.

---

### 📌 Keywords for SEO

- LeetCode 3149 Find the Minimum Cost Array Permutation  
- Bitmask DP traveling salesman problem  
- Java Python C++ solution LeetCode 3149  
- Interview question dynamic programming bitmask  
- How to solve LeetCode 3149 in 5 minutes  
- Optimize Hamiltonian cycle with lexicographic tie‑break  
- Coding interview problem 3149  
- Land a job with LeetCode 3149  

---

## 11. Final Thoughts

*Bit‑mask DP* feels intimidating, but once you break the problem into “what‑to‑do‑next” states, the solution is systematic.  
The **lexicographic tie‑break** is just a tiny extra rule that keeps the algorithm deterministic.  

Master this challenge, add the clean code snippets above to your portfolio, and you’ll have a concrete, interview‑ready example of handling exponential algorithms *with finesse*.

Happy coding, and good luck on your next interview! 💼✨