---
title: LeetCode 2912. Number of Ways to Reach Destination in the Grid - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 LeetCode 2912 – *Number of Ways to Reach Destination in the Grid*  
### The good, the bad, and the ugly  
*Dynamic‑Programming, Grid, Counting, Interview Prep, Java / Python / C++*  

---

### 1. Problem Recap  

You’re given a **1‑indexed** grid of size `n × m` ( `2 ≤ n,m ≤ 10⁹` ), a source cell `source = [x₁, y₁]` and a destination cell `dest = [x₂, y₂]`.  
In one move you can jump from cell `[x₁, y₁]` to `[x₂, y₂]` **iff** the two cells share the same **row** or the same **column** (but not the same cell).  

**Goal:** Count the number of distinct sequences of exactly `k` moves that end on `dest`.  
Return the answer modulo `1 000 000 007`.  
`1 ≤ k ≤ 10⁵`.

> **Why this matters for interviews**  
> 1. Classic DP on a grid with *restricted* moves.  
> 2. Requires clever state compression – a single DP array of size 4 works.  
> 3. Demonstrates handling of large grid dimensions (only algebraic formulas matter).  

---

### 2. The Good  

| ✅ | Reason |
|---|--------|
| **Linear time** – `O(k)` – 100 000 iterations is trivial. |
| **O(1) memory** – only 4 counters are needed. |
| **Mathematically clean** – transition matrix is derived once. |
| **Language‑agnostic** – the same DP works in Java, Python, C++. |
| **No need for matrix exponentiation** – because `k` is at most 10⁵. |

---

### 3. The Bad  

| ⚠️ | Pitfall |
|----|---------|
| `n` and `m` can be as big as `10⁹`, so you **must** use 64‑bit arithmetic before taking the modulo. |
| `k = 1` is a *special* case – you cannot stay on the same cell. |
| Off‑by‑one mistakes in the transition counts (e.g. `n‑1` vs `n‑2`) are easy to make. |
| Overflow in intermediate multiplication (`long * long` in Java / `int * int` in C++). |

---

### 4. The Ugly  

| 👿 | Mistake | Fix |
|---|---------|-----|
| **Staying on the same cell** | Allowed by “same row / same column” rule only if you *move* somewhere else. | The transition from “at dest” to “at dest” must be `0`. |
| **Counting “out” cells** | `m+n-4` is the number of cells that share **neither** row nor column with `dest`. | Derive carefully: total cells in dest‑row & dest‑col (`m + n - 2`) minus the two “special” cells (the ones that are in the same row or column as dest but not dest itself). |
| **Modulo multiplication overflow** | In Java/C++ `int * int` overflows before we can apply `% MOD`. | Promote to `long` (Java) or `long long` (C++) before multiplying. |
| **Negative intermediate values** | Subtractions like `m-2` can become `-1` if `m=2`. | `m ≥ 2`, so `m-2` is always non‑negative. |
| **Edge case `k = 1`** | The DP initialized with the source would incorrectly report `1` when `source == dest`. | Handle `k = 1` explicitly: answer is `1` iff `source` and `dest` share a row or column **and** they’re not the same cell. |

---

### 5. The Ugly‑ish – What to watch out for while coding  

| 😱 | What can go wrong |
|---|-------------------|
| **Wrong transition counts** – double‑counting or missing a path leads to wrong answer. |
| **Integer overflow before modulo** – especially in Java (`int` is 32‑bit). |
| **Wrong initial state** – misclassifying the source as “out” when it’s actually on the same row. |
| **Performance hit from nested loops** – 4×4×k is fine, but using a naive nested loop for every `k` step can be slower in Python if not written efficiently. |
| **Forgot to handle `k = 1`** – many contestants output 1 for `source == dest`. |

---

## 3. Core Insight – 4 “Cell‑Types”  

Instead of thinking of *every* cell in the grid, we only care about its *relationship* to the destination:

| Type | Condition | Symbol |
|------|-----------|--------|
| **Out** – shares **neither** row nor column with `dest` | `x ≠ x₂` **and** `y ≠ y₂` | 0 |
| **Row** – same row as `dest`, different column | `x = x₂` **and** `y ≠ y₂` | 1 |
| **Col** – same column as `dest`, different row | `y = y₂` **and** `x ≠ x₂` | 2 |
| **At** – exactly the destination | `x = x₂` **and** `y = y₂` | 3 |

Only **four** counters are required: `cntOut, cntRow, cntCol, cntAt`.  
After each move all counters are updated simultaneously using a fixed transition matrix.

---

### 4. Transition Matrix  

