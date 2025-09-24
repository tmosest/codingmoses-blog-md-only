---
title: LeetCode 2521. Distinct Prime Factors of Product of Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem Recap  
**2521. Distinct Prime Factors of Product of Array**  
Given an array `nums` of positive integers (`2 ≤ nums[i] ≤ 1000`), return the number of **distinct** prime factors that appear in the product of all elements.  

> **Examples**  
> *Input*: `[2,4,3,7,10,6]` → **Output**: `4` (factors: 2, 3, 5, 7)  
> *Input*: `[2,4,8,16]` → **Output**: `1` (factor: 2)

The straightforward “multiply all numbers and factor the product” overflows instantly. The trick is to factor each element *individually* and merge the results into a set of primes.

---

## 📈 Why This Blog?  
- **SEO‑friendly** keywords: *leetcode 2521, distinct prime factors, array product, Java solution, Python solution, C++ solution*  
- Covers **Good**, **Bad**, and **Ugly** aspects of common approaches  
- Helps you stand out in technical interviews & coding interviews

---

## ⚙️ Optimal Solution – Sieve + Factorization

### Why it works
* The maximum possible number in the array is only 1 000, so all prime factors are ≤ 1000.  
* We pre‑compute all primes up to 1 000 with the Sieve of Eratosthenes (O(√1000)).  
* For each number we divide by each prime until it can no longer be divided.  
* A `HashSet` (Java), `set` (Python), or `unordered_set` (C++) collects the unique primes.

### Complexity
| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n * π(1000)) ≈ O(n * 168)` | `O(n * π(1000))` | `O(n * π(1000))` |
| **Space** | `O(π(1000))` (set of primes) | Same | Same |

---

## ✅ Code in Three Languages

### 1️⃣ Java – Clean & Idiomatic

```java
import java.util.*;

public class Solution {
    // Pre‑computed primes up to 1000
    private static final List<Integer> PRIMES = sieve(1000);

    private static List<Integer> sieve(int limit) {
        boolean[] isPrime = new boolean[limit + 1];
        Arrays.fill(isPrime, true);
        isPrime[0] = isPrime[1] = false;
        for (int p = 2; p * p <= limit; p++) {
            if (isPrime[p]) {
                for (int k = p * p; k <= limit; k += p) {
                    isPrime[k] = false;
                }
            }
        }
        List<Integer> primes = new ArrayList<>();
        for (int i = 2; i <= limit; i++) {
            if (isPrime[i]) primes.add(i);
        }
        return primes;
    }

    public int distinctPrimeFactors(int[] nums) {
        Set<Integer> uniquePrimes = new HashSet<>();
        for (int val : nums) {
            factor(val, uniquePrimes);
        }
        return uniquePrimes.size();
    }

    private void factor(int num, Set<Integer> result) {
        for (int p : PRIMES) {
            if (p * p > num) break;
            if (num % p == 0) {
                result.add(p);
                while (num % p == 0) num /= p;
            }
        }
        if (num > 1) { // remaining prime
            result.add(num);
        }
    }
}
```

> **Good** – Reuses a pre‑computed prime list, O(1) factor lookup.  
> **Bad** – Uses `List<Integer>` for primes; a `int[]` would be slightly faster.  
> **Ugly** – No static initializer for the sieve; each `Solution` object recomputes it if not `static`. The code above declares it `static` to avoid this.

---

### 2️⃣ Python – Concise & Pythonic

```python
from math import isqrt
from typing import List

# Pre‑compute primes up to 1000
def sieve(limit: int) -> List[int]:
    is_prime = [True] * (limit + 1)
    is_prime[0] = is_prime[1] = False
    for p in range(2, isqrt(limit) + 1):
        if is_prime[p]:
            for k in range(p * p, limit + 1, p):
                is_prime[k] = False
    return [i for i, v in enumerate(is_prime) if v]

PRIMES = sieve(1000)

class Solution:
    def distinctPrimeFactors(self, nums: List[int]) -> int:
        unique = set()
        for num in nums:
            self._factor(num, unique)
        return len(unique)

    def _factor(self, num: int, unique: set) -> None:
        for p in PRIMES:
            if p * p > num:
                break
            if num % p == 0:
                unique.add(p)
                while num % p == 0:
                    num //= p
        if num > 1:            # remaining prime
            unique.add(num)
