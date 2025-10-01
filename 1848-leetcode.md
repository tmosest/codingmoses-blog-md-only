---
title: LeetCode 1848. Minimum Distance to the Target Element - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1848. Minimum Distance to the Target Element – A Deep Dive (Java, Python & C++)

> **Keywords:** LeetCode, Minimum Distance to the Target Element, interview question, coding interview, Java, Python, C++, algorithm, time complexity, space complexity, job interview

---

## Table of Contents

1. [Problem Statement](#problem-statement)  
2. [Intuition & Constraints](#intuition)  
3. [Solution Approaches](#approaches)  
   - 3.1 **Good** – One‑Pass Linear Scan (O(n) time, O(1) space)  
   - 3.2 **Bad** – Brute Force with Extra Work  
   - 3.3 **Ugly** – Over‑Optimized but Hard to Read  
4. [Reference Implementations](#implementations)  
   - 4.1 Java  
   - 4.2 Python  
   - 4.3 C++  
5. [Complexity Analysis](#complexity)  
6. [Take‑Away Checklist for Interviews](#checklist)  
7. [Conclusion & Next Steps](#conclusion)  

---

## 1. Problem Statement <a name="problem-statement"></a>

**LeetCode 1848 – Minimum Distance to the Target Element**

> Given an integer array `nums` (0‑indexed), an integer `target`, and a starting index `start`, return the minimum absolute distance from `start` to any index `i` such that `nums[i] == target`.  
> The answer is `abs(i - start)`.

**Examples**

| nums                | target | start | Output | Explanation |
|---------------------|--------|-------|--------|-------------|
| `[1,2,3,4,5]`       | 5      | 3     | 1      | Only index `4` matches target → `|4-3| = 1` |
| `[1]`               | 1      | 0     | 0      | Only element → distance `0` |
| `[1,1,1,1,1,1,1,1,1,1]` | 1 | 0 | 0 | First occurrence already at start |

**Constraints**

- `1 ≤ nums.length ≤ 1000`
- `1 ≤ nums[i] ≤ 10⁴`
- `0 ≤ start < nums.length`
- `target` is guaranteed to appear in `nums`.

---

## 2. Intuition & Constraints <a name="intuition"></a>

The task is **purely a search problem** – we need to find the *closest* index of a value.  
Because the array is small (`≤ 1000`) and we are allowed a single linear pass, the simplest solution is a **one‑pass scan** that tracks the minimum distance encountered.

No sorting, no binary search, no extra data structures are needed.  
This keeps the algorithm **O(n)** time and **O(1)** auxiliary space.

---

## 3. Solution Approaches <a name="approaches"></a>

### 3.1 Good – One‑Pass Linear Scan  
**Why it’s good**

| Criterion | ✔️ |
|-----------|---|
| Simplicity | Easy to understand and code. |
| Performance | O(n) time, O(1) space – optimal for given constraints. |
| Readability | Clear loop, single condition. |
| Edge Cases | Handles start at beginning or end, duplicate targets. |

**Idea**

```
minDist = ∞
for i in 0 … nums.length-1
    if nums[i] == target
        minDist = min(minDist, |i - start|)
return minDist
```

### 3.2 Bad – Brute Force with Extra Work  
A naive approach might:

1. Collect all indices of `target` into a list.
2. Iterate over that list to compute distances.

This introduces unnecessary **O(k)** extra space (where k = number of target occurrences) and a two‑phase pass, which is wasteful for such a small input.

### 3.3 Ugly – Over‑Optimized but Hard to Read  
Some “clever” solutions use bit tricks or two‑pointer dance:

```java
int i = start, j = start;
while (i >= 0 || j < n) { ... }
```

Although still O(n), the logic becomes opaque and can lead to bugs when the start index is near an array boundary.

---

## 4. Reference Implementations <a name="implementations"></a>

Below are clean, production‑ready implementations in the three most popular interview languages.

### 4.1 Java (LeetCode‑style)

```java
/**
 * 1848. Minimum Distance to the Target Element
 * LeetCode style Java implementation.
 */
public class Solution {
    public int getMinDistance(int[] nums, int target, int start) {
        int minDist = Integer.MAX_VALUE;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == target) {
                int dist = Math.abs(i - start);
                if (dist < minDist) {
                    minDist = dist;
                }
            }
        }
        return minDist;
    }
}
```

> **Time:** `O(n)`  
> **Space:** `O(1)`  

### 4.2 Python 3

```python
"""
1848. Minimum Distance to the Target Element
Python 3 implementation
"""

class Solution:
    def getMinDistance(self, nums: List[int], target: int, start: int) -> int:
        min_dist = float('inf')
        for i, val in enumerate(nums):
            if val == target:
                dist = abs(i - start)
                if dist < min_dist:
                    min_dist = dist
        return min_dist
```

### 4.3 C++ (Modern C++17)

```cpp
/**
 * 1848. Minimum Distance to the Target Element
 * C++17 implementation
 */
class Solution {
public:
    int getMinDistance(vector<int>& nums, int target, int start) {
        int minDist = INT_MAX;
        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i] == target) {
                int dist = abs(i - start);
                minDist = min(minDist, dist);
            }
        }
        return minDist;
    }
};
```

All three snippets share the same core logic, ensuring consistent performance across languages.

---

## 5. Complexity Analysis <a name="complexity"></a>

| Metric | Result |
|--------|--------|
| **Time** | **O(n)** – one full traversal of `nums`. |
| **Space** | **O(1)** – only a few integer variables, no additional containers. |
| **Worst‑case** | When every element equals `target`, we still perform `n` iterations. |

Given the constraints (`n ≤ 1000`), this solution comfortably passes all LeetCode tests in 0 ms (the benchmark from the community).

---

## 6. Take‑Away Checklist for Interviews <a name="checklist"></a>

- **Understand the problem** – identify that we need *distance*, not *index*.
- **Think linear** – O(n) scan is usually enough unless constraints explicitly forbid it.
- **Avoid unnecessary data structures** – keep space minimal unless the problem demands it.
- **Edge‑case handling** – start at array boundaries, duplicate values, single‑element array.
- **Explain your solution** – talk about time/space trade‑offs, why you chose a simple loop.
- **Mention alternatives** – you can discuss the brute‑force list approach or two‑pointer trick to show awareness.

---

## 7. Conclusion & Next Steps <a name="conclusion"></a>

The “Minimum Distance to the Target Element” problem is a classic interview question that tests your ability to:

1. Read a statement and translate it into a simple algorithm.
2. Optimize for both time and space.
3. Write clean, idiomatic code in multiple languages.

Mastering this problem demonstrates mastery of **array traversal** and **distance calculations**, skills that surface in many real‑world code‑bases and larger technical interviews.

### Next Steps for Job‑Ready Practitioners

1. **Implement in Your Favorite Language** – Write the solution in at least one language you’ll use on day‑one (Java, Python, or C++).
2. **Add Unit Tests** – Cover the examples, edge cases, and random tests to prove correctness.
3. **Talk About Complexity** – Be ready to explain O(n) time and O(1) space on the interview stage.
4. **Optimize for Readability** – Clean variable names, comments, and a concise structure are often more impressive than micro‑optimizations.
5. **Practice Variants** – Try similar problems: “Nearest Duplicate Value,” “Closest Pair in an Array,” etc., to reinforce the pattern.

Good luck on your next coding interview! 🚀

--- 

*Prepared by a seasoned software engineer with 10+ years of interview experience.*