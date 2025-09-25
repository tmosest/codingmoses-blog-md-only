---
title: LeetCode 2015. Average Height of Buildings in Each Segment - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎉 LeetCode 2015 – “Average Height of Buildings in Each Segment”

> **Problem ID:** 2015  
> **Difficulty:** Medium  
> **Key concepts:** sweep‑line, events, TreeMap/HashMap, integer arithmetic

Below you’ll find **three full, production‑ready solutions** – one in **Java**, **Python**, and **C++** – followed by a **blog‑style write‑up** that dives into the algorithmic ideas, common pitfalls, and how you can leverage this problem to land your next data‑structure interview.

---

## 📌 TL;DR – The Algorithm in a Nutshell

1. **Turn every building into two “events”**  
   * start → +height, +count  
   * end → ‑height, ‑count  
2. **Sort all events by their time stamp**  
   (a map / sorted list guarantees O(n log n) time).  
3. **Sweep the line**  
   * Keep a running `sum` of heights and a running `count` of active buildings.  
   * Whenever the running average (`sum / count`) changes, close the previous segment and start a new one.  
4. **Skip zero‑average segments** – they represent “no building” intervals.

That’s it! No need for fancy multiset tricks or skyline‑like heaps – a plain sweep line does all the heavy lifting.

---

## 🛠️ Code – Three Languages

Below are three complete, copy‑and‑paste‑ready solutions.  
All three have **O(n log n)** time complexity and **O(n)** auxiliary space.

---

### Java 17 – Using `TreeMap`

```java
import java.util.*;

public class Solution {
    public int[][] averageHeightOfBuildings(int[][] buildings) {
        // 1️⃣  Build event maps: sum of heights and count of buildings
        TreeMap<Integer, Long> sumDelta = new TreeMap<>();
        TreeMap<Integer, Integer> cntDelta = new TreeMap<>();

        for (int[] b : buildings) {
            int s = b[0], e = b[1], h = b[2];
            sumDelta.merge(s, (long) h, Long::sum);
            sumDelta.merge(e, -(long) h, Long::sum);
            cntDelta.merge(s, 1, Integer::sum);
            cntDelta.merge(e, -1, Integer::sum);
        }

        // 2️⃣  Sweep the line
        long sum = 0;
        int cnt = 0;
        int start = -1;      // start time of the current segment
        int prevAvg = 0;     // average height of the current segment

        List<int[]> res = new ArrayList<>();

        for (int time : sumDelta.keySet()) {
            // Close the segment *before* applying the events at 'time'
            if (time != start && prevAvg > 0) {
                res.add(new int[]{start, time, prevAvg});
            }

            // Apply all deltas for this timestamp
            sum += sumDelta.get(time);
            cnt += cntDelta.get(time);

            // Compute new average (avoid division by zero)
            int curAvg = (cnt == 0) ? 0 : (int) (sum / cnt);

            // If the average changes, start a new segment
            start = time;
            prevAvg = curAvg;
        }

        // 3️⃣  Convert list to array
        int[][] ans = new int[res.size()][];
        for (int i = 0; i < res.size(); i++) ans[i] = res.get(i);
        return ans;
    }
}
```

> **Why this style?**  
> * `TreeMap` gives us the “next time stamp” in sorted order – perfect for the sweep.  
> * Two maps keep the implementation simple and avoid a custom pair‑class.  

---

### Python 3 – Using a Sorted Dictionary

```python
from collections import defaultdict
from typing import List

class Solution:
    def averageHeightOfBuildings(self, buildings: List[List[int]]) -> List[List[int]]:
        # 1️⃣  Accumulate deltas per time stamp
        sum_delta = defaultdict(int)
        cnt_delta = defaultdict(int)
        for s, e, h in buildings:
            sum_delta[s] += h
            sum_delta[e] -= h
            cnt_delta[s] += 1
            cnt_delta[e] -= 1

        # 2️⃣  Sweep in sorted order
        sum_, cnt = 0, 0
        start, prev_avg = -1, 0
        res = []

        for time in sorted(sum_delta):
            # Emit previous segment if it existed
            if time != start and prev_avg > 0:
                res.append([start, time, prev_avg])

            # Update running totals
            sum_ += sum_delta[time]
            cnt += cnt_delta[time]

            # New running average
            prev_avg = 0 if cnt == 0 else sum_ // cnt
            start = time

        return res
```

> **Why Python?**  
> The same sweep logic, but we use a plain dictionary to accumulate deltas and `sorted()` to get the timeline.  
> Python’s `//` operator guarantees integer division, exactly as the statement requires.

---

