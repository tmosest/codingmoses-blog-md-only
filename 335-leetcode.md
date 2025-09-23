---
title: LeetCode 335. Self Crossing - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 335. Self‑Crossing – Multi‑Language Solutions & SEO‑Optimized Blog

---

### 1.  Problem Recap  
> **Self‑Crossing**  
> You start at **(0, 0)**.  
> Move `distance[0]` north, `distance[1]` west, `distance[2]` south, `distance[3]` east, … (counter‑clockwise).  
> Return **`true`** if the path ever crosses itself, otherwise **`false`**.

**Constraints**

| Constraint | Value |
|------------|-------|
| `1 ≤ distance.length ≤ 10⁵` | |
| `1 ≤ distance[i] ≤ 10⁵` | |

---

### 2.  Why This Problem Rocks for Interviews  
- **Geometry + Arrays** – tests the ability to reason about 2‑D motion without heavy math.  
- **Edge‑Case Awareness** – the path can cross in subtle ways that simple O(n²) checks miss.  
- **Optimality Mindset** – requires O(n) time and O(1) space, a classic interview challenge.  

If you nail this, you’ll impress hiring managers on tech stacks that value **clean code, efficient algorithms, and edge‑case robustness**.

---

### 3.  High‑Level Insight  
A line segment can only intersect the line 3 or 4 segments behind it:

| Index `i` | Check Type | Reason |
|-----------|------------|--------|
| `i ≥ 3`   | **Case 1** – `i` vs `i‑3` | New line can only hit the line that’s 3 steps back. |
| `i ≥ 4`   | **Case 2** – `i` vs `i‑4` | In a “tight spiral”, the new line can touch the line 4 steps back. |

If any of these conditions hold, we return `true`.

The full set of conditions (derived from the official editorial) is:

```
if (i >= 3) {                 // Case 1
    // 1) Overlap with i-3
    if (distance[i] >= distance[i-2] &&
        distance[i-1] <= distance[i-3]) return true;

    if (i >= 4) {             // Case 2
        // 2) Touching i-4
        if (distance[i-1] == distance[i-3] &&
            distance[i] + distance[i-4] >= distance[i-2]) return true;

        // 3) Tight spiral (i-5)
        if (i >= 5 &&
            distance[i-2] >= distance[i-4] &&
            distance[i] + distance[i-4] >= distance[i-2] &&
            distance[i-1] == distance[i-3] &&
            distance[i-3] >= distance[i-5]) return true;
    }
}
```

All other cases are safe.

---

### 4.  Code Implementations  

> **All three solutions share the same O(n) time & O(1) space logic.**  
> They differ only in syntax, not in strategy.

---

#### 4.1 Java (LeetCode‑Style)

```java
class Solution {
    public boolean isSelfCrossing(int[] distance) {
        int n = distance.length;
        for (int i = 3; i < n; i++) {
            // Case 1: current line touches the line 3 steps back
            if (distance[i] >= distance[i - 2] &&
                distance[i - 1] <= distance[i - 3]) {
                return true;
            }

            // Case 2: special cases for a tight spiral
            if (i >= 4) {
                // 2a) current line touches the line 4 steps back
                if (distance[i - 1] == distance[i - 3] &&
                    distance[i] + distance[i - 4] >= distance[i - 2]) {
                    return true;
                }

                // 2b) tighter spiral involving line 5 steps back
                if (i >= 5 &&
                    distance[i - 2] >= distance[i - 4] &&
                    distance[i] + distance[i - 4] >= distance[i - 2] &&
                    distance[i - 1] == distance[i - 3] &&
                    distance[i - 3] >= distance[i - 5]) {
                    return true;
                }
            }
        }
        return false;
    }
}
```

*Comments* are sprinkled to aid readability and maintenance.

---

#### 4.2 Python 3

```python
class Solution:
    def isSelfCrossing(self, distance: list[int]) -> bool:
        n = len(distance)
        for i in range(3, n):
            # Case 1
            if distance[i] >= distance[i - 2] and distance[i - 1] <= distance[i - 3]:
                return True

            # Case 2 – spiral cases
            if i >= 4:
                # 2a
                if distance[i - 1] == distance[i - 3] and distance[i] + distance[i - 4] >= distance[i - 2]:
                    return True

                # 2b – tighter spiral
                if (i >= 5 and
                    distance[i - 2] >= distance[i - 4] and
                    distance[i] + distance[i - 4] >= distance[i - 2] and
                    distance[i - 1] == distance[i - 3] and
                    distance[i - 3] >= distance[i - 5]):
                    return True
        return False
```

