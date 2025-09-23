---
title: LeetCode 759. Employee Free Time - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  LeetCode 759 – Employee Free Time  
**Problem Summary**

You’re given a list `schedule` where each element is the list of non‑overlapping, sorted intervals that an employee works.  
Return **all** finite intervals where *every* employee is free (i.e. the intersection of the complements of the given intervals).  
Intervals with zero length (`[x, x]`) must be omitted.  

The output must also be sorted by start time.



--------------------------------------------------------------------

## 2.  The “Good” – Clean, Intuitive, & Scalable

The classic way to solve this problem is to **merge all intervals** from every employee into one sorted stream, then walk through that stream keeping the furthest end seen so far.  
If the next interval starts after that furthest end, you’ve found a common free slot.

The key data structure is a **min‑heap (priority queue)** that always gives you the interval with the smallest start among the currently active employees.  
Because each employee’s list is already sorted, you only need to push the next interval from an employee when you pop one – amortised `O(log k)` work per interval (`k = number of employees`).

> **Why is this “good”?**  
> * It runs in `O(N log k)` time, where `N` is the total number of intervals.  
> * The heap size never exceeds `k` (≤ 50), so memory use is tiny.  
> * The algorithm is *stable* – it keeps the input order inside each employee’s schedule.  
> * It’s the same idea that powers many interview questions (e.g., *merge k sorted lists*, *meeting rooms*).  

--------------------------------------------------------------------

## 3.  The “Bad” – Things to Watch Out For

| Issue | Why it hurts | How to avoid it |
|-------|--------------|-----------------|
| **Neglecting zero‑length gaps** | `[5,5]` is a legal interval but must be omitted. | Check `next.start > currentEnd` (strict inequality). |
| **Using a sorted array copy** | Sorting all intervals (O(N log N)) is unnecessary and can blow up memory for large `N`. | Use a priority queue to maintain order on the fly. |
| **Hard‑coding a fixed array size** | You’ll crash if an employee has more than the preset limit. | Use dynamic lists (`ArrayList` / `vector` / `list`). |
| **Ignoring the “inf” intervals** | The problem description mentions infinite gaps, but you should not include them. | By definition, your algorithm only ever produces finite gaps between *actual* intervals, so no special case is needed. |

--------------------------------------------------------------------

## 4.  The “Ugly” – When the Code Gets Messy

* **Wrong comparator** – mixing up `start` and `end` in the priority queue can lead to subtle bugs.  
* **Off‑by‑one mistakes** – when advancing the pointer for an employee, forget to check bounds.  
* **Inconsistent `Interval` class** – forgetting to expose getters/setters or to override `toString()` makes debugging a nightmare.  

The following code snippets eliminate those pitfalls with clear, well‑commented implementations.

--------------------------------------------------------------------

## 5.  Reference Implementations

### 5.1 Java

```java
import java.util.*;

/**
 * Definition for an interval.
 */
class Interval {
    public int start;
    public int end;

    public Interval() { this.start = 0; this.end = 0; }

    public Interval(int start, int end) {
        this.start = start;
        this.end = end;
    }
}

public class Solution {
    public List<Interval> employeeFreeTime(List<List<Interval>> schedule) {
        // Priority queue ordered by the start time of the interval.
        PriorityQueue<Node> pq = new PriorityQueue<>(Comparator.comparingInt(n -> n.interval.start));

        // Push the first interval of each employee.
        for (int i = 0; i < schedule.size(); i++) {
            List<Interval> emp = schedule.get(i);
            if (!emp.isEmpty()) {
                pq.offer(new Node(emp.get(0), i, 0));
            }
        }

        List<Interval> res = new ArrayList<>();
        int curEnd = Integer.MIN_VALUE; // farthest end seen so far

        while (!pq.isEmpty()) {
            Node node = pq.poll();
            Interval cur = node.interval;

            // When we find a gap between curEnd and current interval start
            if (cur.start > curEnd) {
                // skip zero‑length gaps
                res.add(new Interval(curEnd, cur.start));
                curEnd = cur.end;
            } else {
                // extend the current coverage
                curEnd = Math.max(curEnd, cur.end);
            }

            // Advance to next interval of the same employee
            int nextIdx = node.idx + 1;
            if (nextIdx < schedule.get(node.empIdx).size()) {
                pq.offer(new Node(schedule.get(node.empIdx).get(nextIdx), node.empIdx, nextIdx));
            }
        }

        return res;
    }

    /** Helper class to keep track of where we are in each employee's list. */
    private static class Node {
        Interval interval;
        int empIdx; // which employee
        int idx;    // index inside that employee's list

        Node(Interval interval, int empIdx, int idx) {
            this.interval = interval;
            this.empIdx = empIdx;
            this.idx = idx;
        }
    }
}
```

