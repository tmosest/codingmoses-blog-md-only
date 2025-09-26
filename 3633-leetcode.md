---
title: LeetCode 3633. Earliest Finish Time for Land and Water Rides I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Solution Code – 3 Languages

Below are **ready‑to‑copy** implementations of the optimal greedy solution for LeetCode 3633 – *Earliest Finish Time for Land and Water Rides I*.  
All three snippets run in **O(n + m)** time, use **O(1)** extra space, and respect the 1‑based constraints.

| Language | File name | Class / Function | Key Points |
|----------|-----------|------------------|------------|
| **Java** | `Solution.java` | `public int earliestFinishTime(int[] landStartTime, int[] landDuration, int[] waterStartTime, int[] waterDuration)` | Uses two passes: 1) compute the earliest finish for a land ride, 2) compute the earliest finish for a water ride. |
| **Python** | `solution.py` | `def earliest_finish_time(land_start_time, land_duration, water_start_time, water_duration):` | Same logic, expressed in idiomatic Python. |
| **C++** | `Solution.cpp` | `int earliestFinishTime(vector<int>& landStartTime, vector<int>& landDuration, vector<int>& waterStartTime, vector<int>& waterDuration)` | Uses `INT_MAX` and `std::max`. |

---

### 1.1 Java

```java
// Solution.java
import java.util.*;

class Solution {
    public int earliestFinishTime(
            int[] landStartTime,
            int[] landDuration,
            int[] waterStartTime,
            int[] waterDuration) {

        int ans = Integer.MAX_VALUE;

        // ---------- Case 1 : land first, water second ----------
        int minLandFinish = Integer.MAX_VALUE;
        for (int i = 0; i < landStartTime.length; i++) {
            minLandFinish = Math.min(minLandFinish,
                                     landStartTime[i] + landDuration[i]);
        }
        for (int j = 0; j < waterStartTime.length; j++) {
            int finish = waterDuration[j] +
                         Math.max(minLandFinish, waterStartTime[j]);
            ans = Math.min(ans, finish);
        }

        // ---------- Case 2 : water first, land second ----------
        int minWaterFinish = Integer.MAX_VALUE;
        for (int j = 0; j < waterStartTime.length; j++) {
            minWaterFinish = Math.min(minWaterFinish,
                                      waterStartTime[j] + waterDuration[j]);
        }
        for (int i = 0; i < landStartTime.length; i++) {
            int finish = landDuration[i] +
                         Math.max(minWaterFinish, landStartTime[i]);
            ans = Math.min(ans, finish);
        }

        return ans;
    }
}
```

---

### 1.2 Python

```python
# solution.py
from typing import List

def earliest_finish_time(
        land_start_time: List[int],
        land_duration: List[int],
        water_start_time: List[int],
        water_duration: List[int]) -> int:
    INF = 10**9
    ans = INF

    # Land first, water second
    min_land_finish = min(ls + ld for ls, ld in zip(land_start_time, land_duration))
    for ws, wd in zip(water_start_time, water_duration):
        finish = wd + max(min_land_finish, ws)
        ans = min(ans, finish)

    # Water first, land second
    min_water_finish = min(ws + wd for ws, wd in zip(water_start_time, water_duration))
    for ls, ld in zip(land_start_time, land_duration):
        finish = ld + max(min_water_finish, ls)
        ans = min(ans, finish)

    return ans
```

---

### 1.3 C++

```cpp
// Solution.cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int earliestFinishTime(vector<int>& landStartTime,
                           vector<int>& landDuration,
                           vector<int>& waterStartTime,
                           vector<int>& waterDuration) {

        int ans = INT_MAX;

        // Case 1: land first, water second
        int minLandFinish = INT_MAX;
        for (size_t i = 0; i < landStartTime.size(); ++i)
            minLandFinish = min(minLandFinish,
                                landStartTime[i] + landDuration[i]);

        for (size_t j = 0; j < waterStartTime.size(); ++j) {
            int finish = waterDuration[j] +
                         max(minLandFinish, waterStartTime[j]);
            ans = min(ans, finish);
        }

        // Case 2: water first, land second
        int minWaterFinish = INT_MAX;
        for (size_t j = 0; j < waterStartTime.size(); ++j)
            minWaterFinish = min(minWaterFinish,
                                 waterStartTime[j] + waterDuration[j]);

        for (size_t i = 0; i < landStartTime.size(); ++i) {
            int finish = landDuration[i] +
                         max(minWaterFinish, landStartTime[i]);
            ans = min(ans, finish);
        }

        return ans;
    }
};
```

---

## 2. Blog Article – “The Good, the Bad, and the Ugly of LeetCode 3633”

> **Title**: *LeetCode 3633 – Earliest Finish Time for Land and Water Rides*: The Good, The Bad, The Ugly (and the Interview‑Ready Code)  
> **Slug**: leetcode-3633-early-finish-time-rides  
> **Meta description**: Master LeetCode 3633 with a step‑by‑step guide, greedy insights, and clean Java/Python/C++ code that shines in interviews.

