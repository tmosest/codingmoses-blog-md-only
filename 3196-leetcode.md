---
title: LeetCode 3196. Maximize Total Cost of Alternating Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3196. Maximize Total Cost of Alternating Subarrays  
**Medium | Public | LeetCode**

---

## 🚀 TL;DR  

- **Goal:** Split the array into contiguous subarrays so that the sum of  
  `nums[l] – nums[l+1] + … + nums[r]` over every subarray is maximized.  
- **Resulting DP:**  
  ```text
  dp[i] = max( s[i] + bestOdd , -s[i] + bestEven )
  ```
  where `s[i]` is the alternating prefix sum and  
  `bestOdd = max_{j odd} (dp[j] – s[j])`,  
  `bestEven = max_{j even} (dp[j] + s[j])`.  
- **Complexity:** `O(n)` time, `O(1)` memory.  
- **Languages:** Java, Python, C++ (all use 64‑bit integers).

---

## 📚 Problem Statement (Simplified)

You’re given an integer array `nums` of length `n`.

```
cost(l, r) = nums[l] - nums[l+1] + nums[l+2] - … + nums[r] * (-1)^(r-l)
```

Split the array into one or more contiguous subarrays such that the total cost is maximized.  
Return the maximum total cost.

---

## 🧩 Key Insight  

The cost of a subarray depends only on the parity of its starting index.  
By pre‑computing the **alternating prefix sum** `s[i] = Σ_{k=0..i} nums[k] * (-1)^k`, the cost of any subarray can be expressed as:

```
cost(l, r) = (-1)^l * ( s[r] – s[l-1] )
```

This allows us to formulate a 1‑dimensional DP that only needs to keep two running maximums (`bestOdd`, `bestEven`) instead of scanning all possible split points.

---

## 🛠️ Solution Outline

1. **Prefix alternation**  
   ```text
   s[i] = s[i-1] + nums[i] * (-1)^i
   ```
2. **DP transition**  
   For each index `i` (ending position of the last subarray):
   ```text
   dp[i] = max( s[i] + bestOdd , -s[i] + bestEven )
   ```
   * `bestOdd`   – maximum value of `dp[j] – s[j]` for odd `j` (including `j = -1`).
   * `bestEven`  – maximum value of `dp[j] + s[j]` for even `j`.
3. **Update running maxima**  
   After computing `dp[i]`, update the corresponding parity:
   ```text
   if i is even: bestEven = max(bestEven, dp[i] + s[i])
   else:         bestOdd  = max(bestOdd,  dp[i] - s[i])
   ```
4. **Answer**  
   `dp[n-1]` is the maximum total cost.

---

## 📦 Code Implementations

### Java

```java
import java.util.*;

public class Solution {
    public long maximumTotalCost(int[] nums) {
        int n = nums.length;
        long[] s = new long[n];
        long bestOdd = 0;   // dp[-1] - s[-1] = 0
        long bestEven = Long.MIN_VALUE; // no even j yet

        for (int i = 0; i < n; i++) {
            long val = nums[i];
            long sign = (i & 1) == 0 ? 1 : -1;
            s[i] = (i == 0 ? 0 : s[i-1]) + val * sign;

            long option1 = s[i] + bestOdd;          // last subarray starts at odd index
            long option2 = -s[i] + bestEven;       // last subarray starts at even index
            long dp = Math.max(option1, option2);

            // Update the best values for future indices
            if ((i & 1) == 0) { // even
                bestEven = Math.max(bestEven, dp + s[i]);
            } else {            // odd
                bestOdd = Math.max(bestOdd, dp - s[i]);
            }
        }
        return s[n-1] + bestOdd; // dp[n-1] = max( s[n-1]+bestOdd , -s[n-1]+bestEven )
    }
}
```

### Python

```python
class Solution:
    def maximumTotalCost(self, nums: List[int]) -> int:
        n = len(nums)
        s = [0] * n
        best_odd = 0          # dp[-1] - s[-1] = 0
        best_even = -10**18   # effectively -∞

        for i, v in enumerate(nums):
            sign = 1 if i % 2 == 0 else -1
            s[i] = (s[i-1] if i else 0) + v * sign

            opt1 = s[i] + best_odd
            opt2 = -s[i] + best_even
            dp = max(opt1, opt2)

            if i % 2 == 0:          # even index
                best_even = max(best_even, dp + s[i])
            else:                   # odd index
                best_odd = max(best_odd, dp - s[i])

        return s[-1] + best_odd  # dp[n-1]
```

### C++

