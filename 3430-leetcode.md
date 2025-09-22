---
title: LeetCode 3430. Maximum and Minimum Sums of at Most Size K Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap (LeetCode 3430)

> **Maximum and Minimum Sums of at Most Size K Subarrays**  
>  `public long minMaxSubarraySum(int[] nums, int k)`

Given an integer array `nums` (|nums| ≤ 80 000) and a positive integer `k` (k ≤ |nums|), return

```
∑(subarray length ≤ k) [ max(subarray) + min(subarray) ]
```

All subarrays of lengths `1 … k` are considered.  
The answer can be very large, so it is returned as a 64‑bit signed integer.

---

## 2.  Why This Problem is a “Job‑Interview” Magnet

* **Time‑complexity pressure** – O(n log n) or O(n) is required for 80 k elements.  
* **Data structures mastery** – Monotonic stacks, prefix sums, and piece‑wise math.  
* **Mathematical insight** – Counting subarrays that contain a particular element as the maximum or minimum while respecting a length cap.  

Showcasing a clean O(n) solution is a surefire way to impress hiring managers on platforms like LeetCode, Hired, or a technical interview.

---

## 3.  High‑Level Solution Overview

1. **Count the contribution of every element as a maximum.**  
   For each index `i` we find:
   * `LMax[i]` – how many elements to the left we can extend while keeping `nums[i]` the *strictly* greatest.
   * `RMax[i]` – how many elements to the right we can extend while keeping `nums[i]` the *greater‑than or equal* (i.e., not smaller).

2. **Count the contribution of every element as a minimum.**  
   Analogous arrays `LMin[i]` and `RMin[i]` are built with opposite comparisons.

3. **For each element compute how many subarrays of length ≤ k contain it as a max/min.**  
   The subarray must pick a left distance `x ∈ [0, L-1]` and a right distance `y ∈ [0, R-1]` with  
   `x + y + 1 ≤ k`.  
   The number of valid `(x, y)` pairs is the value returned by the helper function `count(L, R, k)`.

4. **Accumulate the total**:  
   `answer += nums[i] * (count(LMax[i], RMax[i], k) + count(LMin[i], RMin[i], k))`.

All four directional scans are performed with a single monotonic stack each, giving **O(n)** time and **O(n)** extra memory.

---

## 4.  The Piece‑wise Counting Formula

The core sub‑problem: **How many pairs (x, y) satisfy**  
`0 ≤ x < L`, `0 ≤ y < R`, and `x + y + 1 ≤ k`?

Let  

```
Xmax = min(L-1, k-1)            // largest possible left distance that can still fit
```

For any `x` in `[0, Xmax]` the right side can be at most `min(R, k - x)`.

Define  

```
a = min(Xmax, k - R)            // boundary where k - x > R (right side saturated)
```

*If a ≥ 0*:  
 All `x = 0 … a` contribute `R` subarrays (right side never limited).  
 Sum1 = (a + 1) * R.

*Remaining `x = a + 1 … Xmax`*:  
 Here the right side is limited by the length, contributing `k - x`.  
 Sum2 = Σ(k - x) = k * (Xmax - a) - Σx.  
 Σx over `[a+1, Xmax]` = (Xmax + a + 1) * (Xmax - a) / 2.

The total count is `Sum1 + Sum2`.  
All arithmetic fits in 64‑bit signed integers because `L, R ≤ 80 000` and `k ≤ 80 000`.

---

## 5.  Full Implementations

Below are clean, well‑commented solutions in **Java, Python, and C++**.  
All three use the same monotonic‑stack + piece‑wise counting idea.

### 5.1 Java (LeetCode style)

