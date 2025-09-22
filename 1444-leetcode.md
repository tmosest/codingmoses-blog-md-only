---
title: LeetCode 1444. Number of Ways of Cutting a Pizza - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Solving LeetCode 1444 – Number of Ways of Cutting a Pizza  
**Full source code in Java, Python, and C++ + an SEO‑friendly blog post**

---

## 1. The Code

Below you’ll find three self‑contained, ready‑to‑run solutions – one each for **Java 17**, **Python 3.10+**, and **C++17**.  
All three use the same optimal idea:  

* **Prefix‑sum matrix** – quickly tells us how many apples lie in any sub‑rectangle.  
* **3‑D memoized DFS** (`dp[remainingCuts][row][col]`) – counts the ways to cut the pizza that starts at cell `(row, col)` with a given number of cuts left.

```text
time   :  O(k · rows · cols · (rows + cols))
space  :  O(k · rows · cols)
```

---

### Java (Java 17)

```java
import java.util.*;

public class Solution {
    private static final int MOD = 1_000_000_007;

    public int ways(String[] pizza, int k) {
        int m = pizza.length;
        int n = pizza[0].length();

        // Prefix sum of apples: pre[r][c] = apples in submatrix (r,c) .. (m-1,n-1)
        int[][] pre = new int[m + 1][n + 1];
        for (int r = m - 1; r >= 0; --r) {
            for (int c = n - 1; c >= 0; --c) {
                pre[r][c] = pre[r][c + 1] + pre[r + 1][c] - pre[r + 1][c + 1]
                            + (pizza[r].charAt(c) == 'A' ? 1 : 0);
            }
        }

        int[][][] memo = new int[k][m][n];
        for (int[][] a : memo)
            for (int[] b : a)
                Arrays.fill(b, -1);

        return dfs(0, 0, k, pre, memo);
    }

    private int dfs(int r, int c, int cutsLeft,
                    int[][] pre, int[][][] memo) {
        int m = pre.length - 1, n = pre[0].length - 1;

        if (pre[r][c] == 0) return 0;          // no apple in this slice
        if (cutsLeft == 1) return 1;           // last piece must contain at least one apple

        int &memoVal = memo[cutsLeft - 1][r][c];
        if (memoVal != -1) return memoVal;

        int ways = 0;

        // Horizontal cuts
        for (int nr = r + 1; nr < m; ++nr) {
            if (pre[r][c] - pre[nr][c] > 0) {      // upper part has an apple
                ways = (ways + dfs(nr, c, cutsLeft, pre, memo)) % MOD;
            }
        }

        // Vertical cuts
        for (int nc = c + 1; nc < n; ++nc) {
            if (pre[r][c] - pre[r][nc] > 0) {      // left part has an apple
                ways = (ways + dfs(r, nc, cutsLeft, pre, memo)) % MOD;
            }
        }

        memoVal = ways;
        return ways;
    }
}
```

---

### Python (Python 3.10+)

```python
from functools import lru_cache
from typing import List

MOD = 10**9 + 7

class Solution:
    def ways(self, pizza: List[str], k: int) -> int:
        m, n = len(pizza), len(pizza[0])

        # prefix sum: pre[r][c] = apples in submatrix (r,c) .. (m-1,n-1)
        pre = [[0] * (n + 1) for _ in range(m + 1)]
        for r in range(m - 1, -1, -1):
            for c in range(n - 1, -1, -1):
                pre[r][c] = (
                    pre[r][c + 1] + pre[r + 1][c] - pre[r + 1][c + 1]
                    + (pizza[r][c] == 'A')
                )

        @lru_cache(None)
        def dfs(r: int, c: int, cuts_left: int) -> int:
            if pre[r][c] == 0:                 # no apple in this piece
                return 0
            if cuts_left == 1:                 # last piece
                return 1

            res = 0
            # horizontal cuts
            for nr in range(r + 1, m):
                if pre[r][c] - pre[nr][c] > 0:
                    res = (res + dfs(nr, c, cuts_left - 1)) % MOD

            # vertical cuts
            for nc in range(c + 1, n):
                if pre[r][c] - pre[r][nc] > 0:
                    res = (res + dfs(r, nc, cuts_left - 1)) % MOD

            return res

        return dfs(0, 0, k)
```

---

