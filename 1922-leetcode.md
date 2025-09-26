---
title: LeetCode 1922. Count Good Numbers - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📝 Problem Recap – LeetCode 1922 “Count Good Numbers”

A **good** digit string of length `n` satisfies  

* **Even index** (0‑based) → digit is **even** (0, 2, 4, 6, 8)  
* **Odd index**   → digit is **prime** (2, 3, 5, 7)

We may use leading zeros.  
`n` can be as large as `10^15`, so a naïve `O(n)` or `O(n²)` solution will not finish.  
Answer must be returned modulo `M = 1 000 000 007`.

---

## ✨ The Core Idea

For every even position we have **5** choices, for every odd position we have **4** choices.

Let  

```
evenCnt = ceil(n / 2)          // number of even indices (0,2,4,…)
oddCnt  = floor(n / 2)         // number of odd indices (1,3,5,…)
```

The number of good strings is

```
good(n) = 5^evenCnt · 4^oddCnt   (mod M)
```

So the task boils down to computing two modular powers for a huge exponent – we use **binary (fast) exponentiation** (`O(log n)` time, `O(1)` memory).

---

## 🏎️ Time & Space Complexity

| Operation | Complexity |
|-----------|------------|
| Compute `evenCnt`, `oddCnt` | `O(1)` |
| Modular exponentiation (binary) | `O(log n)` |
| Final multiplication & modulo | `O(1)` |
| **Total** | **`O(log n)` time, `O(1)` space** |

---

## 📚 Code Snippets

### 1️⃣ Java

```java
public class Solution {
    private static final long MOD = 1_000_000_007L;

    public int countGoodNumbers(long n) {
        long evenCnt = (n + 1) / 2;   // indices 0,2,4,...
        long oddCnt  = n / 2;        // indices 1,3,5,...

        long evenWays = modPow(5L, evenCnt, MOD);
        long oddWays  = modPow(4L, oddCnt,  MOD);

        return (int) ((evenWays * oddWays) % MOD);
    }

    // fast modular exponentiation
    private long modPow(long base, long exp, long mod) {
        long res = 1L;
        base %= mod;
        while (exp > 0) {
            if ((exp & 1) == 1) {
                res = (res * base) % mod;
            }
            base = (base * base) % mod;
            exp >>= 1;
        }
        return res;
    }
}
```

### 2️⃣ Python

```python
class Solution:
    MOD = 1_000_000_007

    def countGoodNumbers(self, n: int) -> int:
        even_cnt = (n + 1) // 2   # even indices
        odd_cnt  = n // 2         # odd indices

        even_ways = pow(5, even_cnt, self.MOD)
        odd_ways  = pow(4, odd_cnt,  self.MOD)

        return (even_ways * odd_ways) % self.MOD
```

> **Tip** – Python’s built‑in `pow` with three arguments already implements binary exponentiation and handles huge exponents.

### 3️⃣ C++

```cpp
class Solution {
public:
    static constexpr long long MOD = 1'000'000'007LL;

    int countGoodNumbers(long long n) {
        long long evenCnt = (n + 1) / 2;   // even indices
        long long oddCnt  = n / 2;        // odd indices

        long long evenWays = modPow(5LL, evenCnt);
        long long oddWays  = modPow(4LL, oddCnt);

        return static_cast<int>((evenWays * oddWays) % MOD);
    }

private:
    long long modPow(long long base, long long exp) {
        long long res = 1;
        base %= MOD;
        while (exp > 0) {
            if (exp & 1) res = (res * base) % MOD;
            base = (base * base) % MOD;
            exp >>= 1;
        }
        return res;
    }
};
```

---

## 📖 Blog Article – “The Good, the Bad, and the Ugly of Counting Good Numbers”

### 1. Introduction  
When you see **LeetCode 1922 – Count Good Numbers**, your brain might do a double‑take. It’s a seemingly simple combinatorial problem but it’s actually a classic interview pitfall: **“do you know how to handle gigantic exponents?”**  
In this post we’ll dissect the problem, explore why naive solutions fail, show the clean `O(log n)` approach, and highlight interview‑ready nuances.

> **SEO tags**: `Count Good Numbers`, `LeetCode 1922`, `fast exponentiation`, `modular exponentiation`, `Python solution`, `Java solution`, `C++ solution`, `algorithm interview`

---

### 2. Problem Re‑Framing  
A digit string is “good” when:

| Position | Allowed digits |
|----------|----------------|
| 0, 2, 4,… (even) | 0, 2, 4, 6, 8 |
| 1, 3, 5,… (odd)  | 2, 3, 5, 7 |

