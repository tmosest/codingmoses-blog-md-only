---
title: LeetCode 2323. Find Minimum Time to Finish All Jobs II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## LeetCode 2323 – **Find Minimum Time to Finish All Jobs II**

> **Problem**  
> You are given two arrays `jobs` and `workers` (both of length *n*).  
> `jobs[i]` is the amount of time needed to finish job *i* (in “hours”),  
> `workers[j]` is the number of hours a worker can work **in one day**.  
> Each job must be assigned to exactly one worker and each worker gets exactly one job.  
> Return the minimum number of days required so that all jobs are finished.

> **Example**  
> ```text
> jobs    = [5, 2, 4]
> workers = [1, 7, 5]
> output  = 2
> ```
> (Optimal assignment: 5h job → worker 5h/day, 4h job → worker 7h/day, 2h job → worker 1h/day.)

---

## TL;DR – The One‑Line Solution

```text
Sort both arrays, pair the smallest job with the smallest worker,
the largest job with the fastest worker.
Answer = max( ceil(jobs[i] / workers[i]) )
```

Why? Because the *rearrangement inequality* tells us that pairing the largest job with the fastest worker (smallest worker capacity) minimises the maximum finishing time.

---

## 1.  The “Good” – Why This Works

| Step | What we do | Why it is correct |
|------|------------|-------------------|
| 1 | `jobs.sort()` | Makes it easy to line‑up jobs in non‑decreasing order. |
| 2 | `workers.sort()` | Enables the same line‑up for workers. |
| 3 | `days = ceil(jobs[i] / workers[i])` | Number of days a worker needs to finish that job. |
| 4 | `max(days)` | The whole schedule finishes when the slowest pair finishes. |

**Why the pairing is optimal?**  
Take two jobs `A > B` and two workers `X > Y` (i.e., `X` is faster).  
If we pair `A → X` and `B → Y`, the total days needed are  
`max(ceil(A/X), ceil(B/Y))`.  
If we swap the assignment, the days become  
`max(ceil(A/Y), ceil(B/X))`.  
Because `A/Y ≥ A/X > B/X`, the swapped assignment can never be better, so the original pairing is optimal.

---

## 2.  The “Bad” – Things to Watch Out For

| Issue | How to Fix |
|-------|------------|
| **Integer division rounding** | Use `ceil` carefully: `(jobs[i] + workers[i] - 1) / workers[i]`. |
| **Large numbers** | All numbers ≤ 10⁵, so 32‑bit integers are fine. |
| **Edge cases** | `n == 1` works the same; ensure both arrays are sorted before accessing indices. |

---

## 3.  The “Ugly” – Common Pitfalls

1. **Mis‑reading the problem** – Some people think we can assign the same worker to multiple jobs. The statement says *exactly one* job per worker.
2. **Off‑by‑one in division** – Using `jobs[i] / workers[i]` will truncate to zero for small ratios; you need the ceiling.
3. **Sorting in the wrong order** – If you sort one array ascending and the other descending, you pair the slowest job with the slowest worker – that’s *worst* possible.

---

## 4.  Complexity Analysis

| Algorithm | Time | Space |
|-----------|------|-------|
| Sort both arrays + linear scan | `O(n log n)` | `O(1)` (in‑place sorting) |

With `n ≤ 10⁵`, this easily fits within the limits.

---

## 5.  Code Implementations

Below are three complete, self‑contained solutions in **Java**, **Python**, and **C++**.  
Feel free to copy‑paste into your favourite editor.

---

### 5.1 Java

```java
import java.util.Arrays;

public class Solution {
    public int minimumTime(int[] jobs, int[] workers) {
        // 1. Sort both arrays
        Arrays.sort(jobs);
        Arrays.sort(workers);

        // 2. Find the maximum days needed
        int maxDays = 0;
        for (int i = 0; i < jobs.length; i++) {
            // ceil division: (jobs[i] + workers[i] - 1) / workers[i]
            int days = (jobs[i] + workers[i] - 1) / workers[i];
            if (days > maxDays) maxDays = days;
        }
        return maxDays;
    }
}
```

---

### 5.2 Python

```python
def minimumTime(jobs, workers):
    """
    :type jobs: List[int]
    :type workers: List[int]
    :rtype: int
    """
    # Sort the lists
    jobs.sort()
    workers.sort()

    # Compute the maximum ceil division
    return max((j + w - 1) // w for j, w in zip(jobs, workers))
```

---

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

int minimumTime(vector<int>& jobs, vector<int>& workers) {
    sort(jobs.begin(), jobs.end());
    sort(workers.begin(), workers.end());

    int ans = 0;
    for (size_t i = 0; i < jobs.size(); ++i) {
        int days = (jobs[i] + workers[i] - 1) / workers[i];
        ans = max(ans, days);
    }
    return ans;
}
```

---

## 6.  A Blog‑Style Guide: “The Good, The Bad, and The Ugly”

> **Title**: *“Cracking LeetCode 2323: Find Minimum Time to Finish All Jobs II – A Full Interview Cheat Sheet”*  

> **Target Keywords**  
> *LeetCode 2323*, *Find Minimum Time to Finish All Jobs II*, *Java solution*, *Python solution*, *C++ solution*, *algorithm interview*, *sorting greedy*, *job scheduling*, *software engineering interview*, *backend interview prep*, *full stack interview questions*, *data structures interview*, *job interview tips*  

> **Outline**

| Section | Focus |
|---------|-------|
| 1. Problem Overview | Brief description, constraints, example |
| 2. Intuition & Key Insight | “Largest job ↔ fastest worker” |
| 3. Detailed Solution | Sorting + linear scan + ceiling division |
| 4. Complexity | Time/space |
| 5. Edge Cases & Pitfalls | Integer division, off‑by‑one, sorting order |
| 6. Alternate Approaches | Binary search + greedy (overkill) |
| 7. Code Snippets | Java / Python / C++ |
| 8. Interview Prep | Why this problem shows mastery of sorting & greedy |
| 9. SEO Closing | “If you mastered this, you’re ready for senior dev roles” |

### 6.1 Sample Intro

> “When interviewers throw *Find Minimum Time to Finish All Jobs II* at you, they’re testing a classic algorithmic pattern: **sorting + greedy**. In this article, I’ll walk you through the simplest, most optimal solution in Java, Python, and C++. You’ll also learn why the greedy pairing works, common traps, and how mastering this problem can impress hiring managers at top tech firms.”

### 6.2 Sample Closing

> “Congratulations! You now understand *why* sorting the jobs and workers and pairing them in that order gives the optimal answer. That kind of insight—seeing the big picture and reducing a complex assignment problem to a single linear scan—is exactly what recruiters look for. Practice this pattern with other scheduling problems (e.g., *Task Scheduler*, *Paint House*), and you’ll be well‑prepared for your next software engineering interview.”

---

## 7.  How This Helps You Get a Job

1. **Demonstrates Mastery of Sorting** – Most interviewers ask sorting‑based questions.
2. **Shows Greedy Insight** – Recognising that the optimal solution is *pair largest with fastest* is a classic greedy mindset.
3. **Edge‑Case Awareness** – Handling integer division and large inputs shows production‑ready code.
4. **Multiple Language Mastery** – Providing solutions in Java, Python, and C++ shows versatility, a key trait for full‑stack or backend roles.
5. **SEO‑Friendly Blog** – Writing a detailed, keyword‑rich article boosts your online visibility—LinkedIn posts, Medium articles, or personal blogs can be shared with recruiters.

---

**Happy coding and good luck on your interview journey!**