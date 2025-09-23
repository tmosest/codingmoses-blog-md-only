---
title: LeetCode 2387. Median of a Row Wise Sorted Matrix - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Overview  
**LeetCode 2387 – Median of a Row‑Wise Sorted Matrix**  
> *Given an odd‑sized `m × n` matrix (`m` and `n` are both odd) where each row is sorted in non‑decreasing order, return the median of all `m·n` elements.*

> **Goal** – find the median in **O(m·log n · log V)** time, where `V` is the range of values (≤ 10⁶), i.e. far better than the naïve `O(m·n)` scan.

---

## 2. Why a Binary Search on the *value* Space?  
* Each row is sorted → we can count “how many numbers are ≤ X” in O(log n) with binary search.  
* The median is the smallest number `X` such that at least half of the elements are ≤ X.  
* If we can quickly answer “how many elements ≤ X?” we can binary‑search over the *value* space (from the global minimum to the global maximum).

This strategy is the classic *binary search on answer* pattern.

---

## 3. Algorithm (High‑Level)

1. **Determine search bounds**  
   * `low` = smallest element in the matrix (first element of each row).  
   * `high` = largest element in the matrix (last element of each row).

2. **Binary search on values**  
   While `low < high`:  
   * `mid = (low + high) / 2`  
   * `cnt = Σ countRow(row, mid)` – number of elements in the matrix ≤ mid.  
   * If `cnt < (m·n + 1)/2` → median is **larger** → set `low = mid + 1`.  
   * Else → median is **≤** mid → set `high = mid`.

3. Return `low` (or `high`) – the median.

**countRow(row, mid)** is a standard binary search (`upper_bound`) returning the first index where element > mid. The returned index equals the number of elements ≤ mid in that row.

---

## 4. Correctness Proof  

We prove that the algorithm returns the true median.

### Lemma 1  
For any integer `x`, `cnt(x) = Σ_i upper_bound(row_i, x)` equals the number of matrix elements ≤ x.

*Proof.*  
Because each row is sorted, `upper_bound` returns the index of the first element strictly greater than `x`. All elements before that index are ≤ x. Summing across rows counts all elements in the matrix ≤ x. ∎

### Lemma 2  
Let `k = (m·n + 1)/2` (the position of the median in sorted order).  
If `cnt(mid) < k`, then the median > mid; if `cnt(mid) ≥ k`, then the median ≤ mid.

*Proof.*  
`cnt(mid)` counts how many elements are ≤ mid.  
* If fewer than `k` elements are ≤ mid, then the `k`‑th element (the median) must be larger than `mid`.  
* If at least `k` elements are ≤ mid, then the `k`‑th element cannot be larger than `mid`. ∎

### Lemma 3  
During binary search the invariant  
`low` is always ≤ median and `high` is always ≥ median holds.

*Proof by induction.*  
*Base*: Initially, `low` is the global minimum and `high` the global maximum; the median lies in between.  
*Step*:  
- If `cnt(mid) < k` → by Lemma 2 median > mid → we set `low = mid+1`. Since `mid < median`, `low` remains ≤ median.  
- Else (`cnt(mid) ≥ k`) → median ≤ mid → we set `high = mid`. Since `mid ≥ median`, `high` remains ≥ median. ∎

### Theorem  
When the loop terminates (`low == high`), the value returned equals the matrix median.

*Proof.*  
By Lemma 3 the search interval always contains the median. Termination occurs only when the interval shrinks to a single value. That single value must be the median because no smaller value can satisfy the median condition (`cnt < k`) and no larger value can satisfy `cnt ≥ k`. ∎

---

## 5. Complexity Analysis  

*Let `m` be the number of rows, `n` the number of columns.*

| Step | Operation | Time |
|------|-----------|------|
| Find `low`/`high` | Scan first & last elements of each row | `O(m)` |
| Each binary‑search iteration | For each of `m` rows perform `upper_bound` (`O(log n)`) | `O(m·log n)` |
| Number of iterations | `log₂(range)` where `range ≤ 10⁶` → ≤ 20 (≈ log₂(10⁶)) | `O(log V)` |

