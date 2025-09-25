---
title: LeetCode 3219. Minimum Cost for Cutting Cake II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Minimum Cost for Cutting Cake II – Leetcode Hard (3219)

> **Keywords**: Minimum Cost for Cutting Cake II, Leetcode Hard, greedy algorithm, dynamic programming, Java, Python, C++, interview preparation, coding interview, algorithmic problem solving

---

## 📌 Problem Summary

You are given a rectangular cake of size `m × n`.  
You have to cut the cake into `1 × 1` squares using only the allowed horizontal and vertical cuts.

* `horizontalCut[i]` – cost of cutting along the horizontal line **i** (0‑based).  
  There are `m‑1` horizontal cuts.
* `verticalCut[j]` – cost of cutting along the vertical line **j** (0‑based).  
  There are `n‑1` vertical cuts.

In each operation you pick **one** remaining piece of cake and perform **one** cut (horizontal or vertical) on it.  
The cost of a cut is **fixed** – it does not change with the size of the piece you cut.

Return the minimum total cost to cut the whole cake into `1 × 1` pieces.

---

## 🏁 Example

```
m = 3, n = 2
horizontalCut = [1, 3]
verticalCut   = [5]
```

The optimal strategy is:

1. Cut vertically (cost 5) → two pieces of size 3×1.
2. Cut the left piece horizontally (cost 1) → 1×1 + 2×1.
3. Cut the right piece horizontally (cost 1) → 1×1 + 2×1.
4. Cut the two 2×1 pieces vertically (cost 3 each) → 1×1 + 1×1.

Total = 5 + 1 + 1 + 3 + 3 = **13**.

---

## 🎯 Why a Greedy Strategy Works

If you look closely, every cut will later be multiplied by the number of *segments* it crosses:

* A horizontal cut of cost `h` is multiplied by the current number of **vertical** segments (`vPieces`).
* A vertical cut of cost `v` is multiplied by the current number of **horizontal** segments (`hPieces`).

If you perform a cheaper cut first, you increase the multiplier for the more expensive cut later, which can only increase the total cost.  
Therefore, **cut the most expensive available line first**.

> **Exchange Argument**  
> Suppose two consecutive cuts have costs `h` and `v` with `h > v`.  
> Cutting `h` first costs `h × vPieces`. The remaining vertical cuts will each be multiplied by `hPieces+1`.  
> Cutting `v` first would cost `v × vPieces` + later `h × (hPieces+1)`.  
> Because `h > v`, the first case is always cheaper or equal, proving the greedy choice.

---

## ⏱️ Complexity

| Approach | Time | Space |
|----------|------|-------|
| Sort & Merge (classic greedy) | `O(m log m + n log n)` | `O(1)` extra (in‑place sort) |
| Bucket / Counting sort | `O(m + n + C)` (C ≤ 1000) | `O(C)`  |

The bucket solution is optimal for the worst‑case `m, n ≤ 10⁶` while keeping the multiplier range (`≤ 1000`) tiny.

---

## 📦 Reference Implementations

Below are clean, idiomatic solutions for **Java**, **Python**, and **C++**.

> **Tip** – Always remember to use `long`/`long long` for the answer because the cost can overflow a 32‑bit integer.

### Java

```java
import java.util.Arrays;

class Solution {
    public long minimumCost(int m, int n, int[] horizontalCut, int[] verticalCut) {
        // Sort so we can pick the largest cost first
        Arrays.sort(horizontalCut);     // ascending
        Arrays.sort(verticalCut);

        // Counters of how many pieces we have along each axis
        int hPieces = 1;  // horizontal pieces (affected by vertical cuts)
        int vPieces = 1;  // vertical pieces   (affected by horizontal cuts)

        // Pointers start at the last element (largest cost)
        int hIdx = horizontalCut.length - 1;   // m-2
        int vIdx = verticalCut.length - 1;     // n-2

        long total = 0L;

        while (hIdx >= 0 && vIdx >= 0) {
            if (horizontalCut[hIdx] > verticalCut[vIdx]) {
                total += (long) horizontalCut[hIdx] * vPieces;
                hPieces++;            // we created one more horizontal segment
                hIdx--;
            } else {
                total += (long) verticalCut[vIdx] * hPieces;
                vPieces++;            // we created one more vertical segment
                vIdx--;
            }
        }

        // Drain the remaining cuts of one type
        while (hIdx >= 0) {
            total += (long) horizontalCut[hIdx] * vPieces;
            hPieces++;
            hIdx--;
        }
        while (vIdx >= 0) {
            total += (long) verticalCut[vIdx] * hPieces;
            vPieces++;
            vIdx--;
        }

        return total;
    }
}
```

### Python (3)

