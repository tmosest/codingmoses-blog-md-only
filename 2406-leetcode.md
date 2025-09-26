---
title: LeetCode 2406. Divide Intervals Into Minimum Number of Groups - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 **Divide Intervals Into Minimum Number of Groups – 2406 (LeetCode)**

| Language | Time | Space |
| -------- | ---- | ----- |
| **Java** | `O(n log n)` | `O(n)` |
| **Python** | `O(n log n)` | `O(n)` |
| **C++** | `O(n log n)` | `O(n)` |

> **Problem**:  
> Given `intervals[i] = [lefti, righti]` (inclusive), divide all intervals into the fewest possible groups such that no two intervals in the same group overlap. Return the minimum number of groups.

---

## 🔍 Why This Problem is a *Job‑Interview Gold‑Mine*

1. **Core Concepts**: Sorting, two‑pointer technique, greedy interval scheduling, sweep line.
2. **Language Agnostic**: Solvable in O(n log n) in Java, Python, or C++.  
3. **Common Interview Twist**: The interval *endpoints are inclusive*, so `[1,5]` and `[5,8]` **do** overlap.
4. **Test‑Case Depth**: Large inputs (`n ≤ 10⁵`) – forces you to think about time & memory.

> *If you nail this question, recruiters will say “You understand greedy + sorting – perfect for systems design and backend interview!”*

---

## 📦 How the Code Works – “Good, Bad, & Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Approach** | Two‑pointer sweep line, O(n log n) | Forget the inclusive rule → wrong answer on boundary cases | Use a priority‑queue/heap to track end times – works but heavier (O(n log n) + extra memory) |
| **Complexity** | Fast and memory‑efficient | O(n²) if you compare every pair | O(n log n) but overkill for this problem |
| **Readability** | Clear separation of starts/ends | Mixed in‑place updates → hard to follow | Obfuscated lambda tricks | 

### ✅ Good: Two‑Pointer Sweep

1. **Sort** all start times and all end times.  
2. Walk through the starts.  
3. If the current start is *after* the earliest end (`start > end[endPtr]`), the interval can reuse a group → move `endPtr`.  
4. Otherwise, we need a new group → increment `groups`.  

This is essentially the same idea as the classic “Meeting Rooms II” problem, but simpler because we only need the *maximum concurrent intervals*.

### ❌ Bad: Heap/TreeSet Variant

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
for (int[] interval : intervals) {
    while (!pq.isEmpty() && pq.peek() < interval[0]) pq.poll();
    pq.offer(interval[1]);
}
return pq.size();
```

- Works, but O(n log n) *and* an extra heap → unnecessary overhead.

### 😱 Ugly: Over‑Engineered Sweep

- Sorting, then scanning with a counter that is incremented/decremented on the fly, but with messy boundary checks.
- Hard to debug and maintain.

---

## 📚 Full Code (Three Languages)

> All three implementations follow the same logic:  
> *Sort starts & ends → two‑pointer sweep → count max concurrent intervals.*

---

### Python 3

```python
from typing import List

class Solution:
    def minGroups(self, intervals: List[List[int]]) -> int:
        """
        Minimum number of groups to partition non‑overlapping intervals.

        Time   : O(n log n) – sorting
        Space  : O(n)       – auxiliary arrays
        """
        starts = sorted(i[0] for i in intervals)
        ends   = sorted(i[1] for i in intervals)

        end_ptr = 0          # points to the earliest ending interval
        groups  = 0

        for s in starts:
            # If the interval starts after the earliest end,
            # we can reuse that group (move end_ptr).
            if s > ends[end_ptr]:
                end_ptr += 1
            else:
                # Need a new group.
                groups += 1

        return groups
```

---

### Java (Java 17+)

```java
import java.util.Arrays;

class Solution {
    public int minGroups(int[][] intervals) {
        int n = intervals.length;
        int[] starts = new int[n];
        int[] ends   = new int[n];

        for (int i = 0; i < n; i++) {
            starts[i] = intervals[i][0];
            ends[i]   = intervals[i][1];
        }

        Arrays.sort(starts);
        Arrays.sort(ends);

        int endPtr = 0, groups = 0;
        for (int s : starts) {
            if (s > ends[endPtr]) {   // interval can reuse a group
                endPtr++;
            } else {                  // need a fresh group
                groups++;
            }
        }
        return groups;
    }
}
```

---

### C++ (C++17)

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int minGroups(std::vector<std::vector<int>>& intervals) {
        int n = intervals.size();
        std::vector<int> starts(n), ends(n);
        for (int i = 0; i < n; ++i) {
            starts[i] = intervals[i][0];
            ends[i]   = intervals[i][1];
        }
        std::sort(starts.begin(), starts.end());
        std::sort(ends.begin(), ends.end());

        int endPtr = 0, groups = 0;
        for (int s : starts) {
            if (s > ends[endPtr])  // reuse a group
                ++endPtr;
            else
                ++groups;          // new group needed
        }
        return groups;
    }
};
```

> **Tip**: For **C++**, prefer `vector<int>` over `vector<vector<int>>` when the input is read once.

---

## 📝 Blog‑Style Write‑Up (SEO Optimized)

---

# Divide Intervals Into Minimum Number of Groups (LeetCode 2406) – Java, Python, C++ Solutions

If you’re preparing for a coding interview, you’ll often encounter interval‑related questions. One of the trickiest is **“Divide Intervals Into Minimum Number of Groups”** (LeetCode 2406). In this post, we’ll break down the problem, walk through a clean O(n log n) solution, present working code in **Java, Python, and C++**, and discuss the *good*, *bad*, and *ugly* ways to tackle it. This article is packed with keywords that recruiters love: *minimum groups, greedy, sweep line, meeting rooms, inclusive intervals, interview prep, coding interview*, and more.

