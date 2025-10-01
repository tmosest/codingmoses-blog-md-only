---
title: LeetCode 2557. Maximum Number of Integers to Choose From a Range II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2557. Maximum Number of Integers to Choose From a Range II  

> **Goal** – choose the largest possible set of distinct numbers from  
> `[1 … n]` that are *not* in the `banned` list and whose sum does not exceed `maxSum`.

Below you’ll find ready‑to‑copy implementations in **Java**, **Python 3**, and **C++**.  
After the code you’ll read a short blog article that explains the trade‑offs of the two common solutions (binary‑search vs greedy), gives practical tips for interviews, and is SEO‑optimized so recruiters can find it quickly.

---

## 1. Java Solution – Binary Search

```java
import java.util.*;

class Solution {
    public int maxCount(int[] banned, int n, long maxSum) {
        // sort once to use binary‑search for sum reduction
        Arrays.sort(banned);

        int lo = 0, hi = n;          // [lo, hi] is the candidate size
        while (lo < hi) {
            int mid = lo + (hi - lo + 1) / 2;     // candidate number of picks
            long sum = (long) mid * (mid + 1) / 2; // 1+2+…+mid

            // subtract every banned number <= mid
            int idx = Arrays.binarySearch(banned, mid);
            int pos = idx >= 0 ? idx : -idx - 1;   // first > mid
            for (int i = 0; i < pos; ++i) {
                sum -= banned[i];
            }

            if (sum <= maxSum) lo = mid;   // mid is feasible, try larger
            else                hi = mid - 1;
        }

        // remove the banned numbers that are <= lo
        int bad = 0;
        for (int b : banned) {
            if (b <= lo) bad++;
            else break;          // banned is sorted
        }
        return lo - bad;
    }
}
```

> **Complexity** – `O( |banned| log n )` time, `O(1)` extra space  
> (`banned` is sorted in‑place). Works for `n` up to `10^9` and `maxSum` up to `10^15`.

---

## 2. Python 3 Solution – Binary Search

```python
from bisect import bisect_right
from math import sqrt, floor
from typing import List

class Solution:
    def maxCount(self, banned: List[int], n: int, maxSum: int) -> int:
        banned.sort()                     # O(b log b)
        lo, hi = 0, n

        while lo < hi:
            mid = lo + (hi - lo + 1) // 2
            total = mid * (mid + 1) // 2   # 1+2+…+mid

            # all banned numbers <= mid
            pos = bisect_right(banned, mid)
            for i in range(pos):
                total -= banned[i]

            if total <= maxSum:
                lo = mid
            else:
                hi = mid - 1

        # subtract banned numbers <= lo
        bad = bisect_right(banned, lo)
        return lo - bad
```

> **Complexity** – `O( |banned| log n )` time, `O(1)` extra space.

---

