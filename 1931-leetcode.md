---
title: LeetCode 1931. Painting a Grid With Three Different Colors - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 Leetcode 1931 – *Painting a Grid With Three Different Colors*  
## The Good, the Bad, and the Ugly – A Job‑Interview Friendly Blog Post

> **SEO Keywords**: Leetcode 1931, Painting a Grid With Three Different Colors, DP, Bitmask DP, Grid Coloring, Coding Interview, Java solution, Python solution, C++ solution, interview prep, algorithms, dynamic programming, coding challenge

---

## 1. Problem Overview

| Item | Description |
|------|-------------|
| **Title** | Painting a Grid With Three Different Colors |
| **ID** | 1931 |
| **Difficulty** | Hard |
| **Constraints** | 1 ≤ *m* ≤ 5, 1 ≤ *n* ≤ 1000 |
| **Goal** | Count how many ways to paint an *m* × *n* grid with 3 colors (red, green, blue) such that **no two orthogonally adjacent cells share the same color**. |
| **Answer Mod** | 10⁹ + 7 |

**Why this problem is interview‑worthy**

* It tests **state‑compression DP** (bitmasking) – a staple for many senior‑level interviews.  
* It challenges you to think about **horizontal vs. vertical adjacency** separately.  
* It scales with *n* up to 1000, forcing you to come up with a *O(n · K²)* solution where *K* is the number of valid column states (≤ 81 for *m* = 5).

---

## 2. Intuition & High‑Level Strategy

1. **Treat columns as the DP dimension** – we process the grid column by column.  
2. **Generate all valid column states** (`state`) such that no two consecutive rows in the same column share the same color. For *m* = 5, the count is at most 3⁵ = 243, but vertical conflicts cut that down to 81.  
3. **Determine compatibility between states** – two states `A` and `B` can sit side‑by‑side if for every row `i`, `A[i]` ≠ `B[i]` (no horizontal conflict).  
4. **Dynamic programming**  
   * `dp[0][s] = 1` for every valid state `s` (first column).  
   * Transition:  
     ```text
     dp[col][next] += dp[col‑1][prev]   if prev and next are compatible
     ```  
   * All calculations are performed modulo `MOD`.  
4. **Answer** – sum all `dp[n‑1][s]` for the last column.

> **The “Good”** – The problem naturally decomposes into a *finite* state space; you never need to iterate over the whole grid.  
> **The “Bad”** – If you naïvely generate all 3ⁿ possibilities for each cell, the time explodes.  
> **The “Ugly”** – The subtlety of *vertical* conflicts being separate from *horizontal* conflicts can trip many people. The key is to encode each column as a single integer (bitmask) to keep transitions O(K²).

---

## 3. Detailed Algorithm

### 3.1. Encoding a Column

- Represent each row’s color with 2 bits (`00` = red, `01` = green, `10` = blue).  
- For *m* ≤ 5, the column fits into a 10‑bit integer (5 × 2).  
- A column `mask` is *valid* if for all `row` > 0, the two 2‑bit blocks differ.

### 3.2. Building Compatibility List

- For each pair of valid masks `(maskA, maskB)`:
  - They are **compatible** if `(maskA & 0x3) ≠ (maskB & 0x3)` for the lowest row, `(maskA >> 2 & 0x3) ≠ (maskB >> 2 & 0x3)` for the second row, etc.
  - Store `maskB` in a vector of compatible transitions for `maskA`.  
  - Complexity: *O(K²)*, K ≤ 81.

### 3.3. DP Across Columns

```
dp[0][s] = 1          // first column
for col = 1 .. n-1:
    for prev in validStates:
        for next in transitions[prev]:
            dp[col][next] = (dp[col][next] + dp[col-1][prev]) % MOD
```

Finally, `answer = Σ dp[n-1][s]` for all `s`.

---

## 4. Complexity Analysis

