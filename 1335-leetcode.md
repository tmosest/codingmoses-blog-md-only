---
title: LeetCode 1335. Minimum Difficulty of a Job Schedule - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # LeetCode 1335 – Minimum Difficulty of a Job Schedule  
> **Hard | DP | Recursion + Memoization | O(n·d) Time | O(n·d) Space**

## Table of Contents  
1. [Problem Restatement](#problem-restatement)  
2. [Why It’s Hard](#why-its-hard)  
3. [Brute‑Force Idea (The Ugly)](#brute-force-idea-the-ugly)  
4. [Optimal DP Solution (The Good)](#optimal-dp-solution-the-good)  
5. [Edge Cases & Common Pitfalls (The Bad)](#edge-cases--common-pitfalls-the-bad)  
6. [Java / Python / C++ Code](#code)  
   * Java  
   * Python  
   * C++  
7. [Complexity Analysis](#complexity-analysis)  
8. [SEO‑Friendly Takeaway](#seo-friendly-takeaway)  

---

## Problem Restatement  

You are given an array `jobDifficulty` where `jobDifficulty[i]` is the difficulty of the *i*‑th job, and an integer `d` – the number of days you have to finish all jobs.

- Jobs must be completed **in order**.  
- Every day you must finish **at least one** job.  
- The difficulty of a day = the **maximum** difficulty among the jobs completed that day.  
- The total difficulty of a schedule = sum of the daily difficulties.  

**Goal:** Minimize the total difficulty.  
If you can’t finish all jobs in exactly `d` days (i.e. `jobDifficulty.length < d`), return `-1`.

---

## Why It’s Hard  

1. **Partitioning** – you need to split the array into `d` contiguous segments.  
2. **Exponential search space** – every day’s split point is a choice.  
3. **Non‑linear objective** – the cost of a segment is its maximum, not a sum.  
4. **Small `d` but large `n`** – `n` can be 300 while `d` ≤ 10, meaning an algorithm that is `O(n²·d)` is fine, but anything worse starts to bite.

---

## Brute‑Force Idea (The Ugly)

A naïve recursion tries every split point for every day:

```text
helper(idx, daysLeft):
    if daysLeft == 1:
        return max(jobDifficulty[idx..end])
    best = ∞
    maxDay = -∞
    for i in idx … end - daysLeft + 1:      // choose split after i
        maxDay = max(maxDay, jobDifficulty[i])
        best = min(best, maxDay + helper(i+1, daysLeft-1))
    return best
```

**Problems:**

- Exponential time (`O(2^n)` worst‑case).  
- Repeated sub‑problems cause massive recomputation.  
- Stack depth can reach `n` (risk of stack overflow on large inputs).  
- Hard to read and maintain.

> *The “ugly” part:* this is the first code you’ll write; it shows the *concept*, but it fails on larger tests.

---

## Optimal DP Solution (The Good)

### 1. DP State

`dp[i][k]` = minimal difficulty to finish **first `i` jobs** in **exactly `k` days**.

- `i` ranges from `1 … n`  
- `k` ranges from `1 … d`  

### 2. Transition

For the last day (day `k`) we pick a split point `t` (`k-1 ≤ t < i`):

```
dp[i][k] = min over t ( dp[t][k-1] + max(jobDifficulty[t+1 … i]) )
```

During the inner loop we maintain the running maximum so that computing  
`max(jobDifficulty[t+1 … i])` is `O(1)` for each `t`.

### 3. Initialization

- `dp[i][1]` = `max(jobDifficulty[1 … i])`  
  (All jobs of day 1 are fixed – you can’t split further.)

### 4. Result

`dp[n][d]` (first `n` jobs in `d` days).  
If `n < d` → `-1` immediately.

### 5. Why It Works

The partition of the array is implicitly encoded in the choice of `t`.  
Since `d ≤ 10`, the 3‑nested loops (`n·d·n`) are at most `300·10·300 ≈ 9×10⁵` operations – comfortably fast.

### 6. Implementation Tips

| Good | Reason |
|------|--------|
| **Iterative DP** | Avoids recursion overhead & stack depth issues. |
| **Forward max** | Updates `maxDay` inside the inner loop – no extra `max()` calls. |
| **Sentinel row** | A row of `-1` or `∞` signals “uncomputed”. |

---

## Edge Cases & Common Pitfalls (The Bad)

| Bad practice | What can go wrong |
|--------------|------------------|
| Using `dp[0][*]` as 0 | The problem guarantees *at least one* job per day; `dp[0][k]` should be `∞` for `k > 0`. |
| Off‑by‑one errors in indices | Remember array indices in Java/Python/C++ start at 0, but `dp` uses `1‑based` for readability. |
| Forgetting the `n < d` check | LeetCode’s tests include this case; you must return `-1` immediately. |
| Over‑optimizing with `O(n²·d)` → `O(n·d)` via prefix maxima? | Not necessary here; keep it simple and clear. |

---

## Code  
Below are **three** implementations – one in each language – with extensive comments so you can copy, paste, and run them on your own test cases.

> **All codes use the DP formulation described above.**  
> They run in **O(n·d·n)** time and **O(n·d)** memory, which is optimal for LeetCode’s limits.

### Java

```java
import java.util.Arrays;

public class Solution {
    public int minDifficulty(int[] jobDifficulty, int d) {
        int n = jobDifficulty.length;
        if (n < d) return -1;               // not enough jobs

        // dp[i][k] : minimal difficulty for first i jobs in k days
        int[][] dp = new int[n + 1][d + 1];
        for (int[] row : dp) Arrays.fill(row, Integer.MAX_VALUE);

        // base case: one day -> take max of the segment
        for (int i = 1; i <= n; i++) {
            int max = 0;
            for (int j = 1; j <= i; j++) max = Math.max(max, jobDifficulty[j - 1]);
            dp[i][1] = max;
        }

        // fill DP table
        for (int k = 2; k <= d; k++) {          // days
            for (int i = k; i <= n; i++) {     // jobs
                int maxDay = 0;
                // try all split points t (last day ends at i)
                for (int t = i - 1; t >= k - 1; t--) {
                    maxDay = Math.max(maxDay, jobDifficulty[t]); // job t
                    int candidate = dp[t][k - 1] + maxDay;
                    dp[i][k] = Math.min(dp[i][k], candidate);
                }
            }
        }

        return dp[n][d];
    }

    // Simple main for quick sanity check
    public static void main(String[] args) {
        int[] jobs = {6, 5, 4, 3, 2, 1};
        System.out.println(new Solution().minDifficulty(jobs, 2)); // 7
    }
}
```

> **Why this is good:**  
> *Clean, iterative DP, single‑pass over splits, no recursion.*

---

### Python

```python
from typing import List

class Solution:
    def minDifficulty(self, jobDifficulty: List[int], d: int) -> int:
        n = len(jobDifficulty)
        if n < d:
            return -1

        # dp[i][k] minimal difficulty for first i jobs in k days
        dp = [[float('inf')] * (d + 1) for _ in range(n + 1)]

        # base: one day
        for i in range(1, n + 1):
            dp[i][1] = max(jobDifficulty[:i])

        # DP transition
        for k in range(2, d + 1):
            for i in range(k, n + 1):
                max_day = 0
                # split before i: last job index t
                for t in range(i - 1, k - 2, -1):
                    max_day = max(max_day, jobDifficulty[t])
                    dp[i][k] = min(dp[i][k], dp[t][k - 1] + max_day)

        return dp[n][d]

# quick test
if __name__ == "__main__":
    sol = Solution()
    print(sol.minDifficulty([6,5,4,3,2,1], 2))  # 7
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

        vector<vector<int>> dp(n + 1, vector<int>(d + 1, INT_MAX));

        // base case: one day
        for (int i = 1; i <= n; ++i) {
            int mx = 0;
            for (int j = 0; j < i; ++j) mx = max(mx, jobDifficulty[j]);
            dp[i][1] = mx;
        }

        // DP
        for (int k = 2; k <= d; ++k) {
            for (int i = k; i <= n; ++i) {
                int mx = 0;
                // last day ends at i, split after t
                for (int t = i - 1; t >= k - 1; --t) {
                    mx = max(mx, jobDifficulty[t]);
                    dp[i][k] = min(dp[i][k], dp[t][k - 1] + mx);
                }
            }
        }
        return dp[n][d];
    }
};

int main() {
    Solution s;
    vector<int> jobs = {6,5,4,3,2,1};
    cout << s.minDifficulty(jobs, 2) << endl; // 7
}
```

---

## Complexity Analysis  

| Method | Time | Space |
|--------|------|-------|
| Brute‑Force Recursion | Exponential (`O(2^n)`) | `O(n)` recursion depth |
| DP (iterative) | **O(n² · d)** ≤ 9 × 10⁵ | **O(n·d)** |
| DP (recursion + memoization) | **O(n² · d)** | **O(n·d)** + recursion stack |

Given `n ≤ 300` and `d ≤ 10`, the DP is comfortably fast on LeetCode.

---

## SEO‑Friendly Takeaway  

- **Keywords to target:** *LeetCode 1335*, *Minimum Difficulty of a Job Schedule*, *Hard LeetCode problem*, *Java DP solution*, *Python DP solution*, *C++ DP solution*, *job scheduling algorithm*, *dynamic programming interview*, *job difficulty*.
- Use descriptive headings (`#`, `##`) that contain those keywords – Google loves them.
- Add a short summary paragraph at the very beginning:  
  ```markdown
  ## LeetCode 1335 – Minimum Difficulty of a Job Schedule  
  Hard DP solution in Java, Python and C++ that runs in O(n·d) time.  
  Solve the hard job‑scheduling problem with contiguous partitions and segment maximums.
  ```
- Keep the article under 1,200 words, sprinkle the keywords naturally, and finish with a “call‑to‑action” (e.g., “Try it out on LeetCode and tag your solution with #Leetcode1335”).

---

## Final Words  

- **Good** – The DP solution is clean, fast, and passes all tests.  
- **Bad** – Forgetting the `n < d` check or mixing 0‑based/1‑based indices will crash the solution.  
- **Ugly** – The first naïve recursion is easy to write but impractical; use it only as a learning tool.

Happy coding, and good luck nailing that interview!