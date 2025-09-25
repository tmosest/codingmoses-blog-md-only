---
title: LeetCode 1335. Minimum Difficulty of a Job Schedule - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🛠️ 1335 – Minimum Difficulty of a Job Schedule  
*Hard – LeetCode* | **Dynamic Programming** | **Java / Python / C++**  

> **Goal** – Schedule `n` dependent jobs in `d` days so that the total
> difficulty is minimal.  
> Each day you must complete **at least one job**; the difficulty of a day
> is the maximum difficulty of any job finished that day.  
> Return the minimum total difficulty, or `-1` if the schedule is impossible.

---

## 📑 Problem Recap (Short & Sweet)

| Item | Value |
|------|-------|
| `jobDifficulty` length `n` | 1 … 300 |
| Job difficulty | 0 … 1000 |
| Number of days `d` | 1 … 10 |
| Jobs are *dependent*: job `i` can be done only after jobs `0…i‑1`. |

You must split the array into **exactly `d` contiguous groups** (each group = one day) and minimize the sum of each group’s maximum value.

---

## 📌 Why It’s Hard

* **Exponential possibilities** – each day can take 1 … n jobs.
* **Dependencies** – jobs must be taken in order, ruling out arbitrary assignments.
* **Small `d` but large `n`** – brute force would be \(O(n^{d})\), impossible for `n=300`.

The key is to use **dynamic programming** with a **recursive + memoization** or **iterative** solution that runs in **\(O(n \cdot d)\)** time and **\(O(n \cdot d)\)** memory.

---

## 🧠 Intuition

Imagine you are on the first day.  
If you decide to finish the first `k` jobs today, the rest of the schedule becomes a **smaller sub‑problem**:

```
minDifficulty(jobs[k … n-1], d-1)
```

The difficulty of today is `max(jobs[0 … k-1])`.  
You want the minimum over all valid `k` (the last job of today).

This “divide and conquer” idea translates cleanly into recursion with memoization.

---

## 🔍 DP Formulation

Let  
`dp[idx][days] = minimum total difficulty to finish jobs[idx … n-1] in 'days' days`.

* **Base**  
  * `days == 1` → you must take all remaining jobs in one day →  
    `dp[idx][1] = max(jobDifficulty[idx … n-1])`
* **Recurrence**  
  For `days > 1`:

  ```
  dp[idx][days] = min over k (max(jobDifficulty[idx … k]) + dp[k+1][days-1])
  where k ranges from idx to n - days  (at least one job per remaining day)
  ```

* **Answer** – `dp[0][d]` (unless impossible).

**Impossible case** – if `n < d`, return `-1`.

---

## ⚙️ Complexity

| Approach | Time | Space |
|----------|------|-------|
| Recursion + memo | **O(n · d)** | **O(n · d)** |
| Bottom‑up DP (iterative) | **O(n · d · n)** (worst‑case) | **O(n · d)** |

The recursive version is the most common LeetCode implementation because it’s concise and naturally handles the “at least one job per day” constraint.

---

## 🏆 The Code (Java, Python, C++)

> **LeetCode signature** –  
> `int minDifficulty(int[] jobDifficulty, int d)`  
> `List<int>` or `vector<int>&` in Python / C++.

> All implementations use **int** for the final answer – the maximum possible answer is \(10 \times 1000 = 10\,000\).

### Java

```java
import java.util.Arrays;

public class Solution {
    // Memoization table: -1 means “not computed yet”
    private int[][] memo;
    private int[] jobs;
    private int n;

    public int minDifficulty(int[] jobDifficulty, int d) {
        jobs = jobDifficulty;
        n = jobs.length;

        // Impossible if we have fewer jobs than days
        if (n < d) return -1;

        memo = new int[n][d + 1];
        for (int[] row : memo) Arrays.fill(row, -1);

        return solve(0, d);
    }

    // Recursive helper: min difficulty for jobs[start …] in 'days' days
    private int solve(int start, int days) {
        // Base: only one day left → take all remaining jobs
        if (days == 1) {
            int max = 0;
            for (int i = start; i < n; ++i) {
                max = Math.max(max, jobs[i]);
            }
            return max;
        }

        if (memo[start][days] != -1) return memo[start][days];

        int best = Integer.MAX_VALUE;
        int dayMax = 0;

        // k is the last job of this day
        // We must leave at least 'days-1' jobs for the remaining days
        for (int k = start; k <= n - days; ++k) {
            dayMax = Math.max(dayMax, jobs[k]);          // max for this day
            int rest = solve(k + 1, days - 1);          // optimal rest
            best = Math.min(best, dayMax + rest);
        }

        memo[start][days] = best;
        return best;
    }
}
```

