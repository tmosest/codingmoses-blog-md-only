---
title: LeetCode 1788. Maximize the Beauty of the Garden - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1788. Maximize the Beauty of the Garden  
**Hard – LeetCode** | **Java / Python / C++** | **Interview‑Ready**  

---

## Blog Title  
**“Maximize the Beauty of the Garden – 1788 LeetCode: A 3‑Language Solution (Java, Python, C++)”**

> **SEO Keywords** – LeetCode 1788, Maximize the Beauty of the Garden, garden problem, prefix sum, dynamic programming, coding interview, Java solution, Python solution, C++ solution, job interview coding challenge

---

## 1. Problem Overview  

You are given an array `flowers` of length `n` (`2 ≤ n ≤ 10⁵`).  
Each element is the beauty value of a flower and can be negative.  
You may delete any number of flowers (including none).  
After deletions you must have a **valid garden**:

1. At least two flowers remain.  
2. The first and last flower in the remaining sequence have the **same** beauty value.

The beauty of a garden is the sum of all remaining flowers.  
Return the **maximum** possible beauty of any valid garden.

---

## 2. Intuition – “The First & Last Matter”

Only the first and the last element of the chosen subsequence influence the feasibility of the garden.  
Everything in between can be kept or removed freely.

*If we keep a positive flower between the two equal ends, we gain value; if it is negative, we can drop it without hurting the beauty.*

Hence the optimal strategy is:

1. Pick a value `v`.  
2. Keep the **first** occurrence of `v` as the left end.  
3. Keep the **last** occurrence of `v` as the right end.  
4. Keep every positive flower strictly between those two positions.

This yields the maximum beauty for that particular value `v`.  
We need to try every distinct value and take the best result.

---

## 3. Algorithm (O(n) time, O(n) space)

| Step | Detail |
|------|--------|
| **1** | Scan `flowers` once to record for each value its first and last index. |
| **2** | Compute a prefix array `pref[i]` = sum of **positive** flowers up to index `i` (inclusive). |
| **3** | For every value that appears at least twice (`last > first`):  
`beauty = 2 * v + (pref[last-1] - pref[first])` |
| **4** | Keep the maximum of all such beauties. |

*Why the formula works*  
`pref[last-1] - pref[first]` equals the sum of positive flowers strictly between the two occurrences.  
Adding `2 * v` accounts for the two equal end flowers.  
Negative flowers in the interval are simply ignored.

---

## 4. Edge Cases

| Case | Explanation |
|------|-------------|
| **Only one occurrence of a value** | Cannot form a valid garden → skip. |
| **All flowers negative** | Still must keep two equal negatives. The formula handles this because the prefix sum between them is 0. |
| **All positive** | The best garden is the whole array (first and last are the same value, typically the maximum). |

---

## 5. Code Implementations

Below are clean, production‑ready implementations in **Java, Python, and C++**.  
All three use the same O(n) logic and handle 32‑bit integer limits safely by using `long`/`long long` for sums.

### 5.1 Java

```java
import java.util.*;

class Solution {
    public int maximumBeauty(int[] flowers) {
        int n = flowers.length;

        // 1. Record first and last occurrence of each value
        Map<Integer, Integer> first = new HashMap<>();
        Map<Integer, Integer> last  = new HashMap<>();
        for (int i = 0; i < n; i++) {
            int v = flowers[i];
            if (!first.containsKey(v)) first.put(v, i);
            last.put(v, i);               // keeps the latest index
        }

        // 2. Prefix sum of positive values
        long[] pref = new long[n];
        pref[0] = Math.max(0, flowers[0]);
        for (int i = 1; i < n; i++) {
            pref[i] = pref[i - 1] + Math.max(0, flowers[i]);
        }

        long best = Long.MIN_VALUE;

        // 3. Try every value that occurs at least twice
        for (int v : first.keySet()) {
            int l = first.get(v);
            int r = last.get(v);
            if (l == r) continue;           // need at least two flowers

            long middle = pref[r - 1] - pref[l];   // positives between l and r
            long candidate = 2L * v + middle;
            best = Math.max(best, candidate);
        }

        return (int) best;   // result fits in int by problem constraints
    }
}
```

### 5.2 Python

