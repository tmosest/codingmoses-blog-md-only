---
title: LeetCode 2054. Two Best Non-Overlapping Events - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 📈 How to Nail LeetCode “Two Best Non‑Overlapping Events” (2054) – A Job‑Ready Guide  

> **Want to ace your next coding interview?**  
> Mastering this medium‑level problem shows you can sort, binary‑search, and optimize in linearithmic time – all skills hiring managers love.

---

## 1️⃣ Problem Recap (LeetCode 2054)

You’re given a list of events, each defined by **start time, end time,** and **value**:

```text
events[i] = [start_i, end_i, value_i]
```

* **Events are inclusive.**  
  If one ends at time `t`, the next must start at `t + 1` or later.  
* You can attend **at most two** non‑overlapping events.  
* Return the **maximum total value** achievable.

**Constraints**

| Field | Range |
|-------|-------|
| `events.length` | 2 … 10⁵ |
| `start_i, end_i` | 1 … 10⁹ |
| `value_i` | 1 … 10⁶ |

---

## 2️⃣ Why This Problem Rocks Your Interview Portfolio

| Skill | How the Problem Tests It |
|-------|--------------------------|
| Sorting & Greedy | You must sort by start time to reason about future events. |
| Binary Search | You need to quickly find the next compatible event. |
| Prefix/Suffix Arrays | You store the best future value for fast look‑ups. |
| Time/Space Complexity | Achieve `O(n log n)` time and `O(n)` space. |
| Edge‑Case Thinking | Inclusive times, single‑event optimality, large input sizes. |

---

## 3️⃣ High‑Level Solution Overview

1. **Sort** all events by `start` time.  
2. Build a **suffix maximum array**:  
   `maxVal[i] = max(value of any event from i to end)`.  
   This gives us the best future event value in *O(1)*.  
3. For each event `i`:  
   * Use **binary search** to locate the first event `j` whose start > `end_i`.  
   * Compute candidate sums:  
     * `value_i` (single event)  
     * `value_i + maxVal[j]` (pair)  
   * Keep the global maximum.  

The two‑event pair always consists of the current event and the *best* possible future event that starts after it ends.

---

## 4️⃣ Detailed Algorithm

```text
sort(events by start)

suffixMax[n-1] = events[n-1].value
for i = n-2 downto 0:
    suffixMax[i] = max(events[i].value, suffixMax[i+1])

answer = 0
for i = 0 … n-1:
    # Binary search for first event that starts after events[i].end
    l = i+1, r = n-1, idx = -1
    while l <= r:
        mid = (l+r)//2
        if events[mid].start > events[i].end:
            idx = mid
            r = mid-1
        else:
            l = mid+1

    # Only current event
    answer = max(answer, events[i].value)

    # Pair with the best future event
    if idx != -1:
        answer = max(answer, events[i].value + suffixMax[idx])

return answer
```

**Why it works**

* Sorting guarantees that any future event starts at or after the current event’s start.  
* Binary search guarantees logarithmic lookup of the *next non‑overlapping* event.  
* The suffix array stores the maximum value of *any* event starting at or after index `j`, which is exactly what we need for pairing.

---

## 5️⃣ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Sorting | `O(n log n)` | `O(1)` (in‑place) |
| Building suffix array | `O(n)` | `O(n)` |
| Loop + Binary search | `O(n log n)` | `O(1)` |
| **Total** | **`O(n log n)`** | **`O(n)`** |

Both time and space satisfy the problem constraints comfortably.

---

## 6️⃣ Edge Cases & Gotchas

| Edge Case | Why it matters | Fix |
|-----------|----------------|-----|
| Two events overlap exactly (`start2 == end1`) | Inclusive times mean they cannot both be chosen | Binary search condition `start > end` |
| All events overlap | Best choice is a single event | Code already handles with `max(answer, value)` |
| Very large input (`10⁵` events) | Must avoid O(n²) solutions | Use binary search + suffix array |

---

## 7️⃣ Code Implementations

Below are clean, idiomatic solutions in **Java**, **Python**, and **C++**.  
All compile with the latest standards and run in `O(n log n)` time.

> ⚡️ Tip: When posting on LeetCode, paste the class definition only – the platform supplies the driver.

---

### 📌 Java

```java
import java.util.Arrays;

class Solution {
    public int maxTwoEvents(int[][] events) {
        // Sort by start time
        Arrays.sort(events, (a, b) -> Integer.compare(a[0], b[0]));

        int n = events.length;
        int[] suffixMax = new int[n];
        suffixMax[n - 1] = events[n - 1][2];

        // Build suffix maximum array
        for (int i = n - 2; i >= 0; i--) {
            suffixMax[i] = Math.max(events[i][2], suffixMax[i + 1]);
        }

        int best = 0;

        // For each event, binary search next compatible event
        for (int i = 0; i < n; i++) {
            // Single event value
            best = Math.max(best, events[i][2]);

            int l = i + 1, r = n - 1, idx = -1;
            while (l <= r) {
                int mid = l + (r - l) / 2;
                if (events[mid][0] > events[i][1]) {
                    idx = mid;
                    r = mid - 1;
                } else {
                    l = mid + 1;
                }
            }

            if (idx != -1) {
                best = Math.max(best, events[i][2] + suffixMax[idx]);
            }
        }

        return best;
    }
}
```

