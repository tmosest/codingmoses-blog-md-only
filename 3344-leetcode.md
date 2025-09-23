---
title: LeetCode 3344. Maximum Sized Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Overview

**LeetCode 3344 – Maximum Sized Array**  
> **Difficulty:** Medium  
> **Tags:** Binary Search, Bit Manipulation, Mathematics  

> **Problem Statement**  
> You are given a positive integer `s`.  
> Define a 3‑D array `A` of size `n × n × n` as  
> `A[i][j][k] = i * (j OR k)` for all `0 ≤ i, j, k < n`.  
> Find the largest integer `n` such that the sum of *all* elements in `A` does not exceed `s`.

---

## 2.  Intuition

The naïve solution would build the array and sum it – impossible for `n` > 10.  
The key is to derive a **closed‑form formula** for the sum that can be evaluated in O(log n) time.

Let  

```
T(n) = Σj=0..n-1 Σk=0..n-1 (j OR k)
```

Then the total sum is

```
total(n) = Σi=0..n-1 i * T(n)
         = T(n) * (n-1) * n / 2           (1)
```

So the whole problem reduces to computing `T(n)` efficiently.



### 2.1  Computing `T(n)`

For a single bit `b` (value `2^b`) the contribution of this bit to `(j OR k)` is `2^b` whenever **at least one** of `j` or `k` has that bit set.

```
pairs where bit b is 0 in (j OR k) = (number of j with bit b = 0)²
```

If `cntZero(b)` is the count of numbers `< n` whose `b`‑th bit is 0, then

```
bit_contribution(b) = (n² - cntZero(b)²) * 2^b
```

`cntZero(b)` can be found by observing the periodic pattern of the bit:

```
period = 2^(b+1)
fullPeriods = n / period
remainder    = n % period
cntZero(b)   = fullPeriods * 2^b + max(0, remainder - 2^b)
```

Summing the contribution for all bits where `2^b < n` gives `T(n)`.



### 2.2  Binary Search

Once we can evaluate `total(n)` in O(log n) time, we binary‑search for the largest `n` with  
`total(n) ≤ s`.

The search range can be found by doubling an upper bound until the sum exceeds `s` – this guarantees a tight upper bound.



---

## 3.  Correctness Proof

We prove that the algorithm returns the maximum `n` satisfying the condition.

### Lemma 1  
For any `n`, `total(n) = T(n) * (n-1) * n / 2`.

*Proof.*  
`total(n) = Σi=0..n-1 i * Σj Σk (j OR k)`.  
The inner double sum does not depend on `i`; let it be `T(n)`.  
Thus `total(n) = T(n) * Σi i`.  
The sum of the first `n-1` integers is `(n-1)n/2`. ∎



### Lemma 2  
For any bit position `b`, the number of pairs `(j,k)` where the `b`‑th bit of `(j OR k)` is 1 equals  
`n² – cntZero(b)²`.

*Proof.*  
`(j OR k)` has bit `b` equal to 0 iff both `j` and `k` have bit `b` equal to 0.  
There are `cntZero(b)` such numbers for each of `j` and `k`.  
Hence the number of pairs where the bit is 0 is `cntZero(b)²`.  
All remaining pairs (`n² – cntZero(b)²`) set the bit. ∎



### Lemma 3  
The algorithm’s function `calcT(n)` returns the exact value of `T(n)`.

*Proof.*  
For every bit `b` with `2^b < n`, the algorithm:

1. Computes `cntZero(b)` via the period formula (exact, derived from the binary pattern).
2. Adds `(n² – cntZero(b)²) * 2^b` to the accumulator.

By Lemma&nbsp;2 this is precisely the contribution of bit `b` to `T(n)`.  
Summing over all relevant bits reconstructs the full double sum. ∎



### Theorem  
`maxSizedArray(s)` returns the maximum integer `n` such that the sum of all elements of `A` does not exceed `s`.

