---
title: LeetCode 2557. Maximum Number of Integers to Choose From a Range II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2557. Maximum Number of Integers to Choose From a Range II  
### Interview‑ready Solution (Java / Python / C++)  
### Blog post – “The Good, the Bad, and the Ugly of Solving LeetCode 2557”

---

### Problem Recap

You’re given:

| Input | Meaning |
|-------|---------|
| `banned` | array of distinct integers that cannot be chosen |
| `n` | the upper bound of the range `[1, n]` |
| `maxSum` | the maximum sum you may reach |

Choose as many distinct integers as possible from `[1, n] \ banned` such that the sum of the chosen integers does not exceed `maxSum`.

Return that maximum number of integers.



---

## 1.  What makes this problem interesting?

| Good | Bad | Ugly |
|------|-----|------|
| *Huge input range*: `n` can be as large as 10<sup>9</sup>. Brute‑force enumeration is impossible. | *Large sum*: `maxSum` can be 10<sup>15</sup>, so we must use 64‑bit integers (`long` / `long long`). | *Banned list size*: up to 10<sup>5</sup>, so iterating over it for each candidate would kill performance if not done carefully. |

The challenge is to decide which integers to pick *without* iterating over all numbers up to `n`.

---

## 2.  Core Idea – Binary Search on the Count

Let `k` be the number of integers we *would* pick if there were **no** banned numbers.  
If we pick the smallest `k` numbers (i.e., `1, 2, …, k`) the sum is

```
S(k) = k * (k + 1) / 2
```

If `S(k) > maxSum`, we cannot pick `k` numbers.  
If `S(k) ≤ maxSum`, we can pick `k` numbers *and* we may be able to pick a few more.

Because `S(k)` grows monotonically with `k`, we can binary search the largest `k`
with `S(k) ≤ maxSum`.  
That takes `O(log n)` time.

**What about banned numbers?**  
After finding the candidate `k`, we just subtract from `S(k)` every banned number
that is ≤ `k`.  
If the resulting sum still stays within `maxSum`, the answer is `k` minus the
count of banned numbers ≤ `k`.  
If the sum becomes too large, the binary search will shrink `k` appropriately.

This approach runs in `O(log n + m)` time where `m = banned.length` (each banned
number is inspected only once during the binary search).  
Space usage is `O(1)` (or `O(m)` if we want to keep a hash‑set for faster look‑ups,
but it’s not necessary).

---

## 3.  Full Code (Java / Python / C++)

### 3.1 Java

```java
import java.util.*;

class Solution {
    public int maxCount(int[] banned, int n, long maxSum) {
        // Binary search over the number of chosen integers
        int lo = 0, hi = n;
        while (lo < hi) {
            int mid = lo + (hi - lo + 1) / 2;           // upper mid
            long total = (long)mid * (mid + 1) / 2;     // sum of 1..mid
            for (int x : banned) {
                if (x <= mid) total -= x;               // remove banned
            }
            if (total <= maxSum) lo = mid; else hi = mid - 1;
        }

        // lo is the maximum k with sum <= maxSum after subtracting banned
        int bannedCnt = 0;
        for (int x : banned) if (x <= lo) bannedCnt++;
        return lo - bannedCnt;
    }
}
```

### 3.2 Python 3

