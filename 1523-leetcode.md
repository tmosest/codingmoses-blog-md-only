---
title: LeetCode 1523. Count Odd Numbers in an Interval Range - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # LeetCode 1523 – “Count Odd Numbers in an Interval Range”  
**The Good, The Bad, and The Ugly – A Complete SEO‑Optimized Guide for Job Interviews**

> *Keywords: LeetCode 1523, Count Odd Numbers, O(1) algorithm, interview coding, job interview, algorithmic problem, coding interview solutions, Java, Python, C++*

---

## 📌 Table of Contents  

| Section | Link |
|---------|------|
| 🚀 Introduction | #introduction |
| 📚 Problem Statement | #problem-statement |
| ✅ Constraints | #constraints |
| 🏗️ Naïve Approach | #naïve-approach |
| ⚡ Optimized O(1) Solution | #optimized-solution |
| 💻 Code Implementations | #code-implementations |
| 📈 Complexity Analysis | #complexity-analysis |
| 🔎 Edge‑Case Checklist | #edge‑case-checklist |
| 🤝 Interview Tips | #interview-tips |
| 🎯 The Good | #the-good |
| ⚠️ The Bad | #the-bad |
| 👿 The Ugly | #the-ugly |
| 🎉 Wrap‑up & Takeaways | #wrap‑up |
| 📚 Further Reading | #further-reading |

---

## 🚀 Introduction

When you’re scrolling through **LeetCode** for interview prep, you’ll often stumble upon the *“easy”* category.  
One such problem is **LeetCode 1523 – Count Odd Numbers in an Interval Range**.  
It looks simple, but it’s a perfect example of how a solid mathematical insight can transform a brute‑force loop into an **O(1)** constant‑time solution.  

In this blog we’ll walk through:

1. The problem and its constraints.  
2. A naïve two‑pointer approach (good for teaching, bad for production).  
3. A concise O(1) formula that’s perfect for a live interview.  
4. Full code snippets in **Java, Python, and C++**.  
5. SEO‑friendly explanations so recruiters find you on Google.  

Ready to turn a seemingly trivial problem into a showcase of clean, efficient coding? Let’s dive in.  

---

## 📚 Problem Statement

> **Count Odd Numbers in an Interval Range**  
> **LeetCode ID:** 1523  
> **Difficulty:** Easy  

Given two non‑negative integers `low` and `high` (`low ≤ high`), return the number of odd integers in the inclusive range `[low, high]`.

```text
Input: low = 3, high = 7
Output: 3          // 3, 5, 7

Input: low = 8, high = 10
Output: 1          // 9
```

---

## ✅ Constraints

- `0 ≤ low ≤ high ≤ 10^9`  
- Both `low` and `high` fit in a 32‑bit signed integer.  

---

## 🏗️ Naïve Approach

A straightforward way is to iterate from `low` to `high`, count the odds, and return the result:

```java
int count = 0;
for (int i = low; i <= high; i++) {
    if ((i & 1) == 1) count++;  // i is odd
}
return count;
```

**Time Complexity:** O(high‑low+1) – linear in the interval length.  
**Space Complexity:** O(1).

This approach is acceptable for tiny ranges but will time‑out for the maximum input (`high = 10^9`).  
The **two‑pointer** solution you saw in the LeetCode discussions reduces the loop but still runs in O(n/2). It’s still suboptimal for interview settings where a single‑line solution is expected.

---

## ⚡ Optimized O(1) Solution

The trick: **an arithmetic progression of odds**.  
All odd numbers can be expressed as `2k + 1`.  
The number of odd integers between two numbers can be derived from simple floor/ceil math.

### Derivation

1. Count of odd numbers ≤ `x`:
   - If `x` is odd: `(x / 2) + 1`
   - If `x` is even: `x / 2`

   In one line:
   ```text
   oddUpTo(x) = (x + 1) / 2
   ```

   Because integer division truncates toward zero in Java/Python/C++.

2. Count of odds in `[low, high]`:
   ```text
   oddsInRange = oddUpTo(high) - oddUpTo(low - 1)
   ```

### Final Formula

```text
answer = ((high + 1) / 2) - ((low) / 2)
```

Explanation:

- `high + 1` ensures we include the upper bound if it’s odd.  
- `low / 2` counts the odds strictly **below** `low`.

This works for all `low`, `high` pairs, including when `low = 0`.

---

## 💻 Code Implementations

Below are clean, idiomatic solutions in **Java**, **Python**, and **C++** that run in **O(1)** time and **O(1)** space.

