---
title: LeetCode 3077. Maximum Strength of K Disjoint Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap (LeetCode 3077)

> **Maximum Strength of K Disjoint Subarrays**  
> **Difficulty:** Hard  
> **Tags:** Dynamic Programming, Prefix Sum, Knapsack  

You’re given an integer array `nums` (length `n`) and an odd integer `k`.  
Choose exactly `k` disjoint sub‑arrays  
`sub1, sub2, … , subk` such that the last index of `subi` is strictly before the first index of `subi+1`.  
The **strength** of a chosen set is  

```
strength = k * sum(sub1) – (k‑1) * sum(sub2) + (k‑2) * sum(sub3) – … – 2 * sum(sub{k‑1}) + sum(subk)
```

Return the maximum possible strength.  
`1 ≤ n ≤ 10⁴`, `|nums[i]| ≤ 10⁹`, `1 ≤ k ≤ n`, `k` is odd, and `n·k ≤ 10⁶`.

The problem is a classic dynamic‑programming problem with a “weighted alternating sum” twist.



---

## 2.  Core Idea

*The problem can be rewritten as a DP over prefix sums.*

For every prefix `0 … j` we keep the best possible strength for the first `i` sub‑arrays that end **inside** that prefix.  
Let  

```
dp[i][j] = maximum strength that can be achieved by picking i disjoint sub‑arrays from nums[0 … j-1]
```

Transition for the `i‑th` sub‑array:

```
dp[i][j] = max( dp[i][j-1] ,          // do not finish a subarray at j-1
                max_{t<i-1 … j-1} ( dp[i-1][t] + weight(i) * (pref[j] – pref[t]) ) )
```

where  
`pref[x]` is the prefix sum `nums[0] + … + nums[x-1]` and  

```
weight(i) = (k - i + 1) * (-1)^(i+1)
```

Because `k` is odd, the weights alternate `+k, -(k‑1), +k‑2, … , +1`.  
The inner maximisation can be maintained in *O(1)* per `j` by keeping the best value of  

```
dp[i-1][t] - weight(i) * pref[t]
```

while iterating over `j`.  
Overall complexity: `O(k·n)` time and `O(n)` extra space (we keep only two rows).

---

## 3.  Code

Below are clean, idiomatic implementations in **Java, Python, and C++**.  
Each file is self‑contained and ready for copy‑paste into a LeetCode solution.

> ⚠️  All solutions assume 0‑based indexing for `nums` and use long (64‑bit) arithmetic to avoid overflow.

---

### 3.1  Java

```java
import java.util.*;

class Solution {
    public long maximumStrength(int[] nums, int k) {
        int n = nums.length;
        long[] pref = new long[n + 1];
        for (int i = 0; i < n; i++) pref[i + 1] = pref[i] + nums[i];

        long[] prev = new long[n + 1];   // dp[i-1][*]
        long[] cur  = new long[n + 1];   // dp[i][*]

        for (int i = 1; i <= k; i++) {
            long weight = (k - i + 1) * ((i & 1) == 1 ? 1 : -1); // (+,-,+,-,…)
            long best = Long.MIN_VALUE;       // best of (dp[i-1][t] - weight*pref[t])
            for (int j = 1; j <= n; j++) {
                // update best for the current j (t = j-1)
                best = Math.max(best, prev[j - 1] - weight * pref[j - 1]);

                // option 1: do not end a subarray at j-1
                long notEnd = cur[j - 1];

                // option 2: end a subarray at j-1
                long endHere = best + weight * pref[j];

                cur[j] = Math.max(notEnd, endHere);
            }
            // swap rows for next i
            long[] tmp = prev; prev = cur; cur = tmp;
        }
        return prev[n];
    }
}
```

---

### 3.2  Python

```python
class Solution:
    def maximumStrength(self, nums: List[int], k: int) -> int:
        n = len(nums)
        pref = [0] * (n + 1)
        for i, v in enumerate(nums, 1):
            pref[i] = pref[i-1] + v

        prev = [0] * (n + 1)
        cur  = [0] * (n + 1)

        for i in range(1, k + 1):
            weight = (k - i + 1) * (1 if i & 1 else -1)
            best = float('-inf')
            for j in range(1, n + 1):
                # update best for t = j-1
                best = max(best, prev[j-1] - weight * pref[j-1])

                # keep previous state
                not_end = cur[j-1]
                # finish a subarray at j-1
                end_here = best + weight * pref[j]

                cur[j] = max(not_end, end_here)
            prev, cur = cur, prev  # reuse arrays
        return prev[n]
```

