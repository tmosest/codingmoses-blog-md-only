---
title: LeetCode 2961. Double Modular Exponentiation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 2961 – “Double Modular Exponentiation”  
*The Good, The Bad, The Ugly – a deep‑dive plus 3‑language solutions*  

---

### TL;DR  
- **Goal:** Find all indices *i* of the 2‑D array `variables` such that  
  \[
  ((a_i^{b_i}\ \% \ 10)^{c_i}\ \% \ m_i) == \texttt{target}
  \]  
- **Constraints:** `variables.length ≤ 100`, all numbers ≤ 1000.  
- **Optimal solution:** Fast modular exponentiation (binary exponentiation).  
- **Time complexity:** **O(n log max(bi,ci))** – practically O(n) with the given limits.  
- **Space complexity:** **O(1)** (ignoring output).  

Below you’ll find clean, production‑ready code in **Java, Python, and C++** – ready to paste into your LeetCode editor or your portfolio.

---

## 1. Problem Recap

| Field | Meaning |
|-------|---------|
| `variables[i] = [a, b, c, m]` | Four integers defining one test case. |
| `target` | Integer that the expression must equal. |
| **Expression** | \[
((a^{b} \% 10)^{c} \% m)
\] |

**Return** a list of indices (0‑based) that satisfy the equality, in any order.

---

## 2. Why a Naïve Approach Might Fail

You could compute `a**b` and `result**c` directly.  
With `b, c ≤ 1000`, Python’s big integers can handle it, but:

* **Runtime**: `O(b + c)` multiplications per test → up to 2000 ops * 100 tests = 200 000 ops (still fine).  
* **Overflow**: In Java/C++ plain `int` or `long` will overflow before the modulo step.  
* **Readability**: Repeating loops for each exponent looks messy.

So the *“bad”* part is not correctness, but **fragility** (overflow) and **inefficiency** in languages with fixed‑size integer types.

---

## 3. The “Good” Solution – Fast Modular Exponentiation

Binary exponentiation (`powMod`) reduces the number of multiplications from `O(exp)` to `O(log exp)`.  
Implementation steps:

1. Compute `p1 = powMod(a, b, 10)`.  
2. Compute `p2 = powMod(p1, c, m)`.  
3. If `p2 == target`, add the index to the result.

**Why it’s great**

* Handles overflow safely because each multiplication is performed modulo the current modulus.  
* Runs in negligible time even for the maximum input sizes.  
* Code is short, testable, and language‑agnostic.

---

## 4. The “Ugly” Corner Cases

| Case | What can go wrong? | Fix |
|------|-------------------|-----|
| `m = 1` | `x % 1 == 0` always, so the whole expression collapses to 0. | No extra logic needed – algorithm naturally handles it. |
| `target = 0` | Must consider that 0 can come from many inputs. | Straight comparison, no special handling. |
| `b` or `c` = 0 | `x^0 = 1`. | `powMod` handles this because the loop terminates with result 1. |

---

## 5. 3‑Language Implementations

> All three solutions are *function‑first*, with a small helper for modular exponentiation.  
> Feel free to copy‑paste directly into LeetCode or your local IDE.

### 5.1 Java (LeetCode‑style)

```java
import java.util.*;

public class Solution {
    // Helper: fast modular exponentiation
    private long modPow(long base, long exp, long mod) {
        long result = 1 % mod;
        base %= mod;
        while (exp > 0) {
            if ((exp & 1) == 1) result = (result * base) % mod;
            base = (base * base) % mod;
            exp >>= 1;
        }
        return result;
    }

    public List<Integer> getGoodIndices(int[][] variables, int target) {
        List<Integer> good = new ArrayList<>();
        for (int i = 0; i < variables.length; i++) {
            long a = variables[i][0];
            long b = variables[i][1];
            long c = variables[i][2];
            long m = variables[i][3];

            long p1 = modPow(a, b, 10);     // a^b % 10
            long p2 = modPow(p1, c, m);     // (p1)^c % m

            if (p2 == target) good.add(i);
        }
        return good;
    }
}
```

### 5.2 Python (3.x)

```python
class Solution:
    def getGoodIndices(self, variables: List[List[int]], target: int) -> List[int]:
        def mod_pow(base: int, exp: int, mod: int) -> int:
            result = 1 % mod
            base %= mod
            while exp:
                if exp & 1:
                    result = (result * base) % mod
                base = (base * base) % mod
                exp >>= 1
            return result

        good = []
        for idx, (a, b, c, m) in enumerate(variables):
            p1 = mod_pow(a, b, 10)  # a^b % 10
            p2 = mod_pow(p1, c, m)  # (p1)^c % m
            if p2 == target:
                good.append(idx)
        return good
```

> *Python’s built‑in `pow(base, exp, mod)` is also a perfect fit, but the helper shows the algorithm explicitly.*

