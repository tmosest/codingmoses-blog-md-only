---
title: LeetCode 1770. Maximum Score from Performing Multiplication Operations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 1770 – “Maximum Score from Performing Multiplication Operations”

> **Problem**  
> You have two arrays:  
> • `nums` (size *n*,  *n* ≥ *m*)  
> • `multipliers` (size *m*)  
> Starting with score = 0, you perform exactly *m* operations.  
> In operation *i* you pick an element from the *start* or the *end* of the current `nums`, multiply it by `multipliers[i]`, add it to the score and delete that element from `nums`.  
> Return the **maximum possible score** after all *m* operations.

> **Constraints**  
> * 1 ≤ *m* ≤ 300  
> * *m* ≤ *n* ≤ 10⁵  
> * –1000 ≤ nums[i], multipliers[i] ≤ 1000  

> **Links**  
> • LeetCode problem page  
> • Various community solutions (see the prompt for a list)

---

## 🧩 Why the “Brute‑Force” TLEs

A direct recursion that tries both ends at every step is **O(2ᵐ)**.  
With *m* = 300 the worst‑case number of states is astronomically huge –  
you would try over 10⁸⁵ possibilities.  

Even a simple `for` loop that recomputes every state from scratch would need  
`O(n · m)` memory if you store the whole DP table – far beyond the 1 e5 size of `nums`.

Hence we need a *state reduction* trick.

---

## ✅ The Optimal DP – “Only the Edges Matter”

Observe that after *i* operations you will have taken exactly *i* elements from `nums`.  
The **only** thing that matters for future decisions is *how many* you took from the **left side**.

*Let* `dp[i][k]` = maximum score we can obtain after processing the first *i* multipliers **and** taking exactly `k` elements from the left.  
Consequently we have taken `i‑k` elements from the right, because we removed `i` elements in total.

**Transition**

```
rightIndex = n - 1 - (i - k)          // the element we would pick from the right
dp[i+1][k+1] = max( dp[i+1][k+1],
                    dp[i][k] + nums[k]           * multipliers[i] )
dp[i+1][k]   = max( dp[i+1][k],
                    dp[i][k] + nums[rightIndex] * multipliers[i] )
```

You only need a table of size *(m+1) × (m+1)* – about 90 000 cells –  
well within the limits.

**Why this works**

* When *n* ≥ 2·*m*, the middle part of `nums` is **never reachable**.  
  You can drop it and only keep the first *m* and the last *m* elements.
* The DP only considers those at most *2·m* candidates, which is why the
  algorithm stays fast even for *n* = 10⁵.

---

## 📐 Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Naïve recursion (`2ᵐ`) | O(2ᵐ) | O(m) | Exponential – always TLE |
| Recursion + memo (`m²` states) | **O(m²)** | **O(m²)** | 300² = 90 000 ≈ 0.09 M – trivial |
| Bottom‑up tabulation (iterative) | **O(m²)** | **O(m)** (row‑by‑row) | Even less memory |
| Greedy / two‑pointer (wrong) | O(m) | O(1) | Produces sub‑optimal answer |

With the optimal DP you are guaranteed to finish in less than a millisecond.

---

## 📄 Implementation – Three Languages

Below are clean, ready‑to‑paste implementations for **Java, Python, and C++**.

> **Tip:** The three solutions all use *the same idea* – a recursive memo that stores the maximum score for the pair `(leftIndex, operationsDone)` – only the syntax changes.

---

### Java (Recursive + Memoization)

```java
class Solution {
    private int n, m;
    private int[] nums, mult;
    // dp[left][op] = max score with 'left' elements already taken from the left
    // at operation 'op'
    private int[][] dp;

    public int maximumScore(int[] nums, int[] multipliers) {
        this.n = nums.length;
        this.m = multipliers.length;
        this.nums = nums;
        this.mult = multipliers;
        dp = new int[m][m + 1];   // we only need m×m (op <= m, left <= op)
        for (int[] row : dp) Arrays.fill(row, Integer.MIN_VALUE);

        return solve(0, 0);
    }

    private int solve(int left, int op) {
        if (op == m) return 0;                         // all multipliers used

        if (dp[left][op] != Integer.MIN_VALUE)         // already computed
            return dp[left][op];

        int rightIdx = n - 1 - (op - left);            // current rightmost element
        int pickLeft  = nums[left] * mult[op] + solve(left + 1, op + 1);
        int pickRight = nums[rightIdx] * mult[op] + solve(left, op + 1);

        dp[left][op] = Math.max(pickLeft, pickRight);
        return dp[left][op];
    }
}
```

