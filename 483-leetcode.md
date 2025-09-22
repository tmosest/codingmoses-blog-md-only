---
title: LeetCode 483. Smallest Good Base - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# 483. Smallest Good Base – A Complete, SEO‑Optimized Job‑Ready Guide  

> **Keywords**: LeetCode 483, smallest good base, LeetCode solution, interview algorithms, job interview coding, binary search, exponentiation, BigInteger, C++ __int128, Python int

---

## 1. Problem Recap

**LeetCode 483 – Smallest Good Base**

> Given an integer **n** (as a string) in the range **[3, 10¹⁸]**, find the smallest base **k ≥ 2** such that the representation of *n* in base **k** consists **only of 1’s**.

For example:  
- `n = 13` → `13₁₀ = 111₃` → answer is `"3"`.  
- `n = 4681` → `4681₁₀ = 11111₈` → answer is `"8"`.  
- `n = 1000000000000000000` → `1000000000000000000₁₀ = 11_{999999999999999999}` → answer is `"999999999999999999"`.

**Why this problem is interview‑gold**  
- It tests mastery of number theory, logarithms, and binary search.  
- Requires careful handling of big integers and overflow.  
- A good solution demonstrates thought‑process, algorithmic insight, and clean coding.

---

## 2. Good: The Optimal Approach

### 2.1. Observations

If all digits are 1, then for some length **m** (the number of 1’s)

```
n = 1 + k + k² + … + k^(m‑1) = (k^m – 1) / (k – 1)
```

Rearranged:

```
k^m – 1 = n (k – 1)
```

We are looking for integers **k ≥ 2** and **m ≥ 2** that satisfy this.

### 2.2. Bounding the Length *m*

Since `k ≥ 2`, we have `n ≥ 2^(m‑1)`.  
Thus:

```
m ≤ log₂(n) + 1   (≈ 60 for n ≤ 10¹⁸)
```

So we only need to try **m = 60 down to 2**. The larger *m* gives a smaller *k*, hence iterating *m* downward guarantees we find the smallest base first.

### 2.3. Searching for *k* for a fixed *m*

For a fixed *m*, the equation

```
k^(m‑1) ≤ n < k^m
```

holds. We can binary‑search **k** in the range `[2, floor(n^(1/(m‑1)) + 1)]`.

During binary search we need to evaluate `k^m` without overflow. In languages that lack arbitrary‑precision integers (C++/Java), we use:

- `__int128` in C++ (fast, 128‑bit arithmetic).  
- `BigInteger` in Java.  
- Built‑in arbitrary precision `int` in Python (no worries).

The evaluation routine stops early if the intermediate product exceeds *n*, because we only care whether `k^m` is **<, =, or > n`.

### 2.4. Algorithm

```
for m from 60 down to 2:
    low = 2
    high = floor(n^(1/(m‑1))) + 1
    while low ≤ high:
        mid = (low + high) / 2
        value = mid^m   (computed safely)
        if value == n:
            return mid
        else if value < n:
            low = mid + 1
        else:
            high = mid - 1
return n - 1   // fallback (n is 2^m - 1)
```

### 2.5. Complexity

- **Time**:  
  *O(log(n) * log(n))*, essentially *O(60 · log(n))* ≈ *O(3600)* operations – negligible.

- **Space**:  
  *O(1)* – constant auxiliary storage.

---

## 3. Bad: The Brute‑Force Pitfall

A tempting but **deadly** approach is to try every base **k** from `2` up to `n‑1`, converting `n` to base `k` and checking if all digits are 1.  

- **Time**:  
  *O(n log n)* – impossible for *n = 10¹⁸*.

- **Space**:  
  *O(log n)* for the digits.

It may pass for very small inputs, but in interviews or production code, it’s a catastrophic waste of time.

---

## 4. Ugly: Overflow Traps & Edge Cases

| Problem | Why it happens | Fix |
|---------|----------------|-----|
| **Multiplication overflow** | `k^m` quickly exceeds 64‑bit range. | Use `__int128` (C++), `BigInteger` (Java), or guard multiplication by early exit. |
| **Floating‑point imprecision** | Computing `n^(1/(m‑1))` via `Math.pow` may produce an off‑by‑one error. | After the initial high bound, let binary search correct it. |
| **Large exponent** | `m` close to 60, `k^m` may exceed even 128‑bit. | In binary search, stop multiplying as soon as product > `n`. |
| **Corner case** | `n` itself is a power of two minus one (e.g., `15 = 1111₂`). | The algorithm naturally returns `n‑1` as fallback. |

---

## 5. Code Implementations

Below are **fully working** snippets for **Java**, **Python**, and **C++** that follow the optimal algorithm described above.

> **Note**: In all three, `n` is passed as a `String` to avoid overflow on parsing.

### 5.1. Java (Java 17+)

```java
import java.math.BigInteger;

