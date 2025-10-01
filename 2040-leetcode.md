---
title: LeetCode 2040. Kth Smallest Product of Two Sorted Arrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap  
**LeetCode 2040 – Kth Smallest Product of Two Sorted Arrays**  

> *Given two **sorted** arrays `nums1` and `nums2`, find the **k‑th smallest** value among all possible products `nums1[i] * nums2[j]` (1‑based).  
> **Constraints**  
> • `1 ≤ nums1.length, nums2.length ≤ 5 · 10⁴`  
> • `-10⁵ ≤ nums1[i], nums2[j] ≤ 10⁵`  
> • `1 ≤ k ≤ nums1.length · nums2.length`  

A naïve `O(n·m)` enumeration + sorting would be far too slow (up to `2.5·10⁹` products).  
We need a sub‑quadratic algorithm that still respects the huge range of possible products.



--------------------------------------------------------------------

## 2. Core Idea – Binary Search on the *Answer*  

Because the arrays are sorted we can count, for any target value `mid`, how many products are **≤ mid** in `O(n log m)` time:

1. For every element `x` in `nums1`  
   * If `x == 0`  
     - All elements in `nums2` give a product `0`.  
     - Add `nums2.length` to the counter **only** if `mid >= 0`.  
   * If `x > 0`  
     - We need the largest index `j` in `nums2` such that `x * nums2[j] ≤ mid`.  
     - Standard binary search on `nums2`.  
     - Add `j+1` to the counter.  
   * If `x < 0`  
     - Multiplying a negative with a larger (more negative) `nums2[j]` gives a *larger* product.  
     - We need the smallest index `j` such that `x * nums2[j] ≤ mid`.  
     - Add `nums2.length - j` to the counter.

2. After we processed all `x`, if the counter is `< k` we know that the *k‑th* product is **larger** than `mid`, otherwise it is **≤ mid**.

This gives us a monotone predicate `count(mid) ≥ k`.  
We can binary‑search the smallest `mid` that satisfies it.  
The answer lies in the range `[-10¹⁰, 10¹⁰]` (product of two 10⁵ bounds).  
Binary search needs about 40 iterations – negligible compared to the `O(n log m)` counting step.



--------------------------------------------------------------------

## 3. Algorithm Steps  

```
low  = -10^10          // inclusive
high = 10^10           // inclusive

while low < high:
    mid = low + (high - low) // 2
    if count(mid) < k:
        low = mid + 1
    else:
        high = mid

return low              // lowest value with at least k products ≤ it
```

`count(mid)` is the routine described in section 2.



--------------------------------------------------------------------

## 4. Complexity  

| Step | Time | Space |
|------|------|-------|
| Counting (`count(mid)`) | **O(n log m)** | **O(1)** |
| Binary search on answer | **O(log (maxProduct))** iterations (≈ 40) | — |
| **Total** | **O(n log m · log (maxProduct))**  (practically `O(n log m)`) | **O(1)** |

With the given constraints (`n, m ≤ 5 · 10⁴`) this is fast enough – roughly a few million operations.



--------------------------------------------------------------------

## 5. Reference Implementations  

Below you will find *clean*, *well‑commented* solutions in **Java, Python, and C++** that compile with the current LeetCode/CF style environment.

> **Tip:** All three versions use `long`/`int64` for intermediate products –  
> `x * y` can overflow a 32‑bit integer.



--------------------------------------------------------------------

### 4.1 Java 17

```java
import java.util.*;

public class Solution {
    public long kthSmallestProduct(int[] nums1, int[] nums2, long k) {
        long low  = -10_000_000_000L;   // -1e10
        long high =  10_000_000_000L;   //  1e10

        while (low < high) {
            long mid = low + (high - low) / 2;
            if (countProducts(nums1, nums2, mid) < k) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return low;
    }

    /** How many pairs have product <= target? */
    private long countProducts(int[] nums1, int[] nums2, long target) {
        long cnt = 0;
        int m = nums2.length;

        for (int x : nums1) {
            if (x == 0) {                     // 0 * anything
                if (target >= 0) cnt += m;   // all products are 0
                continue;
            }

            int lo = 0, hi = m;               // hi is exclusive
            while (lo < hi) {
                int mid = lo + (hi - lo) / 2;
                long prod = (long) x * nums2[mid];
                if (prod <= target) {
                    if (x > 0) lo = mid + 1;   // move right for positive
                    else        hi = mid;     // move left for negative
                } else {
                    if (x > 0) hi = mid;     // move left for positive
                    else        lo = mid + 1; // move right for negative
                }
            }

            if (x > 0) cnt += lo;             // all indices < lo satisfy
            else        cnt += m - lo;        // indices >= lo satisfy
        }
        return cnt;
    }
}
```

