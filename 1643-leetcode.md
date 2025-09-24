---
title: LeetCode 1643. Kth Smallest Instructions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📚 LeetCode 1643 – “Kth Smallest Instruction”  
*From the easy “Unique Paths” to a hard combinatorial challenge*  

---

### 1️⃣ Problem Recap  

You’re standing in the top‑left corner of an `R × C` grid.  
You may only move **right** (`H`) or **down** (`V`).  
All sequences that use exactly `R` `V`s and `C` `H`s will bring you to the destination `(R,C)`.

The sequences are sorted **lexicographically** (`H` < `V`).  
Given a 1‑indexed `k`, return the *kth* lexicographically smallest sequence.

```
destination = [2, 3], k = 1   →   "HHHVV"
destination = [2, 3], k = 3   →   "HHVVH"
```

Constraints  
`1 ≤ R, C ≤ 15` – small enough for a DP table but large enough that a brute‑force combinatorial explosion is impossible.

---

## 2️⃣ Why Dynamic Programming Wins  
*The key insight:*  
At any cell `(i, j)` the number of remaining paths to the goal equals the sum of the paths from the cell to the right `(i, j+1)` and the cell below `(i+1, j)` – the classic “Unique Paths” recurrence.

With that table in hand we can **walk** the grid:

1. If the *k*th path starts with a **right** move, it must belong to the first `dp[i][j+1]` lexicographically smallest paths.  
2. Otherwise the path starts with a **down** move and we subtract those first `dp[i][j+1]` paths from *k* and continue.

This greedy walk runs in `O(R+C)` time once the DP table is built.  
Memory is `O(R*C)` – comfortably small for the limits.

---

## 3️⃣ Implementation Highlights  

| Language | Key Points |
|----------|------------|
| **Java** | Use `long` to hold path counts (`C(30,15)` ≈ 155 117 520 < 2³¹). Build DP bottom‑up, then walk the grid. |
| **Python** | `int` is unbounded – same DP logic. `append()` to a list, finally `''.join()`. |
| **C++** | `unsigned long long` (64‑bit) suffices. Use `vector<vector<unsigned long long>>`. |

> **Good** – O(R·C) time, O(R·C) memory, simple to understand.  
> **Bad** – For much larger grids this would become memory‑heavy; also `long` in Java is 64‑bit but we stay far below overflow.  
> **Ugly** – A naïve combinatorial approach that tries to generate all paths or uses factorial formulas quickly overflows or becomes `O(k)` in time.

---

## 4️⃣ Code Snippets  

### 📜 Java  
```java
import java.util.*;

public class Solution {
    public String kthSmallestPath(int[] destination, int k) {
        int R = destination[0] + 1;   // rows including start cell
        int C = destination[1] + 1;   // columns including start cell
        long[][] dp = new long[R][C];

        // last row and column have only one way to finish
        for (int i = 0; i < R; i++) dp[i][C-1] = 1;
        for (int j = 0; j < C; j++) dp[R-1][j] = 1;

        // fill DP bottom‑up
        for (int i = R-2; i >= 0; i--) {
            for (int j = C-2; j >= 0; j--) {
                dp[i][j] = dp[i+1][j] + dp[i][j+1];
            }
        }

        StringBuilder sb = new StringBuilder();
        int i = 0, j = 0;
        while (i < R-1 && j < C-1) {
            long rightPaths = dp[i][j+1];
            if (k <= rightPaths) {          // go right
                sb.append('H');
                j++;
            } else {                        // go down
                k -= rightPaths;
                sb.append('V');
                i++;
            }
        }
        // finish the remaining straight line
        sb.append("V".repeat(R-1-i));
        sb.append("H".repeat(C-1-j));
        return sb.toString();
    }
}
```

### 📜 Python  
```python
class Solution:
    def kthSmallestPath(self, destination: list[int], k: int) -> str:
        R, C = destination[0] + 1, destination[1] + 1
        dp = [[0] * C for _ in range(R)]

        # base cases
        for i in range(R): dp[i][C-1] = 1
        for j in range(C): dp[R-1][j] = 1

        # build DP bottom‑up
        for i in range(R-2, -1, -1):
            for j in range(C-2, -1, -1):
                dp[i][j] = dp[i+1][j] + dp[i][j+1]

        # walk the grid
        i = j = 0
        path = []
        while i < R-1 and j < C-1:
            if k <= dp[i][j+1]:     # go right
                path.append('H')
                j += 1
            else:                   # go down
                k -= dp[i][j+1]
                path.append('V')
                i += 1

        # add the final straight segment
        path.append('V' * (R-1-i))
        path.append('H' * (C-1-j))
        return ''.join(path)
```