```python
from typing import List
from collections import defaultdict

class Solution:
    def maximumBeauty(self, flowers: List[int]) -> int:
        n = len(flowers)

        # first and last indices
        first = {}
        last  = {}
        for i, v in enumerate(flowers):
            if v not in first:
                first[v] = i
            last[v] = i

        # prefix sum of positives
        pref = [0] * n
        pref[0] = max(0, flowers[0])
        for i in range(1, n):
            pref[i] = pref[i - 1] + max(0, flowers[i])

        best = float('-inf')

        for v in first:
            l, r = first[v], last[v]
            if l == r:
                continue
            middle = pref[r - 1] - pref[l]
            candidate = 2 * v + middle
            best = max(best, candidate)

        return best
```

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maximumBeauty(vector<int>& flowers) {
        int n = flowers.size();

        unordered_map<int, int> first, last;
        for (int i = 0; i < n; ++i) {
            int v = flowers[i];
            if (!first.count(v)) first[v] = i;   // first occurrence
            last[v] = i;                         // latest (last) occurrence
        }

        vector<long long> pref(n);
        pref[0] = max(0LL, flowers[0]);
        for (int i = 1; i < n; ++i) {
            pref[i] = pref[i-1] + max(0LL, flowers[i]);
        }

        long long best = LLONG_MIN;

        for (const auto &kv : first) {
            int v = kv.first;
            int l = kv.second;
            int r = last[v];
            if (l == r) continue;   // need two flowers

            long long middle = pref[r-1] - pref[l];
            long long cand = 2LL * v + middle;
            best = max(best, cand);
        }

        return static_cast<int>(best);
    }
};
```

All three codes run in **linear time** (`O(n)`) and use **linear auxiliary space** (`O(n)`).

---

## 6. Complexity Analysis  

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n)` | `O(n)` | `O(n)` |
| **Space** | `O(n)` (two maps + prefix array) | `O(n)` | `O(n)` |
| **Why It’s Acceptable** | `n ≤ 10⁵`, `|flowers[i]| ≤ 10⁴` → sums ≤ 10⁹, safely fit in 32‑bit signed integer. |

---

## 7. “Good, Bad, & Ugly” of the Solution

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Good** | • Single pass, no recursion. <br>• Handles negative values elegantly. <br>• Uses only built‑in containers → fast & memory‑efficient. |
| **Bad** | • Requires scanning the array twice (once for indices, once for prefix). <br>• Needs two hash maps – a bit more memory than strictly necessary. |
| **Ugly** | • Some naive solutions (e.g., “pick any two equal ends and sum everything”) accidentally add negative numbers in the middle, giving sub‑optimal results. <br>• Recursive DP approaches that attempt to evaluate every subsequence explode to exponential time on this scale. |

---

## 8. Alternative Approaches & Why We Avoid Them  

1. **Brute Force (O(n²))** – Enumerate every pair of equal values and sum the positives between. Too slow for `n = 10⁵`.  
2. **Dynamic Programming (DP)** – The problem is *not* a classic DP (there’s no overlapping sub‑problem structure) – using DP would only add unnecessary complexity.  
3. **Segment Tree / Binary Indexed Tree** – Overkill; prefix sums already give constant‑time range queries.

Thus, the prefix‑sum + first/last trick is the sweet spot.

---

## 9. How to Explain This to Interviewers

> **When asked “How would you solve LeetCode 1788?”, you can say:**
>
> *“I first noticed that only the first and last elements matter. I recorded for each beauty value the earliest and latest index. I then built a prefix sum of only positive flowers, which lets me compute the best sum between two equal ends in O(1). Finally I iterate over all values that occur at least twice and keep the maximum. The algorithm is O(n) and uses O(n) extra space – perfect for 10⁵ elements.”*

This concise explanation shows that you can **abstract the problem**, spot a **pattern**, and apply a **clean O(n) technique** – exactly what interviewers look for.

---

## 10. Final Thoughts – Your Next Interview

- **Practice**: Implement this solution in your favourite language from scratch.  
- **Timing**: Run it against 10⁵ random cases to convince yourself of its linearity.  
- **Explain**: Be ready to discuss the “positive‑only prefix” trick – that’s the key insight.  

Mastering this problem gives you a powerful pattern: *“When only boundary conditions matter, keep extremes and greedily include positives inside.”*  
Apply it to similar subsequence problems you’ll encounter in real interviews.

---

### 🚀 Takeaway

- **LeetCode 1788** is solvable in *linear time* using *prefix sums*.  
- The same logic translates to **Java, Python, and C++** with minimal effort.  
- Understanding the intuition behind “first & last” lets you explain the solution fluently in a coding interview, boosting your confidence and score.  

Happy coding, and may your job interview garden always be *beautiful*!