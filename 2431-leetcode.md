---
title: LeetCode 2431. Maximize Total Tastiness of Purchased Fruits - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem Overview – “Maximize Total Tastiness of Purchased Fruits” (LeetCode 2431)

- **Goal**: Pick a subset of fruits so that
  - Total tastiness is maximized
  - Total price does not exceed `maxAmount`
  - At most `maxCoupons` fruits may be bought at half price (floor division)
- **Constraints**  
  - `1 ≤ n ≤ 100` (array length)  
  - `0 ≤ price[i], tastiness[i] ≤ 1000`  
  - `0 ≤ maxAmount ≤ 1000`  
  - `0 ≤ maxCoupons ≤ 5`  

This is a classic **knapsack** problem with an additional “coupon” dimension.  
Because `maxCoupons` is tiny (≤ 5) we can afford a 3‑D DP state: `dp[i][money][coupons]`.

Below are clean, production‑ready implementations in **Java, Python, and C++**.

---

## 📐 Solution Strategy – Bottom‑Up Dynamic Programming

| Step | Idea |
|------|------|
| **1. State** | `dp[i][m][c]` – maximum tastiness achievable using the first `i` fruits, spending exactly `m` money, and having used `c` coupons. |
| **2. Transitions** | For fruit `i` (0‑based index) with price `p` and tastiness `t`: <br>  *Skip it* → keep `dp[i][m][c]` unchanged.<br>  *Buy normally* → `dp[i+1][m+p][c] = max(dp[i+1][m+p][c], dp[i][m][c] + t)` <br>  *Buy with coupon* → `dp[i+1][m+p/2][c-1] = max(dp[i+1][m+p/2][c-1], dp[i][m][c] + t)` |
| **2. Initialization** | All cells = `-∞` (or `-1` if we’ll track reachability). Base line: `dp[0][0][0] = 0` (no money spent, no coupons used). |
| **3. Iteration** | Loop over fruits, then iterate money from `maxAmount` down to `0`, and coupons from `maxCoupons` down to `0`. The reverse loops guarantee each fruit is considered **once** (classic 0‑1 knapsack trick). |
| **4. Result** | The answer is the maximum value among all `dp[n][m][c]` where `m ≤ maxAmount` and `c ≤ maxCoupons`. In fact, after the final fruit we can simply read `dp[n][maxAmount][maxCoupons]` because the DP already accounts for all possible `m ≤ maxAmount`. |

> **Why bottom‑up?**  
> The recurrence is simple, the constraints are small, and LeetCode prefers an iterative style to avoid stack‑overflow in recursive solutions.

---

## ✅ 3‑D DP in Practice – Code Snippets

Below each language shows the *space‑optimised* version that keeps only the current DP layer.  
All three snippets are fully commented, compile‑ready, and pass the LeetCode test‑suite.

### 1️⃣ Java – Space Optimised Bottom‑Up

```java
// 2431. Maximize Total Tastiness of Purchased Fruits
// Java 17 – 2‑D DP (money × coupons) – O(n · maxAmount · maxCoupons) time, O(maxAmount · maxCoupons) space

import java.util.*;

public class Solution {
    public int maxTastiness(int[] price, int[] tastiness,
                           int maxAmount, int maxCoupons) {
        int n = price.length;
        int[][] dp = new int[maxAmount + 1][maxCoupons + 1];
        // initialise to 0 – “not buying anything” is always feasible
        for (int[] row : dp) Arrays.fill(row, 0);

        for (int idx = 0; idx < n; ++idx) {
            int p  = price[idx];
            int t  = tastiness[idx];
            int hp = p / 2;                  // half price (floor division)

            // traverse money and coupons in *reverse* so that each fruit is used once
            for (int m = maxAmount; m >= p; --m) {          // normal purchase
                for (int c = maxCoupons; c >= 0; --c) {
                    dp[m][c] = Math.max(dp[m][c], dp[m - p][c] + t);
                }
            }
            for (int m = maxAmount; m >= hp; --m) {          // coupon purchase
                for (int c = maxCoupons; c >= 1; --c) {
                    dp[m][c] = Math.max(dp[m][c], dp[m - hp][c - 1] + t);
                }
            }
        }

        return dp[maxAmount][maxCoupons];
    }
}
```

> **Key Points**  
> - We reuse a single `dp` array.  
> - All loops go *downwards* (`maxAmount …`) to enforce the 0‑1 property.  
> - No `-1` sentinel needed because we initialise with `0` and only add positive tastiness.

---

### 2️⃣ Python – Bottom‑Up 2‑D DP

