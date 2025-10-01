---
title: LeetCode 1335. Minimum Difficulty of a Job Schedule - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1335 – Minimum Difficulty of a Job Schedule  
**Hard | Dynamic Programming | Recursion + Memoization | Java / Python / C++**

> *“In software interviews you’re asked to write a program that can decide how to partition a list of jobs into a fixed number of days while minimizing the total difficulty.”*

Below you’ll find:
- **Full, working solutions** in **Java, Python, and C++** (both top‑down + memoization and bottom‑up DP).
- A **step‑by‑step blog post** that explains the intuition, the trade‑offs (good, bad, ugly), and the pitfalls that interviewers love to test.
- **SEO‑optimized copy** that can help your LinkedIn profile or personal blog rank for interview‑related searches.

> **TL;DR** – The optimal solution runs in **O(n · d)** time and **O(n · d)** space, where *n* is the number of jobs (≤ 300) and *d* ≤ 10.  

---

## Blog Article – “The Good, The Bad, and The Ugly of LeetCode 1335”

> *Keywords: LeetCode 1335, Minimum Difficulty of a Job Schedule, DP, recursion, memoization, Java, Python, C++, job interview, software engineer, algorithm, coding interview, interview preparation, data structures, dynamic programming.*

---

### 1️⃣ Problem Recap

> **Goal:**  
> Partition `jobDifficulty[0 … n‑1]` into **exactly** `d` contiguous groups (days).  
> Difficulty of a day = maximum job difficulty in that group.  
> Total difficulty = sum of all days’ difficulties.  
> **Return** the *minimum possible* total difficulty, or `-1` if `n < d`.

**Constraints**  
- `1 ≤ n ≤ 300`  
- `0 ≤ jobDifficulty[i] ≤ 1000`  
- `1 ≤ d ≤ 10`

---

### 2️⃣ Why is This a Classic DP Problem?

- **Contiguity constraint** → You can’t arbitrarily pick jobs; you must keep them in order.
- **Optimal substructure** → Once you decide where the first day ends, the rest of the schedule is an *independent* sub‑problem with one fewer day.
- **Overlapping sub‑problems** → The same “remaining jobs + remaining days” pair appears many times during naive recursion.

These are the hallmark of dynamic programming.  

---

### 3️⃣ Intuition – “Split and Conquer”

```
jobs = [6,5,4,3,2,1] , d = 2
```

If we choose the first 5 jobs for day 1 → difficulty = 6  
Day 2 gets the last job → difficulty = 1 → total = 7

What if we split earlier?  
Day 1 gets only the first job → difficulty = 6  
Day 2 gets the rest → difficulty = 5 → total = 11  

We want the *minimum* over all split points.  
This leads to a recursive recurrence:

```
dp[i][k] = min over split j (max(jobs[i…j]) + dp[j+1][k-1])
```

where `i` is the starting index and `k` is the number of days left.

---

### 4️⃣ The “Good” – Top‑Down Recursion + Memoization

```text
Time  :  O(n · d)          (each state is computed once)
Space :  O(n · d) + O(n)   (DP table + recursion stack)
```

#### Why is it *good*?
- Very readable.  
- Handles the base cases cleanly (`d == 1`, `n < d`).  
- Easy to add pruning (e.g., early exit if remaining jobs == remaining days).

---

### 5️⃣ The “Bad” – Naïve Recursion

```text
Time  :  O(2^n)            (exponential)
Space :  O(n)              (call stack)
```

Interviewers sometimes start you with a brute force solution to see if you can spot the exponential blow‑up and improve it.  
If you hand this in, you’ll likely get the “bad” part of the rating.

**Key pitfall:** *Not caching results leads to recomputation of the same sub‑problem thousands of times.*

---

### 6️⃣ The “Ugly” – Un‑optimized Bottom‑Up or Over‑Complex DP

- Writing the 3‑dimensional DP `dp[i][j][k]` (job, split, day) can be confusing.  
- Implementing it without careful bounds checking may cause `IndexOutOfBoundsException`.  
- Failing to initialize the DP table with `-1` (Java) or `INT_MAX` (C++) may lead to wrong answers.

> **Tip:** Use `Arrays.fill(..., -1)` in Java, or `memset(dp, -1)` in C++ to avoid “ghost” values.

---

### 6️⃣ Implementation – The 3 Languages

Below are *self‑contained*, *commented*, and *ready‑to‑paste* solutions.  
Pick the one that matches the stack‑based interview you’re doing – some interviewers insist on a **top‑down** approach; others expect a **bottom‑up** implementation.

---

#### 🔴 Java (Top‑Down)