---

### Python

```python
from functools import lru_cache
from typing import List

class Solution:
    def minDifficulty(self, jobDifficulty: List[int], d: int) -> int:
        n = len(jobDifficulty)
        if n < d:
            return -1

        @lru_cache(None)
        def dfs(idx: int, days: int) -> int:
            # Base: one day left → take all remaining jobs
            if days == 1:
                return max(jobDifficulty[idx:])

            best = float('inf')
            day_max = 0
            # last job of the current day
            for k in range(idx, n - days + 1):
                day_max = max(day_max, jobDifficulty[k])
                best = min(best, day_max + dfs(k + 1, days - 1))
            return best

        return dfs(0, d)
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minDifficulty(vector<int>& jobDifficulty, int d) {
        int n = jobDifficulty.size();
        if (n < d) return -1;

        vector<vector<int>> memo(n, vector<int>(d + 1, -1));

        function<int(int,int)> dfs = [&](int idx, int days) -> int {
            if (days == 1) {               // last day – take all remaining jobs
                int mx = 0;
                for (int i = idx; i < n; ++i) mx = max(mx, jobDifficulty[i]);
                return mx;
            }

            if (memo[idx][days] != -1) return memo[idx][days];

            int best = INT_MAX;
            int dayMax = 0;
            for (int k = idx; k <= n - days; ++k) {
                dayMax = max(dayMax, jobDifficulty[k]);       // today
                best = min(best, dayMax + dfs(k + 1, days - 1));
            }
            return memo[idx][days] = best;
        };

        return dfs(0, d);
    }
};
```

> *Note* – All three snippets follow the same DP recurrence and run in **\(O(n \cdot d)\)** time thanks to memoization.

---

## ✅ Edge‑Case Checklist

| Situation | Expected Result |
|-----------|-----------------|
| `n < d` | `-1` |
| `d == 1` | `max(jobDifficulty)` |
| Some jobs have difficulty `0` | Works – the max in a segment still counts |
| All jobs same difficulty | `d * sameDifficulty` |
| Large `n` (300) & `d` (10) | Still fast – ~3,000 DP states |

---

## 🔍 Quick Test Harness (Python)

```python
def test():
    s = Solution()
    assert s.minDifficulty([6,5,4,3,2,1], 2) == 7
    assert s.minDifficulty([1,1,1], 3) == 3
    assert s.minDifficulty([11,111,22,222,33,333], 2) == 444
    assert s.minDifficulty([1], 2) == -1
    print("All tests passed!")
test()
```

Run the snippet on any Python environment (Python 3.8+).

---

## ⚠️ Common Pitfalls & “Ugly” Traps

| Mistake | Why it fails | Fix |
|---------|--------------|-----|
| **Iterate `k` to `n-1`** for all days | Leaves 0 jobs for remaining days → schedule invalid | Stop at `n - days` |
| **Base case `days == 0`** | You’ll try to read outside the array | Use `days == 1` as the only base |
| **Using `int[][] dp` with 0‑initialized** | 0 may be a legitimate answer, ambiguous | Use `-1` as “unset” |
| **Large recursion depth** | Python stack may overflow for `n=300` | Use `lru_cache` or iterative DP |
| **Missing impossible case** | `n < d` → you’ll get an array‑index error | Explicitly check early |

---

## 📈 Interview‑Prep Takeaway

1. **Understand the partition nature** – you’re always splitting the array.  
2. **Leverage the small `d`** – the DP dimension `days` is tiny.  
3. **Memoize** – even the simplest recursive formula is \(O(n·d)\) when cached.  
4. **Edge‑case first** – check `n < d` and `d == 1` before diving in.  

With this pattern, you can solve many “schedule‑partition” problems that surface in interviews.

---

## 🎯 SEO‑Friendly Closing

* **Keywords**: LeetCode 1335, Minimum Difficulty of a Job Schedule, Dynamic Programming, Recursion, Memoization, Job Scheduling, Hard Interview Problem, Java, Python, C++, DP, Interview Prep.  
* **Meta description**: “Solve LeetCode 1335 – Minimum Difficulty of a Job Schedule – with an O(n·d) DP solution. Get Java, Python, and C++ code examples plus interview‑friendly explanations.”  
* **Header Tags**: `# LeetCode 1335`, `## Problem Statement`, `## DP Approach`, `## Complexity`, `## Code (Java/Python/C++)`, `## Testing`, `## Conclusion`.  

---

### 📢 Call‑to‑Action

> **Got a LeetCode “hard” that’s giving you headaches?**  
> Share this article, drop a comment, or fork the repo.  
> Let’s conquer more interview questions together! 🚀  

Happy coding!