---
title: LeetCode 1229. Meeting Scheduler - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1229. **Meeting Scheduler** – Three‑Language Implementation + SEO‑Optimized Blog

> **Problem** – Find the earliest overlapping time window of at least `duration` minutes that fits two people’s schedules.  
> **Constraints** – Up to 10⁴ slots per person, 32‑bit integer times, `duration ≤ 10⁶`.  
> **Goal** – Return `[start, start+duration]` of the first feasible slot or an empty array if none exist.

Below you’ll find **clean, production‑ready** solutions in **Java, Python, and C++** followed by a **blog article** that explains the algorithm, highlights pros/cons, and is SEO‑friendly for interview‑prep and recruiter visibility.

---

## 1.  Algorithm Overview

| Technique | Why it works |
|-----------|--------------|
| **Sort both slot lists by start time** | Guarantees non‑overlap within each person and lets us walk the two arrays linearly. |
| **Two‑pointer sweep** | `i` and `j` point to the current slot in each list.  
  *Compute intersection*: `start = max(a[i][0], b[j][0])`, `end = min(a[i][1], b[j][1])`.  
  If `end - start + 1 ≥ duration` (inclusive endpoints) → we’ve found the earliest slot.  
  Otherwise advance the pointer whose slot ends first (the “earlier‑finisher” cannot participate in any future overlap). |
| **Early‑exit** | As soon as a valid window is found, we return – the sweep guarantees that it is the earliest possible. |

**Complexities**

* **Time** – `O(n log n + m log m)` for sorting, `O(n + m)` for the sweep, total `O((n+m) log (n+m))`.  
  With `n, m ≤ 10⁴` this is trivial.  
* **Space** – `O(1)` auxiliary (apart from the output), sorting can be done in‑place.

---

## 2.  Code Implementations

> **Tip** – All three snippets use the same logic; copy‑paste into your IDE and run unit tests.

### 2.1 Java

```java
import java.util.*;

public class Solution {
    public List<Integer> minAvailableDuration(int[][] slots1, int[][] slots2, int duration) {
        // Sort by start time
        Arrays.sort(slots1, Comparator.comparingInt(a -> a[0]));
        Arrays.sort(slots2, Comparator.comparingInt(a -> a[0]));

        int i = 0, j = 0;
        while (i < slots1.length && j < slots2.length) {
            int start = Math.max(slots1[i][0], slots2[j][0]);
            int end   = Math.min(slots1[i][1], slots2[j][1]);

            // inclusive end, so need +1 if you treat it as a half‑open interval
            if (end - start + 1 >= duration) {
                return Arrays.asList(start, start + duration);
            }

            // advance the pointer whose interval ends first
            if (slots1[i][1] < slots2[j][1]) i++;
            else j++;
        }
        return Collections.emptyList();   // no possible slot
    }
}
```

### 2.2 Python

```python
from typing import List

class Solution:
    def minAvailableDuration(
        self,
        slots1: List[List[int]],
        slots2: List[List[int]],
        duration: int
    ) -> List[int]:
        # Sort by start time
        slots1.sort(key=lambda x: x[0])
        slots2.sort(key=lambda x: x[0])

        i = j = 0
        while i < len(slots1) and j < len(slots2):
            start = max(slots1[i][0], slots2[j][0])
            end   = min(slots1[i][1], slots2[j][1])

            if end - start + 1 >= duration:          # inclusive bounds
                return [start, start + duration]

            # advance the slot that finishes earlier
            if slots1[i][1] < slots2[j][1]:
                i += 1
            else:
                j += 1

        return []   # no slot found
```

