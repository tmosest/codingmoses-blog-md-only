---
title: LeetCode 2518. Number of Great Partitions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 2518 – Number of Great Partitions  
**LeetCode Hard | DP | Knapsack | Java | Python | C++**  

> Want to land that interview call?  
> Read the **“Good, Bad & Ugly”** of this classic hard problem, and come away with a polished, production‑ready solution in **three languages** that you can copy‑paste into your portfolio.

---

### Table of Contents  

| Section | Link |
|---------|------|
| Problem Overview | #problem-overview |
| Key Ideas | #key-ideas |
| Good | #good |
| Bad | #bad |
| Ugly | #ugly |
| Optimizations & Pitfalls | #optimizations |
| Full Code (Java) | #java |
| Full Code (Python) | #python |
| Full Code (C++) | #cpp |
| Take‑away for Job Interviews | #job-takeaway |

---

## Problem Overview <a name="problem-overview"></a>

You’re given an array `nums` of positive integers and an integer `k`.  
A **partition** splits the array into two *ordered* groups, every element goes to exactly one group.  
A partition is **great** if **both** groups have a sum ≥ k.

**Return** the number of distinct great partitions modulo `1 000 000 007`.

| Input | Output |
|-------|--------|
| `nums = [1,2,3,4]`, `k = 4` | `6` |
| `nums = [3,3,3]`, `k = 4` | `0` |
| `nums = [6,6]`, `k = 2` | `2` |

**Constraints**

* `1 ≤ nums.length, k ≤ 1000`
* `1 ≤ nums[i] ≤ 10⁹`

> **Note** – The sum of all numbers can be up to `10⁹ × 1000`, far beyond any array‑size that can be stored.  
> We only need to track sums **below** `k` (≤ 1000), which makes the problem tractable.

---

## Key Ideas <a name="key-ideas"></a>

1. **Total partitions** – Each element has 2 choices ⇒ `2ⁿ` total partitions (modular exponentiation).
2. **Invalid partitions** – Count partitions where **at least one** group’s sum is `< k` and subtract them.
3. **Knapsack DP for small sums** –  
   `dp[s]` = number of ways to choose a subset whose sum is exactly `s` (for `0 ≤ s < k`).  
   Classic 0/1 knapsack, O(`n k`) time, O(`k`) space.
4. **Inclusion–Exclusion** –  
   *If subset sum `s < k` and the complement sum `total - s ≥ k`,* we subtract `dp[s]`.  
   *If both `s` and `total - s` are `< k`,* the same partition is subtracted twice, so we add it back once.

---

## The Good <a name="good"></a>

| Good Practice | Why it matters |
|---------------|----------------|
| **Dynamic programming only up to `k-1`** | `k` ≤ 1000 → tiny memory (≈ 8 kB). |
| **Modular exponentiation** | Avoids integer overflow for `2ⁿ`. |
| **Explicit handling of `total < 2k`** | Immediate answer `0`; saves work. |
| **Clear separation of phases** | DP phase → counting phase → final mod. |
| **Test‑coverage** | Edge cases (`k=1`, `k=1000`, single element arrays). |

---

## The Bad <a name="bad"></a>

| Common Mistake | What goes wrong |
|----------------|-----------------|
| Enumerating all `2ⁿ` partitions | `n` can be 1000 ⇒ 10³⁰⁰⁰ possibilities. |
| Using 64‑bit integers for DP sums | Sums can reach `10¹²`; but we only track `≤ 1000`. |
| Forgetting to use modulo when adding `dp` values | Overflow in languages like C++ (`long long` still safe, but modulo is a contract). |
| Not handling the complement sum in inclusion‑exclusion | Wrong answer for cases where **both** groups are `< k`. |
| Not pruning `dp` updates when `value ≥ k` | Unnecessary inner loops; small slowdown but harmless. |

---

## The Ugly <a name="ugly"></a>

