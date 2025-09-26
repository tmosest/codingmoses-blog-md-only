---
title: LeetCode 1799. Maximize Score After N Operations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 📌 LeetCode 1799 – **Maximize Score After N Operations**  
**Hard | DP + Bitmask | 1 ≤ n ≤ 7 | 2 · n ≤ 14**  

---

## 🚀 TL;DR  

> **Goal** – pair up all `2n` numbers so that the weighted sum  
> \[
>  \sum_{i=1}^{n} i \times \gcd(x_i, y_i)
> \]
> is maximised.  
> **Solution** – *exponential* bit‑mask DP:  
> * pre‑compute all \(\gcd\) pairs,  
> * traverse all masks (2ⁿ possibilities),  
> * greedily add a new pair if it does not overlap with the current mask.  

The algorithm runs in **O(2ⁿ · n²)** time (≤ 7 · 13 000 operations) and **O(2ⁿ)** memory – well within limits for LeetCode.

---

## 🔍 Problem Breakdown  

| Key Point | Detail |
|-----------|--------|
| **Array length** | `2n`, `n ≤ 7` (so max length = 14) |
| **Operation** | Pick two unused numbers `x` and `y`, score `i * gcd(x, y)` (operation index starts at 1) |
| **Goal** | Maximise total score after exactly `n` operations (all numbers used) |
| **Constraints** | 1 ≤ nums[i] ≤ 10⁶ – fits in 32‑bit int |

Because the array is small, exploring all possible pairings is feasible using a bitmask.

---

## 🎯 Algorithm – Bitmask Dynamic Programming  

1. **Pre‑compute all possible pairs**  
   For each pair `(i, j)` (0‑based indices) store  
   *bitmask* `1<<i | 1<<j` → `gcd(nums[i], nums[j])`.  
   We’ll need this for every DP transition.

2. **DP array** – `dp[mask]` is the best score achievable after using exactly the numbers indicated by `mask`.  
   * `mask` has `2n` bits.  
   * Base: `dp[0] = 0`.  
   * Transition:  
     * `k` = bitmask of a pair.  
     * If `k & mask == 0` (none of the pair’s numbers are used yet)  
       ```text
       newMask = mask | k
       operationIndex = bitCount(mask) / 2 + 1   // because each operation consumes 2 numbers
       dp[newMask] = max(dp[newMask], dp[mask] + operationIndex * gcdPair[k])
       ```

3. **Result** – `dp[(1 << (2n)) - 1]` (all numbers used).

### Complexity  

| Metric | Value |
|--------|-------|
| **Time** | `O(2ⁿ · n²)` – at most `2¹⁴ · 14² ≈ 3.3 × 10⁶` operations |
| **Memory** | `O(2ⁿ)` – about 16 k integers for `n = 7` |

Both are tiny for modern CPUs.

---

## 📦 Code Implementations  

> All implementations share the same logic; only syntax differs.

### Java (LeetCode Style)

```java
import java.util.*;

class Solution {
    public int maxScore(int[] nums) {
        int m = nums.length;                     // 2n
        int maxMask = 1 << m;
        int[] dp = new int[maxMask];             // dp[mask] = best score

        // pre‑compute gcd for every pair
        int[][] pair = new int[m][m];
        for (int i = 0; i < m; ++i)
            for (int j = i + 1; j < m; ++j)
                pair[i][j] = gcd(nums[i], nums[j]);

        for (int mask = 0; mask < maxMask; ++mask) {
            // number of already used elements
            int used = Integer.bitCount(mask);
            if (used % 2 != 0) continue;         // can't be a valid state

            // try to add a new pair
            for (int i = 0; i < m; ++i) {
                if ((mask & (1 << i)) != 0) continue;   // i already used
                for (int j = i + 1; j < m; ++j) {
                    if ((mask & (1 << j)) != 0) continue; // j already used
                    int nextMask = mask | (1 << i) | (1 << j);
                    int opIdx = used / 2 + 1;   // 1‑based operation number
                    int cand = dp[mask] + opIdx * pair[i][j];
                    if (cand > dp[nextMask]) dp[nextMask] = cand;
                }
            }
        }
        return dp[maxMask - 1];
    }

    private int gcd(int a, int b) {
        while (b != 0) {
            int t = a % b;
            a = b;
            b = t;
        }
        return a;
    }
}
```

### Python (LeetCode Style)

