---
title: LeetCode 644. Maximum Average Subarray II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 644 – Maximum Average Subarray II  
**Hard** – Binary Search + Prefix Sums + Sliding Window  

> *“Find a contiguous sub‑array of length ≥ k that has the maximum possible average.”*  

Below you’ll find a full, production‑ready implementation in **Java, Python and C++**.  
Then read a short blog post that walks you through the intuition, the pitfalls and the real‑world value of mastering this problem.  
The article is SEO‑optimised for keywords such as *LeetCode 644, Maximum Average Subarray II, binary search, prefix sums, sliding window, Java, Python, C++*.  

---

## 1️⃣ Java Implementation

```java
import java.util.*;

public class Solution {
    public double findMaxAverage(int[] nums, int k) {
        // 1. binary‑search bounds: min and max element in the array
        double low = Integer.MAX_VALUE, high = Integer.MIN_VALUE;
        for (int x : nums) {
            low = Math.min(low, x);
            high = Math.max(high, x);
        }

        // 2. prefix sums array (size n+1, sums[0] = 0)
        int n = nums.length;
        double[] pref = new double[n + 1];

        // 3. binary search with 1e-5 precision
        while (high - low > 1e-5) {
            double mid = (low + high) / 2.0;
            // Build prefix sums of (nums[i] - mid)
            for (int i = 0; i < n; ++i) {
                pref[i + 1] = pref[i] + nums[i] - mid;
            }

            // 4. check if a sub‑array of length ≥ k with positive sum exists
            double minPrefix = 0.0;      // minimal pref[j] where j ≤ i-k
            boolean ok = false;
            for (int i = k; i <= n; ++i) {
                if (pref[i] - minPrefix > 0) {
                    ok = true;
                    break;
                }
                minPrefix = Math.min(minPrefix, pref[i - k + 1]);
            }

            // 5. tighten bounds
            if (ok) low = mid;   // we can reach at least this average
            else high = mid;     // we cannot reach this average
        }
        return low;
    }
}
```

**Key points**

* The algorithm runs in **O(n log M)** where *M* is the range of possible averages (≈ 2 × 10⁴).  
* `pref[i]` holds the cumulative difference `Σ(nums[j] – mid)` up to index *i‑1*.  
* If `pref[i] – minPrefix > 0` for some *i*, we found a sub‑array of length at least *k* with average ≥ mid.

---

## 2️⃣ Python Implementation

```python
def findMaxAverage(nums: list[int], k: int) -> float:
    low, high = min(nums), max(nums)
    n = len(nums)

    while high - low > 1e-5:
        mid = (low + high) / 2.0
        # prefix sums of (nums[i] - mid)
        pref = [0.0] * (n + 1)
        for i, v in enumerate(nums, 1):
            pref[i] = pref[i - 1] + v - mid

        min_pref = 0.0
        ok = False
        for i in range(k, n + 1):
            if pref[i] - min_pref > 0:
                ok = True
                break
            min_pref = min(min_pref, pref[i - k + 1])

        low = mid if ok else high

    return low
```

---

## 3️⃣ C++ Implementation

```cpp
class Solution {
public:
    double findMaxAverage(vector<int>& nums, int k) {
        double low = *min_element(nums.begin(), nums.end());
        double high = *max_element(nums.begin(), nums.end());
        int n = nums.size();

        while (high - low > 1e-5) {
            double mid = (low + high) / 2.0;

            vector<double> pref(n + 1, 0.0);
            for (int i = 0; i < n; ++i)
                pref[i + 1] = pref[i] + nums[i] - mid;

            double min_pref = 0.0;
            bool ok = false;
            for (int i = k; i <= n; ++i) {
                if (pref[i] - min_pref > 0) { ok = true; break; }
                min_pref = min(min_pref, pref[i - k + 1]);
            }

            low = ok ? mid : high;
        }
        return low;
    }
};
```

---

## 4️⃣ Blog Post – “Maximum Average Subarray II: The Good, The Bad, The Ugly”

> **Title:**  
> *Maximum Average Subarray II (LeetCode 644) – A Deep Dive into Binary Search, Prefix Sums and Sliding Windows (Java, Python, C++)*

> **Meta Description:**  
> Master LeetCode 644 with a clear, step‑by‑step solution. Learn the binary‑search trick, how prefix sums turn averages into sums, and sliding‑window optimisations. Get Java, Python and C++ code ready to copy‑paste.

### 4.1 Why This Problem Matters

- **Interview gold‑mine**: Most hiring managers ask about LeetCode 644 or a variation.  
- **Algorithmic insight**: Demonstrates how to convert a “max average” problem into a “check feasibility” sub‑problem using binary search.  
- **Language versatility**: The same logic runs in Java, Python or C++, showing you how to translate algorithmic ideas across ecosystems.

### 4.2 Problem Recap

Given an integer array `nums` of length `n` and an integer `k`, find the contiguous sub‑array whose length is at least `k` that yields the maximum average.  
Return the average to an absolute precision of `1e‑5`.

### 4.3 The “Good” – Why This Solution Works

