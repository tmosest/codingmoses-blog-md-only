---
title: LeetCode 174. Dungeon Game - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🏰 Dungeon Game – LeetCode 174  
**Problem** | **Difficulty** | **Languages**  
--- | --- | ---  
[174. Dungeon Game](https://leetcode.com/problems/dungeon-game/) | Hard | Java, Python, C++

> A knight must traverse a 2‑D dungeon from the top‑left to the bottom‑right cell, moving only **right** or **down**.  
>  
> Each cell contains a negative value (demon damage), 0 (empty room) or a positive value (healing orb).  
> The knight’s health must **always stay ≥ 1**; otherwise he dies.  
> Return the *minimum* initial health the knight needs to rescue the princess in the bottom‑right room.

---

## 🛠️  Solution Overview

The problem is a classic **Dynamic Programming (DP)** problem – we need to know the *minimum* health required *before* entering a cell so that after the room’s effect the knight survives and can reach the goal.

Key observation  
*From any cell we only have two possible next steps: right or down.*  
Therefore the *required health* of a cell depends only on the *minimum required health* of the two adjacent cells to the right and below.

We work **backwards** from the princess’s cell (bottom‑right) to the starting cell (top‑left).  
For each cell `dp[i][j]`:

```
needed = min( dp[i+1][j], dp[i][j+1] )   // minimum health needed in the next step
dp[i][j] = max( 1, needed - dungeon[i][j] )   // heal or take damage
```

The boundary conditions are handled by treating out‑of‑bounds moves as `∞` (so they never become the minimum).

The answer is `dp[0][0]`.

### Time / Space Complexity
|  | Java / Python / C++ |
|---|---|
| **Time** | `O(m·n)` – one pass over the grid |
| **Space** | `O(m·n)` for the DP table (can be reduced to `O(n)` with a single row DP) |

---

## 📦  Code Implementations

Below are clean, well‑commented implementations for **Java, Python, and C++**.  
All three use a *bottom‑up* DP approach and are `O(m·n)` in both time and space.

### 1️⃣ Java

```java
import java.util.Arrays;

public class Solution {
    public int calculateMinimumHP(int[][] dungeon) {
        int m = dungeon.length, n = dungeon[0].length;
        int[][] dp = new int[m + 1][n + 1];          // extra row/col for easier boundary handling
        for (int[] row : dp) Arrays.fill(row, Integer.MAX_VALUE);
        dp[m][n-1] = dp[m-1][n] = 1;                // sentinel cells

        for (int i = m - 1; i >= 0; --i) {
            for (int j = n - 1; j >= 0; --j) {
                int need = Math.min(dp[i+1][j], dp[i][j+1]) - dungeon[i][j];
                dp[i][j] = Math.max(1, need);
            }
        }
        return dp[0][0];
    }
}
```

### 2️⃣ Python

```python
from typing import List

class Solution:
    def calculateMinimumHP(self, dungeon: List[List[int]]) -> int:
        m, n = len(dungeon), len(dungeon[0])
        # dp[i][j] : minimum health needed to enter cell (i, j)
        dp = [[float('inf')] * (n + 1) for _ in range(m + 1)]
        dp[m][n-1] = dp[m-1][n] = 1   # sentinel cells

        for i in range(m-1, -1, -1):
            for j in range(n-1, -1, -1):
                need = min(dp[i+1][j], dp[i][j+1]) - dungeon[i][j]
                dp[i][j] = max(1, need)

        return dp[0][0]
```

### 3️⃣ C++

```cpp
class Solution {
public:
    int calculateMinimumHP(vector<vector<int>>& dungeon) {
        int m = dungeon.size(), n = dungeon[0].size();
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, INT_MAX));
        dp[m][n-1] = dp[m-1][n] = 1;                 // sentinel

        for (int i = m - 1; i >= 0; --i) {
            for (int j = n - 1; j >= 0; --j) {
                int need = min(dp[i+1][j], dp[i][j+1]) - dungeon[i][j];
                dp[i][j] = max(1, need);
            }
        }
        return dp[0][0];
    }
};
```

> All three solutions run in **O(m·n)** time and use **O(m·n)** extra space.  
> They pass all official LeetCode test cases and can handle the maximum constraints (200 × 200).

---

## 📚  Blog Article – “The Good, the Bad, and the Ugly of the Dungeon Game”

> **Title:** *The Good, the Bad & the Ugly of LeetCode 174 – Dungeon Game (Java, Python, C++)*  
> **Meta Description:** Master LeetCode 174 “Dungeon Game” with clear Java, Python, & C++ solutions. Learn the dynamic‑programming trick, pitfalls, and how to ace the interview.  

---

### Introduction

If you’re preparing for coding interviews, **LeetCode 174 – Dungeon Game** is a classic problem that tests your grasp of dynamic programming (DP). It may look intimidating, but the underlying principle is simple: **work backwards** from the princess’s cell and compute the *minimum health* required to survive the path.

In this post, we dissect the solution, walk through three language implementations, and discuss what makes the problem *good*, what could go wrong (*bad*), and how to avoid nasty pitfalls (*ugly*). All with SEO‑friendly headings so recruiters find your expertise instantly!

---

### 1. Why This Problem Is a “Good” Interview Question

| Reason | Explanation |
|---|---|
| **Algorithmic Depth** | Requires knowledge of DP, boundary handling, and optimization tricks. |
| **Language Agnostic** | Same logic applies to Java, Python, C++, etc. |
| **Edge‑Case Rich** | Negative numbers, zeroes, varying grid sizes, and large values force careful handling of integer overflow. |
| **Test‑Case Variety** | LeetCode provides random large grids, which make brute‑force solutions fail instantly. |
| **Career‑Boosting** | Solving it shows mastery of DP, a staple in many interviews. |

---

### 2. The “Good” – The Clear, Clean DP Solution

- **Backward DP**: Start at the princess’s cell and propagate required health backwards.
- **Simplicity**: Two lines of recurrence, no recursion stack, no memoization overhead.
- **Deterministic Complexity**: `O(m·n)` time and space—well within limits for 200 × 200 grids.
- **Scalable**: Easily adapted to one‑dimensional DP (O(n) space) if needed.

**Takeaway**: If you can explain this backward approach in under 2 minutes, you’re ready for many interview questions.

---

### 3. The “Bad” – Common Pitfalls

| Mistake | Why It Breaks | Fix |
|---|---|---|
| **Top‑down recursion without memoization** | Leads to exponential time, stack overflow. | Use memoization or iterative DP. |
| **Using `0` as the initial sentinel** | `dp[i][j]` can become `0`, causing the knight to die immediately. | Use `INT_MAX` / `float('inf')` as sentinel and enforce `max(1, …)`. |
| **Wrong boundary conditions** | Off‑by‑one errors make the answer `-1` or huge. | Add an extra row/col initialized to `INF` and set `dp[m][n-1] = dp[m-1][n] = 1`. |
| **Integer overflow** | Subtracting large negative values can exceed 32‑bit int. | Use 64‑bit integers (`long` in Java, `long long` in C++). |
| **Ignoring the `-1` starting health requirement** | Some solutions return `dp[0][0] - 1`. | The recurrence already guarantees `≥ 1`; return `dp[0][0]`. |

---

### 4. The “Ugly” – Unnecessary Complexity and Hard‑to‑Read Code

- **Over‑engineering**: Adding unnecessary helper functions or classes.
- **Unclear variable names** (`a`, `b`, `c`) that obscure logic.
- **Missing comments**: Future you (or a recruiter) will have to guess intent.
- **Hard‑coded bounds** (`dp[201][201]` instead of dynamic sizing).

**Best Practice**: Keep code concise, comment the recurrence, and let the compiler handle edge cases.

---

### 5. Optimizing for Interviews & SEO

- **Use “Dynamic Programming”, “Bottom‑up DP”, “Minimum Health”** in headings and meta tags.
- **Highlight time complexity** (`O(m·n)`) in the first paragraph—search engines love quantified metrics.
- **Include a “Quick Recap”** section with bullet points for fast reading.
- **Add a code‑highlighted snippet** for each language—helps search bots index the code.

---

### 6. Code Snippets – The Quick Reference

| Language | Key Recurrence |
|---|---|
| **Java** | `dp[i][j] = Math.max(1, Math.min(dp[i+1][j], dp[i][j+1]) - dungeon[i][j]);` |
| **Python** | `dp[i][j] = max(1, min(dp[i+1][j], dp[i][j+1]) - dungeon[i][j])` |
| **C++** | `dp[i][j] = max(1, min(dp[i+1][j], dp[i][j+1]) - dungeon[i][j]);` |

Feel free to copy‑paste the full solutions above into your IDE.

---

### 7. Conclusion

LeetCode 174 – Dungeon Game is a *classic* DP problem that can be solved cleanly with a backward bottom‑up approach. Avoid the common pitfalls, keep your code readable, and practice explaining the recurrence in simple terms. Master this problem, and you’ll have a strong interview talking point that showcases your dynamic programming chops.

> **Ready to impress recruiters?** Drop the code into your portfolio, add the article to your blog, and show that you know how to turn a “hard” problem into a “clean” solution. 🚀

---

## 🎯  Final Checklist

1. **Run** each implementation against LeetCode’s sample tests.  
2. **Profile** your code: `O(m·n)` time, `O(m·n)` space.  
3. **Explain** the solution verbally—practice a 2‑minute pitch.  
4. **Publish** the blog article (SEO‑optimized).  
5. **Add** the code and article to your GitHub README.

Good luck, and may your knight always stay alive! 🏆