## 3. C++ Solution – Binary Search

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxCount(vector<int>& banned, int n, long long maxSum) {
        sort(banned.begin(), banned.end());
        int lo = 0, hi = n;

        while (lo < hi) {
            int mid = lo + (hi - lo + 1) / 2;
            long long total = 1LL * mid * (mid + 1) / 2;  // 1+2+…+mid

            // subtract banned numbers <= mid
            auto it = upper_bound(banned.begin(), banned.end(), mid);
            for (auto itr = banned.begin(); itr != it; ++itr)
                total -= *itr;

            if (total <= maxSum) lo = mid;
            else                 hi = mid - 1;
        }

        // remove banned numbers <= lo
        int bad = upper_bound(banned.begin(), banned.end(), lo) - banned.begin();
        return lo - bad;
    }
};
```

> **Complexity** – `O( |banned| log n )` time, `O(1)` extra space.

---

## 4. Blog Article – “The Good, the Bad, and the Ugly of LeetCode 2557”

> **Keywords**: LeetCode 2557, Maximum Number of Integers to Choose From a Range II, interview problem, binary search, greedy, algorithm design, time complexity, space complexity, job interview, software engineer.

### 4.1 Problem Overview  
LeetCode 2557 asks you to pick the most numbers from `[1…n]` subject to two constraints:  
* the numbers must be distinct and not in `banned`,  
* the total sum must stay ≤ `maxSum`.  

The challenge is **scale** – `n` can be a billion, while `maxSum` can be fifteen quadrillion. You need an algorithm that works in **logarithmic time** relative to `n`, not linear.

### 4.2 Constraints that Shape the Design  
* `|banned|` ≤ 10⁵ – a relatively small array.  
* `n` ≤ 10⁹ – you cannot build a prefix sum array of length `n`.  
* `maxSum` ≤ 10¹⁵ – 64‑bit arithmetic is required.  

These constraints push you toward **mathematical insight** (closed‑form sums) and **efficient data structures** (sorted arrays, binary search, hash sets).

### 4.3 Approach #1 – Binary Search (Prefix‑Sum + Filtering)

**Good**  
* **Deterministic** – guarantees the optimal answer in log n steps.  
* **Mathematically clean** – uses the closed‑form sum `mid*(mid+1)/2`.  
* **Scalable** – works for the maximum input sizes LeetCode allows.

**Bad**  
* **O(|banned|)** work per binary‑search step because you need to subtract every banned value that falls inside `[1…mid]`.  
* Implementation‑heavy: you have to handle sorting, binary search, and careful 64‑bit arithmetic.

**Ugly**  
* When `|banned|` is close to `n`, the loop that subtracts banned numbers becomes expensive.  
* Sorting `banned` takes `O(|banned| log |banned|)` time, which can be a nuisance in a tight interview slot.

> **Interview Tip** – *Explain that the “binary‑search” part really searches for the largest feasible count, not for a particular number.*  
> *Show the interviewer the math that turns the sum of an interval into a quadratic inequality, then solve it with a square‑root trick if you need a pure‑math version.*

### 4.4 Approach #2 – Greedy (Segment‑by‑Segment)

**Good**  
* **Linear in |banned|** – no logarithmic factor, so it runs faster on average.  
* Very intuitive: process each forbidden “gap” separately.  

**Bad**  
* You need to **sort `banned`** anyway, so the asymptotic cost is still `O(|banned| log |banned|)` if you do not have a sorted array to begin with.  
* Requires careful handling of edge cases: empty segments, overflow in arithmetic progression sums.

**Ugly**  
* The math behind the square‑root solution can be intimidating; many interviewers expect a clean, step‑by‑step explanation, not a “jump‑to‑equation” trick.  
* You must guard against floating‑point inaccuracies when using `sqrt`. A small error can change the final count.

#### The Greedy Core (C++‑style pseudocode)

```text
sort(banned)
low = 1
count = 0
for every banned[i] (including a sentinel at n+1):
    high = banned[i] (or n+1 if i == size)
    segmentSize = high - low
    segmentSum  = (low + high - 1) * segmentSize / 2

    if segmentSum > maxSum:
        // solve x^2 + x <= 2*maxSum + low^2 - low
        maxLen = floor( sqrt(2*maxSum + low*low - low + 0.25) - 0.5 )
        count += maxLen - low + 1
        break
    else:
        count += segmentSize
        maxSum -= segmentSum
    low = high + 1
```

*Time:* `O(|banned|)`  
*Space:* `O(1)` (in‑place sort only)

### 4.5 Edge‑Case Checklist

| Case | What to Test | Why it matters |
|------|---------------|----------------|
| All numbers banned | `banned = [1,2,…,n]` | Result must be `0` |
| No banned numbers | `banned = []` | Works with the binary‑search formula directly |
| `maxSum` very small | e.g. `maxSum = 1` | Only the number `1` (if not banned) |
| `maxSum` huge | e.g. `maxSum = 10^15` | You may pick every non‑banned number |
| `n` very large, banned near `n` | `n=10^9`, banned near 10^9 | Avoid integer overflow |

### 4.6 Complexity Summary

| Method | Time | Extra Space |
|--------|------|-------------|
| Binary Search | `O(|banned| log n)` | `O(1)` |
| Greedy (Segment) | `O(|banned|)` | `O(1)` |
| Sort `banned` (once) | `O(|banned| log |banned|)` | `O(1)` |

### 4.7 Interview‑Ready Checklist

1. **State the problem clearly** – mention the two constraints (`banned` and `maxSum`).  
2. **Discuss the dominant challenge** – the huge range `[1…n]`.  
3. **Offer two approaches** – binary‑search (optimal in worst‑case) and greedy (fast in practice).  
4. **Explain edge‑case handling** – sorted `banned`, binary‑search bounds, 64‑bit sums.  
5. **Show the code** in the language the interviewer prefers.  
6. **Be ready to justify your choice** – if time is tight, use greedy; if accuracy matters, binary‑search.

### 4.8 SEO‑Friendly Meta Description

> “Learn how to crack LeetCode 2557 – Maximum Number of Integers to Choose From a Range II.  
> Detailed binary‑search and greedy solutions in Java, Python 3, and C++.  
> Interview tips, complexity analysis, and edge‑case handling – perfect for software‑engineering job interviews.”

### 4.9 Final Thoughts

LeetCode 2557 is a classic *interval‑sum* problem where a naïve approach would be `O(n)` and thus impossible for the given constraints.  
By either **binary‑searching the answer** or **processing the allowed intervals greedily**, you can solve it comfortably within the limits.  

Bring both solutions to your interview and be ready to discuss the trade‑offs.  
Happy coding, and good luck landing that software‑engineering role!