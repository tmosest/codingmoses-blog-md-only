---
title: LeetCode 2589. Minimum Time to Complete All Tasks - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 2589 – Minimum Time to Complete All Tasks  
**Solutions in Java | Python | C++**  
**SEO‑optimized guide** that covers the *good, the bad, and the ugly* of this problem, perfect for interview prep and job hunting.

---

### 📌 Problem Recap  

- **Input**: `tasks[i] = [start_i, end_i, duration_i]`  
  *The task must run for `duration_i` seconds (not necessarily continuous) within the inclusive window `[start_i, end_i]`.*  
- **Computer**: can run an unlimited number of tasks at the same time.  
- **Goal**: Find the **minimum total seconds** the computer has to be turned on to finish *all* tasks.

> **Constraints**  
> * 1 ≤ tasks.length ≤ 2000  
> * 1 ≤ start_i ≤ end_i ≤ 2000  
> * 1 ≤ duration_i ≤ end_i − start_i + 1  

---

## ✅ The “Good” – Why This Solution Rocks  

| Language | Time Complexity | Space Complexity | Why it works |
|----------|----------------|------------------|--------------|
| **Java** | `O(n * w)` (worst‑case) → `O(n * 2000)` ≈ `O(n)` | `O(2000)` | Greedy + Boolean array |
| **Python** | Same as Java | Same as Java | Same as Java |
| **C++** | Same as Java | Same as Java | Same as Java |

- **Greedy on the right‑endpoint** guarantees that every interval has already “checked” all possible overlaps with the intervals that finish earlier.  
- The Boolean/bit array (`used[1..2000]`) marks every second the computer is “on”.  
- We **first** consume any seconds already marked inside the task’s window, then we **add the remaining seconds** starting from the interval’s `end` and moving left.  
- The final answer is simply the count of marked seconds.

---

### 🛠️ The Code  

> All three implementations assume the LeetCode environment: a single method/function named `findMinimumTime` (or the Python `Solution` class).

---

#### Java (Java 17+)

```java
import java.util.Arrays;
import java.util.Comparator;

public class Solution {
    /**
     * LeetCode 2589 – Minimum Time to Complete All Tasks
     * Greedy + Boolean array (max index 2000)
     */
    public int findMinimumTime(int[][] tasks) {
        // Sort by the right endpoint
        Arrays.sort(tasks, Comparator.comparingInt(a -> a[1]));

        // Marked seconds: index 1..2000
        boolean[] used = new boolean[2001];

        for (int[] t : tasks) {
            int start = t[0];
            int end   = t[1];
            int dur   = t[2];

            // 1️⃣ Count how many seconds already used in this window
            for (int i = start; i <= end && dur > 0; i++) {
                if (used[i]) {
                    dur--;               // this second counts for the task
                }
            }

            // 2️⃣ Fill the remaining needed seconds greedily from the right
            for (int i = end; dur > 0 && i >= start; i--) {
                if (!used[i]) {
                    used[i] = true;     // turn computer on at this second
                    dur--;              // consume one second of the task
                }
            }
        }

        // Sum of all marked seconds is the answer
        int ans = 0;
        for (boolean b : used) if (b) ans++;
        return ans;
    }
}
```

---

#### Python (Python 3.8+)

```python
from typing import List

class Solution:
    def findMinimumTime(self, tasks: List[List[int]]) -> int:
        """
        LeetCode 2589 – Minimum Time to Complete All Tasks
        Greedy + Boolean array (size 2001)
        """
        # Sort by the interval end
        tasks.sort(key=lambda x: x[1])

        used = [0] * 2001  # index 1..2000

        for start, end, duration in tasks:
            # 1️⃣ Consume already marked seconds inside the window
            for i in range(start, end + 1):
                if used[i]:
                    duration -= 1
                if duration == 0:
                    break

            # 2️⃣ Add the missing seconds greedily from right to left
            i = end
            while duration > 0:
                if used[i] == 0:
                    used[i] = 1
                    duration -= 1
                i -= 1

        return sum(used)
```

---

