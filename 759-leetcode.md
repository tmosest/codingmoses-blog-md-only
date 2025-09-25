---
title: LeetCode 759. Employee Free Time - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 759. **Employee Free Time** – Solutions in **Java**, **Python**, and **C++**  
> *Get interview‑ready with a clean, production‑grade implementation for LeetCode 759.*

---

## 1. Problem Recap

You are given a list of schedules – one list per employee.  
Each employee’s schedule is a **sorted list of non‑overlapping intervals** `[start, end)`.

```
schedule[i] = [[1,2],[5,6]]
```

Return **all finite intervals** that are free for **every** employee (i.e., the intersection of all employees’ “gaps”).  
Intervals of zero length are excluded.

> **Constraints**  
> - 1 ≤ `schedule.length`, `schedule[i].length` ≤ 50  
> - 0 ≤ `schedule[i].start` < `schedule[i].end` ≤ 10⁸

---

## 2. High‑Level Idea

1. **Flatten** the 2‑D schedule into a single list of intervals.
2. **Sort** the flattened list by `start`.
3. **Merge** overlapping/adjacent intervals.
4. **Collect** the gaps between the merged intervals → free time for everyone.

This is a classic “merge‑intervals” problem with an extra step to extract gaps.

**Complexity**  
- `O(N log N)` time for sorting (`N` = total number of intervals).  
- `O(N)` extra space to hold the flattened list and the result.

---

## 3. Code Implementations

Below are idiomatic, production‑ready solutions in the three most popular interview languages.

> **Tip** – All solutions share the same core logic; adapt the data structure (`Interval` class/struct, `Tuple`, etc.) to fit your language.

---

### 3.1 Java (OOP + Collections)

```java
import java.util.*;

class Interval {
    int start;
    int end;
    Interval(int s, int e) { start = s; end = e; }
}

public class Solution {
    public List<Interval> employeeFreeTime(List<List<Interval>> schedule) {
        List<Interval> all = new ArrayList<>();
        for (List<Interval> emp : schedule) all.addAll(emp);

        // Sort by start time
        all.sort(Comparator.comparingInt(a -> a.start));

        List<Interval> free = new ArrayList<>();
        if (all.isEmpty()) return free;

        Interval prev = all.get(0);
        for (int i = 1; i < all.size(); i++) {
            Interval cur = all.get(i);
            if (cur.start > prev.end) {           // a gap
                free.add(new Interval(prev.end, cur.start));
                prev = cur;
            } else {                              // overlap – merge
                prev.end = Math.max(prev.end, cur.end);
            }
        }
        return free;
    }

    // ---- Demo ----
    public static void main(String[] args) {
        List<List<Interval>> schedule = new ArrayList<>();
        schedule.add(Arrays.asList(new Interval(1,2), new Interval(5,6)));
        schedule.add(Arrays.asList(new Interval(1,3)));
        schedule.add(Arrays.asList(new Interval(4,10)));

        System.out.println(new Solution().employeeFreeTime(schedule)
                .stream()
                .map(iv -> "[" + iv.start + "," + iv.end + "]")
                .toList());
        // → [[3,4]]
    }
}
```

---

### 3.2 Python (Dataclass + Sorting)

```python
from dataclasses import dataclass
from typing import List

@dataclass(order=True)
class Interval:
    start: int
    end: int

def employee_free_time(schedule: List[List[Interval]]) -> List[Interval]:
    # Flatten
    all_ints: List[Interval] = [iv for emp in schedule for iv in emp]
    # Sort by start (dataclass ordering works)
    all_ints.sort(key=lambda iv: iv.start)

    free: List[Interval] = []
    if not all_ints:
        return free

    prev = all_ints[0]
    for cur in all_ints[1:]:
        if cur.start > prev.end:          # gap
            free.append(Interval(prev.end, cur.start))
            prev = cur
        else:                             # merge
            prev.end = max(prev.end, cur.end)

    return free

# ---- Demo ----
if __name__ == "__main__":
    schedule = [
        [Interval(1,2), Interval(5,6)],
        [Interval(1,3)],
        [Interval(4,10)]
    ]
    print([f"[{iv.start},{iv.end}]" for iv in employee_free_time(schedule)])
    # → ['[3,4]']
```

---

### 3.3 C++ (Struct + STL)

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Interval {
    int start, end;
    Interval(int s=0, int e=0) : start(s), end(e) {}
};

vector<Interval> employeeFreeTime(const vector<vector<Interval>>& schedule) {
    vector<Interval> all;
    for (const auto& emp : schedule)
        all.insert(all.end(), emp.begin(), emp.end());

    sort(all.begin(), all.end(), [](const Interval& a, const Interval& b) {
        return a.start < b.start;
    });

    vector<Interval> free;
    if (all.empty()) return free;

    Interval prev = all[0];
    for (size_t i = 1; i < all.size(); ++i) {
        const Interval& cur = all[i];
        if (cur.start > prev.end) {                // gap
            free.emplace_back(prev.end, cur.start);
            prev = cur;
        } else {                                   // merge
            prev.end = max(prev.end, cur.end);
        }
    }
    return free;
}

