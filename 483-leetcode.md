---
title: LeetCode 483. Smallest Good Base - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 483. Smallest Good Base  
**Hard – Interview‑Style Problem**  
**Keywords:** Smallest Good Base, LeetCode 483, base‑conversion, binary search, integer root, algorithm interview

---

### 1.  Problem Recap  

Given an integer `n` (as a string) with  
`3 ≤ n ≤ 10^18`, find the **smallest base** `k (k ≥ 2)` such that the representation of `n` in base `k` consists solely of `1`’s.  
For example  

* `n = 13` → base `3` → `111`  
* `n = 4681` → base `8` → `11111`  
* `n = 10^18` → base `999999999999999999` → `11`

If no base other than `n‑1` works, the answer is `n‑1` (because `n` in base `n‑1` is always `11`).

---

### 2.  Core Insight  

For a base `k` and a representation length `m`  
```
n = 1 + k + k^2 + … + k^(m-1)
```
The right‑hand side is a geometric series:

```
n = (k^m – 1) / (k – 1)
```

So for a fixed `m` we only need to find an integer `k` that satisfies this equation.  
- The larger the `m`, the smaller the possible `k`.  
- The maximum useful `m` is `⌊log2(n)⌋ + 1` (because `k ≥ 2`).

Therefore the search strategy is:  
1. Iterate `m` from the largest possible value down to `3`.  
2. For each `m` use binary search to find a candidate `k`.  
3. Verify the candidate by computing the sum safely (watch out for overflow).  
4. The first match is the smallest base.

If no match is found, return `n-1`.

---

### 3.  Complexity  

* **Time**:  
  - `O(log n)` iterations of `m`.  
  - For each `m`, binary search takes `O(log n)` steps.  
  - Each step evaluates the sum in `O(m)` (but `m ≤ 60`).  
  Overall `O((log n)^2)` which is negligible for `n ≤ 10^18`.

* **Space**: `O(1)` auxiliary.

---

### 4.  Reference Implementations  

Below are clean, production‑ready solutions in **Java, Python, and C++**.  
All handle overflow safely and run in a fraction of a millisecond.

---

#### 4.1  Java

```java
import java.math.BigInteger;

public class Solution {
    public String smallestGoodBase(String nStr) {
        long n = Long.parseLong(nStr);
        if (n == 3) return "2";                     // special case

        // max possible length of the representation
        int maxM = (int) (Math.log(n) / Math.log(2)) + 1;

        for (int m = maxM; m >= 3; --m) {
            long k = binarySearchK(n, m);
            if (k != -1) return Long.toString(k);
        }
        return Long.toString(n - 1);                // fallback
    }

    private long binarySearchK(long n, int m) {
        long low = 2, high = (long) Math.pow(n, 1.0 / (m - 1)) + 1;
        while (low <= high) {
            long mid = low + (high - low) / 2;
            long sum = computeSum(mid, m, n);
            if (sum == n) return mid;
            if (sum < n) low = mid + 1;
            else high = mid - 1;
        }
        return -1;
    }

    // computes 1 + k + k^2 + … + k^(m-1) with overflow guard
    private long computeSum(long k, int m, long limit) {
        long sum = 1;
        long term = 1;
        for (int i = 1; i < m; ++i) {
            if (term > limit / k) return limit + 1; // overflow → > limit
            term *= k;
            if (sum > limit - term) return limit + 1;
            sum += term;
        }
        return sum;
    }
}
```

**Why this is safe**  
- The helper `computeSum` stops early if an overflow would happen (`term > limit / k`).  
- Binary search bounds are derived from the integer `m`‑th root of `n`, guaranteeing the search space is minimal.

---

#### 4.2  Python

```python
def smallestGoodBase(n: str) -> str:
    n = int(n)
    if n == 3:
        return "2"

    max_m = n.bit_length()          # floor(log2(n)) + 1
    for m in range(max_m, 2, -1):   # m >= 3
        low, high = 2, int(n ** (1.0 / (m - 1))) + 1
        while low <= high:
            mid = (low + high) // 2
            # compute 1 + mid + mid^2 + ... + mid^(m-1)
            total, term = 1, 1
            for _ in range(1, m):
                term *= mid
                total += term
            if total == n:
                return str(mid)
            if total < n:
                low = mid + 1
            else:
                high = mid - 1
    return str(n - 1)
```