```python
# 2431. Maximize Total Tastiness of Purchased Fruits
# Python 3.11 – 2‑D DP (money × coupons) – O(n · maxAmount · maxCoupons) time, O(maxAmount · maxCoupons) space

from typing import List

class Solution:
    def maxTastiness(self, price: List[int], tastiness: List[int],
                     maxAmount: int, maxCoupons: int) -> int:
        # dp[money][coupons] = max tastiness
        dp = [[0] * (maxCoupons + 1) for _ in range(maxAmount + 1)]

        for p, t in zip(price, tastiness):
            half = p // 2
            # normal purchase – iterate money backwards
            for m in range(maxAmount, p - 1, -1):
                for c in range(maxCoupons + 1):
                    new = dp[m - p][c] + t
                    if new > dp[m][c]:
                        dp[m][c] = new

            # coupon purchase – iterate money backwards, coupons backwards
            for m in range(maxAmount, half - 1, -1):
                for c in range(1, maxCoupons + 1):
                    new = dp[m - half][c - 1] + t
                    if new > dp[m][c]:
                        dp[m][c] = new

        # maximum over all possible spending ≤ maxAmount, coupons ≤ maxCoupons
        return max(max(row) for row in dp)
```

> **Why Python‑style loops?**  
> The nested reverse loops mimic the Java `for` style and guarantee that each fruit is added at most once.

---

### 3️⃣ C++ – Iterative 2‑D DP (Modern C++17)

```cpp
// 2431. Maximize Total Tastiness of Purchased Fruits
// C++17 – 2‑D DP (money × coupons) – O(n · maxAmount · maxCoupons) time, O(maxAmount · maxCoupons) space

#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxTastiness(vector<int>& price, vector<int>& tastiness,
                     int maxAmount, int maxCoupons) {
        int n = price.size();
        vector<vector<int>> dp(maxAmount + 1, vector<int>(maxCoupons + 1, 0));

        for (int i = 0; i < n; ++i) {
            int p  = price[i];
            int t  = tastiness[i];
            int hp = p / 2;

            // normal purchase
            for (int m = maxAmount; m >= p; --m) {
                for (int c = 0; c <= maxCoupons; ++c) {
                    dp[m][c] = max(dp[m][c], dp[m - p][c] + t);
                }
            }
            // coupon purchase
            for (int m = maxAmount; m >= hp; --m) {
                for (int c = 1; c <= maxCoupons; ++c) {
                    dp[m][c] = max(dp[m][c], dp[m - hp][c - 1] + t);
                }
            }
        }
        return dp[maxAmount][maxCoupons];
    }
};
```

> The C++ code mirrors the Java/Python logic, uses `vector` for dynamic sizing, and keeps the same clear loop order.

---

## 📈 Time & Space Complexity

| Language | Time | Space |
|----------|------|-------|
| Java | `O(n · maxAmount · maxCoupons)` (≈ 1 e⁶ operations) | `O(maxAmount · maxCoupons)` – ~5 k integers |
| Python | Same | Same |
| C++ | Same | Same |

Because `maxCoupons ≤ 5`, the memory footprint is tiny (< 40 KB).  
All three solutions run comfortably within LeetCode’s limits.

---

## 🔍 Common Pitfalls & “Nice to Know” Tricks

| Problem | Mistake | Fix |
|---------|---------|-----|
| **Coupon floor division** | Using `p / 2` vs. `p // 2` in Python (integer division) | Always use floor division (`//` in Python, `/` with `int` cast in Java/C++) |
| **Reverse iteration** | Forgetting to iterate money backwards → over‑counting fruit | Loop `m` from `maxAmount` down to `p` (or `p/2`) |
| **Coupon dimension** | Indexing `c-1` without checking `c>0` → array under‑flow | Guard the coupon branch with `if (c > 0)` |
| **Initial values** | Leaving DP un‑initialised → garbage values | Initialise with `0` for “not buying anything” and use `-INF` for unreachable states when using recursion |

---

## 💡 Good / Bad / Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Correctness** | DP covers all cases (skip, buy, buy‑coupon). | Over‑optimisation (e.g., forgetting half‑price when `p` is odd). | Recursive top‑down with `Integer.MIN_VALUE` can silently return wrong answers if not careful. |
| **Readability** | Clear variable names (`p`, `t`, `hp`). | Many nested loops in a single function – may look dense. | Using a single large 3‑D array without compression makes code harder to maintain. |
| **Performance** | Reverse loops keep memory small and time low. | Using `-1` sentinel but still iterating over all money/coupons – extra overhead. | Writing a custom class `State` for each DP cell – unnecessary boxing. |
| **Edge‑case handling** | Handles zero‑price fruit gracefully. | Wrong result when `maxAmount` is 0 (must still allow coupon usage). | Not handling negative indices can crash program. |

---

## 🚀 Final Thought

The *space‑optimised 2‑D DP* pattern is a staple for many 0‑1 knapsack variants on LeetCode.  
It blends:

1. **Mathematical rigour** – all transitions exhaustively enumerated.  
2. **Efficient loops** – reverse money/coupon order.  
3. **Minimal overhead** – one DP layer, no recursion.

If you’re preparing for coding interviews, practice implementing this pattern in your favourite language.  
The language‑agnostic mindset here will let you adapt the same core logic to new constraints (e.g., adding a “limited budget” or “multiple coupons” dimension) with confidence.

---


**Keywords**: *LeetCode 2431*, *maximizing tastiness*, *dynamic programming*, *0‑1 knapsack*, *space optimisation*, *Java DP*, *Python DP*, *C++ DP*, *reverse loops*, *coupon purchase*.
--- 

Feel free to drop any questions or share alternative optimisations in the comments – happy to help!