### 5.3 C++ (17)

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    // Fast modular exponentiation
    long long modPow(long long base, long long exp, long long mod) {
        long long result = 1 % mod;
        base %= mod;
        while (exp) {
            if (exp & 1LL) result = (result * base) % mod;
            base = (base * base) % mod;
            exp >>= 1LL;
        }
        return result;
    }

    vector<int> getGoodIndices(vector<vector<int>>& variables, int target) {
        vector<int> ans;
        for (int i = 0; i < (int)variables.size(); ++i) {
            long long a = variables[i][0];
            long long b = variables[i][1];
            long long c = variables[i][2];
            long long m = variables[i][3];

            long long p1 = modPow(a, b, 10);   // a^b % 10
            long long p2 = modPow(p1, c, m);   // (p1)^c % m

            if (p2 == target) ans.push_back(i);
        }
        return ans;
    }
};
```

---

## 6. Blog‑Style Deep Dive

### 6.1 Introduction

When prepping for a **software engineering interview** you’ll often see LeetCode problems that sound deceptively simple but hide a trick in their wording. *Double Modular Exponentiation* is one of those gems. The key is to recognise that the expression can be broken into two independent modular exponentiations, and that we must compute each safely to avoid overflow.

> **SEO Boost** – If you’re searching “LeetCode double modular exponentiation solution” or “fast exponentiation interview”, this article has you covered.

### 6.2 Problem Recap (again)

We’re given an array of quadruples `[a, b, c, m]`. For each quadruple, we need to compute:

```
temp = (a^b) % 10
value = (temp^c) % m
```

If `value == target`, the index is “good”. Return all good indices.

### 6.3 Why Modular Arithmetic?

- **Overflow**: In Java or C++ an `int` can hold only up to 2³¹−1. Even `a^b` can blow this up for moderate values.  
- **Efficiency**: Doing the modulo after every multiplication keeps the numbers small.  
- **Mathematical property**: `(x * y) % mod = ((x % mod) * (y % mod)) % mod`.

### 6.4 The Fast Modular Exponentiation Algorithm

```
powMod(base, exp, mod):
    result = 1
    base = base % mod
    while exp > 0:
        if exp is odd:
            result = (result * base) % mod
        base = (base * base) % mod
        exp = exp // 2
    return result
```

The loop halves the exponent each iteration → **O(log exp)** multiplications.

### 6.5 Step‑by‑Step Execution (Example)

Let’s walk through the first sample:

```
variables = [[2,3,3,10], [3,3,3,1], [4,1,2,3]]
target    = 0
```

- **Index 0**:  
  temp = powMod(2,3,10) = 8  
  value = powMod(8,3,10) = 0  → good  
- **Index 1**:  
  temp = powMod(3,3,10) = 7  
  value = powMod(7,3,1) = 0  → good (m=1 → always 0)  
- **Index 2**:  
  temp = powMod(4,1,10) = 4  
  value = powMod(4,2,3) = 1  → not good  

Result: `[0,1]`
```

The algorithm runs in a fraction of a millisecond even for the largest input sizes.

### 6.6 Complexity Analysis

- **n** = number of quadruples (`≤ 100`).  
- **b, c** ≤ 1000 → `log₂(1000) ≈ 10`.  
- Each quadruple does at most 20 modular multiplications → practically *instantaneous*.  

**Time**: `O(n * log(max(b,c)))`  
**Space**: `O(1)` (aside from the output list).

### 6.7 Edge Cases & Pitfalls

| Edge | Handling |
|------|----------|
| `m == 1` | Every `% 1` is 0 → expression always 0. |
| `b == 0` or `c == 0` | `powMod` returns 1 because result starts at 1. |
| `target == 0` | The comparison is exact; no special logic required. |

### 6.8 The Final 3‑Language Code

*(see section 5 above)*

### 6.9 Why This Code Rocks for Your Portfolio

- **Type‑safety** – The helper guarantees no integer overflow.  
- **Readability** – A single helper (`modPow`) and a concise loop over `variables`.  
- **Cross‑platform** – Works in Java, Python, C++, or any language that supports 64‑bit integers.

> **Career Tip** – Add this as a snippet to your personal repo, tag it with `#fast‑exponentiation`, `#modular‑arithmetic`, and `#leetcodesolutions`. Recruiters love seeing clean, language‑agnostic code.

### 6.10 TL;DR for Interviewers

> **“Tell me how you’d solve Double Modular Exponentiation.”**  
> *Answer*: “Split the problem into two modular exponentiations and compute each with binary exponentiation. This keeps all intermediate values bounded and runs in logarithmic time.”  

---

## 7. Closing Thoughts

- **The “Good” part** – Binary exponentiation turns a potential overflow nightmare into a robust, O(log exp) routine.  
- **The “Bad” part** – Forgetting to mod after every multiplication leads to crashes in Java/C++.  
- **The “Ugly” part** – None of the edge cases actually break the algorithm; the helper makes them *explicit* and *handled automatically*.

Use the three solutions as a ready‑to‑copy reference, and reference the article in your LinkedIn posts or portfolio to demonstrate both your coding chops and your *explanation* skills.

> **Happy coding, and good luck on your next interview!** 🚀  

---