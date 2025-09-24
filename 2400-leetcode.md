---
title: LeetCode 2400. Number of Ways to Reach a Position After Exactly k Steps - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📦 **Number of Ways to Reach a Position After Exactly k Steps – LeetCode 2400**

> **Problem ID:** 2400  
> **Difficulty:** Medium  
> **Tags:** Math, Combinatorics, Dynamic Programming, Algorithms, Interview

---

### 🚀 TL;DR

| Language | Time | Space |
|----------|------|-------|
| **Java** | **O(k)** (Math solution) | **O(1)** |
| **Python** | **O(k)** (Math solution) | **O(1)** |
| **C++** | **O(k)** (Math solution) | **O(1)** |

The *cleanest* solution uses a simple combinatorial formula:

```
Let d = endPos - startPos.
If |d| > k  →  impossible  →  return 0
If (k - d) % 2 ≠ 0 → impossible  →  return 0
Otherwise:
    rightSteps = (k + d) / 2          // number of steps to the right
    ways = C(k, rightSteps) % MOD
```

Computing the binomial coefficient modulo `10⁹+7` in `O(k)` time using multiplicative inverses gives a blazing‑fast answer.

---

## 📄 Blog Article – “The Good, the Bad, and the Ugly of LeetCode 2400”

> **Target Audience:** Front‑end/Back‑end engineers, algorithm enthusiasts, recruiters  
> **Goal:** Deliver a deep dive that showcases your problem‑solving skills, earns SEO credit, and helps you land a software‑engineering job.

---

### 1️⃣ Problem Overview

> **“Number of Ways to Reach a Position After Exactly k Steps”**  
> You start at `startPos` on an infinite integer line and may step **+1** or **–1** per move.  
> After **exactly** `k` steps, how many distinct sequences of moves land you at `endPos`?  
> Return the count modulo `1 000 000 007`.

#### Key Constraints

- `1 ≤ startPos, endPos, k ≤ 1000`  
- The line is unbounded, so positions can be negative.

---

### 2️⃣ The “Good” – Why the Math Approach Wins

| ✅ Feature | Explanation |
|------------|-------------|
| **O(k) Time** | No DP table or recursion – just a single loop computing a binomial coefficient. |
| **O(1) Space** | Only a few integer variables. |
| **Mathematically Elegant** | Reduces the problem to combinatorics: choose which of the `k` steps go right. |
| **Fast Modulo Arithmetic** | Uses Fermat’s little theorem for modular inverses, avoiding costly pre‑computed factorials. |
| **Easy to Verify** | Small test cases can be cross‑checked manually. |

The core observation: each step is either left or right. Suppose we take `r` right steps and `l` left steps. We need:

```
r + l = k
r – l = endPos – startPos   →  r = (k + (endPos – startPos)) / 2
```

`r` must be an integer and `0 ≤ r ≤ k`. If these conditions fail, the answer is zero.

Once `r` is known, the number of distinct sequences is simply `C(k, r)` – the ways to choose the positions of the right moves among the `k` steps.

---

### 3️⃣ The “Bad” – Why a Straightforward DP Might Hurt

A naive dynamic‑programming solution:

```text
dp[pos][step] = ways to reach `pos` after `step` moves
```

- **Memory blow‑up**: `dp` has size `(2k+1) × (k+1)` → ~3 M entries for `k = 1000`.  
- **Time**: `O(k²)` due to nested loops or recursive calls.  
- **Recursion depth**: Risk of stack overflow for larger `k`.  
- **Indexing issues**: Need to offset negative positions, adding boilerplate.

Although DP is conceptually simple and works for the given constraints, it is *overkill* for this problem and could fail under tighter limits or in an interview where time is critical.

---

### 4️⃣ The “Ugly” – Edge Cases & Pitfalls

| ⚠️ Issue | Why it’s ugly | How to avoid it |
|----------|---------------|-----------------|
| **Parity mismatch** | If `(k - d)` is odd, no sequence exists. | Explicit check `(k - d) % 2 != 0` before computing the binomial. |
| **Negative positions** | DP tables must map negative indices to array indices. | Use an offset (`+k`) or a `HashMap`. |
| **Large `k`** | Modulo inverses can overflow if not handled as `long`. | Use 64‑bit arithmetic (`long` in Java/C++, `int` in Python). |
| **Modular inverse** | Mistakenly computing `inv(i+1)` via division will crash. | Use Fermat’s little theorem: `pow(a, MOD-2, MOD)` or recursion `inv(a) = (MOD - MOD//a) * inv(MOD%a) % MOD`. |
| **Zero right steps** | If `r = 0`, `C(k,0)` should be 1. | Loop from `0` to `r-1`; if `r == 0`, skip the loop and return 1. |

---

### 5️⃣ Implementation Details

#### 5.1. Modular Inverse (Fermat)

For a prime modulus `p`, the modular inverse of `a` is `a^(p-2) mod p`. We can compute this by fast exponentiation in `O(log p)` time, or recursively with the property:

```
inv(a) = (p - p/a) * inv(p % a) % p
```

The recursive formula is efficient for small `a` (≤ k), which is all we need for this problem.

#### 5.2. Binomial Coefficient in O(k)

```
C(k, r) = Π_{i=0}^{r-1} (k - i) / (i + 1)
```