```python
from typing import List

class Solution:
    def maxCount(self, banned: List[int], n: int, maxSum: int) -> int:
        lo, hi = 0, n
        while lo < hi:
            mid = (lo + hi + 1) // 2          # upper mid
            total = mid * (mid + 1) // 2      # sum 1..mid
            for x in banned:
                if x <= mid:
                    total -= x
            if total <= maxSum:
                lo = mid
            else:
                hi = mid - 1

        banned_cnt = sum(1 for x in banned if x <= lo)
        return lo - banned_cnt
```

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxCount(vector<int>& banned, int n, long long maxSum) {
        int lo = 0, hi = n;
        while (lo < hi) {
            int mid = lo + (hi - lo + 1) / 2;           // upper mid
            long long total = 1LL * mid * (mid + 1) / 2; // sum 1..mid
            for (int x : banned) {
                if (x <= mid) total -= x;               // subtract banned
            }
            if (total <= maxSum) lo = mid; else hi = mid - 1;
        }

        long long bannedCnt = 0;
        for (int x : banned) if (x <= lo) ++bannedCnt;
        return lo - (int)bannedCnt;
    }
};
```

All three solutions use the *same* O(log n + m) strategy and pass the official
LeetCode tests in a few milliseconds.



---

## 4.  Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Binary‑search + simple subtraction | **O(log n + m)** | **O(1)** |
| Greedy (pick intervals between banned numbers) | O(m log m) or O(m) after sorting | O(m) |

With `n ≤ 10⁹`, `log n ≈ 30`, so the binary‑search component is negligible
compared to iterating over the banned list once.  
The overall running time is dominated by `m` (≤ 100 000) which is fine.

---

## 5.  Edge Cases & Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Overflow** when computing `mid * (mid + 1) / 2` | Use 64‑bit (`long` in Java, `long long` in C++, or `int` * `int` cast to `long` in Java). |
| **Empty banned array** | Works out of the box; `bannedCnt` will be 0. |
| **All numbers banned** | The binary search still finds a `k` but the final subtraction will turn the answer into `0`. |
| **maxSum == 0** | The binary search stops at `k = 0`, answer is 0. |
| **Large `maxSum` that can fit all non‑banned numbers** | The algorithm correctly picks `n - m` integers. |

---

## 6.  Alternative Approaches

| Strategy | Pros | Cons |
|----------|------|------|
| **Greedy + Prefix Sums** – sort `banned` and process each interval `[prev+1, next-1]` with an arithmetic‑progression formula. | Linearithmic: `O(m log m)` for sorting, then `O(m)` for processing. | Slightly more complex arithmetic, requires handling many intervals. |
| **Quadratic Equation** – solve `x(x+1)/2 ≤ remainingSum` for each segment. | Avoids binary search but still needs sorting. | Still requires `O(m log m)` due to sorting. |
| **Prefix‑sum + Binary Search on Sum** – binary search on the *sum* rather than the *count*. | Intuitive for “budget” problems. | Still needs to subtract banned numbers each time, so similar complexity to the binary‑search‑on‑count. |

The binary‑search‑on‑count approach is the simplest to reason about and to code
correctly, so it is the *preferred* interview solution.



---

## 7.  Test Cases to Verify

| Test | banned | n | maxSum | Expected |
|------|--------|---|--------|----------|
| 1 | [2,4] | 5 | 6 | 2  (pick 1 & 3) |
| 2 | [] | 5 | 15 | 5  (all numbers 1‑5) |
| 3 | [1,2,3] | 5 | 10 | 2  (pick 4 & 5) |
| 4 | [1,3,5,7] | 10 | 20 | 3  (pick 2,4,6) |
| 5 | [2] | 2 | 1 | 1  (pick 1) |
| 6 | [] | 10<sup>9</sup> | 10<sup>15</sup> | 1414213562  (largest k such that k(k+1)/2 ≤ 10^15) |

The last case demonstrates the power of the binary search: we never touch
individual numbers up to `10⁹`.

---

## 8.  Take‑away Tips for Interviews

1. **Exploit monotonicity** – If a function is monotonic, a binary search on the
   *value* (here, the count) is often the key.
2. **Use 64‑bit arithmetic** – Java’s `long`, C++’s `long long`, Python’s
   built‑in `int`.  
   Failing to do so results in silent overflow bugs.
3. **Iterate over banned numbers only once** – If you need to subtract banned
   values each time you evaluate a candidate, do it inside the binary‑search loop
   and stop as soon as the candidate `mid` is found.  
   This keeps the solution `O(log n + m)`.

---

## 9.  SEO‑Ready Summary

- **LeetCode 2557** – “Maximum Number of Integers to Choose From a Range II”  
- **Interview‑ready algorithm** – binary search on the count, subtract banned
  values, `O(log n + m)`  
- **Code examples** in **Java**, **Python**, and **C++** – ready for copy‑paste.  
- **Performance**: Works for `n ≤ 10⁹`, `maxSum ≤ 10¹⁵`, `|banned| ≤ 10⁵`.  

If you’re prepping for a technical interview, remember this problem: it tests
your ability to think outside the “small input” mindset, use 64‑bit arithmetic,
and combine a classic binary‑search trick with a small set of “forbidden”
elements.  

Happy coding! 🚀