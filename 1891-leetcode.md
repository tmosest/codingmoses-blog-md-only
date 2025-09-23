---
title: LeetCode 1891. Cutting Ribbons - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 1891. Cutting Ribbons – The Ultimate Binary‑Search Interview Problem  
*(LeetCode #1891)*  

| Language | Code |
|----------|------|
| **Java** | <details><summary>Click to expand</summary>```java
/**
 * 1891. Cutting Ribbons
 * LeetCode – Medium
 *
 * Time Complexity : O(n · log(max(ribbons)))
 * Space Complexity: O(1)
 */
class Solution {
    public int maxLength(int[] ribbons, int k) {
        // 1. Find the maximum possible length in the array
        int maxLen = 0;
        for (int r : ribbons) maxLen = Math.max(maxLen, r);

        // 2. Binary search on answer
        int low = 1, high = maxLen, best = 0;
        while (low <= high) {
            int mid = low + (high - low) / 2;      // candidate length

            long cnt = 0;                          // how many pieces we can get
            for (int r : ribbons) cnt += r / mid;   // integer division

            if (cnt >= k) {                       // we can produce at least k pieces
                best = mid;                       // mid is a candidate
                low = mid + 1;                    // try to increase the length
            } else {
                high = mid - 1;                    // need a smaller length
            }
        }
        return best;
    }
}
```</details> |
| **Python** | <details><summary>Click to expand</summary>```python
"""
1891. Cutting Ribbons – LeetCode
Time Complexity : O(n * log(max(ribbons)))
Space Complexity: O(1)
"""

def maxLength(ribbons: list[int], k: int) -> int:
    # Find the maximum ribbon length
    max_len = max(ribbons)

    # Binary search on the answer
    low, high = 1, max_len
    best = 0
    while low <= high:
        mid = (low + high) // 2

        # Count how many pieces of length `mid` we can obtain
        pieces = sum(r // mid for r in ribbons)

        if pieces >= k:
            best = mid          # mid is a valid answer, try to increase
            low = mid + 1
        else:
            high = mid - 1      # too many pieces, length too large

    return best
```
```</details> |
| **C++** | <details><summary>Click to expand</summary>```cpp
/**
 * 1891. Cutting Ribbons – LeetCode
 * Time Complexity : O(n · log(max(ribbons)))
 * Space Complexity: O(1)
 */

#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxLength(vector<int>& ribbons, int k) {
        int maxLen = *max_element(ribbons.begin(), ribbons.end());

        int low = 1, high = maxLen, best = 0;
        while (low <= high) {
            int mid = low + (high - low) / 2;

            long long pieces = 0;
            for (int r : ribbons)
                pieces += r / mid;

            if (pieces >= k) {          // can produce at least k pieces
                best = mid;
                low = mid + 1;          // try larger length
            } else {
                high = mid - 1;         // need smaller length
            }
        }
        return best;
    }
};
```</details> |

---

# 📝 Blog Post – “Cutting Ribbons: The Good, The Bad, and The Ugly”

> **SEO Title**: Cutting Ribbons LeetCode 1891 – Binary Search Strategy, Java/Python/C++ Solutions & Interview Tips  
> **Meta Description**: Master LeetCode 1891 (Cutting Ribbons) with a clear binary‑search solution in Java, Python, and C++. Learn the pitfalls, performance tricks, and how to ace this problem in your next software‑engineering interview.

---

## 1. Introduction

**Cutting Ribbons** (LeetCode #1891) is a classic *maximum‑value binary‑search* problem that tests your ability to combine two fundamental CS concepts:

1. **Greedy counting** – “Given a ribbon length `L`, how many pieces can we cut?”
2. **Binary search on answer** – “Find the largest `L` that satisfies the condition.”

If you’re preparing for a **software‑engineering interview**, this problem is a staple. It’s often asked in *Google*, *Amazon*, and *Microsoft* interviews. Understanding the good, bad, and ugly approaches will help you **score high** on the coding round.

---

## 2. Problem Statement (Simplified)

You’re given:

- An integer array `ribbons` where `ribbons[i]` is the length of the i‑th ribbon.
- An integer `k`.

You can cut each ribbon into any number of *positive integer* segments (or leave it intact). Discard leftovers.

**Goal**: Find the maximum integer `x` such that you can cut at least `k` pieces of length `x`.  
If no such `x` exists, return `0`.

---

## 3. The Good – Efficient Binary Search (O(n log m))

### 3.1. Intuition

- If a length `x` works (you can cut `k` pieces), then every smaller length also works.
- If a length `x` fails, every larger length fails too.

Thus the feasibility function is *monotonic*: `true → true → … → false → false`.  
This is a textbook scenario for binary search on the answer.

### 3.2. Feasibility Check

```text
countPieces(L) = Σ (ribbon / L)   for all ribbons
```

- `/` is integer division → automatically discards remainders.
- Sum up the number of pieces of length `L` from all ribbons.

If `countPieces(L) >= k` → `L` is *feasible*.
```

