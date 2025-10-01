---
title: LeetCode 1523. Count Odd Numbers in an Interval Range - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 1523. Count Odd Numbers in an Interval Range – A Complete, SEO‑Optimized Guide  
*(Java | Python | C++) – “The Good, The Bad, & The Ugly” + Job‑Ready Blog Post*  

---

### 1. Problem Recap  

> **Given two non‑negative integers `low` and `high`, return the count of odd numbers between them (inclusive).**  
> **Constraints**: `0 ≤ low ≤ high ≤ 10⁹`  

**Examples**  

| low | high | Result | Explanation |
|-----|------|--------|-------------|
| 3   | 7    | 3      | 3, 5, 7 are odd |
| 8   | 10   | 1      | 9 is the only odd |

---

### 2. The “Good” – O(1) Math Formula  

The fastest, cleanest, and most memory‑efficient solution is **O(1)**:

1. **Total integers** in `[low, high]` → `high - low + 1`  
2. **Even numbers** in the range can be computed by:
   * If both ends are even → `(high - low) / 2 + 1`
   * If one end is odd → `(high - low + 1) / 2`
3. **Odd numbers** = total - evens

But an even simpler one‑liner works for all cases:

```text
oddCount = (high + 1) / 2 - low / 2
```

Why?  
* `high + 1` pushes the upper bound to the next integer, turning the count of **odd** numbers up to `high` into an integer division.  
* Subtracting `low / 2` removes the odds that are below `low`.

This logic holds for both odd and even boundaries.

---

### 3. The “Bad” – Two‑Pointer (Brute‑Force) Approach  

Some learners try to iterate with two pointers, stepping by 2:

```java
int low = (low % 2 == 0) ? low + 1 : low;
int high = (high % 2 == 0) ? high - 1 : high;
int count = 0;
while (low <= high) {
    count++;
    low += 2;
}
```

* **Time Complexity**: `O((high - low)/2)` → up to 5×10⁸ operations for worst‑case range → **slow**.  
* **Space Complexity**: `O(1)` – fine.  
* **Real‑World Impact**: On LeetCode it may time‑out or barely pass due to the large input bound.

---

### 4. The “Ugly” – Manual Edge‑Case Handling  

Some solutions hard‑code checks for equal boundaries or try to handle odd/even mismatches separately.  
Example (Java):

```java
if (low == high) return (low % 2 == 0) ? 0 : 1;
```

While correct, this bloats the code and makes it harder to read.  
**Best practice**: Stick to the simple formula and avoid branching logic unless absolutely necessary.

---

## 5. Code Implementations

Below are concise, production‑ready solutions in **Java**, **Python**, and **C++** that run in **O(1)** time and **O(1)** space.

---

### 5.1 Java

```java
/**
 * LeetCode 1523. Count Odd Numbers in an Interval Range
 *
 * @param low  lower bound (inclusive)
 * @param high upper bound (inclusive)
 * @return number of odd integers between low and high
 */
class Solution {
    public int countOdds(int low, int high) {
        // (high + 1)/2 gives count of odds <= high
        // low/2 gives count of odds < low
        return (high + 1) / 2 - low / 2;
    }
}
```

> **Complexity**:  
> • Time: **O(1)**  
> • Space: **O(1)**

---

### 5.2 Python

```python
class Solution:
    def countOdds(self, low: int, high: int) -> int:
        """
        Count odd numbers in inclusive range [low, high].
        """
        return (high + 1) // 2 - low // 2
```

> **Complexity**:  
> • Time: **O(1)**  
> • Space: **O(1)**

---

### 5.3 C++

```cpp
class Solution {
public:
    int countOdds(int low, int high) {
        // Integer division in C++ truncates toward zero
        return (high + 1) / 2 - low / 2;
    }
};
```

> **Complexity**:  
> • Time: **O(1)**  
> • Space: **O(1)**

---

## 6. SEO‑Optimized Blog Article