| Ugly Situations | Tips to tame them |
|-----------------|-------------------|
| **Huge input values (`10⁹`)** – they do *not* affect DP for `s < k`, so we can skip them. | Skip DP updates if `value ≥ k`. |
| **Very small `k` (e.g., `k = 1`)** – all partitions are great. | `total < 2k` check handles this automatically. |
| **Modulo with negative numbers** – Some languages (C++/Python) keep negatives after subtraction. | Always add `MOD` before `%` to keep the result positive. |
| **Power‑mod with large exponent (`n = 1000`)** – iterative binary exponentiation needed. | `powMod(2, n)` or `fastPower` to keep complexity O(log n). |

---

## Optimizations & Pitfalls <a name="optimizations"></a>

| Optimization | Effect |
|--------------|--------|
| **Skip DP for `value ≥ k`** | Inner loop runs fewer times, but correctness is unchanged because such values can’t contribute to sums `< k`. |
| **Pre‑compute powers of 2** | If you have to answer multiple queries with the same `n`, store `pow2[i]` for all `i ≤ 1000`. |
| **Avoid `<< 1` on `dp[i]` in C++ if `dp[i]` may be `≥ MOD/2`** | Use `(dp[i] * 2) % MOD`. |
| **Use `vector<uint64_t>` or `array<long long, 1001>` in C++** | Keeps the DP small and cache friendly. |

**Pitfall** – Remember that `total` can be **`long`** (`int64`) in Java or `long long` in C++.  
Never cast it to `int` before the `total < 2k` test.

---

## Full Implementation <a name="full-code"></a>

Below you’ll find clean, production‑ready code for **Java, Python, and C++**.  
Copy the snippet into your IDE and paste it into your LeetCode solution.

> **Remember:** All solutions use the same core algorithm; only syntax changes.  
> The comments explain every line.

---

### Java 17 <a name="java"></a>

```java
/**
 * LeetCode 2518 – Number of Great Partitions
 * Java 17 – DP + Knapsack + Inclusion–Exclusion
 */
public class Solution {
    private static final long MOD = 1_000_000_007L;

    public int countPartitions(int[] nums, int k) {
        long total = 0;
        for (int v : nums) total += v;
        if (total < 2L * k) return 0;                 // quick exit

        long[] dp = new long[k];
        dp[0] = 1;                                    // empty subset

        for (int v : nums) {
            if (v >= k) continue;                    // cannot be part of a sum < k
            for (int s = k - 1 - v; s >= 0; --s) {
                dp[s + v] = (dp[s + v] + dp[s]) % MOD;
            }
        }

        long result = powMod(2L, nums.length, MOD);   // all partitions

        for (int s = 0; s < k; ++s) {
            if (total - s < k) {                     // both sides < k
                result = (result - dp[s] + MOD) % MOD;
            } else {                                 // only left side < k
                result = (result - (dp[s] << 1) % MOD + MOD) % MOD;
            }
        }
        return (int) result;
    }

    /** fast modular exponentiation (2^exp % mod) */
    private long powMod(long base, int exp, long mod) {
        long res = 1;
        while (exp > 0) {
            if ((exp & 1) == 1) res = (res * base) % mod;
            base = (base * base) % mod;
            exp >>= 1;
        }
        return res;
    }
}
```

---

### Python 3 <a name="python"></a>

```python
"""
LeetCode 2518 – Number of Great Partitions
Python 3 – DP + Inclusion–Exclusion
"""

MOD = 10 ** 9 + 7

def countPartitions(nums, k):
    total = sum(nums)
    if total < 2 * k:
        return 0

    # dp[s] = number of subsets with sum == s  (0 <= s < k)
    dp = [0] * k
    dp[0] = 1

    for val in nums:
        if val >= k:          # cannot contribute to sums < k
            continue
        for s in range(k - 1 - val, -1, -1):
            dp[s + val] = (dp[s + val] + dp[s]) % MOD

    # total partitions = 2^n  (mod)
    result = pow(2, len(nums), MOD)

    for s in range(k):
        if total - s < k:    # complement >= k
            result = (result - dp[s]) % MOD
        else:                # both sides are < k – subtract twice
            result = (result - 2 * dp[s]) % MOD

    return result % MOD
```

