---
title: LeetCode 878. Nth Magical Number - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 878. Nth Magical Number – A Deep‑Dive with Java, Python & C++ Solutions  

> **TL;DR**  
> The Nth Magical Number problem is solved in **O(log n)** time by binary searching on the answer range and using the inclusion–exclusion principle with LCM.  
> Code snippets in **Java, Python, and C++** are provided.  
> A comprehensive blog post follows that explains the trick, pitfalls (“the ugly”), best practices (“the good”), and why this solution will impress hiring managers.

---

## 📚 Problem Statement (LeetCode 878)

A *magical* number is a positive integer that is divisible by **a** or **b**.  
Given three integers **n, a, b**, return the *nth* magical number.  
Because the answer can be huge, return it modulo `10⁹ + 7`.

**Constraints**

|  |  |
|---|---|
| `1 ≤ n ≤ 10⁹` | `2 ≤ a, b ≤ 4·10⁴` |

---

## 🔑 Key Observations

1. **Counting divisible numbers**  
   For a given `x`, the count of numbers ≤ `x` divisible by `a` is `x / a`.  
   Similarly for `b` and for `lcm(a, b)`.

2. **Inclusion–Exclusion**  
   `cnt(x) = x/a + x/b – x/lcm(a, b)`  
   gives the number of magical numbers ≤ `x`.

3. **Binary Search on the Answer**  
   We need the smallest `x` such that `cnt(x) ≥ n`.  
   The search range is `[min(a,b), n * min(a,b)]`.  
   This guarantees the answer is within the range.

4. **Overflow & Modulo**  
   Use 64‑bit arithmetic for intermediate results (LCM, multiplication).  
   Finally return `answer % (10⁹ + 7)`.

---

## 💡 Algorithm Outline

1. **Compute GCD** of `a` and `b` (Euclid).  
2. Compute `lcm = a / gcd * b` (use 64‑bit to avoid overflow).  
3. Binary search on `[low, high]`:
   - `mid = (low + high) / 2`  
   - If `cnt(mid) < n` → search right half (`low = mid + 1`)  
   - Else → search left half (`high = mid`).  
4. Result is `low` (or `high`), modulo `MOD = 1_000_000_007`.

---

## 📦 Implementation

Below are clean, self‑contained implementations in **Java, Python, and C++**.  
Each program defines a class `Solution` (Java & C++) or a function `nthMagicalNumber` (Python) that can be dropped directly into a LeetCode submission.

---

### Java

```java
class Solution {
    private static final long MOD = 1_000_000_007L;

    public int nthMagicalNumber(int n, int a, int b) {
        long A = a, B = b;

        // 1. Compute LCM
        long gcd = gcd(A, B);
        long lcm = (A / gcd) * B;          // 64‑bit

        // 2. Binary search bounds
        long low = Math.min(a, b);
        long high = low * (long) n;        // safe upper bound

        while (low < high) {
            long mid = low + (high - low) / 2;
            long cnt = mid / a + mid / b - mid / lcm;

            if (cnt < n) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return (int) (low % MOD);
    }

    private long gcd(long x, long y) {
        while (y != 0) {
            long t = x % y;
            x = y;
            y = t;
        }
        return x;
    }
}
```

---

### Python

```python
class Solution:
    MOD = 1_000_000_007

    def nthMagicalNumber(self, n: int, a: int, b: int) -> int:
        from math import gcd

        lcm = a // gcd(a, b) * b

        low, high = min(a, b), min(a, b) * n

        while low < high:
            mid = (low + high) // 2
            cnt = mid // a + mid // b - mid // lcm
            if cnt < n:
                low = mid + 1
            else:
                high = mid

        return low % self.MOD
```

---

### C++

```cpp
class Solution {
public:
    const long long MOD = 1'000'000'007LL;

    long long gcd(long long a, long long b) {
        while (b) {
            long long t = a % b;
            a = b; b = t;
        }
        return a;
    }

    int nthMagicalNumber(int n, int a, int b) {
        long long lcm = (long long)a / gcd(a, b) * b;

        long long low = min(a, b);
        long long high = low * 1LL * n;          // upper bound

        while (low < high) {
            long long mid = low + (high - low) / 2;
            long long cnt = mid / a + mid / b - mid / lcm;
            if (cnt < n) low = mid + 1;
            else high = mid;
        }

        return (int)(low % MOD);
    }
};
```

---

## 🔎 The Good, The Bad, and The Ugly

| Aspect | Explanation |
|--------|-------------|
| **Good** | • **O(log n)** time, far better than brute force (O(n)). <br>• Uses 64‑bit arithmetic to avoid overflow. <br>• Handles the modulo cleanly at the end. <br>• Binary search bounds are mathematically proven to contain the answer. |
| **Bad** | • If you forget to cast to 64‑bit (Java `long`, Python's arbitrary ints help, C++ `long long`), you risk overflow when computing `lcm` or `mid`. <br>• Off‑by‑one errors in binary search boundaries (use `low < high` and `mid = low + (high - low) / 2`). |
| **Ugly** | • The inclusion–exclusion step (`cnt = mid/a + mid/b - mid/lcm`) looks simple but can be a source of subtle bugs if `lcm` is zero (not possible here but good to guard). <br>• GCD/LCD computation may be over‑engineered for beginners; a one‑liner `gcd = std::gcd(a, b)` in C++17 helps. <br>• Remember that the answer must be returned modulo `10⁹+7`; forgetting this can cause a wrong answer on huge test cases. |

---

## 📈 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| GCD/LCM | `O(log min(a,b))` | `O(1)` |
| Binary search | `O(log(n * min(a,b)))` ≈ `O(log n)` | `O(1)` |
| Total | `O(log n)` | `O(1)` |

The memory footprint is constant – no additional arrays or recursion.

---

## 🧪 Test Cases

| `n` | `a` | `b` | Expected |
|-----|-----|-----|----------|
| 1 | 2 | 3 | 2 |
| 4 | 2 | 3 | 6 |
| 5 | 5 | 7 | 25 |
| 10⁹ | 2 | 3 | 1999999998 (mod 10⁹+7) |
| 100 | 40000 | 40000 | 40000 |

Run each snippet in its language's environment or on LeetCode's "Run Code" panel.

---

## 🚀 SEO‑Optimized Conclusion

If you’re preparing for software‑engineering interviews, mastering the **Nth Magical Number** problem demonstrates:

1. **Algorithmic Thinking** – recognizing the need for binary search on answer space.
2. **Mathematical Rigor** – applying inclusion–exclusion, GCD, LCM.
3. **Coding Discipline** – handling large numbers, using 64‑bit arithmetic, and modulo operations correctly.

Use the code snippets above in your portfolio or GitHub README to showcase your proficiency. The blog post structure – problem statement, key observations, algorithm, code, complexity – aligns with what recruiters search for in *LeetCode solution explanations*.

**Keywords** that will boost your SEO:  
`LeetCode 878`, `Nth Magical Number`, `binary search`, `LCM`, `GCD`, `coding interview`, `algorithmic problem solving`, `O(log n)`, `Java solution`, `Python solution`, `C++ solution`, `interview preparation`.

Share this post on Medium, Dev.to, or your personal blog, add relevant tags, and you’ll be on the radar of hiring managers looking for candidates who can turn math into efficient code.

Happy coding—and may that Nth magical number always be just a few lines away!