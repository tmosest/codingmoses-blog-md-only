---
title: LeetCode 1893. Check if All the Integers in a Range Are Covered - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1893 – “Check if All the Integers in a Range Are Covered”

| Language | Time | Space |
|----------|------|-------|
| **Java** | O(n + m) | O(m) |
| **Python** | O(n + m) | O(m) |
| **C++** | O(n + m) | O(m) |

> *n = number of ranges, m = size of the integer universe (≤ 50)*

> **Why this matters** – This “cover‑check” problem is a *classic* for coding‑interviews, especially for roles that focus on **array manipulation, prefix sums, and difference arrays**. Mastering it demonstrates a clean O(1) domain solution that scales to large inputs.

---

## 1️⃣ Problem Summary

You’re given:

- `ranges`: a list of inclusive integer intervals `[[start1,end1], …]`.
- Two bounds `left` and `right`.

**Goal:** Return `true` iff every integer `x` with `left ≤ x ≤ right` lies inside at least one of the provided intervals. Otherwise return `false`.

---

## 2️⃣ Brute‑Force Solution (Naïve)

Check each integer in `[left,right]` against all intervals.

```python
def is_covered_bruteforce(ranges, left, right):
    for x in range(left, right+1):
        if not any(start <= x <= end for start, end in ranges):
            return False
    return True
```

- **Time:** `O((right-left+1) * n)` – worst‑case 2500×50 = 125k operations (acceptable for LeetCode but not elegant).
- **Space:** `O(1)`.

---

## 3️⃣ Optimal Solution – Difference Array (Line Sweep)

Because the universe is bounded (`1..50`), we can maintain a *difference array* that records when a coverage starts and stops.

### 3.1 Intuition

1. For every interval `[l, r]`, increment `diff[l]` by `1`.
2. Decrement `diff[r+1]` by `1` (since `r` is inclusive).
3. Prefix‑sum `diff` gives the number of active intervals at each integer.
4. While scanning, if we hit a number in `[left,right]` with 0 active intervals, the answer is `false`.

### 3.2 Code

#### Python

```python
def is_covered(ranges: list[list[int]], left: int, right: int) -> bool:
    """
    O(n + m) time, O(m) space
    m == 51 because 1 <= start,end <= 50
    """
    diff = [0] * 52  # indices 1..50; 51 used for r+1 when r==50

    for l, r in ranges:
        diff[l] += 1
        diff[r + 1] -= 1

    active = 0
    for i in range(1, 51):
        active += diff[i]
        if left <= i <= right and active == 0:
            return False
    return True
```

#### Java

```java
public class Solution {
    public boolean isCovered(int[][] ranges, int left, int right) {
        int[] diff = new int[52];          // indices 1..50, 51 for r+1
        for (int[] r : ranges) {
            diff[r[0]]++;                  // start of coverage
            diff[r[1] + 1]--;              // end of coverage
        }

        int active = 0;
        for (int i = 1; i <= 50; i++) {
            active += diff[i];
            if (i >= left && i <= right && active == 0) {
                return false;
            }
        }
        return true;
    }
}
```

#### C++

```cpp
class Solution {
public:
    bool isCovered(vector<vector<int>>& ranges, int left, int right) {
        int diff[52] = {};                // 1..50, 51 for r+1

        for (auto &r : ranges) {
            diff[r[0]]++;                  // start
            diff[r[1] + 1]--;              // end+1
        }

        int active = 0;
        for (int i = 1; i <= 50; ++i) {
            active += diff[i];
            if (i >= left && i <= right && active == 0) {
                return false;
            }
        }
        return true;
    }
};
```

### 3.3 Complexity

- **Time:** `O(n + 50)` ≈ `O(n)` (linear in the number of ranges).
- **Space:** `O(52)` ≈ `O(1)` – constant extra memory.

---

## 4️⃣ Discussion – Good, Bad & Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Concept** | Uses a *difference array* – a classic linear‑time trick | Requires knowledge of line‑sweep / prefix sum (may be unfamiliar) | Over‑engineering for a tiny domain (≤ 50) – could just mark a boolean array |
| **Implementation** | Extremely concise; only two loops | Index `r+1` may look odd; off‑by‑one bugs common | Hard‑to‑debug when universe changes; need to resize array |
| **Performance** | O(1) space, O(n) time – optimal | Brute force is O((right-left+1)*n) – slow for large ranges | For `m` huge (e.g., 10^9) this approach fails; need a sweep line over events |
| **Readability** | Easy for seasoned engineers | Might be intimidating for beginners | If the interviewer expects a “sort‑intervals” solution, this diff‑array might be seen as overkill |

> **Takeaway** – Master the *difference array* trick; it’s a go‑to pattern for range‑update/count problems. In interviews, you can start with a clear explanation of the idea, then implement the constant‑size array version. If the interviewer pushes for a generic solution (unbounded universe), you can switch to an event‑based sweep line.

---

## 5️⃣ Test Cases

```python
# Example 1
assert is_covered([[1,2],[3,4],[5,6]], 2, 5) == True

# Example 2
assert is_covered([[1,10],[10,20]], 21, 21) == False

# Edge: overlapping intervals
assert is_covered([[1,3],[2,5],[4,6]], 1, 6) == True

# Edge: single number not covered
assert is_covered([[1,5]], 6, 6) == False

# Full coverage
assert is_covered([[1,50]], 1, 50) == True
```

---

## 6️⃣ SEO‑Optimized Blog Article

> ### “How to Crack LeetCode 1893 in 5 Minutes – A Java/Python/C++ Guide for Job Interviews”

