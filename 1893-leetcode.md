---
title: LeetCode 1893. Check if All the Integers in a Range Are Covered - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# Check If All the Integers in a Range Are Covered  
*LeetCode 1893 – Java, Python & C++ Solutions + SEO‑Optimised Interview Guide*

---

## TL;DR  

- **Problem** – Determine whether every integer in `[left, right]` is covered by at least one of the given inclusive intervals.
- **Optimal Solution** – A *difference array* (prefix‑sum line sweep) that runs in **O(n + 50)** time and **O(1)** extra space.
- **Why It Rocks** – Simplicity, linear time, constant‑space, and a perfect interview demo of *range counting* techniques.

> **Want to land that software‑engineering job?** Master this pattern and showcase it on your portfolio or GitHub. The same idea powers interval‑covering, segment trees, and sweep‑line problems that recruiters love.

---

## 1. Problem Statement (LeetCode 1893)

> You’re given a 2‑D integer array `ranges` and two integers `left` and `right`.  
> `ranges[i] = [start_i, end_i]` represents an **inclusive** interval.  
> Return `true` if **every** integer in `[left, right]` is covered by at least one interval in `ranges`. Otherwise return `false`.

**Constraints**

| Variable | Range |
|----------|-------|
| `ranges.length` | 1 – 50 |
| `start_i` | 1 – 50 |
| `end_i` | `start_i` – 50 |
| `left`, `right` | 1 – 50 |

> *Because the universe is tiny (1‑50), even an O(2500) brute force works. But we’ll build an O(n) solution that scales.*

---

## 2. Intuition & Key Observation

1. **Covering a single point** – A point `x` is covered iff there exists an interval with `start ≤ x ≤ end`.
2. **From intervals to an array** – We can translate the set of intervals into an array `cover[1…50]` where `cover[x] > 0` means *x is covered*.
3. **Incremental updates** – Updating every point inside each interval would be *O(n · m)*.  
   Instead, use a **difference array** (also called a *lazy‑update* or *delta array*).  
   * Increment `diff[start]` by 1.  
   * Decrement `diff[end+1]` by 1.  
   * The running prefix sum of `diff` gives the active coverage count at each position.

Because the domain is fixed (1‑50), the array size is only 52 (index 0 unused, 51 handles `end+1` when `end==50`).

---

## 3. Algorithm (Line‑Sweep / Difference Array)

```text
1. Create diff[0 … 51] = {0}
2. For each [l, r] in ranges
       diff[l]   += 1
       diff[r+1] -= 1            // r+1 ≤ 51 always
3. cover = 0
   For i = 1 to 50
       cover += diff[i]           // prefix sum – how many intervals cover i
       If left ≤ i ≤ right AND cover == 0
           return false           // a gap found
4. return true
```

**Why it works**

- After step 2, `diff` stores “start” and “end+1” markers.
- The prefix sum `cover` is exactly the number of intervals covering index `i`.
- If any integer in `[left, right]` has `cover == 0`, it is uncovered → return `false`.
- If we finish the loop, every integer in `[left, right]` was covered → return `true`.

---

## 4. Complexity Analysis

| Metric | Calculation | Result |
|--------|-------------|--------|
| **Time** | `O(#ranges)` updates + `O(50)` scan | `O(n)` (≤ O(100)) – effectively constant |
| **Space** | `diff[52]` | `O(1)` (constant) |

> *The solution scales to 10⁵ intervals if the domain grows to 10⁵. The same pattern is used in sweep‑line and segment‑tree algorithms.*

---

## 5. Code Implementations

### 5.1 Java

```java
public class Solution {
    public boolean isCovered(int[][] ranges, int left, int right) {
        // 1‑based indices up to 50; index 51 is the guard for r+1
        int[] diff = new int[52];
        for (int[] r : ranges) {
            diff[r[0]]++;          // start
            diff[r[1] + 1]--;      // end + 1
        }

        int cover = 0;
        for (int i = 1; i <= 50; i++) {
            cover += diff[i];
            if (i >= left && i <= right && cover == 0) {
                return false;      // uncovered point found
            }
        }
        return true;
    }
}
```

> *Java 17+, compile with `javac`, run on LeetCode.*

### 5.2 Python

```python
class Solution:
    def isCovered(self, ranges: List[List[int]], left: int, right: int) -> bool:
        diff = [0] * 52
        for l, r in ranges:
            diff[l] += 1
            diff[r + 1] -= 1

        cover = 0
        for i in range(1, 51):
            cover += diff[i]
            if left <= i <= right and cover == 0:
                return False
        return True
```

> *Python 3.9+, run locally or on LeetCode.*

### 5.3 C++

```cpp
class Solution {
public:
    bool isCovered(vector<vector<int>>& ranges, int left, int right) {
        int diff[52] = {};          // zero‑initialized
        for (const auto &r : ranges) {
            diff[r[0]]++;           // start
            diff[r[1] + 1]--;       // end + 1
        }

        int cover = 0;
        for (int i = 1; i <= 50; ++i) {
            cover += diff[i];
            if (i >= left && i <= right && cover == 0) return false;
        }
        return true;
    }
};
```