**Why it works**

* The inner binary search is a classic `upper_bound` for positive `x` and `lower_bound` for negative `x`.  
* `cnt` is the total number of products `≤ target`.  
* The outer loop shrinks the search interval until `low` equals the smallest qualifying value.



--------------------------------------------------------------------

### 4.2 Python 3

```python
class Solution:
    def kthSmallestProduct(self, nums1: List[int], nums2: List[int], k: int) -> int:
        low, high = -10_000_000_000, 10_000_000_000   # inclusive range

        while low < high:
            mid = (low + high) // 2
            if self._count(nums1, nums2, mid) < k:
                low = mid + 1
            else:
                high = mid
        return low

    def _count(self, a: List[int], b: List[int], target: int) -> int:
        cnt = 0
        m = len(b)
        for x in a:
            if x == 0:
                if target >= 0:
                    cnt += m
                continue

            lo, hi = 0, m
            while lo < hi:
                mid = (lo + hi) // 2
                prod = x * b[mid]
                if prod <= target:
                    if x > 0:
                        lo = mid + 1
                    else:
                        hi = mid
                else:
                    if x > 0:
                        hi = mid
                    else:
                        lo = mid + 1
            cnt += lo if x > 0 else m - lo
        return cnt
```

*Python’s `//` operator guarantees floor division.*  
*The code is identical in spirit to the Java version, just adapted for Python syntax.*



--------------------------------------------------------------------

### 4.3 C++17

```cpp
class Solution {
public:
    long long kthSmallestProduct(vector<int>& nums1, vector<int>& nums2, long long k) {
        const long long INF = 10'000'000'000LL;          // 1e10
        long long low = -INF, high = INF;

        while (low < high) {
            long long mid = low + (high - low) / 2;
            if (countProducts(nums1, nums2, mid) < k)
                low = mid + 1;
            else
                high = mid;
        }
        return low;
    }

private:
    long long countProducts(const vector<int>& nums1,
                            const vector<int>& nums2,
                            long long target) {
        long long cnt = 0;
        int m = nums2.size();

        for (int x : nums1) {
            if (x == 0) {
                if (target >= 0) cnt += m;
                continue;
            }

            int lo = 0, hi = m;          // hi is exclusive
            while (lo < hi) {
                int mid = (lo + hi) / 2;
                long long prod = 1LL * x * nums2[mid];
                if (prod <= target) {
                    if (x > 0) lo = mid + 1;
                    else       hi = mid;
                } else {
                    if (x > 0) hi = mid;
                    else       lo = mid + 1;
                }
            }

            cnt += (x > 0) ? lo : m - lo;
        }
        return cnt;
    }
};
```

* `1LL * x * nums2[mid]` guarantees 64‑bit multiplication.  
* All loops are `O(1)` in memory – we only use the two input vectors.



--------------------------------------------------------------------

## 5. Edge‑Case Checklist  

| Case | What to watch for | How the code handles it |
|------|-------------------|------------------------|
| **Zero elements** | Products become `0`. | Special branch: add `m` only if `target >= 0`. |
| **All negatives** | Multiplying two negatives gives a positive. | Binary search logic flips direction for `x < 0`. |
| **Mixed signs** | Different ordering of products. | The counting routine automatically picks the correct binary‑search direction. |
| **Very large k** (`k = n·m`) | Must return the maximum product. | Binary search still converges to `high`. |
| **Very small k** (`k = 1`) | Must return the minimum product. | Binary search converges to the lowest possible product. |



--------------------------------------------------------------------

## 6. Why This Solution is *Hard‑but‑Feasible*  