---

## 📌 Problem Statement

You’re given an array `intervals` where `intervals[i] = [left_i, right_i]` (inclusive).  

Divide the intervals into the **fewest groups** such that **no two intervals in the same group overlap**. Two intervals overlap if they share at least one point, **including the endpoints**. Return the minimum number of groups required.

> **Example**  
> Input: `[[5,10],[6,8],[1,5],[2,3],[1,10]]`  
> Output: `3`

---

## 🧠 Why This Question Matters

- **Greedy + Sorting**: Classic interview technique.  
- **Inclusive Endpoints**: Many candidates forget that `[1,5]` and `[5,8]` *do* overlap.  
- **Large Constraints**: `n ≤ 10⁵` forces you to design an `O(n log n)` solution.  
- **Language‑agnostic**: Works in Java, Python, and C++.

---

## 🛠️ Solution Overview – Two‑Pointer Sweep Line

1. **Separate** all start and end points into two arrays.  
2. **Sort** each array.  
3. **Walk through starts** while tracking the earliest end (`endPtr`).  
   - If the current start `s` is **after** `ends[endPtr]` (`s > ends[endPtr]`), we can reuse that group → move `endPtr`.  
   - Otherwise, we need a **new group** → increment `groups`.  
4. The final `groups` count equals the minimum number of groups.

> This is the same logic used for **Meeting Rooms II**, but the inclusive endpoint changes the comparison from `>=` to `>`.

### ✅ Good
- **Time**: `O(n log n)` (sorting dominates).  
- **Space**: `O(n)` (two auxiliary arrays).  
- **Clarity**: Two loops, a single pointer, straightforward.

### ❌ Bad
- **Heap / Priority‑Queue** variant – still `O(n log n)` but adds unnecessary heap overhead.

### 😱 Ugly
- Over‑engineered sweep with counters and in‑place updates that are hard to reason about.

---

## 📦 Code Snippets

> Each language implementation follows the same algorithmic steps.

### Python 3

```python
from typing import List

class Solution:
    def minGroups(self, intervals: List[List[int]]) -> int:
        starts = sorted(i[0] for i in intervals)
        ends   = sorted(i[1] for i in intervals)

        end_ptr = 0
        groups  = 0

        for s in starts:
            if s > ends[end_ptr]:
                end_ptr += 1
            else:
                groups += 1

        return groups
```

### Java (Java 17+)

```java
import java.util.Arrays;

class Solution {
    public int minGroups(int[][] intervals) {
        int n = intervals.length;
        int[] starts = new int[n];
        int[] ends   = new int[n];

        for (int i = 0; i < n; i++) {
            starts[i] = intervals[i][0];
            ends[i]   = intervals[i][1];
        }

        Arrays.sort(starts);
        Arrays.sort(ends);

        int endPtr = 0, groups = 0;
        for (int s : starts) {
            if (s > ends[endPtr]) endPtr++;
            else groups++;
        }
        return groups;
    }
}
```

### C++ (C++17)

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int minGroups(std::vector<std::vector<int>>& intervals) {
        int n = intervals.size();
        std::vector<int> starts(n), ends(n);
        for (int i = 0; i < n; ++i) {
            starts[i] = intervals[i][0];
            ends[i]   = intervals[i][1];
        }
        std::sort(starts.begin(), starts.end());
        std::sort(ends.begin(), ends.end());

        int endPtr = 0, groups = 0;
        for (int s : starts) {
            if (s > ends[endPtr]) ++endPtr;
            else ++groups;
        }
        return groups;
    }
};
```

---

## 📈 Complexity Analysis

| Language | Time | Space |
| -------- | ---- | ----- |
| Python   | `O(n log n)` | `O(n)` |
| Java     | `O(n log n)` | `O(n)` |
| C++      | `O(n log n)` | `O(n)` |

> The runtime is dominated by sorting. The sweep is linear (`O(n)`).

---

## 🎯 Interview Tips

1. **Clarify inclusive endpoints** before coding.  
2. **Explain the sweep** to the interviewer – show that you’re counting the maximum number of simultaneous intervals.  
3. **Test edge cases**:  
   - `[1,5], [5,10]` → 2 groups.  
   - `[1,2], [3,4]` → 1 group.  
4. **Mention** the relation to Meeting Rooms II, as it demonstrates your breadth of knowledge.

---

## 🔗 Further Reading

- **Meeting Rooms II** – LeetCode 253  
- **Non‑Overlapping Intervals** – LeetCode 435  
- **Greedy Algorithms** – *Cracking the Coding Interview*  
- **Sweep Line Algorithm** – *Algorithms (Cormen, Leiserson, Rivest, Stein)*  

---

## 🚀 Takeaway

The **Divide Intervals Into Minimum Number of Groups** problem is a gold‑mine for interviewers to test your mastery of greedy sorting, boundary handling, and algorithmic efficiency. The clean two‑pointer sweep line solution is efficient, easy to implement, and language‑agnostic. By sharing this post, you’re also showcasing the ability to write clean code in Java, Python, and C++—exactly what recruiters look for.

Happy coding, and good luck with your next interview!

--- 

## 💡 Final Note

> If you found this article helpful, share it on LinkedIn or Twitter and tag your recruiters with #codinginterview #leetcode2406 #minGroups.  

--- 

**Keywords used:** `Divide Intervals Into Minimum Number of Groups, LeetCode 2406, minimum groups, greedy algorithm, sweep line, meeting rooms II, inclusive endpoints, coding interview, Java solution, Python solution, C++ solution, interview prep, algorithmic complexity`.