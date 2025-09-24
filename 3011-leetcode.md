---
title: LeetCode 3011. Find if Array Can Be Sorted - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem 3011 – “Find if Array Can Be Sorted”

> **LeetCode**: https://leetcode.com/problems/find-if-array-can-be-sorted/  
> **Difficulty**: Medium  
> **Function signature**  
> ```java
> public boolean canSortArray(int[] nums)
> ```  

**Statement**  
You are given a 0‑indexed array of positive integers `nums`.  
In one operation you may swap any two *adjacent* elements **only if** they have the **same number of set bits** (the same popcount).  
You can perform this operation any number of times (including zero).  
Return `true` if it is possible to sort the array in ascending order, otherwise return `false`.

**Constraints**

| Constraint | Value |
|------------|-------|
| `1 ≤ nums.length ≤ 100` | |  
| `1 ≤ nums[i] ≤ 10^28` | |

--------------------------------------------------------------------

## 2.  Intuition & “Good / Bad / Ugly” Analysis

| Aspect | What you’re looking for | Why it matters |
|--------|-------------------------|----------------|
| **Good** | **Observation** – Elements can *only* cross other elements if they have the *same* popcount. | This reduces the problem to a simple comparison of two sequences: the original popcount sequence and the popcount sequence after a normal sort. |
| **Bad** | **Common pitfall** – Forgetting that *adjacent* swaps preserve the relative order of *different* popcount groups. | Many interviewers ask you to think about “block‑permutations”; the wrong solution that ignores block ordering gets full credit in a naive test, but will be rejected in a rigorous interview. |
| **Ugly** | **Misreading “popcount”** – Counting bits incorrectly (e.g. using `bitCount` on a 64‑bit value vs. 32‑bit). | It can silently produce wrong answers for large numbers; always use the language‑specific built‑in popcount that matches the integer width. |

--------------------------------------------------------------------

## 3.  Solution Idea

1. **Compute the popcount for every element** (the number of `1`s in its binary representation).
2. **Create a sorted copy** of the array (ordinary ascending sort).
3. **Compare** the popcount of the element at each index in the original array with the popcount of the element at the same index in the sorted array.
4. If any index mismatches, sorting is impossible → return `false`.  
   Otherwise, return `true`.

**Why it works**

- Adjacent swaps between elements of *different* popcount cannot occur, so the *relative order of popcount groups is fixed*.
- After sorting, the sequence of popcounts must be identical to the original sequence for the sort to be realizable.
- If the popcount sequences match, we can arbitrarily reorder elements inside each block, thus achieving the sorted order.

**Complexities**

- Time: `O(n log n)` – dominated by the sorting step (`n ≤ 100` so trivial).
- Space: `O(n)` – to store the sorted copy.

--------------------------------------------------------------------

## 4.  Code

Below are clean, ready‑to‑paste solutions in **Java**, **Python**, and **C++**.

---

### 4.1 Java

```java
import java.util.Arrays;

public class Solution {
    public boolean canSortArray(int[] nums) {
        // 1. Compute popcounts of the original array
        int n = nums.length;
        int[] popOrig = new int[n];
        for (int i = 0; i < n; i++) {
            popOrig[i] = Integer.bitCount(nums[i]);   // 32‑bit popcount
        }

        // 2. Sorted copy
        int[] sorted = nums.clone();
        Arrays.sort(sorted);

        // 3. Compare popcounts
        for (int i = 0; i < n; i++) {
            if (popOrig[i] != Integer.bitCount(sorted[i])) {
                return false;
            }
        }
        return true;
    }
}
```

---

### 4.2 Python