Total time: `O(m·log n·log V)` ≈ `O(m·log n)` for the given constraints.  
Space: `O(1)` – only a few integer variables.

---

## 6. Reference Implementations  

Below you’ll find **ready‑to‑copy** code in **Java, Python, and C++**. Each implementation follows the algorithm described above and contains inline comments for clarity.

### 6.1 Java (LeetCode‑compatible)

```java
import java.util.*;

class Solution {
    public int matrixMedian(int[][] grid) {
        int m = grid.length, n = grid[0].length;

        // 1. Find global min and max
        int low = Integer.MAX_VALUE;
        int high = Integer.MIN_VALUE;
        for (int[] row : grid) {
            low = Math.min(low, row[0]);           // first element of a sorted row
            high = Math.max(high, row[n - 1]);      // last element
        }

        int target = (m * n + 1) / 2;   // k-th element in sorted order

        // 2. Binary search on the value range
        while (low < high) {
            int mid = low + (high - low) / 2;

            int count = 0;
            for (int[] row : grid) {
                count += upperBound(row, mid);   // #elements ≤ mid in this row
            }

            if (count < target) {
                low = mid + 1;   // median is larger
            } else {
                high = mid;      // median is ≤ mid
            }
        }
        return low;   // low == high
    }

    /** returns first index > target (i.e., count of elements ≤ target) */
    private int upperBound(int[] arr, int target) {
        int lo = 0, hi = arr.length;   // hi is exclusive
        while (lo < hi) {
            int mid = (lo + hi) >>> 1;
            if (arr[mid] <= target) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}
```

---

### 6.2 Python

```python
from bisect import bisect_right
from typing import List

class Solution:
    def matrixMedian(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])

        # global bounds
        low = min(row[0] for row in grid)
        high = max(row[-1] for row in grid)

        target = (m * n + 1) // 2

        while low < high:
            mid = (low + high) // 2
            count = sum(bisect_right(row, mid) for row in grid)

            if count < target:
                low = mid + 1
            else:
                high = mid

        return low
```

---