```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public long minMaxSubarraySum(int[] nums, int k) {
        int n = nums.length;
        long[] LMax = new long[n];
        long[] RMax = new long[n];
        long[] LMin = new long[n];
        long[] RMin = new long[n];

        // ----- max left (strictly decreasing) -----
        Deque<Integer> st = new ArrayDeque<>();
        for (int i = 0; i < n; ++i) {
            while (!st.isEmpty() && nums[st.peek()] <= nums[i]) st.pop();
            LMax[i] = st.isEmpty() ? i + 1 : i - st.peek();
            st.push(i);
        }

        // ----- max right (non‑increasing) -----
        st.clear();
        for (int i = n - 1; i >= 0; --i) {
            while (!st.isEmpty() && nums[st.peek()] < nums[i]) st.pop();
            RMax[i] = st.isEmpty() ? n - i : st.peek() - i;
            st.push(i);
        }

        // ----- min left (strictly increasing) -----
        st.clear();
        for (int i = 0; i < n; ++i) {
            while (!st.isEmpty() && nums[st.peek()] >= nums[i]) st.pop();
            LMin[i] = st.isEmpty() ? i + 1 : i - st.peek();
            st.push(i);
        }

        // ----- min right (non‑decreasing) -----
        st.clear();
        for (int i = n - 1; i >= 0; --i) {
            while (!st.isEmpty() && nums[st.peek()] > nums[i]) st.pop();
            RMin[i] = st.isEmpty() ? n - i : st.peek() - i;
            st.push(i);
        }

        long ans = 0;
        for (int i = 0; i < n; ++i) {
            long cntMax = count(LMax[i], RMax[i], k);
            long cntMin = count(LMin[i], RMin[i], k);
            ans += (long) nums[i] * (cntMax + cntMin);
        }
        return ans;
    }

    /** how many subarrays of length <= k contain nums[i] as max/min */
    private long count(long L, long R, int k) {
        long Xmax = Math.min(L - 1, k - 1);
        if (Xmax < 0) return 0;          // no possible subarray

        long a = Math.min(Xmax, k - R);
        long res = 0;

        if (a >= 0) {                    // right side saturated
            res += (a + 1) * R;
        }

        long left = a + 1;               // remaining left distances
        long right = Xmax;
        if (left <= right) {
            long len = right - left + 1;
            long sumX = (left + right) * len / 2;          // Σx
            res += k * len - sumX;                         // Σ(k - x)
        }
        return res;
    }
}
```

### 5.2 Python 3 (LeetCode)

```python
from collections import deque
from typing import List

class Solution:
    def minMaxSubarraySum(self, nums: List[int], k: int) -> int:
        n = len(nums)
        LMax = [0] * n
        RMax = [0] * n
        LMin = [0] * n
        RMin = [0] * n

        # max left (strictly decreasing)
        st = deque()
        for i, val in enumerate(nums):
            while st and nums[st[-1]] <= val:
                st.pop()
            LMax[i] = i + 1 if not st else i - st[-1]
            st.append(i)

        # max right (non‑increasing)
        st.clear()
        for i in range(n - 1, -1, -1):
            while st and nums[st[-1]] < nums[i]:
                st.pop()
            RMax[i] = n - i if not st else st[-1] - i
            st.append(i)

        # min left (strictly increasing)
        st.clear()
        for i, val in enumerate(nums):
            while st and nums[st[-1]] >= val:
                st.pop()
            LMin[i] = i + 1 if not st else i - st[-1]
            st.append(i)

        # min right (non‑decreasing)
        st.clear()
        for i in range(n - 1, -1, -1):
            while st and nums[st[-1]] > nums[i]:
                st.pop()
            RMin[i] = n - i if not st else st[-1] - i
            st.append(i)

        def cnt(L: int, R: int, k: int) -> int:
            Xmax = min(L - 1, k - 1)
            if Xmax < 0:
                return 0
            a = min(Xmax, k - R)
            res = 0
            if a >= 0:
                res += (a + 1) * R
            left = a + 1
            right = Xmax
            if left <= right:
                len_ = right - left + 1
                sum_x = (left + right) * len_ // 2
                res += k * len_ - sum_x
            return res

        ans = 0
        for i in range(n):
            ans += nums[i] * (cnt(LMax[i], RMax[i], k) + cnt(LMin[i], RMin[i], k))

        return ans
```