Python’s built‑in arbitrary precision integers mean we never overflow.

---

#### 4.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string smallestGoodBase(string nStr) {
        unsigned long long n = stoull(nStr);
        if (n == 3) return "2";

        int maxM = log2(n) + 1;
        for (int m = maxM; m >= 3; --m) {
            unsigned long long k = binarySearch(n, m);
            if (k != 0) return to_string(k);
        }
        return to_string(n - 1);
    }

private:
    // binary search for base k
    unsigned long long binarySearch(unsigned long long n, int m) {
        unsigned long long low = 2;
        unsigned long long high = pow(n, 1.0 / (m - 1)) + 1;
        while (low <= high) {
            unsigned long long mid = low + (high - low) / 2;
            if (check(mid, m, n)) return mid;
            else if (sum(mid, m, n) < n) low = mid + 1;
            else high = mid - 1;
        }
        return 0;
    }

    // check if mid^m-1 == n*(mid-1)+1  (i.e., the geometric sum equals n)
    bool check(unsigned long long k, int m, unsigned long long n) {
        __int128 sum = 1, term = 1;
        for (int i = 1; i < m; ++i) {
            term *= k;
            sum += term;
            if (sum > n) return false;
        }
        return sum == n;
    }

    unsigned long long sum(unsigned long long k, int m, unsigned long long limit) {
        __int128 sum = 1, term = 1;
        for (int i = 1; i < m; ++i) {
            term *= k;
            sum += term;
            if (sum > limit) return limit + 1;
        }
        return (unsigned long long)sum;
    }
};
```

C++ uses `__int128` for the intermediate sum, so we avoid overflow up to `10^18`.

---

### 5.  The Good, the Bad, and the Ugly  

| Category | What worked | What made me careful | How we fixed it |
|----------|-------------|----------------------|-----------------|
| **Good** | *Geometric series* reduces a complex base‑check to a single equation. | • Binary search over `k` gives `O(log n)` per `m`. | • Iterate `m` from large to small to stop early. |
| **Bad** | *Large exponent* may overflow `long`/`int`. | • Calculating `k^m` naively can exceed 64‑bit. | • Use safe multiplication checks or `__int128`/`BigInteger`. |
| **Ugly** | *Edge cases* like `n = 3` or `n` being a perfect power of 2. | • The fallback `n-1` must be returned only when no other base works. | • Handle `n==3` separately; always return `n-1` if loop fails. |

---

### 6.  Why This Solution Rocks in an Interview  

1. **Math‑y, Not Brute‑Force** – Interviewers love seeing you derive the formula `n = (k^m – 1)/(k – 1)` instead of trying every base.  
2. **Efficient and Precise** – The algorithm runs in microseconds and never overflows if written carefully.  
3. **Showcases Multiple Skills**  
   * Big‑Integer handling (Python, Java BigInteger).  
   * Binary search + integer root estimation.  
   * Careful overflow checks (`__int128` in C++).  
4. **Clean Code** – The logic is split into small helper methods (`binarySearchK`, `computeSum`, etc.), making it easy to reason about.  

---

### 7.  SEO‑Optimized Takeaway  

- **Title:** *LeetCode 483 – Smallest Good Base: Java, Python, C++ Solutions + Interview Tips*  
- **Meta‑Description:** *Master LeetCode 483 (Smallest Good Base) with clean Java, Python, and C++ code. Learn the geometric‑series trick, binary search, and how to avoid overflow. Great interview prep.*  
- **Key Phrases:**  
  * LeetCode 483  
  * Smallest Good Base solution  
  * Java base conversion algorithm  
  * Python integer root binary search  
  * C++ __int128 overflow handling  
  * Coding interview base‑conversion  

Include these tags in your blog post header, anchor texts, and image alt‑text.  

---

### 8.  Final Thoughts  

The “Smallest Good Base” problem is a perfect example of how a solid mathematical insight can turn a seemingly hard “search” problem into a clean, efficient algorithm.  
When you present it in an interview, highlight the steps:  
1. Recognize the geometric sum.  
2. Reduce the search space.  
3. Apply binary search over integer roots.  
4. Tidy up edge cases and overflow.

With the reference code above, you’re ready to ace this LeetCode question—and impress hiring managers with your algorithmic maturity. Happy coding!