```cpp
class Solution {
public:
    long long maximumTotalCost(vector<int>& nums) {
        int n = nums.size();
        vector<long long> s(n);
        long long bestOdd = 0;               // dp[-1] - s[-1]
        long long bestEven = LLONG_MIN / 2;   // -∞

        for (int i = 0; i < n; ++i) {
            long long sign = (i % 2 == 0) ? 1 : -1;
            s[i] = (i ? s[i-1] : 0) + 1LL * nums[i] * sign;

            long long opt1 = s[i] + bestOdd;          // start at odd index
            long long opt2 = -s[i] + bestEven;        // start at even index
            long long dp = max(opt1, opt2);

            if (i % 2 == 0)   // even j
                bestEven = max(bestEven, dp + s[i]);
            else              // odd j
                bestOdd  = max(bestOdd, dp - s[i]);
        }
        return s[n-1] + bestOdd;   // dp[n-1]
    }
};
```

---

## 📈 Why This Works (Proof Sketch)

- **Cost formula**  
  `cost(l, r) = (-1)^l * (s[r] – s[l-1])`.  
  The sign `(-1)^l` only depends on the *parity* of `l`.

- **DP rewrite**  
  Let `i` be the end of the last subarray.  
  `dp[i] = max_{j<i} dp[j] + cost(j+1, i)`.  
  Substituting the cost expression gives two separate linear expressions in `s[i]` depending on the parity of `j`.  
  Hence the maximum over all `j` reduces to the maximum of two running values (`bestOdd`, `bestEven`).

- **Running maxima**  
  The updates `bestEven = max(bestEven, dp[i] + s[i])` (even `i`) and  
  `bestOdd  = max(bestOdd , dp[i] - s[i])` (odd  `i`) keep the best possible
  contributions for future transitions without any loops.

---

## 📌 Edge Cases & Pitfalls (Good / Bad / Ugly)

| Situation | What to Watch | Recommendation |
|-----------|---------------|----------------|
| **Large sums** | `nums[i]` up to ±1 000 000 000, `n` up to 100 000 → total can reach ~10¹⁴ | Use `long` in Java, `long long` in C++, `int` is fine in Python (unbounded). |
| **Initial `bestEven`** | There is *no* even index `j` before the first element. | Start with `-∞` (e.g., `Long.MIN_VALUE/2`). |
| **Index parity** | In Java & C++, `-1` is odd, so `bestOdd` starts at 0, covering the case where the first subarray starts at index 0. | Explicitly set `bestOdd = 0` and skip `bestEven` at the start. |
| **Overflow in languages with fixed width** | The intermediate value `dp[i] + s[i]` may overflow 32‑bit. | Always cast to 64‑bit (`long` / `long long`). |
| **Python** | No fixed‑width overflow; just be careful with negative infinity (`-10**18` is safe). | Use `-10**18` or `float('-inf')` if you prefer. |
| **Corner case** | Empty array is not allowed (`n ≥ 1`). | Code naturally handles it because we always compute `dp[0]`. |

---

## 🎯 Interview‑Ready Summary

1. **Understand parity** – the sign flips with every element, so the *starting* index decides whether we add or subtract the alternating prefix sum.  
2. **Derive an O(n) DP** – keep two running maximums; the transition becomes a simple `max` of two linear functions.  
3. **Watch the data type** – 64‑bit integers are mandatory for Java/C++.  
4. **Implement cleanly** – the algorithm is only ~30 lines per language and uses no extra arrays beyond the prefix sum.

---

## 📢 SEO‑Boosted Takeaway

If you’re prepping for a **coding interview** or looking for an elegant **LeetCode 3196** solution, this post covers everything you need:

- **Keywords**: LeetCode 3196, Maximize Total Cost of Alternating Subarrays, Dynamic Programming, Interview Preparation, Java, Python, C++
- **Benefits**:  
  *One‑pass, linear‑time algorithm* that can be pasted into any interview platform.  
  The same idea applies to any problem where the cost of a segment depends only on its starting parity.

---

### 🎓 Suggested Practice Problems

| Problem | Difficulty | Link |
|---------|------------|------|
| **Maximum Subarray Sum with Alternating Sign** | Medium | [LeetCode 1180](https://leetcode.com/problems/maximum-subarray-sum-with-alternating-sign/) |
| **Maximum Sum of Two Non‑Overlapping Subarrays** | Hard | [LeetCode 689](https://leetcode.com/problems/maximum-sum-of-two-non-overlapping-subarrays/) |
| **Maximum Sum of 3 Non‑Overlapping Subarrays** | Medium | [LeetCode 689](https://leetcode.com/problems/maximum-sum-of-3-non-overlapping-subarrays/) |

---

## 🔧 Final Thought

The “ugly” part of this problem is the sign gymnastics: you must keep a tight handle on which indices are even or odd and flip the sign accordingly. Once you master that trick, the rest is a clean DP pass with two constants – **a classic interview win**.

Happy coding! 🚀