// ---- Demo ----
int main() {
    vector<vector<Interval>> schedule = {
        {{1,2},{5,6}},
        {{1,3}},
        {{4,10}}
    };

    auto res = employeeFreeTime(schedule);
    for (auto& iv : res)
        cout << "[" << iv.start << "," << iv.end << "] ";
    // → [3,4]
}
```

---

## 4. Blog Article – “The Good, the Bad, and the Ugly” of LeetCode 759

> **Title:**  
> **Employee Free Time – The Good, the Bad, and the Ugly | Java / Python / C++ Solutions**

> **Meta Description:**  
> Master LeetCode 759 with clean, production‑grade solutions in Java, Python, and C++. Discover the pitfalls, optimal strategies, and interview‑ready insights that will help you ace your coding interview.

> **Keywords:**  
> *Employee Free Time, LeetCode 759, Java solution, Python solution, C++ solution, interview algorithm, merge intervals, sweep line, coding interview tips, job interview coding, software engineering interview*

---

### Introduction

The “Employee Free Time” problem is a favorite on LeetCode because it blends two classic concepts:

1. **Interval merging** – a must‑know for many coding interviews.
2. **Multi‑source scheduling** – the “real world” twist that keeps interviewers engaged.

While the problem seems simple at first glance, subtle edge cases and implementation pitfalls can trip up even seasoned programmers. In this post we’ll dissect the **good**, **bad**, and **ugly** of solving LeetCode 759, and present polished solutions in **Java**, **Python**, and **C++**.

---

### The Good – Why This Problem Rocks

| Benefit | Why it Matters |
|---------|----------------|
| **Real‑world relevance** | Scheduling and resource allocation are core to many applications (meeting rooms, CPU scheduling, cloud services). |
| **Broad applicability** | The merge‑intervals pattern appears in problems like “Meeting Rooms II”, “Insert Interval”, etc. |
| **Scalable solution** | With a clean algorithm you can handle up to 50 × 50 intervals in milliseconds – perfect for interview speed. |
| **Showcases OOP & Collections** | Demonstrates mastery over data structures (`List`, `PriorityQueue`) and sorting tricks. |
| **Cross‑language familiarity** | Solving it in Java, Python, and C++ shows you’re comfortable in multiple stacks – a huge plus in interviews. |

---

### The Bad – Common Pitfalls

| Pitfall | Explanation | Fix |
|---------|-------------|-----|
| **Overlooking the “inf” boundaries** | Some solutions mistakenly include `[-inf, first_start)` or `(last_end, inf)` as free intervals. | Discard any interval containing `-inf` or `inf`. |
| **Assuming one employee only** | If you treat each employee separately, you’ll miss gaps that exist only after merging all schedules. | Flatten all intervals first, then merge. |
| **Using wrong comparison** | `if (cur.start <= prev.end)` vs `if (cur.start > prev.end)` – subtle but crucial for adjacent intervals. | Treat adjacent intervals (`cur.start == prev.end`) as overlapping (no free time). |
| **Modifying input lists** | In-place updates can corrupt the original schedule. | Work on a copy or build a new result list. |
| **Time‑complexity confusion** | Sorting dominates; any nested loops that run on `N²` can kill the solution. | Keep the algorithm `O(N log N)` by sorting once. |

---

### The Ugly – Hidden Tricks and Edge Cases

| Edge Case | Why it’s Ugly | Insight |
|-----------|---------------|---------|
| **Zero‑length intervals** | `[3,3)` is technically free but should be omitted. | Add a guard: `if (prev.end < cur.start)` when collecting gaps. |
| **Identical intervals across employees** | Multiple employees can have exactly the same interval. | Still merge – duplicates vanish. |
| **Large integer ranges** | The bounds go up to 10⁸; be careful with integer overflows in languages with 32‑bit `int`. | Use `long` in Java or `int64_t` in C++ if the language’s default int might overflow. |
| **Stability of sorting** | When two intervals share the same `start`, the relative order of `end` values matters for correctness. | Sort by `start`, and if equal, by `end` (ascending). |
| **Empty schedule** | If the input is empty, you should return an empty list, not a “null” interval. | Check `if (all.empty())` early. |

---

### Interview‑Ready Talk‑Points

During an interview, don’t just present the code – explain **why** you flattened, why you sorted, and how you handle boundaries. Show that you:

- Recognize that this is an *intersection of all gaps* problem.
- Are comfortable using a single pass after sorting.
- Understand the difference between *gaps* (free time) and *merged intervals* (busy time).

> **Pro‑tip:** If the interviewer asks for the *optimal* complexity, you can always mention that you’re using a *sort‑then‑merge* pattern that is `O(N log N)`.

---

### Summary

- **Good**: Real‑world insight, scalable OOP pattern, cross‑language exposure.
- **Bad**: Forgetting `inf` bounds, mishandling multi‑employee merges, subtle comparison bugs.
- **Ugly**: Edge cases (zero‑length, adjacent intervals) that require defensive programming.

By mastering these nuances and practicing the clean solutions above, you’ll:

- Deliver a bug‑free, efficient answer in any language.  
- Communicate your thought process clearly to interviewers.  
- Stand out as a *full‑stack* algorithmic wizard.

---

## 5. Final Checklist – Ready for the Next Interview

1. **Understand the core merge‑intervals logic** – this will translate to almost any interval problem.
2. **Practice the same pattern in Java, Python, and C++** – feel the idiomatic differences.
3. **Explain your code** – highlight boundary handling, complexity, and data‑structure choices.
4. **Edge‑case tests** – run on empty schedules, all employees with one interval, adjacent intervals, etc.
5. **Optimize for speed** – keep a single `O(N log N)` pass; no nested `N²` loops.

With this knowledge, LeetCode 759 becomes less a puzzle and more a showcase of algorithmic maturity.

Good luck, and may your next coding interview be *fast*, *bug‑free*, and *interview‑winning*! 🚀

--- 

*Want more interview‑ready blogs? Follow us on LinkedIn & Twitter for the latest algorithm insights.*