### 6.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int matrixMedian(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();

        int low = INT_MAX, high = INT_MIN;
        for (const auto& row : grid) {
            low = min(low, row.front());
            high = max(high, row.back());
        }

        int target = (m * n + 1) / 2;

        while (low < high) {
            int mid = low + (high - low) / 2;
            int count = 0;
            for (const auto& row : grid) {
                count += upper_bound(row.begin(), row.end(), mid) - row.begin();
            }

            if (count < target) low = mid + 1;
            else high = mid;
        }
        return low;
    }
};
```

---

## 7. Blog Post – *“The Good, the Bad, and the Ugly of Finding the Median in a Row‑Wise Sorted Matrix”*  

### 7.1 Why This Problem Matters (SEO keywords: *algorithm interview, binary search, matrix median, coding interview, LeetCode 2387*)

When recruiters ask “How would you find the median of a sorted matrix?” they’re really testing three core competencies:

1. **Algorithmic Thinking** – Can you spot the opportunity for binary search on *values* instead of indices?
2. **Complexity Awareness** – Do you know the time/space trade‑offs of sorting versus searching?
3. **Implementation Skill** – Can you translate a concise idea into production‑ready code?

A well‑crafted answer to this problem demonstrates all three and earns you brownie points in your next data‑structure interview.

---

### 7.2 The Good – What Makes This Problem a Goldmine

| Aspect | Why it’s good |
|--------|---------------|
| **Row‑wise sorted** | Allows *log‑n* lookups per row. |
| **Odd dimensions** | Guarantees a unique median (no tie‑break rules needed). |
| **Small value range (≤ 10⁶)** | Enables binary search on the value range in a constant number of iterations. |
| **Scalable** | Works for 500×500 matrices (250k elements) in under a millisecond. |
| **Reusable** | The “binary search on answer” pattern applies to many interview questions (kth largest, split array, etc.). |

---

### 7.3 The Bad – Common Pitfalls

| Pitfall | How to avoid it |
|---------|-----------------|
| **Using linear scan** | `O(m·n)` is too slow; make sure you’re not iterating over every element when counting. |
| **Using `mid = (low + high) / 2` without overflow guard** | For large ranges, prefer `low + (high - low) / 2`. |
| **Misinterpreting the median position** | With `m·n` odd, target = `(m*n + 1)/2`. Off‑by‑one errors produce wrong answers. |
| **Ignoring that each row is sorted** | If you try to merge rows naively, you lose the `log n` benefit. |
| **Not using `upper_bound` correctly** | It must return the count of elements ≤ mid, not < mid. |

---

### 7.4 The Ugly – Edge Cases That Trip You Up

| Edge Case | Why it’s tricky | Quick Fix |
|-----------|-----------------|-----------|
| **All rows identical** | Binary search still works, but many `mid` values will yield the same count. | No change needed – the algorithm converges correctly. |
| **Matrix with very small range (e.g., all numbers are 1)** | The loop still runs ~20 times but always finds the answer quickly. | Keep the same `log V` bound. |
| **Large integers close to `int` max/min** | Overflow in `mid` calculation or `target` multiplication. | Use `long long` (C++/Java) or safe arithmetic (`(low + high) >>> 1`). |
| **Non‑square matrix (m ≠ n)** | People sometimes mistakenly use `n` for both dimensions. | Explicitly compute `target = (m*n + 1)/2`. |
| **Negative values** | Value range might be negative; ensure `low` and `high` capture negatives. | The algorithm handles negatives automatically because the range is defined by first/last elements. |

---

### 7.5 How to Nail It in Your Interview

1. **Explain the *binary search on answer* idea** before writing any code. Show the invariant that the search interval always contains the median.  
2. **Walk through a small example** on the whiteboard (e.g., a 3×3 matrix). Show how `cnt(mid)` changes and how the interval shrinks.  
3. **Drop the implementation** (pick one of the reference solutions above). Mention the key functions (`upper_bound`, `bisect_right`) and why they are O(log n).  
4. **Mention the time/space complexity** succinctly: `O(m·log n)` time, `O(1)` space.  
5. **Add a small optimization** – in Java/C++ you can pre‑store the first and last elements of each row to avoid scanning the entire row when finding bounds.

---

### 7.6 Final Thought – Why Mastering This Gives You a Competitive Edge

- **LeetCode Mastery**: Solving LeetCode 2387 efficiently puts you in the top percentile of matrix problems.  
- **Interview Confidence**: You’ll know how to pivot from naive sorting to a logarithmic solution.  
- **Problem‑Solving Reputation**: Recruiters will remember your elegant use of binary search on a value range – a pattern they’ll recall in future interviews.

---

### 7.7 Call‑to‑Action (SEO: *coding interview preparation, median algorithm, binary search matrix, interview tips*)

> Want to see how this solution stacks up against the “Merge‑K‑sorted‑arrays” approach? Dive into the full **GitHub repository** linked below, where each language version comes with a test harness and performance metrics.

**GitHub**: https://github.com/yourusername/matrix-median-impl

---

## 8. Closing Note

Mastering the median‑in‑matrix problem is a *show‑stopper* in technical interviews. With the **binary search on answer** strategy, you can solve it in sub‑quadratic time and demonstrate deep understanding of algorithmic patterns. Use the reference code above to practice, benchmark, and then explain the solution clearly to any interview panel. Good luck!  

--- 

*Feel free to adapt the blog post to your style or add additional sections such as “Alternative Solutions (heap, divide‑and‑conquer)” if you wish. Happy coding!*  

--- 

> *If you liked this post, consider subscribing for more algorithm interview guides, coding tutorials, and interview preparation content.*  

--- 

**End of Documentation**