```

> **Good** – Leverages Python’s `set` and list comprehensions.  
> **Bad** – The sieve is recomputed every time the module is imported (but that's fine for LeetCode).  
> **Ugly** – The code still iterates over *all* primes up to 1000 for each number, even if `num` is tiny. A more clever approach would stop once `num == 1`.

---

### 3️⃣ C++ – Performance‑oriented

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
private:
    static vector<int> primes;

    static vector<int> buildPrimes(int limit) {
        vector<bool> isPrime(limit + 1, true);
        isPrime[0] = isPrime[1] = false;
        for (int p = 2; p * p <= limit; ++p)
            if (isPrime[p])
                for (int k = p * p; k <= limit; k += p)
                    isPrime[k] = false;
        vector<int> primes;
        for (int i = 2; i <= limit; ++i)
            if (isPrime[i]) primes.push_back(i);
        return primes;
    }

public:
    int distinctPrimeFactors(vector<int>& nums) {
        unordered_set<int> uniq;
        for (int val : nums) {
            factor(val, uniq);
        }
        return uniq.size();
    }

private:
    void factor(int num, unordered_set<int>& uniq) {
        for (int p : primes) {
            if (p * p > num) break;
            if (num % p == 0) {
                uniq.insert(p);
                while (num % p == 0) num /= p;
            }
        }
        if (num > 1) uniq.insert(num);
    }
};

vector<int> Solution::primes = Solution::buildPrimes(1000);
```

> **Good** – Uses static member to build primes once, shared across all instances.  
> **Bad** – The header `bits/stdc++.h` is non‑standard but convenient on LeetCode.  
> **Ugly** – The `unordered_set` may re‑hash if the number of unique primes grows; in practice this is negligible.

---

## 🔍 The Good, The Bad, and The Ugly

| Category | What You’ll Love | What You’ll Sweat Over | Avoid at All Costs |
|----------|------------------|------------------------|--------------------|
| **Good** | • Pre‑computed primes avoid repeated trial division. <br>• Set/HashSet ensures uniqueness in O(1). <br>• Linear in `n` and trivial in practice. | – | – |
| **Bad** | • Some solutions re‑factor the same primes for each element (costly but still < 2 000 ops). <br>• Using `Set` may allocate many small objects in Java/Python. | • Re‑computing sieve per instance (Java) <br>• Over‑generating primes (up to 10 000) when 1 000 suffices (Python). | – |
| **Ugly** | – | – | • Multiplying the whole array (overflow). <br>• Recursively dividing without a break condition. <br>• Storing the full product in a `long long` then factoring (overflow risk). |

---

## 📚 Quick‑Start Cheat Sheet

```text
1. Build list of primes up to 1000 (Sieve of Eratosthenes).
2. For each number in nums:
     a. For each prime p:
          i. If p * p > num → break.
          ii. If num % p == 0: add p to set; divide num by p until not divisible.
     b. If num > 1: add num (the remaining prime) to set.
3. Return set.size().
```

---

## 🎯 Why This Blog Helps Your Job Hunt

1. **Keyword‑rich Title & Headings** – Search engines flag it for *leetcode*, *distinct prime factors*, *Java/Python/C++ solutions*.  
2. **Clear, step‑by‑step code snippets** – Recruiters can copy‑paste and run instantly.  
3. **Deep dive into pitfalls** – Shows you not only the “right” answer but why you shouldn’t do it that way.  
4. **SEO‑friendly meta‑description** – Short, punchy, and packed with relevant terms.  
5. **Professional formatting** – Code blocks, tables, and a clean narrative make the article look polished.

> **Meta‑Description (≈150 chars)**  
> *Solve LeetCode 2521 – Distinct Prime Factors of Product of Array. Java, Python, C++ code, analysis of good/bad/ugly approaches, and interview‑ready explanations.*

---

## 📣 Call to Action

> Want to ace your next coding interview?  
> - **Download** the GitHub repo with all three solutions.  
> - **Clone** and run the tests.  
> - **Share** this post on LinkedIn/Dev.to and let recruiters see your problem‑solving style!

Happy coding! 🚀