### 2.3 C++

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    std::vector<int> minAvailableDuration(std::vector<std::vector<int>>& slots1,
                                          std::vector<std::vector<int>>& slots2,
                                          int duration) {
        auto cmp = [](const std::vector<int>& a, const std::vector<int>& b) {
            return a[0] < b[0];
        };
        std::sort(slots1.begin(), slots1.end(), cmp);
        std::sort(slots2.begin(), slots2.end(), cmp);

        size_t i = 0, j = 0;
        while (i < slots1.size() && j < slots2.size()) {
            int start = std::max(slots1[i][0], slots2[j][0]);
            int end   = std::min(slots1[i][1], slots2[j][1]);

            if (end - start + 1 >= duration) {   // inclusive
                return {start, start + duration};
            }

            // advance the pointer whose interval ends first
            if (slots1[i][1] < slots2[j][1])
                ++i;
            else
                ++j;
        }
        return {};   // empty vector if no slot found
    }
};
```

> **Note** – All three codes assume the input slots are **inclusive** (`[start, end]`). If the problem statement uses half‑open intervals, remove the `+1` in the checks.

---

## 3.  SEO‑Optimized Blog Article

### Title  
**“Meeting Scheduler LeetCode 1229: Two‑Pointer Masterclass (Java | Python | C++) – The Good, The Bad, The Ugly”**

### Meta Description  
Master LeetCode #1229 “Meeting Scheduler” with clean two‑pointer solutions in Java, Python, and C++. Learn the trade‑offs, common pitfalls, and how to ace the interview.  

---

### Introduction  

During a recent data‑structures & algorithms interview, the interviewer tossed a classic scheduling problem: *“Find the earliest overlapping time slot of a given duration for two people.”* It’s LeetCode 1229 – *Meeting Scheduler*. Despite its simple wording, the key lies in recognizing that both schedules are already **sorted and non‑overlapping** per person. This opens the door to a **two‑pointer sweep** that runs in linear time.

In this post, I’ll walk through the **good**, **bad**, and **ugly** of this problem, provide production‑grade code in **Java, Python, and C++**, and give you interview‑ready talking points.

---

## 4.  The Good – Why the Two‑Pointer Approach Wins

| Feature | Explanation |
|---------|-------------|
| **Linear scan after sorting** | Once each list is sorted by start time, we only need to traverse them once. This is `O(n+m)` after `O(n log n + m log m)` sorting. |
| **Early termination** | The first qualifying intersection is the earliest by definition. We can return immediately, saving time on the rest of the lists. |
| **Memory efficiency** | No extra data structures are required beyond the input arrays and a couple of indices. |
| **Robustness** | Works with any valid input size (`<= 10⁴`) and any time values (`0 ≤ time ≤ 10⁹`). |

> **Pro tip:** When you’re in an interview, start by stating the plan – “Sort both arrays, then use two pointers.” It demonstrates algorithmic clarity and saves the interviewer time.

---

## 5.  The Bad – Common Pitfalls and Misunderstandings

| Pitfall | Fix |
|---------|-----|
| **Treating intervals as half‑open** | The problem defines `[start, end]` as inclusive. Forgetting the `+1` in `end - start + 1` can produce wrong results, especially when `duration` equals the length of the intersection. |
| **Skipping the sorting step** | Some candidates assume the input is already sorted. Even though the problem guarantees non‑overlap per person, the start times may not be ordered. |
| **Using `>=` instead of `+1`** | When checking feasibility, remember that the meeting can start at `start` and end at `start + duration`. So we need `start + duration <= end`. |
| **Over‑engineering with priority queues** | A heap or tree map can solve the problem, but it adds unnecessary overhead. The two‑pointer method is simpler and faster. |

> **Interview hint:** Clarify assumptions about inclusivity. If in doubt, ask the interviewer how intervals are defined.

---

## 6.  The Ugly – Edge Cases that Trip Up the Code

| Edge Case | Why It’s Tricky | Fix |
|-----------|----------------|-----|
| **Exact boundary match** | e.g., slots `[[10,20]]` and `[[15,25]]` with `duration = 6`. The intersection is `[15,20]` (length 6). Many solutions mistakenly require `end - start >= duration`. | Use `if (end - start + 1 >= duration)` or equivalently `if (start + duration <= end)`. |
| **Multiple overlapping windows** | The earliest may be hidden behind a later slot if the pointers are not advanced correctly. Advancing the *earlier‑finisher* is essential. |
| **Empty input lists** | One or both lists can be empty. The algorithm should gracefully return an empty list. |
| **Large duration** | If `duration` is larger than any possible intersection, the algorithm must keep scanning until the end. No shortcut exists. |
| **Duplicate start times** | Even if schedules are non‑overlapping, two slots can share the same start time. The logic still holds; just ensure the `if-else` advancement picks the correct pointer. |

> **Testing checklist:**  
> 1. `[[0,10]]` & `[[5,15]]` with `duration=6` → `[5,11]`.  
> 2. `[[0,5]]` & `[[6,10]]` with any duration → empty.  
> 3. `[[0,10]]` & `[[10,20]]` with `duration=1` → `[10,11]` (inclusive).  

---

## 7.  Interview‑Ready Talking Points

1. **Explain the invariants** – each person’s slots are disjoint, so once an interval is passed, it can never be part of a future intersection.  
2. **Justify sorting** – “We need the start times in order to know which pair to examine first.”  
3. **Detail pointer advancement** – “We move the pointer whose current interval ends earlier; that interval cannot overlap any later interval from the other list.”  
4. **Show the return condition** – “If `start + duration <= end`, we’re done.”  
5. **Complexity claim** – “`O((n+m) log (n+m))` time, `O(1)` space.”  
6. **Ask clarifying questions** – e.g., “Are the start/end times inclusive?” This prevents hidden mistakes.

---

## 7.  Wrap‑Up – Why This Matters for Your Resume

* **Demonstrates mastery of classic “two‑pointer” technique** – a staple for many interview problems.  
* **Highlights language flexibility** – having clean solutions in **Java, Python, and C++** shows you can work with the recruiter’s preferred tech stack.  
* **Shows thoughtful debugging** – recognizing inclusive intervals and handling edge cases is a mark of a seasoned coder.  

If you’re preparing for a data‑science, backend, or systems role, LeetCode 1229 is a perfect example to showcase **algorithmic thinking, clean code, and interview poise**.  

---

## 7.  Final Thought

Keep the algorithm simple, test against edge cases, and explain your reasoning step by step. A concise two‑pointer solution not only solves *Meeting Scheduler* in milliseconds but also impresses recruiters with clear, efficient thinking.  

Happy coding, and may your interviews always end with a “YES” instead of “Sorry, we couldn’t find a slot”!

---

### 8.  Suggested Keywords (for SEO)

* Meeting Scheduler LeetCode 1229  
* Two pointers algorithm  
* Java interview coding  
* Python coding interview  
* C++ algorithm interview  
* Interview preparation LeetCode  
* Scheduling problem interview  
* Time‑complexity analysis  
* Production‑ready interview code  
* Algorithmic design interview

Feel free to use or adapt this article in your LinkedIn posts, portfolio blogs, or as a reference during mock interviews. Good luck!