```python
import math
from functools import lru_cache

class Solution:
    def maxScore(self, nums):
        m = len(nums)                     # 2n
        max_mask = 1 << m
        dp = [0] * max_mask

        # pre‑compute all pair gcd values
        pair = [[0] * m for _ in range(m)]
        for i in range(m):
            for j in range(i + 1, m):
                pair[i][j] = math.gcd(nums[i], nums[j])

        for mask in range(max_mask):
            used = bin(mask).count('1')
            if used & 1:                  # odd count -> impossible state
                continue

            for i in range(m):
                if mask & (1 << i):      # already used
                    continue
                for j in range(i + 1, m):
                    if mask & (1 << j):
                        continue
                    next_mask = mask | (1 << i) | (1 << j)
                    op_idx = used // 2 + 1
                    cand = dp[mask] + op_idx * pair[i][j]
                    if cand > dp[next_mask]:
                        dp[next_mask] = cand

        return dp[max_mask - 1]
```

### C++ (LeetCode Style)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxScore(vector<int>& nums) {
        int m = nums.size();              // 2n
        int maxMask = 1 << m;
        vector<int> dp(maxMask, 0);

        // pre‑compute gcd for all pairs
        vector<vector<int>> pair(m, vector<int>(m, 0));
        for (int i = 0; i < m; ++i)
            for (int j = i + 1; j < m; ++j)
                pair[i][j] = std::gcd(nums[i], nums[j]);

        for (int mask = 0; mask < maxMask; ++mask) {
            int used = __builtin_popcount(mask);
            if (used & 1) continue;              // must be even

            for (int i = 0; i < m; ++i) {
                if (mask & (1 << i)) continue;   // i already used
                for (int j = i + 1; j < m; ++j) {
                    if (mask & (1 << j)) continue; // j already used
                    int nextMask = mask | (1 << i) | (1 << j);
                    int opIdx = used / 2 + 1;
                    int cand = dp[mask] + opIdx * pair[i][j];
                    dp[nextMask] = max(dp[nextMask], cand);
                }
            }
        }
        return dp[maxMask - 1];
    }
};
```

> **Tip:** All three snippets use the same `pair` table to avoid recomputing GCDs during DP.  

---

## 🧐 Good, Bad, and Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Approach** | *Bitmask DP* is a textbook solution for small `n`. | *Exponential* time is inevitable; for `n > 7` it becomes impractical. | None—`n ≤ 7` is guaranteed, so the algorithm is safe. |
| **Complexity** | Fast enough (`2⁷ · 14² ≈ 3M` ops). | Memory usage is `2ⁿ` ints (16 k for n=7). | If someone mis‑reads `n` as array length, they might set `maxMask = 1 << n` → wrong state space. |
| **Code Clarity** | Pre‑computing GCD pairs keeps DP transition simple. | Nested loops (`i`/`j`) might look messy. | Forgetting to skip *odd‑count* states leads to incorrect results. |
| **Edge Cases** | Handles all `nums[i] = 1` correctly. | None. | None. |
| **Extensibility** | Easy to adapt for larger `n` with *meet‑in‑the‑middle* or *branch‑and‑bound* tricks. | Requires major algorithmic change for `n > 7`. | None. |

---

## 🎯 Interview Tips

1. **Clarify constraints** – ask the interviewer if `n` can grow beyond 7.  
2. **Explain DP state** – “mask of used indices” + “current operation index”.  
3. **Justify pre‑computing GCD** – saves time in DP loop.  
4. **Show complexity** – O(2ⁿ · n²) is acceptable.  
5. **Walk through a small example** – e.g., `nums = [2,3,4,9]` to illustrate mask transitions.  

---

## 🔗 Further Reading  

| Topic | Link |
|-------|------|
| Bitmask DP fundamentals | [LeetCode Discuss: 1072. Flip Columns For Maximum Number of Equal Rows](https://leetcode.com/problems/flip-columns-for-maximum-number-of-equal-rows/discuss/) |
| Meet‑in‑the‑middle for pairing problems | [Codeforces 1036C – Flipping Game](https://codeforces.com/problemset/problem/1036/C) |
| GCD optimizations | [C++17 std::gcd](https://en.cppreference.com/w/cpp/numeric/gcd) |

---

## 🚀 Summary

* The problem’s tiny input size turns a seemingly hard combinatorial problem into a neat bitmask DP.  
* Pre‑computing all pairwise GCDs turns the DP transition into a single multiplication.  
* The provided Java, Python, and C++ solutions all follow LeetCode conventions and run comfortably under the platform’s time limits.

Use this guide to ace the question, impress recruiters, or simply sharpen your algorithmic toolbox!  

--- 

*Happy coding!* 🎉
--- 
**Keywords:** `maximization`, `bitmask`, `dynamic programming`, `gcd`, `LeetCode`, `Java`, `Python`, `C++`, `algorithm design`, `interview strategy`
--- 
**Disclaimer:** All solutions are tested on the official LeetCode online judge.