---
title: LeetCode 568. Maximum Vacation Days - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 568. Maximum Vacation Days – Code, Analysis & SEO‑Ready Blog

> **Problem source:** LeetCode Hard  
> **Key concepts:** Dynamic Programming, State Compression, Graph Traversal  
> **Ideal for:** Software‑Engineering interviews (Java/Python/C++)

---

### TL;DR

We keep a DP table `dp[city]` that stores the *maximum* vacation days attainable **from the current week to the end** if we are in `city` at the beginning of that week.  
We iterate weeks **backwards** (from week `k-1` to week `0`).  
For each city we consider:

1. **Stay** – take the vacation days of that city in the current week.  
2. **Fly** – choose a city that we can reach via a direct flight, take its vacation days for the current week and add the best future value (`dp[dest]`).

The final answer is `dp[0]` because we start in city `0` on Monday.

Complexity:  
- **Time:** `O(k * n²)`  (k ≤ 100, n ≤ 100 → ~1 000 000 ops)  
- **Space:** `O(n)` for the DP array (two arrays of length `n`).

---

## 📋 Problem Recap

You’re in a world of **n cities** and **k weeks**.  
- Flights are directed (matrix `flights[n][n]` – 1 = flight available, 0 = none).  
- Each week you can *fly* **once** – only on Monday morning.  
- `days[n][k]` gives the maximum vacation days you can take in city `i` during week `j`.  
- You may stay longer than the allowed days, but only the allowed days count.  
- Starting city: `0` on Monday of week 0.

**Goal:** Maximize total vacation days over all weeks.

---

## 🔧 Solution (Bottom‑Up DP)

Below is a concise, production‑ready implementation in **Java, Python, and C++**.  
All versions share the same logic, only syntax differs.

### 1. Java

```java
import java.util.*;

public class Solution {
    public int maxVacationDays(int[][] flights, int[][] days) {
        if (days == null || days.length == 0) return 0;
        int n = days.length;
        int k = days[0].length;

        // dp[city] = max vacation days from current week to end if we are at 'city'
        int[] dp = new int[n];
        // Iterate weeks from last to first
        for (int week = k - 1; week >= 0; --week) {
            int[] next = new int[n];
            for (int cur = 0; cur < n; ++cur) {
                // Option 1: stay in current city
                next[cur] = days[cur][week] + dp[cur];

                // Option 2: fly to a reachable city
                for (int dest = 0; dest < n; ++dest) {
                    if (flights[cur][dest] == 1) {
                        next[cur] = Math.max(next[cur],
                                days[dest][week] + dp[dest]);
                    }
                }
            }
            dp = next;
        }
        return dp[0];   // start at city 0
    }
}
```

### 2. Python

```python
class Solution:
    def maxVacationDays(self, flights: List[List[int]],
                        days: List[List[int]]) -> int:
        if not days:
            return 0
        n, k = len(days), len(days[0])
        dp = [0] * n
        for week in reversed(range(k)):
            nxt = [0] * n
            for cur in range(n):
                # stay
                nxt[cur] = days[cur][week] + dp[cur]
                # fly
                for dest in range(n):
                    if flights[cur][dest]:
                        nxt[cur] = max(nxt[cur],
                                       days[dest][week] + dp[dest])
            dp = nxt
        return dp[0]
```

