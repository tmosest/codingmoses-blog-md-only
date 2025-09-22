---
title: LeetCode 1751. Maximum Number of Events That Can Be Attended II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Code – Three Languages

Below are clean, beginner‑friendly solutions for **LeetCode 1751 – “Maximum Number of Events That Can Be Attended II”**.  
All three implementations follow the same idea:

1. **Sort** events by their end day.  
2. For each event, **binary‑search** the last non‑overlapping event.  
3. Use **DP** `dp[i][j]` – the maximum value attainable by considering the first `i` sorted events and picking at most `j` of them.

| Language | Time | Space | Notes |
|----------|------|-------|-------|
| **Java** | `O(N·K + N·log N)` | `O(N·K)` | Uses `int[][]` for DP. |
| **Python** | `O(N·K + N·log N)` | `O(N·K)` | Uses `list[list[int]]`. |
| **C++** | `O(N·K + N·log N)` | `O(N·K)` | Uses `vector<vector<long long>>`. |

> **Why `long long` / `long`?**  
> `value` can be up to `10^6` and `k·n` up to `10^6`, so the answer may reach `10^12`. 64‑bit integers avoid overflow.

---

### 1.1 Java

```java
import java.util.Arrays;

public class Solution {
    public int maxValue(int[][] events, int k) {
        // 1. Sort by end day
        Arrays.sort(events, (a, b) -> Integer.compare(a[1], b[1]));

        int n = events.length;
        // dp[i][j] – best value using first i events, taking at most j
        long[][] dp = new long[n + 1][k + 1];

        for (int i = 1; i <= n; i++) {
            int[] ev = events[i - 1];
            int prev = findLastNonOverlap(events, ev[0]);   // binary search

            for (int j = 1; j <= k; j++) {
                long skip = dp[i - 1][j];
                long take = dp[prev + 1][j - 1] + ev[2];
                dp[i][j] = Math.max(skip, take);
            }
        }
        return (int) dp[n][k];
    }

    // returns index of last event whose end < start, -1 if none
    private int findLastNonOverlap(int[][] events, int start) {
        int l = 0, r = events.length - 1, res = -1;
        while (l <= r) {
            int m = l + (r - l) / 2;
            if (events[m][1] < start) {
                res = m;
                l = m + 1;
            } else {
                r = m - 1;
            }
        }
        return res;
    }
}
```

---

### 1.2 Python

```python
from bisect import bisect_left
from typing import List

class Solution:
    def maxValue(self, events: List[List[int]], k: int) -> int:
        # 1. Sort by end day
        events.sort(key=lambda x: x[1])

        n = len(events)
        # dp[i][j] – best value using first i events, taking at most j
        dp = [[0] * (k + 1) for _ in range(n + 1)]

        # list of end days for binary search
        ends = [e[1] for e in events]

        for i in range(1, n + 1):
            s, e, val = events[i - 1]
            # last event that ends before s
            prev = bisect_left(ends, s) - 1

            for j in range(1, k + 1):
                skip = dp[i - 1][j]
                take = dp[prev + 1][j - 1] + val
                dp[i][j] = max(skip, take)

        return dp[n][k]
```

---

