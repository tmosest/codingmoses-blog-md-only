---
title: LeetCode 3523. Make Array Non-decreasing - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  Solution Code

Below are three complete, ready‑to‑submit solutions – one in **Java**, one in **Python**, and one in **C++** – that solve LeetCode 3523 *Make Array Non‑decreasing*.

> ⚠️  *The “simple greedy” approach is correct for this problem.  
> It keeps every element that is not smaller than the previous kept element and ignores the rest.  
> The resulting array is the longest possible non‑decreasing array that can be obtained by merging sub‑arrays into their maximum values.*

---

### Java

```java
// 3523. Make Array Non‑decreasing
// Runtime: O(n), Memory: O(1)

class Solution {
    public int maximumPossibleSize(int[] nums) {
        int size = 0;
        int prev = Integer.MIN_VALUE;   // any value smaller than the smallest element

        for (int num : nums) {
            if (num >= prev) {          // keep this element
                prev = num;
                size++;
            }
        }
        return size;
    }
}
```

---

### Python

```python
# 3523. Make Array Non‑decreasing
# Runtime: O(n), Memory: O(1)

class Solution:
    def maximumPossibleSize(self, nums: List[int]) -> int:
        size = 0
        prev = float('-inf')          # any value smaller than the smallest element

        for num in nums:
            if num >= prev:           # keep this element
                prev = num
                size += 1

        return size
```

---

### C++

```cpp
// 3523. Make Array Non‑decreasing
// Runtime: O(n), Memory: O(1)

class Solution {
public:
    int maximumPossibleSize(vector<int>& nums) {
        int size = 0;
        int prev = INT_MIN;           // any value smaller than the smallest element

        for (int num : nums) {
            if (num >= prev) {        // keep this element
                prev = num;
                ++size;
            }
        }
        return size;
    }
};
```

---

## 2️⃣  Blog Article – “Make Array Non‑decreasing: The Good, The Bad, and The Ugly”

> **Keywords**: *Make Array Non‑decreasing, LeetCode 3523, interview coding challenge, greedy algorithm, longest non‑decreasing subsequence, Java solution, Python solution, C++ solution, data‑structure interview, algorithm interview, coding interview tips, software engineering interview, SEO job posting, tech interview prep.*

---

### Introduction

LeetCode 3523 – *Make Array Non‑decreasing* – is a deceptively simple yet insightful problem that often crops up in software‑engineering interviews. It asks you to perform **zero or more** operations on an array, where each operation collapses a sub‑array into a single element equal to its maximum value. After all operations, the resulting array must be **non‑decreasing**. Your goal: **maximize the size of the final array**.

Why does this matter?  
In real‑world code, you’re often asked to transform data with minimal loss of information. This problem forces you to think about *how far you can keep elements untouched* while still respecting order constraints. It’s a classic illustration of a greedy strategy: keep everything you can and discard the rest.

---

### 1️⃣  The Good – Why the Greedy Solution Works

#### 1.1  Intuition

Imagine walking through the array from left to right.  
- **If the current element is at least as big as the last element you decided to keep**, you can safely keep it – it won’t break the non‑decreasing property.  
- **If it’s smaller**, you *must* either merge it into the previous element (losing its own contribution) or drop it entirely. Merging cannot help you later because any merge with a smaller element only replaces the sub‑array with the larger maximum; the smaller element will never become useful.

Thus, a **single pass** that keeps the “good” elements is sufficient.

#### 1.2  Formal Argument

Let `prev` be the value of the last kept element.  
- When `num >= prev`, the array `prev, num` is already non‑decreasing.  
- When `num < prev`, any sub‑array ending at `num` that includes `prev` will have maximum `prev` (since `prev` is larger). Replacing that sub‑array with `prev` keeps the array non‑decreasing but reduces size by at least one.  
Hence, *any* optimal solution will keep all elements satisfying `num >= prev`, and drop the rest.

#### 1.3  Time & Space Complexity

| Language | Time Complexity | Space Complexity |
|----------|-----------------|------------------|
| Java     | **O(n)**        | **O(1)**         |
| Python   | **O(n)**        | **O(1)**         |
| C++      | **O(n)**        | **O(1)**         |