---

### 📌 Python 3

```python
class Solution:
    def maxTwoEvents(self, events: List[List[int]]) -> int:
        # 1. Sort by start time
        events.sort(key=lambda e: e[0])

        n = len(events)
        suffix_max = [0] * n
        suffix_max[-1] = events[-1][2]

        # 2. Build suffix max
        for i in range(n - 2, -1, -1):
            suffix_max[i] = max(events[i][2], suffix_max[i + 1])

        best = 0

        for i in range(n):
            # Single event
            best = max(best, events[i][2])

            # Binary search for first event with start > end_i
            l, r, idx = i + 1, n - 1, -1
            while l <= r:
                mid = (l + r) // 2
                if events[mid][0] > events[i][1]:
                    idx = mid
                    r = mid - 1
                else:
                    l = mid + 1

            if idx != -1:
                best = max(best, events[i][2] + suffix_max[idx])

        return best
```

---

### 📌 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxTwoEvents(vector<vector<int>>& events) {
        // Sort by start time
        sort(events.begin(), events.end(),
             [](const vector<int>& a, const vector<int>& b) {
                 return a[0] < b[0];
             });

        int n = events.size();
        vector<int> suffixMax(n);
        suffixMax[n - 1] = events[n - 1][2];

        // Suffix maximum
        for (int i = n - 2; i >= 0; --i)
            suffixMax[i] = max(events[i][2], suffixMax[i + 1]);

        int best = 0;

        for (int i = 0; i < n; ++i) {
            best = max(best, events[i][2]);

            int l = i + 1, r = n - 1, idx = -1;
            while (l <= r) {
                int mid = l + (r - l) / 2;
                if (events[mid][0] > events[i][1]) {
                    idx = mid;
                    r = mid - 1;
                } else {
                    l = mid + 1;
                }
            }

            if (idx != -1)
                best = max(best, events[i][2] + suffixMax[idx]);
        }

        return best;
    }
};
```

---

## 8️⃣ Quick Test Cases

| Test | Input | Expected |
|------|-------|----------|
| 1 | `[[1,3,4],[2,5,2],[7,9,4]]` | `8` (events 1 & 3) |
| 2 | `[[1,3,3],[4,5,5],[6,7,7]]` | `12` (events 2 & 3) |
| 3 | `[[1,3,10],[2,4,5],[5,6,5]]` | `15` (event 1 + event 3) |
| 4 | `[[1,10,10],[2,3,2],[4,5,5],[6,7,8]]` | `18` (event 1 + event 4) |

---

## 9️⃣ How to Present This Solution in an Interview

1. **Explain the sorting rationale** – “We sort to make future decisions easier.”  
2. **Show the suffix max idea** – “We want the best possible event after the current one.”  
3. **Demonstrate binary search** – “We need O(log n) look‑up to avoid the quadratic pitfall.”  
4. **State the complexity** – “O(n log n) time, O(n) space – optimal for 10⁵ inputs.”  
5. **Mention corner‑case handling** – “Inclusive times, single‑event optimality.”  

Feel free to tweak the code on the fly: replace binary search with a *two‑pointer* sweep for an `O(n)` solution when all events are sorted by end time first.

---

## 10️⃣ Takeaways for Your Next Coding Interview

| ✅ Skill | What you proved |
|----------|-----------------|
| **Data‑Structure Choice** | Sorted arrays, suffix arrays, binary search. |
| **Complexity Management** | Balanced `O(n log n)` solution. |
| **Problem Decomposition** | Single event vs. paired event analysis. |
| **Robustness** | Inclusive times & large inputs. |
| **Coding Style** | Clean, testable code in three languages. |

*Show the recruiter that you can write clean, production‑ready code across languages—exactly what they look for in a senior software engineer.*

---

## 11️⃣ Final Checklist Before Your Next Interview

- ✅ **Understand the inclusive time constraint.**  
- ✅ **Implement the suffix max array** to avoid repeated scans.  
- ✅ **Binary‑search** for the first event whose start > current end.  
- ✅ **Return the max of single‑event and pair sums.**  
- ✅ Test with edge cases (overlap, single best event, huge N).  

---

## 🔍 SEO‑Ready Keywords

- LeetCode 2054  
- Two Best Non‑Overlapping Events  
- Binary Search interview problem  
- Sorting algorithm interview question  
- Job interview algorithm  
- Time complexity O(n log n)  
- Job interview prep LeetCode  

---

### 🎯 Final Thought

Solving “Two Best Non‑Overlapping Events” isn’t just a technical win—it’s a **story** you can tell recruiters: *“I sorted, I binary‑searched, I stored suffix maximums, and I achieved the optimal result in log‑linear time.”*  
Add this to your portfolio, brag on your résumé, and you’ll be ready for that next interview call. Good luck! 🚀