Python’s `list[int]` type hint aligns with LeetCode’s test harness.

---

#### 4.3 C++17

```cpp
class Solution {
public:
    bool isSelfCrossing(vector<int>& distance) {
        int n = distance.size();
        for (int i = 3; i < n; ++i) {
            // Case 1
            if (distance[i] >= distance[i-2] &&
                distance[i-1] <= distance[i-3]) return true;

            // Case 2
            if (i >= 4) {
                // 2a
                if (distance[i-1] == distance[i-3] &&
                    distance[i] + distance[i-4] >= distance[i-2]) return true;

                // 2b – tighter spiral
                if (i >= 5 &&
                    distance[i-2] >= distance[i-4] &&
                    distance[i] + distance[i-4] >= distance[i-2] &&
                    distance[i-1] == distance[i-3] &&
                    distance[i-3] >= distance[i-5]) return true;
            }
        }
        return false;
    }
};
```

The logic is identical; we only adjust syntax for `vector<int>`.

---

### 5.  Complexity Analysis  

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time   | **O(n)** | **O(n)** | **O(n)** |
| Space  | **O(1)** | **O(1)** | **O(1)** |

With `n ≤ 10⁵`, the solution comfortably meets performance constraints on all three platforms.

---

### 6.  Edge‑Case Checklist  

| Scenario | Why it matters | Check |
|----------|----------------|-------|
| All distances equal | Forms a perfect square spiral | `i‑2 >= i‑4` and equal lengths trigger case 2b |
| Distance[0] < distance[1] | Starting segment too short | No crossing; algorithm still safe |
| Length < 4 | Cannot form a self‑crossing shape | Loop starts at 3, returns `false` |

Test the algorithm with these patterns before submitting.

---

### 7.  Common Pitfalls & “Ugly” Mistakes  

1. **O(n²) naive approach** – iterating over all previous segments leads to 10⁵² operations, impossible.  
2. **Off‑by‑one errors** – mis‑indexing `i-3`, `i-4` etc.  
3. **Missing the spiral case** – ignoring the `i >= 5` conditions will miss tight loops.  
4. **Using 64‑bit overflow** – `distance[i] + distance[i-4]` can reach `2·10⁵`; fits in 32‑bit signed int, but keep an eye in languages with smaller integer ranges.  

The “ugly” part of this problem is the need to **think geometrically** without drawing each step. The trick is to **recognize the only two intersection patterns** and encode them succinctly.

---

### 8.  Interview Tips  

| Tip | How it Helps |
|-----|--------------|
| **Explain the geometry first** | Demonstrates analytical thinking. |
| **Write pseudocode before coding** | Shows clarity and reduces bugs. |
| **Highlight O(n) & O(1) space** | Interviewers love efficient solutions. |
| **Show edge‑case tests** | Proves robustness. |
| **Mention LeetCode links** | Provides context and shows research skills. |

> *“Why do you only need to look back 3 or 4 steps?”* – explain that any farther segment cannot intersect the current one due to the counter‑clockwise movement pattern.

---

### 9.  SEO‑Optimized Blog Post (Excerpt)

> **Title:** *Mastering LeetCode 335 – Self‑Crossing: Java, Python, C++ Solutions + Interview Secrets*  
> **Meta Description:** *Learn how to solve LeetCode Self‑Crossing in O(n) time. Get Java, Python, C++ code, a detailed walk‑through, and interview tips that land you a job.*

> **Keywords:** `Self Crossing problem`, `Leetcode 335`, `Java solution`, `Python solution`, `C++ solution`, `geometric path crossing`, `algorithm interview`, `O(n) solution`, `job interview coding`, `software engineer interview`.

> **Body Highlights:**
> - Problem definition in plain language
> - Intuitive explanation of 3‑step and 4‑step crossing
> - Clean, commented code snippets for each language
> - Complexity analysis with visual charts
> - Edge‑case checklist and common mistakes
> - Interview Q&A with expert advice
> - Take‑away: why this problem is a must‑know for backend & system design roles

---

### 10.  Takeaway  

- **Know the two core crossing patterns** (3‑step & 4‑step).  
- **Implement in O(n) time, O(1) space**; avoid O(n²).  
- **Show thorough testing** – LeetCode accepts the edge cases, so cover them.  
- **Translate the logic into any language** – the algorithm is language‑agnostic.

With these three solutions in your résumé and the interview mindset outlined above, you’ll turn **Self‑Crossing** into a showcase of **geometric intuition, algorithmic efficiency, and clean coding** – exactly what recruiters in tech want. Good luck on your next coding interview!