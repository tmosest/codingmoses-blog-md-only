---
title: LeetCode 3574. Maximize Subarray GCD Score - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3574. Maximize Subarray GCD Score  
**Hard** | 1500 ≤ n ≤ 1500 | 1 ≤ nums[i] ≤ 10⁹ | 1 ≤ k ≤ n  

> You may double any element at most once, but you can perform at most **k** such operations.  
> A sub‑array score = (length of sub‑array) × (GCD of the sub‑array).  
> Return the maximum score achievable after at most **k** doublings.

---

### 1. Intuition

* Doubling an element only multiplies it by 2.  
* The GCD of a sub‑array can only be increased by factors of 2 – everything else is already shared by all elements.  
* Let’s factor every number as  

  \[
  nums[i] = 2^{e_i}\; \cdot\; a_i, \qquad a_i\ \text{odd}
  \]

  `e_i` is the exponent of the lowest set bit (`__builtin_ctz` in C++, `Integer.numberOfTrailingZeros` in Java, `x & -x` trick in Python).  
  The odd part `a_i` cannot be changed by doubling; only the exponent `e_i` can grow by 1 if we double the element.

* For a fixed sub‑array `[l … r]` the GCD of the odd parts is a single odd number `g`.  
  The GCD of the whole sub‑array is then  

  \[
  GCD = g \; \cdot\; 2^{\min\{e_l,\dots,e_r\}}
  \]

  Doubling an element that already has the *minimum* exponent will increase the GCD by another factor 2.  
  Doubling any other element does not change the GCD because the minimum exponent stays the same.

* Therefore, for a sub‑array we only care about

  1. **`g`** – gcd of the odd parts.  
  2. **`minE`** – smallest exponent among the elements.  
  3. **`cntMin`** – how many elements in the sub‑array have exponent `minE`.

  If `cntMin ≤ k`, we may double all those `cntMin` elements → GCD is doubled.

---

### 2. Algorithm

```
best = 0
for l = 0 … n-1:
    g = 0
    minE = INF
    cntMin = 0
    for r = l … n-1:
        g   = gcd(g, a[r])          // a[r] = nums[r] >> e[r]
        e   = exponent[r]           // trailing zeros of nums[r]

        if e < minE:
            minE = e
            cntMin = 1
        else if e == minE:
            cntMin += 1

        len   = r - l + 1
        base  = len * g * (1LL << minE)
        best  = max(best, base)

        if cntMin <= k:
            best  = max(best, base * 2)   // we can double all min‑exponent elements
return best
```

* **Time complexity** – Two nested loops → **O(n²)** (≤ 2 · 10⁶ iterations for n = 1500).  
* **Space complexity** – O(1) extra (only a few integers / longs).  
* All arithmetic fits in 64‑bit signed (`long` in Java / C++ / Python’s int).

---

### 3. Correctness Proof  

We prove that the algorithm returns the maximum achievable GCD score.

#### Lemma 1  
For any sub‑array `S`, let `g` be the GCD of its odd parts, `minE` the minimum exponent, and `cntMin` the number of elements attaining `minE`.  
The GCD of `S` without any doubling is `g · 2^{minE}`.

*Proof.*  
Every element can be written as `2^{e_i} · a_i` with odd `a_i`.  
The GCD of the odd parts is `g`.  
The GCD of the powers of two is the smallest exponent, `minE`.  
Thus the GCD is their product. ∎



#### Lemma 2  
If we double all `cntMin` elements that have exponent `minE`, the GCD of `S` becomes  
`g · 2^{minE+1}`; doubling any other element cannot increase the GCD.

*Proof.*  
Doubling an element with exponent `e_i` increases it to `2^{e_i+1}·a_i`.  
If `e_i > minE`, the minimum exponent among the elements remains `minE`, so the GCD stays `g·2^{minE}`.  
If `e_i = minE`, the new minimum becomes `minE+1`, so the GCD increases exactly by a factor 2. ∎



#### Lemma 3  
For any sub‑array `S`, the maximum GCD obtainable after at most `k` doublings is

```
score(S) = len(S) * g * 2^{minE}           if cntMin > k
score(S) = len(S) * g * 2^{minE+1}         if cntMin ≤ k
```

*Proof.*  
If `cntMin > k`, we cannot double all minimal‑exponent elements, so the best we can do is keep the GCD as in Lemma 1.  
If `cntMin ≤ k`, we can double all those elements (using at most `cntMin ≤ k` operations), achieving the larger GCD by Lemma 2.  
No other combination of doublings can increase the GCD further because all other elements have exponent ≥ `minE`. ∎



#### Theorem  
The algorithm outputs the maximum GCD score achievable after at most `k` doublings.

*Proof.*  
During the double loop, every possible sub‑array `[l…r]` is examined exactly once.  
For that sub‑array the algorithm computes `base = len · g · 2^{minE}` (Lemma 1).  
If `cntMin ≤ k`, it also evaluates `base·2` (Lemma 2).  
By Lemma 3 these are precisely the best scores attainable for that sub‑array.  
The variable `best` keeps the maximum of all those scores, so after all sub‑arrays are processed `best` equals the global optimum. ∎



---

### 4. Implementation

Below are full, compilable solutions in **Java**, **Python**, and **C++**.

---

#### 4.1 Java

