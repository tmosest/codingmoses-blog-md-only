---
title: LeetCode 483. Smallest Good Base - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 The “Smallest Good Base” – 483 | LeetCode | Hard  
> Master this problem and show recruiters you’re a coding‑interview wizard.  

---

## TL;DR  
- **Goal:** Find the smallest base \(k \ge 2\) such that the decimal number **n** can be expressed as `111…1` (all 1’s) in base‑\(k\).  
- **Range:** \(3 \le n \le 10^{18}\).  
- **Answer:** \(k = 999999999999999999\) for \(n = 10^{18}\).  
- **Complexity:** \(O(\log^2 n)\) – fast enough for 1e18.  
- **Languages:** Java, Python, C++ (fully commented).  

---

## 1. Why this problem rocks (and why it’s hard)

| Good | Bad | Ugly |
|------|-----|------|
| **Good** – It tests your knowledge of number theory, binary search, and careful overflow handling. | **Bad** – It forces you to think about *why* a base that produces all 1’s must satisfy a specific formula. | **Ugly** – Many naive solutions blow up on the upper bound or overflow 64‑bit integers. |

### Key Insight  
If a number `n` can be written as `111…1` (m+1 digits) in base `k`, then  

\[
n = 1 + k + k^2 + \dots + k^m = \frac{k^{m+1} - 1}{k-1}
\]

Thus for each possible digit length `m+1` we only need to find an integer `k` that satisfies this equation.  
The maximum length is bounded by `log2(n)` (~60 for 1e18), so we only iterate a few dozen times.

---

## 2. Algorithm Walk‑Through

1. **Handle the trivial case** – If `m = 1`, the representation is “11”, so `k = n-1`.  
2. **Loop over possible lengths** `m` from `log2(n)` down to `2`.  
   - `m` is the *exponent* of the highest power of `k`.  
   - For each `m`, we **search** for an integer `k` that satisfies the formula.  
3. **Binary search for k**  
   - Lower bound `lo = 2`.  
   - Upper bound `hi = pow(n, 1/m) + 1` (guaranteed to contain the answer).  
   - While `lo <= hi`:  
     * mid = (lo+hi)/2*  
     * Compute `value = 1 + mid + mid² + … + mid^m` (stop early if it exceeds `n` to avoid overflow).  
     * If `value == n`, we found the smallest base.  
     * If `value < n`, search higher; otherwise lower.
4. If no `k` is found for any `m`, return `n-1` (the base that makes the representation “11”).

### Why we go **downwards** for `m`?  
The smaller the base, the longer the representation. We want the *smallest* base, which corresponds to the *longest* representation. Starting from the maximum possible `m` guarantees we find the minimal base first.

---

## 3. Complexity Analysis  

| Step | Time | Reason |
|------|------|--------|
| Loop over `m` | `O(log n)` | `m` ∈ [2, log₂ n] |
| Binary search per `m` | `O(log n)` | Search space shrinks with base value |
| Value calculation | `O(m)` | Summation up to `m` terms (≤ 60) |

Total: **O(log² n)**, far below one millisecond for 1e18 on modern hardware.

Memory: **O(1)** – we only store a few integers and loop variables.

---

## 4. Pitfalls & Edge Cases

| Pitfall | Fix |
|---------|-----|
| **64‑bit overflow** when computing `k^i` | Use `long` and stop if the intermediate exceeds `n`. |
| **Floating point inaccuracies** for `pow(n, 1/m)` | Do not rely on it; just use it as an upper bound (`+1`) or avoid entirely. |
| **Large `m` (e.g., 60)** | Summation will break early if value > n. |
| **n = 3** | Should return base `2` (binary “11”). |
| **n = 1000000000000000000** | Base `999999999999999999` (representation “11”). |

---

## 5. The Code – 3 Languages

> All implementations share the same logic.  
> Comments explain every step.

### 5.1 Java (LeetCode‑style)