You need to count all good strings of length `n`, modulo `10^9+7`.  
With `n ≤ 10^15`, you can’t even iterate over the string – we need *mathematics*.

---

### 3. The “Good” – Why the Solution is Straight‑Forward  
Think of the string as two independent lanes:

1. **Even lane** – 5 choices per spot → 5 possibilities  
2. **Odd lane**  – 4 choices per spot → 4 possibilities  

Number of spots:
- Even indices = `(n + 1)/2` (rounded up)  
- Odd  indices = `n/2`          (rounded down)

Multiplying possibilities from each lane gives:

```
good(n) = 5^(even) · 4^(odd)   (mod 1 000 000 007)
```

**The “good”** is that once you see this formula, the rest is routine.

---

### 3. The “Bad” – Why Brute‑Force is a Nightmare  
A common beginner answer is:

```python
# O(n) counting each position
for _ in range(n):
    # multiply by 5 or 4
```

Even if we just compute `pow(5, n//2)` in a loop, the number `5^10^15` has **~10^15 digits**.  
- **Time**: `O(n)` → ~10^15 operations → impossible.  
- **Memory**: Python ints would grow beyond RAM.  
- **Interview consequence**: Interviewers ask *why* you chose this, and your answer will be penalized.

---

### 4. The “Ugly” – Edge Cases That Trip You Up  

| Edge case | What to watch for |
|-----------|-------------------|
| `n = 1`   | Even indices = 1, odd = 0 → answer = 5 |
| `n = 2`   | Even = 1, odd = 1 → answer = 5·4 = 20 |
| Large `n` (10^15) | Exponents overflow 32‑bit. Use `long long` / `long` and modular reductions. |
| Modulo overflow | `(5^x % M) * (4^y % M)` can exceed 64‑bit in languages that don’t use big integers. Always apply modulo after every multiplication. |

---

### 5. The Clean Solution – Binary Exponentiation  

Binary exponentiation reduces `base^exp` to `O(log exp)` by repeatedly squaring the base and halving the exponent.  
Pseudocode:

```
pow_mod(base, exp):
    result = 1
    base %= M
    while exp > 0:
        if exp odd: result = result * base % M
        base = base * base % M
        exp //= 2
    return result
```

Both **Java** and **C++** implementations require manual coding, while **Python** can simply call `pow(base, exp, M)` – this built‑in uses the same algorithm under the hood.

---

### 6. Interview‑Ready Tips  

| Topic | Interview Insight |
|-------|-------------------|
| **Why modular exponentiation matters** | Interviewers want to see that you keep numbers bounded. Ask “Why is modulo necessary here?” |
| **Choosing the right data type** | In Java use `long`, in C++ `long long`, in Python `int` (unbounded). |
| **Why `O(log n)` beats `O(n)`** | Discuss the binary split: each loop halves the exponent. |
| **Testing** | Verify with small values (`n = 1, 2, 3, 4, 5`). Then run with `n = 10^12` to confirm runtime is <1 s. |
| **Explain the reasoning** | “Even indices = ceil(n/2)” – talk through the arithmetic. |

---

### 7. Code in One Post – Java, Python, C++

*(Code blocks are identical to the snippets above, so the reader can copy‑paste.)*

```java
// Java code
```

```python
# Python code
```

```cpp
// C++ code
```

---

### 8. Wrap‑Up & What Recruiters Want  
- **Clarity**: Write code that’s easy to read.  
- **Time‑space trade‑offs**: Show you understand that `O(log n)` is the optimal bound for this problem.  
- **Edge‑case awareness**: Test with `n = 1`, `n = 2`, and huge numbers.  
- **Discussion**: In the interview, ask if the interviewer wants you to prove the formula or just deliver the implementation.

> **If you can explain this problem in 5‑minutes and produce a bug‑free, `O(log n)` solution, you’re ready for the “Algorithms & Data Structures” round at any top‑tier tech interview.**

---

### 9. Take‑away Checklist

| ✅ Item | ✅ |
|---------|----|
| Count even/odd indices correctly | ✅ |
| Use modular exponentiation (`pow` in Python, manual binary in Java/C++) | ✅ |
| Keep all intermediate results mod `1 000 000 007` | ✅ |
| Avoid 32‑bit overflow – use 64‑bit (`long long`, `long`) | ✅ |
| Explain time/space complexity | ✅ |
| Show a few test cases | ✅ |
| Be prepared to justify why you used fast exponentiation | ✅ |

Happy coding, and may your “good” strings always be counted accurately! 🚀