---

### 3.3  C++

```cpp
class Solution {
public:
    long long maximumStrength(vector<int>& nums, int k) {
        int n = nums.size();
        vector<long long> pref(n + 1, 0);
        for (int i = 0; i < n; ++i) pref[i+1] = pref[i] + nums[i];

        vector<long long> prev(n + 1, 0), cur(n + 1, 0);

        for (int i = 1; i <= k; ++i) {
            long long weight = (long long)(k - i + 1) * ((i & 1) ? 1 : -1);
            long long best = LLONG_MIN;
            for (int j = 1; j <= n; ++j) {
                // update best for t = j-1
                best = max(best, prev[j-1] - weight * pref[j-1]);

                long long notEnd = cur[j-1];
                long long endHere = best + weight * pref[j];

                cur[j] = max(notEnd, endHere);
            }
            swap(prev, cur);
        }
        return prev[n];
    }
};
```

All three solutions run in **O(k · n)** time and **O(n)** extra space – well below the limits (`n·k ≤ 10⁶`).

---

## 4.  Blog Article: “The Good, the Bad, and the Ugly of LeetCode 3077”

> **Title:**  
> *Maximum Strength of K Disjoint Subarrays – The Good, the Bad, and the Ugly (A DP Masterclass)*  
> **Meta Description:**  
> Master LeetCode 3077 with a dynamic‑programming walkthrough, practical code in Java, Python & C++, and interview‑ready insights.  
> **Keywords:** LeetCode 3077, maximum strength of k disjoint subarrays, dynamic programming, interview coding, job interview, algorithm design

---

### 4.1  Introduction

LeetCode 3077 asks you to pick *exactly* `k` disjoint sub‑arrays from an integer array and maximise a *weighted alternating sum*.  
At first glance, it feels like a variant of the classic “maximum sum of `k` disjoint sub‑arrays” problem, but the sign pattern (+, –, +, – …) throws a wrench into standard solutions.

If you’re preparing for a data‑structure or algorithm interview, mastering this problem demonstrates:

* **DP skill** – you can formulate a multi‑dimensional DP with non‑trivial state transitions.  
* **Space optimisation** – you can reduce `O(k·n)` to `O(n)` by re‑using rows.  
* **Attention to detail** – you correctly handle negative numbers and large inputs.

Let’s dissect the problem into three parts:

| Part | What you’re learning | Why it matters |
|------|---------------------|----------------|
| **The Good** | Clean DP formulation + time/space complexity | Shows you can reason about optimal sub‑structures |
| **The Bad** | Common pitfalls (off‑by‑one, sign errors, overflow) | Highlights what *not* to do during an interview |
| **The Ugly** | Deep reasoning about the weight pattern & prefix optimisation | Reveals the deeper algorithmic insight |

---

### 4.2  The Good – A Clean DP

#### 4.2.1  State

`dp[i][j]` = best strength using **first `i` sub‑arrays** within the prefix `nums[0…j-1]`.

- `i` ranges 0…k.  
- `j` ranges 0…n.  
- Base case: `dp[0][*] = 0` (no sub‑array → strength 0).

#### 4.2.2  Transition

For the `i`‑th sub‑array, its weight is  

```
weight(i) = (k - i + 1) * (-1)^(i+1)
```

If we finish the `i`‑th sub‑array at index `j-1`, the sub‑array sum is `pref[j] - pref[t]` for some `t < j`.  
Thus:

```
dp[i][j] = max(
            dp[i][j-1],                                 // skip j-1
            max_{t < j} ( dp[i-1][t] + weight(i) * (pref[j] - pref[t]) )
          )
```

The inner maximum looks at *all* possible starting points of the last sub‑array.  
Without optimisation this would be `O(n)` per cell, giving `O(k·n²)` – too slow.

#### 4.2.3  Prefix Trick

Define

```
best = max_{t < j} ( dp[i-1][t] - weight(i) * pref[t] )
```

Then

```
dp[i][j] = max( dp[i][j-1] , best + weight(i) * pref[j] )
```

During the scan over `j`, we keep `best` updated in `O(1)` time, yielding overall `O(k·n)`.