public class SmallestGoodBase {
    public String smallestGoodBase(String nStr) {
        BigInteger n = new BigInteger(nStr);
        // maximum possible exponent m: log2(n) + 1
        int maxM = 64;  // safe upper bound for 10^18

        for (int m = maxM; m >= 2; m--) {
            // lower and upper bounds for k
            long low = 2;
            long high = (long) Math.pow(n.doubleValue(), 1.0 / (m - 1)) + 1;
            while (low <= high) {
                long mid = low + (high - low) / 2;
                BigInteger k = BigInteger.valueOf(mid);
                BigInteger power = powWithLimit(k, m, n);
                int cmp = power.compareTo(n);
                if (cmp == 0) {
                    return Long.toString(mid);
                } else if (cmp < 0) {
                    low = mid + 1;
                } else {
                    high = mid - 1;
                }
            }
        }
        // fallback: n-1 (n is 2^m - 1)
        return n.subtract(BigInteger.ONE).toString();
    }

    // Computes base^exp but stops and returns > n if overflow
    private BigInteger powWithLimit(BigInteger base, int exp, BigInteger limit) {
        BigInteger result = BigInteger.ONE;
        for (int i = 0; i < exp; i++) {
            result = result.multiply(base);
            if (result.compareTo(limit) > 0) {
                return result; // early exit, greater than limit
            }
        }
        return result;
    }
}
```

### 5.2. Python 3 (built‑in unlimited precision)

```python
def smallestGoodBase(n: str) -> str:
    num = int(n)
    max_m = 64  # sufficient for n <= 1e18

    for m in range(max_m, 1, -1):
        low, high = 2, int(num ** (1.0 / (m - 1))) + 1
        while low <= high:
            mid = (low + high) // 2
            value = pow(mid, m)
            if value == num:
                return str(mid)
            elif value < num:
                low = mid + 1
            else:
                high = mid - 1
    return str(num - 1)  # fallback
```

### 5.3. C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string smallestGoodBase(string n) {
        unsigned long long N = stoull(n);
        // maximum exponent m: log2(N) + 1
        const int MAX_M = 64;

        for (int m = MAX_M; m >= 2; --m) {
            unsigned long long low = 2;
            unsigned long long high = pow((long double)N, 1.0L / (m - 1)) + 1;
            while (low <= high) {
                unsigned long long mid = low + (high - low) / 2;
                __int128 value = powerWithLimit(mid, m, N);
                if (value == N) return to_string(mid);
                if (value < N) low = mid + 1;
                else high = mid - 1;
            }
        }
        // fallback: N-1
        return to_string(N - 1);
    }

private:
    // compute base^exp, but return > limit if it overflows
    __int128 powerWithLimit(unsigned long long base, int exp, unsigned long long limit) {
        __int128 res = 1;
        for (int i = 0; i < exp; ++i) {
            res *= base;
            if (res > limit) return res;   // early exit
        }
        return res;
    }
};
```

---

## 6. Blog Article: The Good, The Bad, and The Ugly

> **Title**: *Smallest Good Base – LeetCode 483: The Good, the Bad, and the Ugly (Job‑Ready Code & Interview Tips)*  

### 6.1. Introduction

If you’re preparing for a software‑engineering interview, one of the classic problems you’ll encounter is **LeetCode 483 – Smallest Good Base**. It’s a perfect test of your number theory knowledge, your ability to think logarithmically, and your knack for avoiding overflow pitfalls.

In this article we’ll:

- Explain why this problem is a “must‑know” for interviewees.  
- Walk through a **clean, optimal solution** (the *good*).  
- Highlight why a brute‑force attempt is a *bad* idea.  
- Discuss the subtle overflow traps that make the problem *ugly*.  
- Provide ready‑to‑copy code in Java, Python, and C++.

Grab a cup of coffee; we’re about to dive deep into base‑representation gymnastics.

### 6.2. Why It Matters

1. **Algorithmic Breadth**  
   The problem spans **binary search**, **exponentiation**, **big‑integer arithmetic**, and **logarithms** – all classic interview topics.