```java
import java.math.BigInteger;

class Solution {
    public String smallestGoodBase(String nStr) {
        long n = Long.parseLong(nStr);
        // Handle the trivial base n-1 (representation "11")
        if (n == 3) return "2";

        long maxM = Long.highestOneBit(n) == n ? Long.SIZE - 1 : Long.SIZE - 2;
        // Try lengths from longest (largest m) to shortest
        for (long m = maxM; m >= 2; --m) {
            long base = findBase(n, m);
            if (base != -1) return Long.toString(base);
        }
        return Long.toString(n - 1); // fallback
    }

    private long findBase(long n, long m) {
        long lo = 2, hi = (long) Math.pow(n, 1.0 / m) + 1;
        while (lo <= hi) {
            long mid = lo + (hi - lo) / 2;
            long val = sumSeries(mid, m, n);
            if (val == n) return mid;
            if (val < n) lo = mid + 1;
            else hi = mid - 1;
        }
        return -1;
    }

    // Compute 1 + mid + mid^2 + ... + mid^m, but stop if > n
    private long sumSeries(long base, long m, long n) {
        long val = 1;
        long cur = 1;
        for (long i = 1; i <= m; ++i) {
            if (cur > n / base) return n + 1; // overflow guard
            cur *= base;
            val += cur;
            if (val > n) return n + 1;
        }
        return val;
    }
}
```

> **Note:** The code avoids `BigInteger` by carefully checking overflow. If you prefer clarity over micro‑optimization, replace the series computation with `BigInteger`.

---

### 5.2 Python 3

```python
def smallestGoodBase(n_str: str) -> str:
    n = int(n_str)
    if n == 3:
        return "2"

    # maximum exponent m such that 2^m <= n
    max_m = n.bit_length() - 1

    for m in range(max_m, 1, -1):
        base = find_base(n, m)
        if base != -1:
            return str(base)

    return str(n - 1)  # fallback


def find_base(n: int, m: int) -> int:
    """Binary search for base k that satisfies (k^(m+1)-1)/(k-1) == n."""
    lo, hi = 2, int(n ** (1.0 / m)) + 2
    while lo <= hi:
        mid = (lo + hi) // 2
        val = series_sum(mid, m, n)
        if val == n:
            return mid
        elif val < n:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1


def series_sum(base: int, m: int, limit: int) -> int:
    """Compute 1 + base + base^2 + ... + base^m with overflow guard."""
    total, cur = 1, 1
    for _ in range(m):
        if cur > limit // base:
            return limit + 1  # overflow
        cur *= base
        total += cur
        if total > limit:
            return limit + 1
    return total
```

---

### 5.3 C++ (GNU++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string smallestGoodBase(string n_str) {
        long long n = stoll(n_str);
        if (n == 3) return "2";

        int max_m = 63;                    // because 2^63 > 1e18
        for (int m = max_m; m >= 2; --m) {
            long long base = findBase(n, m);
            if (base != -1) return to_string(base);
        }
        return to_string(n - 1);
    }

private:
    long long findBase(long long n, int m) {
        long long lo = 2, hi = pow(n, 1.0 / m) + 1;
        while (lo <= hi) {
            long long mid = lo + (hi - lo) / 2;
            long long val = seriesSum(mid, m, n);
            if (val == n) return mid;
            if (val < n) lo = mid + 1;
            else hi = mid - 1;
        }
        return -1;
    }

    long long seriesSum(long long base, int m, long long limit) {
        long long total = 1, cur = 1;
        for (int i = 0; i < m; ++i) {
            if (cur > limit / base) return limit + 1;   // overflow guard
            cur *= base;
            total += cur;
            if (total > limit) return limit + 1;
        }
        return total;
    }
};
```

---

## 6. Interview Take‑away

- **Showcase**:  
  *Number theory + binary search*  
  *Overflow awareness*  
  *Efficient use of logarithms to bound search space*  
- **Talk through**:  
  1. The derived formula.  
  2. Why we iterate over the exponent `m`.  
  3. How to compute the series without overflow.  
  4. Edge cases like `n = 3` and `n = 10^18`.  

---

## 7. SEO‑Optimized Meta‑Description

> “LeetCode 483 – Smallest Good Base” explained in Java, Python, and C++. Discover the math behind the solution, step‑by‑step code, and interview insights. Perfect for coding interview prep and algorithm practice.

---

## 8. Wrap‑Up

The “Smallest Good Base” problem is a *beautiful* blend of math and coding. By treating the problem as a search for a base that satisfies a geometric series, we avoid brute‑force enumeration and stay comfortably within the 64‑bit limit. Mastering this trick shows recruiters you can **translate a mathematical insight into clean, efficient code** – a highly sought skill in technical interviews.

Good luck, and may your interview answers always be as clean as a “111…1” representation! 🚀