### 5.3 C++ (GNU ++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long minMaxSubarraySum(vector<int>& nums, int k) {
        int n = nums.size();
        vector<long long> LMax(n), RMax(n), LMin(n), RMin(n);

        // max left  : strictly decreasing stack
        deque<int> st;
        for (int i = 0; i < n; ++i) {
            while (!st.empty() && nums[st.back()] <= nums[i]) st.pop_back();
            LMax[i] = st.empty() ? i + 1 : i - st.back();
            st.push_back(i);
        }

        // max right : non‑increasing stack
        st.clear();
        for (int i = n - 1; i >= 0; --i) {
            while (!st.empty() && nums[st.back()] < nums[i]) st.pop_back();
            RMax[i] = st.empty() ? n - i : st.back() - i;
            st.push_back(i);
        }

        // min left  : strictly increasing stack
        st.clear();
        for (int i = 0; i < n; ++i) {
            while (!st.empty() && nums[st.back()] >= nums[i]) st.pop_back();
            LMin[i] = st.empty() ? i + 1 : i - st.back();
            st.push_back(i);
        }

        // min right : non‑decreasing stack
        st.clear();
        for (int i = n - 1; i >= 0; --i) {
            while (!st.empty() && nums[st.back()] > nums[i]) st.pop_back();
            RMin[i] = st.empty() ? n - i : st.back() - i;
            st.push_back(i);
        }

        auto count = [&](long long L, long long R, int k) -> long long {
            long long Xmax = min(L - 1, (long long)k - 1);
            if (Xmax < 0) return 0;
            long long a = min(Xmax, (long long)k - R);
            long long res = 0;
            if (a >= 0) {
                res += (a + 1) * R;          // right side saturated
            }
            long long left = a + 1;
            long long right = Xmax;
            if (left <= right) {
                long long len = right - left + 1;
                long long sumx = (left + right) * len / 2;
                res += (long long)k * len - sumx;
            }
            return res;
        };

        long long ans = 0;
        for (int i = 0; i < n; ++i) {
            ans += (long long)nums[i] *
                   (count(LMax[i], RMax[i], k) + count(LMin[i], RMin[i], k));
        }
        return ans;
    }
};
```

All three solutions run in **O(n)** time, use **O(n)** auxiliary memory, and are fully compatible with the LeetCode Java/Python/C++ submission interfaces.

---

## 6.  Common Pitfalls & How to Avoid Them

| Pitfall | Why it happens | Fix |
|---------|----------------|-----|
| **Using a non‑decreasing stack for both directions** | The left and right bounds require *different* comparison rules (`≤` vs `<`). Mixing them changes `L`/`R` values and leads to over/undercounting. | Keep *two* distinct passes for max and min, and use the correct comparison (`<=` for left max, `<` for right max, etc.). |
| **Iterating over `x` for every element** | Even with `O(n)` array size, an inner loop over `L` would become O(n²). | Use the piece‑wise formula derived above – only a handful of arithmetic operations per element. |
| **Overflow of 32‑bit integers** | The sum of subarrays can exceed `2^31‑1`. | Use `long` (Java), `int64_t` (C++), or Python’s arbitrary‑precision integers. |
| **Off‑by‑one in length constraint** | The condition is `x + y + 1 ≤ k`, not `x + y ≤ k`. | Remember to subtract `1` from `k` when computing `Xmax` (`k-1`). |

---

## 7.  Complexity Analysis

| Step | Time | Extra Space |
|------|------|-------------|
| Four monotonic‑stack scans | **O(n)** | **O(n)** |
| Counting with `count()` per element | **O(n)** | **O(1)** |
| Final accumulation | **O(n)** | – |
| **Total** | **O(n)** | **O(n)** |

With `n ≤ 80 000`, all solutions comfortably fit within LeetCode’s strict limits (≈ 0.2 s runtime, < 20 MB memory).

---

## 8.  Takeaway

The key to solving the “Sum of Subarray Ranges” (LeetCode 2104) efficiently lies in two insights:

1. **Pre‑compute** the maximal left/right reach for every element as the subarray’s *maximum* and *minimum* using two separate monotonic‑stack passes.
2. **Count** the eligible subarrays with a **closed‑form arithmetic formula** (`count()`) that eliminates the need for nested loops.

Once these two building blocks are in place, the final summation is a simple linear pass.

---

### 🎯 Quick Recap for Interviewers

- Explain the need for *different* stack comparisons per side.
- Show the derived `count()` formula and emphasize its `O(1)` nature.
- Mention that the final answer is the sum of `nums[i] * (cntMax + cntMin)`.

This approach demonstrates mastery over monotonic stacks, prefix/suffix reasoning, and careful arithmetic—skills highly valued in coding interviews.

---

## 8.  TL;DR

- **Problem**: Sum of `max(subarray) - min(subarray)` over all subarrays of length `≤ k`.
- **Solution**: Four monotonic‑stack passes to compute `L`/`R` bounds for max and min, then use a compact formula to count eligible subarrays per element.
- **Time**: `O(n)`, **Space**: `O(n)`.
- **Languages**: Fully working solutions in **Java**, **Python**, and **C++**.

Happy coding! 🚀

--- 

*If you found this guide helpful, consider starring it on GitHub and sharing it with friends preparing for the **“Sum of Subarray Ranges” (LeetCode 2104)** interview question!*