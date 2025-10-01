---
title: LeetCode 1922. Count Good Numbers - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 1922. Count Good Numbers – A Quick‑Win LeetCode Solution

**Problem link**: <https://leetcode.com/problems/count-good-numbers/>

> A digit string is *good* if  
> * every digit at an **even** (0‑indexed) position is **even**  
> * every digit at an **odd** position is **prime** (2, 3, 5, 7)  
> Count all good strings of length **n**.  
> Return the answer modulo \(10^9+7\).  
> \(1 \le n \le 10^{15}\)

---

### TL;DR

| Language | Solution |
|----------|----------|
| **Java** | <details><summary>Click to expand</summary> <pre>class Solution {<br>    private static final long MOD = 1_000_000_007L;<br>    public int countGoodNumbers(long n) {<br>        long evenPos = (n + 1) / 2; // ceil(n/2)<br>        long oddPos  = n / 2;        // floor(n/2)<br>        long evenWays = modPow(5, evenPos, MOD);<br>        long oddWays  = modPow(4, oddPos, MOD);<br>        return (int)((evenWays * oddWays) % MOD);<br>    }<br>    private long modPow(long base, long exp, long mod) {<br>        long res = 1L;<br>        base %= mod;<br>        while (exp > 0) {<br>            if ((exp & 1L) == 1L) res = (res * base) % mod;<br>            base = (base * base) % mod;<br>            exp >>= 1; // divide by 2<br>        }<br>        return res;<br>    }<br>}</pre> </details> |
| **Python** | <details><summary>Click to expand</summary> <pre>class Solution:<br>    MOD = 10**9 + 7<br>    def countGoodNumbers(self, n: int) -> int:<br>        even_pos = (n + 1) // 2   # ceil(n/2)<br>        odd_pos  = n // 2        # floor(n/2)<br>        # Python’s built‑in pow is already O(log n) and uses modulo safely.<br>        return (pow(5, even_pos, self.MOD) * pow(4, odd_pos, self.MOD)) % self.MOD</pre> </details> |
| **C++** | <details><summary>Click to expand</summary> <pre>#include &lt;bits/stdc++.h&gt;<br>using namespace std;<br>\nconst long long MOD = 1'000'000'007LL;\n\nlong long mod_pow(long long base, long long exp) {\n    long long res = 1;\n    base %= MOD;\n    while (exp > 0) {\n        if (exp & 1LL) res = (res * base) % MOD;\n        base = (base * base) % MOD;\n        exp >>= 1; // divide by 2\n    }\n    return res;\n}\n\nclass Solution {\npublic:\n    int countGoodNumbers(long long n) {\n        long long evenPos = (n + 1) / 2; // ceil(n/2)\n        long long oddPos  = n / 2;       // floor(n/2)\n        long long evenWays = mod_pow(5, evenPos);\n        long long oddWays  = mod_pow(4, oddPos);\n        return static_cast<int>((evenWays * oddWays) % MOD);\n    }\n};</pre> </details> |

> **All three solutions run in \(O(\log n)\) time and \(O(1)\) space.**  
> They use *binary exponentiation* (also called *fast power*) to handle the huge exponents up to \(10^{15}\).

---

## 📖 Blog Article: “Count Good Numbers – The Good, The Bad, The Ugly”

> **Keywords**: Count Good Numbers LeetCode 1922, Java solution, Python solution, C++ solution, fast exponentiation, modular arithmetic, competitive programming, job interview, algorithm design

### 1. Introduction

If you’re preparing for coding interviews or competitive programming contests, you’ll soon find yourself looking for “quick‑win” problems that test both your **math intuition** and your ability to **handle large numbers**.  
1922. *Count Good Numbers* is exactly that kind of problem: a small, well‑defined pattern that becomes an instant candidate for a **log‑time** solution.  
In this post we walk through the **good**, the **bad**, and the **ugly** of this problem – what makes it a perfect interview talking point and how you can nail it.

### 2. Problem Recap (From the Interviewer's POV)

> *You’re given a positive integer \(n\) (up to \(10^{15}\)).  
> A string of digits is *good* if even‑indexed positions contain even digits (0,2,4,6,8) and odd‑indexed positions contain prime digits (2,3,5,7).  
> Count all good strings of length \(n\), modulo \(1\,000\,000\,007\).*

Why is this a **great interview problem**?

| ✅ | ✅ |
|---|---|
| **Compact statement** – no nested loops or DP arrays needed. | **Big‑O constraints** push you to use *binary exponentiation* instead of naïve loops. |
| **Math‑driven** – you can show an elegant closed‑form solution. | **Modular arithmetic** – a classic interview topic. |

### 3. The “Good”: A Beautiful Closed‑Form

#### 3.1 Observe the Pattern

- **Even positions** (0,2,4,…) → any of the 5 even digits: {0,2,4,6,8}.
- **Odd positions** (1,3,5,…) → any of the 4 prime digits: {2,3,5,7}.

Each position is independent of every other – the choice for one digit doesn’t influence the choice for another.

#### 3.2 How Many Positions?

```
n = 5      →  positions: 0 1 2 3 4
            even count = ceil(5/2) = 3
            odd  count = floor(5/2) = 2
```

In general:

```
evenPositions = (n + 1) / 2   // integer division (ceil)
oddPositions  = n / 2         // integer division (floor)
```

#### 3.3 Closed‑Form Formula

\[
\text{answer} = 5^{\text{evenPositions}} \times 4^{\text{oddPositions}} \pmod{10^9+7}
\]

Why does it work?  
Because each even position has *5* independent options, and each odd position has *4*.  
With \(k\) even positions, we have \(5^k\) possibilities; with \(m\) odd positions, we have \(4^m\).  
Multiplying them gives all valid strings.

#### 3.4 Binary Exponentiation – The Engine

You can’t compute \(5^{10^{15}}\) directly – the number is astronomically huge.  
Binary exponentiation splits the exponent in half each iteration:

```
pow(base, exp):
    res = 1
    while exp > 0:
        if exp is odd: res = res * base
        base = base * base
        exp  = exp // 2
```

- **Time**: \(O(\log \text{exp})\) multiplications.  
- **Space**: \(O(1)\).  
- **Modulo**: we reduce every intermediate product by \(10^9+7\) to avoid overflow.

### 4. The “Bad”: Things You Must Watch Out For

| Issue | Why it’s tricky | How to avoid it |
|-------|-----------------|-----------------|
| **Integer Overflow** | Using 32‑bit ints for `pow(5, evenPos)` will overflow long before the modulus is applied. | Use 64‑bit (`long` in Java, `long long` in C++). Reduce modulo before multiplication. |
| **Wrong Modulus Placement** | Multiplying `(5^k % MOD)` and `(4^m % MOD)` then taking `% MOD` again is fine, but forgetting the second modulo can give an overflow. | Always reduce after every multiplication: `(a * b) % MOD`. |
| **Python’s Built‑in `pow`** | If you forget the third argument, `pow(5, evenPos)` will try to compute a gigantic integer and explode. | Always call `pow(5, evenPos, MOD)` or `pow(4, oddPos, MOD)`. |
| **Large `n` Not Fit in `int`** | Some interviewers will give you `long long` (C++), `long` (Java), or `int` (Python). Using `int` will silently truncate. | Keep `n` as a 64‑bit integer; Python’s `int` is arbitrary precision anyway. |

### 5. The “Ugly”: A Small Edge Case You Can’t Miss

> **Zero‑Indexed Odd vs. Even** – A rookie mistake is to treat positions as 1‑indexed.  
> In this problem *“even position” means *0‑based* even indices*.  
> **Double‑check** the definition: `evenPos = (n + 1) / 2` (ceil), `oddPos = n / 2` (floor).  
> If you swap them, you’ll get the wrong answer even though the algorithm is still O(log n).

### 6. Implementation Snippets

Below are clean, production‑ready snippets for **Java**, **Python**, and **C++** – all with inline comments.

---

#### Java

```java
class Solution {
    private static final long MOD = 1_000_000_007L;

    public int countGoodNumbers(long n) {
        long evenPos = (n + 1) / 2; // ceil(n/2) – positions 0,2,4,...
        long oddPos  = n / 2;       // floor(n/2) – positions 1,3,5,...
        long evenWays = modPow(5, evenPos, MOD);
        long oddWays  = modPow(4, oddPos, MOD);
        return (int) ((evenWays * oddWays) % MOD);
    }

    private long modPow(long base, long exp, long mod) {
        long res = 1L;
        base %= mod;
        while (exp > 0) {
            if ((exp & 1L) == 1L) res = (res * base) % mod;
            base = (base * base) % mod;
            exp >>= 1;
        }
        return res;
    }
}
```

#### Python

```python
class Solution:
    MOD = 10**9 + 7

    def countGoodNumbers(self, n: int) -> int:
        even_pos = (n + 1) // 2   # ceil(n/2)
        odd_pos  = n // 2         # floor(n/2)
        # pow(base, exp, mod) is O(log exp) and safe against overflow.
        return (pow(5, even_pos, self.MOD) * pow(4, odd_pos, self.MOD)) % self.MOD
```

#### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

const long long MOD = 1'000'000'007LL;

long long mod_pow(long long base, long long exp) {
    long long res = 1;
    base %= MOD;
    while (exp > 0) {
        if (exp & 1LL) res = (res * base) % MOD;
        base = (base * base) % MOD;
        exp >>= 1;
    }
    return res;
}

class Solution {
public:
    int countGoodNumbers(long long n) {
        long long evenPos = (n + 1) / 2;
        long long oddPos  = n / 2;
        long long evenWays = mod_pow(5, evenPos);
        long long oddWays  = mod_pow(4, oddPos);
        return static_cast<int>((evenWays * oddWays) % MOD);
    }
};
```

---

### 7. Why It’s a “Fast‑Track” Interview Talk‑Point

| Skill | Why it matters in a hiring interview |
|-------|--------------------------------------|
| **Pattern Recognition** | Spotting the “5 for even, 4 for odd” is a classic DP simplification. |
| **Big‑Integer Handling** | `n` can be \(10^{15}\). You must know how to handle exponents that don’t fit in 32‑bit ints. |
| **Modular Arithmetic** | Most interviewers love to see you reduce intermediate values (`% MOD`) to avoid overflow. |
| **Binary Exponentiation** | Proving the algorithm is \(O(\log n)\) and implementing it cleanly shows you understand algorithmic time‑complexity. |
| **Edge‑Case Awareness** | Using `ceil(n/2)` vs `floor(n/2)` for even/odd positions is a subtle point; many candidates overlook it. |

If you want to stand out in a *software engineering* or *data‑structures & algorithms* interview, prepare a **clean implementation** (like the snippets above), a short **explanation** of why it works, and a **quick mental proof** of the complexity.

### 8. Final Takeaway

1922. *Count Good Numbers* is a **minimalistic problem** with a *maximal impact* for interviewers.  
The answer is a one‑liner closed‑form, and the only heavy lifting comes from binary exponentiation.  
Master the pattern, be careful with 0‑indexed positions, and always reduce modulo after each multiplication.  

Good luck, and let the digits line up in your favor! 🚀

--- 

**Enjoy the problem, practice the snippets, and bring a clean talk‑point to your next interview!**