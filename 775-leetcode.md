---
title: LeetCode 775. Global and Local Inversions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Global vs. Local Inversions – LeetCode 775  
### The “Ideal Permutation” Problem (Java, Python & C++)

> **Problem** – LeetCode 775  
> Given a permutation `nums` of length `n` (values `0 … n‑1`), return `true` if the number of global inversions equals the number of local inversions.

---

## 1.  The One‑liner Solution (O(n) time, O(1) space)

The key insight:

*Every global inversion must also be a local inversion.*  
If any element is displaced by more than one position from its “ideal” index, a non‑local global inversion will appear and the counts can never be equal.

Thus the check reduces to:

```text
abs(nums[i] - i) <= 1   for every i
```

If the condition holds for all indices, return `true`; otherwise `false`.

### Java

```java
public class Solution {
    public boolean isIdealPermutation(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            if (Math.abs(nums[i] - i) > 1) {
                return false;
            }
        }
        return true;
    }
}
```

### Python

```python
class Solution:
    def isIdealPermutation(self, nums: List[int]) -> bool:
        return all(abs(nums[i] - i) <= 1 for i in range(len(nums)))
```

### C++

```cpp
class Solution {
public:
    bool isIdealPermutation(vector<int>& nums) {
        for (int i = 0; i < nums.size(); ++i) {
            if (abs(nums[i] - i) > 1) return false;
        }
        return true;
    }
};
```

> **Time Complexity** – `O(n)` (single scan)  
> **Space Complexity** – `O(1)` (in‑place)

---

## 2.  The Brute‑Force “Good” – When You Need a Reference

A naive `O(n²)` double loop can be useful for debugging or when the array size is tiny.

```java
int local = 0, global = 0;
for (int i = 0; i < n - 1; i++) if (nums[i] > nums[i+1]) local++;
for (int i = 0; i < n; i++)
    for (int j = i+1; j < n; j++)
        if (nums[i] > nums[j]) global++;
return local == global;
```

*Pros:* Easy to understand, no hidden tricks.  
*Cons:* Too slow for `n = 10⁵`. Use only for small test cases or as a sanity check.

---

## 3.  The Optimal Approach – “The Ugly” of Over‑Engineering

Some top solutions use a *prefix‑maximum* trick to detect a non‑local global inversion without the absolute check. The idea:

```text
If max(nums[0..i]) > nums[i+1]  →  non‑local global inversion.
```

While elegant, it adds a tiny bit of extra bookkeeping and is less intuitive than the absolute distance check. For interviewers, the simpler `abs(nums[i] - i)` solution is preferred.

---

## 4.  Why This Problem Matters in Interviews

1. **Algorithmic Insight** – Shows ability to think about permutations and inversion properties.  
2. **Constant‑Space Optimisation** – Demonstrates that O(1) space is often enough.  
3. **Clean Code** – One‑liner logic can impress when presented with proper reasoning.  
4. **Multi‑Language Mastery** – Being able to write the same logic in Java, Python, and C++ proves versatility.

---

## 5.  SEO‑Friendly Blog Post Outline

Below is a fully‑ready article you can paste into a Medium blog, Dev.to, or a personal portfolio. The content is keyword‑rich, structured for readability, and includes a meta description for maximum visibility.

---

# 🏁 Blog Post: “Global vs. Local Inversions – How to Crack LeetCode 775 in Java, Python & C++”

**Meta Description (for search engines)**  
> Master LeetCode 775 – Global vs. Local Inversions. Learn the ideal permutation trick, read a Java, Python & C++ solution, and understand why it’s a must‑know for software‑engineering interviews.

---

## 📚 Introduction

If you’ve been grinding LeetCode, you’ve probably bumped into **LeetCode 775 – Global vs. Local Inversions**. It’s deceptively simple: a permutation of `0…n‑1`, check whether global inversions equal local inversions. Yet this tiny problem is a *classic interview pet‑project* that can showcase your algorithmic thinking and give you a quick interview win.

Why is it useful?  
- **Permutation fundamentals** – Inversions are a core concept in sorting and algorithm analysis.  
- **One‑liner elegance** – Interviewers love clean, O(n) solutions that run in constant space.  
- **Edge‑case thinking** – Spotting that a value must stay within one index away from its “ideal” spot trains you to spot hidden constraints.

Let’s walk through the *good*, *bad*, and *ugly* parts of this problem, show how to implement it in Java, Python, and C++, and finish with interview‑ready tips.

---

## 🔍 Problem Statement (Restated)

You’re given an array `nums` of length `n` that is a permutation of `0 … n‑1`.  
- **Global inversion** – pair `(i, j)` where `i < j` and `nums[i] > nums[j]`.  
- **Local inversion** – pair `(i, i+1)` where `nums[i] > nums[i+1]`.

Return `true` if **global inversions = local inversions**.

---

## 5️⃣ Naïve Brute‑Force (Good but Slow)

