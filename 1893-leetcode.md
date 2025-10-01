---
title: LeetCode 1893. Check if All the Integers in a Range Are Covered - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 1893 – “Check if All the Integers in a Range Are Covered”

> **SEO Keywords**: LeetCode 1893, Check if All the Integers in a Range Are Covered, Java solution, Python solution, C++ solution, interview algorithm, difference array, line sweep, prefix sum, job interview prep

---

### 1️⃣ Problem Overview

Given an array `ranges` where each element is an inclusive interval `[start, end]`, and two integers `left` and `right`, determine whether **every** integer in the inclusive interval `[left, right]` is covered by at least one interval in `ranges`.

> **Example**  
> `ranges = [[1,2],[3,4],[5,6]]`, `left = 2`, `right = 5` → **true**  
> All numbers 2, 3, 4, 5 are covered.

---

### 2️⃣ Constraints

| Item | Value |
|------|-------|
| `1 ≤ ranges.length ≤ 50` | small input size |
| `1 ≤ starti ≤ endi ≤ 50` | bounded universe |
| `1 ≤ left ≤ right ≤ 50` | bounded query range |

Because the universe is only 1…50, a *constant‑time* algorithm is possible, but we’ll present a generic solution that works for larger inputs as well.

---

### 3️⃣ Naïve Approach (O(n·m))

* For every integer `x` in `[left, right]`  
  * Scan all ranges to see if `x` is inside any interval.  
  * If we find a gap, return `false`.

This is easy to implement but costs `O(n · (right-left+1))`. With the given limits it runs fast, but it’s **not** scalable.

---

### 4️⃣ Efficient Approach – Difference Array / Line Sweep (O(n + m))

1. **Create a difference array** `diff[0 … 51]` (size `maxVal+2` to handle the `+1` sentinel).  
2. For each interval `[l, r]`  
   * `diff[l]   += 1`    (start of coverage)  
   * `diff[r+1] -= 1`   (end of coverage)  
3. Sweep from `1` to `right` maintaining a running sum `cover`.  
   * `cover += diff[i]`  
   * If `i ≥ left` **and** `cover == 0` → a gap exists → return `false`.  
4. If the loop finishes, every integer in `[left, right]` was covered → return `true`.

> **Why `r+1`?**  
> Intervals are inclusive. Subtracting at `r+1` turns coverage off *after* the end.

**Complexities**

| Metric | Result |
|--------|--------|
| Time | `O(n + m)` (n = number of ranges, m = length of the universe, here 50) |
| Space | `O(m)` (the difference array) |

With the fixed limit of 50, the algorithm is essentially `O(51)` – a *constant‑time* solution.

---

## 🚀 Code Implementations

### 📄 Java

```java
public class Solution {
    public boolean isCovered(int[][] ranges, int left, int right) {
        int[] diff = new int[52];                 // 1..50 + 51 sentinel
        for (int[] r : ranges) {
            diff[r[0]] += 1;
            diff[r[1] + 1] -= 1;
        }

        int cover = 0;
        for (int i = 1; i <= right; i++) {
            cover += diff[i];
            if (i >= left && cover == 0) {
                return false;                    // uncovered integer
            }
        }
        return true;                             // all covered
    }
}
```

---

### 📄 Python

```python
class Solution:
    def isCovered(self, ranges: List[List[int]], left: int, right: int) -> bool:
        diff = [0] * 52                       # 1..50 + sentinel
        for l, r in ranges:
            diff[l] += 1
            diff[r + 1] -= 1

        cover = 0
        for i in range(1, right + 1):
            cover += diff[i]
            if i >= left and cover == 0:
                return False
        return True
```

---

### 📄 C++

```cpp
class Solution {
public:
    bool isCovered(vector<vector<int>>& ranges, int left, int right) {
        int diff[52] = {0};                     // 1..50 + sentinel
        for (auto &r : ranges) {
            diff[r[0]] += 1;
            diff[r[1] + 1] -= 1;
        }

        int cover = 0;
        for (int i = 1; i <= right; ++i) {
            cover += diff[i];
            if (i >= left && cover == 0) {
                return false;                   // uncovered
            }
        }
        return true;
    }
};
```

> All three solutions share the same algorithmic heart: a linear *line sweep* with a difference array.

---

## 🌱 The Good, The Bad, and The Ugly

| Aspect | The Good | The Bad | The Ugly |
|--------|----------|---------|----------|
| **Algorithmic Insight** | Difference array turns range updates into point queries. | Naïve `O(n·m)` is simple but not scalable. | Forgetting the sentinel (`r+1`) leads to off‑by‑one errors. |
| **Code Simplicity** | Single loop, clear logic. | O(n·m) is one‑liner but hidden performance cost. | Complex set‑based or sorting approaches add unnecessary lines. |
| **Scalability** | Works for any universe size. | Falls apart for large ranges. | Over‑engineering with segment trees is overkill. |
| **Readability** | Clear variable names (`diff`, `cover`). | Very concise but less explicit. | Deep recursion or lambda tricks may obfuscate intent. |
| **Edge Cases** | Handles `left == right`, overlapping intervals, duplicates. | May silently accept gaps if not careful. | Not accounting for `r == 50` (need sentinel) → runtime error. |

**Takeaway**: The difference array is the sweet spot – it’s *simple*, *fast*, and *robust*.

---

## 🧪 Quick Test Cases

```text
1. ranges = [[1,2],[3,4],[5,6]], left=2, right=5   → true
2. ranges = [[1,10],[10,20]], left=21, right=21     → false
3. ranges = [[1,50]], left=1, right=50              → true
4. ranges = [[2,4],[6,8]], left=5, right=7          → false
5. ranges = [[1,5],[3,7],[6,10]], left=4, right=9  → true
```

Run them in each language to confirm correctness.

---

## 📈 Complexity Summary

| Language | Time | Space |
|----------|------|-------|
| Java     | `O(n + m)` | `O(m)` |
| Python   | `O(n + m)` | `O(m)` |
| C++      | `O(n + m)` | `O(m)` |

With `m = 50`, both time and space are *constant*.

---

## 🎯 How This Helps Your Job Hunt

1. **Interview Warm‑Up** – LeetCode 1893 is a classic “interval coverage” problem. Mastering it shows you can handle *range queries* and *line sweep* techniques.
2. **Clean Code** – Your solution is concise yet readable – a big plus for senior engineering interviews.
3. **Scalable Thinking** – You’re ready to extend the idea to larger universes or dynamic updates (segment trees, BITs).
4. **Showcase** – Add this to your portfolio or GitHub README with a brief blog post. Recruiters love seeing real‑world solutions.

---

## 🎓 Final Takeaway

The *difference array* (also known as *prefix sum sweep*) is a powerful pattern for problems involving range updates and point queries. When the universe is bounded, it gives you an *O(1)* solution. For larger inputs, the same idea scales linearly. Implement it in Java, Python, or C++, test thoroughly, and you’ll have a solid, interview‑ready snippet to showcase.

Happy coding—and may the job offers roll in! 🚀