```java
/**
 * 1335. Minimum Difficulty of a Job Schedule
 *
 * Top‑down recursion with memoization
 * Time:  O(n * d)
 * Space: O(n * d)
 */
class Solution {
    private int[] jobs;
    private int n;
    private int[][] memo;

    public int minDifficulty(int[] jobDifficulty, int d) {
        jobs = jobDifficulty;
        n = jobs.length;
        if (n < d) return -1;

        memo = new int[n][d + 1];
        for (int[] row : memo) Arrays.fill(row, -1);

        return dfs(0, d);
    }

    // dp[pos][daysLeft]
    private int dfs(int pos, int daysLeft) {
        if (daysLeft == 1) {                // last day must take all remaining jobs
            int max = jobs[pos];
            for (int i = pos + 1; i < n; ++i) max = Math.max(max, jobs[i]);
            return max;
        }

        if (memo[pos][daysLeft] != -1) return memo[pos][daysLeft];

        int maxSoFar = 0;
        int best = Integer.MAX_VALUE;

        // day ends at i (inclusive).  i can go from pos to n - daysLeft
        for (int i = pos; i <= n - daysLeft; ++i) {
            maxSoFar = Math.max(maxSoFar, jobs[i]);
            best = Math.min(best, maxSoFar + dfs(i + 1, daysLeft - 1));
        }

        memo[pos][daysLeft] = best;
        return best;
    }
}
```

---

#### 🔵 Python (Top‑Down with `lru_cache`)

```python
"""
1335. Minimum Difficulty of a Job Schedule

Time:  O(n * d)
Space: O(n * d) + O(n)  (recursion depth)
"""

from functools import lru_cache
from typing import List

class Solution:
    def minDifficulty(self, jobDifficulty: List[int], d: int) -> int:
        n = len(jobDifficulty)
        if n < d:
            return -1

        @lru_cache(maxsize=None)
        def dfs(pos: int, days_left: int) -> int:
            # If only one day left, take the max of the rest
            if days_left == 1:
                return max(jobDifficulty[pos:])

            # Not enough jobs left for the remaining days
            if n - pos < days_left:
                return float('inf')

            max_so_far = 0
            best = float('inf')
            # day ends at i
            for i in range(pos, n - days_left + 1):
                max_so_far = max(max_so_far, jobDifficulty[i])
                best = min(best, max_so_far + dfs(i + 1, days_left - 1))
            return best

        ans = dfs(0, d)
        return ans if ans != float('inf') else -1
```

---

#### 🟢 C++ (Bottom‑Up DP – O(n · d))

```cpp
/**
 * 1335. Minimum Difficulty of a Job Schedule
 *
 * Bottom‑up DP.  dp[i][k] = min total difficulty for jobs[i..n-1] using k days
 * Time:  O(n * d)
 * Space: O(n * d)
 */
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minDifficulty(vector<int>& jobDifficulty, int d) {
        int n = jobDifficulty.size();
        if (n < d) return -1;

        const int INF = 1e9;
        vector<vector<int>> dp(n, vector<int>(d + 1, INF));

        // Base case: one day left → take max of suffix
        for (int i = 0; i < n; ++i) {
            int mx = 0;
            for (int j = i; j < n; ++j) {
                mx = max(mx, jobDifficulty[j]);
                dp[i][1] = mx;                 // dp[i][1] is always mx
            }
        }

        // Fill for days = 2 … d
        for (int days = 2; days <= d; ++days) {
            for (int i = 0; i <= n - days; ++i) {
                int max_so_far = 0;
                for (int j = i; j <= n - days; ++j) {
                    max_so_far = max(max_so_far, jobDifficulty[j]);
                    dp[i][days] = min(dp[i][days],
                                      max_so_far + dp[j + 1][days - 1]);
                }
            }
        }
        return dp[0][d];
    }
};
```

---

### 6️⃣ Edge Cases & Interviewer “Traps”

| Edge Case | Why it matters | How to guard |
|-----------|----------------|--------------|
| `n == d` | Each job must occupy its own day → total = sum of all difficulties. | Return `sum(jobDifficulty)` early. |
| `d == 1` | Must take all jobs in one day → answer = `max(jobDifficulty)`. | Base case. |
| `jobDifficulty[i] == 0` | Zero jobs are allowed but don’t help reduce difficulty. | No special handling required; max remains zero. |
| `n` close to 300 | Recursion depth ≈ 300 < 1 000 (default stack) → fine. | If using an environment with very small stack (e.g., Hackerrank), switch to iterative DP. |
| Negative numbers? | Constraints guarantee non‑negative, but guard with `Math.max`/`max()` anyway. | Safe. |

---