We perform the division via modular inverse:

```
res = 1
for i in 0..r-1:
    res = res * (k - i) % MOD
    res = res * inv(i + 1) % MOD
```

This keeps `res` small and respects the modulus.

---

### 6️⃣ Code Samples

#### 6.1. Java

```java
import java.util.*;

public class Solution {
    private static final long MOD = 1_000_000_007L;

    // Fast recursive modular inverse (Fermat's little theorem)
    private static long inv(long a) {
        if (a == 1) return 1;
        return (MOD - MOD / a) * inv(MOD % a) % MOD;
    }

    public int numberOfWays(int startPos, int endPos, int k) {
        int diff = endPos - startPos;

        // Impossible if distance > k or parity mismatch
        if (Math.abs(diff) > k || ((k - diff) & 1) == 1) return 0;

        int right = (k + diff) / 2;          // number of +1 steps
        if (right < 0 || right > k) return 0;

        long res = 1;
        for (int i = 0; i < right; i++) {
            res = res * (k - i) % MOD;
            res = res * inv(i + 1) % MOD;
        }
        return (int) res;
    }
}
```

*Why it’s interview‑friendly:* All arithmetic is performed with `long`, and the inverse function is tiny and pure.

#### 6.2. Python

```python
MOD = 1_000_000_007

def mod_inv(a: int) -> int:
    """Recursive modular inverse for prime MOD."""
    if a == 1:
        return 1
    return (MOD - MOD // a) * mod_inv(MOD % a) % MOD

def number_of_ways(startPos: int, endPos: int, k: int) -> int:
    diff = endPos - startPos

    # Parity and feasibility checks
    if abs(diff) > k or (k - diff) & 1:
        return 0

    right = (k + diff) // 2
    res = 1
    for i in range(right):
        res = res * (k - i) % MOD
        res = res * mod_inv(i + 1) % MOD
    return res
```

Python’s built‑in big integers mean we don’t worry about overflow, but we still keep the arithmetic in `int` for speed.

#### 6.3. C++

```cpp
#include <bits/stdc++.h>
using namespace std;

const long long MOD = 1'000'000'007LL;

// Recursive modular inverse (Fermat)
long long modInv(long long a) {
    if (a == 1) return 1;
    return (MOD - MOD / a) * modInv(MOD % a) % MOD;
}

class Solution {
public:
    int numberOfWays(int startPos, int endPos, int k) {
        int diff = endPos - startPos;
        if (abs(diff) > k || ((k - diff) & 1)) return 0;

        int right = (k + diff) / 2;
        long long res = 1;
        for (int i = 0; i < right; ++i) {
            res = res * (k - i) % MOD;
            res = res * modInv(i + 1) % MOD;
        }
        return static_cast<int>(res);
    }
};
```

All three implementations share the same logical structure; only the syntax differs.

---

### 7️⃣ Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| **Math / combinatorial** | `O(k)` (single loop) | `O(1)` (constant variables) |
| **DP (if you choose to mention)** | `O(k²)` | `O(k²)` (2‑D table) |

With `k ≤ 1000`, the math solution is trivial to compute, while DP would be slower and use more memory.

---

### 8️⃣ Common Interview Mistakes

| Mistake | Why it hurts | Fix |
|---------|--------------|-----|
| Returning `0` too early (before parity check) | Fails to explain *why* zero is the answer | Add explicit parity check first |
| Using `pow(a, MOD-2, MOD)` in Java without handling long | Might overflow int | Use `long` everywhere |
| Mis‑offsetting DP indices | Leads to IndexOutOfBoundsException | Use an offset (`+k`) or a hash map |
| Forgetting modulo after every addition/subtraction | Overflow or wrong answer | Always `% MOD` after each operation |

---

### 9️⃣ Interview‑Style Tips

- **Start with the constraints** – `k ≤ 1000` means we can afford `O(k)` operations; no need for heavy pre‑computation.
- **State the parity rule first** – recruiters love clear “why this is impossible” statements.
- **Explain Fermat’s little theorem** – demonstrates knowledge of number theory, which often impresses senior engineers.
- **Show both DP and math** – If the interviewer asks “what if constraints were 10⁵?”, explain why the DP would fail and the math would still be fine.
- **Test with small cases** – Write a brute‑force generator in your IDE to cross‑check the formula. Mention that during the interview you can “prove” a few examples on the whiteboard.

---

### 10️⃣ Final Take‑Away

> **The optimal solution to LeetCode 2400 is a one‑liner in logic but requires a *clean* modular arithmetic implementation.**  
> Mastering this pattern not only solves the problem in milliseconds but also shows recruiters you can spot hidden combinatorial structures in seemingly simple DP problems.

Happy coding, and may your next interview feature a *combination* of mathematical insight and clean code—just like the solution above! 🚀

---

## 🧾 Quick‑Start Checklist for Your Interview

1. **Understand the constraints.**  
2. **Check feasibility**: `abs(d) ≤ k` and `(k - d) % 2 == 0`.  
3. **Compute rightSteps = (k + d) / 2.**  
4. **Calculate `C(k, rightSteps)` with modular inverses in O(k).**  
5. **Return `ways % MOD`.**

Print the code, walk through a test case, and explain the parity logic—done!  

--- 

*Good luck, and may your job search be as fast as the algorithm above!*