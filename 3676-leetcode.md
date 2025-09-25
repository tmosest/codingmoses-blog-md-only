---
title: LeetCode 3676. Count Bowl Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3676 – Count Bowl Subarrays  
**Full solutions in Java, Python, and C++ + a SEO‑ready interview blog**

> *“If you understand the *why* behind a solution, you’ll remember it forever.”* – LeetCode community

---

## Table of Contents
| # | Section | Description |
|---|---------|-------------|
| 1 | Problem Overview | Quick recap of the challenge |
| 2 | Brute‑Force Baseline | Why it fails on large inputs |
| 3 | Monotonic Stack Insight | Core idea that drives the O(n) solution |
| 4 | Implementation – Java | Code, commentary, edge‑case handling |
| 5 | Implementation – Python | Fast, concise, ready for LeetCode |
| 6 | Implementation – C++ | STL‑friendly version |
| 7 | Complexity Analysis | Time & space |
| 8 | The Good, the Bad, the Ugly | What you learn, pitfalls, and interview‑ready pitfalls |
| 9 | SEO‑Friendly Takeaway | Keywords, meta tags, call‑to‑action |

---

## 1. Problem Overview

> **Count Bowl Subarrays**  
> Given an integer array `nums` of distinct elements, count all subarrays of length ≥ 3 that satisfy  
> **min(nums[l], nums[r]) > max(nums[l+1…r‑1])**.  
> In other words, the two ends form a “bowl” and every inner element is strictly smaller than both ends.

### Sample

```text
Input : nums = [2,5,3,1,4]
Output: 2   // [3,1,4] and [5,3,1,4]
```

### Constraints

| Constraint | Value |
|------------|-------|
| 3 ≤ nums.length ≤ 10⁵ | |
| 1 ≤ nums[i] ≤ 10⁹ | |
| All elements are distinct | |

---

## 2. Brute‑Force Baseline

A naive triple‑nested loop enumerating all subarrays would run in **O(n³)** and is impossible for `n = 10⁵`.  
Even a two‑nested loop with a running maximum inside (O(n²)) will time‑out on the worst case.

> **Why it fails**  
> *The inner maximum calculation costs O(length)* – repeated many times.  
> With 10⁵ elements, that’s ~10¹⁰ operations.

---

## 3. Monotonic Stack Insight – The Core Idea

The condition can be re‑phrased:

* For a subarray `[l … r]`, the **first** element on the left that is **greater** than `nums[r]` must be **inside** the subarray, and similarly for the right.  
* This translates to *finding, for each index, the next greater element (NGE) to the right* and *the previous greater element (PGE) to the left*.

If the distance between an index and its NGE (or PGE) is at least 2 (i.e., the subarray has length ≥ 3), then that pair contributes **one** bowl.

**Why a monotonic stack works**

* While scanning from left to right, keep a stack of indices whose values are **decreasing**.  
* When you encounter an element that is **larger** than the stack’s top, the current index is the NGE for all popped indices.  
* Similarly, scanning from right to left gives all PGE values.

Because each index is pushed and popped at most once, the overall complexity is linear.

---

## 4. Implementation – Java

```java
import java.util.*;

class Solution {
    public long bowlSubarrays(int[] nums) {
        int n = nums.length;
        long res = 0;
        if (n < 3) return 0;

        // ----- next greater (to the right) -----
        int[] next = new int[n];
        Arrays.fill(next, -1);
        Deque<Integer> stack = new ArrayDeque<>();

        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && nums[stack.peek()] < nums[i]) {
                next[stack.pop()] = i;          // i is the NGE for popped index
            }
            stack.push(i);
        }

        // Count bowls where the left end is the NGE of some index
        for (int l = 0; l < n; l++) {
            int r = next[l];
            if (r != -1 && r - l + 1 >= 3) res++;
        }

        // ----- previous greater (to the left) -----
        int[] prev = new int[n];
        Arrays.fill(prev, -1);
        stack.clear();

        for (int i = n - 1; i >= 0; i--) {
            while (!stack.isEmpty() && nums[stack.peek()] < nums[i]) {
                prev[stack.pop()] = i;          // i is the PGE for popped index
            }
            stack.push(i);
        }

        // Count bowls where the right end is the PGE of some index
        for (int r = 0; r < n; r++) {
            int l = prev[r];
            if (l != -1 && r - l + 1 >= 3) res++;
        }

        return res;
    }
}
```

### Highlights

* `Deque<Integer>` for an efficient stack (amortised O(1) ops).  
* Two separate scans: one left‑to‑right (next greater), one right‑to‑left (previous greater).  
* 64‑bit `long` is used because the answer can exceed `int`.

---

## 5. Implementation – Python

```python
class Solution:
    def bowlSubarrays(self, nums: List[int]) -> int:
        n = len(nums)
        if n < 3: return 0

        # ----- next greater -----
        next_greater = [-1] * n
        stack = []
        for i, val in enumerate(nums):
            while stack and nums[stack[-1]] < val:
                idx = stack.pop()
                next_greater[idx] = i
            stack.append(i)

        res = 0
        for l in range(n):
            r = next_greater[l]
            if r != -1 and r - l + 1 >= 3:
                res += 1

        # ----- previous greater -----
        prev_greater = [-1] * n
        stack.clear()
        for i in range(n - 1, -1, -1):
            while stack and nums[stack[-1]] < nums[i]:
                idx = stack.pop()
                prev_greater[idx] = i
            stack.append(i)

        for r in range(n):
            l = prev_greater[r]
            if l != -1 and r - l + 1 >= 3:
                res += 1

        return res
```

