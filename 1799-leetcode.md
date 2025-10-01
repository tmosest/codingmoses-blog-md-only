---
title: LeetCode 1799. Maximize Score After N Operations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 1799 – **Maximize Score After N Operations**  
**Solution in Java, Python & C++ (Bit‑Mask DP)**

The goal of the problem is to pair up all numbers in the array **exactly once** and, for the *i‑th* pair (1‑based), add  
`i * gcd(x, y)` to a running score.  
We want the maximum possible score.

Because `n ≤ 7` (the array length is `2*n ≤ 14`), the classic **bit‑mask dynamic programming** solution runs in time `O(2^n · n²)` – easily fast enough.

Below you’ll find a compact, fully‑commented implementation in three languages.

---

### 1.1 Java 17

```java
import java.util.*;

public class Solution {
    public int maxScore(int[] nums) {
        int n = nums.length;            // 2*n in the problem statement
        int fullMask = (1 << n) - 1;     // all numbers are used
        int[] dp = new int[1 << n];
        Arrays.fill(dp, Integer.MIN_VALUE);
        dp[0] = 0;                       // base case: no number used → score 0

        // pre‑compute all gcd values (optional, but faster)
        int[][] gcd = new int[n][n];
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                gcd[i][j] = gcd(nums[i], nums[j]);
            }
        }

        // iterate over every mask
        for (int mask = 0; mask <= fullMask; mask++) {
            if (dp[mask] == Integer.MIN_VALUE) continue; // unreachable
            int used = Integer.bitCount(mask);           // how many numbers already used
            if (used % 2 == 1) continue;                // we only extend from even masks

            // pick the first unused index to reduce duplicate work
            int i = 0;
            while (((mask >> i) & 1) == 1) i++;
            // try pairing it with every other unused j
            for (int j = i + 1; j < n; j++) {
                if (((mask >> j) & 1) == 1) continue;    // j already used
                int newMask = mask | (1 << i) | (1 << j);
                int opIdx = used / 2 + 1;                // operation number (1‑based)
                int candidate = dp[mask] + opIdx * gcd[i][j];
                if (candidate > dp[newMask]) dp[newMask] = candidate;
            }
        }
        return dp[fullMask];
    }

    /* Euclid’s algorithm – fast & iterative */
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

---

### 1.2 Python 3

```python
from math import gcd
from functools import lru_cache

def maxScore(nums: list[int]) -> int:
    n = len(nums)
    full_mask = (1 << n) - 1
    dp = [float('-inf')] * (1 << n)
    dp[0] = 0

    # Pre‑compute gcd values for O(1) look‑ups
    g = [[0] * n for _ in range(n)]
    for i in range(n):
        for j in range(i + 1, n):
            g[i][j] = gcd(nums[i], nums[j])

    for mask in range(1 << n):
        if dp[mask] == float('-inf'):
            continue
        used = mask.bit_count()
        if used & 1:        # odd count → cannot add a pair
            continue

        # pick first unused index to avoid symmetric duplicates
        i = 0
        while mask & (1 << i):
            i += 1

        for j in range(i + 1, n):
            if mask & (1 << j):
                continue
            new_mask = mask | (1 << i) | (1 << j)
            op = used // 2 + 1
            val = dp[mask] + op * g[i][j]
            if val > dp[new_mask]:
                dp[new_mask] = val

    return dp[full_mask]
```

---

### 1.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxScore(vector<int>& nums) {
        int n = nums.size();
        int full = (1 << n) - 1;
        vector<int> dp(1 << n, INT_MIN);
        dp[0] = 0;

        // Pre‑compute gcds
        vector<vector<int>> g(n, vector<int>(n, 0));
        for (int i = 0; i < n; ++i)
            for (int j = i + 1; j < n; ++j)
                g[i][j] = std::gcd(nums[i], nums[j]);

        for (int mask = 0; mask <= full; ++mask) {
            if (dp[mask] == INT_MIN) continue;
            int used = __builtin_popcount(mask);
            if (used & 1) continue;          // only extend from even masks

            int i = 0;
            while (mask & (1 << i)) ++i;      // first unused index

            for (int j = i + 1; j < n; ++j) {
                if (mask & (1 << j)) continue;
                int newMask = mask | (1 << i) | (1 << j);
                int op = used / 2 + 1;
                int val = dp[mask] + op * g[i][j];
                dp[newMask] = max(dp[newMask], val);
            }
        }
        return dp[full];
    }
};
```

All three solutions run in **O(2ⁿ · n²)** time and **O(2ⁿ)** memory.  
With `n ≤ 7`, the maximum number of masks is `2¹⁴ = 16,384`, which is trivial even on an online judge.

---

## 2.  Blog Article – “Mastering LeetCode 1799: Maximize Score After N Operations”

> **SEO Keywords**: LeetCode 1799, maximize score after n operations, bit‑mask DP, dynamic programming interview, job interview algorithms, C++ Java Python solutions

---

### 2.1 Introduction

If you’re hunting for a **software engineering role** or just looking to polish your problem‑solving chops, LeetCode 1799 – *Maximize Score After N Operations* – is a perfect showcase. It tests:

- **Bit‑mask DP** – a classic technique for “choose‑subset” problems.
- **GCD** – efficient integer math.
- **State pruning** – a subtle but powerful optimization.

In this post we’ll walk through the problem, explain the *good*, *bad*, and *ugly* parts of typical solutions, present clean code in **Java, Python, C++**, and give you interview‑ready insights.

---

