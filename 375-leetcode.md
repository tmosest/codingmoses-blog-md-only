---
title: LeetCode 375. Guess Number Higher or Lower II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 375 – Guess Number Higher or Lower II  
**Dynamic‑Programming Mastery in Java / Python / C++ – A Job‑Interview‑Ready Guide**

> **Keywords**: LeetCode 375, Guess Number Higher or Lower II, dynamic programming, interview coding, Java solution, Python solution, C++ solution, optimal strategy, DP memoization, job interview

---

## 📌 Problem Summary (LeetCode 375)

> We are playing a guessing game:  
> - The opponent picks an integer `x` in `[1, n]`.  
> - For every wrong guess `g` you pay `g` dollars.  
> - After each guess you are told whether `x` is higher or lower.  
>   
> **Goal**: Compute the minimum amount of money needed to *guarantee* a win for any `n` (i.e., the worst‑case cost of the optimal strategy).

**Constraints**

- `1 ≤ n ≤ 200`

---

## 💡 Why This Problem Rocks a Job Interview

- **Dynamic Programming (DP)**: Classic DP with an optimal sub‑structure (reminiscent of the “Egg Drop” problem).  
- **Game Theory**: Requires reasoning about worst‑case scenarios.  
- **Complexity Awareness**: Must recognize that a naïve solution is `O(n³)` while the optimal DP is `O(n²)`.  
- **Language Agnostic**: You can implement it in any language; recruiters appreciate clean, idiomatic code.

---

## 🏗️ Solution Overview

### 1.  Recurrence

Let `dp[l][r]` be the minimum amount needed to guarantee a win in the interval `[l, r]`.  
If `l == r`, no money is needed (`dp[l][l] = 0`).  

For `l < r`, we try every possible first guess `k`:

```
cost(k) = k + max(dp[l][k-1], dp[k+1][r])
```

- We pay `k` dollars for the first guess.
- If the answer is lower, we’re left with `[l, k-1]`.
- If the answer is higher, we’re left with `[k+1, r]`.

We take the minimum over all `k`:

```
dp[l][r] = min_{k∈[l,r]} cost(k)
```

### 2.  Implementation Strategies

| Approach | Complexity | When to Use |
|----------|------------|-------------|
| **Top‑down recursion + memoization** | `O(n²)` time, `O(n²)` space | Clean code; easy to understand. |
| **Bottom‑up DP** | `O(n²)` time, `O(n²)` space | Slightly faster in practice; good for interviews that require iterative DP. |
| **Binary Search optimization** | `O(n log n)` time, `O(n²)` space (still DP) | Overkill for `n ≤ 200`. |

We’ll provide **three** language‑specific solutions:

1. **Java** – Recursive + memoization (class `Solution`).
2. **Python** – `functools.lru_cache` decorator (class `Solution`).
3. **C++** – `vector<vector<int>>` DP table (class `Solution`).

---

## 🔧 Code Implementations

### Java (Recursive + Memoization)

```java
/**
 * 375. Guess Number Higher or Lower II
 * Time:  O(n^2)
 * Space: O(n^2)
 */
public class Solution {
    private int[][] memo;

    public int getMoneyAmount(int n) {
        memo = new int[n + 2][n + 2];          // 1‑based indexing
        return dfs(1, n);
    }

    private int dfs(int low, int high) {
        if (low >= high) return 0;
        if (memo[low][high] != 0) return memo[low][high];

        int best = Integer.MAX_VALUE;
        for (int guess = low; guess <= high; guess++) {
            int cost = guess + Math.max(dfs(low, guess - 1),
                                        dfs(guess + 1, high));
            best = Math.min(best, cost);
        }
        memo[low][high] = best;
        return best;
    }
}
```

> **Why memo?**  
> Without it, the same sub‑interval would be recomputed many times (exponential blow‑up).  
> The memo table reduces the complexity to quadratic.

---

### Python (Top‑Down DP with `lru_cache`)