*Uses `list` as a stack (`append`/`pop`).  
`int` in Python 3 is unbounded, so no worries about overflow.*

---

## 6. Implementation – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long bowlSubarrays(vector<int>& nums) {
        int n = nums.size();
        if (n < 3) return 0;
        long long res = 0;

        // ----- next greater (right) -----
        vector<int> next(n, -1);
        vector<int> st;
        for (int i = 0; i < n; ++i) {
            while (!st.empty() && nums[st.back()] < nums[i]) {
                next[st.back()] = i;   // i is NGE for st.back()
                st.pop_back();
            }
            st.push_back(i);
        }

        for (int l = 0; l < n; ++l) {
            int r = next[l];
            if (r != -1 && r - l + 1 >= 3) res++;
        }

        // ----- previous greater (left) -----
        vector<int> prev(n, -1);
        st.clear();
        for (int i = n - 1; i >= 0; --i) {
            while (!st.empty() && nums[st.back()] < nums[i]) {
                prev[st.back()] = i;   // i is PGE for st.back()
                st.pop_back();
            }
            st.push_back(i);
        }

        for (int r = 0; r < n; ++r) {
            int l = prev[r];
            if (l != -1 && r - l + 1 >= 3) res++;
        }

        return res;
    }
};
```

*`vector<int>` + `vector<int>` for stacks (LIFO) gives the same amortised O(1) per op.*

---

## 5. Complexity Analysis

| Aspect | Formula | Result |
|--------|---------|--------|
| **Time** | Each index is pushed & popped once in both scans | **O(n)** |
| **Space** | Two auxiliary arrays (`next`, `prev`) + stack | **O(n)** |
| **Answer type** | `long` / `long long` (≥ 2 × (n‑1)) | |

*The stack itself is at most `n` in size, so the extra memory is linear.*

---

## 6. The Good, the Bad, the Ugly – What the Interviewer Wants

| Aspect | What you’ll impress | Pitfalls to avoid |
|--------|---------------------|-------------------|
| **Good** | • *Monotonic stack* is a classic interview pattern – shows knowledge of data‑structures.  <br>• Code is clean, runs fast for `10⁵` elements. | – |
| **Bad** | Misinterpreting “next greater” vs “previous greater” can lead to double counting or missing bowls.  <br>Wrong inequality (`<=` vs `>`) changes the logic entirely. | • Off‑by‑one: length ≥ 3 → distance ≥ 2.  <br>• Forget to `continue` when `n < 3`. |
| **Ugly** | A “one‑liner” brute force may get accepted on the LeetCode test suite but will fail in a real interview where the judge runs 2‑second TL.  <br>Also, people often forget the *distinct* property, causing unnecessary `max`/`min` recalculations. | • Over‑optimizing: adding unnecessary `if` checks inside loops can hurt speed.  <br>• Using recursion with a stack → stack‑overflow on 10⁵. |

### Interview Trick: “Why do we need two scans?”

*Because each end of a bowl can be the NGE of an inner index or the PGE of an outer index.  
Counting both directions ensures we count every valid pair exactly once.*

---

## 7. SEO‑Friendly Takeaway

### Meta‑Title  
> “Count Bowl Subarrays – Efficient Monotonic Stack Solution (Java, Python, C++)”

### Meta‑Description  
> Master LeetCode 3676, “Count Bowl Subarrays”, with a fast O(n) monotonic‑stack solution.  
> Read our interview‑ready guide, compare Java/Python/C++ implementations, and learn the key patterns that make you shine in coding interviews.

### Focus Keywords  
`count bowl subarrays`, `leetcode 3676`, `monotonic stack`, `job interview coding`, `Java solution`, `Python solution`, `C++ solution`, `array subarray counting`, `distinct elements`, `linear time algorithm`, `interview pattern`.

### Header Tags (H1–H3)  
* LeetCode – Count Bowl Subarrays (H1)  
* Problem Recap (H2)  
* Brute‑Force vs O(n) (H3)  
* Monotonic Stack (H3)  
* Java, Python, C++ Code (H3)  
* Good / Bad / Ugly (H3)  

### Call‑to‑Action  
> **Want to ace your next coding interview?**  
> Download our 7‑day *Coding Interview Prep* email course, get daily LeetCode walkthroughs, and start landing top‑tech roles.  
> 👉 [Sign Up Now](https://example.com/prepare)

---

## 8. Wrap‑Up

* **Why this matters** – The ability to turn a “bowl” condition into a pair of greater‑element lookups is a powerful abstraction that shows you can *think outside the box* and *apply classic DS patterns*.  
* **Interview Tip** – When the LeetCode description feels oddly worded, translate it into a relationship that can be answered with a stack or queue.  
* **Beyond LeetCode** – Monotonic stacks appear in stock‑span problems, skyline queries, and even in computational geometry. Mastering it gives you a tool for many real‑world coding challenges.

Happy coding, and good luck landing that dream job! 🚀