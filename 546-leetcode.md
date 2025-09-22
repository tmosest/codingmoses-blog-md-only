---
title: LeetCode 546. Remove Boxes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🏆 “Remove Boxes” (LeetCode 546) – The Good, The Bad, & The Ugly  
### Why mastering this problem can land you that software‑engineer role

> **Keywords** – *Remove Boxes Leetcode 546, dynamic programming, 3‑D DP, Java, Python, C++, interview problem, coding interview, algorithm design, job interview prep*  

---

### 1️⃣ Problem Recap

> **Input**: `int[] boxes` – colors represented by positive integers.  
> **Goal**: Repeatedly delete a contiguous segment of identical colors of length `k` and earn `k × k` points.  
> **Return**: Maximum total score possible until no boxes remain.

---

### 2️⃣ Intuition – What makes this problem *hard*?

- A naïve greedy strategy (e.g., delete the longest run first) is sub‑optimal.  
- The optimal order of deletions depends on *future* deletions that can merge separated runs of the same color.  
- You need to keep track of **how many identical boxes you already have on the “right”** that can be merged later.

This gives the problem its characteristic **3‑dimensional DP**.

---

### 3️⃣ 3‑D DP Formulation

Let `dp[l][r][k]` = maximum points we can obtain from sub‑array `boxes[l … r]` **with `k` extra boxes of the same color as `boxes[r]` appended to the right**.

*Why “extra boxes”?*  
When we delete a segment at the end, we may want to postpone it to combine it with equal colored boxes that are still on the left.

#### Recurrence

```
dp[l][r][k] =
    1) Remove the last segment now:
        dp[l][r-1][0] + (k+1)²
    2) Merge with a previous same‑colored box:
        For every i in [l, r-1] where boxes[i] == boxes[r]:
            dp[l][i][k+1] + dp[i+1][r-1][0]
```

The answer is `dp[0][n-1][0]`.

#### Optimisation (common trick)

Before evaluating the recurrence we “shrink” the right end:

```
while r > l and boxes[r] == boxes[r-1]:
    r--          // merge identical tail boxes
    k++          // increase the count of right‑hand identical boxes
```

This reduces the state space dramatically.

---

### 4️⃣ Complexity Analysis

|  | **Time** | **Space** |
|---|---|---|
| **Recursion + memoisation** | `O(n³)` (worst case `n = 100`) | `O(n³)` (3‑D array or memo map) |

`n³ = 1e6` – perfectly fine for modern CPUs and memory.

---

### 5️⃣ Implementation Highlights

| Language |  Key points |
|---|---|
| **Java** | Use a 3‑D `int[][][] dp` filled with `-1`. Recursive helper with memoisation. |
| **Python** | `@lru_cache(maxsize=None)` decorator for memoised recursion. |
| **C++** | Static 3‑D array `int dp[101][101][101]` initialised to `-1`. Recursive helper. |

All implementations follow the same recurrence; only syntax differs.

---

## 🔥 The “Good, Bad & Ugly” of the Problem

| Aspect | Good | Bad | Ugly |
|---|---|---|---|
| **Concept** | Demonstrates how future decisions can affect current state – a classic interview pattern. | Requires careful reasoning about the *state* you need to remember. | Mis‑reading the statement (e.g., thinking “delete all boxes of a color at once”) leads to wasted effort. |
| **Algorithm** | 3‑D DP is a neat, elegant solution once understood. | 3‑D DP can be confusing; many candidates jump straight to a 2‑D DP and fail. | Implementation bugs: off‑by‑one errors, wrong memo keys, overflow in `(k+1)*(k+1)` for large `k`. |
| **Readability** | With comments it’s clear: shrink tail, merge, memoise. | Boilerplate for 3‑D arrays can hide logic. | Recursion depth in Python/Java might hit limits for `n=100`; iterative DP could be safer but more verbose. |
| **Performance** | Meets constraints comfortably. | Space usage is high but acceptable. | Some solutions allocate full 100³ arrays regardless of actual `n`, wasting memory. |

---

## 📌 Take‑away for Job Interviews

1. **Explain the state clearly** – “dp[l][r][k]” means: *consider sub‑array `l..r` and we already have `k` boxes of the same color as the rightmost box that we can merge later*.  
2. **Show the shrink trick** – merging identical tail boxes reduces complexity and avoids extra states.  
3. **Discuss complexity** – `O(n³)` is fine for `n≤100`; justify why we use memoisation.  
4. **Edge Cases** – single element, all same colors, no two adjacent same colors.  
5. **Optional Optimisation** – discuss using a `HashMap<Long, Integer>` (packed state key) in languages where 3‑D arrays are awkward.

---

## 🚀 Full Code

> The following code snippets solve **Remove Boxes** in **Java**, **Python**, and **C++**.  
> Each includes ample comments and follows the 3‑D DP recurrence described above.

