---
title: LeetCode 630. Course Schedule III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# The “Course Schedule III” Interview Puzzle  
**LeetCode 630 – Hard**  
*What recruiters want to see? How to solve it in Java, Python, & C++.*

---

## TL;DR

| Language | Code |
|----------|------|
| **Java** | [View & copy](#java-code) |
| **Python** | [View & copy](#python-code) |
| **C++** | [View & copy](#c-code) |

> **Algorithm** – Greedy + Max‑Heap  
> **Time** – `O(n log n)`  
> **Space** – `O(n)`

---

## Why this problem rocks in a job interview

* **Greedy proof** – Shows you can reason about optimality.  
* **Heap data structure** – Gives you a chance to demonstrate familiarity with priority queues.  
* **Real‑world vibe** – Scheduling courses is like project management. Recruiters love to hear it.  

---

## Problem Recap

You’re given `courses`, an array where each element is `[duration, lastDay]`.  
You start on day 1, can’t overlap courses, and each course must finish **by** its `lastDay`.  
Return the **maximum number** of courses you can take.

---

## The “Good” – A clean, optimal greedy

1. **Sort courses by deadline (`lastDay`)** –  
   always try to finish the earliest‑due course first.
2. **Keep a max‑heap of durations** –  
   the heap holds all courses you’ve accepted so far.
3. **If adding a new course keeps you on‑time** → accept it.  
   Update current time and push its duration onto the heap.
4. **If adding it would make you late** →  
   look at the longest course you’re already taking (`heap.peek()`).  
   If that longest duration is **greater** than the new one, replace it:  
   * remove the longest, add the new one, adjust total time.  
   This frees up time and keeps the schedule tight.
5. **Result** – size of the heap is the max number of courses.

Why it works?  
Because the heap always stores the **shortest possible durations** that allow the earliest deadlines to be met.  
If at any point we are late, replacing the longest course with a shorter one can only help.

---

## The “Bad” – What to watch out for

| Issue | Why it matters | Fix |
|-------|----------------|-----|
| **Ties in deadlines** | Sorting by `lastDay` alone is enough, but be careful with `stable` sorting in some languages. | Use stable sort or add a secondary key (duration) if desired. |
| **Large input** | `n` can be 10⁴, so a linear or quadratic algorithm will TLE. | Use `O(n log n)` solution above. |
| **Integer overflow** | Cumulative time may exceed 2³¹‑1. | Use 64‑bit integers (`long` in Java, `long long` in C++, `int` in Python is unlimited). |

---

## The “Ugly” – Common pitfalls & edge cases

| Case | Problem | Solution |
|------|---------|----------|
| **Course takes longer than its deadline** | `[5, 3]` – impossible to finish. | The algorithm will skip it automatically. |
| **All courses impossible** | Example 3: `[[3,2],[4,3]]`. | Heap stays empty → return 0. |
| **Duplicate deadlines & durations** | Sorting must not misbehave. | Comparator should only use `lastDay`. |
| **Very large durations but few deadlines** | Time may exceed int limits. | Use 64‑bit integers. |
| **Negative values** | Constraints guarantee positives, but defensive coding is good. | Assert or guard against negatives. |

---

## The Code

Below are ready‑to‑paste solutions for **Java**, **Python**, and **C++**.  
All three use the same algorithmic idea.

### Java

```java
import java.util.*;

public class Solution {
    public int scheduleCourse(int[][] courses) {
        // 1️⃣ Sort by lastDay
        Arrays.sort(courses, Comparator.comparingInt(a -> a[1]));

        // 2️⃣ Max‑heap (largest duration on top)
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

        long currentTime = 0;               // 64‑bit to avoid overflow

        for (int[] course : courses) {
            int duration = course[0];
            int deadline = course[1];

            if (currentTime + duration <= deadline) {
                // 3️⃣ Accept the course
                maxHeap.offer(duration);
                currentTime += duration;
            } else if (!maxHeap.isEmpty() && maxHeap.peek() > duration) {
                // 4️⃣ Replace the longest taken course
                currentTime += duration - maxHeap.poll();
                maxHeap.offer(duration);
            }
            // else: skip this course
        }
        return maxHeap.size();
    }
}
```

### Python

```python
import heapq
from typing import List

class Solution:
    def scheduleCourse(self, courses: List[List[int]]) -> int:
        # Sort by deadline
        courses.sort(key=lambda x: x[1])

        # Max‑heap using negative durations
        max_heap = []
        current_time = 0

        for duration, deadline in courses:
            if current_time + duration <= deadline:
                heapq.heappush(max_heap, -duration)
                current_time += duration
            elif max_heap and -max_heap[0] > duration:
                current_time += duration + heapq.heappop(max_heap)  # pop largest, add new
                heapq.heappush(max_heap, -duration)

        return len(max_heap)
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int scheduleCourse(vector<vector<int>>& courses) {
        // 1️⃣ Sort by deadline
        sort(courses.begin(), courses.end(),
             [](const vector<int>& a, const vector<int>& b) {
                 return a[1] < b[1];
             });

        // 2️⃣ Max‑heap of durations
        priority_queue<int> maxHeap;
        long long curTime = 0;               // 64‑bit

        for (const auto& c : courses) {
            int dur = c[0], dl = c[1];
            if (curTime + dur <= dl) {
                maxHeap.push(dur);
                curTime += dur;
            } else if (!maxHeap.empty() && maxHeap.top() > dur) {
                curTime += dur - maxHeap.top();
                maxHeap.pop();
                maxHeap.push(dur);
            }
        }
        return (int)maxHeap.size();
    }
};
```

---

## Blog Article: “The Good, The Bad, and The Ugly of Course Schedule III”

> **Title** – *“Mastering LeetCode 630: Course Schedule III – The Good, The Bad & The Ugly”*  
> **Meta‑Description** – *Learn the greedy heap solution to LeetCode 630, dive into edge cases, and ace your next software‑engineering interview.*

---

### 1️⃣ The Good: Why This Solution Rocks

- **Simplicity + Power** – A single pass after sorting gives the optimal answer.  
- **Intuitive Greedy** – “Take the course with the earliest deadline first.”  
- **Heap Leverage** – Replacing the longest course is O(log n), keeping the schedule tight.  
- **Real‑World Parallel** – Similar to task scheduling, project planning, or CPU job scheduling.

### 2️⃣ The Bad: Common Pitfalls to Avoid

| Pitfall | Fix |
|---------|-----|
| Using `int` for cumulative time | Switch to `long` / `long long` to avoid overflow. |
| Ignoring the possibility of early deadlines | Always sort by deadline; a wrong sort gives sub‑optimal results. |
| Assuming all courses fit | The algorithm skips impossible courses automatically. |
| Complexity creep | A naïve `O(n²)` solution (e.g., backtracking) will TLE on 10⁴ inputs. |

### 3️⃣ The Ugly: Edge Cases & Tuning

- **Exact deadline match** – Course finishes on its `lastDay` is allowed.  
- **Large durations** – Even if a course’s duration is larger than its deadline, the algorithm discards it naturally.  
- **Ties in deadlines** – The order of courses with identical `lastDay` doesn’t affect correctness, but a stable sort keeps the logic clear.  
- **Memory footprint** – Heap stores at most `n` durations, so `O(n)` memory is acceptable.

---

## Interview Tips: Talking About This Problem

1. **Explain the greedy invariant** – “If you can finish a course by its deadline, it's always safe to take it; otherwise, we replace the longest one if it frees up time.”
2. **Show the heap intuition** – “We need quick access to the longest duration so we can swap it out.”
3. **Mention complexity** – “Sorting `O(n log n)`, each heap operation `O(log n)`, total `O(n log n)`.”
4. **Discuss edge cases** – “What if a course’s duration exceeds its deadline? Our algorithm skips it.”

---

## SEO & Job‑Hunting Boost

| Keyword | Why it helps |
|---------|--------------|
| `LeetCode 630` | Direct match for recruiters searching this problem. |
| `Course Schedule III solution` | High search volume for interview prep. |
| `Java priority queue LeetCode` | Targets Java‑focused interviews. |
| `Python heapq interview` | Showcases Python skills. |
| `C++ priority_queue scheduling` | Highlights C++ proficiency. |
| `Greedy algorithm interview` | Demonstrates algorithmic thinking. |
| `Software engineer interview tips` | Broad audience. |

**Include these keywords naturally in headings, bullets, and the meta description**. A well‑structured article with clear code snippets will rank higher and catch recruiters’ eyes.

---

### Final Checklist Before You Submit

- ✅ Code compiles in Java 17 / Python 3.10 / C++17.  
- ✅ All edge cases covered.  
- ✅ Complexity stated.  
- ✅ Blog article ready for Medium, Dev.to, or your own portfolio.  
- ✅ Keywords sprinkled throughout.

Good luck! 🚀

---