```java
int local = 0, global = 0;
for (int i = 0; i < n-1; i++) if (nums[i] > nums[i+1]) local++;
for (int i = 0; i < n; i++)
    for (int j = i+1; j < n; j++)
        if (nums[i] > nums[j]) global++;
return local == global;
```

- **Good**: Easy to follow, no tricky math.  
- **Bad**: O(n²) – will time‑out for `n = 100,000`.  
- **Ugly**: Not scalable, but good for sanity testing.

---

## 💡 The One‑liner – The “Good” Optimal Solution

**Observation** – In a permutation, the “ideal” index for value `v` is `v` itself.  
If a value moves more than one place, you create a global inversion that is *not* local.

So:

```text
|nums[i] – i| <= 1  for all i
```

If the condition holds everywhere, local and global inversions match.

**Java**

```java
public boolean isIdealPermutation(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        if (Math.abs(nums[i] - i) > 1) return false;
    }
    return true;
}
```

**Python**

```python
return all(abs(nums[i] - i) <= 1 for i in range(len(nums)))
```

**C++**

```cpp
for (int i = 0; i < nums.size(); ++i)
    if (abs(nums[i] - i) > 1) return false;
return true;
```

- **Time**: `O(n)`  
- **Space**: `O(1)`

This is the *good* optimal approach: fast, simple, and uses only a constant amount of memory.

---

## 🚨 The “Ugly” Alternative: Prefix‑Maximum Check

Some solutions maintain the maximum value seen so far:

```text
if (max_so_far > nums[i+1]) → non‑local global inversion.
```

```cpp
int max_so_far = INT_MIN;
for (int i = 0; i < nums.size()-1; ++i) {
    if (max_so_far > nums[i+1]) return false;
    max_so_far = max(max_so_far, nums[i]);
}
return true;
```

- **Pros**: No `abs()` call, a bit of extra logic.  
- **Cons**: Less readable, requires thinking about prefix maxima.  
- **When to Use**: When you want to avoid `abs()` in an interview setting that penalizes math calls, but the one‑liner is usually more interview‑friendly.

---

## 📈 Complexity Summary

| Approach                 | Time | Space |
|--------------------------|------|-------|
| Absolute‑distance one‑liner | O(n) | O(1) |
| Prefix‑maximum | O(n) | O(1) |
| Brute‑force | O(n²) | O(1) |

---

## 🛠️ Common Pitfalls & Edge‑Case Checklist

| Pitfall | Fix |
|---------|-----|
| **Using `>` instead of `>=`** – The condition must be *strictly* `<= 1`. | Use `Math.abs(nums[i] - i) > 1` (Java) or `abs(nums[i] - i) > 1` (Python/C++). |
| **Overflow** – `abs()` on `Integer.MIN_VALUE` is problematic in Java. | Safely compute `Math.abs(nums[i] - i)`; the values are within `[0, n-1]`, so overflow never occurs. |
| **Misreading “local” vs “global”** – Local is always between consecutive indices. | Double‑check your definition if implementing the brute‑force. |
| **Testing on empty array** – Problem guarantees `n ≥ 1`, but guard against `nums.length == 0`. | Add a trivial `if (nums.length == 0) return true;`. |

---

## 🎯 Interview‑Ready Tips

1. **Explain the insight first** – “All non‑local global inversions require an element to be displaced by > 1.”  
2. **Show the simple O(n) code** – Avoid unnecessary complexity.  
3. **Mention time/space** – Interviewers always want a big‑O summary.  
4. **Ask clarifying questions** – e.g., “Is the array always a permutation?” to avoid wasted work.  
5. **Optional: Show the brute‑force** – Helps demonstrate that you understand the problem definition fully.

---

## 📢 SEO Checklist

| Keyword | Placement |
|---------|-----------|
| LeetCode 775 | Title, intro, problem statement |
| Global vs. Local Inversions | Throughout, especially in explanation |
| Ideal Permutation | Problem section, solution comments |
| Java solution | Java code header |
| Python solution | Python code header |
| C++ solution | C++ code header |
| Interview algorithm | Conclusion, interview tips |
| Software engineer interview | Closing remarks |
| Algorithmic interview | Meta description, headings |

**Meta Description (for search engines):**  
> Crack LeetCode 775 – Global vs. Local Inversions. Read the O(n) “Ideal Permutation” solution in Java, Python, and C++ with a clear explanation, time/space analysis, and interview‑ready tips. Perfect for software‑engineering interviews.

---

## 📌 Final Thoughts

- **Good** – The problem forces you to reason about permutations and inversion counts.  
- **Bad** – A naive `O(n²)` solution is too slow for the constraints but great for learning.  
- **Ugly** – Over‑engineering with prefix‑max or binary indexed trees makes the code less readable; keep it simple.

By mastering the one‑liner, you’ll be able to **solve LeetCode 775 in an interview** in under 30 seconds and impress the interviewer with a clean, optimal solution.  

Happy coding—and good luck landing that next software‑engineering role!