**Usage**

```python
print(countPartitions([1, 2, 3, 4], 4))   # 6
print(countPartitions([3, 3, 3], 4))      # 0
print(countPartitions([6, 6], 2))         # 2
```

---

### C++17 <a name="cpp"></a>

```cpp
/**
 * LeetCode 2518 – Number of Great Partitions
 * C++17 – DP + Knapsack + Inclusion–Exclusion
 */
#include <bits/stdc++.h>
using namespace std;

class Solution {
private:
    static constexpr long long MOD = 1'000'000'007LL;

    long long mod_pow(long long base, int exp) const {
        long long res = 1;
        while (exp > 0) {
            if (exp & 1) res = (res * base) % MOD;
            base = (base * base) % MOD;
            exp >>= 1;
        }
        return res;
    }

public:
    int countPartitions(vector<int>& nums, int k) {
        long long total = 0;
        for (int v : nums) total += v;
        if (total < 2LL * k) return 0;               // quick reject

        vector<long long> dp(k, 0);
        dp[0] = 1;                                   // empty set

        for (int v : nums) {
            if (v >= k) continue;                    // cannot reach sums < k
            for (int s = k - 1 - v; s >= 0; --s) {
                dp[s + v] = (dp[s + v] + dp[s]) % MOD;
            }
        }

        long long result = mod_pow(2LL, static_cast<int>(nums.size()));

        for (int s = 0; s < k; ++s) {
            if (total - s < k) {                     // complement >= k
                result = (result - dp[s] + MOD) % MOD;
            } else {                                 // both < k – subtract twice
                result = (result - (dp[s] * 2 % MOD) + MOD) % MOD;
            }
        }
        return static_cast<int>(result);
    }
};
```

---

## Optimizations & Pitfalls <a name="optimizations"></a>

| Optimization | How to Implement |
|--------------|------------------|
| **Skip DP updates for `value >= k`** | `if (v >= k) continue;` |
| **Avoid `dp[i] << 1` overflow in C++** | Use `(dp[i] * 2) % MOD`. |
| **Use `unsigned long long` for 2ⁿ** | Keep everything under `MOD` with `mod_pow`. |
| **Batch modulo after each addition** | Prevents intermediate negative values. |
| **Iterate backwards in DP** | Guarantees 0/1 knapsack property. |

---

## Take‑away for Job Interviews <a name="job-takeaway"></a>

1. **Show you understand the problem constraints** – `k ≤ 1000` is the *key* that turns a seemingly impossible 2ⁿ enumeration into an O(`n k`) DP.
2. **Explain inclusion–exclusion clearly** – interviewers love a concise, mathematically rigorous reasoning.
3. **Provide clean, modular code** – separate helper functions (`powMod`, `mod_pow`) so the main logic stays readable.
4. **Mention edge cases** – “what if `total < 2k`?” – demonstrates defensive programming.
5. **Mention time/space complexity** – O(`n k`) time, O(`k`) space.  
   Interviewers often ask: *“Could you optimize further?”* – you can say “if all numbers are ≥ k, the DP is trivial; otherwise, we skip unnecessary updates.”
6. **Be prepared to convert to any language** – the algorithm is language‑agnostic; the snippets above show this adaptability.

> **Pro tip:** When you submit on LeetCode, add a comment at the top:  
> `// Problem: 2518. Number of Great Partitions – DP + Knapsack + Inclusion–Exclusion`.  
> That signals the exact approach and makes your solution stand out.

---

Happy coding, and good luck on your next interview! 🚀

---