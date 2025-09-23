---
title: LeetCode 799. Champagne Tower - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Champagne Tower – LeetCode 799  
**A Medium‑Level DP Problem that Impresses Recruiters**

> **Keywords** – LeetCode, Champagne Tower, dynamic programming, interview problem, Java, Python, C++, algorithm, coding interview, job interview, coding challenge.

---

## 1️⃣ Problem Overview

On LeetCode problem **799 – Champagne Tower** you are given a pyramid of glasses:

- Row 0 has 1 glass, row 1 has 2 glasses, … row 99 has 100 glasses.  
- Each glass can hold exactly **1 unit** of champagne.  
- You pour `poured` cups into the top glass.  
- If a glass overflows, the excess is split **evenly** between the two glasses directly below it.  
- Anything that overflows out of the bottom row just drips onto the floor.

> **Goal** – After all the champagne has been poured, return how full (0–1) the glass at `(query_row, query_glass)` is.

> **Constraints**  
> ```
> 0 ≤ poured ≤ 10^9
> 0 ≤ query_row < 100
> 0 ≤ query_glass ≤ query_row
> ```

> **Example**  
> poured = 2, query_row = 1, query_glass = 1 → **0.5**

---

## 2️⃣ Why Recruiters Love This Problem

| Reason | Why it matters |
|--------|----------------|
| **Dynamic Programming (DP)** | Shows you can model a real‑world “flow” problem with DP. |
| **Simulation + Overflow** | Demonstrates careful handling of floating‑point arithmetic and edge cases. |
| **Time/Space O(r²)** | Even though constraints are tiny, you still show an optimal solution. |
| **Language‑agnostic** | Solved easily in Java, Python, or C++ – great for interviews. |

If you can explain your solution and present clean, readable code, you’ll get a “thumbs‑up” in any technical interview.

---

## 3️⃣ The Core Idea – Row‑by‑Row Simulation

Think of the tower as **Pascal’s Triangle** where each entry is the amount of champagne that arrives at that glass.  
The process:

1. **Initialize** `tower[0][0] = poured`.
2. For every glass `tower[r][c]` in row `r` (up to `query_row`):
   - If `tower[r][c] > 1`, compute the **excess**:  
     ```excess = (tower[r][c] - 1) / 2.0```
   - Add that excess to the two glasses below:  
     ```tower[r+1][c]   += excess```  
     ```tower[r+1][c+1] += excess```
3. After simulation, the answer is `min(1.0, tower[query_row][query_glass])`.

> **Why min(1.0)?**  
> A glass can never hold more than one unit. The simulation may compute a higher value if the incoming flow is huge; we clamp it to 1.

---

## 4️⃣ Complexity Analysis

- **Time** – We visit each glass up to `query_row`.  
  \[
  T = 1 + 2 + \dots + (\text{query_row}+1) = O(\text{query_row}^2)
  \]  
  With `query_row ≤ 99`, this is trivial (≤ 5 000 operations).

- **Space** – A 2‑D array of size `(query_row+1) × (query_row+1)` → `O(query_row^2)`  
  We can reduce it to a 1‑D array of size `query_row+1` if desired, but readability wins here.

---

## 5️⃣ Edge‑Case “Gotchas”

| Edge case | Why it matters | Fix |
|-----------|----------------|-----|
| **poured = 0** | All glasses stay empty. | Return 0 early. |
| **query_glass = 0 or query_glass = query_row** | First/last glass receives only half of the incoming overflow. | Handled automatically by DP. |
| **Large poured (10⁹)** | Overflow values become huge; use `double`. | Use `double` to avoid integer overflow. |
| **Floating‑point precision** | Return result with 5 decimal places. | Use `Math.min(1.0, val)` and format accordingly. |

---

## 6️⃣ Implementation – Three Languages

### 6.1 Java

```java
public class Solution {
    public double champagneTower(int poured, int query_row, int query_glass) {
        if (poured == 0) return 0.0;

        double[][] tower = new double[query_row + 2][query_row + 2];
        tower[0][0] = poured;

        for (int r = 0; r < query_row; r++) {
            for (int c = 0; c <= r; c++) {
                double excess = (tower[r][c] - 1.0) / 2.0;
                if (excess > 0) {
                    tower[r + 1][c]     += excess;
                    tower[r + 1][c + 1] += excess;
                }
            }
        }

        return Math.min(1.0, tower[query_row][query_glass]);
    }
}
```

