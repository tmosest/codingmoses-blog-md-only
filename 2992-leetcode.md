---
title: LeetCode 2992. Number of Self-Divisible Permutations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🔍 LeetCode 2992 – *Number of Self‑Divisible Permutations*  
**Languages**: Java, Python, C++  
**Difficulty**: Medium  
**Max n**: 12  

> **SEO Tags**:  
> `LeetCode 2992`, `self divisible permutations`, `dynamic programming bitmask`, `Java solution`, `Python solution`, `C++ solution`, `job interview coding`, `algorithms interview`, `bitmask DP`

---

### 📌 Problem Statement (Short)

Given an integer `n`, count how many permutations of the 1‑indexed array `nums = [1, 2, …, n]` satisfy  
`gcd(nums[i], i) == 1` for every `1 ≤ i ≤ n`.  
The array length is at most **12**.

---

### 💡 Intuition

The requirement is a local one: when we decide the element that goes into position `i`, we only need to check whether it is coprime with `i`.  
This suggests a *state‑by‑state* dynamic programming where the state records which numbers have already been placed.

Because `n ≤ 12`, a 12‑bit mask (or 64‑bit integer in C++) can encode the set of used numbers efficiently.  
Let `mask` be such that bit `j‑1` is `1` iff the number `j` has already been placed.  
If `mask` has `k` bits set, then the next free position is `k + 1`.

From state `mask`, we try every unused number `x` (`1 … n`).  
If `x` is coprime with the new position `k+1`, we can place it and transition to `mask | (1 << (x-1))`.  
The DP value `dp[mask]` is the number of valid ways to fill exactly the positions described by `mask`.

---

### 🛠️ Algorithm (Bitmask DP)

1. `S = 1 << n` – total number of masks.  
2. `dp[0] = 1` – empty arrangement is one way.  
3. For each `mask` from `0` to `S-1`:
   * `k = popcount(mask)` – numbers already placed.  
   * `pos = k + 1` – index of the next position to fill.  
   * For each number `x` (`1 … n`):
     * if bit `x-1` is **unset** in `mask` **and** `gcd(x, pos) == 1`  
       then `dp[mask | (1 << (x-1))] += dp[mask]`.
4. Answer is `dp[S-1]` – all numbers used.

> **Time Complexity**: `O(2^n * n)`  
> **Space Complexity**: `O(2^n)`  
> With `n ≤ 12`, this is perfectly fine (`2^12 * 12 ≈ 49k` operations).

---

## 📑 Code Implementations

Below you’ll find clean, ready‑to‑paste solutions in **Java**, **Python**, and **C++**.  
All three share the same logic and are heavily commented for clarity.

---

### Java 17

```java
import java.util.*;

class Solution {
    public int selfDivisiblePermutationCount(int n) {
        int totalMask = 1 << n;          // 2^n
        int[] dp = new int[totalMask];
        dp[0] = 1;                       // empty permutation

        for (int mask = 0; mask < totalMask; mask++) {
            if (dp[mask] == 0) continue; // no ways to reach this state

            int placed = Integer.bitCount(mask);   // numbers already used
            int nextPos = placed + 1;              // next index to fill

            for (int x = 1; x <= n; x++) {
                // skip if x already used
                if ((mask & (1 << (x - 1))) != 0) continue;

                // coprime check
                if (gcd(x, nextPos) == 1) {
                    int newMask = mask | (1 << (x - 1));
                    dp[newMask] += dp[mask];
                }
            }
        }
        return dp[totalMask - 1];
    }

    /** Euclid's algorithm */
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

### Python 3

```python
class Solution:
    def selfDivisiblePermutationCount(self, n: int) -> int:
        total_mask = 1 << n            # 2^n
        dp = [0] * total_mask
        dp[0] = 1

        for mask in range(total_mask):
            if dp[mask] == 0:
                continue

            placed = bin(mask).count("1")  # numbers already used
            pos = placed + 1              # next index

            for x in range(1, n + 1):
                if mask & (1 << (x - 1)):
                    continue                 # already used

                if self._gcd(x, pos) == 1:
                    new_mask = mask | (1 << (x - 1))
                    dp[new_mask] += dp[mask]

        return dp[total_mask - 1]

    @staticmethod
    def _gcd(a: int, b: int) -> int:
        while b:
            a, b = b, a % b
        return a
```

---

### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int selfDivisiblePermutationCount(int n) {
        int totalMask = 1 << n;              // 2^n
        vector<int> dp(totalMask, 0);
        dp[0] = 1;                           // empty arrangement

        for (int mask = 0; mask < totalMask; ++mask) {
            if (!dp[mask]) continue;

            int placed = __builtin_popcount(mask);
            int pos = placed + 1;            // next index

            for (int x = 1; x <= n; ++x) {
                if (mask & (1 << (x - 1))) continue;   // already used

                if (gcd(x, pos) == 1) {
                    int newMask = mask | (1 << (x - 1));
                    dp[newMask] += dp[mask];
                }
            }
        }
        return dp[totalMask - 1];
    }

private:
    int gcd(int a, int b) {
        while (b) {
            int t = a % b;
            a = b;
            b = t;
        }
        return a;
    }
};
```

---

## 🔍 Good, Bad & Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithm** | Bitmask DP uses all 2ⁿ states once → optimal for n ≤ 12 | Time complexity `O(2ⁿ·n)` grows exponentially, but n is tiny | None – algorithm is clean |
| **Implementation** | Simple loop over masks + bit operations | Need careful indexing (bit `x-1`) | Forgetting `gcd` check can produce wrong answer |
| **Backtracking Alternative** | Illustrative, easy to code | Exponential `O(n·n!)` – far slower for n=12 | Requires pruning to be usable |
| **Readability** | Self‑documenting with comments | May look dense to newcomers | N/A |
| **Space** | O(2ⁿ) int array | Still acceptable (≈ 16 KB) | None |

---

## 📈 Why This Is a Great Interview Talking Point

1. **State Compression** – Using a bitmask to encode which numbers are used shows mastery of combinatorial DP.  
2. **Problem‑specific Insight** – Recognizing that the next position depends only on how many numbers have already been placed.  
3. **Efficient GCD Check** – Euclid’s algorithm is standard; you can mention the potential to cache results if needed.  
4. **Scalability Discussion** – You can explain why this approach works up to `n = 12` but would break for larger `n`, and what alternatives (e.g., inclusion‑exclusion) you might consider.

---

## 📚 Take‑away Checklist

- ✅ Use a bitmask DP for combinatorial constraints with `n ≤ 20`.  
- ✅ Count used numbers with `popcount` (`__builtin_popcount`, `Integer.bitCount`, or `bin(mask).count`).  
- ✅ Transition to the *next* position (`k+1`) only.  
- ✅ Verify coprimality with `gcd`.  
- ✅ Return `dp[(1<<n)-1]`.  

---

## 🚀 Ready to impress recruiters?

Use the above Java / Python / C++ snippets as your “show‑off” code.  
Explain the intuition and talk through the good/bad/ugly table in a next‑level interview – you’ll nail that LeetCode 2992 question and demonstrate the kind of algorithmic thinking recruiters love!