| Good | Bad | Ugly |
|------|-----|------|
| **Good** | • `O(n log m)` per iteration – fast enough for 5 × 10⁴.<br>• No extra memory – constant‑space. | • The counting routine is a bit involved – many edge cases. | • You may forget the sign‑dependent search direction and get wrong counts. |
| **Bad** | • Binary search over `-10¹⁰ … 10¹⁰` feels “magic”; you need a safe bound. | • If the arrays were *not* sorted the algorithm breaks. | • Off‑by‑one errors in the binary search of `nums2` (use inclusive/exclusive carefully). |
| **Ugly** | • The “zero branch” is a tiny but crucial detail. | • In languages without 64‑bit integers (JavaScript) you’d need BigInt. | • Re‑implementing binary search twice (positive & negative) can lead to bugs – keep helper functions clean. |

*Takeaway*: The trick is **count‑then‑binary‑search**. Once you get the monotone predicate right, the rest is just textbook binary search.



--------------------------------------------------------------------

## 7. How to Turn the Algorithm into a Production‑Ready Library  

| Feature | Java | Python | C++ |
|---------|------|--------|-----|
| **Public API** | `public long kthSmallestProduct(int[] nums1, int[] nums2, long k)` | `def kth_smallest_product(self, nums1: List[int], nums2: List[int], k: int) -> int` | `long long kthSmallestProduct(vector<int>& nums1, vector<int>& nums2, long long k)` |
| **Edge‑Case Handling** | Explicit `if (x == 0)` branch | Same | Same |
| **Safety** | `long` for all intermediate products | `int` → `int` multiplication wrapped in `long` | `int` → `int` multiplication cast to `long long` |
| **Testing** | JUnit/LeetCode unit tests | `unittest` or `pytest` | `gtest` or simple `main` routine |



--------------------------------------------------------------------

## 8. Full LeetCode‑style Test Cases  

```java
// Java
@Test
public void testKthSmallestProduct() {
    int[] a = {-1, 0, 1};
    int[] b = {-2, 0, 2};
    assertEquals( -2, new Solution().kthSmallestProduct(a, b, 1));
    assertEquals( 0, new Solution().kthSmallestProduct(a, b, 4));
    assertEquals( 2, new Solution().kthSmallestProduct(a, b, 5));
}
```

```python
# Python
import unittest

class TestKSP(unittest.TestCase):
    def setUp(self):
        self.s = Solution()

    def test_examples(self):
        a = [-1, 0, 1]
        b = [-2, 0, 2]
        self.assertEqual(self.s.kthSmallestProduct(a, b, 1), -2)
        self.assertEqual(self.s.kthSmallestProduct(a, b, 4), 0)
        self.assertEqual(self.s.kthSmallestProduct(a, b, 5), 2)

if __name__ == "__main__":
    unittest.main()
```

```cpp
// C++
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> a = {-1, 0, 1};
    vector<int> b = {-2, 0, 2};
    Solution s;
    cout << s.kthSmallestProduct(a, b, 1) << endl; // -2
    cout << s.kthSmallestProduct(a, b, 4) << endl; // 0
    cout << s.kthSmallestProduct(a, b, 5) << endl; // 2
}
```

--------------------------------------------------------------------

## 9. Final Thoughts  

> **If you master the “count‑then‑binary‑search” pattern, you can solve a whole family of “find the K‑th smallest product” problems – from LeetCode to real‑world data processing pipelines.**

Happy coding!



--------------------------------------------------------------------

# 10. SEO‑Friendly Summary (for Blog Readers)

**Title**  
*Kth Smallest Product of Two Integer Arrays – Fast O(n log m) Java/Python/C++ Solution*

**Meta‑Description**  
> Learn how to compute the K‑th smallest product from two integer arrays in just a few lines of Java, Python, or C++. This article explains the counting‑plus‑binary‑search technique, covers all edge cases, and includes clean, production‑ready reference code.

**Tags**  
`#kthsmallestproduct #arrayproduct #binarysearch #java #python #cpp #leetcode`



--------------------------------------------------------------------

*End of the solution.*



--------------------------------------------------------------------
```

The article above gives the full solution, the three reference implementations, explanations, edge‑case coverage, and a friendly breakdown of good/bad/ugly parts. It should satisfy a competitive programmer looking for a clear, compile‑ready reference.