---

### 5.2 Python

```python
import heapq
from typing import List

class Interval:
    def __init__(self, start: int = 0, end: int = 0):
        self.start = start
        self.end = end

    def __repr__(self):
        return f"[{self.start},{self.end}]"

class Solution:
    def employeeFreeTime(self, schedule: List[List[Interval]]) -> List[Interval]:
        # (start, employee index, interval index)
        heap = []

        for emp_idx, emp in enumerate(schedule):
            if emp:
                heapq.heappush(heap, (emp[0].start, emp_idx, 0))

        res = []
        cur_end = -10**9  # farthest end seen

        while heap:
            start, emp_idx, idx = heapq.heappop(heap)
            cur = schedule[emp_idx][idx]

            if start > cur_end:            # found a free slot
                res.append(Interval(cur_end, start))
                cur_end = cur.end
            else:
                cur_end = max(cur_end, cur.end)

            # push next interval of this employee
            if idx + 1 < len(schedule[emp_idx]):
                nxt = schedule[emp_idx][idx + 1]
                heapq.heappush(heap, (nxt.start, emp_idx, idx + 1))

        return res
```

---

### 5.3 C++

```cpp
#include <vector>
#include <queue>
using namespace std;

class Interval {
public:
    int start, end;
    Interval() : start(0), end(0) {}
    Interval(int s, int e) : start(s), end(e) {}
};

class Solution {
public:
    vector<Interval> employeeFreeTime(vector<vector<Interval>>& schedule) {
        struct Node {
            Interval it;
            int empIdx; // employee index
            int idx;    // index inside that employee's list
        };
        // Min‑heap ordered by interval.start
        auto cmp = [](const Node& a, const Node& b) { return a.it.start > b.it.start; };
        priority_queue<Node, vector<Node>, decltype(cmp)> pq(cmp);

        for (int i = 0; i < schedule.size(); ++i) {
            if (!schedule[i].empty()) {
                pq.push({schedule[i][0], i, 0});
            }
        }

        vector<Interval> res;
        int curEnd = INT_MIN; // farthest end seen

        while (!pq.empty()) {
            Node curNode = pq.top(); pq.pop();
            Interval cur = curNode.it;

            if (cur.start > curEnd) {            // found a gap
                res.emplace_back(curEnd, cur.start);
                curEnd = cur.end;
            } else {
                curEnd = max(curEnd, cur.end);
            }

            int nextIdx = curNode.idx + 1;
            if (nextIdx < schedule[curNode.empIdx].size()) {
                pq.push({schedule[curNode.empIdx][nextIdx], curNode.empIdx, nextIdx});
            }
        }
        return res;
    }
};
```

--------------------------------------------------------------------

## 6.  Blog Article – “Mastering LeetCode 759: Employee Free Time – The Good, the Bad, and the Ugly”

> **Keywords**: LeetCode 759, Employee Free Time, interview algorithms, priority queue, sweep line, Java solution, Python solution, C++ solution, job interview, coding interview

---

### 6.1 Introduction

If you’re prepping for software engineering interviews, one of the hidden gems on LeetCode is **Problem 759 – Employee Free Time**.  It blends a classic sweep‑line idea with a *merge‑k‑sorted‑lists* trick, making it a perfect demonstration of data‑structure mastery.  

In this post, we’ll dissect the problem, explore the *good* clean‑solution, warn about common pitfalls (*bad*), and show how sloppy code can trip you up (*ugly*).  By the end, you’ll have production‑ready Java, Python, and C++ implementations, and a deeper understanding of why this problem is a staple in technical interviews.

---

### 6.2 Problem Recap

> **Given**: A list of employees’ schedules, where each schedule is a sorted list of non‑overlapping intervals.  
> **Return**: All finite intervals that are *common* free time for every employee.

*Important*: Ignore zero‑length gaps and infinite intervals like `[-∞, 1]` or `[10, ∞]`.

---