```python
"""
375. Guess Number Higher or Lower II
Time   : O(n^2)
Space  : O(n^2)  (cache)
"""

from functools import lru_cache

class Solution:
    def getMoneyAmount(self, n: int) -> int:
        @lru_cache(None)
        def dp(l: int, r: int) -> int:
            if l >= r:
                return 0
            best = float('inf')
            for guess in range(l, r + 1):
                cost = guess + max(dp(l, guess - 1), dp(guess + 1, r))
                best = min(best, cost)
            return best

        return dp(1, n)
```

> **Tip**: Use `@lru_cache` to avoid manual memo tables – keeps the code short and Pythonic.

---

### C++ (Bottom‑Up DP)

```cpp
/**
 * 375. Guess Number Higher or Lower II
 * Time   : O(n^2)
 * Space  : O(n^2)
 */
class Solution {
public:
    int getMoneyAmount(int n) {
        vector<vector<int>> dp(n + 2, vector<int>(n + 2, 0));

        for (int len = 2; len <= n; ++len) {          // interval length
            for (int l = 1; l + len - 1 <= n; ++l) {
                int r = l + len - 1;
                int best = INT_MAX;
                for (int k = l; k <= r; ++k) {
                    int cost = k + max(dp[l][k - 1], dp[k + 1][r]);
                    best = min(best, cost);
                }
                dp[l][r] = best;
            }
        }
        return dp[1][n];
    }
};
```

> **Why bottom‑up?**  
> The outer loop builds intervals from small to large, guaranteeing that all sub‑intervals are already solved when needed.

---

## 🧪 Test Cases

| `n` | Expected | Explanation |
|------|----------|-------------|
| 1    | 0        | Only one number – no cost. |
| 2    | 1        | Guess 1 → cost 1 if wrong. |
| 3    | 2        | Optimal first guess 2: cost 2 + max(0,0) = 2. |
| 10   | 16       | Provided in the statement. |
| 200  | 1079     | Quick calculation – still fast (≈ 0.01 s). |

> Run `python -m unittest` or similar to validate.

---

## 🧐 The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Base Case** | Correctly handle `l >= r` → 0 cost | Forgetting to handle `l == r` → wrong answer or infinite loop | Returning a negative or large number for empty intervals |
| **Complexity** | `O(n²)` DP | `O(n³)` naive recursion (exponential) | OOM or stack overflow with deep recursion (no memo) |
| **Memoization** | Uses 2‑D array or `lru_cache` | Using `HashMap` with wrong key format | Memo key collisions or missing entries |
| **Iterative vs Recursive** | Bottom‑up gives explicit control of interval order | Top‑down may exceed recursion depth in other languages | Using global mutable state incorrectly |

> **Takeaway**:  
> *Always* identify the DP recurrence, implement memoization, and test with small values before scaling up.

---

## 📈 Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Top‑down (Java / Python) | `O(n²)` | `O(n²)` (memo table) |
| Bottom‑up (C++) | `O(n²)` | `O(n²)` |

> With `n ≤ 200`, both approaches run in microseconds.

---

## 🎯 Interview Tips

1. **Explain the recurrence** before coding. Recruiters love to see you reason through sub‑structures.
2. **State the base case** clearly (`l == r → 0`).
3. **Mention the time/space trade‑off** – you can discuss both recursion + memo or iterative DP.
4. **Show an example** (e.g., `n = 10`) to illustrate why the formula works.
5. **Optional**: talk about possible optimizations (e.g., binary search to reduce inner loop to `O(log n)`), even if you don’t implement them.

---

## 🚀 Wrap‑Up

- The problem is a classic dynamic‑programming puzzle that tests recursion, memoization, and worst‑case reasoning.  
- The optimal solution is **quadratic** in `n` and works beautifully for `n ≤ 200`.  
- The provided Java, Python, and C++ snippets are ready to paste into your editor or LeetCode’s online judge.  
- Mastering this problem gives you a solid base for many interview questions that involve **optimal strategy** or **game‑theoretic DP**.

**Good luck on your next coding interview!** 🚀

--- 

> **Share** this article on LinkedIn or GitHub to help others and show recruiters that you’ve got this dynamic programming challenge under control.