```python
class Solution:
    def canSortArray(self, nums: List[int]) -> bool:
        # original popcounts
        pop_orig = [bin(x).count('1') for x in nums]

        # sorted copy
        sorted_nums = sorted(nums)

        # compare popcounts
        for orig, s in zip(pop_orig, sorted_nums):
            if orig != bin(s).count('1'):
                return False
        return True
```

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool canSortArray(vector<int>& nums) {
        int n = nums.size();
        vector<int> popOrig(n);
        for (int i = 0; i < n; ++i)
            popOrig[i] = __builtin_popcount(nums[i]);   // 32‑bit popcount

        vector<int> sorted = nums;
        sort(sorted.begin(), sorted.end());

        for (int i = 0; i < n; ++i) {
            if (popOrig[i] != __builtin_popcount(sorted[i]))
                return false;
        }
        return true;
    }
};
```

--------------------------------------------------------------------

## 5.  Blog Post – “The Good, The Bad & The Ugly of LeetCode 3011”

> **Title (SEO‑friendly)**  
> *LeetCode 3011 – “Find if Array Can Be Sorted” | Java, Python & C++ Solutions | Interview Preparation Guide*  

> **Meta Description**  
> Master LeetCode 3011 in minutes! Learn the clean algorithm, explore pitfalls, and see working Java, Python, and C++ code. Perfect for technical interview prep.

---

### 5.1 Introduction

When it comes to technical interviews, problems that combine *bit manipulation* with *sorting* are surprisingly common. LeetCode 3011 – “Find if Array Can Be Sorted” – is one such challenge. It tests your understanding of:

- **Bit counting (popcount)**  
- **Adjacency constraints in swaps**  
- **Invariant preservation**  

Below, we dissect the problem, present a succinct solution, and cover the “good”, “bad”, and “ugly” parts that interviewers love to probe.

---

### 5.2 Problem Recap

> **Goal**: Determine whether an array can be sorted into ascending order using only adjacent swaps between numbers that share the same number of set bits.

The key twist is the *popcount constraint*: only numbers with equal popcount can exchange places.

---

### 5.3 “Good” – The Elegant Insight

> **Observation**: The *relative ordering* of numbers with different popcounts never changes.  
> Thus, after any series of allowed swaps, the sequence of popcounts in the array remains identical to the original.

**Consequence**:  
If you take a *normal sorted copy* of the array and compare the popcount at each index with the original array, a match guarantees that the sorted order can be achieved via allowed swaps. If any index mismatches, the sort is impossible.

This reduces the entire problem to a single linear pass over two arrays and one standard sort – O(n log n) time, O(n) space.

---

### 5.4 “Bad” – Common Mistakes

| Mistake | Why It Happens | How to Avoid It |
|---------|----------------|-----------------|
| **Assuming you can swap across popcount boundaries** | Misreading the “adjacent” swap rule. | Remember that *adjacent* does not override the popcount condition. |
| **Using 64‑bit popcount on 32‑bit data** | Mixing integer sizes in languages like Java (`Long.bitCount`) or Python (`bit_length`). | Use the language’s built‑in 32‑bit popcount (`Integer.bitCount`, `__builtin_popcount`). |
| **Ignoring duplicates** | Thinking that duplicates break the algorithm. | Duplicates are fine; popcount comparison still works. |

---

### 5.5 “Ugly” – Edge Cases & Implementation Traps

| Edge Case | Potential Bug | Fix |
|-----------|----------------|-----|
| **Numbers > 2^31−1** | Java’s `Integer.bitCount` only processes 32 bits, dropping high bits. | Convert to `long` and use `Long.bitCount` (or handle via bitwise shifts). |
| **Large Arrays (n=100)** | None; algorithm is linear after sort. | Still fine – no optimization required. |
| **Negative numbers** | Not allowed per constraints, but if present, bitwise logic changes. | Validate input or guard with `Math.abs` before bit counting. |

---

### 5.6 Full Working Code

> **Java**  
> ```java
> public boolean canSortArray(int[] nums) {
>     int n = nums.length;
>     int[] popOrig = new int[n];
>     for (int i = 0; i < n; i++) {
>         popOrig[i] = Integer.bitCount(nums[i]);
>     }
>     int[] sorted = nums.clone();
>     Arrays.sort(sorted);
>     for (int i = 0; i < n; i++) {
>         if (popOrig[i] != Integer.bitCount(sorted[i])) return false;
>     }
>     return true;
> }
> ```

> **Python**  
> ```python
> class Solution:
>     def canSortArray(self, nums: List[int]) -> bool:
>         pop_orig = [bin(x).count('1') for x in nums]
>         sorted_nums = sorted(nums)
>         return all(o == bin(s).count('1') for o, s in zip(pop_orig, sorted_nums))
> ```

> **C++**  
> ```cpp
> bool canSortArray(vector<int>& nums) {
>     vector<int> popOrig(nums.size());
>     for (int i = 0; i < nums.size(); ++i)
>         popOrig[i] = __builtin_popcount(nums[i]);
>     vector<int> sorted = nums;
>     sort(sorted.begin(), sorted.end());
>     for (int i = 0; i < nums.size(); ++i)
>         if (popOrig[i] != __builtin_popcount(sorted[i])) return false;
>     return true;
> }
> ```

---

### 5.7 Why This Matters for Your Interview

- **Time‑boxed**: The solution is short enough to write in 5–10 minutes.  
- **Conceptual depth**: It shows you can *reduce a complex constraint to a simple invariant*.  
- **Language versatility**: Having all three language implementations demonstrates mastery over Java, Python, and C++ – a common interview requirement.

---

### 5.8 Closing Thoughts

LeetCode 3011 is deceptively simple once the popcount invariant is understood. The trick for interview success is to articulate that insight cleanly, flag common pitfalls, and present a robust implementation.

Good luck on your next technical interview – and remember: **bitcount + sorting** → always check *invariant preservation* first!

---

### 5.9 References & Further Reading

- [LeetCode 3011](https://leetcode.com/problems/find-if-array-can-be-sorted/)
- [Bit Counting – popcount](https://en.wikipedia.org/wiki/Population_count)
- [Algorithmic Interview Prep: Sorting with Constraints](https://medium.com/@microsofttech/algorithmic-interview-prep-3b5b3a1d5e5c)

--------------------------------------------------------------------

## 6.  Final Remarks

You now have:

- A single‑paragraph explanation that’s both *conceptual* and *algorithmic*  
- Ready‑to‑run Java, Python, and C++ implementations  
- A deeper understanding of the problem’s nuances  

Use the “Good / Bad / Ugly” framework to practice explaining the solution to an interviewer – that’s the real edge in technical interviews. Happy coding!  

---