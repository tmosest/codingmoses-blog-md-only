---
title: LeetCode 1936. Add Minimum Number of Rungs - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1936. Add Minimum Number of Rungs  
### The Good, the Bad, and the Ugly – A Job‑Ready LeetCode Deep‑Dive

> **Keywords:** Leetcode 1936, add minimum number of rungs, ladder problem, greedy algorithm, interview coding, Java solution, Python solution, C++ solution, O(n) algorithm, coding interview tips, data structures, algorithm design, get a job

---

### 1️⃣ Problem Recap

You are standing on the ground (`height = 0`).  
A ladder has a strictly increasing set of rungs `rungs[]`.  
You can only step to the next rung if the vertical distance between your current position and that rung is **≤ dist**.

You can add any number of new rungs at any positive integer height.  
**Goal:** Find the *minimum* number of rungs that must be inserted so that you can reach the topmost rung.

| Example | Input | Output | Explanation |
|---------|-------|--------|-------------|
| 1 | `[1,3,5,10]`, `dist = 2` | `2` | Add rungs at heights `7` and `8`. |
| 2 | `[3,6,8,10]`, `dist = 3` | `0` | Already climbable. |
| 3 | `[3,4,6,7]`, `dist = 2` | `1` | Add a rung at height `1`. |

---

### 2️⃣ Why It’s a **Greedy** Problem

You never need to look *ahead* to decide whether to add a rung.  
If the gap between two consecutive rungs is `gap`, the only thing that matters is how many extra steps of size `≤ dist` you need to traverse that gap.

- **Observation**: The optimal way to fill a gap is to insert the fewest possible rungs.  
  The best you can do is always place a rung exactly `dist` units above the previous one.

Thus the problem reduces to: *For every gap, count how many “extra steps” of length `dist` are required.*

---

### 3️⃣ The Simple O(n) Formula

For a gap `gap = next - current`:

```
extra_needed = (gap - 1) // dist
```

Why?  
- If `gap <= dist` → `gap - 1 < dist` → integer division yields `0`. No extra rung.  
- If `gap = dist + 1` → `(dist + 1 - 1) // dist = 1` → exactly one rung needed, etc.

You just sum this value over all gaps (including the gap from the ground to the first rung).

---

### 4️⃣ Implementation – Code in Three Languages

#### Java (LeetCode‑style)

```java
// 1936. Add Minimum Number of Rungs
// Time:  O(n)
// Space: O(1)
public class Solution {
    public int addRungs(int[] rungs, int dist) {
        int added = 0;   // answer accumulator
        int prev = 0;    // starting point is the ground

        for (int rung : rungs) {
            int diff = rung - prev;
            if (diff > dist) {
                added += (diff - 1) / dist; // integer division
            }
            prev = rung;
        }
        return added;
    }
}
```

#### Python 3

```python
# 1936. Add Minimum Number of Rungs
# Time:  O(n)
# Space: O(1)
from typing import List

class Solution:
    def addRungs(self, rungs: List[int], dist: int) -> int:
        added = 0
        prev = 0
        for rung in rungs:
            diff = rung - prev
            if diff > dist:
                added += (diff - 1) // dist
            prev = rung
        return added
```

#### C++

```cpp
// 1936. Add Minimum Number of Rungs
// Time:  O(n)
// Space: O(1)
int addRungs(vector<int>& rungs, int dist) {
    int added = 0;
    int prev = 0;
    for (int rung : rungs) {
        int diff = rung - prev;
        if (diff > dist) {
            added += (diff - 1) / dist;
        }
        prev = rung;
    }
    return added;
}
```

---

### 5️⃣ Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n)` | `O(n)` | `O(n)` |
| **Space** | `O(1)` | `O(1)` | `O(1)` |

`n` = number of existing rungs (≤ 10⁵).  
The algorithm only scans the array once and does a constant amount of work per element.

---

### 6️⃣ Common Pitfalls (The “Bad”)

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| **Adding rungs one by one** (simulate each step) | Leads to `O(n · dist)` worst‑case, impossible for `dist` up to 10⁹. | Use the division formula above. |
| **Off‑by‑one errors** | Using `gap / dist` instead of `(gap - 1) / dist` gives wrong counts when `gap` is a multiple of `dist`. | Always subtract 1 before division. |
| **Ignoring ground‑to‑first gap** | Failing to count the first rung’s distance from the floor. | Start `prev = 0`. |
| **Large integer overflow** | In languages with fixed integer size, `gap` could be up to 10⁹. | Use 64‑bit types if needed (e.g., `long` in Java, `int64_t` in C++). |

---

### 7️⃣ Alternatives & Variants (The “Ugly”)

1. **Sliding Window** – Overkill; the greedy approach already yields optimality.
2. **Recursive DP** – Unnecessary overhead; the optimal substructure is linear, not combinatorial.
3. **Binary Search on Answer** – Can be used for “minimum steps” problems but adds complexity.
4. **Simulate Adding Rungs** – Works only for small constraints; will TLE on LeetCode.

**Bottom line:** The division formula is *both* clean and optimal.  

---

### 8️⃣ Quick Test Harness (Python)

```python
if __name__ == "__main__":
    sol = Solution()
    print(sol.addRungs([1,3,5,10], 2))   # 2
    print(sol.addRungs([3,6,8,10], 3))   # 0
    print(sol.addRungs([3,4,6,7], 2))    # 1
```

---

### 9️⃣ Take‑away for Interviews

- **Read the statement carefully.** The “gap” is between *consecutive* positions, not just the last rung.
- **Greedy proof:** The optimal placement of new rungs is always `dist` units apart.  
  If you skip a potential spot, you’ll need at least as many rungs later.
- **Complexity matters.** LeetCode problems with `10⁵` inputs require linear solutions.
- **Show the math.** Derive `(gap - 1) // dist` explicitly; interviewers love seeing the reasoning.
- **Write clean code.** Add comments, use descriptive variable names (`prev`, `gap`, `added`).
- **Test edge cases.**  
  - Very small `dist` (1) → you may need many rungs.  
  - Very large `dist` → answer is 0.  
  - Large gaps (up to 10⁹) → ensure no overflow.

---

### 🔑 Final Thought

This problem is a textbook example of a greedy algorithm that is *straightforward*, *efficient*, and *idiomatic* across languages. Mastering it demonstrates that you can:

- Translate a real‑world constraint into a clean mathematical expression.  
- Avoid common pitfalls that lead to TLE or wrong answers.  
- Write production‑ready code that passes all edge cases.

These are exactly the skills recruiters look for when they ask you to solve “Add Minimum Number of Rungs” on a coding interview. Good luck landing that next job!