#### C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findMinimumTime(vector<vector<int>>& tasks) {
        // Sort by the right endpoint
        sort(tasks.begin(), tasks.end(),
             [](const vector<int>& a, const vector<int>& b) {
                 return a[1] < b[1];
             });

        vector<int> used(2001, 0);   // index 1..2000

        for (auto& t : tasks) {
            int l = t[0], r = t[1], dur = t[2];

            // 1️⃣ Count already used seconds in this window
            for (int i = l; i <= r && dur; ++i) {
                if (used[i]) dur--;
            }

            // 2️⃣ Add missing seconds greedily from the right
            for (int i = r; i >= l && dur; --i) {
                if (!used[i]) {
                    used[i] = 1;
                    dur--;
                }
            }
        }

        // Sum of all marked seconds
        return accumulate(used.begin(), used.end(), 0);
    }
};
```

---

## 📝 The Blog: “The Good, The Bad, and The Ugly of Solving LeetCode 2589”

> **Meta‑description (SEO):**  
> *Learn how to crack LeetCode 2589 – Minimum Time to Complete All Tasks. Get Java, Python, and C++ solutions, algorithmic intuition, time complexity, and interview‑ready tips.*

---

### 1️⃣ What the Problem Wants (Good)

| Point | Why It Helps |
|-------|--------------|
| **Intervals + Duration** | Classic “interval scheduling” flavor. |
| **Unlimited Parallelism** | Makes the problem about *coverage* rather than classic scheduling. |
| **Small time horizon (≤2000)** | Allows us to use a simple O(2000) “time line” array. |

The **good** part is that the constraints are generous: you can mark each second explicitly. That eliminates the need for complex data structures like segment trees or binary indexed trees.

---

### 2️⃣ The Intuition (Good)

1. **Overlap is Free**  
   *If two tasks share a second, the computer is already on for that second; no extra cost.*  
2. **Greedy from the Right**  
   *For any task, we try to finish as many remaining seconds as possible in the latest seconds of its interval.*  
3. **Sort by `end`**  
   *When intervals are sorted by their right end, all possible overlaps with previously processed intervals have already been considered.*

> **Why Sorting by `start` fails**  
> If you sort by the left endpoint, a task that starts early may “steal” a second from a later task that needs that second, causing the algorithm to over‑turn the computer on.

---

### 3️⃣ The Core Algorithm (Good)

```
sort tasks by end time
used[1..2000] = 0          // Boolean array: 1 if computer is on at that second
for each task [l, r, d] in sorted order:
    // 1️⃣ Count already used seconds inside [l, r]
    for i = l .. r:
        if used[i] : d -= 1
        if d == 0 : break

    // 2️⃣ Add remaining seconds greedily from the right
    i = r
    while d > 0:
        if !used[i]:
            used[i] = 1
            d -= 1
        i -= 1

answer = sum(used)
```

- **Time**:  
  *Worst‑case*: every task scans its entire interval → `O(n * 2000)` ≈ `O(n)` because 2000 is a constant.  
- **Space**: `O(2000)` for the Boolean array → constant.

---

### 4️⃣ The Pitfalls (Bad)

| Pitfall | Symptom | Fix |
|---------|---------|-----|
| **Wrong sort key** | Wrong answer or TLE | Always sort by `end` (`tasks[i][1]`). |
| **Off‑by‑one in the Boolean array** | Array index 0 or 2000 missing | Allocate size `2001` (or `2005` for safety). |
| **Ignoring already used seconds** | Over‑counting turns on | Subtract `used[i]` from `duration` before adding new seconds. |
| **Using a `Set<Integer>`** | Works but slower (`O(log n)` per insert) | Use a boolean/bit array – constant time. |
| **Processing tasks in input order** | Wrong result for overlapping tasks | Sort before processing. |

---

### 5️⃣ The “Ugly” – When Things Get Complicated

1. **If `duration_i` were larger than the interval length**  
   The greedy strategy still works because the task can be split arbitrarily, but you must ensure you don't go out of bounds while filling from the right.  
   *Solution*: clamp the loop to `i >= l` and stop once `duration` becomes 0.

2. **Large input range (>2000)**  
   In a hypothetical variant with `start_i, end_i` up to 10⁹, the Boolean array is impossible.  
   *Alternative*: use a **`TreeSet` or `HashSet`** to store used seconds and a **segment tree** or **binary indexed tree** for range queries.  
   The time line approach is no longer “good” but becomes “ugly” – more code, more bugs.

3. **Multiple tasks requiring exactly the same second**  
   If many tasks have identical windows, the algorithm still counts each second once, but you must not double‑count.  
   *Insight*: the algorithm’s first loop already consumes all overlapping seconds, so the second loop only adds new unique seconds.

---

### 6️⃣ Interview Tips (Ready‑to‑Use)

- **Explain the “line” idea** before diving into code.  
- **Show the key proof** that sorting by `end` is optimal: any later interval cannot benefit from earlier unused seconds.  
- **Mention the constant‑time Boolean array** – it’s a great trick to avoid data‑structure overhead in coding interviews.  
- **Prepare for follow‑up questions**: “What if the horizon was huge?” – be ready to discuss segment trees or coordinate compression.

---

### 6️⃣ Final Thought

Cracking LeetCode 2589 is all about **coverage**. Once you realize that overlap doesn’t cost anything and that the time horizon is tiny, the problem reduces to a simple greedy sweep.  
The provided Java, Python, and C++ code illustrates the same elegant idea, letting you focus on the **algorithmic insight** rather than battling implementation details.

Good luck on your coding journey and your next technical interview! 🚀

--- 

> *Feel free to copy/paste the code snippets or the entire blog into your notes. Happy coding!*

---



--- 
**Enjoy the “good” code, avoid the “bad” pitfalls, and be prepared to tackle the “ugly” edge cases if the problem changes.** 

--- 

> **TL;DR**: Sort by `end`, use a Boolean array, consume already‑used seconds, greedily add missing seconds from the right, sum up marked seconds. All three language implementations deliver the same `O(n)` time and `O(1)` space solution.

--- 

Happy coding! 🎉🚀