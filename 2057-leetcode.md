---
title: LeetCode 2057. Smallest Index With Equal Value - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 2057 – *Smallest Index With Equal Value*  
### A complete Java/Python/C++ solution + a job‑search‑friendly blog article

> **Keywords:** LeetCode 2057, Smallest Index With Equal Value, Java solution, Python solution, C++ solution, interview algorithm, coding interview, job interview, algorithmic thinking

---

## 1️⃣ Problem Recap  

You’re given a 0‑indexed integer array `nums`.  
Find the smallest index `i` such that `i % 10 == nums[i]`.  
Return `-1` if no such index exists.

```
Input : nums = [4,3,2,1]
Output: 2          // 2 % 10 == 2
```

**Constraints**

| Property | Value |
|----------|-------|
| 1 ≤ `nums.length` ≤ 100 | |
| 0 ≤ `nums[i]` ≤ 9 | |

The limits are tiny, but the challenge is to write a clean, readable solution that a hiring manager can skim in a few seconds.

---

## 2️⃣ The “Good, the Bad, and the Ugly”

| Aspect | What you’ll love | What could trip you up | The ugly truth |
|--------|------------------|-----------------------|----------------|
| **Good** | • O(n) time, O(1) space.<br>• No extra data structures.<br>• One-pass, early‑exit logic. | | |
| **Bad** | The modulo base is hard‑coded (`10`). If you later need a generic “mod m” check, you’ll need to refactor. | | |
| **Ugly** | Some interviewers will throw a *corner case*: an array of all 9’s. The naive solution *works* but you might still be asked to explain *why* you used `break` instead of returning immediately. | | |

> **Bottom line:** Keep the algorithm simple, explain the early‑exit rationale, and be ready to generalize if the interviewer says “What if the base was `k`?”

---

## 3️⃣ Brute‑Force vs. One‑Pass

A naïve “two‑loop” approach would double‑check every pair – clearly overkill.  
The one‑pass solution below iterates from the start, checks the condition, and stops on the first hit. That’s all you need.

---

## 4️⃣ Solutions in 3 Languages

> All three snippets are **function‑only** – the surrounding `main` or test harness is omitted to keep the focus on the core logic.

### 4.1 Java

```java
// LeetCode 2057 – Smallest Index With Equal Value
class Solution {
    public int smallestEqual(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            if (i % 10 == nums[i]) {
                return i;          // first match = smallest index
            }
        }
        return -1;                 // no match found
    }
}
```

**Why `return` instead of `break`?**  
Returning immediately is clearer to a reviewer – the function exits as soon as the answer is known.

---

### 4.2 Python

```python
# LeetCode 2057 – Smallest Index With Equal Value
class Solution:
    def smallestEqual(self, nums: List[int]) -> int:
        for i, val in enumerate(nums):
            if i % 10 == val:
                return i
        return -1
```

*Note:* `enumerate` gives both index and value in a single pass.

---

### 4.3 C++

```cpp
// LeetCode 2057 – Smallest Index With Equal Value
class Solution {
public:
    int smallestEqual(vector<int>& nums) {
        for (int i = 0; i < nums.size(); ++i) {
            if (i % 10 == nums[i]) return i;
        }
        return -1;
    }
};
```

`vector<int>&` passes by reference for efficiency, though copying a 100‑element array is trivial.

---

## 5️⃣ Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time   | **O(n)** | **O(n)** | **O(n)** |
| Space  | **O(1)** | **O(1)** | **O(1)** |

`n` is the length of `nums` (≤ 100).  
Because we break on the first hit, the *average* time is even less than `n` in typical inputs.

---

## 6️⃣ Edge Cases & Gotchas

| Edge case | What to check | Fix |
|-----------|---------------|-----|
| Empty array | Not allowed by constraints, but guard anyway | Add `if (nums.empty()) return -1;` |
| All 9’s | No match → return -1 | Handled automatically |
| `i % 10 == nums[i]` when `nums[i] == 0` | 0 works fine | No special handling needed |
| `nums[i] > 9` | Violates constraints – you may still write robust code | `if (i % 10 == nums[i] % 10)` – but not required |

---

## 7️⃣ Variants & Generalizations

| Question | How to adapt |
|----------|--------------|
| “What if the base was **k** instead of 10?” | Replace `10` with `k`. |
| “Find all indices, not just the smallest.” | Collect indices in a `vector<int>` and return. |
| “The array could be extremely large (10⁶).” | Still O(n) time; memory unaffected. |

---

## 8️⃣ Interview‑Style Questions

1. **Why do you return immediately instead of breaking?**  
   *Answer:* It shortens the code and clearly signals the algorithm’s exit point.

2. **What if `nums[i]` could be negative?**  
   *Answer:* In Python and Java, the `%` operator handles negatives by returning a non‑negative remainder (Python’s `%`), but you’d need to adjust for C++’s sign behavior.

3. **How would you handle a scenario where you need to return the *second* smallest matching index?**  
   *Answer:* Track the first match, continue the loop, and capture the second one when found.

---

## 9️⃣ SEO‑Optimized Blog Outline

Below is a draft you can copy‑paste into a blogging platform (Medium, Dev.to, LinkedIn Pulse, etc.).  

> **Title**: “Cracking LeetCode 2057 – Smallest Index With Equal Value – Java, Python, C++ + Interview Tips”

```
# Cracking LeetCode 2057 – Smallest Index With Equal Value

## Problem Statement
...

## Why This Problem Matters
...
## The One‑Pass Solution – O(n) Time, O(1) Space
...

### Java Implementation
...

### Python Implementation
...

### C++ Implementation
...

## Complexity Analysis
...

## Common Pitfalls
...

## Interview‑Ready Variants
...

## Final Thoughts & Takeaway
...
```

*Add meta tags:*  
- `meta name="keywords" content="LeetCode 2057, Smallest Index With Equal Value, Java solution, Python solution, C++ solution, coding interview, algorithm interview, job interview"`  
- `meta name="description" content="Learn how to solve LeetCode 2057 (Smallest Index With Equal Value) in Java, Python, and C++. Understand the algorithm, complexity, and interview tips to impress hiring managers."`

*Use headings (`h1`, `h2`, `h3`) and bullet lists for readability.*  
*Add code blocks with syntax highlighting.*  

---

## 10️⃣ Final Checklist Before You Submit

- ✅ **Correctness** – Run all provided examples + some random tests.  
- ✅ **Readability** – Variable names (`i`, `val`, `answer`) are self‑explanatory.  
- ✅ **Time/Space** – Documented and proven optimal.  
- ✅ **Interview Talk** – Be ready to explain *why* you break/return early and *how* you would generalize.  
- ✅ **Blog** – Use the outline, publish, and share on LinkedIn/Twitter with a compelling caption.

---

### 🎉 Congratulations!

You now have:

- A clean, production‑ready solution in **Java, Python, and C++**.  
- A job‑friendly blog post that showcases your understanding of the problem, the algorithm, and interview‑style communication.  
- A quick reference guide for common interview follow‑ups.

Good luck on your coding interviews! 🚀