---
title: LeetCode 1799. Maximize Score After N Operations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Maximize Score After N Operations – 1799  
**LeetCode | Hard | 7 ≤ n ≤ 7**

---

## 1.  Problem Recap (SEO‑friendly intro)

> **LeetCode 1799 – Maximize Score After N Operations**  
> **Difficulty:** Hard  
> **Tags:** Bitmask, DP, GCD, Recursion, Backtracking

You are given an array `nums` of length `2 × n` (`1 ≤ n ≤ 7`).  
In the *i*‑th operation you pick two unused numbers `x` and `y`, earn `i × gcd(x, y)`, and remove them.  
After `n` operations all numbers are used.  
Return the **maximum total score** you can achieve.

> **Why this problem matters for your next interview?**  
> • It tests your ability to *exploit tiny constraints* with a *bitmask DP*  
> • You need to combine *dynamic programming* with *number theory (gcd)*  
> • LeetCode’s “Hard” tag means interviewers look for a clean, optimal solution

---

## 2.  Understanding the Constraints

| Parameter | Value |
|-----------|-------|
| `nums.length` | `2 × n` |
| `n` | `1 … 7` |
| `nums[i]` | `1 … 10⁶` |

* **n is tiny** – at most 7.  
* **2ⁿ ≤ 128** – a bitmask over the indices is perfectly feasible.  
* **Time‑complexity of O(2ⁿ × n²)** (≈ 128 × 49 ≈ 6 k operations) is trivial in practice.  

The small `n` is the *good* part – we can explore *every* pairing. The *bad* part is the fact that a naïve recursion that picks an arbitrary pair will revisit the same state many times unless we memoize. The *ugly* part is correctly handling the *operation order* (the multiplier `i`) while building the DP.

---

## 3.  Core Idea – Bitmask Dynamic Programming

### 3.1  What is a bitmask?

Treat each element’s index `0 … 2n‑1` as a bit.  
```
mask bit 0 -> element 0 is used
mask bit 1 -> element 1 is used
...
mask bit (2n-1) -> element 2n-1 is used
```

`mask = 0` means *no elements used*.  
`mask = (1 << (2n)) – 1` means *all elements used*.

### 3.2  DP state

`dp[mask] = maximum score achievable when the set of used indices is exactly mask`.

Because each operation consumes **exactly two** unused numbers, we only consider masks with an even number of bits set.  
Let `k = number_of_bits(mask) / 2` be the count of already finished operations.  
The next operation to perform will be operation `k + 1`.

### 3.3  Transition

For every unused pair `(i, j)`:

```
pairMask = (1 << i) | (1 << j)
newMask  = mask | pairMask          // add the pair to the used set
dp[newMask] = max(dp[newMask],
                  dp[mask] + (k+1) * gcd(nums[i], nums[j]))
```

Because the pair is chosen from *unused* indices, we only apply the transition if `(mask & pairMask) == 0`.

### 3.4  Pre‑computation of GCDs

Since `n ≤ 7`, we can pre‑compute the gcd for every unordered pair `(i, j)` (there are at most 28 of them).  
Store them in a list of tuples: `(pairMask, gcd)`.

### 3.5  Bottom‑up vs. Top‑down

* **Bottom‑up**: iterate masks from 0 to `FULL`.  
* **Top‑down** (memoized recursion): recursively compute the best for a given mask, exploring all possible pairs first.  
Both are equivalent in complexity; the bottom‑up version is a bit shorter and easier to debug.

---

## 4.  Code – 3 Languages

Below are clean, production‑ready implementations for **Java**, **Python**, and **C++**.

### 4.1  Java (Bitmask DP)

```java
import java.util.*;

public class Solution {
    public int maxScore(int[] nums) {
        int m = nums.length;              // 2 * n
        int FULL = (1 << m) - 1;
        int n = m / 2;

        // Pre‑compute GCD for every pair and store (pairMask, gcd)
        List<Pair> pairs = new ArrayList<>();
        for (int i = 0; i < m; i++) {
            for (int j = i + 1; j < m; j++) {
                int mask = (1 << i) | (1 << j);
                pairs.add(new Pair(mask, gcd(nums[i], nums[j])));
            }
        }

        int[] dp = new int[FULL + 1];
        Arrays.fill(dp, Integer.MIN_VALUE);
        dp[0] = 0;                         // nothing used, score 0

        for (int mask = 0; mask <= FULL; mask++) {
            if (dp[mask] == Integer.MIN_VALUE) continue;
            int usedPairs = Integer.bitCount(mask) / 2;   // already finished ops
            int nextOp = usedPairs + 1;                  // operation index (1‑based)

            for (Pair p : pairs) {
                if ((mask & p.mask) != 0) continue; // pair already used
                int newMask = mask | p.mask;
                dp[newMask] = Math.max(dp[newMask],
                        dp[mask] + nextOp * p.gcd);
            }
        }
        return dp[FULL];
    }

    private int gcd(int a, int b) {
        return b == 0 ? a : gcd(b, a % b);
    }

    private static class Pair {
        int mask, gcd;
        Pair(int mask, int gcd) { this.mask = mask; this.gcd = gcd; }
    }
}
```

**Key points**

* `FULL` is a mask with all `2n` bits set.  
* `dp` is initialized to `Integer.MIN_VALUE` except for `dp[0]`.  
* The transition uses the *next operation index* `nextOp` which is derived from the current mask.

---

### 4.2  Python (Bottom‑up DP)