| Metric | Formula | For *m* = 5 | Explanation |
|--------|---------|-------------|-------------|
| **K (states)** | 3ᵐ – vertical conflicts | ≤ 81 | All 3⁵ possibilities minus those with consecutive equal colors. |
| **Time** | `O(n · K²)` | ~ 1000 · 81² ≈ 6.5 M ops | Dominated by the transition loops. |
| **Space** | `O(K)` | ~ 81 integers per column | We can keep only the current and previous column DP arrays. |

---

## 5. Code Implementations

> **Tip for interviews**: Show a single pass of DP with *O(K)* space, but keep the code readable.

### 5.1. Java 17 (Fastest readable)

```java
import java.util.*;

public class Solution {
    private static final int MOD = 1_000_000_007;

    public int colorTheGrid(int m, int n) {
        List<Integer> states = new ArrayList<>();
        // Generate all valid column masks
        for (int mask = 0; mask < (1 << (m * 2)); mask++) {
            if (isValidColumn(mask, m)) states.add(mask);
        }

        // Pre‑compute compatibility
        List<List<Integer>> compat = new ArrayList<>();
        for (int i = 0; i < states.size(); i++) {
            List<Integer> list = new ArrayList<>();
            for (int j = 0; j < states.size(); j++) {
                if (isCompatible(states.get(i), states.get(j), m))
                    list.add(states.get(j));
            }
            compat.add(list);
        }

        // DP
        long[] prev = new long[states.size()];
        Arrays.fill(prev, 1);   // first column

        for (int col = 1; col < n; col++) {
            long[] next = new long[states.size()];
            for (int i = 0; i < states.size(); i++) {
                long ways = prev[i];
                if (ways == 0) continue;
                for (int nb : compat.get(i)) {
                    int idx = states.indexOf(nb);
                    next[idx] = (next[idx] + ways) % MOD;
                }
            }
            prev = next;
        }

        // Sum
        long ans = 0;
        for (long v : prev) ans = (ans + v) % MOD;
        return (int) ans;
    }

    private boolean isValidColumn(int mask, int m) {
        int prev = -1;
        for (int r = 0; r < m; r++) {
            int color = (mask >> (2 * r)) & 3;   // 0=red,1=green,2=blue
            if (color == prev) return false;
            prev = color;
        }
        return true;
    }

    private boolean isCompatible(int a, int b, int m) {
        for (int r = 0; r < m; r++) {
            int ca = (a >> (2 * r)) & 3;
            int cb = (b >> (2 * r)) & 3;
            if (ca == cb) return false;
        }
        return true;
    }
}
```

> **Why this Java code wins**  
> * Uses primitive `int` bitmask – no boxing overhead.  
> * Keeps memory footprint minimal (`states.size()` ≤ 81).  
> * Demonstrates clean separation of *generation* (`isValidColumn`) and *transition* (`isCompatible`).

---

### 5.2. Python 3 (Clean & Pythonic)

```python
MOD = 1_000_000_007

class Solution:
    def colorTheGrid(self, m: int, n: int) -> int:
        # 1️⃣ Generate all valid column masks
        def gen(row, prev, mask):
            if row == m:
                states.append(mask)
                return
            for col in range(3):           # 0,1,2 for R,G,B
                if row > 0 and (mask >> (2*(row-1))) & 3 == col:
                    continue
                gen(row+1, col, mask | (col << (2*row)))

        states = []
        gen(0, -1, 0)

        # 2️⃣ Pre‑compute transitions
        comp = {s: [] for s in states}
        for a in states:
            for b in states:
                if all(((a >> (2*i)) & 3) != ((b >> (2*i)) & 3) for i in range(m)):
                    comp[a].append(b)

        # 3️⃣ DP across columns
        dp = {s: 1 for s in states}          # first column
        for _ in range(1, n):
            new = {}
            for prev, ways in dp.items():
                for nxt in comp[prev]:
                    new[nxt] = (new.get(nxt, 0) + ways) % MOD
            dp = new

        # 4️⃣ Sum answer
        return sum(dp.values()) % MOD
```