`n` is the array length (up to 200 000). A linear pass is the fastest possible.

---

### 2️⃣  The Bad – Common Pitfalls

| Mistake | Why it’s wrong | Fix |
|---------|----------------|-----|
| **Treating it as a Longest Increasing Subsequence (LIS)** | LIS considers any subsequence; here you cannot reorder elements, only merge. | Use the greedy “keep if ≥ prev” rule instead of O(n log n) LIS. |
| **Using a stack or deque to simulate merges** | Over‑engineering; the operation’s effect is trivial (replace with max). | One pass suffices; no need for extra data structures. |
| **Assuming you can keep all non‑decreasing pairs** | You might skip a later element that could be kept if you had merged earlier ones. | The greedy algorithm automatically handles this: if a later element is smaller, you skip it, regardless of earlier merges. |
| **Ignoring edge cases (all equal, all decreasing)** | May lead to off‑by‑one errors. | Initialize `prev` to a very small number (`INT_MIN`, `-inf`, or `Integer.MIN_VALUE`). |

---

### 3️⃣  The Ugly – Edge‑Case Traps & “What If” Scenarios

| Scenario | What goes wrong? | Recommendation |
|----------|------------------|----------------|
| **Very large numbers (up to 2 × 10⁵)** | Using `int` in Java or C++ is fine; but avoid `short` or `byte`. | Stick with 32‑bit signed integers. |
| **Negative values** | Problem constraints guarantee positives, but if modified, the algorithm still works with `prev = -inf`. | Ensure `prev` starts lower than any possible element. |
| **Empty array** | LeetCode guarantees at least one element, but defensive coding is good. | Return 0 if array is empty. |
| **All decreasing** | You might mistakenly think you can keep all. | The algorithm correctly keeps only the first element. |
| **All equal** | Some might think you can keep all. | Since each element equals `prev`, you keep all – result is `n`. |

---

### 4️⃣  Bonus – Why This Problem Is a Great Interview Question

1. **Clarity of Constraints** – Simple integer array, fixed operation.  
2. **Immediate Solution** – One pass, one comparison.  
3. **Deep Insight** – Teaches you to recognize when a greedy strategy is optimal.  
4. **Time‑Pressure Friendly** – You can code in 5 minutes, but you still need to explain reasoning.  

A good answer during an interview:  
> “I’ll iterate through the array, keeping a `prev` variable. If the current element is ≥ `prev`, I’ll keep it and update `prev`. Otherwise I’ll skip it. This yields the maximum size because any element that is smaller than the previous kept one would force us to merge it with the previous element, reducing size. The algorithm is O(n) time and O(1) space.”

---

### 5️⃣  Final Thoughts

*Make Array Non‑decreasing* is a beautiful illustration of **“keep it if you can, otherwise drop it”**. The greedy solution is not only efficient but also elegant: it captures the essence of the operation without any auxiliary data structures.  

Whether you’re writing Java for LeetCode, Python for a coding interview, or C++ for a systems‑level interview, the logic remains identical. Just remember to initialize `prev` to a value smaller than any array element, then traverse once and count the “good” elements.

---

#### 📌 Quick Reference – Code Snippets

| Language | Key Lines |
|----------|-----------|
| **Java** | `for (int num : nums) { if (num >= prev) { prev = num; size++; } }` |
| **Python** | `for num in nums: if num >= prev: prev = num; size += 1` |
| **C++** | `for (int num : nums) { if (num >= prev) { prev = num; ++size; } }` |

---

### 🚀 SEO Takeaway

If you’re looking to get noticed by recruiters or automated résumé scanners, include **keywords** such as:

- “Make Array Non‑decreasing solution”
- “LeetCode 3523 Java”
- “Python greedy algorithm”
- “C++ array transformation interview”
- “Longest non‑decreasing subsequence”
- “Interview coding challenge solutions”

These terms are highly searched by hiring managers looking for candidates with algorithmic chops. Combine them with the code snippets above, and you’ll have a solid showcase for your portfolio or LinkedIn.

Happy coding, and may your arrays always stay non‑decreasing!