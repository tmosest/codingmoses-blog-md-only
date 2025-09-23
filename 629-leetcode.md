---
title: LeetCode 629. K Inverse Pairs Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution Code

Below you’ll find clean, ready‑to‑copy implementations in **Java**, **Python** and **C++** that solve LeetCode 629 – *K Inverse Pairs Array*.

> **Problem Recap**  
> Given `n` and `k`, count the permutations of `[1…n]` that contain exactly `k` inverse pairs.  
> Return the answer modulo `10⁹+7`.

### 1.1 Java – Rolling DP (O(n k) time, O(k) space)

```java
// 629. K Inverse Pairs Array
// Java – Rolling DP, O(n*k) time, O(k) space

public class Solution {
    private static final int MOD = 1_000_000_007;

    public int kInversePairs(int n, int k) {
        int[] dp = new int[k + 1];      // dp[j] – #ways for current i with j pairs
        dp[0] = 1;                      // base: 0 elements → 1 way (empty array)

        for (int i = 1; i <= n; i++) {
            int[] next = new int[k + 1];
            next[0] = 1;                 // only 1 permutation with 0 pairs for any i
            for (int j = 1; j <= k; j++) {
                // dp[j] – dp[j-i]   (if j-i >= 0)
                int sub = (j - i) >= 0 ? dp[j - i] : 0;
                int val = (dp[j] - sub + MOD) % MOD;   // temp value for this j
                next[j] = (next[j - 1] + val) % MOD;   // prefix sum trick
            }
            dp = next;                 // move to the next i
        }

        // dp[k] holds the sum of dp[0..k], subtract dp[k-1] to get exactly k
        int result = (dp[k] - (k > 0 ? dp[k - 1] : 0) + MOD) % MOD;
        return result;
    }
}
```

### 1.2 Python – Rolling DP (O(n k) time, O(k) space)

```python
# 629. K Inverse Pairs Array
# Python 3 – Rolling DP, O(n*k) time, O(k) space

MOD = 1_000_000_007

def k_inverse_pairs(n: int, k: int) -> int:
    dp = [0] * (k + 1)
    dp[0] = 1  # base case

    for i in range(1, n + 1):
        nxt = [0] * (k + 1)
        nxt[0] = 1
        for j in range(1, k + 1):
            sub = dp[j - i] if j - i >= 0 else 0
            val = (dp[j] - sub) % MOD
            nxt[j] = (nxt[j - 1] + val) % MOD
        dp = nxt

    # exact k pairs
    return (dp[k] - (dp[k - 1] if k > 0 else 0)) % MOD
```

### 1.3 C++ – Rolling DP (O(n k) time, O(k) space)

```cpp
// 629. K Inverse Pairs Array
// C++17 – Rolling DP, O(n*k) time, O(k) space

#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int kInversePairs(int n, int k) {
        const int MOD = 1'000'000'007;
        vector<int> dp(k + 1, 0), nxt(k + 1, 0);
        dp[0] = 1;   // base case

        for (int i = 1; i <= n; ++i) {
            nxt[0] = 1;
            for (int j = 1; j <= k; ++j) {
                int sub = (j - i >= 0) ? dp[j - i] : 0;
                int val = (dp[j] - sub + MOD) % MOD;  // temp value
                nxt[j] = (nxt[j - 1] + val) % MOD;    // prefix sum trick
            }
            dp.swap(nxt);  // move to the next i
        }

        int result = (dp[k] - (k > 0 ? dp[k - 1] : 0) + MOD) % MOD;
        return result;
    }
};
```

All three implementations run in `O(n k)` time and `O(k)` auxiliary space, which easily fits the constraints (`n, k ≤ 1000`).

---

## 2.  Blog Article – “The Good, The Bad, and the Ugly of K‑Inverse‑Pairs”

> **Keywords:**  
> `K Inverse Pairs Array`, `LeetCode 629`, `dynamic programming`, `DP optimization`, `coding interview`, `Java`, `Python`, `C++`, `job interview`, `algorithm interview`, `LeetCode Hard`

### 2.1 Introduction

If you’re preparing for a technical interview or a data‑structures‑and‑algorithms (DSA) bootcamp, you’ll run into **LeetCode 629 – K Inverse Pairs Array** quite often. It’s a *Hard* problem that forces you to think beyond naive recursion and brute force. In this post we’ll dissect the problem, walk through a **dynamic‑programming** solution, explore common pitfalls (the *bad*), and reveal an **optimized linear‑space** trick (the *good*). Finally, we’ll talk about how mastering this problem can set you apart in a hiring pipeline.

> **Read the full tutorial on the official LeetCode site:** https://leetcode.com/problems/k-inverse-pairs-array/

---

### 2.2 Problem Recap

> **Given** an integer `n` and an integer `k`, return the number of permutations of `[1 … n]` that contain **exactly `k` inverse pairs**.  
> An **inverse pair** is a pair `(i, j)` where `i < j` and `nums[i] > nums[j]`.  
> The answer must be returned modulo `1,000,000,007`.

> **Examples**  
> 1. `n = 3, k = 0` → `1` (only `[1,2,3]`)  
> 2. `n = 3, k = 1` → `2` (`[1,3,2]`, `[2,1,3]`)

---

### 2.3 The Brute‑Force “Bad” Approach

A first thought is to generate all `n!` permutations, count inverse pairs, and filter. For `n = 10` this is already astronomical (`3.6M` permutations). Even with memoization, counting inverse pairs for each permutation would be `O(n²)` per permutation – clearly infeasible for the constraints (`n, k ≤ 1000`).