> **Why 2‑D?** Keeps the code readable. The array is tiny (≤ 102 × 102).

---

### 6.2 Python

```python
class Solution:
    def champagneTower(self, poured: int, query_row: int, query_glass: int) -> float:
        if poured == 0:
            return 0.0

        # initialize (query_row+2) rows for easier index handling
        tower = [[0.0] * (query_row + 2) for _ in range(query_row + 2)]
        tower[0][0] = poured

        for r in range(query_row):
            for c in range(r + 1):
                excess = (tower[r][c] - 1.0) / 2.0
                if excess > 0:
                    tower[r + 1][c]     += excess
                    tower[r + 1][c + 1] += excess

        return min(1.0, tower[query_row][query_glass])
```

> **Python tip** – The list comprehension builds a clean 2‑D matrix.

---

### 6.3 C++

```cpp
class Solution {
public:
    double champagneTower(int poured, int query_row, int query_glass) {
        if (poured == 0) return 0.0;

        vector<vector<double>> tower(query_row + 2,
                                    vector<double>(query_row + 2, 0.0));
        tower[0][0] = poured;

        for (int r = 0; r < query_row; ++r) {
            for (int c = 0; c <= r; ++c) {
                double excess = (tower[r][c] - 1.0) / 2.0;
                if (excess > 0) {
                    tower[r + 1][c]     += excess;
                    tower[r + 1][c + 1] += excess;
                }
            }
        }

        return min(1.0, tower[query_row][query_glass]);
    }
};
```

> **Why `vector<vector<double>>`?**  
> The C++ STL makes dynamic allocation straightforward, and the size is still < 200 × 200.

---

## 7️⃣ A “Gotcha‑Free” One‑Dimensional Variant (Optional)

If you want to impress recruiters that you can *optimize* memory, here’s the 1‑D trick (Java‑style):

```java
double[] dp = new double[query_row + 2];
dp[0] = poured;
for (int r = 0; r < query_row; r++) {
    for (int c = r; c >= 0; c--) {          // iterate backward!
        double excess = (dp[c] - 1.0) / 2.0;
        if (excess > 0) {
            dp[c]     += excess;
            dp[c + 1] += excess;
        }
    }
}
return Math.min(1.0, dp[query_glass]);
```

*Backwards iteration* guarantees that the current row’s values are not overwritten before they’re used.

---

## 7️⃣ How to Present This in an Interview

1. **State the assumptions** – e.g., “We’ll simulate row by row, clamping any glass to a maximum of 1.”
2. **Draw a quick diagram** – Show a small 3‑row tower and how overflow flows.
3. **Explain the DP recurrence** – Overflow = `(current‑amount – 1) / 2`.
4. **Walk through a sample** – Use the 2‑D array to illustrate the calculations.
5. **Mention complexity** – O(r²) time, O(r²) space; trivial for r < 100.
6. **Talk about edge cases** – early exit for `poured == 0`, floating‑point precision, clamping to 1.

> **Tip** – Keep the answer to 5 decimals: `System.out.printf("%.5f\n", ans);` (Java) or `format(ans, '.5f')` (Python).

---

## 8️⃣ Takeaway for Your Next Technical Interview

- **Understand the flow** – It’s not just “add 1”; it’s *how much overflows*.
- **DP + Simulation** – A perfect blend of algorithmic thinking and careful implementation.
- **Language‑agnostic proof** – Show the same logic in Java, Python, and C++ to prove versatility.
- **Clarity > Micro‑optimization** – Recruiters care more about clean logic than squeezing one byte.

> **Pro‑tip:** When you write your own LeetCode solution, add a comment at the top with the problem link and a short description. It signals that you’re organized and prepared.

---

## 🎯 SEO‑Ready Summary

> *“Champagne Tower”* is a common LeetCode interview challenge that tests dynamic programming, simulation, and edge‑case handling. The problem is solvable in Java, Python, or C++ with a simple row‑by‑row DP. This article walks you through the logic, analyzes complexity, lists pitfalls, and provides clean code snippets. Use this as a talking point in your next coding interview, and you’ll demonstrate strong algorithmic chops that recruiters love.

---