```python
from typing import List

class Solution:
    def minimumCost(self, m: int, n: int,
                    h: List[int], v: List[int]) -> int:
        # Sort ascending, process from largest to smallest
        h.sort()
        v.sort()

        h_pieces, v_pieces = 1, 1  # start with a single slice in each direction
        h_idx, v_idx = len(h) - 1, len(v) - 1
        total = 0

        while h_idx >= 0 and v_idx >= 0:
            if h[h_idx] > v[v_idx]:
                total += h[h_idx] * v_pieces
                h_pieces += 1
                h_idx -= 1
            else:
                total += v[v_idx] * h_pieces
                v_pieces += 1
                v_idx -= 1

        # Drain remaining cuts
        while h_idx >= 0:
            total += h[h_idx] * v_pieces
            h_pieces += 1
            h_idx -= 1
        while v_idx >= 0:
            total += v[v_idx] * h_pieces
            v_pieces += 1
            v_idx -= 1

        return total
```

### C++ (17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long minimumCost(int m, int n,
                          vector<int>& horizontalCut,
                          vector<int>& verticalCut) {
        sort(horizontalCut.begin(), horizontalCut.end());
        sort(verticalCut.begin(), verticalCut.end());

        long long total = 0;
        long long hPieces = 1, vPieces = 1; // current number of slices

        int hIdx = (int)horizontalCut.size() - 1;
        int vIdx = (int)verticalCut.size() - 1;

        while (hIdx >= 0 && vIdx >= 0) {
            if (horizontalCut[hIdx] > verticalCut[vIdx]) {
                total += (long long)horizontalCut[hIdx] * vPieces;
                ++hPieces;
                --hIdx;
            } else {
                total += (long long)verticalCut[vIdx] * hPieces;
                ++vPieces;
                --vIdx;
            }
        }

        while (hIdx >= 0) {
            total += (long long)horizontalCut[hIdx] * vPieces;
            ++hPieces;
            --hIdx;
        }

        while (vIdx >= 0) {
            total += (long long)verticalCut[vIdx] * hPieces;
            ++vPieces;
            --vIdx;
        }

        return total;
    }
};
```

---

## 🚨 Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Using `int` for the answer → overflow | Use `long`/`long long` |
| Forgetting to multiply by the *current* piece count | Keep two counters (`hPieces`, `vPieces`) |
| Wrong pointer initialisation (`m‑1` instead of `m‑2`) | Remember that `horizontalCut` has length `m‑1` → last valid index is `m‑2` |
| Sorting in the wrong direction | Sort ascending, then walk from the back (largest to smallest) |

---

## 🔍 Alternative Approaches

1. **Dynamic Programming** – Build a 2‑D DP over the number of horizontal/vertical cuts left.  
   Complexity `O(m·n)` – too slow for the hard constraints.
2. **Priority Queue (Min‑Heap)** – Pull the cheapest cut each time and multiply by the opposite piece count.  
   Works but has extra `O((m+n) log (m+n))` overhead.
3. **Bucket / Counting Sort** – Since cut costs are ≤ 1000, we can count frequencies and process from high to low in linear time `O(m+n+1000)`.  
   Ideal for the extreme limits (`m, n ≤ 10⁶`).

> **Why we recommend the simple `sort‑and‑merge` greedy**  
> It is easy to reason about, has the same asymptotic complexity as the bucket solution, and works on all modern platforms.

---

## 📈 Time & Space Complexity

| Implementation | Time | Space |
|----------------|------|-------|
| Java, Python, C++ (sort + merge) | `O(m log m + n log n)` | `O(1)` (in‑place sort) |
| Bucket / Counting sort | `O(m + n + C)` (`C=1000`) | `O(C)` |

The `C` term is constant and negligible compared to `m, n`.

---

## 🎤 Interview Angle

- **What the interviewer wants**: Demonstrate that you understand how the cost of a cut is *multiplied* by the number of existing slices.
- **Key Talking Points**:
  1. Explain the cost model.
  2. Argue why cutting the most expensive line first is optimal (exchange argument).
  3. Discuss implementation details (pointers, counters, overflow).
  4. Mention edge cases (`m=1` or `n=1`).
  5. Show a clean, bug‑free code snippet.

---

## 👀 Summary

- **Problem**: Cut a `m × n` cake into `1 × 1` squares with fixed cut costs.
- **Solution**: Greedy – cut the most expensive line first; multiply by the current number of segments.
- **Proof**: Exchange argument ensures that any cheaper cut performed earlier cannot reduce the total.
- **Complexity**: `O(m log m + n log n)` time, `O(1)` extra space.
- **Variants**: Bucket/Counting sort gives `O(m+n)` time for the hard version.

---

## 🎯 How This Helps You Get a Job

- Mastering this problem demonstrates *algorithmic thinking*, *optimal‑substructure*, and *careful handling of integer overflow* – all highly prized skills in a coding interview.
- The greedy approach is succinct, easy to explain, and showcases your ability to reason about multipliers – a classic interview trick.
- Having a clean, language‑agnostic implementation (Java, Python, C++) shows you can write production‑ready code in the language the company uses.

Feel free to add this solution to your **Leetcode Hard** portfolio, explain it in a video, or use it as a talking point in your next interview.

Good luck, happy coding, and may the interview gods be ever in your favor! 🚀

--- 

> **Meta‑Description**:  
> Master Leetcode 3219 – Minimum Cost for Cutting Cake II – with a clear greedy explanation, proof, complexity analysis, and polished Java, Python, and C++ implementations. Perfect for interview prep and job‑seeking programmers.