### 📜 C++  
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string kthSmallestPath(vector<int>& destination, int k) {
        int R = destination[0] + 1;
        int C = destination[1] + 1;
        vector<vector<unsigned long long>> dp(R, vector<unsigned long long>(C, 1));

        // last row / column already 1
        for (int i = R-2; i >= 0; --i) {
            for (int j = C-2; j >= 0; --j) {
                dp[i][j] = dp[i+1][j] + dp[i][j+1];
            }
        }

        string ans;
        int i = 0, j = 0;
        while (i < R-1 && j < C-1) {
            if (k <= dp[i][j+1]) {          // go right
                ans.push_back('H');
                ++j;
            } else {                        // go down
                k -= dp[i][j+1];
                ans.push_back('V');
                ++i;
            }
        }
        ans.append(R-1-i, 'V');
        ans.append(C-1-j, 'H');
        return ans;
    }
};
```

> All three solutions are `O(R*C)` time, `O(R*C)` memory, and work for the maximum grid size (`15 × 15`).

---

## 5️⃣ Complexity Analysis  

| Operation | Java | Python | C++ |
|-----------|------|--------|-----|
| **Time**  | `O(R·C + R+C)` ≈ `O(R·C)` | same | same |
| **Memory**| `O(R·C)` | `O(R·C)` | `O(R·C)` |
| **Overflow risk** | negligible (`dp[30][15] < 2^63`) | none (`int` unbounded) | safe with `unsigned long long` |

---

## 6️⃣ Edge‑Case & “What If” Checklist  

| Edge Case | Handling |
|-----------|----------|
| `k` equals the total number of paths | Walk will always go right until forced down, producing the last string. |
| `k` equals 1 | Immediately always take right moves, giving the first row of `H`s followed by all `V`s. |
| Destination `[0, 0]` | `R = C = 1`, DP is a single cell, loop never runs; we append an empty string. |
| `k` > `dp[0][0]` | Problem guarantees valid `k`; otherwise we would throw an exception. |

---

## 7️⃣ Interview‑Ready Tips  

| Topic | Why it matters |
|-------|----------------|
| **Dynamic Programming Mastery** | Most hiring managers love clear DP solutions. Highlight the recurrence, base cases, and bottom‑up construction. |
| **Space Optimization** | Discuss how the DP can be reduced to `O(C)` if you only keep the current row, but for this problem the full table is fine. |
| **Combinatorics vs DP** | Show you understand why factorial‑based enumeration fails (overflow, time). |
| **Testing** | Generate random grids (≤15) and brute‑force check with `itertools.product` for small sizes. |
| **Coding Style** | Clear variable names (`rightPaths`, `dp`), single‑responsibility functions, and comments. |

> **Interview Hook**: “Can you find the kth path in an `R × C` grid without enumerating all possibilities?” – This question is a favorite in algorithmic interview rounds and appears in LeetCode 1643. Master it, and you’ll ace the DP portion of almost any coding test.

---

## 8️⃣ SEO‑Optimized Blog Article  

> **Meta Description**  
> “Learn how to solve LeetCode 1643 – Kth Smallest Instruction – with dynamic programming. Get the code in Java, Python, C++, and interview‑ready tips for job hunting.”

---

# Kth Smallest Instruction – LeetCode 1643 – A Complete Guide

## 🗒️ What is “Kth Smallest Instruction”?
LeetCode 1643 is a grid‑path combinatorics problem that challenges you to return the `kth` lexicographically smallest sequence of moves that leads from the top‑left corner to a grid destination using only right (`H`) and down (`V`) steps.

> **Keywords**: *Kth Smallest Instruction*, *LeetCode 1643*, *grid path combinatorics*, *dynamic programming interview question*, *grid path DP*, *unique paths*

## 🎯 Why It’s a Must‑Know Interview Problem  
- **Core Concepts**: Recurrence relations, combinatorics, lexicographic ordering.  
- **Coding Practice**: Perfect for practicing DP, string manipulation, and greedy decision making.  
- **Real‑World Application**: Navigation algorithms, robot motion planning, and combinatorial optimization.  

---

## 📑 Problem Statement (in a nutshell)

```
int[] destination = {R, C};   // R rows, C columns (1‑indexed)
int k;                        // 1‑indexed position in sorted path list
return kth lexicographically smallest sequence of 'H' and 'V'.
```

Constraints: `1 ≤ R, C ≤ 15`.

---

## 🔍 Approach: Bottom‑Up DP + Greedy Walk  

1. **DP Table**:  
   `dp[i][j] = dp[i+1][j] + dp[i][j+1]`  
   Base cases: last row/column → 1.

2. **Greedy Walk**:  
   At cell `(i, j)`, if `k` is ≤ `dp[i][j+1]`, the next move is `H`; otherwise `V` and subtract `dp[i][j+1]` from `k`.

3. **Finish Straight**:  
   Once you’re on the last row or column, append the remaining `V`s or `H`s.

---

## 🏁 Complexity  

- **Time**: `O(R · C)` for building DP + `O(R + C)` for walking = `O(R·C)`.  
- **Space**: `O(R · C)` (≤ 256 cells for the given limits).  

---

## 🛠️ Full Code (Java, Python, C++)

### Java  
```java
import java.util.*;

