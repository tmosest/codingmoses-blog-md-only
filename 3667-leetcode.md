---
title: LeetCode 3667. Sort Array By Absolute Value - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 3667 – “Sort Array By Absolute Value”  
**LeetCode** | **Easy** | **Time Limit 2 s** | **Memory 512 MB**

---

### TL;DR  
> Reorder an integer array so that the elements are sorted by increasing absolute value.  
> Any order that satisfies the condition is acceptable.  

---

## 📚 Problem Statement

You’re given an integer array `nums` (1 ≤ nums.length ≤ 100, –100 ≤ nums[i] ≤ 100).  
Return any permutation of `nums` that is **non‑decreasing by absolute value**.

> **Example**  
> `nums = [3, -1, -4, 1, 5]` → `[-1, 1, 3, -4, 5]`  
> (Both `[-1,1,3,-4,5]` and `[1,-1,3,-4,5]` are valid.)

---

## 🧠 Easy, Concise Solutions in Three Languages

Below are clean, production‑ready snippets that solve the problem in **O(n log n)** time using the language’s built‑in sorting utilities.

| Language | Code |
|----------|------|
| **Java** | ```java
import java.util.*;
import static java.lang.Math.abs;

class Solution {
    public int[] sortByAbsoluteValue(int[] nums) {
        // Convert to stream → boxed → sorted by Math::abs → back to int[]
        return Arrays.stream(nums)
                     .boxed()
                     .sorted(Comparator.comparingInt(Math::abs))
                     .mapToInt(Integer::intValue)
                     .toArray();
    }
}
``` |
| **Python** | ```python
class Solution:
    def sortByAbsoluteValue(self, nums: List[int]) -> List[int]:
        # Python's sorted uses Timsort – stable, O(n log n)
        return sorted(nums, key=abs)
``` |
| **C++** | ```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> sortByAbsoluteValue(vector<int>& nums) {
        sort(nums.begin(), nums.end(),
             [](int a, int b){ return abs(a) < abs(b); });
        return nums;
    }
};
``` |

---

## 📏 Complexity Analysis

| Step | Complexity | Notes |
|------|------------|-------|
| Sorting | **O(n log n)** | Built‑in sort is highly optimized. |
| Extra space | **O(n)** | Java stream + boxing; Python list copy; C++ `sort` works in‑place but requires a few constants. |
| Edge cases | None | Handles empty or single‑element arrays gracefully. |

> **Why O(n log n) is fine**  
> With *n* ≤ 100, any O(n²) approach would also pass. Sorting keeps the solution short, clear, and scalable for larger inputs.

---

## 🏗️ Alternative (Counting Sort) – When You Need O(n)

Because the range of values is tiny (–100 to 100), you can sort in linear time:

```python
def sortByAbsoluteValue(nums):
    bucket = [[] for _ in range(201)]  # 0 maps to abs(0)
    for x in nums:
        bucket[abs(x)] .append(x)
    return [x for group in bucket for x in group]
```

This uses **O(n)** time and **O(1)** extra space (201 buckets).  
Useful for interviewers who ask “Can you do better than O(n log n)?”

---

## 🔎 The “Good, The Bad, The Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Clarity** | Built‑in sort + lambda keeps code short. | Over‑optimisation (counting sort) can hide the intent. | Misusing `abs()` on `Integer.MIN_VALUE` (Java) or `LLONG_MIN` (C++) may overflow. |
| **Performance** | O(n log n) is enough for constraints. | Sorting may be overkill for tiny arrays. | Using `Math.abs(Integer.MIN_VALUE)` triggers `ArithmeticException` in Java. |
| **Robustness** | Handles negative, positive, zero seamlessly. | None. | Forgetting to import `java.util.*` or `static java.lang.Math.abs` in Java. |

> **Takeaway:** The simplest, most maintainable solution wins most interviews. Only dive into linear‑time tricks if explicitly asked.

---

## 🎯 How This Helps Your Job Hunt

1. **Algorithmic Breadth** – Demonstrates familiarity with sorting, lambda functions, and stream APIs – skills interviewers love.
2. **Coding Best Practices** – Clean, commented, and language‑idiomatic code shows attention to detail.
3. **Problem‑Solving Narrative** – In your interview, walk through the “good, bad, ugly” lens; it showcases your critical thinking.
4. **Edge‑Case Awareness** – Mentioning overflow risks reflects real‑world production‑grade consciousness.

---

## 📌 SEO‑Optimized Blog Post

### Title  
**“Master LeetCode 3667: Sort Array By Absolute Value – Java, Python, C++ Solutions + Interview Tips”**

### Meta Description  
Solve LeetCode 3667 “Sort Array By Absolute Value” in Java, Python, and C++ with clear code, complexity analysis, and interview‑ready strategies. Boost your coding interview score today!

### Suggested Keywords  
- LeetCode 3667  
- Sort Array By Absolute Value  
- Java sorting absolute value  
- Python sort by abs  
- C++ absolute value sort  
- coding interview algorithms  
- job interview coding questions  
- algorithm complexity  

---

### Blog Outline

| Section | Content |
|---------|---------|
| **Introduction** | Brief problem recap + why it matters in interviews. |
| **Problem Statement** | Exact constraints, examples, edge cases. |
| **Naïve Approach** | Mention `O(n²)` bubble sort and why it’s unnecessary. |
| **Optimal Approach** | Show the three concise snippets; explain the comparator/`key`. |
| **Complexity Discussion** | Time & space trade‑offs. |
| **Linear‑time Counting Sort** | When you can go beyond `O(n log n)`. |
| **Good, Bad, Ugly** | Quick table summarizing pros/cons. |
| **Interview Angle** | What interviewers look for: clarity, edge‑case handling, alternative solutions. |
| **Testing & Edge Cases** | Sample test harnesses, boundary checks. |
| **Take‑away & Career Advice** | How mastering this boosts your résumé. |
| **Call‑to‑Action** | Invite comments, suggest following for more interview prep. |

---

## 📁 Final Code Repository

Create a small repo with the following structure:

```
leetcode-3667/
├─ java/
│   └─ Solution.java
├─ python/
│   └─ solution.py
├─ cpp/
│   └─ solution.cpp
└─ README.md   (copy of this blog)
```

Push to GitHub, add the commit hash to your resume or LinkedIn, and link it in your portfolio.

---

## 🎉 🎉 🎉

You now have:

1. **Three production‑ready solutions** ready to paste into LeetCode or a job interview.  
2. A **complete blog post** that explains the problem, dives deep into the algorithmic trade‑offs, and positions you as a thoughtful candidate.  
3. **SEO‑friendly content** that can help your personal site rank for “Sort Array By Absolute Value” and related interview questions.

Happy coding—and good luck landing that job! 🚀