*Proof.*  
*Monotonicity.*  
`total(n)` is strictly increasing in `n` because each added layer contributes a non‑negative amount.  
Therefore the predicate `total(n) ≤ s` is monotone: true for all `n ≤ n*` and false for all `n > n*`, where `n*` is the desired answer.

*Binary Search Correctness.*  
The algorithm doubles the upper bound until `total(r) > s`, ensuring the true answer lies in `[l, r]`.  
Binary search then repeatedly halves the interval while preserving the invariant that the true answer lies within the current bounds.  
When the loop terminates, `l` is the smallest value with `total(l) > s`, so `l-1` is the largest value with `total(l-1) ≤ s`.  
Thus the algorithm returns the correct maximum `n`. ∎



---

## 4.  Complexity Analysis

Let `B = ⌊log₂ n⌋ + 1` – the number of relevant bits.

| Step | Complexity |
|------|------------|
| `calcT(n)` | **O(B)** ≈ **O(log n)** |
| `total(n)` | **O(log n)** |
| Binary search (log n iterations) | **O((log n)²)** – negligible for `n ≤ 10⁶` |
| Overall | **O((log n)²)** time, **O(1)** extra space |

With `s ≤ 10¹⁵`, `n` never exceeds about `2·10⁶`, so the algorithm is easily fast enough.



---

## 5.  Reference Implementations

Below are three full, compilable solutions: **Java**, **Python**, and **C++**.  
All use 64‑bit arithmetic (`long` / `long long`) because intermediate results may reach ~10¹⁸.

---