> **Python‑specific highlights**  
> * Recursive generator is tiny – no external libraries.  
> * Uses dictionary for DP; still O(K²) transitions.  
> * Keeps memory usage small – only two dictionaries at a time.

---

### 5.3. C++17 (Fastest possible)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static constexpr int MOD = 1'000'000'007;

    int colorTheGrid(int m, int n) {
        // ---------- 1. Build all valid column states ----------
        vector<int> states;
        for (int mask = 0; mask < (1 << (m * 2)); ++mask) {
            if (validColumn(mask, m)) states.push_back(mask);
        }

        // ---------- 2. Pre‑compute compatibility ----------
        vector<vector<int>> trans(states.size());
        for (int i = 0; i < (int)states.size(); ++i) {
            for (int j = 0; j < (int)states.size(); ++j) {
                if (compatible(states[i], states[j], m))
                    trans[i].push_back(j);
            }
        }

        // ---------- 3. DP across columns ----------
        vector<int> dp(states.size(), 1), ndp(states.size(), 0);
        for (int col = 1; col < n; ++col) {
            fill(ndp.begin(), ndp.end(), 0);
            for (int i = 0; i < (int)states.size(); ++i) {
                if (!dp[i]) continue;
                for (int j : trans[i]) {
                    ndp[j] = (ndp[j] + dp[i]) % MOD;
                }
            }
            dp.swap(ndp);
        }

        // ---------- 4. Sum up ----------
        int ans = 0;
        for (int x : dp) ans = (ans + x) % MOD;
        return ans;
    }

private:
    bool validColumn(int mask, int m) const {
        int prev = -1;
        for (int r = 0; r < m; ++r) {
            int col = (mask >> (2 * r)) & 3;
            if (col == prev) return false;
            prev = col;
        }
        return true;
    }

    bool compatible(int a, int b, int m) const {
        for (int r = 0; r < m; ++r) {
            int ca = (a >> (2 * r)) & 3;
            int cb = (b >> (2 * r)) & 3;
            if (ca == cb) return false;
        }
        return true;
    }
};
```

> **Why this C++ solution excels**  
> * Uses `vector<int>` – contiguous memory, cache friendly.  
> * `trans` as adjacency list → O(K²) but only integer indices.  
> * Constant `MOD` – inline mod operation is very cheap.

---

## 6. What to Emphasize in Interviews

1. **State Space Reduction** – Show how encoding a column into a bitmask reduces the problem to `K` states.  
2. **Transition Pre‑computation** – Clarify that compatibility is independent of column index, enabling reuse across all `n` columns.  
3. **Space Optimization** – Keep only two DP rows (`prev`, `next`) instead of `n` full matrices.  
4. **Modular Arithmetic** – All sums must be modulo `MOD` (important edge case).  
5. **Edge Cases** – When `n == 0`? (though constraints say `n ≥ 1`).  
6. **Testing** – Verify on sample cases, e.g., `m=2, n=3 → 18`.

---

## 7. Final Thoughts

> **In summary**:  
> *Leverage the small state space, encode columns with bitmasks, pre‑compute compatible transitions, and run a simple DP.*  

> **Interview Strategy**  
> * Talk through the problem decomposition first.  
> * Show the state generation and compatibility logic.  
> * Then explain the DP transition.  
> * End with the final summation.  

With this structure, you demonstrate both a **conceptual** grasp and **practical** implementation – the winning combination for any coding interview.

--- 

### 8. Further Reading & Similar Problems

- **"Number of Ways to Fill an Array With K Integers"** (LeetCode 1106) – similar state‑based DP.  
- **"Colorful Stones"** – dynamic programming over bitmasks.  
- **"Grid Coloring with Constraints"** – advanced combinatorics with DP.

> **Happy coding!** 🚀

--- 

> *If you want a step‑by‑step walk through the bit‑mask conversions, feel free to ask. Happy interviewing!*

--- 
**Keywords**: `LeetCode 1106`, `Grid coloring`, `Bitmask DP`, `Java`, `Python`, `C++`, `Dynamic Programming`, `Combinatorics`.