> **Why this works**  
> *`left`* ranges from `0 … op`, so at most 300 × 300 cells.  
> The `rightIdx` computation is the only “ugly” part – it is a little tricky but stays constant‑time.

---

### Python (Bottom‑Up DP – O(m²) time & O(m) space)

```python
class Solution:
    def maximumScore(self, nums: List[int], multipliers: List[int]) -> int:
        n, m = len(nums), len(multipliers)
        # We only need the first m and the last m elements of nums
        if n > 2 * m:
            nums = nums[:m] + nums[-m:]

        # dp[k] = best score after taking k left elements at current multiplier
        dp = [0] * (m + 1)
        for i in range(m - 1, -1, -1):          # from the last multiplier to the first
            new = [0] * (m + 1)
            for k in range(i + 1):              # k can be 0 … i
                left  = dp[k + 1] + multipliers[i] * nums[k]
                right = dp[k] + multipliers[i] * nums[len(nums) - 1 - (i - k)]
                new[k] = max(left, right)
            dp = new
        return dp[0]
```

> **Why the slice `nums[:m] + nums[-m:]`?**  
> If *n* > 2·*m*, the middle numbers can never be chosen – pruning them saves memory.

---

### C++ (Iterative Tabulation – O(m²) memory)

```cpp
class Solution {
public:
    int maximumScore(vector<int>& nums, vector<int>& multipliers) {
        int n = nums.size(), m = multipliers.size();

        // keep only reachable ends
        if (n > 2 * m) {
            vector<int> tmp(2 * m);
            copy(nums.begin(), nums.begin() + m, tmp.begin());
            copy(nums.end() - m, nums.end(), tmp.begin() + m);
            nums.swap(tmp);
            n = 2 * m;
        }

        vector<vector<long long>> dp(m + 1, vector<long long>(m + 1, 0));

        for (int i = m - 1; i >= 0; --i) {
            for (int left = 0; left <= i; ++left) {
                int right = n - 1 - (i - left);
                long long takeLeft  = 1LL * nums[left] * multipliers[i] + dp[left + 1][i + 1];
                long long takeRight = 1LL * nums[right] * multipliers[i] + dp[left][i + 1];
                dp[left][i] = max(takeLeft, takeRight);
            }
        }
        return (int)dp[0][0];
    }
};
```

> The C++ solution follows the same logic as the Java & Python ones – only the syntax changes.

---

## 🔍 Understanding the Good, Bad, and Ugly

| Aspect | What’s Good | What’s Bad | What’s Ugly |
|--------|-------------|------------|-------------|
| **Intuition** | Think of *k* picks from the left. The remaining *i‑k* picks come from the right. | None | None |
| **State space** | `dp[left][op]` – only *m²* states, not *n · m*. | Too many states if you accidentally use full `n × m` array. | Index arithmetic (`rightIdx = n - 1 - (op - left)`) is easy to get wrong. |
| **Memory** | 300 × 300 ≈ 90 k ints (≈ 0.7 MB) – trivial. | If you keep the whole `nums` in DP, you’ll hit 1 e5 × 300 cells. | If you forget to prune the middle part of `nums` when `n > 2·m`, you’ll still allocate an array of size *n* – wasteful. |
| **Speed** | Linear in *m²* → ≈ 90 k operations – instant. | Exponential naïve recursion → TLE. | A common bug: using `long long` (C++) or `int` (Java) without checking overflow? Not an issue here because each multiplication fits in 32‑bit, but summing up to 300 times still fits in 32‑bit (`300 × 1000 × 1000` = 3 × 10⁸). |
| **Edge Cases** | *n* = *m* → you must use all elements. | *n* ≫ *m* → you can safely drop the middle part of `nums`. | Off‑by‑one errors in the `rightIdx` calculation are common – double‑check with a small test. |

---

## 📈 Complexity Summary

* **Time**: `O(m²)` – at most 90 000 operations.  
* **Space**: `O(m²)` for the memo table, or `O(m)` for the iterative version.  
* **Why it passes**:  
  * The middle of `nums` is pruned when unnecessary.  
  * The DP table never touches *n* directly, so the huge *n* does not hurt.

---

## 📣 Final Word

When you see a problem that asks for “maximum score / minimum cost” with a limited number of operations, always try to:

1. **Reduce the problem** – remove unreachable items.
2. **Define a state** that captures only what future decisions need.
3. **Memoize / tabulate** – avoid recomputation.

These implementations are concise, free of boilerplate, and demonstrate the elegant DP trick that lets you win in interviews and contests alike.

Happy coding! 🚀

--- 

**Happy Interviewing!** If you have questions about any part, feel free to ask.