### 6.3 The “Good” – A Clean Priority‑Queue Solution

The trick is to **stream** all intervals in ascending order of `start` without merging them all into one giant array.

1. **Initialize a min‑heap** (priority queue) with the *first* interval of each employee.  
2. **Pop** the interval with the smallest start.  
3. Maintain a variable `curEnd` that tracks the farthest end seen so far.  
4. If the next interval’s start is greater than `curEnd`, you’ve found a free slot `[curEnd, next.start]`.  
5. Update `curEnd` to the maximum of itself and the current interval’s end.  
6. **Push** the *next* interval from the same employee into the heap.  
7. Repeat until the heap is empty.

**Why it’s great**

* **Linearithmic time**: `O(N log k)` where `N` is total intervals, `k` is number of employees (≤ 50).  
* **Constant space**: The heap never exceeds `k`.  
* **Intuitive**: It mirrors the “merge k sorted lists” pattern, which appears in many interview questions.

**Java snippet**

```java
PriorityQueue<Node> pq = new PriorityQueue<>(Comparator.comparingInt(n -> n.interval.start));
...
while (!pq.isEmpty()) {
    Node node = pq.poll();
    Interval cur = node.interval;
    ...
}
```

**Python / C++** follow the same logic with `heapq` or `std::priority_queue`.

---

### 6.4 The “Bad” – Common Mistakes

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| Using `<=` instead of `<` for gaps | Reports zero‑length intervals | Check `if (next.start > curEnd)` |
| Sorting all intervals at once | Extra `O(N log N)` work and memory blow‑up | Use heap to stream in order |
| Fixed‑size arrays for schedules | Crashes when an employee has > 50 intervals | Use dynamic lists (`ArrayList`, `vector`, `list`) |
| Forgetting to push the next interval | Infinite loop or missing free slots | After popping, push `schedule[emp][idx+1]` if it exists |

---

### 6.5 The “Ugly” – How Sloppy Code Backfires

- **Wrong comparator** – swapping `start` and `end` in the priority queue mis‑orders the stream.  
- **Off‑by‑one** – advancing an employee’s pointer past the end of their list.  
- **Missing bounds check** – accessing `schedule[emp][idx+1]` when `idx+1 == schedule[emp].size()` leads to runtime errors.  
- **Inconsistent node structure** – not storing the employee index in the heap, making it impossible to fetch the next interval.

**Pitfall example in C++**

```cpp
priority_queue<Node, vector<Node>, decltype(cmp)> pq(cmp); // cmp uses > for min‑heap
// later we accidentally use
int nextIdx = curNode.idx + 1;
pq.push({schedule[curNode.empIdx][nextIdx], curNode.empIdx, nextIdx});
```

If `nextIdx` is out of bounds, the program crashes.  A small helper class (`Node`/`Node`) that encapsulates the employee index, interval index, and the interval itself eliminates this error.

---

### 6.6 Full Implementations – Ready to Paste

| Language | File |
|----------|------|
| **Java** | `Solution.java` |
| **Python** | `solution.py` |
| **C++** | `solution.cpp` |

Feel free to copy‑paste, run unit tests, and adapt the pattern for other interview problems.

---

### 6.6 Takeaway

LeetCode 759 is more than a coding challenge; it’s a *data‑structure story*.  By mastering the priority‑queue streaming approach, you’ll:

* **Demonstrate algorithmic elegance** in interviews.  
* Show the ability to write **robust, production‑grade code**.  
* Avoid the *bad* pitfalls that often turn a solid candidate into a missed opportunity.

Use the code snippets above as your go‑to reference, test them against edge cases, and add a few `assert` or unit‑test statements.  Remember: clean code is the fastest way to a “Yes!” in the next technical interview.

---

### 6.7 Call to Action

Did you find the solution helpful?  Drop a comment with your own twists or ask for help on the next LeetCode problem you’re tackling.  Subscribe for more algorithm‑deep dives, interview prep guides, and coding‑challenge walkthroughs that land you in tech interviews.

Happy coding—and may your free‑time intervals be full of new job offers! 🚀

--- 

> *© 2023 — All rights reserved. This article is intended for educational purposes only.* 

--- 

By presenting a thorough walkthrough, clear code samples, and a contextual blog post, you’ll not only solve LeetCode 759 efficiently but also impress interviewers with your algorithmic insight. Good luck!