### Title  
> *“Master LeetCode 1523: Count Odd Numbers in an Interval – Fast O(1) Solution in Java, Python & C++”*

### Meta Description  
> Learn the simplest O(1) trick to solve LeetCode 1523 “Count Odd Numbers in an Interval Range”. Includes Java, Python, and C++ code snippets, time‑space analysis, and a deep dive into common pitfalls.

---

### 6.1 Introduction  

Counting odd numbers in a range may sound trivial, but a naive loop can choke on large bounds like `10⁹`. In this post, we’ll:

1. Break down the mathematical shortcut that turns the problem into a one‑liner.
2. Compare the efficient O(1) method to the classic two‑pointer brute‑force approach.
3. Offer clean, ready‑to‑paste code in **Java**, **Python**, and **C++**.
4. Highlight why this solution is a must‑know for any software engineering interview.

Whether you’re prepping for LeetCode, a coding interview, or just love clean algorithms, this guide will boost your confidence.

---

### 6.2 The Math Behind the One‑Liner  

**Why does ` (high + 1) / 2 - low / 2 ` work?**

| Step | Explanation |
|------|-------------|
| 1 | `high + 1` shifts the range to the next integer, effectively counting all odd numbers up to `high`. |
| 2 | Integer division ` / 2` in C++/Java/Python truncates toward zero, turning the sum into a count of odds. |
| 3 | Subtracting `low / 2` removes any odds that are below `low`. |
| 4 | Result is the count of odds **in** `[low, high]`. |

This approach is valid for any non‑negative `low` and `high`, and handles both odd and even boundaries automatically.

---

### 6.3 Time & Space Complexity

| Approach | Time | Space | Why it matters |
|----------|------|-------|----------------|
| Brute‑Force (two‑pointer) | **O((high - low)/2)** | **O(1)** | Linear in range – unacceptable for 10⁹ |
| O(1) formula | **O(1)** | **O(1)** | Constant time & memory – ideal for interviews |

---

### 6.4 Common Mistakes to Avoid  

| Mistake | Fix |
|---------|-----|
| Iterating one by one (`i++`) | Step by 2 or use the formula |
| Hard‑coding edge cases (`low == high`) | Let the formula handle it |
| Using floating point division | Use integer division (`//` or `/` with ints) |
| Forgetting inclusive bounds | Add 1 to `high` in the formula |

---

### 6.5 Real‑World Applications  

1. **Database Queries** – Counting odd IDs in a large dataset.  
2. **Analytics** – Fast frequency counts in a time range.  
3. **Competitive Programming** – Quick answer to range‑based problems.  

---

### 6.6 Quick Code Reference  

| Language | Code |
|----------|------|
| **Java** | ```public int countOdds(int low, int high) { return (high + 1) / 2 - low / 2; }``` |
| **Python** | ```def countOdds(low, high): return (high + 1) // 2 - low // 2``` |
| **C++** | ```int countOdds(int low, int high) { return (high + 1) / 2 - low / 2; }``` |

---

### 6.7 Takeaway  

- The **O(1) formula** is the optimal solution for LeetCode 1523.  
- It runs in constant time regardless of input size and requires no loops.  
- Understanding this trick demonstrates mathematical intuition—exactly what interviewers love.

---

### 6.8 Final Thoughts & Career Tip  

> **Mastering simple O(1) tricks** like this one proves you can think algorithmically and write clean code—skills that employers prize.  
> Add the code snippets and explanation to your GitHub README, tag your blog with “LeetCode 1523”, “Interview Prep”, “O(1) Algorithms”, and you’ll boost visibility and attract recruiters.

Happy coding! 🚀

---

## 7. Quick Checklist for Recruiters

- ✅ O(1) solution implemented in three major languages.  
- ✅ Edge‑case handling is implicit, not explicit.  
- ✅ Complexity analysis ready for interview discussion.  
- ✅ Blog post SEO‑friendly, ready to rank for “Count Odd Numbers LeetCode”.  

Good luck landing that next interview!