### 2.2 Problem Restatement

You’re given an array `nums` of `2 * n` positive integers (`1 ≤ n ≤ 7`).  
You will perform exactly `n` operations. In the *i‑th* operation you:

1. Pick two unused elements `x` and `y`.
2. Gain a score of `i * gcd(x, y)`.
3. Remove `x` and `y` from the array.

Return the **maximum** possible total score after all `n` operations.

---

### 2.3 Why Bit‑Mask DP?

* **State** – which elements have already been paired?  
  Represented by a binary mask of length `2*n` (max 14 bits).

* **Transition** – from a mask with an even number of bits set (i.e. we’ve performed `k` pairs), pick any two zero‑bits, pair them, and add ` (k+1) * gcd(...) ` to the score.

* **DP array** – `dp[mask] = maximum score achievable after using the elements indicated by `mask`.  
  Final answer: `dp[(1 << (2*n)) - 1]`.

Because `n` is tiny, enumerating all `2^(2n)` states (≤ 16,384) is trivial, yet the logic is non‑trivial – that’s why it’s marked “Hard” on LeetCode.

---

### 2.4 The “Good” – A Clean, Readable Implementation

- **Iterative DP** instead of recursion → no stack overhead, easier to debug.
- **Pre‑computed GCD table** – speeds up look‑ups from `O(log M)` to `O(1)`.
- **Pick first unused index** → reduces symmetric transitions, cutting the constant factor in half.
- **Bit‑count guard** → we only extend from masks that already contain an even number of set bits.

All languages above illustrate this “good” approach.

---

### 2.5 The “Bad” – Common Pitfalls

| Pitfall | Why it hurts | Fix |
|---------|--------------|-----|
| **Recursive backtracking** with memoization | Exposes you to accidental deep recursion and harder to trace DP states | Use a flat array and iterate over masks |
| **Re‑computing GCD on the fly** | Still fast, but extra log operations per transition | Pre‑compute a 2‑D matrix |
| **Enumerating all pairs naively** | You’ll double‑count symmetric pairs (i ↔ j) | Pick the first unused index, only pair it with later unused ones |
| **Wrong bit‑count logic** | Mixing 0‑based vs 1‑based operation indices can be a source of off‑by‑one errors | Compute `op = used / 2 + 1` after a mask with `used` bits set |

---

### 2.5 The “Ugly” – What to Avoid

- **Unbounded recursion + `@lru_cache`** on Python – works but uses more memory and can hit recursion limits if the constraints were relaxed.
- **Global variables** or static arrays – okay in contests but not interview‑friendly; prefer local scopes.
- **Hard‑coding array size** – always compute `fullMask = (1 << n) - 1` instead of assuming 14 bits.

---

### 2.6 Optimization Tips for Interviews

1. **Pre‑compute GCD** – saves a `log(min(x, y))` call per transition.
2. **Bit‑operations** – use built‑ins like `__builtin_popcount` (C++), `mask.bit_count()` (Python 3.10+), or `Integer.bitCount()` (Java).
3. **Early pruning** – skip masks that are unreachable (`dp[mask] == MIN_VALUE`).
4. **Symmetry reduction** – always pair the *first* unused element with a later unused one; this eliminates 2× redundant transitions.

---

### 2.7 Job‑Interview Takeaways

* **Show the state diagram** – explain the mask and how many pairs you’ve performed.  
  Interviewers love seeing that you *understand the dynamic programming state machine*.

* **Explain the operation number** – why it’s `k+1`? It’s the 1‑based index of the current operation, derived from the number of pairs already formed (`k = used / 2`).

* **Discuss trade‑offs** – e.g., iterative vs recursive, time vs memory, pre‑computing vs on‑the‑fly.  
  Demonstrates critical thinking beyond just writing a working solution.

* **Talk about limits** – `n ≤ 7` → you can safely enumerate all states; ask “what if `n` were 20?” → leads into exploring *meet‑in‑the‑middle* or *branch‑and‑bound* techniques.

---

### 2.8 Sample Code Snippet (Python)

```python
from math import gcd

def maxScore(nums):
    n = len(nums)
    full_mask = (1 << n) - 1
    dp = [float('-inf')] * (1 << n)
    dp[0] = 0

    g = [[0]*n for _ in range(n)]
    for i in range(n):
        for j in range(i+1,n):
            g[i][j] = gcd(nums[i], nums[j])

    for mask in range(1 << n):
        if dp[mask] == float('-inf'): continue
        used = mask.bit_count()
        if used & 1: continue

        i = 0
        while mask & (1 << i): i += 1
        for j in range(i+1,n):
            if mask & (1 << j): continue
            new = mask | (1 << i) | (1 << j)
            op = used//2 + 1
            dp[new] = max(dp[new], dp[mask] + op * g[i][j])

    return dp[full_mask]
```

> **Tip**: In interviews, write the function signature the interviewer asks for (e.g., `int maxScore(int[] nums)` in Java) and then explain the loop logic on a whiteboard.

---

### 2.9 Closing Thoughts

LeetCode 1799 is more than a “pair‑up” puzzle; it’s a *state machine* problem that rewards:

- **Understanding of bit‑mask representations**.
- **Thoughtful transition design**.
- **Clear, maintainable code**.

By mastering this problem you’ll be able to explain state‑based DP to recruiters, show proficiency in integer math, and demonstrate the ability to tackle “hard” dynamic‑programming questions—skills that are highly prized in top tech companies.

Good luck with your interview prep! 🚀

---