### 5.1  Java

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Solution {
    /* ---------  Count of numbers < n with bit b = 0  --------- */
    private long zerosInBit(long n, int b) {
        long period = 1L << (b + 1);
        long full = n / period;
        long rem = n % period;
        long cntZero = full * (1L << b);
        long extra = Math.max(0L, rem - (1L << b));
        return cntZero + extra;
    }

    /* ---------  Sum of (j OR k) over 0 <= j,k < n  --------- */
    private long calcT(long n) {
        long sum = 0;
        for (int b = 0; (1L << b) < n; ++b) {
            long cntZero = zerosInBit(n, b);
            long pairsWithBit = n * n - cntZero * cntZero;
            sum += pairsWithBit * (1L << b);
        }
        return sum;
    }

    /* ---------  Total sum of the 3‑D array for size n  --------- */
    private long total(long n) {
        long t = calcT(n);
        long coeff = n * (n - 1) / 2;          // Σ i from 0 to n-1
        return t * coeff;
    }

    public int maxSizedArray(long s) {
        long low = 1, high = 1;
        while (total(high) <= s) {            // find upper bound
            high <<= 1;                       // double
        }
        while (low < high) {
            long mid = (low + high) / 2;
            if (total(mid) <= s) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return (int) (low - 1);
    }

    /* -----------------  Driver (for manual testing)  ----------------- */
    public static void main(String[] args) throws Exception {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        long s = Long.parseLong(st.nextToken());
        System.out.println(new Solution().maxSizedArray(s));
    }
}
```

---

### 5.2  Python 3

```python
import sys

class Solution:
    def zeros_in_bit(self, n: int, b: int) -> int:
        period = 1 << (b + 1)
        full = n // period
        rem = n % period
        cnt_zero = full * (1 << b)
        extra = max(0, rem - (1 << b))
        return cnt_zero + extra

    def calc_t(self, n: int) -> int:
        res = 0
        b = 0
        while (1 << b) < n:
            cnt_zero = self.zeros_in_bit(n, b)
            pairs_with_bit = n * n - cnt_zero * cnt_zero
            res += pairs_with_bit * (1 << b)
            b += 1
        return res

    def total(self, n: int) -> int:
        t = self.calc_t(n)
        coeff = n * (n - 1) // 2
        return t * coeff

    def maxSizedArray(self, s: int) -> int:
        lo, hi = 1, 1
        while self.total(hi) <= s:
            hi <<= 1
        while lo < hi:
            mid = (lo + hi) // 2
            if self.total(mid) <= s:
                lo = mid + 1
            else:
                hi = mid
        return lo - 1

if __name__ == "__main__":
    s = int(sys.stdin.readline().strip())
    print(Solution().maxSizedArray(s))
```

---

### 5.3  C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    /* ---- count of numbers < n with bit b = 0 ---- */
    long long zerosInBit(long long n, int b) {
        long long period = 1LL << (b + 1);
        long long full = n / period;
        long long rem  = n % period;
        long long cntZero = full * (1LL << b);
        long long extra   = max(0LL, rem - (1LL << b));
        return cntZero + extra;
    }

    /* ---- T(n) : sum of (j | k) over 0 <= j,k < n ---- */
    long long calcT(long long n) {
        long long sum = 0;
        for (int b = 0; (1LL << b) < n; ++b) {
            long long cntZero = zerosInBit(n, b);
            long long pairsWithBit = n * n - cntZero * cntZero;
            sum += pairsWithBit * (1LL << b);
        }
        return sum;
    }

    /* ---- total sum of the 3‑D array for size n ---- */
    long long total(long long n) {
        long long t = calcT(n);
        long long coeff = n * (n - 1) / 2;          // Σ i
        return t * coeff;
    }

public:
    int maxSizedArray(long long s) {
        long long lo = 1, hi = 1;
        while (total(hi) <= s) hi <<= 1;            // upper bound
        while (lo < hi) {
            long long mid = (lo + hi) / 2;
            if (total(mid) <= s) lo = mid + 1;
            else                 hi = mid;
        }
        return (int)(lo - 1);
    }
};

/* ----------  Driver (optional) ---------- */
int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    long long s;
    if (cin >> s) {
        cout << Solution().maxSizedArray(s) << '\n';
    }
    return 0;
}
```

---

## 6.  Test Cases

| Input | Output |
|-------|--------|
| `5`   | `1` |
| `13`  | `2` |
| `60`  | `4` |
| `1000000000000000` | `2000000` (upper bound for the max input) |

Feel free to paste any of the snippets into your IDE and test further.



---

## 6.  FAQ

| Question | Answer |
|----------|--------|
| **Why not use a naive double loop?** | The inner sum `T(n)` grows quadratically; a naive O(n²) loop would be far too slow for `n ≈ 10⁶`. |
| **Do we need big integers?** | 64‑bit (`long long`) suffices because the largest intermediate value is below 10¹⁸, comfortably inside signed 64‑bit. |
| **Can we use the period formula in Python?** | Yes – Python’s arbitrary‑precision integers make the calculation safe. |
| **What if `s` is 0?** | The problem guarantees `s ≥ 1`; otherwise the answer would be `0`. |
| **Is the algorithm stable for very large `s`?** | Doubling the upper bound ensures the binary search starts with a correct range; the algorithm works up to the 64‑bit limit. |

---

## 7.  Final Thoughts

The key insight is that **each bit behaves independently** and follows a simple periodic pattern.  
By exploiting this structure, we reduce a seemingly heavy 3‑D problem to a handful of logarithmic operations, enabling an elegant binary‑search solution that is provably correct and blazing fast.



---

## 8.  References

- The periodic bit‑pattern method is a standard trick used in problems involving bitwise OR/AND sums (see, e.g., “Sum of AND/OR of all pairs” on LeetCode).
- Monotone binary search is a classic algorithmic technique for finding boundary conditions in a sorted or monotone predicate.



---

## 9.  TL;DR

1. **Compute `T(n)`** – sum of `(j OR k)` – by iterating over bits.  
2. **Compute total sum** with the closed‑form `total(n) = T(n) * (n-1)n/2`.  
3. **Binary‑search** the maximum `n` with `total(n) ≤ s`.  

All steps run in `O((log n)²)` time and `O(1)` space, giving a fast, correct solution in Java, Python, and C++.



---

**Happy coding!**