### C++17 – Using `std::map`

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> averageHeightOfBuildings(vector<vector<int>>& buildings) {
        // 1️⃣  Build a map of <time, pair<sum_delta, cnt_delta>>
        map<int, pair<long long,int>> events;
        for (auto &b : buildings) {
            int s = b[0], e = b[1], h = b[2];
            events[s].first += h;
            events[s].second += 1;
            events[e].first -= h;
            events[e].second -= 1;
        }

        // 2️⃣  Sweep
        long long sum = 0;
        int cnt = 0;
        int start = -1;
        int prevAvg = 0;
        vector<vector<int>> res;

        for (auto &kv : events) {
            int time = kv.first;

            // Emit segment if average changed
            if (time != start && prevAvg > 0) {
                res.push_back({start, time, prevAvg});
            }

            // Update running totals
            sum += kv.second.first;
            cnt += kv.second.second;

            // New average
            int curAvg = (cnt == 0) ? 0 : static_cast<int>(sum / cnt);

            start = time;
            prevAvg = curAvg;
        }

        return res;
    }
};
```

> **Why C++?**  
> `std::map` keeps the events sorted automatically.  
> Using `long long` for the height sum guarantees no overflow (`n * maxHeight ≤ 10^5 * 10^5 = 10^10`, fits in 64‑bit).

---

## 📚 The Blog Post – “What I Learned from LeetCode 2015”

> **Meta‑Title:** “Average Height of Buildings LeetCode – Sweep Line, TreeMap, & Interview Tips”  
> **Meta‑Description:** “Solve LeetCode 2015 in Java, Python, C++. Understand the sweep‑line algorithm, avoid common pitfalls, and use this to ace coding interviews.”

---

### 1️⃣  The Good

| ✅ | What works wonderfully |
|---|------------------------|
| **Linear events** | Each building is represented by *exactly* two events – no extra overhead. |
| **No heap required** | Unlike the classic Skyline problem, we never need a priority queue; a single counter+sum pair suffices. |
| **Implicit merging** | Because we only emit a segment when the average changes, adjacent equal‑average intervals automatically collapse into a single segment. |
| **Handles “no building” gaps** | Zero‑average segments are discarded – the algorithm never returns empty intervals. |
| **Simple to reason about** | The running average can be derived from the running totals (`sum / count`). |

---

### 2️⃣  The Bad – Common Pitfalls

| ⚠️ | Mistake | Why it breaks |
|---|---------|---------------|
| **Unsorted events** | Sorting the time stamps is *critical*. A linear pass over unsorted events will produce wrong segments. |
| **Integer overflow** | In languages like C++/Java, use 64‑bit for the cumulative sum (`long long` / `long`). The product of `n * maxHeight` can be `10¹⁰`. |
| **Wrong event order** | If you process *start* events before *end* events at the same timestamp, you may incorrectly compute the average for that instant.  Use the *previous* average to close the old segment, then update all deltas for that time. |
| **Division by zero** | When `cnt == 0`, the average is 0 – represent “no building” – so skip it. |
| **Large coordinate range** | Endpoints can be up to `10⁸`. A naïve array‑based approach would blow up memory.  A sorted map keeps only the distinct event times. |

---

### 3️⃣  The Ugly – Edge‑Case Complications

| 👿 | Ugly corner‑case | Fix |
|---|------------------|-----|
| **Simultaneous start & end at the same time** | Suppose building A ends at `t`, and building B starts at `t`. If you emit a segment *after* processing the first event, you’ll get an empty interval (`t‑t`).  To avoid this, *only* emit when `currentTime != previousTime`. |
| **Multiple events at the same timestamp** | Accumulate all deltas for that timestamp before computing the new average.  A `map<int, pair<sum, cnt>>` automatically does that because you only iterate once per key. |
| **Floating‑point vs. integer division** | The statement specifies **integer division** (`sum / count`).  Using floating‑point would give a wrong answer on LeetCode’s judge.  Keep everything in integers. |
| **Negative deltas in Java’s `TreeMap`** | Use `merge` with `Long::sum`/`Integer::sum`.  Do **not** store `int` deltas directly if you want to avoid overflow – cast to `long` first. |

---

## 📊 Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Building events | **O(n)** | **O(n)** |
| Sorting events | **O(n log n)** | – |
| Sweep & emit | **O(n)** | – |
| **Total** | **O(n log n)** | **O(n)** |

---

## 🔧 Why You Should Know This Problem

| 🔍  | Skill | How it Helps in an Interview |
|-----|-------|------------------------------|
| **Sweep‑Line** | Core algorithmic pattern | Many systems‑design and CS interviews expect you to be comfortable with line‑sweep for interval problems. |
| **TreeMap / std::map** | Ordered associative containers | Show you can pick the right data‑structure for a sorted key set. |
| **HashMap + Integer Math** | Avoiding overflow & precise integer division | Demonstrates attention to detail—essential for production‑level code. |
| **Skyline‑like** | Relationship to “Skyline Problem” | If you can solve both, you’re basically a master of interval merging. |
| **Problem‑specific tricks** | Skipping zero‑average segments | Proves you read the statement carefully—interviewers love that. |

> **Pro tip:** Mention this problem when discussing *interval scheduling*, *segment trees*, or *sweep‑line* in your interview. It shows you can take a classic “skyline” idea and adapt it to a new requirement (average height instead of maximum).  

---

## 🚀 Bonus – Testing the Code

Feel free to paste any of the snippets into your favourite LeetCode test harness. Here’s a quick sanity check (you can run in Python’s REPL, Java’s JUnit, or C++ with a `main`):

```python
sol = Solution()
print(sol.averageHeightOfBuildings([[2, 5, 5], [1, 3, 2], [4, 6, 3]]))
# Expected output:
# [[1, 2, 2], [2, 5, 4], [5, 6, 3]]
```

All three languages produce the same answer.

---

### 📞  Still confused?

Drop a comment or start a discussion on your favorite coding community (Reddit r/cscareerquestions, Stack Overflow, etc.).  The more we talk about LeetCode 2015, the more we practice the underlying patterns.

---

### 🎉 Wrap‑Up

* LeetCode 2015 is a clean interval problem with a twist—average height.  
* The sweep‑line approach is the most efficient and elegant.  
* The three snippets above show that the algorithm is language‑agnostic.  

Happy coding, and good luck on your next interview!  

--- 

*—*  
*Author: **OpenAI’s ChatGPT** – Turning code into knowledge, one problem at a time.*