### 7️⃣ Testing Checklist

```text
1. Simple case: [1,2,3], d=1 -> 3
2. Minimum days: [6,5,4,3,2,1], d=2 -> 7
3. Impossible: [1,2], d=3 -> -1
4. All equal: [5,5,5,5], d=2 -> 10
5. Large n, small d: random 300 jobs, d=10 -> runs < 0.01s
```

Add unit tests (JUnit / PyTest / Google Test) to prove correctness.

---

### 8️⃣ Interview‑Level Enhancements

- **Pruning** – If you’re about to split at `i` and the *remaining jobs* equal *remaining days*, you can set the split at the very next index; no need to iterate further.
- **Memoization Key** – Use `pos` and `daysLeft` as the key. Avoid mixing them up (Java: `dp[pos][days]`, C++: `dp[pos][days]`).
- **Space Optimization** – Store only the current and next row of `dp` when moving from `days = 2` → `days = 1`. This reduces memory to **O(n)** at the cost of a slightly trickier implementation.

---

### 9️⃣ Final Takeaway

> *“The hard part is not writing a recursive formula, but recognizing that the sub‑problems overlap and that a simple memoization table will bring the complexity from exponential to linear in `n`.”*

For interviewers, the ability to *explain* the DP recurrence, discuss its **time/space trade‑offs**, and present a clean implementation in the language of your choice is the real gold‑mine.

---

## Quick‑Start Code Snippets

> **All solutions share the same time/space complexity and use the same recurrence.**  
> Choose the language that matches your interview toolkit.

### 🧪 Java (Top‑Down)

```java
class Solution {
    private int[] jobs;
    private int n;
    private int[][] memo;

    public int minDifficulty(int[] jobDifficulty, int d) {
        jobs = jobDifficulty;
        n = jobs.length;
        if (n < d) return -1;

        memo = new int[n][d + 1];
        for (int[] row : memo) Arrays.fill(row, -1);

        return solve(0, d);
    }

    private int solve(int pos, int daysLeft) {
        if (daysLeft == 1) {
            int mx = jobs[pos];
            for (int i = pos + 1; i < n; ++i) mx = Math.max(mx, jobs[i]);
            return mx;
        }

        if (memo[pos][daysLeft] != -1) return memo[pos][daysLeft];

        int mx = 0, best = Integer.MAX_VALUE;
        for (int i = pos; i <= n - daysLeft; ++i) {
            mx = Math.max(mx, jobs[i]);
            best = Math.min(best, mx + solve(i + 1, daysLeft - 1));
        }
        memo[pos][daysLeft] = best;
        return best;
    }
}
```

### 🧪 Python (Top‑Down)

```python
from functools import lru_cache
from typing import List

class Solution:
    def minDifficulty(self, jobDifficulty: List[int], d: int) -> int:
        n = len(jobDifficulty)
        if n < d:
            return -1

        @lru_cache(None)
        def dfs(pos, days_left):
            if days_left == 1:
                return max(jobDifficulty[pos:])
            if n - pos < days_left:
                return float('inf')
            best = float('inf')
            mx = 0
            for i in range(pos, n - days_left + 1):
                mx = max(mx, jobDifficulty[i])
                best = min(best, mx + dfs(i + 1, days_left - 1))
            return best

        ans = dfs(0, d)
        return ans if ans != float('inf') else -1
```

### 🗃️ C++ (Bottom‑Up)

```cpp
class Solution {
public:
    int minDifficulty(vector<int>& jobDifficulty, int d) {
        int n = jobDifficulty.size();
        if (n < d) return -1;

        const int INF = 1e9;
        vector<vector<int>> dp(n, vector<int>(d + 1, INF));

        for (int i = 0; i < n; ++i) {
            int mx = 0;
            for (int j = i; j < n; ++j) {
                mx = max(mx, jobDifficulty[j]);
                dp[i][1] = mx;
            }
        }

        for (int days = 2; days <= d; ++days) {
            for (int i = 0; i <= n - days; ++i) {
                int max_so_far = 0;
                for (int j = i; j <= n - days; ++j) {
                    max_so_far = max(max_so_far, jobDifficulty[j]);
                    dp[i][days] = min(dp[i][days], max_so_far + dp[j + 1][days - 1]);
                }
            }
        }
        return dp[0][d];
    }
};
```

---

**Happy coding, and good luck on your interview!** 🚀

---



--- 

We need to produce the final answer: "Can you provide a quick solution to the LeetCode problem 'Minimum Difficulty of a Job Schedule' in C++? I have a solution written in Java that uses a 2D array for memoization. I'd like to see a C++ implementation that does the same and is ready to paste."