public class Solution {
    public String kthSmallestPath(int[] destination, int k) {
        int R = destination[0] + 1;
        int C = destination[1] + 1;
        long[][] dp = new long[R][C];

        for (int i = 0; i < R; i++) dp[i][C-1] = 1;
        for (int j = 0; j < C; j++) dp[R-1][j] = 1;

        for (int i = R-2; i >= 0; i--) {
            for (int j = C-2; j >= 0; j--) {
                dp[i][j] = dp[i+1][j] + dp[i][j+1];
            }
        }

        StringBuilder sb = new StringBuilder();
        int i = 0, j = 0;
        while (i < R-1 && j < C-1) {
            if (k <= dp[i][j+1]) {
                sb.append('H');
                j++;
            } else {
                k -= dp[i][j+1];
                sb.append('V');
                i++;
            }
        }
        sb.append("V".repeat(R-1-i));
        sb.append("H".repeat(C-1-j));
        return sb.toString();
    }
}
```

### Python  
```python
class Solution:
    def kthSmallestPath(self, destination: list[int], k: int) -> str:
        R, C = destination[0] + 1, destination[1] + 1
        dp = [[1] * C for _ in range(R)]

        for i in range(R-2, -1, -1):
            for j in range(C-2, -1, -1):
                dp[i][j] = dp[i+1][j] + dp[i][j+1]

        path = []
        i = j = 0
        while i < R-1 and j < C-1:
            if k <= dp[i][j+1]:
                path.append('H')
                j += 1
            else:
                k -= dp[i][j+1]
                path.append('V')
                i += 1

        path.append('V' * (R-1-i))
        path.append('H' * (C-1-j))
        return ''.join(path)
```

### C++  
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string kthSmallestPath(vector<int>& destination, int k) {
        int R = destination[0] + 1;
        int C = destination[1] + 1;
        vector<vector<unsigned long long>> dp(R, vector<unsigned long long>(C, 1));

        for (int i = R-2; i >= 0; --i) {
            for (int j = C-2; j >= 0; --j) {
                dp[i][j] = dp[i+1][j] + dp[i][j+1];
            }
        }

        string ans;
        int i = 0, j = 0;
        while (i < R-1 && j < C-1) {
            if (k <= dp[i][j+1]) {
                ans.push_back('H');
                ++j;
            } else {
                k -= dp[i][j+1];
                ans.push_back('V');
                ++i;
            }
        }
        ans.append(R-1-i, 'V');
        ans.append(C-1-j, 'H');
        return ans;
    }
};
```

---

## 9️⃣ Good, Bad, Ugly – What You Should Tell Interviewers

| Aspect | ✅ Good | ⚠️ Bad | 💔 Ugly |
|--------|---------|--------|---------|
| **Algorithmic Complexity** | `O(R·C)` DP + `O(R+C)` walk – trivial for interviewers. | For grids > 2000, DP table would exceed memory limits. | Trying to compute the kth path via full factorial enumeration (`O(2^(R+C))`) is both slow and prone to overflow. |
| **Implementation Detail** | Clear, testable code with comments; no magic numbers. | Hard‑coded assumptions (e.g., ignoring invalid `k`) could raise suspicion. | Recursive brute‑force without pruning is stack‑overflow and `O(2^(R+C))`. |
| **Edge‑Case Handling** | All corner cases covered; algorithm self‑checks. | If `k` is out of range, you need to throw an error. | Unchecked `k` leads to negative indexing or infinite loops. |

> **Interview Statement**  
> “We can find the kth path without enumerating all paths by building a DP table that counts remaining paths and then walking greedily through the grid, making decisions based on the remaining count.”

---

## 📢 Closing Thought

Mastering **LeetCode 1643 – Kth Smallest Instruction** showcases your ability to translate combinatorial problems into efficient dynamic‑programming solutions. The clean code examples in Java, Python, and C++ above will help you nail the coding portion, and the interview‑ready commentary ensures you can discuss your thought process confidently.

> **Ready for the next challenge?** Keep practicing DP, string manipulation, and greedy algorithms. Your future employer will thank you!  

--- 

> **Keywords** repeated for maximum reach: *Kth Smallest Instruction*, *LeetCode 1643*, *grid DP*, *dynamic programming interview*, *robot path planning*, *unique paths problem*, *coding interview question*.

--- 

### 🚀 Happy coding, and good luck with your job search!  

---


---

> **End of Blog**  
> Feel free to share on GitHub, StackOverflow, or coding communities – this solution set is the gold standard for LeetCode 1643.