```java
import java.util.*;

public class Solution {
    // GCD for int – works for all values <= 1e9
    private static int gcd(int a, int b) {
        while (b != 0) {
            int t = a % b;
            a = b;
            b = t;
        }
        return a;
    }

    public long maxGCDScore(int[] nums, int k) {
        int n = nums.length;
        // Pre‑compute exponents and odd parts
        int[] exp = new int[n];
        int[] odd = new int[n];
        for (int i = 0; i < n; i++) {
            int e = Integer.numberOfTrailingZeros(nums[i]); // exponent of lowest set bit
            exp[i] = e;
            odd[i] = nums[i] >>> e;          // shift right by e bits
        }

        long best = 0;

        for (int l = 0; l < n; l++) {
            int g = 0;
            int minE = Integer.MAX_VALUE;
            int cntMin = 0;

            for (int r = l; r < n; r++) {
                g = gcd(g, odd[r]);

                int e = exp[r];
                if (e < minE) {
                    minE = e;
                    cntMin = 1;
                } else if (e == minE) {
                    cntMin++;
                }

                long len = r - l + 1;
                long base = len * g * (1L << minE);
                best = Math.max(best, base);

                if (cntMin <= k) {
                    best = Math.max(best, base * 2);  // double all minimal elements
                }
            }
        }

        return best;
    }
}
```

*Compile & run:*  
```bash
javac Solution.java
java Solution   // call maxGCDScore(...) from a test harness
```

---

#### 4.2 Python 3

```python
import sys
from math import gcd
from typing import List

class Solution:
    def maxGCDScore(self, nums: List[int], k: int) -> int:
        n = len(nums)

        # Pre‑compute exponent of lowest set bit and odd part
        exp = [0] * n
        odd = [0] * n
        for i, val in enumerate(nums):
            e = (val & -val).bit_length() - 1  # trailing zeros via x & -x
            exp[i] = e
            odd[i] = val >> e

        best = 0

        for l in range(n):
            g = 0
            minE = n + 1   # sentinel
            cntMin = 0

            for r in range(l, n):
                g = gcd(g, odd[r])

                e = exp[r]
                if e < minE:
                    minE = e
                    cntMin = 1
                elif e == minE:
                    cntMin += 1

                length = r - l + 1
                base = length * g * (1 << minE)
                best = max(best, base)

                if cntMin <= k:
                    best = max(best, base * 2)

        return best
```

---

#### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, k;
    if (!(cin >> n >> k)) return 0;
    vector<long long> nums(n);
    for (int i = 0; i < n; ++i) cin >> nums[i];

    // Pre‑compute exponents and odd parts
    vector<int> exp(n);
    vector<int> odd(n);
    for (int i = 0; i < n; ++i) {
        int e = __builtin_ctzll(nums[i]); // exponent of lowest set bit
        exp[i] = e;
        odd[i] = nums[i] >> e;            // odd part
    }

    long long best = 0;

    for (int l = 0; l < n; ++l) {
        int g = 0;
        int minE = INT_MAX;
        int cntMin = 0;

        for (int r = l; r < n; ++r) {
            g = std::gcd(g, odd[r]);

            int e = exp[r];
            if (e < minE) {
                minE = e;
                cntMin = 1;
            } else if (e == minE) {
                ++cntMin;
            }

            long long len  = r - l + 1;
            long long base = len * g * (1LL << minE);
            best = max(best, base);

            if (cntMin <= k) {
                best = max(best, base * 2);   // double all min‑exponent elements
            }
        }
    }

    cout << best << '\n';
    return 0;
}
```

All three programs have **O(n²)** runtime and **O(1)** auxiliary memory, satisfying the limits.

---

## 5. Blog‑Style Discussion

### 5.1 Problem Recap

> *You can double at most **k** elements (once per element) and you want the highest `(length × GCD)` of any sub‑array.*

The key twist is that only powers of two can be affected by a doubling; all other common factors are already present in the GCD.

### 5.2 Why Two Nested Loops Are Good Enough

With `n ≤ 1500` the total number of sub‑arrays is  

\[
\frac{n(n+1)}{2} \le 1\,125\,750
\]

Even with the inner GCD calculation (constant time) this is far below one second in modern CPUs.  
A brute‑force search that recomputes the GCD from scratch for each sub‑array would be *O(n³)* and would TLE – our clever incremental GCD keeps it *O(n²)*.

### 5.3 Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Using 32‑bit integers for the final product (`len * g * 2^minE`) can overflow | Use `long` (Java) / `long long` (C++) / Python’s arbitrary‑precision int |
| Forgetting to shift the *odd* part correctly | `odd[i] = nums[i] >> e[i]` (Java/C++) or `odd[i] = nums[i] // (1 << e[i])` (Python) |
| Using `__builtin_ctz` on zero | `nums[i]` is always ≥ 1, so safe. |
| Mis‑counting `cntMin` (should reset when a new smaller exponent appears) | Reset `cntMin = 1` when `e < minE`. |

### 5.4 Complexity Summary

| Step | Time | Space |
|------|------|-------|
| Pre‑processing exponents & odd parts | O(n) | O(1) |
| Two nested loops | O(n²) | O(1) |
| Overall | **O(n²)** | **O(1)** |

For the worst case `n = 1500` this is only about 2 × 10⁶ inner iterations – easily fast enough.

### 5.5 Why the Solution Is Elegant

* We never touch the original array; we work on its factored representation.  
* The entire problem reduces to keeping track of the **minimum exponent** in a sliding window.  
* Doubling is treated as a *conditional* multiplication by 2 – no need for a complicated DP.

### 5.6 SEO & Tags

- **LeetCode 3574**  
- **Maximize GCD score**  
- **Doubling operations**  
- **Array GCD**  
- **O(n²) solution**  
- **Two‑pointer GCD trick**  
- **C++ O(n²) GCD**  
- **Java 64‑bit integer**  

---

**Happy coding!**