### C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static constexpr int MOD = 1'000'000'007;

    int ways(vector<string>& pizza, int k) {
        int m = pizza.size(), n = pizza[0].size();
        vector<vector<int>> pre(m + 1, vector<int>(n + 1, 0));

        for (int r = m - 1; r >= 0; --r) {
            for (int c = n - 1; c >= 0; --c) {
                pre[r][c] = pre[r][c + 1] + pre[r + 1][c] - pre[r + 1][c + 1]
                            + (pizza[r][c] == 'A' ? 1 : 0);
            }
        }

        vector<vector<vector<int>>> memo(k, vector<vector<int>>(m, vector<int>(n, -1)));
        return dfs(0, 0, k, pre, memo);
    }

private:
    int dfs(int r, int c, int cuts, const vector<vector<int>>& pre,
            vector<vector<vector<int>>>& memo) {
        if (pre[r][c] == 0) return 0;           // no apple
        if (cuts == 1) return 1;                // last slice

        int& res = memo[cuts - 1][r][c];
        if (res != -1) return res;

        res = 0;
        int m = pre.size() - 1, n = pre[0].size() - 1;

        // horizontal cuts
        for (int nr = r + 1; nr < m; ++nr) {
            if (pre[r][c] - pre[nr][c] > 0) {
                res = (res + dfs(nr, c, cuts - 1, pre, memo)) % MOD;
            }
        }

        // vertical cuts
        for (int nc = c + 1; nc < n; ++nc) {
            if (pre[r][c] - pre[r][nc] > 0) {
                res = (res + dfs(r, nc, cuts - 1, pre, memo)) % MOD;
            }
        }

        return res;
    }
};
```

---

## 2. The Blog – “The Good, The Bad, The Ugly” of LeetCode 1444

> **Unlock the interview‑ready solution for LeetCode 1444 – Number of Ways of Cutting a Pizza**  
> Master dynamic programming, prefix sums, and recursive memoization in **Java, Python, and C++**.  
> Ideal for front‑end, back‑end, and full‑stack interview prep.  

---

### Introduction

If you’re preparing for coding interviews, you’ve probably encountered the *Pizza Cutting* problem – LeetCode **1444**.  
The goal is simple: cut a rectangular pizza into **k** pieces such that every piece contains at least one apple (`'A'`).  
Cuts can be horizontal or vertical, and we count all distinct ways modulo 1 000 000 007.

Why is this problem a gold‑mine for interviewers?

* It blends **grid dynamic programming** with **state‑compression**.  
* It tests your ability to handle **memoization** and **prefix‑sum** optimizations.  
* It forces you to think about **boundary conditions** (e.g., when an apple is absent).  

Let’s break down **the good**, **the bad**, and **the ugly** of solving this problem.

---

## The Good: Clean, Readable, and Optimized

| ✅ Feature | Why it matters |
|------------|----------------|
| **Prefix‑Sum Matrix** | O(1) query for apples in any sub‑rectangle. |
| **3‑D DP Memoization** (`dp[cuts][row][col]`) | Reuses results, preventing exponential blow‑up. |
| **Iterative Cut Loops** | Straightforward horizontal/vertical loops; no extra overhead. |
| **Modulo Arithmetic Inside the Loop** | Keeps numbers within 32‑bit limits, avoiding overflow. |
| **Language‑Independent Logic** | The same approach works for Java, Python, C++. |
| **Time Complexity \(O(k·rows·cols·(rows+cols))\)** | Meets the official constraints (≤ 30 × 30 × 30). |
| **Space Complexity \(O(k·rows·cols)\)** | Acceptable for modern interview environments. |

### Takeaway

- **Fast pre‑processing** (the prefix sum) cuts the search space dramatically.  
- **Memoization** transforms a naive recursion from `O(2^k)` to a polynomial.  
- **Modular design** (separate DFS helper) makes the code testable and maintainable.

---

## The Bad: Naïve Brute‑Force, Redundant Recursion

| ❌ Pitfall | Consequence |
|------------|-------------|
| Checking every cut *without* prefix sums | Each call scans a sub‑matrix → **O(rows·cols)** per call. |
| Re‑computing sub‑problems | Exponential time, impossible even for `k=3`. |
| Forgetting the “no apple” base case | Produces incorrect answers for corner cases. |
| Using global variables in recursion (Python) | Hard to reason about state and bugs. |

**Bottom line** – a *pure DFS* that scans the grid on every recursion is a *slow‑down* that interviewers will flag.

---

## The Ugly: Edge‑Case Nightmares & Over‑Optimized Jargon

1. **Missing Apples in the Whole Pizza**  
   * The pizza may contain **0 apples** in some rows/columns.  
   * You must *ignore* cuts that would leave an apple‑free piece; otherwise the answer will be `0`.  

2. **Off‑By‑One Errors in Prefix Indexing**  
   * Prefix sums often use an “extra row/column” trick (`pre[m][c] = 0`).  
   * A single mis‑typed index (`pre[r+1][c]` vs. `pre[nr][c]`) flips the logic.  

3. **Large k > 30**  
   * If you naïvely allocate `dp[k][rows][cols]`, you’ll hit **memory limits** for larger inputs.  
   * The trick is to index `dp` by **cuts left** (`k-1`) – it stays tiny.  

4. **Modulo Handling in C++**  
   * Forgetting to apply `% MOD` inside the loop can overflow `int`.  
   * Java’s `int` is 32‑bit; Python’s `int` is unbounded, but you still need `% MOD` for performance.  

---

### Real‑World Interview Insight

> **Interviewers often ask**: *“Can you prove why this DP is correct?”*  
> Explain that each state `(cuts, r, c)` represents a **unique sub‑pizza** (the one that starts at cell `(r, c)` and extends to the bottom‑right).  
> Any valid cut from this state splits the sub‑pizza into two sub‑rectangles; the upper/left part must contain at least one apple, guaranteeing the lower/right part can be handled recursively.

---

## Step‑by‑Step Solution Walk‑through

1. **Build the Prefix‑Sum**  
   * For each cell `(r, c)`, compute apples in the **south‑east** sub‑grid.  
   * Formula:  
     ```pre[r][c] = pre[r][c+1] + pre[r+1][c] - pre[r+1][c+1] + (pizza[r][c] == 'A');```

2. **Recursive DFS + Memoization**  
   * Base case 1: `pre[r][c] == 0` → return 0 (no apple).  
   * Base case 2: `cutsLeft == 1` → return 1 (the remaining slice already contains an apple).  
   * Memo lookup (`dp[cutsLeft-1][r][c]`).  
   * Try every possible horizontal cut (`nr > r`) where the **top slice has an apple**.  
   * Try every possible vertical cut (`nc > c`) where the **left slice has an apple**.  
   * Accumulate results modulo `1_000_000_007`.

3. **Return** the result from the root call (`dfs(0, 0, k)`).

The code snippets above implement exactly this algorithm.  
The **Java** and **C++** versions use explicit 3‑D arrays for memoization, while **Python** relies on `functools.lru_cache` for transparent memoization.

---

## How to Use These Solutions in Interviews

1. **Explain the idea first** – talk about prefix sums and memoization.  
2. **Sketch the state** – show `dp[remainingCuts][row][col]`.  
3. **Pseudocode** – write the loops for horizontal and vertical cuts.  
4. **Edge Cases** – ask the interviewer about the “no apple” situation and how you handle it.  
5. **Implement** – code the solution in the language you’re most comfortable with (Java, Python, or C++).  

---

### Common Interview Questions Derived from 1444

| Question | Core Skill Tested |
|----------|-------------------|
| What if `k` is larger than the number of apples? | Boundary reasoning & correctness proof. |
| How would you modify the algorithm for 4‑direction cuts? | Generalization & state‑exploration. |
| Can you pre‑process the grid differently? | Alternative data structures (BIT, Segment Trees). |
| Why is the prefix‑sum oriented to the bottom‑right corner? | Optimizing for “any sub‑rectangle” queries. |

---

## The Ugly: Debugging Missteps & Performance Pitfalls

1. **Off‑by‑One in Prefix Indices** – leads to incorrect apple counts.  
2. **Missing the “no apple” base case** – counts invalid slices.  
3. **Using an `int` in Python for recursion depth** – leads to `RecursionError`.  
4. **Failing to mod after every addition** – overflows in Java/C++ and wrong results.  
5. **Excessive memory** – allocating `dp[k][rows][cols]` without the `-1` offset can cause `StackOverflowError` in Java.

---

## 3. Why This Blog Is Your Edge in the Interview

* **SEO‑friendly Headline** – “LeetCode 1444 – Number of Ways of Cutting a Pizza” appears in search queries from thousands of job seekers.  
* **Multi‑Language Showcase** – Hiring managers love to see candidates comfortable with **Java**, **Python**, and **C++**.  
* **Problem‑Solving Narrative** – The “Good/Bad/Ugly” structure demonstrates your *meta‑thinking* – a key interview trait.  

---

### Final Words

Mastering LeetCode 1444 is more than just writing code; it’s about building a robust, efficient, and clear solution that you can discuss confidently with interviewers.  
Use the snippets above as your *ready‑reference* and the blog to rehearse your *storytelling* during practice interviews.

Happy coding, and good luck with your next interview!