#### 4.2.4  Space Reduction

Only `dp[i-1][*]` and `dp[i][*]` are needed simultaneously.  
We keep two 1‑D arrays `prev` and `cur`, swapping them after each `i`.  
Space shrinks to `O(n)` – vital when `k` can be a few thousand.

---

### 4.3  The Bad – Common Pitfalls

1. **Off‑by‑one in prefix sums**  
   - Mistakenly use `pref[j]` instead of `pref[j-1]` in the transition.  
   - Fix: Remember `pref[x]` is sum of the first `x` elements.

2. **Weight sign confusion**  
   - Since `k` is odd, weights alternate starting with `+k`.  
   - A sign error propagates through the DP, giving an absurdly large negative answer.

3. **Integer overflow**  
   - Each `nums[i]` can be 10⁹ and there can be up to 10⁴ elements → prefix sums up to 10¹³.  
   - Use `long`/`long long` (64‑bit) instead of `int`.

4. **Wrong base case**  
   - Forgetting `dp[0][j] = 0` leads to `Long.MIN_VALUE` propagation and the algorithm crashing.

5. **Not re‑using arrays**  
   - Some solutions allocate `k`×`n` matrices and exceed memory (even if `n·k ≤ 10⁶` the constant factor matters in Java).

6. **Mis‑interpreting “disjoint”**  
   - Sub‑arrays must be *non‑overlapping*; you cannot skip an index inside a chosen sub‑array.

---

### 4.4  The Ugly – Weight Pattern Deep Dive

The weight pattern `(+k, -(k-1), +k-2, …, +1)` is what turns this into an “ugly” problem.

#### 4.4.1  Why It Matters

If all weights were `+1`, the DP reduces to the well‑known “max sum of `k` disjoint sub‑arrays” – you can solve it with a simple recurrence.  
The alternating signs mean the contribution of a sub‑array can *subtract* from the total if its index is even (relative to the chosen order).  

#### 4.4.2  Optimisation Insight

Consider the inner maximisation:

```
max_{t} ( dp[i-1][t] + weight * (pref[j] – pref[t]) )
```

Expanding:

```
= weight * pref[j] + max_{t} ( dp[i-1][t] - weight * pref[t] )
```

The term inside the `max` is *independent* of `j` after we fix `t`.  
Therefore, while scanning `j` left to right, we can maintain the best value of  

```
dp[i-1][t] - weight * pref[t]
```

in a running variable `best`.  
Each new `j` just needs `prev[j-1] - weight * pref[j-1]` to update `best`.  
That’s the “ugly” algebraic trick that turns the DP into *linear* time per `i`.

---

### 4.5  Interview‑Ready Tips

| Tip | Example Code Snippet |
|-----|----------------------|
| **Explain your DP diagram** – sketch `dp` table on paper. | `dp[i][j]` – first `i` sub‑arrays inside `0…j-1`. |
| **Walk through a small example** (e.g., `nums = [1,-2,3,4]`, `k=3`). | Show how `best` updates each step. |
| **Show overflow handling** – use `long`. | `long weight = (k - i + 1) * ((i & 1) == 1 ? 1 : -1);` |
| **Space optimise** – mention row swapping. | `swap(prev, cur);` |
| **Explain why k is odd** – ensures final weight is `+1`. | `weight(i)` formula. |

---

### 4.6  Conclusion

LeetCode 3077 is a beautiful blend of *classic DP* and *non‑trivial optimisation*.  
You can solve it cleanly, avoid common interview traps, and, most importantly, show interviewers that you can *see* the underlying structure and translate it into code.

> *“Solve the problem, then ask: what would a follow‑up interview question look like?”*  
> That curiosity keeps you ahead of the curve.



---

## 5.  Final Thoughts

The DP solution above is both **efficient** and **extensible**.  
If you’re unsure how to approach it at the interview, try building the DP table by hand for a small input – you’ll see the pattern of “best so far” emerge naturally.

Happy coding, and good luck landing that job! 🚀



---



### 5.1  References

* LeetCode 3077 – Maximum Strength of K Disjoint Subarrays  
* “Maximum Sum of `k` Non‑Overlapping Subarrays” – classic LeetCode 689  
* Competitive Programming 3 – dynamic‑programming techniques



---



*Feel free to comment below if you’d like a deeper dive into any of the DP optimisations or interview strategies!*