### Java (LeetCode‑style)

```java
/**
 * LeetCode 1523. Count Odd Numbers in an Interval Range
 */
class Solution {
    public int countOdds(int low, int high) {
        // (high + 1)/2 counts odds <= high
        // low/2 counts odds < low
        return (high + 1) / 2 - low / 2;
    }
}
```

*Why it’s great:*  
- Single line inside the method.  
- No loops, no extra variables.  
- Handles `low = 0` naturally.  

### Python

```python
# LeetCode 1523 – Count Odd Numbers in an Interval Range
class Solution:
    def countOdds(self, low: int, high: int) -> int:
        return (high + 1) // 2 - low // 2
```

Python’s integer division (`//`) behaves like Java’s `/` for integers, so the logic is identical.

### C++ (C++17)

```cpp
// LeetCode 1523. Count Odd Numbers in an Interval Range
class Solution {
public:
    int countOdds(int low, int high) {
        return (high + 1) / 2 - low / 2;
    }
};
```

All three snippets use the same O(1) formula; the only differences are language‑specific syntax and comments.

---

## 📈 Complexity Analysis

| Metric | Naïve / Two‑Pointer | Optimized O(1) |
|--------|---------------------|----------------|
| **Time** | O(high - low + 1) | O(1) |
| **Space** | O(1) | O(1) |
| **Practical Impact** | Can hit time limits for `high = 10^9`. | Instantly passes all test cases, even with huge ranges. |

---

## 🔎 Edge‑Case Checklist

| Edge Case | What to Test | Expected Result |
|-----------|--------------|-----------------|
| `low = high` | Single‑number interval | `1` if odd, `0` if even |
| `low = 0` | Lower bound at zero | Count odds from 1 to `high` |
| `high = 10^9` | Max value | Works without overflow (Java/C++ 32‑bit signed int still ok) |
| `low = 1, high = 1` | Odd single element | `1` |
| `low = 2, high = 2` | Even single element | `0` |

The formula handles all these gracefully.

---

## 🤝 Interview Tips

1. **Explain the math upfront.**  
   “The number of odds up to x is (x+1)/2…”.  
   This shows you understand the underlying pattern.

2. **Show the one‑liner.**  
   Recruiters love concise, readable code.  
   “`return (high + 1) / 2 - low / 2;`”

3. **Mention edge cases.**  
   Highlight that `low = 0` is already covered.

4. **If they ask for a two‑pointer solution, discuss its inefficiency** and why the O(1) approach is preferable.

5. **Be ready to implement in any language** you’re comfortable with. The formula is language‑agnostic.

---

## 🎯 The Good

- **Simplicity:** A single arithmetic line.  
- **Performance:** Constant time even for the largest inputs.  
- **Readability:** Easy to understand at a glance.  
- **Portability:** Works in Java, Python, C++, and most other languages.  

---

## ⚠️ The Bad

- **Misreading the problem** can lead to a wrong formula.  
- **Neglecting edge cases** (e.g., forgetting `low = 0`).  
- **Assuming a loop is necessary** – many candidates write O(n) code by default.  

---

## 👿 The Ugly

- **Two‑pointer solutions** that still run in linear time (`O(n/2)`), e.g.,

  ```java
  int count = 0;
  int l = low | 1;          // make l odd
  int r = high & ~1;        // make r odd
  while (l <= r) { count++; l += 2; r -= 2; }
  ```

  *Why it’s ugly:* Still loops over every second number, wasted time on large intervals.

- **Using floating point arithmetic** for integer counts can introduce rounding errors.  
  Always stick to integer division.

---

## 🎉 Wrap‑up

*LeetCode 1523* is a textbook example of how a mathematical insight turns a linear scan into a constant‑time solution.  
- Use the formula: **`(high + 1)/2 - low/2`**.  
- Write it in one line – recruiters love brevity.  
- Cover edge cases quickly.  

By mastering this problem, you’ll demonstrate:

- **Algorithmic thinking** (identifying patterns).  
- **Coding efficiency** (optimal time/space).  
- **Interview poise** (clear explanation + concise code).  

These skills are exactly what recruiters look for in a coding candidate.

---

## 📚 Further Reading

- [LeetCode 1523 on the official site](https://leetcode.com/problems/count-odd-numbers-in-an-interval-range/)  
- “Algorithmic Thinking” – *Coding Interview Prep*  
- “Time Complexity in Coding Interviews” – *InterviewBit Blog*  

---

*Ready to impress your next interviewer?*  
Copy the snippets, practice explaining the math, and rock that interview! 🚀