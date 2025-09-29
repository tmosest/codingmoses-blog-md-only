---
title: LeetCode 759. Employee Free Time - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1. Code Solutions

Below are three independent, fully‑commented implementations that solve **LeetCode 759 – Employee Free Time**.  
Each implementation follows the same *merge‑intervals* strategy:

1. Flatten all employees’ schedules into a single list of intervals.  
2. Sort the list by start time.  
3. Merge overlapping intervals while recording the gaps between them.

The result is the list of finite free intervals common to **all** employees.

> **Tip** – In an interview, mention that the approach works in `O(N log N)` time (dominated by the sort) and `O(1)` extra space beyond the result list.

---

## Java – Clean & Intuitive

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

/**
 * Definition for an interval.
 * You can also use the provided Interval class from LeetCode.
 */
class Interval {
    int start, end;
    Interval() { this.start = 0; this.end = 0; }
    Interval(int s, int e) { this.start = s; this.end = e; }
    @Override
    public String toString() { return "[" + start + "," + end + "]"; }
}

public class Solution {
    public List<Interval> employeeFreeTime(List<List<Interval>> schedule) {
        List<Interval> all = new ArrayList<>();
        List<Interval> free = new ArrayList<>();

        if (schedule == null || schedule.isEmpty()) return free;

        // 1️⃣ Flatten all intervals
        for (List<Interval> emp : schedule) {
            all.addAll(emp);
        }

        // 2️⃣ Sort by start time
        Collections.sort(all, Comparator.comparingInt(a -> a.start));

        // 3️⃣ Merge while collecting gaps
        Interval prev = all.get(0);
        for (int i = 1; i < all.size(); i++) {
            Interval cur = all.get(i);
            if (cur.start > prev.end) {            // gap found
                free.add(new Interval(prev.end, cur.start));
                prev = cur;
            } else {                               // overlapping
                prev.end = Math.max(prev.end, cur.end);
            }
        }
        return free;
    }
}
```

---

## Python – Concise & Readable

```python
from typing import List

class Interval:
    def __init__(self, start: int, end: int):
        self.start = start
        self.end = end
    def __repr__(self):
        return f"[{self.start},{self.end}]"

class Solution:
    def employeeFreeTime(self, schedule: List[List[Interval]]) -> List[Interval]:
        # 1️⃣ Flatten
        intervals = [it for emp in schedule for it in emp]
        # 2️⃣ Sort
        intervals.sort(key=lambda x: x.start)

        free = []
        prev = intervals[0]
        for cur in intervals[1:]:
            if cur.start > prev.end:            # gap
                free.append(Interval(prev.end, cur.start))
                prev = cur
            else:                               # overlap
                prev.end = max(prev.end, cur.end)

        return free
```

---

## C++ – STL‑powered

```cpp
#include <vector>
#include <algorithm>

struct Interval {
    int start;
    int end;
    Interval() : start(0), end(0) {}
    Interval(int s, int e) : start(s), end(e) {}
};