---

### 2.1 Why this Problem Matters

- **Real‑world feel** – It mirrors a typical *“choose the best schedule”* scenario that appears in systems design interviews.
- **Easy complexity** – With **O(n + m)** you learn to reason about *dual‑sequence* decisions quickly.
- **Interview staple** – Many hiring managers ask “How would you solve a similar scheduling problem?” after you tackle this one.

---

### 2.2 Problem Recap

| Category | Input | Meaning |
|----------|-------|---------|
| **Land rides** | `landStartTime[i]`, `landDuration[i]` | Earliest boarding time & duration |
| **Water rides** | `waterStartTime[j]`, `waterDuration[j]` | Earliest boarding time & duration |

You must:

1. Pick **exactly one** land ride and **exactly one** water ride.
2. You can do them in any order.
3. If you finish a ride at time `t`, you may immediately start the other if it’s already open; otherwise you wait.

**Goal**: *Return the minimum possible finish time.*

---

### 2.3 The Good – The Greedy Insight

The key observation is that **you only need the earliest finish time for the first ride type**.  

Why?

- Suppose you choose *land first*.  
  - The earliest you can finish the land ride is `min(landStart[i] + landDuration[i])`.  
  - After that, you only need to consider the **second ride’s start time** (since waiting is deterministic).  
  - For each water ride `j`, the finish time is `waterDuration[j] + max(minLandFinish, waterStart[j])`.

The same logic mirrors *water first*.

Thus the optimal solution boils down to two simple scans:

1. Compute `minLandFinish`.
2. Compute `minWaterFinish`.
3. For every candidate of the *second* ride type, compute the finish time using the formula above.
4. Return the minimum.

The greedy choice is *obviously optimal* because any other sequence would finish no earlier than the one that uses the minimal‑finish first ride.

---

### 2.4 The Bad – Common Pitfalls

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| **Brute‑force all pairings** | `O(n*m)` (≤ 10⁴) is fine for the constraints but easily TLE on larger hidden tests | Replace with the greedy two‑pass approach |
| **Using `min()` of *start* times instead of *finish*** | You’ll underestimate wait times and miss the optimal pair | Always add the duration to the start before taking the minimum |
| **Ignoring order** | Returning the minimum of `minLandFinish` + `minWaterFinish` is wrong | Explicitly test both “land first” and “water first” cases |
| **Overflow with `int`** | Sum may reach 2·10⁵, still fine, but using `long` protects against accidental larger inputs | Use `long` if you’re paranoid, but `int` is safe per constraints |

---

### 2.5 The Ugly – Edge Cases That Trick the Unprepared

- **All rides start at the same time**: The algorithm still works because `max(minLandFinish, waterStart[j])` will be `waterStart[j]` for every water ride.
- **Very long duration on the second ride**: The formula still holds; you’re not affected by the order because the wait is determined by the first ride’s finish.
- **Only one ride of a type**: Still fine – the loop will run once and produce the correct finish time.

---

### 2.5 Interview‑Ready Code – 3 Clean Versions

*(See the code section above.)*

- Each snippet follows the same logical structure.
- Add comments explaining the two “cases” for interviewers who ask for **why** the algorithm works.
- Keep the class name `Solution` to match the LeetCode template.

---

### 2.6 Time & Space Complexity

- **Time**: `O(n + m)` – Two passes for first‑type min, then one pass over the second type for each case.  
- **Space**: `O(1)` – Only a handful of integer variables; no additional data structures are needed.

---

### 2.7 Takeaway – How to Shine in an Interview

1. **State the greedy rationale** – “I first finish the ride with the smallest `(start + duration)` because that gives me the earliest possible time to begin the second ride.”  
2. **Explain the two‑case symmetry** – Show that the same logic applies to water‑first and land‑first.  
3. **Write clean code** – Use meaningful variable names (`minLandFinish`, `minWaterFinish`) and avoid magic numbers.  
4. **Complexity trade‑off** – Be ready to discuss why an `O(n*m)` brute‑force is *suboptimal* and why your greedy approach beats it.

---

### 2.8 Closing Thoughts

LeetCode 3633 is a **small** but *conceptually powerful* scheduling problem.  
It trains you to:

- Spot the optimal greedy substructure early.
- Reduce a seemingly combinatorial task to a linear scan.
- Communicate the intuition cleanly – exactly what interviewers look for.

Feel confident to drop this problem into your portfolio, sprinkle it with the right keywords (“LeetCode 3633”, “Earliest Finish Time”, “Greedy algorithm”), and share your solution on LinkedIn or GitHub.  
A well‑explain blog post, together with polished Java/Python/C++ code, is a great signal to recruiters that you can **translate theory into production‑ready code**.  

Good luck – may your rides finish *earliest* and your interviews *smooth*!