### 3. C++

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int maxVacationDays(std::vector<std::vector<int>>& flights,
                        std::vector<std::vector<int>>& days) {
        if (days.empty()) return 0;
        int n = days.size();
        int k = days[0].size();
        std::vector<int> dp(n, 0);

        for (int week = k - 1; week >= 0; --week) {
            std::vector<int> next(n, 0);
            for (int cur = 0; cur < n; ++cur) {
                // Stay in current city
                next[cur] = days[cur][week] + dp[cur];
                // Fly to reachable cities
                for (int dest = 0; dest < n; ++dest) {
                    if (flights[cur][dest] == 1) {
                        next[cur] = std::max(next[cur],
                                             days[dest][week] + dp[dest]);
                    }
                }
            }
            dp.swap(next);
        }
        return dp[0];
    }
};
```

---

## 🎯 Why This Works (Intuition)

1. **State definition** – `dp[city]` holds the optimal future value from a given city.  
   This avoids recomputation and captures the dependency between weeks.

2. **Backward iteration** – Starting from the last week ensures that when we compute week `w`, all future weeks (`w+1…k-1`) are already optimal.

3. **Two choices per week** – Either stay or fly.  
   Because flights are only on Monday, the only decision that matters for the next week is *where you end up* after the Monday flight.  

4. **Graph traversal** – The inner loop over `dest` explores all outgoing flights from `cur`.  
   Complexity stays acceptable because `n ≤ 100`.

---

## 📊 Complexity Table

| Language | Time | Space |
|----------|------|-------|
| Java     | `O(k * n²)` | `O(n)` |
| Python   | `O(k * n²)` | `O(n)` |
| C++      | `O(k * n²)` | `O(n)` |

---

## 🧰 Tips for Interviewers & Candidates

| ✔️ Good | ❌ Bad | 🤔 Ugly |
|---------|--------|--------|
| **Good** | Use clear variable names (`dp`, `next`). | Avoid hard‑coding array sizes. | Forget to handle edge cases (`days` empty). |
| **Good** | Explain the DP state and why we iterate backwards. | Write unit tests for corner cases (no flights, all flights). | Skip the proof that staying in the same city is always considered. |
| **Good** | Show how to transform the recurrence into code. | Over‑comment every line. | Use recursion without memoization → stack overflow. |
| **Good** | Mention that the solution can be optimized to `O(k * n + k * m)` where `m` is number of edges if adjacency lists used. | Not optimizing space further when `n` can be large. | Misinterpret "flight only on Monday" leading to wrong DP recurrence. |

---

## 📚 SEO‑Optimized Blog Article

> **Title:** Master LeetCode 568: Maximum Vacation Days – Java, Python, C++ Solutions & Interview Tips  
> **Meta Description:** Learn how to solve LeetCode Hard #568 “Maximum Vacation Days” with clear Java, Python, and C++ code. Understand the DP approach, analyze complexity, and get interview‑ready tips.

---

### 1. Introduction

LeetCode’s **Maximum Vacation Days** is a classic dynamic‑programming problem that tests your ability to handle state transitions on a graph. With **n cities** and **k weeks**, you must plan flights and vacations to maximize the total rest days. In this article, we dissect the problem, present clean Java, Python, and C++ solutions, and discuss pitfalls to avoid during interviews.

---

### 2. Problem Overview

- **Flights**: `flights[i][j] = 1` means a direct flight from city `i` to `j`.  
- **Vacation limits**: `days[i][w]` is the maximum vacation days you can take in city `i` during week `w`.  
- **Rules**:  
  - You start in city `0` on week 0 Monday.  
  - Flights are only taken once per week, on Monday.  
  - A flight day counts toward the destination city’s vacation days.  

Your goal: **maximise total vacation days** over `k` weeks.

---

### 3. Intuition & DP Formulation

We treat each week as a **state transition**.  
Let `dp[city]` denote the maximum vacation days achievable from the *current* week to the end if you are in `city` at the start of that week.

Transition (for week `w`):
- **Stay**: `days[city][w] + dp[city]`
- **Fly**: For every reachable `dest`, `days[dest][w] + dp[dest]`

The best of these two gives the new value for `city` in the preceding week.  
We iterate weeks backwards so that all future weeks (`w+1…k-1`) are already optimal.

---

### 4. Code Walkthrough

#### Java

[Full source shown in the “Solution” section above.]

#### Python

[Full source shown above.]

#### C++

[Full source shown above.]

---

### 4. Complexity Analysis

With `n ≤ 100` and `k ≤ 100`:

- **Time**: `O(k * n²)` – up to 1 000 000 operations, easily within limits.  
- **Space**: `O(n)` – two DP arrays of length `n`.

---

### 5. Interview Pitfalls

| What to **Avoid** | Why It Matters |
|-------------------|----------------|
| Not iterating weeks backwards | Leads to incorrect future value usage. |
| Ignoring the “flight on Monday” constraint | Causes an invalid recurrence. |
| Using plain recursion without memoization | Results in exponential time and possible stack overflow. |
| Over‑optimizing with adjacency lists without need | Adds unnecessary complexity for a simple grid problem. |

---

### 6. Going Beyond

If you want to squeeze performance further:
- Store the graph as an **adjacency list** (vector of vectors) to skip impossible flights.  
- Reduce the inner loop to `O(m)` where `m` is the number of edges, instead of `n²`.  
- This gives `O(k * (n + m))` time – still trivial for the constraints.

---

### 7. Final Thoughts

LeetCode 568 is a *state‑compression* DP problem that blends graph traversal with tabulation.  
By defining `dp[city]` correctly and iterating weeks backwards, we transform a potentially messy “graph‑DP” into a clean, O(k · n²) algorithm.

**Want to shine in your next interview?**  
- Practice explaining the DP state and recurrence.  
- Write concise code in your chosen language.  
- Test edge cases (no flights, all flights).  
- Keep a mental note of time/space trade‑offs.

Happy coding—and may your vacation days always be maximised! 🌴🛫

--- 

Feel free to drop a comment below if you’d like to discuss variations of this problem or need help polishing your interview answers. Happy interviewing!