Let `MOD = 1 000 000 007`.  
Let `i` be the current state, `j` the next state.

| From\To | Out | Row | Col | At |
|---------|-----|-----|-----|----|
| **Out** | `n+m-4` | `1` | `1` | `0` |
| **Row** | `n-1` | `m-2` | `0` | `1` |
| **Col** | `m-1` | `0` | `n-2` | `1` |
| **At** | `0` | `m-1` | `n-1` | `0` |

**Why these numbers?**  

* From a cell *outside* of dest’s row/col you can:
  * jump to any other “outside” cell – there are `(m-1)+(n-1)-2 = n+m-4` such cells,
  * jump to the only cell in the same row as dest – **1**,
  * jump to the only cell in the same column as dest – **1**.
* From a cell *in the same row* as dest:
  * jump to any other cell in that row – `n-1` “outside” cells,
  * stay in the same row but avoid dest – `m-2` cells,
  * jump straight to dest – **1**.
* The remaining two cases are symmetric.
* From the destination you can only leave – no “stay” transition.

All numbers are **derived algebraically** from `n` and `m` and are safe to compute with 64‑bit integers even for `n,m = 10⁹`.

---

### 5. DP Iteration  

```
dp[i]  – number of ways to be in state i after the current number of moves
next[i] – same but after one additional move
```

```
for step = 1 … k-1
        next[0…3] = 0
        for i in 0..3
                for j in 0..3
                        next[j] = (next[j] + dp[i] * T[i][j]) mod MOD
        dp = next
answer = dp[At]   // number of ways that end on dest after k moves
```

*Time* : `O(4·4·k) ≈ O(k)`  
*Memory*: `O(4)` counters → `O(1)`  

---

### 6. Special Handling for `k = 1`  

When `k == 1` we cannot stay on the same cell.  
The only valid paths are the direct jumps that share a row **or** a column:

```text
answer = 1 if (source[0] == dest[0] and source[1] != dest[1])  // same row
         or (source[0] != dest[0] and source[1] == dest[1])    // same column
         else 0
```

---

## 7. Reference Implementations  

Below are fully‑commented solutions for **Java, Python, and C++** that follow the exact same DP logic.

---

### 7.1 Java 17  

```java
public class Solution {
    private static final long MOD = 1_000_000_007L;

    // state indices
    private static final int OUT = 0, ROW = 1, COL = 2, AT = 3;

    public int numberOfWays(int n, int m, int k, int[] source, int[] dest) {
        /* -------------- 1. Special case k == 1 ----------------- */
        if (k == 1) {
            boolean sameRow = source[0] == dest[0] && source[1] != dest[1];
            boolean sameCol = source[1] == dest[1] && source[0] != dest[0];
            return (sameRow || sameCol) ? 1 : 0;
        }

        /* -------------- 2. Transition matrix ------------------- */
        long[][] T = new long[4][4];

        // From OUT
        T[OUT][OUT] = n + m - 4;
        T[OUT][ROW] = 1;
        T[OUT][COL] = 1;
        // T[OUT][AT] stays 0

        // From ROW
        T[ROW][OUT] = n - 1;
        T[ROW][ROW] = m - 2;
        T[ROW][AT]  = 1;

        // From COL
        T[COL][OUT] = m - 1;
        T[COL][COL] = n - 2;
        T[COL][AT]  = 1;

        // From AT (destination)
        T[AT][ROW] = m - 1;
        T[AT][COL] = n - 1;
        // T[AT][AT] stays 0

        /* -------------- 3. Initial DP vector ------------------- */
        long[] dp = new long[4];
        if (source[0] == dest[0] && source[1] == dest[1]) {
            dp[AT] = 1;
        } else if (source[0] == dest[0]) {
            dp[ROW] = 1;
        } else if (source[1] == dest[1]) {
            dp[COL] = 1;
        } else {
            dp[OUT] = 1;
        }

        /* -------------- 4. DP iterations ----------------------- */
        for (int step = 1; step < k; step++) {
            long[] next = new long[4];
            for (int i = 0; i < 4; i++) {
                for (int j = 0; j < 4; j++) {
                    if (T[i][j] == 0) continue;          // skip zero entries
                    next[j] = (next[j] + dp[i] * T[i][j]) % MOD;
                }
            }
            dp = next;
        }

        /* -------------- 5. Result -------------------------------- */
        return (int) dp[AT];
    }
}
```

---

### 7.2 Python 3.10  

