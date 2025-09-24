---
title: LeetCode 3418. Maximum Amount of Money Robot Can Earn - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Problem Recap – LeetCode 3418: *Maximum Amount of Money Robot Can Earn*

**Goal**  
A robot starts at `(0,0)` and moves only **right** or **down** to reach `(m‑1, n‑1)` in an `m × n` grid `coins`.  
* `coins[i][j] ≥ 0` → the robot **gains** that many coins.  
* `coins[i][j] < 0` → a robber steals `|coins[i][j]|`.  
The robot can neutralise robbers in **at most two cells** on its path, preventing those robberies.

Return the **maximum** total coins the robot can collect (the total can be negative).

> **Constraints**  
> `1 ≤ m, n ≤ 500`  
> `-1000 ≤ coins[i][j] ≤ 1000`

---

## 🔍 Why this problem is a “Must‑Know” for job interviews

* **DP on 2‑D grid** – a classic interview pattern.
* **Limited resource (2 neutralisations)** – shows how to add an extra dimension to DP.
* **Large input** (`500×500`) – tests your ability to manage time & memory.

If you can explain this solution clearly in an interview, you’ll show mastery of dynamic programming and problem‑solving – a huge plus for any software engineering role.

---

## ✅ Solution Overview

1. **State**  
   `dp[i][j][k]` – maximum coins that can be collected when the robot is at cell `(i,j)` having used exactly `k` neutralisations (`k = 0, 1, 2`).

2. **Transition**  
   From `(i-1,j)` or `(i,j-1)` we can  
   * **Move without using** a neutraliser:  
     `dp[i][j][k] = max(dp[i][j][k], dp[prev][k] + coins[i][j])`  
   * **Move and use** one neutraliser on the current cell (if `k > 0`):  
     `dp[i][j][k] = max(dp[i][j][k], dp[prev][k-1] + (coins[i][j] >= 0 ? coins[i][j] : 0))`  
     (when we neutralise, a negative value is treated as `0`).

3. **Initialisation**  
   `dp[0][0][0] = coins[0][0]`  
   If `coins[0][0] < 0`, we also set `dp[0][0][1] = 0` (neutralising the first robber).

4. **Answer**  
   `max(dp[m-1][n-1][0], dp[m-1][n-1][1], dp[m-1][n-1][2])`

5. **Complexities**  
   *Time* – `O(m × n × 3)` ≈ `O(mn)` (≈ 3 × 250 000 = 750 k operations) – fast for 500×500.  
   *Space* – `O(m × n × 3)` ≈ `O(mn)` (~ 4 MB).  
   We can also shrink to 2 rows (`O(2 × n × 3)`), but the full 3‑D array keeps the code simple.

---

## 🧩 Code Implementations

Below are clean, well‑commented implementations in **Java, Python, and C++**.  
Feel free to copy‑paste them into your IDE and run your own tests.

### 1️⃣ Java (LeetCode style)

```java
class Solution {
    public int maximumAmount(int[][] coins) {
        int m = coins.length, n = coins[0].length;
        final int NEG = Integer.MIN_VALUE / 4;          // safe negative sentinel

        // dp[i][j][k] : max coins at (i,j) after using k neutralisations
        int[][][] dp = new int[m][n][3];
        for (int i = 0; i < m; i++)
            for (int j = 0; j < n; j++)
                for (int k = 0; k < 3; k++)
                    dp[i][j][k] = NEG;

        // start cell
        dp[0][0][0] = coins[0][0];
        if (coins[0][0] < 0) dp[0][0][1] = 0;          // neutralise first robber

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (i == 0 && j == 0) continue;      // already initialised

                for (int k = 0; k < 3; k++) {
                    // from top
                    if (i > 0) {
                        // 1. move without neutralising
                        dp[i][j][k] = Math.max(dp[i][j][k],
                            dp[i-1][j][k] + coins[i][j]);

                        // 2. move and neutralise this cell
                        if (k > 0) {
                            int val = dp[i-1][j][k-1];
                            val += (coins[i][j] >= 0) ? coins[i][j] : 0;
                            dp[i][j][k] = Math.max(dp[i][j][k], val);
                        }
                    }

                    // from left
                    if (j > 0) {
                        dp[i][j][k] = Math.max(dp[i][j][k],
                            dp[i][j-1][k] + coins[i][j]);

                        if (k > 0) {
                            int val = dp[i][j-1][k-1];
                            val += (coins[i][j] >= 0) ? coins[i][j] : 0;
                            dp[i][j][k] = Math.max(dp[i][j][k], val);
                        }
                    }
                }
            }
        }

        return Math.max(dp[m-1][n-1][0],
               Math.max(dp[m-1][n-1][1], dp[m-1][n-1][2]));
    }
}
```

---

### 2️⃣ Python 3