### 1.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxValue(vector<vector<int>>& events, int k) {
        // 1. Sort by end day
        sort(events.begin(), events.end(),
             [](const auto& a, const auto& b){ return a[1] < b[1]; });

        int n = events.size();
        vector<long long> ends(n);
        for (int i = 0; i < n; ++i) ends[i] = events[i][1];

        // dp[i][j] – best value using first i events, taking at most j
        vector<vector<long long>> dp(n + 1, vector<long long>(k + 1, 0));

        for (int i = 1; i <= n; ++i) {
            int s = events[i - 1][0];
            int val = events[i - 1][2];

            // last event that ends before s
            int prev = int(upper_bound(ends.begin(), ends.end(), s - 1) - ends.begin()) - 1;

            for (int j = 1; j <= k; ++j) {
                long long skip = dp[i - 1][j];
                long long take = dp[prev + 1][j - 1] + val;
                dp[i][j] = max(skip, take);
            }
        }
        return static_cast<int>(dp[n][k]);
    }
};
```

> **Tip** – In C++ use `upper_bound` on the `ends` vector for the binary search.  
> `prev = upper_bound(ends.begin(), ends.end(), s - 1) - ends.begin() - 1`.

---

## 2. Blog Article – “The Good, The Bad, The Ugly of LeetCode 1751”

> **Keywords:**  
> *Maximum Number of Events That Can Be Attended II* | *LeetCode 1751* | *dynamic programming* | *binary search* | *Java* | *Python* | *C++* | *coding interview* | *job interview* | *algorithm design*.

### 2.1 Introduction

When recruiters push a **hard‑level** problem from LeetCode onto your interview stack, it’s a signal that they’re testing your *problem‑solving architecture* more than your coding speed.  
LeetCode 1751 – “Maximum Number of Events That Can Be Attended II” is one of those gold‑standard problems that showcases:

* **Event scheduling** logic (non‑overlapping constraints).  
* **Dynamic programming** (choice vs skip).  
* **Binary search** for efficient look‑ups.

In this post, we’ll dissect the problem, walk through a clean solution, then explore the *good*, *bad*, and *ugly* aspects that arise in real interview settings.

---

### 2.2 Problem Recap

**Given**

* `events[i] = [startDay, endDay, value]` – an event lasts from `startDay` to `endDay` inclusive.  
* You can attend at most `k` events.  
* Events cannot overlap (two events that share any day conflict).

**Goal**

Return the maximum total value you can collect.

**Constraints**

* `1 <= k <= events.length <= 10^5`  
* `k * events.length <= 10^6` (guarantees DP table fits in memory)  
* `1 <= startDay <= endDay <= 10^9`  
* `1 <= value <= 10^6`

---

### 2.3 “The Good”

| ✅ | Explanation |
|----|-------------|
| **Clear DP state** – `dp[i][j]` is *precisely* the best value for the first `i` events and at most `j` picks. | Avoids the “off‑by‑one” pitfalls that kill many candidates. |
| **Sorted by end day** – guarantees that any event after index `i` ends later than event `i`. | Simplifies the non‑overlap check. |
| **Binary search on end days** – `O(log N)` lookup of the last compatible event. | Makes the whole algorithm `O(N·K + N·log N)` instead of quadratic. |
| **Compact DP table** (`N × K` ≤ `10^6` entries). | Fits comfortably in interview‑time memory limits. |

---

### 2.3 “The Bad”

| ❌ | Issue | Why it happens |
|----|-------|----------------|
| **Large numeric ranges** – `startDay` and `endDay` can be up to `10^9`. | Naïve “array of days” solutions explode in memory. |
| **Overflow risk** – Sum of up to `k` values can reach `10^12`. | 32‑bit integers in Java/Python `int` overflow. |
| **DP dimension explosion** – naïvely allocating `events.length × k` without pruning can blow memory for 10^5 × 10^5. | Interviewers may forget to use the `k·n <= 10^6` guarantee. |
| **Edge‑case off‑by‑one** – Inclusive end day vs exclusive `end+1` in binary search. | A subtle bug that only surfaces with a small test set. |

---

### 2.4 “The Ugly”

| ⚠️ | Problem | Real‑world Consequence |
|----|---------|------------------------|
| **Multiple optimal solutions** – You might produce the correct answer but use an *incorrect DP recurrence*. | Recruiters will still see that you understood the structure but they may penalize for missing the *canonical* solution. |
| **Time‑limit vs memory‑limit trade‑off** – Using `vector<vector<long long>>` in C++ or a full 2‑D array in Java can hit stack limits if not carefully sized. | Candidate must balance readability and performance, a classic interview tension. |
| **Binary search implementation errors** – Using `lower_bound` incorrectly or missing the `-1` adjustment leads to `IndexError` or wrong results. | Interviewers often expect you to *explain* every step; a single mis‑comment can derail the solution. |

---

### 2.5 Clean Solution – DP + Binary Search

#### 2.5.1 Idea in a Nutshell

1. **Sort** events by `endDay`.  
2. For each event `i` (sorted order) compute `prev(i)` – the largest index `< i` such that `events[prev].end < events[i].start`.  
   * Binary search on the array of end days (`O(log N)`).  
3. Maintain a 2‑D DP table:  

   ```
   dp[i][j] = max( dp[i‑1][j] ,          // skip event i
                   dp[prev(i)+1][j‑1] + events[i].value )   // take event i
   ```

4. Final answer is `dp[n][k]`.

> **Why “at most j” rather than “exactly j”?**  
> The recurrence naturally allows “skip” and automatically handles “at most”.  If you want *exactly* `k` events, you can check `dp[n][k]` but still allow `<k` by taking the maximum over `j ≤ k`.

#### 2.5.2 Binary Search Detail

Because events are sorted by *ending* day, the *last non‑overlapping* event is simply the rightmost event whose `endDay` is `< current.startDay`.  
`bisect_left(ends, start)` returns the first position where `end >= start`, so subtract 1 to get the valid index.  
If no such event exists, `prev = -1`, and `dp[prev+1][…]` refers to `dp[0][…]` – the empty prefix.

#### 2.5.3 Complexity

* **Time**:  
  * Sorting – `O(N log N)`  
  * For each of the `N` events, binary search – `O(log N)`  
  * DP updates – `O(N·K)` (the DP table has at most `10^6` cells).  
  * **Total**: `O(N·K + N·log N)`  

* **Space**:  
  * DP table – `O(N·K)`  
  * Auxiliary arrays (sorted events, end days) – `O(N)`

---

### 2.6 Common Interview Pitfalls

| Pitfall | How to Avoid |
|---------|--------------|
| **Using `int` for answer** | The answer may exceed `2^31‑1`. Switch to `long`/`long long`. |
| **Inclusive vs exclusive day** | Remember that two events that *share any day* conflict.  Use `end < start` when searching. |
| **Binary search off‑by‑one** | Double‑check the `bisect_left` / `upper_bound` logic.  Test with a small sample (`[[1,2,3],[2,3,4]]`). |
| **Large input with small `k`** | Even if `k` is tiny, you must still build a DP table of size `(N+1) × (k+1)` because the recurrence depends on `k`. |
| **Wrong sort key** | Sorting by `startDay` alone leads to wrong `prev(i)`.  The correct key is the *end day*. |

---

### 2.7 Variation: “Maximum Number of Events That Can Be Attended I”

LeetCode 2086 is the *easier* variant where you can attend *at most one* event at a time (`k` is not part of the input).  
The DP simplifies to a 1‑D array and still uses binary search.  
Studying both versions builds a **strong foundation** for any interview involving *interval scheduling*.

---

### 2.8 Interview‑Ready Tips

| Tip | Rationale |
|-----|-----------|
| **Explain the state before coding** | Shows you understand the *problem‑structure* – a must‑have for senior interviews. |
| **Talk through the binary search** | Interviewers love to hear you can *transform* a naive “scan all earlier events” into `O(log N)` search. |
| **Mention overflow** | A quick `long`/`long long` remark demonstrates you read constraints thoroughly. |
| **Show sample test** | `[[1,2,3],[3,4,2],[2,3,4],[4,5,1]]`, `k = 2` → answer `7`.  Demonstrates “skip vs take”. |
| **Discuss complexity** | Being able to break down `O(N·K)` and `O(N·log N)` into intuitive parts (sorting, binary search, DP loops) impresses interviewers. |

---

### 2.9 Conclusion

LeetCode 1751 is more than a hard problem; it’s a **mini‑curriculum** for interview prep:

* **Event scheduling** – teaches conflict detection.  
* **Dynamic programming** – showcases optimal substructure.  
* **Binary search** – demonstrates algorithmic optimization.

The *good* is that a single, well‑structured DP solves the whole problem.  
The *bad* is the risk of subtle bugs around inclusive dates and integer overflow.  
The *ugly* shows up when interviewers ask you to refactor or explain optimizations on the fly.

**If you master this problem, you’ll feel confident tackling any interval‑DP or scheduling interview question – a key skill that recruiters look for in candidates preparing for high‑tech roles.**

---

## 3. Final Takeaway for Your Interview Portfolio

* **Implement** the DP‑BS solution in **Java** (for backend roles), **Python** (for data science), or **C++** (for system‑level).  
* **Explain** the reasoning clearly – *state, recurrence, transition, base case*.  
* **Show** the trade‑offs (time vs space) and why the constraints allow a 2‑D DP.  

By presenting a clean, correct solution in three popular languages *and* a blog‑style explanation, you’ll have the **content** and the **confidence** to ace LeetCode 1751 in a coding interview – and impress your future employers.