```python
class Solution:
    MOD = 10**9 + 7
    OUT, ROW, COL, AT = 0, 1, 2, 3

    def numberOfWays(self, n: int, m: int, k: int,
                     source: List[int], dest: List[int]) -> int:
        # ---------- k == 1 special handling ----------
        if k == 1:
            same_row = source[0] == dest[0] and source[1] != dest[1]
            same_col = source[1] == dest[1] and source[0] != dest[0]
            return int(same_row or same_col)

        # ---------- Transition matrix ----------
        T = [[0] * 4 for _ in range(4)]

        # From OUT
        T[self.OUT][self.OUT] = n + m - 4
        T[self.OUT][self.ROW] = 1
        T[self.OUT][self.COL] = 1

        # From ROW
        T[self.ROW][self.OUT] = n - 1
        T[self.ROW][self.ROW] = m - 2
        T[self.ROW][self.AT]  = 1

        # From COL
        T[self.COL][self.OUT] = m - 1
        T[self.COL][self.COL] = n - 2
        T[self.COL][self.AT]  = 1

        # From AT
        T[self.AT][self.ROW] = m - 1
        T[self.AT][self.COL] = n - 1

        # ---------- Initial vector ----------
        dp = [0] * 4
        if source[0] == dest[0] and source[1] == dest[1]:
            dp[self.AT] = 1
        elif source[0] == dest[0]:
            dp[self.ROW] = 1
        elif source[1] == dest[1]:
            dp[self.COL] = 1
        else:
            dp[self.OUT] = 1

        # ---------- DP iterations ----------
        for _ in range(1, k):          # we already handled step 1
            nxt = [0] * 4
            for i in range(4):
                if dp[i] == 0:
                    continue
                for j in range(4):
                    if T[i][j] == 0:
                        continue
                    nxt[j] = (nxt[j] + dp[i] * T[i][j]) % self.MOD
            dp = nxt

        return int(dp[self.AT])
```

---

### 7.3 C++20  

```cpp
class Solution {
public:
    static constexpr long long MOD = 1'000'000'007LL;
    // state indices
    static constexpr int OUT = 0, ROW = 1, COL = 2, AT = 3;

    int numberOfWays(int n, int m, int k, vector<int>& source, vector<int>& dest) {
        /* ---------- k == 1 special case ---------- */
        if (k == 1) {
            bool sameRow = source[0] == dest[0] && source[1] != dest[1];
            bool sameCol = source[1] == dest[1] && source[0] != dest[0];
            return (sameRow || sameCol) ? 1 : 0;
        }

        /* ---------- transition matrix ---------- */
        long long T[4][4] = {};          // zero-initialised
        T[OUT][OUT] = (long long)n + m - 4;
        T[OUT][ROW] = 1;
        T[OUT][COL] = 1;

        T[ROW][OUT] = n - 1;
        T[ROW][ROW] = (long long)m - 2;
        T[ROW][AT]  = 1;

        T[COL][OUT] = m - 1;
        T[COL][COL] = n - 2;
        T[COL][AT]  = 1;

        T[AT][ROW] = (long long)m - 1;
        T[AT][COL] = (long long)n - 1;

        /* ---------- initial DP vector ---------- */
        long long dp[4] = {0,0,0,0};
        if (source[0] == dest[0] && source[1] == dest[1]) dp[AT] = 1;
        else if (source[0] == dest[0]) dp[ROW] = 1;
        else if (source[1] == dest[1]) dp[COL] = 1;
        else dp[OUT] = 1;

        /* ---------- DP iterations ---------- */
        for (int step = 1; step < k; ++step) {
            long long nxt[4] = {0,0,0,0};
            for (int i = 0; i < 4; ++i) {
                if (dp[i] == 0) continue;
                for (int j = 0; j < 4; ++j) {
                    if (T[i][j] == 0) continue;
                    nxt[j] = (nxt[j] + dp[i] * T[i][j]) % MOD;
                }
            }
            memcpy(dp, nxt, sizeof(dp));
        }
        return (int)dp[AT];
    }
};
```

---

## 8. Final Thoughts  

* The solution boils down to **four counters** and a **fixed** transition matrix.  
* All path counts are expressed as simple algebraic functions of `n` and `m`.  
* Careful handling of `k = 1` and promotion to 64‑bit types guarantees correctness.

With this understanding, you can implement the solution in any language without falling into the typical pitfalls. Happy coding! 🚀

--- 

### 9. Further Reading (Optional)

* **Dynamic Programming on Graphs** – why state reduction works.
* **Algebraic counting of paths** – see combinatorial interpretations of the transition counts.
* **Modular Arithmetic in 64‑bit** – pitfalls in languages like Java/C++/Python.

--- 

*Happy Problem Solving!*