---

#### 🔎 Keywords

- LeetCode 1893
- Check if All the Integers in a Range Are Covered
- coding interview
- difference array
- prefix sum
- line sweep
- algorithm interview question
- Java solution LeetCode
- Python LeetCode
- C++ coding interview

---

#### 📄 Article

---

### 1. Introduction

In software‑engineering interviews, you’ll often see questions that ask whether *every integer in a range is covered by a set of intervals*. LeetCode 1893 is the canonical example. Though the constraints look tiny (`1 ≤ start ≤ end ≤ 50`), the problem is a **gateway** to core algorithm concepts: prefix sums, difference arrays, and line‑sweep. Mastering it not only boosts your LeetCode rating but also signals to recruiters that you understand efficient array manipulation.

---

### 2. Problem Recap

You receive an array of inclusive intervals and two bounds `left` and `right`. Return `true` iff each integer `x` with `left ≤ x ≤ right` lies inside at least one interval. This is a *coverage* test: `[[1,2],[3,4],[5,6]]` covers `[2,5]` → `true`; `[[1,10],[10,20]]` does **not** cover `21` → `false`.

---

### 3. Why a Naïve Approach is Bad

A double loop that tests each integer against every interval is simple, but it’s **O((right‑left+1) × n)**. On LeetCode you might still pass (125 k operations), but in an interview you’ll spend precious seconds on a *slightly un‑optimal* solution. Recruiters want to see that you’ll think **line‑sweep** before “just brute‑forcing”.

---

### 3.1 Optimal Strategy: Difference Array

| Step | Action | Rationale |
|------|--------|-----------|
| 1 | **Increment** `diff[l]` by `1` for every interval `[l,r]`. | Marks the *start* of coverage. |
| 2 | **Decrement** `diff[r+1]` by `1`. | Since the interval is inclusive, coverage ends *after* `r`. |
| 3 | Compute a prefix sum over `diff`. | Gives the count of active intervals at each integer. |
| 4 | While scanning, if an integer in `[left,right]` has 0 active intervals → `false`. | Immediate detection of a gap. |

> **Why it’s O(n + m) vs O((right-left+1)*n)** – We *pre‑aggregate* all starts/ends in a single pass, then a single linear pass over the 51‑slot array (constant). No repeated checks against each interval.

---

### 4. Implementation Highlights

#### Java

```java
public boolean isCovered(int[][] ranges, int left, int right) { … }
```

> • `int[] diff = new int[52];` – constant memory.  
> • Two loops: one to fill `diff`, another to scan and early‑exit.

#### Python

```python
def is_covered(ranges, left, right): … 
```

> • `diff = [0] * 52` – Python list is memory‑efficient for small domains.  
> • Use `any(start <= x <= end for start, end in ranges)` in the naive version to see the difference.

#### C++

```cpp
bool isCovered(vector<vector<int>>& ranges, int left, int right) { … }
```

> • Static array `int diff[52]` – zero‑initialization in C++ is trivial.  
> • Fastest compile‑time due to stack allocation.

---

### 5. What Interviewers Really Look For

| Hint | Why |
|------|-----|
| Explain the *difference array* in plain English before coding. | Shows you understand the “prefix‑sum trick” and can articulate it. |
| Mention that `r+1` is safe because the universe is bounded. | Avoids “off‑by‑one” surprises. |
| Offer an alternate “sort‑intervals” solution if asked. | Flexibility: you can pivot to a sweep line over events for larger universes. |

> **Pro tip:** If the interviewee’s first solution is *naïve*, quickly pivot to the difference array. Recruiters love a candidate who can **recognize an opportunity for optimization**.

---

### 6. Complexity Cheat Sheet

| Language | Time | Space |
|----------|------|-------|
| Java | **O(n + m)** → **O(n)** | **O(m)** → **O(1)** |
| Python | **O(n + m)** | **O(m)** |
| C++ | **O(n + m)** | **O(m)** |

> *n = # of ranges (≤ 50), m = size of universe (≤ 51).*

---

### 7. Final Thoughts

- **LeetCode 1893** is not just a range‑check – it’s a *signal* that you grasp core data‑structure tricks.
- The difference‑array pattern is **portable**: use it for “range addition”, “range sum queries”, and many other interview staples.
- In a coding‑interview, present the idea first, then deliver the constant‑size array implementation. If the recruiter asks about scalability, explain how the same concept can be extended to a *sweep‑line* over event points.

---

### 8. Ready to Nail Your Next Job Interview?

- Write the solution in **Java** (static typing + OOP style).
- Test it in **Python** (concise, readable).
- Validate with **C++** (performance‑oriented).

Add these solutions to your portfolio, push them to GitHub, and when recruiters search “LeetCode 1893 Java”, your code will pop up at the top. Good luck with your interview prep! 🚀

---

### 📌 TL;DR

- **Use a difference array** – 2 loops, constant memory, linear time.
- **Explain the logic** clearly; recruiters love a well‑structured answer.
- **Include edge‑case tests** – it shows you’ve thought through “bad” and “ugly” scenarios.

--- 

#### 📥 Download All Code

```bash
# Java
curl -o Solution.java https://pastebin.com/raw/xxxxxxxx

# Python
curl -o solution.py https://pastebin.com/raw/xxxxxxxx

# C++
curl -o solution.cpp https://pastebin.com/raw/xxxxxxxx
```

(Replace `xxxxxxxx` with the real Pastebin hash or your own hosted copy.)

--- 

✅ *Now you’re ready to answer LeetCode 1893 like a pro, impress recruiters, and land that software‑engineering job.*