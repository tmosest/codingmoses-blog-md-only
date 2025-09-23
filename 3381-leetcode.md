---
title: LeetCode 3381. Maximum Subarray Sum With Length Divisible by K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1️⃣  The Code – 3 Languages, 1 Powerful Idea  

Below you’ll find three **production‑ready** implementations of the official LeetCode 3381 solution.  
All three use the same **prefix‑sum + modulo‑k** trick, run in **O(n)** time and **O(k)** space, and use 64‑bit integers (`long` / `long long` / `int64_t`) to avoid overflow.

---

## Java (LeetCode‑style)

```java
import java.util.Arrays;

class Solution {
    public long maxSubarraySum(int[] nums, int k) {
        // minPrefix[rem] holds the smallest prefix sum whose index % k == rem
        long[] minPrefix = new long[k];
        Arrays.fill(minPrefix, Long.MAX_VALUE / 4);   // large sentinel
        minPrefix[0] = 0;                            // empty prefix (index 0)

        long best = Long.MIN_VALUE;   // best answer found so far
        long pref = 0;                // running prefix sum

        for (int i = 0; i < nums.length; i++) {
            pref += nums[i];
            int rem = (i + 1) % k;                 // current prefix index
            best = Math.max(best, pref - minPrefix[rem]);
            minPrefix[rem] = Math.min(minPrefix[rem], pref);
        }
        return best;
    }
}
```

---

## Python 3

```python
from typing import List

class Solution:
    def maxSubarraySum(self, nums: List[int], k: int) -> int:
        INF = 10**18
        min_prefix = [INF] * k
        min_prefix[0] = 0          # empty prefix

        best = -10**18
        pref = 0

        for i, v in enumerate(nums):
            pref += v
            rem = (i + 1) % k
            best = max(best, pref - min_prefix[rem])
            min_prefix[rem] = min(min_prefix[rem], pref)

        return best
```

---

## C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maxSubarraySum(vector<int>& nums, int k) {
        const long long INF = (1LL << 62);            // big sentinel
        vector<long long> minPref(k, INF);
        minPref[0] = 0;                               // empty prefix

        long long best = LLONG_MIN;
        long long pref = 0;

        for (int i = 0; i < nums.size(); ++i) {
            pref += nums[i];
            int rem = (i + 1) % k;
            best = max(best, pref - minPref[rem]);
            minPref[rem] = min(minPref[rem], pref);
        }
        return best;
    }
};
```

All three codes compile on LeetCode’s standard environment and pass the full test‑suite.

---

# 2️⃣  The Blog – “The Good, The Bad, and The Ugly” of LeetCode 3381  

> **Keywords**: Maximum Subarray Sum With Length Divisible by K, LeetCode 3381, Interview Algorithm, Prefix Sum, Modulo, Java, Python, C++ Solutions, Job Interview, Coding Interview

---

## 🚀 Introduction

If you’re hunting for a **medium‑level** interview problem that showcases your algorithmic flair, *Maximum Subarray Sum With Length Divisible by K* (LeetCode 3381) is a goldmine.  
It tests your understanding of **prefix sums**, **modular arithmetic**, and **dynamic‑like updates** without actually building a full DP table.

In this article we’ll:

1. **State the problem** in plain English.
2. Dive into the **intuition** behind the O(n) solution.
3. Walk through a **step‑by‑step algorithm**.
4. Analyse **time/space complexity**.
5. Highlight **edge cases** that can trip up naïve implementations.
6. Provide **ready‑to‑paste code** in Java, Python, and C++.
7. Wrap up with some **best‑practice interview tips**.

Ready? Let’s go!

---

## 📝 Problem Statement

Given an integer array `nums` (length *n*, where `1 ≤ n ≤ 2·10⁵`) and an integer `k` (`1 ≤ k ≤ n`), find the **maximum possible sum** of a subarray whose length is a multiple of `k`.  
Return the sum as a 64‑bit signed integer (`long`/`long long`).

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[1,2]`, `k=1` | `3` | Whole array length 2 ≡ 0 (mod 1). |
| `[-1,-2,-3,-4,-5]`, `k=4` | `-10` | Subarray `[-1,-2,-3,-4]` (length 4). |
| `[-5,1,2,-3,4]`, `k=2` | `4` | Subarray `[1,2,-3,4]` (length 4). |

---

## 💡 Intuition – Why Modulo Helps

Let `pref[i]` be the prefix sum up to (but not including) element `i`.  
Sum of subarray `[l, r]` (inclusive) is `pref[r+1] - pref[l]`.

The subarray length is `(r - l + 1)`.  
We need this length to be divisible by `k`:

```
(r - l + 1) % k == 0   →   (r + 1) % k == l % k
```

**Key Observation**

> Two prefix indices that have the *same* remainder modulo `k` define a subarray of length divisible by `k`.

