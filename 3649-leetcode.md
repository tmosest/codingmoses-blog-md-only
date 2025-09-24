---
title: LeetCode 3649. Number of Perfect Pairs - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 3649 – Number of Perfect Pairs  
**Medium** –  `O(n log n)` time, `O(n)` space  

> **Problem**  
> You’re given an integer array `nums`.  
> A pair of indices `(i, j)` (with `i < j`) is **perfect** iff, with  
> `a = nums[i]` and `b = nums[j]`  
> ```
> min(|a - b|, |a + b|) <= min(|a|, |b|)
> max(|a - b|, |a + b|) >= max(|a|, |b|)
> ```  
> Return the number of distinct perfect pairs.

> **Examples**  
> *`nums = [0,1,2,3]` → `2` perfect pairs*  
> *`nums = [-3,2,-1,4]` → `4` perfect pairs*  
> *`nums = [1,10,100,1000]` → `0` perfect pairs*

---

### 🚀 TL;DR – The Math Behind the Solution  

If we only look at the *absolute* values `x = |a|` and `y = |b|` and sort them such that `x ≤ y`, the two inequalities above collapse to a single range condition:

```
(x + 1) / 2 ≤ y ≤ 2 * x          (1)
```

So a pair is perfect **iff** the larger absolute value lies inside this interval.

Hence the algorithm is:

1. Convert every element to its absolute value.
2. Sort the list.
3. For each element `x` (at index `i`) count how many subsequent elements `y` satisfy (1) using binary search.
4. Sum all counts – that’s the answer.

The trick is that binary search gives us the number of `y` values in the desired range in `O(log n)` time.  
Overall complexity: **O(n log n)**.

---

## 📚 Full Solutions

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.  
All three use the same logic described above.

---

### Java

```java
import java.util.Arrays;

public class Solution {
    public long perfectPairs(int[] nums) {
        int n = nums.length;
        long[] abs = new long[n];
        for (int i = 0; i < n; i++) {
            abs[i] = Math.abs((long) nums[i]);   // long to avoid overflow
        }

        Arrays.sort(abs);

        long answer = 0;
        for (int i = 0; i < n; i++) {
            long x = abs[i];
            long left  = (x + 1) / 2;   // ceil((x+1)/2)
            long right = 2 * x;

            // indices of first >= left and first > right
            int lo = lowerBound(abs, left, i + 1, n);
            int hi = upperBound(abs, right, i + 1, n);

            answer += (long) (hi - lo);
        }
        return answer;
    }

    // first index in [start, end) with arr[idx] >= target
    private int lowerBound(long[] arr, long target, int start, int end) {
        int l = start, r = end;
        while (l < r) {
            int m = (l + r) >>> 1;
            if (arr[m] < target) l = m + 1;
            else r = m;
        }
        return l;
    }

    // first index in [start, end) with arr[idx] > target
    private int upperBound(long[] arr, long target, int start, int end) {
        int l = start, r = end;
        while (l < r) {
            int m = (l + r) >>> 1;
            if (arr[m] <= target) l = m + 1;
            else r = m;
        }
        return l;
    }
}
```

**Why this is good for interviews**

| ✅ Feature | Why it matters |
|------------|----------------|
| `long` everywhere | Prevents accidental overflow when computing `2*x` |
| Dedicated binary‑search helpers | Keeps the core loop concise & self‑explanatory |
| O‑notation in comments | Shows you *why* each step is efficient |

---

### Python

```python
import bisect

class Solution:
    def perfectPairs(self, nums: list[int]) -> int:
        # 1. Absolute values + sorting
        abs_vals = sorted(abs(x) for x in nums)

        n = len(abs_vals)
        ans = 0

        for i, x in enumerate(abs_vals):
            left  = (x + 1) // 2          # ceil((x+1)/2)
            right = 2 * x

            # bisect_left/right work on the whole list,
            # so we slice the view we actually care about.
            lo = bisect.bisect_left(abs_vals, left, i + 1, n)
            hi = bisect.bisect_right(abs_vals, right, i + 1, n)

            ans += hi - lo

        return ans
```

Python’s `bisect` module is a thin wrapper around C’s binary‑search, giving the same `O(n log n)` performance.

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long perfectPairs(vector<int>& nums) {
        int n = nums.size();
        vector<long long> absVals(n);
        for (int i = 0; i < n; ++i)
            absVals[i] = llabs((long long)nums[i]);   // llabs -> long long

        sort(absVals.begin(), absVals.end());

        long long ans = 0;
        for (int i = 0; i < n; ++i) {
            long long x = absVals[i];
            long long left  = (x + 1) / 2;   // ceil
            long long right = 2 * x;

            auto lo = lower_bound(absVals.begin() + i + 1, absVals.end(), left);
            auto hi = upper_bound(absVals.begin() + i + 1, absVals.end(), right);
            ans += hi - lo;
        }
        return ans;
    }
};
```

---

## 📈 Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Convert to abs | `O(n)` | `O(n)` |
| Sort | `O(n log n)` | `O(1)` extra (in‑place) |
| Binary‑search for every `x` | `O(n log n)` | – |
| **Total** | **`O(n log n)`** | **`O(n)`** |

The algorithm scales comfortably up to the maximum constraints (`n = 100,000`, `|nums[i]| ≤ 10^9`).

---

## 🔍 Good, Bad & Ugly – What Interviewers Care About

| Category | What the Interviewer Looks For | How the Solution Excels / Falls Short |
|----------|--------------------------------|--------------------------------------|
| **Good** | *Clarity*, *time‑optimality*, *proof of concept* | The math simplification (1) is elegant; binary search keeps code clean. |
| **Bad** | *Handling of edge‑cases* | Zero values are correctly counted – a subtle corner case. |
| **Ugly** | *Overflow pitfalls* | Multiplying `x` by `2` could overflow a 32‑bit int, so we use `long long / long`. In Java the cast to `long` is essential. |

**Key interview take‑aways**

1. **Always look for a reduction** – turning two inequalities into a single range is a common LeetCode trick.
2. **Binary search on a sorted array** gives you a *count* in logarithmic time – a powerful tool for range queries.
3. **Beware of overflow** – when multiplying up to `1e9`, use 64‑bit types.

---

## 📌 TL;DR for Your Resume / Interview

> *“I solved LeetCode 3649 in Java/Python/C++ with an `O(n log n)` algorithm that transforms the problem into a simple range query on sorted absolute values, leveraging binary search for efficient counting.”*

- Mention `ceil((x+1)/2)` and `2*x` as the critical bounds.
- Highlight binary search (`lower_bound` / `upper_bound` / `bisect_left` / `bisect_right`).
- Emphasize *time* and *space* complexity.

---

## 🤝 Want to Practice More?

| Language | Link to Full Code (GitHub Gist) |
|----------|--------------------------------|
| Java | <https://gist.github.com/yourusername/abc123> |
| Python | <https://gist.github.com/yourusername/def456> |
| C++ | <https://gist.github.com/yourusername/ghi789> |

Feel free to clone, run tests, and tweak the solution to your style.  

Happy coding and good luck smashing that LeetCode interview! 🚀

--- 

> **Keywords** – LeetCode Number of Perfect Pairs, LeetCode 3649, perfect pair algorithm, Java binary search solution, Python bisect solution, C++ lower_bound solution, time complexity, interview prep.