---
title: LeetCode 354. Russian Doll Envelopes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Mastering LeetCode 354 – “Russian Doll Envelopes”

| Language | Code |
|----------|------|
| **Java** | [Java implementation](#java-implementation) |
| **Python** | [Python implementation](#python-implementation) |
| **C++** | [C++ implementation](#c-implementation) |

> **Why read this?**  
> If you’re preparing for a software‑engineering interview, “Russian Doll Envelopes” is a classic hard‑level problem that tests sorting tricks, dynamic programming, and binary search.  
> This post gives you a **complete, production‑ready solution** in Java, Python, and C++, plus a deep dive into the *good, the bad, and the ugly* of solving it.

---

# 🧩 Problem Overview

**LeetCode 354 – Russian Doll Envelopes**

> You’re given a list of envelopes where `envelopes[i] = [wi, hi]`.  
> An envelope can be placed inside another *iff* **both** its width *and* height are strictly smaller.  
> Return the maximum number of envelopes you can nest.

*Example*  
`[[5,4],[6,4],[6,7],[2,3]]` → **3** (`[2,3] → [5,4] → [6,7]`)

**Constraints**

- `1 ≤ envelopes.length ≤ 10⁵`
- `1 ≤ wi, hi ≤ 10⁵`

---

# 💡 The “Good, the Bad, and the Ugly”

| Aspect | What’s Good | What’s Bad | What’s Ugly |
|--------|-------------|------------|-------------|
| **Approach** | Reduce to Longest Increasing Subsequence (LIS) → `O(n log n)` | Naïve DP (`O(n²)`) fails on 10⁵ input | Forgetting the *height‑descending* trick for equal widths → wrong answer |
| **Sorting** | Sort by width asc, height desc | Sorting by both asc can lead to same‑width envelopes being treated incorrectly | Sorting errors cause TLE or WA if not careful |
| **Binary Search** | Reuse `Arrays.binarySearch` or custom lower‑bound | Mis‑handling negative indices leads to out‑of‑bounds | Implementing own binary search incorrectly (mid‑overflow) |
| **Space** | O(n) for DP array | Extra memory for copy arrays | Not freeing memory on large inputs |
| **Readability** | Clear helper for LIS | Mixed responsibilities in a single function | Over‑complicated inline lambdas or anonymous classes |

---

# 📚 The Optimal Solution: `O(n log n)` Approach

1. **Sort Envelopes**  
   - Width **ascending**.  
   - If widths are equal, sort height **descending**.  
   - Why descending?  
     - Envelopes with the same width cannot nest.  
     - By putting the taller one first, we avoid counting it twice in LIS.

2. **Extract Heights**  
   After sorting, we have a sequence of heights where each envelope’s width is already increasing.

3. **Compute LIS on Heights**  
   - Use a classic patience‑sorting method (binary search on a `dp` array).  
   - `dp[i]` stores the smallest possible tail of an increasing subsequence of length `i+1`.  
   - The length of the longest increasing subsequence is the answer.

> **Why LIS?**  
> After sorting, any valid nested sequence must have strictly increasing heights. Thus, the problem reduces to finding the longest increasing subsequence of heights.

---

# 🛠️ Implementation Details

Below you’ll find clean, ready‑to‑copy implementations for **Java, Python, and C++**.  
All three use the same algorithmic core: sort + LIS with binary search.

---

## 📌 Java Implementation

```java
import java.util.Arrays;
import java.util.Comparator;

public class Solution {

    // Helper: Longest Increasing Subsequence using binary search
    private int lengthOfLIS(int[] nums) {
        int[] dp = new int[nums.length];
        int len = 0;

        for (int num : nums) {
            int i = Arrays.binarySearch(dp, 0, len, num);
            if (i < 0) i = -(i + 1);   // insertion point
            dp[i] = num;
            if (i == len) len++;
        }
        return len;
    }

    public int maxEnvelopes(int[][] envelopes) {
        // 1️⃣ Sort by width asc, height desc for equal widths
        Arrays.sort(envelopes, new Comparator<int[]>() {
            @Override
            public int compare(int[] a, int[] b) {
                if (a[0] == b[0]) return b[1] - a[1]; // height descending
                return a[0] - b[0];                   // width ascending
            }
        });

        // 2️⃣ Extract heights
        int[] heights = new int[envelopes.length];
        for (int i = 0; i < envelopes.length; i++) {
            heights[i] = envelopes[i][1];
        }

        // 3️⃣ Run LIS on heights
        return lengthOfLIS(heights);
    }
}
```

**Complexities**

- Time: `O(n log n)` (sorting + LIS)  
- Space: `O(n)` (height array + DP)

---

## 📌 Python Implementation

```python
from bisect import bisect_left
from typing import List

class Solution:
    def maxEnvelopes(self, envelopes: List[List[int]]) -> int:
        # 1️⃣ Sort: width asc, height desc
        envelopes.sort(key=lambda x: (x[0], -x[1]))

        # 2️⃣ Extract heights
        heights = [h for _, h in envelopes]

        # 3️⃣ LIS via patience sorting
        dp = []          # dp[i] = smallest tail of length i+1
        for h in heights:
            idx = bisect_left(dp, h)
            if idx == len(dp):
                dp.append(h)
            else:
                dp[idx] = h
        return len(dp)
```

**Complexities**

- Time: `O(n log n)`  
- Space: `O(n)`

---

## 📌 C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxEnvelopes(vector<vector<int>>& envelopes) {
        // 1️⃣ Sort: width asc, height desc
        sort(envelopes.begin(), envelopes.end(),
             [](const auto& a, const auto& b) {
                 if (a[0] == b[0]) return a[1] > b[1]; // height desc
                 return a[0] < b[0];                   // width asc
             });

        // 2️⃣ Extract heights
        vector<int> heights;
        heights.reserve(envelopes.size());
        for (auto& e : envelopes) heights.push_back(e[1]);

        // 3️⃣ LIS using lower_bound
        vector<int> dp;          // dp[i] = smallest tail of length i+1
        for (int h : heights) {
            auto it = lower_bound(dp.begin(), dp.end(), h);
            if (it == dp.end()) dp.push_back(h);
            else *it = h;
        }
        return static_cast<int>(dp.size());
    }
};
```

**Complexities**

- Time: `O(n log n)`  
- Space: `O(n)`

---

# 🔍 Common Pitfalls & How to Avoid Them

| Pitfall | Why it Happens | Fix |
|---------|----------------|-----|
| **Sorting by height ascending** for equal widths | Results in a chain of envelopes with the same width, which violates the strict “>” rule | Sort height **descending** when widths are equal |
| **Using `Arrays.binarySearch` incorrectly** (Java) | Negative indices need to be flipped | `i = -(i + 1);` |
| **Binary search overflow** (`mid = l + (r-l)/2`) | With `int` ranges up to `10⁵`, overflow isn’t an issue, but for safety use the safe form | Always compute `mid` safely |
| **O(n²) DP on 10⁵ input** | TLE, memory blow | Use LIS with binary search |
| **Off‑by‑one in LIS implementation** | Wrong subsequence length | Double‑check insertion point logic |

---

# 📈 Performance Analysis

| Algorithm | Time | Space | Notes |
|-----------|------|-------|-------|
| **Naïve DP** (`O(n²)`) | ~5 × 10¹⁰ ops (10⁵²) | `O(n)` | Not feasible |
| **Sorting + LIS** (`O(n log n)`) | ~10⁶–10⁷ ops | `O(n)` | Works comfortably under 1‑second time limit |
| **Optimized DP with Binary Search** | Same as above | Same | The only difference is implementation clarity |

**Why `O(n log n)` passes easily**

- Sorting 100 000 elements ≈ 100 000 × log₂(100 000) ≈ 1.7 × 10⁶ comparisons.  
- LIS binary search: another ≈ 1.7 × 10⁶ operations.  
- Total < 4 × 10⁶ primitive ops → ~0.01–0.02 s on modern judges.

---

# 🗣️ Interview‑Ready Talking Points

1. **Explain the “Russian Doll” analogy**: nested sequences ↔ strictly increasing pairs.  
2. **Why sorting is necessary**: reduces 2‑dimensional comparison to 1‑dimensional LIS.  
3. **The height‑descending trick**: handles equal widths correctly.  
4. **Detail the patience sorting method**: `dp` array, binary search, `lower_bound`.  
5. **Complexity**: show big‑O and why it meets the constraints.  
6. **Edge cases**: single envelope, all equal envelopes, unsorted input.  
7. **Possible extensions**: if “≥” instead of “>” is allowed, you’d use `bisect_right`.

> *Tip*: Keep the explanation succinct but highlight the clever trick (height‑descending). That’s what interviewers love.

---

# 🎯 Summary

- **Goal**: Max nested envelopes → LIS on heights after smart sorting.  
- **Algorithm**: `O(n log n)` (sort + patience sorting).  
- **Implementations**: Java, Python, C++ – ready for production or interview coding tests.  
- **Common errors**: wrong sorting direction, naive DP, mishandling binary search.

By mastering this problem, you’ll be equipped to tackle **any problem that can be reduced to LIS**, and you’ll have a great story for your next coding interview.

Happy coding, and good luck on your interview journey! 🚀

--- 

*Feel free to drop questions in the comments or share your own variations of the solution.*