> **Why it fails:**  
> - Exponential time (`O(n!)`).  
> - Space blow‑up due to storing permutations.  
> - Poor scalability: the algorithm stalls well before the official test cases.

So we need a polynomial‑time algorithm. That’s where **dynamic programming** steps in.

---

### 2.4 The DP “Good” – The Classic 2‑D Recurrence

Let `dp[i][j]` = number of permutations of the first `i` numbers (`1…i`) that have exactly `j` inverse pairs.

**Transition**

When we insert the number `i` into a permutation of `1…i-1`, the new element can land in any position `p` (1‑based). It will create `p-1` new inverse pairs because it will be larger than all elements to its left. Thus:

```
dp[i][j] = Σ_{p=1}^{i} dp[i-1][j-(p-1)]
```

Simplify by changing index: `q = p-1`, `q ∈ [0, i-1]`:

```
dp[i][j] = Σ_{q=0}^{min(i-1, j)} dp[i-1][j-q]
```

**Base Cases**

```
dp[0][0] = 1   // empty permutation
dp[0][j>0] = 0
```

**Complexity**

- Time: `O(n * k * n)` → `O(n² k)` (because the inner sum runs up to `i`).
- Space: `O(n * k)` for a full 2‑D table.

While this solves the problem, it’s still too slow for `n = k = 1000`. We need a smarter recurrence.

---

### 2.5 The “Good” – Prefix Sum Optimization (O(n k))

Notice that the transition is a **convolution** of the previous row with a sliding window of width `i`. We can avoid the inner sum by using a **prefix sum** trick:

Let’s write the recurrence as:

```
dp[i][j] = dp[i][j-1] + dp[i-1][j] - dp[i-1][j-i]
```

Explanation:

- `dp[i][j-1]` already contains all permutations where the last inserted element created ≤ `j-1` new pairs.
- Adding `dp[i-1][j]` appends a case where the last element creates exactly `0` new pairs.
- Subtract `dp[i-1][j-i]` removes the over‑count where the new element would create more than `i-1` pairs (i.e., `p > i`).

All operations are taken modulo `M`.

**Rolling Array**

Since `dp[i][*]` depends only on `dp[i-1][*]`, we can keep two 1‑D arrays:

```text
prev[j]   – dp[i-1][j]
curr[j]   – dp[i][j]
```

And after each outer loop we swap them. This reduces the space to `O(k)`.

**Final Step**

After building up to `i = n`, `dp[n][k]` actually holds the **cumulative** number of permutations with ≤ `k` inverse pairs because of the prefix‑sum trick. To get exactly `k`, subtract the previous value:

```
answer = dp[n][k] - dp[n][k-1]  (mod M)
```

---

### 2.6 The “Ugly” – Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Negative modulo** | `dp[i-1][j] - dp[i-1][j-i]` can be negative | Add `MOD` before applying `%` |
| **Index Out‑of‑Bounds** | `j-i < 0` or `j-i < 0` in Python/Java | Guard with `if (j-i >= 0)` |
| **Integer overflow in C++** | `dp[j] + curr[j-1]` may exceed `int` | Use `long long` or cast to `long long` before `%` |
| **Time limit due to naive 3‑D loops** | O(n³) in Python due to list comprehension | Use the 1‑D rolling array |
| **Missing base case** | `dp[0][0] = 1` but `dp[0][>0]` not initialized | Set entire array to `0` then `dp[0] = 1` |
| **Not swapping arrays** | After each `i`, `prev` and `curr` are mixed up | `prev.swap(curr)` (C++), `dp = nxt` (Java/Python) |

---

### 2.7 Takeaway: Why Solving 629 Matters

1. **Shows DP Mastery** – Many “Hard” LeetCode problems revolve around DP. Demonstrating a correct recurrence and a clever optimization signals deep understanding of state transitions.
2. **Mathematical Insight** – Recognizing the sliding‑window property and using prefix sums highlights mathematical intuition – a prized trait for system‑design interviews.
3. **Performance Engineering** – Implementations that avoid `O(n² k)` are practical. Recruiters love candidates who know how to **reduce both time and space**.
4. **Language Agnostic** – Whether you code in Java, Python, or C++, the algorithmic idea is the same. This shows flexibility – another asset in a fast‑moving tech environment.

---

### 2.8 Conclusion

LeetCode 629 is a classic “Hard” problem that teaches you how to:

1. Formulate a DP table (`dp[i][j]`).
2. Spot a convolution and turn it into a prefix‑sum recurrence.  
3. Deploy a rolling array for linear space.  
4. Handle modular arithmetic correctly.

Mastering this problem will not only earn you a high score on LeetCode but also equip you with a transferable skill set: *state definition, recurrence simplification, and space optimization.*  

> **Pro tip:** After you’re comfortable with this DP trick, try solving the *“K‑th Smallest Prime Fraction”* or *“Maximum Product Subarray”* – they’re structurally similar and follow the same optimization principles.

Happy coding, and may your interviewers see the *Good* and *Ugly* in your own algorithmic journey! 🚀

--- 

> **For further reading:**  
> - *Dynamic Programming* (MIT OCW) – https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-fall-2011/lecture-notes/  
> - *The Art of DP in Interviews* – https://www.interviewbit.com/dynamic-programming-interview-questions/  
> - *LeetCode Discuss – 629* – https://leetcode.com/problems/k-inverse-pairs-array/discuss/
--- 

**Happy Interviewing!**