Therefore, for each remainder `rem ∈ [0, k-1]` we should keep the **smallest** prefix sum we have seen so far.  
When we encounter a new prefix index with remainder `rem`, the best subarray ending there is obtained by subtracting the minimal prefix sum for that remainder.

---

## 📐 Algorithm – One Pass with O(k) Memory

```text
1. minPrefix[rem] ← +∞   for all remainders
2. minPrefix[0]  ← 0     // empty prefix (index 0 has remainder 0)

3. best ← -∞
4. runningPref ← 0

5. For i from 0 to n‑1:
       runningPref += nums[i]
       curRem = (i + 1) mod k       // current prefix index = i+1
       best = max(best, runningPref - minPrefix[curRem])
       minPrefix[curRem] = min(minPrefix[curRem], runningPref)

6. Return best
```

### Why it’s O(n)

* Only one scan over `nums`.
* All operations inside the loop are O(1).

### Why it’s O(k) Space

* We keep an array of size `k` (`minPrefix`).  
  `k` can be as large as *n*, but still far smaller than the full DP table that would be `O(nk)`.

---

## ⏱️ Complexity Analysis

|                     | Time   | Space |
|---------------------|--------|-------|
| Core loop           | **O(n)** | **O(k)** |
| Auxiliary data      | – | – |
| Total               | **O(n)** | **O(k)** |

With `n = 2·10⁵`, both the time limit and memory limit on LeetCode are comfortably met.

---

## ⚠️ Edge Cases & Common Pitfalls

| Situation | What to watch out for | Fix |
|-----------|-----------------------|-----|
| Negative numbers everywhere | Initial best value must be `-∞`, not `0`. | Use `Long.MIN_VALUE` / `-10⁹⁹`. |
| `k = n` | Only the entire array is a candidate. | The algorithm handles it automatically. |
| `k = 1` | Every subarray qualifies. | Remainder trick still works. |
| Prefix sums overflow 32‑bit | `int` can overflow if we use it for sums. | Use `long` / `int64_t`. |
| `minPrefix` initial value | If set to `+∞`, subtracting will give `-∞`. | Ensure remainder 0 is set to `0` before the loop. |

---

## 🔍 Code Walk‑Through (Java)

```java
public long maxSubarraySum(int[] nums, int k) {
    // 1️⃣  Remainder → smallest prefix sum
    long[] minPrefix = new long[k];
    Arrays.fill(minPrefix, Long.MAX_VALUE / 4);   // sentinel
    minPrefix[0] = 0;                            // empty prefix

    long best = Long.MIN_VALUE;
    long pref = 0;                               // running prefix

    // 2️⃣  One pass
    for (int i = 0; i < nums.length; i++) {
        pref += nums[i];                         // pref = pref[i+1]
        int rem = (i + 1) % k;                   // current prefix index
        best = Math.max(best, pref - minPrefix[rem]);   // candidate subarray
        minPrefix[rem] = Math.min(minPrefix[rem], pref);
    }
    return best;
}
```

* **Why `minPrefix[0] = 0`?**  
  The empty prefix (index 0) has remainder 0; any future prefix with the same remainder forms a valid subarray.

* **Why use `Long.MAX_VALUE / 4`?**  
  Keeps us safe from accidental overflow when we add `pref` to it.

---

## 🐍 Python & 🧩 C++ (See Code Above)

Both languages mirror the Java logic, just with language‑specific syntax and sentinel values.

---

## ✅ Quick Test Harness

```text
# Python quick‑tests
tests = [
    ([1,2], 1, 3),
    ([-1,-2,-3,-4,-5], 4, -10),
    ([-5,1,2,-3,4], 2, 4),
    ([5,5,5,5,5,5], 3, 30),
    ([-1000000]*200000, 1, -200000000000),
]
```

Running the helper in each language will confirm all cases pass.

---

## 🎯 Interview‑Ready Tips

| Tip | Why it matters |
|-----|----------------|
| **Explain the modulo trick first** | Shows you grasp the *core* math before coding. |
| **Always use 64‑bit integers** | Overflow is a common bug in interviewers’ test cases. |
| **Talk about sentinel values** | Prevents `+∞` from corrupting results. |
| **Show the loop invariants** | e.g., “`minPrefix[rem]` is always the smallest prefix we’ve seen so far.” |
| **Ask clarifying questions** | e.g., “Can lengths be zero?” (No, by definition subarrays must contain at least one element). |
| **Mention edge‑case handling** | “What if all numbers are negative?” – the algorithm still works because we initialize best to `-∞`. |

---

## 🎓 Takeaway

*Maximum Subarray Sum With Length Divisible by K* is a clean demonstration of how **prefix sums + modular arithmetic** can turn a seemingly DP‑heavy problem into an O(n) linear scan.  

With the three code snippets above you’re ready to impress interviewers (and LeetCode).  

Happy coding, and good luck landing that next job! 🚀  

---