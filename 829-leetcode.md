---
title: LeetCode 829. Consecutive Numbers Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 829. Consecutive Numbers Sum  
### “The Good, The Bad, and The Ugly” – A Deep‑Dive Blog

> **Want to land that Java/Python/C++ interview?**  
> Mastering this classic LeetCode “Consecutive Numbers Sum” problem is a *must‑know*.  
> It’s a quick 5‑minute interview question that tests *mathematical insight*, *loop optimization*, and *corner‑case handling* – exactly what hiring managers look for.

---

### TL;DR – What you’ll learn

| What | Why It Matters |
|------|----------------|
| **O(√n) math‑only solution** | Handles `n ≤ 10⁹` in < 1 ms |
| **O(log n) factor‑based solution** | Even faster, perfect for *big‑integer* interviewers |
| **Edge‑case handling** | Avoids overflow and off‑by‑one errors |
| **Clean code** | Ready‑to‑paste Java, Python, C++ snippets |

---

## 1️⃣ Problem Statement

> **Given an integer `n` (1 ≤ n ≤ 10⁹), count how many ways `n` can be written as a sum of one or more consecutive positive integers.**

Examples  
```
n = 5  → 2  (5 = 5, 5 = 2+3)
n = 9  → 3  (9 = 9, 9 = 4+5, 9 = 2+3+4)
n = 15 → 4  (15 = 15, 15 = 8+7, 15 = 4+5+6, 15 = 1+2+3+4+5)
```

---

## 2️⃣ The Good: Mathematical Insight

Let the consecutive sequence have length `k` and start with integer `x`.  
```
x + (x+1) + … + (x+k-1) = n
k·x + k(k-1)/2 = n
```
Rearrange for `x`:

```
k·x = n – k(k-1)/2          (1)
```

For a **valid** sequence:
* `x > 0` → RHS > 0
* `x` must be an integer → RHS divisible by `k`

So, for each `k` we just need to test:

```
n – k(k-1)/2 > 0  AND  (n – k(k-1)/2) % k == 0
```

### What bounds `k`?

`k(k-1)/2 < n`  →  `k(k-1) < 2n`  
Since `k(k-1)` grows ~ `k²`, we can stop when `k² >= 2n`.  
Thus the loop runs `O(√n)` times.

> **Result**: Counting all admissible `k` gives the answer in **O(√n)** time, **O(1)** space.

---

## 3️⃣ The Bad: Common Pitfalls

| Pitfall | Why It Breaks |
|---------|---------------|
| **Using `k*k < 2*n` without overflow guard** | For `n=10⁹`, `k*k` may overflow 32‑bit int. Use `long` or double. |
| **Starting `k` at 2** | You’ll miss the trivial representation `n = n`. Start at 1. |
| **Off‑by‑one in the division check** | `n - k*(k-1)/2` must be computed **after** the multiplication to avoid truncation. |
| **Neglecting that `k` can be 1** | All numbers can be represented by themselves. |
| **Assuming `n` is odd implies odd number of ways** | That holds for *odd divisors* but not for all `n`. |

---

## 4️⃣ The Ugly: A Faster, Factor‑Based Alternative

Another observation:  
The number of ways equals the count of **odd divisors** of `n`.

Proof sketch:

```
n = k·x + k(k-1)/2
⇔ 2n = k(2x + k - 1)
```

Because `k` and `2x + k - 1` have opposite parity, the factorisation reduces to counting odd divisors.

Implementation: factorise `n` in `O(√n)` and multiply `(exponent+1)` for odd primes.  
If `n` is a power of two, answer is 1 (only `n` itself).

> **Why ugly?**  
> It hides the intuition behind consecutive sums.  
> It also requires prime factorisation – more code, less readability.

---

## 5️⃣ Code: Ready‑to‑Paste Solutions

### 5.1 Java – O(√n) Loop

```java
public class Solution {
    public int consecutiveNumbersSum(int n) {
        int count = 0;
        // Use long to avoid overflow for k*k
        for (long k = 1; k * k < 2L * n; k++) {
            long rhs = n - k * (k - 1) / 2;
            if (rhs <= 0) break;          // safety, though loop condition already covers
            if (rhs % k == 0) count++;
        }
        return count;
    }
}
```

### 5.2 Python – O(√n) Loop

```python
class Solution:
    def consecutiveNumbersSum(self, n: int) -> int:
        count = 0
        k = 1
        while k * k < 2 * n:
            rhs = n - k * (k - 1) // 2
            if rhs > 0 and rhs % k == 0:
                count += 1
            k += 1
        return count
```

### 5.3 C++ – O(√n) Loop

```cpp
class Solution {
public:
    int consecutiveNumbersSum(int n) {
        int cnt = 0;
        for (long long k = 1; k * k < 2LL * n; ++k) {
            long long rhs = n - k * (k - 1) / 2;
            if (rhs > 0 && rhs % k == 0) ++cnt;
        }
        return cnt;
    }
};
```

---

## 6️⃣ SEO‑Optimized Blog Post Outline

> **Title:** *Consecutive Numbers Sum – 829 – Interview‑Ready Java/Python/C++ Code*

### Headings & Meta

| Heading | SEO Keywords |
|---------|--------------|
| 829. Consecutive Numbers Sum – Interview Problem | `consecutive numbers sum`, `leetcode 829`, `interview question` |
| The Good, The Bad, and The Ugly | `algorithm pitfalls`, `edge case handling` |
| Math‑Based O(√n) Solution | `sqrt(n) algorithm`, `sum of consecutive integers` |
| Fast Factor‑Based Trick | `odd divisor trick`, `prime factorisation` |
| Java/Python/C++ Code Snippets | `leetcode java solution`, `python solution`, `cpp solution` |
| Takeaway & Practice Tips | `leetcode practice`, `coding interview prep` |

### Meta Description (160 chars)

> Solve LeetCode 829 “Consecutive Numbers Sum” fast. Read our Java, Python, C++ O(√n) solution, factor‑based trick, pitfalls, and interview tips.

---

## 7️⃣ Final Take‑aways

1. **Always start the loop at `k = 1`.**  
2. **Use `long` (or `long long`) to avoid overflow** when squaring `k`.  
3. **Check divisibility after subtracting `k(k‑1)/2`.**  
4. **Optional:** Use the odd‑divisor trick if you’re comfortable with prime factorisation.  
5. **Explain the math** in interviews – it shows depth and confidence.

Good luck cracking LeetCode 829, and may your next interview go *smoothly*! 🚀