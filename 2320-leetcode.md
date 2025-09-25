---
title: LeetCode 2320. Count Number of Ways to Place Houses - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Mastering LeetCode 2320: *Count Number of Ways to Place Houses*  
**Java | Python | C++** – One‑liner DP + optional O(log n) fast‑pow  
*SEO‑optimized guide for software‑engineering interview success*  

---

### 1️⃣ Problem Recap  

> **LeetCode 2320 – Count Number of Ways to Place Houses**  
> There are `2·n` plots on a street (two sides).  
> Plots on each side are numbered `1 … n`.  
> A house can be built on any plot, but **two houses on the same side cannot be adjacent**.  
> Houses on opposite sides are independent – a house on plot `i` on one side does **not** forbid a house on plot `i` on the other side.  
>  
> Return the total number of possible placements modulo `1 000 000 007`.

| `n` | Example Output | Explanation |
|-----|----------------|-------------|
| 1   | 4              | `{∅, left, right, both}` |
| 2   | 9              | See diagram in LeetCode problem |

---

### 2️⃣ Constraints  

- `1 ≤ n ≤ 10⁴`  
- Modulo: `MOD = 1 000 000 007`  

> **Why does n = 10⁴ matter?**  
> A simple `O(n²)` backtracking would explode. We need an `O(n)` or better solution.

---

### 3️⃣ Observations & Key Insight  

- **Sides are independent** – the placement on the left side never influences the right side.  
- Counting ways for *one* side is a classic “no adjacent houses” problem.  
  - Let `f[n]` = ways to fill `n` plots on a single side.  
  - `f[0] = 1` (no plots, one way – empty).  
  - `f[1] = 2` (empty or house).  
  - For `n ≥ 2`: `f[n] = f[n-1] + f[n-2]` (place house on last plot → previous plot must be empty).  
  - Thus `f[n]` follows the Fibonacci sequence shifted by 2 positions:  
    `f[n] = Fib(n+2)` where `Fib(0)=0, Fib(1)=1`.  
- Since the two sides are independent:  
  **Answer = f[n] × f[n] mod MOD**.

---

### 4️⃣ Two Optimal Solutions  

| Approach | Time | Space | Implementation Notes |
|----------|------|-------|----------------------|
| **Iterative DP** | `O(n)` | `O(1)` | Simple loop, keeps last two Fibonacci numbers. |
| **Fast‑Power / Matrix Exponentiation** | `O(log n)` | `O(1)` | Uses the closed‑form of Fibonacci via fast doubling. |

> **Why both?**  
> - For interviewers: demonstrating understanding of both DP and fast‑doubling shows depth.  
> - For production: `O(n)` is perfectly fine up to 10⁴, but `O(log n)` is future‑proof for larger inputs.

---

## 5️⃣ Code – Three Languages  

> All solutions use `MOD = 1_000_000_007`.  
> The *Iterative DP* version is the core solution; the *Fast‑Power* version is an optional extra.

### 5.1 Java

```java
class Solution {
    private static final long MOD = 1_000_000_007L;

    // ---- O(n) DP ----------------------------------------------------
    public int countHousePlacements(int n) {
        long a = 1;   // f[0]
        long b = 2;   // f[1]
        for (int i = 2; i <= n; i++) {
            long c = (a + b) % MOD;
            a = b;
            b = c;
        }
        return (int) ((b * b) % MOD);          // f[n] * f[n] % MOD
    }

    // ---- Optional: O(log n) Fast Doubling --------------------------------
    private long fib(long k) {                 // returns Fib(k) mod MOD
        if (k == 0) return 0;
        long a = 0, b = 1;                    // (F(0), F(1))
        for (int i = 63 - Long.numberOfLeadingZeros(k); i >= 0; i--) {
            long d = (a * ((b * 2 % MOD - a + MOD) % MOD)) % MOD;   // F(2n)
            long e = (a * a % MOD + b * b % MOD) % MOD;              // F(2n+1)
            a = d;
            b = e;
            if (((k >> i) & 1) == 1) {          // go to n+1
                long f = (a + b) % MOD;         // F(2n+1) + F(2n)
                a = b;
                b = f;
            }
        }
        return a;
    }

    public int countHousePlacementsFast(int n) {
        long fibNPlus2 = fib(n + 2);          // f[n] = Fib(n+2)
        return (int) ((fibNPlus2 * fibNPlus2) % MOD);
    }
}
```

### 5.2 Python

