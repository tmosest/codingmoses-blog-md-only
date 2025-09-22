---
title: LeetCode 3024. Type of Triangle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap

> **LeetCode 3024 – Type of Triangle**  
> You are given a 0‑indexed integer array `nums` of length **3** that contains the side lengths of a potential triangle.  
>  
> *Return* `"equilateral"`, `"isosceles"`, `"scalene"`, or `"none"` based on the side lengths.

> **Constraints**  
> * `nums.length == 3`  
> * `1 ≤ nums[i] ≤ 100`

---

## 2.  Core Idea

1. **Triangle Inequality** – For any 3 sides `a ≤ b ≤ c` the only requirement to form a triangle is `a + b > c`.  
2. **Uniqueness Count** – The type is determined by how many distinct side lengths exist  
   * 1 unique → `equilateral`  
   * 2 unique → `isosceles`  
   * 3 unique → `scalene`

Sorting guarantees the largest side is at the end, making the inequality check trivial.  
A `Set` (or a frequency map) gives the uniqueness count in `O(1)` space.

---

## 3.  Reference Implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.

---

### 3.1 Java

```java
import java.util.*;

class Solution {
    public String triangleType(int[] nums) {
        // 1. Sort to make the largest side last
        Arrays.sort(nums);

        // 2. Triangle inequality check
        if (nums[0] + nums[1] <= nums[2]) {
            return "none";
        }

        // 3. Count distinct sides
        Set<Integer> distinct = new HashSet<>();
        for (int n : nums) distinct.add(n);

        switch (distinct.size()) {
            case 1: return "equilateral";
            case 2: return "isosceles";
            default: return "scalene";
        }
    }
}
```

---

### 3.2 Python

```python
class Solution:
    def triangleType(self, nums: List[int]) -> str:
        nums.sort()                          # largest side last
        if nums[0] + nums[1] <= nums[2]:
            return "none"

        distinct = len(set(nums))
        if distinct == 1:
            return "equilateral"
        if distinct == 2:
            return "isosceles"
        return "scalene"
```

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string triangleType(vector<int> &nums) {
        sort(nums.begin(), nums.end());          // largest side last
        if (nums[0] + nums[1] <= nums[2])        // triangle inequality
            return "none";

        unordered_set<int> s(nums.begin(), nums.end());
        switch (s.size()) {
            case 1: return "equilateral";
            case 2: return "isosceles";
            default: return "scalene";
        }
    }
};
```

All three solutions run in **O(1)** time (sorting 3 elements is constant) and use **O(1)** additional space.

---

## 4.  Blog Article – “The Good, The Bad, and the Ugly of Determining Triangle Type”

### 4.1 Why This Problem Matters

- **Foundational Geometry** – Understanding triangle properties is a cornerstone of computational geometry.
- **Interview Proficiency** – LeetCode 3024 is a popular “easy” question that appears in many coding interviews.
- **Algorithmic Thinking** – It forces you to think about *edge cases* (`0`, negative, non‑triangle) and *optimal data structures* (`Set` vs. `Map`).

### 4.2 The Good

| What | Why It’s Good |
|------|---------------|
| **Simplicity** | Sorting 3 numbers is trivial; no need for complex data structures. |
| **Robustness** | The triangle‑inequality guard catches all impossible cases. |
| **Readability** | A small `Set` or `unordered_set` tells the story in one line. |
| **Time/Space** | O(1) time and space; perfect for interview constraints. |

### 4.3 The Bad (Edge‑Case Pitfalls)

| Pitfall | Example | Fix |
|---------|---------|-----|
| **Assuming Sorted Input** | Someone might skip sorting and mistakenly think the array is already ordered. | Always sort before the inequality check. |
| **Off‑by‑One on Inequality** | Using `>=` instead of `>` makes degenerate (collinear) “triangles” slip through. | Use strict `>` per the triangle inequality theorem. |
| **Incorrect Uniqueness Count** | Counting frequencies incorrectly (e.g., using `if (a==b && b==c) …`) fails when sides are shuffled. | Use a set to get the true unique count. |

### 4.4 The Ugly – Common Mis‑Approaches

| Mis‑Approach | Why It’s Ugly |
|--------------|---------------|
| **Full‑Scale Sorting Algorithm** | For 3 elements, using `Arrays.sort()` (which uses quicksort/mergesort) is overkill and can be slower in practice. |
| **Nested Loops for Count** | Checking each pair for equality (`if a==b && b==c`) becomes error‑prone as you add more sides. |
| **Missing Triangle Inequality** | Returning a type when `a + b <= c` passes leads to wrong answers on many hidden tests. |
| **Using Big O Misleading** | Stating the algorithm is O(n log n) is technically true for general arrays, but for n=3 it’s misleading in an interview setting. |

---

## 5.  SEO‑Optimized Closing

**Title**: “Master LeetCode 3024: How to Detect the Type of a Triangle (Java, Python, C++)”

**Meta Description**:  
Learn how to solve LeetCode 3024 – *Type of Triangle* – in Java, Python, and C++. Understand the triangle inequality, uniqueness counting, and why this easy question is a must‑know for coding interviews. Get the best code snippets, edge‑case tips, and interview prep strategies.

**Keywords**:  
LeetCode 3024, triangle type, triangle inequality, equilateral, isosceles, scalene, coding interview, Java solution, Python solution, C++ solution, easy algorithm, set usage, interview questions

**Call‑to‑Action**:  
If you’re prepping for your next coding interview, hit “Like” and subscribe for more algorithm walkthroughs. Drop a comment with the next LeetCode problem you’d like us to crack!

---

### 6.  Takeaway

- Keep your code **small** and **readable**.  
- Always guard against **edge cases** – especially triangle inequality.  
- Use a **set** to count unique values – it’s bullet‑proof.  
- Remember, for n = 3, time and space are **O(1)**, so simplicity trumps fancy tricks.

Happy coding—and may your interviewees be impressed by your triangle‑savvy!