class Solution {
public:
    std::vector<Interval> employeeFreeTime(std::vector<std::vector<Interval>>& schedule) {
        std::vector<Interval> all, free;
        if (schedule.empty()) return free;

        // 1️⃣ Flatten
        for (auto& emp : schedule)
            all.insert(all.end(), emp.begin(), emp.end());

        // 2️⃣ Sort
        std::sort(all.begin(), all.end(),
                  [](const Interval& a, const Interval& b){ return a.start < b.start; });

        // 3️⃣ Merge & collect gaps
        Interval prev = all[0];
        for (size_t i = 1; i < all.size(); ++i) {
            Interval cur = all[i];
            if (cur.start > prev.end) {           // free interval
                free.emplace_back(prev.end, cur.start);
                prev = cur;
            } else {                              // overlapping
                prev.end = std::max(prev.end, cur.end);
            }
        }
        return free;
    }
};
```

---

# 2. Blog Article – “Employee Free Time: The Good, The Bad, and The Ugly”

> **Title:** *Employee Free Time – The Good, The Bad, and The Ugly: A Job‑Interviewer‑Friendly Guide*  
> **Target Keywords:** `employee free time`, `leetcode 759`, `interval merge`, `job interview coding`, `algorithm interview`, `Python solution`, `Java solution`, `C++ solution`

---

## Introduction

Every software engineer has that one interview problem that feels like a *lot* more than it is.  
LeetCode 759 – *Employee Free Time* is one of those “look‑simple‑but‑has‑gotchas” problems.  

In this post, we’ll walk through:

1. **The Good** – why the problem is a great interview question.  
2. **The Bad** – the common pitfalls that trip up candidates.  
3. **The Ugly** – edge cases and subtle bugs that can derail an otherwise perfect solution.  

Along the way, we’ll present clean, idiomatic solutions in **Java, Python, and C++** – the three languages that most interviewers love to see.

> **Why does this matter?**  
> A strong grasp of interval problems demonstrates your ability to think about *overlap*, *boundary conditions*, and *greedy strategies*—skills that are invaluable in production code.

---

## The Good: Why 759 is a Gold‑Mine for Interviews

| Reason | What it Shows |
|--------|---------------|
| **Simplicity** | The statement is short, the data structure (intervals) is intuitive. |
| **Multiple Approaches** | Merge‑intervals, sweep‑line, priority queue. Candidates can pick one that fits their comfort. |
| **Real‑world Analogy** | Scheduling, booking systems, event planning – all familiar to developers. |
| **Edge‑case Rich** | Infinite bounds, zero‑length intervals, unsorted input. |
| **Scalable** | The solution naturally extends to `k` lists (k‑way merge). |

### Interviewer Takeaway

> *“You’re comfortable with a classic algorithmic pattern, and you’re able to adapt it to the constraints of the problem.”*

---

## The Bad: Common Missteps

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Assuming Input is Fully Sorted** | Only each employee’s list is sorted, not the combined list. | Flatten and sort *before* merging. |
| **Ignoring Zero‑length Intervals** | `[5,5]` should be discarded. | Skip intervals where `start == end` or check gap length. |
| **Using Incorrect Merge Condition** | Using `>=` instead of `>` leads to missing gaps. | Use `if cur.start > prev.end` for a *strict* gap. |
| **Not Handling Empty Schedules** | Null or empty input can throw `IndexOutOfBounds`. | Return empty list early. |
| **Mutable vs Immutable Interval Objects** | Updating `prev.end` in-place can corrupt the original data if reused elsewhere. | Create a new `Interval` for merged intervals. |

> **Key Insight:** *The devil is in the sorting step.* Many candidates skip it because they see “each employee’s schedule is sorted.”

---

## The Ugly: Edge Cases That Fool Even the Smartest

| Edge Case | What Happens | How to Handle |
|-----------|--------------|---------------|
| **Very Large Numbers** | `end` values up to `10^8` – no overflow in 32‑bit signed int. | Use `long` in languages that expose it (Java) if you want extra safety. |
| **All Employees Busy All Time** | No finite gaps – result should be empty. | After merging, if the resulting list is empty, return it. |
| **Single Employee** | Free time is all gaps between their own intervals. | Still works – algorithm reduces to a single list merge. |
| **Intervals Touching Exactly** | `[1,3]` and `[3,5]` – no free time. | Use `>` comparison, not `>=`. |
| **Intervals Out of Order Within an Employee** | Although the spec says sorted, test against a malformed input. | A robust solution could re‑sort each employee’s list first. |

---

## Algorithm Recap – Merge‑Intervals

1. **Flatten** all intervals into a single array.  
2. **Sort** by `start`.  
3. **Iterate** once, merging overlaps and recording gaps.  
4. Return the gaps.

### Complexity

| Time | Space |
|------|-------|
| `O(N log N)` – `N` = total intervals, due to sorting | `O(1)` extra (besides result) |

> **Why this matters:**  
> The interviewer is often more interested in your *thought process* than micro‑optimizations, but knowing the complexity gives you an edge.

---

## Code Show‑and‑Tell

| Language | Highlights |
|----------|------------|
| **Java** | Uses `Comparator.comparingInt`; mutates `prev` for brevity. |
| **Python** | List comprehension for flattening; lambda sort key. |
| **C++** | STL `sort` with lambda; `emplace_back` for efficiency. |

(See the code blocks above.)

---

## How to Nail the Interview

1. **Ask Clarifying Questions**  
   * Are intervals inclusive or exclusive?  
   * Should we ignore zero‑length intervals? (Always clarify.)

2. **Speak Your Thought Process**  
   * “I’ll flatten the lists first because each employee’s schedule is sorted individually.”  
   * “Then I’ll merge while collecting gaps.”  
   * “Finally, I’ll return the gaps.”  

3. **Write Clean Code**  
   * Avoid hidden bugs like `>=` vs `>`.  
   * Use descriptive variable names (`prev`, `cur`).  

4. **Test Edge Cases**  
   * A single employee.  
   * Completely overlapping schedules.  
   * Adjacent intervals.  
   * Empty input.  

5. **Explain Complexity**  
   * “Sorting dominates the runtime at `O(N log N)`.  
   * “Merging is linear.”  

6. **Mention Extensions**  
   * “If we had to support infinite bounds, we could treat them as `INT_MAX` and `INT_MIN` and ignore them.”  

---

## Final Takeaway

LeetCode 759 is *not* a trick question; it’s a *well‑balanced* interview problem that tests the basics of algorithm design, careful handling of boundaries, and code robustness.  

By mastering the **Good**, avoiding the **Bad**, and confronting the **Ugly**, you’ll walk away with a *confident, well‑structured solution*—one that showcases exactly why you’re the right candidate for a production role.

Good luck, and remember: *intervals are just time stamps waiting for your greedy intuition.*

--- 

> **Want to practice?**  
> Try writing the solution in your preferred language, then run it against the following test harness (available on GitHub).  

---

**Happy coding!** 🚀
---

> **Author:** *Alex “Algorithm” Johnson*  
> **Contact:** `alex.johnson@example.com`  
> **Subscribe** for more interview‑ready tutorials.  
> **Follow:** @AlgoMentor on Twitter.  

--- 

> **Disclaimer:** *The code snippets above are for educational purposes. Always adapt them to your own coding style.*  

--- 

*End of post.* 

--- 

**Meta‑Note (for recruiters / hiring managers):**  
When candidates can articulate the *Good, Bad, Ugly* story and provide clean solutions across major languages, they demonstrate the blend of *problem‑solving, communication, and production‑readiness* that every modern team needs. This article is an ideal addition to your interview prep toolkit.