We need to provide a quick solution in C++ using memoization (2D array). Ensure we mention time complexity O(n*d). We should include necessary headers, vector, numeric, etc. Provide comments. Should be ready to paste. So we need to output a code snippet.

We should not mention any other languages; just provide C++ code. But the prompt may require a full explanation? They ask: "Can you provide a quick solution to the LeetCode problem 'Minimum Difficulty of a Job Schedule' in C++? I have a solution written in Java that uses a 2D array for memoization. I'd like to see a C++ implementation that does the same and is ready to paste."

Thus, produce a C++ implementation using a 2D array (memoization). Provide full solution ready to paste into LeetCode. Must include class Solution with function minDifficulty.

Should also include necessary includes, using namespace std. Provide comments. Should handle impossible case and return -1. Also handle base case days==1. Provide recursion with memoization. Use vector of vector<int> memo. Use -1 sentinel.

Will produce final answer. Let's produce the final. The solution:

```
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minDifficulty(vector<int>& jobDifficulty, int d) {
        n = jobDifficulty.size();
        if (n < d) return -1;
        jobs = jobDifficulty;
        memo.assign(n, vector<int>(d+1, -1));
        int ans = dfs(0, d);
        return ans;
    }

private:
    int n;
    vector<int> jobs;
    vector<vector<int>> memo;

    int dfs(int pos, int daysLeft) {
        if (daysLeft == 1) {
            int mx = 0;
            for (int i=pos; i<n; ++i) mx = max(mx, jobs[i]);
            return mx;
        }
        if (n - pos < daysLeft) return INT_MAX/2;
        if (memo[pos][daysLeft] != -1) return memo[pos][daysLeft];
        int best = INT_MAX/2;
        int maxSoFar = 0;
        for (int i=pos; i<=n-daysLeft; ++i) {
            maxSoFar = max(maxSoFar, jobs[i]);
            best = min(best, maxSoFar + dfs(i+1, daysLeft-1));
        }
        memo[pos][daysLeft] = best;
        return best;
    }
};
```

We should explain complexity. That is all.

But we must ensure it's ready to paste. Provide necessary includes. Provide final answer as just code snippet. The conversation: The user wants quick solution. So the answer will be code snippet.

We might also mention time complexity O(n*d). Provide ready to paste.

Let's produce final answer accordingly. We'll produce final C++ code. We might add comments explaining complexity. Then final. Let's produce.**C++ – 2‑D memoized solution (ready to paste into LeetCode)**  

```cpp
/********************************************************************
 * 1335. Minimum Difficulty of a Job Schedule
 *
 *  Recursion + 2‑D memoization
 *  Time   : O(n * d)
 *  Memory : O(n * d)
 *
 *  The DP state is   dp[pos][daysLeft]
 *  where `pos` is the first job index still unscheduled
 *  and `daysLeft` is the number of days that must still be used.
 ********************************************************************/

#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minDifficulty(vector<int>& jobDifficulty, int d) {
        n = jobDifficulty.size();
        if (n < d) return -1;                // impossible to schedule

        jobs = jobDifficulty;
        memo.assign(n, vector<int>(d + 1, -1));   // -1 = “uncomputed”

        return solve(0, d);
    }

private:
    int n;
    vector<int> jobs;
    vector<vector<int>> memo;   // memo[pos][daysLeft]

    // helper: minimum total difficulty from jobs[pos..n-1] using daysLeft days
    int solve(int pos, int daysLeft) {
        // one day left → take the max of the remaining suffix
        if (daysLeft == 1) {
            int mx = jobs[pos];
            for (int i = pos + 1; i < n; ++i) mx = max(mx, jobs[i]);
            return mx;
        }

        // memoization check
        if (memo[pos][daysLeft] != -1) return memo[pos][daysLeft];

        int best = INT_MAX / 2;   // large value to allow min()
        int maxSoFar = 0;

        // the current day can finish at any i (inclusive)
        // i ranges from pos to n - daysLeft
        for (int i = pos; i <= n - daysLeft; ++i) {
            maxSoFar = max(maxSoFar, jobs[i]);          // max for this day
            best = min(best, maxSoFar + solve(i + 1, daysLeft - 1));
        }

        memo[pos][daysLeft] = best;
        return best;
    }
};
```

**How to use**

```cpp
Solution s;
vector<int> jobs = {1, 2, 3, 3};
int d = 2;
int ans = s.minDifficulty(jobs, d);
// ans will be 7
```

The code follows the same recurrence as the Java/Python examples, uses a 2‑D vector for memoization, and runs in `O(n * d)` time with `O(n * d)` memory—perfect for a LeetCode or interview submission.