```python
class Solution:
    def maximumAmount(self, coins: List[List[int]]) -> int:
        m, n = len(coins), len(coins[0])
        NEG = -10**15

        dp = [[[NEG] * 3 for _ in range(n)] for _ in range(m)]

        # start cell
        dp[0][0][0] = coins[0][0]
        if coins[0][0] < 0:
            dp[0][0][1] = 0          # neutralise first robber

        for i in range(m):
            for j in range(n):
                if i == 0 and j == 0:
                    continue

                for k in range(3):
                    # from top
                    if i > 0:
                        dp[i][j][k] = max(dp[i][j][k],
                                          dp[i-1][j][k] + coins[i][j])
                        if k > 0:
                            val = dp[i-1][j][k-1] + \
                                  (coins[i][j] if coins[i][j] >= 0 else 0)
                            dp[i][j][k] = max(dp[i][j][k], val)

                    # from left
                    if j > 0:
                        dp[i][j][k] = max(dp[i][j][k],
                                          dp[i][j-1][k] + coins[i][j])
                        if k > 0:
                            val = dp[i][j-1][k-1] + \
                                  (coins[i][j] if coins[i][j] >= 0 else 0)
                            dp[i][j][k] = max(dp[i][j][k], val)

        return max(dp[m-1][n-1])
```

---

### 3️⃣ C++ (LeetCode style)

```cpp
class Solution {
public:
    int maximumAmount(vector<vector<int>>& coins) {
        int m = coins.size(), n = coins[0].size();
        const int NEG = -1e9;                       // safe negative sentinel

        // dp[i][j][k] – max coins at (i,j) after using k neutralisations
        vector<vector<array<int,3>>> dp(m, vector<array<int,3>>(n, {NEG,NEG,NEG}));

        dp[0][0][0] = coins[0][0];
        if (coins[0][0] < 0) dp[0][0][1] = 0;        // neutralise start

        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (i == 0 && j == 0) continue;    // already init

                for (int k = 0; k < 3; ++k) {
                    // from top
                    if (i > 0) {
                        dp[i][j][k] = max(dp[i][j][k],
                                          dp[i-1][j][k] + coins[i][j]);

                        if (k > 0) {
                            int val = dp[i-1][j][k-1];
                            val += (coins[i][j] >= 0 ? coins[i][j] : 0);
                            dp[i][j][k] = max(dp[i][j][k], val);
                        }
                    }

                    // from left
                    if (j > 0) {
                        dp[i][j][k] = max(dp[i][j][k],
                                          dp[i][j-1][k] + coins[i][j]);

                        if (k > 0) {
                            int val = dp[i][j-1][k-1];
                            val += (coins[i][j] >= 0 ? coins[i][j] : 0);
                            dp[i][j][k] = max(dp[i][j][k], val);
                        }
                    }
                }
            }
        }

        return max({dp[m-1][n-1][0], dp[m-1][n-1][1], dp[m-1][n-1][2]});
    }
};
```

> **Tip** – If you want to shave memory, replace `dp` with two rows (`dp[2][n][3]`) and roll the indices.

---

## 🎯 What Makes This Solution “Good” (the *✅* part)

| Category | ✅ | ❌ |
|----------|-----|-----|
| **Correctness** | ✔️ The DP covers *all* possible uses of neutralisers. | ❌ A naive recursion that tries every path explodes exponentially (`O(2^mn)`). |
| **Time‑Efficiency** | ✔️ ~ 750 k operations for 500×500 – easily under 0.1 s on LeetCode. | ❌ Brute‑force `O(3^mn)` or `O(mn²)` solutions hit the TL. |
| **Memory‑Footprint** | ✔️ ~ 4 MB (full 3‑D array) – well below LeetCode’s 256 MB limit. | ❌ Unbounded recursion or memoisation without pruning uses too much stack and memory. |
| **Scalability** | ✔️ The DP can be reduced to **two rows** (`O(2×n×3)`) for extreme memory‑constrained environments. | ❌ Trying to optimise at the cost of readability can make the interview explanation hard. |

---

## 🚨 The “Ugly” Parts – Common Pitfalls to Avoid

1. **Deep recursion without memoisation** – gets a *RuntimeError* or *Time Limit Exceeded* in Python/LeetCode.  
2. **Storing four integers per cell** (`k=0,1,2`) in a gigantic 3‑D vector and never pruning leads to ~ 1 GB memory (in languages with high overhead).  
3. **Forgetting to neutralise a negative cell** – you’ll miss the 2‑neutralisation budget and return a sub‑optimal answer.

**Bottom line:** Stick to the 3‑D DP described above; it’s simple, fast, and demonstrates clear logic to an interviewer.

---

## 🎉 Bonus: A Test Harness (Python)

```python
if __name__ == "__main__":
    s = Solution()
    print(s.maximumAmount([[0, 1], [-2, -3]]))  # → 1
    print(s.maximumAmount([[1, -5], [0, 2]]))  # → 3
    # Add your own grids here!
```

---

## 🚀 Ready to nail the interview?

* Walk through the **state definition** (`dp[i][j][k]`).
* Explain the **transition logic** – especially the “use a neutraliser” case.
* Highlight the **initialisation** trick for the first cell.
* Mention the **time/space trade‑off** and possible 2‑row optimisation.
* Show one of the clean code snippets and be prepared to **debug** on the spot.

If you can do that, you’ve proven you understand dynamic programming, resource constraints, and large‑scale input handling – all skills that recruiters are looking for in a senior software engineer. 🚀

Happy coding! 🎉