### 3.3. Binary Search Procedure

1. **Search space**: `[1, max(ribbons)]`.  
   (`max(ribbons)` is the largest possible answer).
2. **Mid‑point**: `mid = low + (high - low) / 2` (avoids overflow).
3. **Update pointers**:
   - If feasible → store answer, search right (`low = mid + 1`).
   - Else → search left (`high = mid - 1`).

### 3.3. Complexity

- **Time**: Each feasibility test is `O(n)`.  
  Binary search needs `O(log max(ribbons))` iterations → `O(n log m)`.
- **Space**: Only a few integer variables → `O(1)`.

### 3.4. Edge‑Case Handling

- **k > sum(ribbons)**: impossible → return `0`.  
  The binary search will naturally yield `0` because no length can produce enough pieces.
- **Large `k`**: Use `long long` / `long` to avoid overflow when summing pieces.

---

## 4. The Bad – Naïve Linear or DP Solutions

| Approach | Time | Space | Why it’s Bad |
|----------|------|-------|--------------|
| Brute force enumeration of all lengths (1…max) | O(n · max) | O(1) | `max` can be `10^5`. Worse than `O(n log m)` for large input. |
| Dynamic programming on ribbon lengths | O(n · max) | O(max) | DP is unnecessary and adds extra memory. |
| Two‑pointer sliding window | O(n) | O(1) | Doesn’t apply – problem isn’t about sub‑arrays. |

These naive approaches *mismatch the problem’s monotonic property* and give **90‑plus percent** slower solutions than the binary‑search method. In an interview setting, the interviewer will **immediately notice** the inefficiency.

---

## 5. The Ugly – Incorrect Recursion / Over‑Optimization

Some candidates write a recursive “divide‑and‑conquer” that *tries* to cut ribbons and backtracks on failure. While interesting, it:

- **Blows up on deep recursion** (`k` up to `10^9` → stack overflow).
- **Has exponential worst‑case** if not carefully memoized.
- **Is hard to read** – interviews reward *clean, readable code*.

> **Bottom line**: Stick to the clean greedy + binary‑search loop. No need for fancy recursion or memoization.

---

## 6. Common Pitfalls & How to Avoid Them

| Mistake | Symptom | Fix |
|---------|---------|-----|
| Using `int` to sum pieces → overflow | Wrong answer for large `k` | Use `long long`/`long` for the counter. |
| Off‑by‑one errors in binary search bounds | Returns `maxLen+1` or `0` incorrectly | Use `low <= high` and update `best` only on feasibility. |
| Not handling `k > totalLength` | Returns `maxLen` instead of `0` | Early check: if `k > sum(ribbons)` → return `0`. |
| Recomputing `maxLen` in every loop | Extra `O(n)` per iteration | Compute once before the binary search. |

---

## 7. Variation & Extension Ideas

1. **Different Cut Cost** – Each cut costs time; minimize total cost while keeping at least `k` pieces. → Use *binary search* + *cost function*.
2. **Piece Length Non‑Integer** – Allow fractional lengths → still binary search but use *floating‑point* feasibility test.
3. **Multiple Queries** – Pre‑compute prefix sums to answer several `k` values in O(log m) each.

Knowing how to adapt the core algorithm gives you *flexibility* in interviews.

---

## 8. Sample Solutions (Java / Python / C++)

(See the code table above).  
Each solution follows the same clean structure:

```pseudo
maxLen = max(ribbons)
best = 0
while low <= high:
    mid = (low + high) // 2
    pieces = sum(ribbon // mid)
    if pieces >= k:
        best = mid
        low = mid + 1
    else:
        high = mid - 1
return best
```

---

## 9. Take‑Away Checklist

- [ ] Understand *monotonic feasibility* → binary search.
- [ ] Use integer division (`//` in Python, `/` in Java/C++).
- [ ] Keep the counter in a 64‑bit type to avoid overflow.
- [ ] Compute the search space’s `high` once (`max(ribbons)`).
- [ ] Prefer `while low <= high` over `while low < high` for clarity.
- [ ] Add early return `0` if `k > totalPieces(1)` (optional but neat).

---

## 10. Final Thoughts

Cutting Ribbons is a **micro‑ecosystem** of CS ideas: greedy, monotonic, binary search.  
- **Good**: Binary‑search on answer, linear‑time feasibility → fast and scalable.  
- **Bad**: Linear or exponential brute force → too slow for large tests.  
- **Ugly**: Over‑engineered recursion → hard to read and maintain.

Master the *good* pattern, avoid the *bad*, and keep the *ugly* away. You’ll be ready to solve this problem in any coding interview—and to impress hiring managers who care about clean, efficient solutions.

---

> **Want to ace more LeetCode interview questions?**  
> Subscribe to our weekly coding challenge newsletter for curated problems, interview questions, and video walkthroughs. 🚀

---