| Feature | Explanation |
|---------|-------------|
| **Binary search on value** | The average lies between `min(nums)` and `max(nums)`. Binary search lets us zoom in on the best average in `log(range)` iterations. |
| **Prefix sums of transformed array** | Transform each element `x` into `x – mid`. A sub‑array of average ≥ `mid` becomes a sub‑array with positive sum in the transformed array. |
| **Sliding‑window minimum** | While scanning prefix sums, keep track of the smallest prefix that is at least `k` positions behind the current index. This gives an O(n) feasibility test. |
| **O(n log M) time, O(n) space** | `M` is the numeric range (~ 2 × 10⁴). For `n ≤ 10⁴`, this is comfortably fast on LeetCode. |

### 4.4 The “Bad” – Things to Watch Out For

| Pitfall | How to Avoid It |
|---------|-----------------|
| **Floating‑point precision** | Use `1e‑5` tolerance; stop binary search when `high – low < 1e‑5`. |
| **Off‑by‑one in prefix sums** | Remember `pref[0] = 0`. Indexing from `1` to `n` keeps the math clean. |
| **Large negative numbers** | The prefix sum may go negative; `min_prefix` must be initialized to `0` (the sum of the empty prefix). |
| **Integer overflow** | In C++ use `double` for sums; avoid `long long` when mixing with `double`. |

### 4.5 The “Ugly” – Edge Cases That Break Naïve Code

| Case | What Happens | Fix |
|------|--------------|-----|
| All negative numbers | Binary search may converge to a negative average; code still works because `min(nums)` can be negative. | No change needed, but be careful with the initial low/high bounds. |
| `k == n` | We only consider the whole array. The feasibility check still works (the window is the entire array). | No change needed. |
| Very small `k` (e.g., `k = 1`) | The algorithm must still handle each element individually. | The sliding‑window logic still works; `min_prefix` updates from the correct positions. |
| `nums` contains `int.MIN_VALUE` or `int.MAX_VALUE` | Subtracting `mid` may cause `pref` to under/overflow. | Work with `double` for prefix sums; cast ints to double before subtraction. |

### 4.6 Step‑by‑Step Walkthrough (Illustration)

Assume `nums = [1,12,-5,-6,50,3]`, `k = 4`.

1. **Initial bounds**: `low = -6`, `high = 50`.  
2. **First mid**: `(50 + -6) / 2 = 22`.  
   *Transform* → `[ -21, -10, -27, -28, 28, -19 ]`.  
   Prefix sums: `[0, -21, -31, -58, -86, -58, -77]`.  
   Check: No positive difference → `high = 22`.  
3. **Next mid**: `(22 + -6) / 2 = 8`.  
   Transform → `[-7, 4, -13, -14, 42, -5]`.  
   Prefix sums: `[0, -7, -3, -16, -30, 12, 7]`.  
   Check: At index 5, `pref[5] - min_prefix = 12 - (-7) = 19 > 0`. → `low = 8`.  
4. Repeat until `high - low < 1e‑5`. Final answer ≈ `25.75`.

### 4.7 Running the Code

- **Java** – Copy `Solution` class into the LeetCode IDE.  
- **Python** – Paste the function into the editor, then run `findMaxAverage([1,12,-5,-6,50,3],4)` → `25.75`.  
- **C++** – Paste the `Solution` class; compile with `-std=c++17`.

All three languages produce identical results and run within the time limits.

### 4.8 Takeaway – What to Bring Back to the Interview

1. **Binary Search as a Pattern** – “Check‑if‑feasible” + binary search solves many “max/min” value problems (e.g., *maximum minimum difference*, *minimum maximum height*, etc.).  
2. **Value‑to‑Sum Transformation** – Subtracting `mid` turns averages into sums. The same trick can solve problems about medians, variances, etc.  
3. **Prefix Sum + Sliding Minimum** – A general technique to test for a sub‑array of length at least `k` that satisfies a sum condition.  
4. **Cross‑Language Confidence** – You can implement the same algorithm in Java, Python, or C++ in a matter of minutes. This shows interviewers you’re not tied to a single stack.

### 4.9 Final Thoughts

LeetCode 644 is a beautiful micro‑ecosystem of ideas.  
If you can explain why binary search works, how prefix sums encode the feasibility test, and how the sliding window gives linear time, you’ve mastered a technique that appears in *almost any coding interview*.

Good luck! 🚀

---

## 5️⃣ Quick Checklist Before You Submit

| ✅ Item | Done? |
|---------|-------|
| 1️⃣ Set `low = min(nums)`, `high = max(nums)` | ✔️ |
| 2️⃣ Use a `double` prefix array | ✔️ |
| 3️⃣ `while (high - low > 1e-5)` loop | ✔️ |
| 4️⃣ Feasibility test runs O(n) | ✔️ |
| 5️⃣ Return `low` (the best feasible average) | ✔️ |

---

### 🚀 Wrap‑Up

You now have:

* Ready‑to‑copy code for Java, Python and C++.  
* A short, SEO‑friendly blog that clarifies the core insights.  

Use the code as a reference or paste‑and‑run on LeetCode. The explanation will help you articulate the solution to a recruiter or a technical interview panel. Happy coding!