```python
from math import gcd
from functools import lru_cache

class Solution:
    def maxScore(self, nums) -> int:
        m = len(nums)                 # 2 * n
        n = m // 2
        FULL = (1 << m) - 1

        # Pre‑compute all pair masks and gcd values
        pairs = []
        for i in range(m):
            for j in range(i + 1, m):
                mask = (1 << i) | (1 << j)
                pairs.append((mask, gcd(nums[i], nums[j])))

        dp = [-10**9] * (FULL + 1)
        dp[0] = 0

        for mask in range(FULL + 1):
            if dp[mask] == -10**9:
                continue
            used_pairs = bin(mask).count('1') // 2
            next_op = used_pairs + 1

            for pm, g in pairs:
                if mask & pm:
                    continue
                new_mask = mask | pm
                dp[new_mask] = max(dp[new_mask], dp[mask] + next_op * g)

        return dp[FULL]
```

**Why Python works**

* The same logic, just with Python’s dynamic typing.  
* Uses `gcd` from the standard library.  
* `-10**9` is a safe “negative infinity” for integer DP.

---

### 4.3  C++17 (Bitmask DP)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxScore(vector<int>& nums) {
        int m = nums.size();          // 2 * n
        int n = m / 2;
        int FULL = (1 << m) - 1;

        // Pre‑compute all pair masks and their gcd
        struct Pair { int mask; int g; };
        vector<Pair> pairs;
        for (int i = 0; i < m; ++i) {
            for (int j = i + 1; j < m; ++j) {
                int mask = (1 << i) | (1 << j);
                int g = std::gcd(nums[i], nums[j]);
                pairs.push_back({mask, g});
            }
        }

        vector<int> dp(FULL + 1, INT_MIN);
        dp[0] = 0;

        for (int mask = 0; mask <= FULL; ++mask) {
            if (dp[mask] == INT_MIN) continue;
            int usedPairs = __builtin_popcount(mask) / 2;
            int nextOp = usedPairs + 1;

            for (const auto& p : pairs) {
                if (mask & p.mask) continue;
                int newMask = mask | p.mask;
                dp[newMask] = max(dp[newMask], dp[mask] + nextOp * p.g);
            }
        }
        return dp[FULL];
    }
};
```

* Uses `__builtin_popcount` for fast bit counting.  
* Standard `std::gcd` from C++17.

---

## 5.  Complexity Analysis

| Step | Complexity |
|------|------------|
| Pre‑compute all pairs | O((2n)²) = O(49) |
| DP over masks | O(2²ⁿ) = O(128) |
| Transition per mask | Each mask loops over at most 28 pairs | O(28) |
| **Total** | **O(2ⁿ × n²)** ≈ 6 k integer ops |

Memory: `dp` array of size `2^(2n)` → at most 129 integers.  
Both time and memory are *well below any realistic limits*.

---

## 6.  “Good, Bad, Ugly” – A Quick Recap

| Aspect | Explanation |
|--------|-------------|
| **Good** | Tiny `n` → full search with a bitmask is trivial. |
| **Bad** | Without memoization recursion would recompute the same mask many times. |
| **Ugly** | Maintaining the correct operation multiplier (`i`) while adding a pair to the DP state. |

> **Takeaway:**  
> *Pre‑compute all states.*  
> *Memoize (or iterate bottom‑up).*  
> *Compute the operation index from the current mask.*  

---

## 6.  Interview‑Ready Tips

1. **Explain the bitmask before diving into code.**  
   Interviewers appreciate a *clear mapping* from the problem to DP states.  
2. **Mention the “state compression” technique** – a key insight for “Hard” problems with small input sizes.  
3. **Show your pre‑computation strategy** – this avoids recomputing gcd many times.  
4. **Edge Cases**  
   * `n = 1` → only one operation, mask jumps from 0 to FULL directly.  
   * All numbers equal → all gcds are the same, DP still works.  
   * Distinct numbers → gcd often 1, but multiplier still matters.  
5. **Run the solution on the provided examples** (Java, Python, C++) to demonstrate correctness.

---

## 7.  How to Nail It in a Live Coding Interview

1. **Start with a high‑level sketch** – “We’ll use a bitmask DP where each state represents which indices are used.”  
2. **Clarify the operation order** – “Operation i is determined by how many pairs have already been taken.”  
3. **Write a helper** to compute gcd; in Java/Python/C++ you can rely on built‑ins.  
4. **Implement DP bottom‑up** – iterate all masks, skip odd‑bit masks, transition over all unused pairs.  
5. **Test** on small arrays first (`[3,4]`, `[5,7,8,10]`, `[2,3,5,7,11,13]`).  
6. **Explain your complexity** – “O(2ⁿ × n²) which is trivial for n ≤ 7.”

---

## 8.  Final Thought – Why This Solution Succeeds

* **Optimal**: we consider every possible sequence of pairings.  
* **Efficient**: bitmask DP turns the problem into a small table lookup.  
* **Scalable to “Hard”**: the same pattern (DP + pre‑computation + bitmask) is used in many interview questions (e.g., “Maximum Score After Applying Operations” series, “Minimum Cost to Merge Stones”, etc.).  

Now you’re equipped with:

* A **clean algorithm** that interviewers love.  
* Three **ready‑to‑copy** code snippets for Java, Python, C++.  
* The confidence to explain every design decision during an interview.

Good luck! 🚀

--- 

**TL;DR**  
> Use a bitmask DP over the indices.  
> For each mask pick an unused pair, apply `(k+1) * gcd`.  
> Pre‑compute all pair GCDs.  
> Complexity O(2ⁿ × n²) – trivial for n ≤ 7.  
> Implemented in Java, Python, and C++.  

---