> *C++17, compile with `g++ -std=c++17`.*

---

## 6. Edge Cases & Why They Pass

| Edge Case | Reasoning |
|-----------|-----------|
| **`left == right`** | The loop checks that single integer. |
| **Intervals touching at boundaries** (`[1,10]`, `[10,20]`) | `diff[10]` is incremented twice and decremented once at 11, so `cover[10]` remains >0. |
| **No intervals cover a point** | `cover` stays 0, immediate `false`. |
| **All intervals cover entire domain** | `cover` never 0 in `[left,right]`. |
| **Intervals outside 1‑50** | Not allowed by constraints. |

---

## 7. Alternative Approaches

| Approach | Complexity | When to Use |
|----------|------------|-------------|
| **Brute force** – Check each `x` against all intervals | `O(n · (right-left+1))` | Very small ranges (≤ 10) |
| **Sorting & Merging** – Sort intervals, merge, then sweep | `O(n log n)` | When domain size is huge but intervals are few |
| **Segment Tree / BIT** – Range add, point query | `O(log M)` per operation | Dynamic queries, updates |
| **Bitset** – Mark bits for each covered integer | `O(n · size)` but space‑heavy | When 64‑bit mask covers full range |

The difference‑array method is the sweet spot for this problem’s constraints.

---

## 8. The Good, the Bad, and the Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Conceptual Simplicity** | One-pass prefix sum; easy to explain | Requires thinking about “end+1” trick | Forgetting to handle the guard (index 51) → off‑by‑one bugs |
| **Space Efficiency** | Constant extra memory | None | None |
| **Scalability** | Linear in number of intervals | Not adaptive to *updates* (static problem) | Using a large array when domain is huge → wasteful |
| **Interview Presence** | Demonstrates knowledge of sweep‑line and delta updates | Might be seen as too “lazy” if interviewer wants a data‑structure | Over‑engineering with a segment tree for a 50‑size problem |
| **Testing** | One test case fails → fail fast | None | None |

> **Tip** – In an interview, show the “difference array” idea first, then discuss how it generalises to larger domains or dynamic scenarios.

---

## 9. Interview Tips

1. **Show the Pattern**  
   > “I’m using a difference array. Think of it like a *lazy‑update* on a 1‑D array.”  
   > This instantly signals you understand *interval add & prefix query* patterns.

2. **Mention Sweep‑Line**  
   > “Conceptually it’s a sweep‑line: I mark where coverage starts and ends.”  
   > Recruiters love sweep‑line because it’s a general tool.

3. **Discuss Edge Cases**  
   > “We add a guard at 51 to handle `end+1` when `end==50`. This prevents array overrun.”  
   > Shows attention to detail.

4. **Compare Complexity**  
   > “My solution runs in O(n) time and O(1) space, far better than a brute‑force O(n · m).”  
   > Demonstrates analytical thinking.

5. **Portfolio**  
   - Push the Java/Python/C++ code to a public repo.  
   - Add a README explaining the algorithm (copy this guide).  
   - Tag it with `LeetCode`, `interval-covering`, `algorithm`.

6. **Practice Variants**  
   - Try the same pattern on “Maximum Overlap of Intervals” (LeetCode 435).  
   - Write a dynamic version (range add, point query) using Fenwick Tree.

---

## 9. How to Make This Post Job‑Ready

1. **Add to GitHub** – `leetcodes/1893-Check-If-All-Intervals-Are-Covered`.  
2. **CI/CD** – Add unit tests for the edge cases above.  
3. **Docs** – Markdown file with this explanation + code.  
4. **Blog** – Publish on dev.to / Medium with the title *“Check if All the Integers in a Range Are Covered – LeetCode 1893”*.  
5. **LinkedIn** – Post the article, tag recruiters, and share your solution.

---

## 9. Conclusion

- The **difference array** is a *one‑liner* algorithm for *range covering* problems.  
- It runs in linear time, uses constant space, and is trivial to implement in Java, Python, and C++ – all three languages are in high demand for software‑engineering interviews.  
- Mastering this pattern will let you ace LeetCode 1893 *and* impress interviewers who ask similar interval or sweep‑line questions.

> **Next steps**  
> 1. Implement the solution in your preferred language.  
> 2. Add unit tests for all edge cases.  
> 3. Publish a blog post (this one) and the code on GitHub.  
> 4. Practice the pattern on other interval problems (e.g., LeetCode 435, 452).  

Happy coding, and may your next interview start with a *true*!

---  

**SEO Metadata**  

- **Title**: Check If All the Integers in a Range Are Covered – LeetCode 1893 Java, Python & C++ Solutions  
- **Meta Description**: Master LeetCode 1893 with a constant‑space line‑sweep algorithm. See Java, Python, and C++ code, complexity analysis, edge cases, and interview tips.  
- **Keywords**: LeetCode, Java, Python, C++, interval covering, difference array, prefix sum, sweep line, coding interview, job interview, software engineering, algorithm, interview preparation.  

---  

**Author** – *Your Name* | *Software Engineer* | *Open Source Enthusiast*  

---  

Happy interviewing! 🚀