```python
MOD = 1_000_000_007

class Solution:
    # ---- O(n) DP ----------------------------------------------------
    def countHousePlacements(self, n: int) -> int:
        a, b = 1, 2                 # f[0], f[1]
        for _ in range(2, n + 1):
            a, b = b, (a + b) % MOD
        return (b * b) % MOD

    # ---- Optional: O(log n) Fast Doubling --------------------------------
    def fib(self, k: int) -> int:          # Fib(k) % MOD
        if k == 0:
            return 0
        a, b = 0, 1
        for bit in reversed(bin(k)[2:]):
            d = (a * ((b * 2 - a) % MOD)) % MOD
            e = (a * a + b * b) % MOD
            a, b = d, e
            if bit == '1':
                a, b = b, (a + b) % MOD
        return a

    def countHousePlacementsFast(self, n: int) -> int:
        fib_n_plus_2 = self.fib(n + 2)
        return (fib_n_plus_2 * fib_n_plus_2) % MOD
```

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    const long long MOD = 1'000'000'007LL;

public:
    // ---- O(n) DP ----------------------------------------------------
    int countHousePlacements(int n) {
        long long a = 1, b = 2;   // f[0], f[1]
        for (int i = 2; i <= n; ++i) {
            long long c = (a + b) % MOD;
            a = b;
            b = c;
        }
        return static_cast<int>((b * b) % MOD);
    }

    // ---- Optional: O(log n) Fast Doubling --------------------------------
    long long fib(long long k) {          // return Fib(k) % MOD
        if (k == 0) return 0;
        long long a = 0, b = 1;          // (F(0), F(1))
        for (int i = 63 - __builtin_clzll(k); i >= 0; --i) {
            long long d = (a * ((b * 2 % MOD - a + MOD) % MOD)) % MOD; // F(2n)
            long long e = (a * a % MOD + b * b % MOD) % MOD;           // F(2n+1)
            a = d;
            b = e;
            if ((k >> i) & 1LL) {                                 // move to n+1
                long long f = (a + b) % MOD;
                a = b;
                b = f;
            }
        }
        return a;
    }

    int countHousePlacementsFast(int n) {
        long long fibNPlus2 = fib(n + 2);          // f[n] = Fib(n+2)
        return static_cast<int>((fibNPlus2 * fibNPlus2) % MOD);
    }
};
```

> **Tip** – All three languages keep **`long long / long`** for the intermediate multiplication to avoid overflow before the `% MOD`.

---

## 6️⃣ Why This Blog Will Make You Stand Out  

| Skill | How the Blog Demonstrates It |
|-------|-----------------------------|
| **Algorithmic thinking** | We broke the problem into *independent* sides and used Fibonacci. |
| **Dynamic Programming** | The `O(n)` DP loop is a clean, interview‑friendly solution. |
| **Mathematical elegance** | `f[n] = Fib(n+2)` shows you can spot closed‑forms. |
| **Advanced techniques** | Fast‑doubling shows mastery of `O(log n)` tricks. |
| **Multilingual implementation** | Providing Java, Python, and C++ solutions showcases versatility. |
| **Clean code & mod arithmetic** | No magic numbers, constant‑time space, and robust modulo handling. |

---

## 7️⃣ Quick‑Start Checklist (Interview Ready)

1. **Explain the independence of the two sides** – gives the interviewer a glimpse of your problem‑decomposition skills.  
2. **Derive the Fibonacci recurrence for one side** – make sure you can articulate why `f[n] = f[n-1] + f[n-2]`.  
3. **Show the final formula** – answer = `f[n]^2 mod MOD`.  
4. **Write the iterative DP** – 5–10 lines of code, O(n), O(1) memory.  
5. **Optional**: mention/implement the fast‑doubling Fibonacci for `O(log n)` to impress on depth.  
6. **Test edge cases** – `n = 1`, `n = 2`, `n = 10⁴`, and a sanity test for `n = 0` (if the test harness allows it).  
7. **Talk through complexity** – confirm it satisfies the constraints.

---

## 8️⃣ Final Thought: From Code to Career 🚀  

> **LeetCode 2320** is a textbook example of *“think in terms of independent sub‑problems”*.  
> The same pattern appears in **Hotel Booking, House Robber, and Matrix Chain Multiplication**.  
> By mastering this problem in **Java, Python, and C++**, you’re equipped to:
> - Pass the typical algorithm‑design round in any tech‑company interview.  
> - Explain how to scale an `O(n)` DP to `O(log n)` with fast doubling – a talking‑point for senior roles.  
> - Show that you can write clean, production‑ready code that handles big‑mod arithmetic flawlessly.

Keep this solution in your *algorithm cheat‑sheet* and you’ll be ready to nail the next coding interview. Happy coding! 🎉

--- 

**Keywords**: *software‑engineering interview, algorithm design, dynamic programming, Fibonacci, LeetCode 2320, Java coding interview, Python interview, C++ interview, fast doubling, O(log n) algorithms, no‑adjacent‑houses, interview preparation*.