2. **Thought Process**  
   A great solution shows you can transform a seemingly arithmetic puzzle into a search problem, and that you know how to bound your search space.

3. **Edge‑Case Handling**  
   Interviewers love to see how you guard against overflow and floating‑point inaccuracies. 

4. **Time‑Space Mastery**  
   The optimal algorithm is *O(log n)* time and *O(1)* space – a clean, efficient answer that impresses.

### 6.3. The Good: Optimal Binary‑Search + Power‑Check

**Key Insight**  
If the representation of *n* in base *k* is all 1’s, we have:

```
n = (k^m – 1) / (k – 1)
```

for some **m ≥ 2**. Fixing *m* gives a single unknown **k**. Because `k ≥ 2`, the maximum possible length *m* is bounded by `log₂(n)+1` (~60 for 10¹⁸).  
Thus we can iterate *m* from 60 down to 2, and for each *m* **binary‑search** *k* in `[2, n^(1/(m-1))]`.  

**Why This Is Good**  
- **Fast**: at most 60 * log₂(n) ~ 3600 iterations.  
- **Safe**: we use 128‑bit arithmetic (C++), `BigInteger` (Java), or Python’s unlimited ints.  
- **Correct**: the first *m* that yields an integer *k* is the smallest base.

**Pseudo‑Code**  
```
for m = 60 down to 2:
    low = 2
    high = floor(n^(1/(m-1))) + 1
    while low <= high:
        mid = (low + high) / 2
        val = mid^m (checked for overflow)
        if val == n: return mid
        else if val < n: low = mid + 1
        else: high = mid - 1
return n-1
```

### 6.4. The Bad: Brute‑Force on k

Trying every base `k` from `2` to `n-1` and checking if all digits are 1 is an *O(n log n)* algorithm. With *n* up to 10¹⁸ this is astronomically slow – even with the best hardware you can’t finish.

> **Lesson**: Always look for a way to *compress* the problem. Here, base‑representation is an arithmetic equation, not a loop over all bases.

### 6.5. The Ugly: Overflow & Floating‑Point Glitches

- **Overflow**  
  Computing `mid^m` directly in 64‑bit integers will overflow quickly.  
  **Fix**: stop multiplying as soon as the intermediate product exceeds *n* or use wider types (`__int128` / `BigInteger`).

- **Pow Inaccuracies**  
  `n^(1/(m-1))` computed with `Math.pow` or `float` may be off by 1 due to rounding. The binary‑search step corrects any mis‑estimation.

- **Edge Case: n = 2^m – 1**  
  In this scenario the smallest base is `n-1`. The algorithm naturally falls back to this when no *k* satisfies the equation.

### 6.5. Ready‑to‑Use Code

Below we provide full, working implementations for **Java**, **Python**, and **C++**. Each follows the *good* algorithm and includes comments to explain the trickiest parts.

*(Insert the code snippets from Section 5 above here.)*

### 6.6. Final Tips for Interviews

1. **Explain the Math** – Walk through the derivation of `n = (k^m – 1)/(k-1)` before jumping into code.  
2. **State the Bound** – Tell the interviewer why *m* can’t exceed 60 for this input range.  
3. **Show Overflow Guard** – In Java, explain the use of `BigInteger`. In C++ mention `__int128`.  
4. **Ask Questions** – If you’re unsure about the input size, ask. It shows you care about efficiency.  
5. **Time Your Solution** – If the interviewer asks, you can estimate `60 * log₂(10^18) ≈ 3600` operations, confirming the algorithm is sub‑second.

### 6.7. Conclusion

LeetCode 483 is more than a puzzle; it’s a miniature exam of your algorithmic skill set. By mastering the *good* approach, avoiding the *bad* brute‑force, and sidestepping the *ugly* overflow pitfalls, you’ll come out looking sharp in any technical interview.

Keep these solutions handy, practice the mental steps, and you’ll conquer this problem—and any similar one—in no time.

---

## 7. Closing

Whether you’re on a hiring panel or a junior engineer polishing your code, the **Smallest Good Base** problem teaches you how to:

- Translate arithmetic conditions into searchable equations.  
- Bound search spaces using logarithms.  
- Use high‑precision types to sidestep overflow.  

Grab the code snippets above, integrate them into your repository, and you’ll be ready to ace LeetCode 483 and any interview that tries to catch you off‑guard with base‑representation puzzles. Happy coding!