---

### 📃 Java Solution

```java
import java.util.Arrays;

public class Solution {
    // dp[l][r][k] = max points for boxes[l..r] with k extra boxes of same color as boxes[r] on the right
    private int[][][] dp;

    public int removeBoxes(int[] boxes) {
        int n = boxes.length;
        dp = new int[n][n][n + 1];          // +1 because k can be n-1
        for (int[][] arr2 : dp)
            for (int[] arr1 : arr2)
                Arrays.fill(arr1, -1);      // -1 means “uncomputed”

        return solve(boxes, 0, n - 1, 0);
    }

    private int solve(int[] boxes, int l, int r, int k) {
        if (l > r) return 0;

        // Merge identical boxes at the right end
        while (r > l && boxes[r] == boxes[r - 1]) {
            r--;
            k++;
        }

        if (dp[l][r][k] != -1) return dp[l][r][k];

        // Option 1: remove the last segment now
        int best = solve(boxes, l, r - 1, 0) + (k + 1) * (k + 1);

        // Option 2: merge with a previous same‑colored box
        for (int i = l; i < r; i++) {
            if (boxes[i] == boxes[r]) {
                int left  = solve(boxes, l, i, k + 1);   // merge i with the right segment
                int right = solve(boxes, i + 1, r - 1, 0); // remove middle part first
                best = Math.max(best, left + right);
            }
        }

        return dp[l][r][k] = best;
    }
}
```

---

### 📃 Python Solution

```python
from functools import lru_cache
from typing import List

class Solution:
    def removeBoxes(self, boxes: List[int]) -> int:
        n = len(boxes)

        @lru_cache(maxsize=None)
        def dfs(l: int, r: int, k: int) -> int:
            """Return max points for boxes[l..r] with k extra boxes same as boxes[r] on the right."""
            if l > r:
                return 0

            # Merge identical boxes at the right end
            while r > l and boxes[r] == boxes[r - 1]:
                r -= 1
                k += 1

            # Option 1: delete the rightmost segment now
            best = dfs(l, r - 1, 0) + (k + 1) ** 2

            # Option 2: merge with an earlier same‑colored box
            for i in range(l, r):
                if boxes[i] == boxes[r]:
                    best = max(best,
                               dfs(l, i, k + 1) +  # merge i with right
                               dfs(i + 1, r - 1, 0))  # remove middle part

            return best

        return dfs(0, n - 1, 0)
```

---

### 📃 C++ Solution

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int removeBoxes(vector<int>& boxes) {
        int n = boxes.size();
        // dp[l][r][k] = -1 indicates uncomputed
        static int dp[101][101][101];          // n <= 100
        memset(dp, -1, sizeof(dp));
        return dfs(boxes, 0, n - 1, 0, dp);
    }

private:
    int dfs(vector<int>& boxes, int l, int r, int k,
            int (&dp)[101][101][101]) {
        if (l > r) return 0;

        // Shrink identical tail boxes
        while (r > l && boxes[r] == boxes[r - 1]) {
            r--;
            k++;
        }

        if (dp[l][r][k] != -1) return dp[l][r][k];

        // Option 1: delete the rightmost segment now
        int best = dfs(boxes, l, r - 1, 0, dp) + (k + 1) * (k + 1);

        // Option 2: merge with an earlier same‑colored box
        for (int i = l; i < r; ++i) {
            if (boxes[i] == boxes[r]) {
                int left  = dfs(boxes, l, i, k + 1, dp);   // merge i with right
                int right = dfs(boxes, i + 1, r - 1, 0, dp); // remove middle part
                best = max(best, left + right);
            }
        }

        return dp[l][r][k] = best;
    }
};
```

---

## 🎯 Final Checklist Before You Submit

| ✅ | Item |
|---|---|
| State definition | `dp[l][r][k]` – *clear*? |
| Tail‑shrink logic | Included? |
| Memoisation | `-1` / `lru_cache` / `memset`? |
| Bounds | `dp[101][101][101]` is safe for `n ≤ 100`. |
| Edge cases | Single element, all same, no adjacent equals – verified by unit tests. |
| Overflow | `(k+1)*(k+1)` fits in 32‑bit signed int for `k ≤ 99` (max ≈ 10 000). |

---

### 📈 Closing Thought

“Remove Boxes” is **a classic 3‑D DP interview problem** that forces you to think *ahead* and to manage a *non‑obvious state* (`k`).  

Mastering it means you’re comfortable with:

- Designing states that capture *future merge possibilities*  
- Writing clean, recursive DP with memoisation  
- Communicating algorithmic intuition under interview pressure  

So the next time you see “Delete the longest run first”, remember: **the best order might be the one that merges runs later**.  

Good luck, and may your next interview feel more like a *solved puzzle* than a *brain‑break*! 🚀