---
title: LeetCode 483. Smallest Good Base - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 483. Smallest Good Base – Code, Strategy & Career‑Boosting Blog

> **Short title for SEO**:  
> *“Smallest Good Base – LeetCode 483 – Java / Python / C++ Solution (Interview Ready)”*

---

### 1. Problem Recap (LeetCode 483)

> **Given** an integer `n` (as a string) in the range `[3, 10^18]`.  
> Find the **smallest integer base** `k (k ≥ 2)` such that the representation of `n` in base `k` consists **only of `1`s**.  
> Return `k` as a string.

> **Examples**

| n | Base `k` | Base‑`k` representation |
|---|----------|--------------------------|
| "13" | 3 | 111 |
| "4681" | 8 | 11111 |
| "1000000000000000000" | 999999999999999999 | 11 |

---

### 2. The Insight – Why Power‑Series Matter

If a number `n` is expressed in base `k` as `111…1` (m digits), mathematically:

\[
n = 1 + k + k^2 + \dots + k^{m-1}
  = \frac{k^m - 1}{k - 1}
\]

So for every possible `m` (the number of digits) we can search for a `k` that satisfies

\[
k^m - 1 = n (k - 1)
\]

> **Key idea** – iterate over `m` from the largest possible value down to `2` and test whether a suitable `k` exists.  
> The largest possible `m` is bounded by `log₂(n)`, which is at most **60** for `n ≤ 10¹⁸`.

Because `k` is an integer, we can recover it by

* approximating `k ≈ n^{1/(m-1)}` (using `pow` or binary search),  
* then **verifying** by recomputing the series to avoid floating‑point errors.

---

### 3. The Algorithm (O(log n) per `m`, 60 · log n overall)

1. **Parse** `n` as a 64‑bit integer (`long` in Java, `long long` in C++, Python int is unlimited).
2. For `m` from `max_m = floor(log₂(n))` down to `2`:
   * Compute a candidate base `k = floor(n^{1/(m-1)})` (or binary search on `[2, n]`).
   * Reconstruct the series:  
     ```
     value = 1
     for i = 1 … m-1:
         value = value * k + 1
         if value > n: break
     ```
   * If `value == n`, we found the smallest base (since we iterate from large `m` to small).
3. If no base is found, the only solution is `k = n-1` (representation `11`).  

> **Why start from large `m`?**  
> Larger `m` → smaller `k`. The first match is the answer.

---

### 4. Implementation in Three Languages

> All implementations handle **integer overflow** safely:  
> *Java* – uses `BigInteger` for the reconstruction loop.  
> *C++* – uses `__int128` inside the loop.  
> *Python* – native big integers.

---

#### Java (Fast, Production‑Ready)

```java
import java.math.BigInteger;

class Solution {
    public String smallestGoodBase(String n) {
        long num = Long.parseLong(n);
        // maximum possible digits
        int maxM = (int) (Math.log(num) / Math.log(2));

        for (int m = maxM; m >= 2; m--) {
            long k = (long) Math.pow(num, 1.0 / (m - 1));
            // try k and k+1 because rounding errors
            for (long cand = Math.max(2, k - 1); cand <= k + 1; cand++) {
                if (isValid(num, cand, m))
                    return String.valueOf(cand);
            }
        }
        // fallback: n-1
        return String.valueOf(num - 1);
    }

    private boolean isValid(long n, long k, int m) {
        BigInteger value = BigInteger.ONE;
        BigInteger base = BigInteger.valueOf(k);
        for (int i = 1; i < m; i++) {
            value = value.multiply(base).add(BigInteger.ONE);
            if (value.compareTo(BigInteger.valueOf(n)) > 0) return false;
        }
        return value.longValue() == n;
    }
}
```

---

#### Python (Elegant & Concise)

```python
class Solution:
    def smallestGoodBase(self, n: str) -> str:
        num = int(n)
        max_m = int(num.bit_length())  # log2(num)+1

        for m in range(max_m, 1, -1):
            k = int(num ** (1.0 / (m - 1)))  # floor
            for cand in (k - 1, k, k + 1):
                if cand < 2:
                    continue
                if self._check(num, cand, m):
                    return str(cand)
        return str(num - 1)

    @staticmethod
    def _check(n, k, m):
        val = 1
        for _ in range(1, m):
            val = val * k + 1
            if val > n:
                return False
        return val == n
```

---

#### C++ (High‑Performance, Uses 128‑bit)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string smallestGoodBase(string n) {
        unsigned long long N = stoull(n);
        int maxM = log2(N) + 1;                 // maximum digits

        for (int m = maxM; m >= 2; --m) {
            unsigned long long k = pow(N, 1.0 / (m - 1));
            for (long long cand = max(2LL, (long long)k - 1);
                 cand <= (long long)k + 1; ++cand) {
                if (valid(N, cand, m))
                    return to_string(cand);
            }
        }
        return to_string(N - 1);                // fallback
    }

private:
    bool valid(unsigned long long N, unsigned long long k, int m) {
        __int128 val = 1;
        for (int i = 1; i < m; ++i) {
            val = val * k + 1;
            if (val > N) return false;
        }
        return val == N;
    }
};
```

---

### 5. Good, Bad, and Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Mathematical elegance** | Expressing the problem as a geometric series gives a clean O(log n) algorithm. | None. | None. |
| **Runtime** | ≤ 60 · log₂(10¹⁸) ≈ 360 iterations – practically instant. | None. | None. |
| **Precision** | Uses integer arithmetic; no floating‑point bugs. | Potential rounding when computing `k` → check ±1. | If you use only double pow without a retry loop, you can miss the answer. |
| **Language support** | Java needs BigInteger, C++ needs `__int128`, Python native ints. | Each language requires a safe overflow‑aware loop. | Forgetting overflow handling in Java (`long`) or C++ (`long long`) leads to wrong answers. |
| **Code complexity** | 3‑4 lines of logic + helper. | None. | None. |

---

### 6. Edge Cases Handled

| Input | Expected Output | Why |
|-------|-----------------|-----|
| "3" | "2" | 3 = 11 in base 2. |
| "10^18" | "999999999999999999" | Only two digits: 11 in base `n-1`. |
| "9" | "2" | 9 = 1001₂? Wait 9=1001₂. But smallest good base is 2? 9 in base 2 is 1001, not all ones. Actually answer is 8? Let's test: 9 = 1001₂. 9 = 11₈. So answer is 8. | Illustrates that the algorithm catches the correct base via loop. |

---

### 7. Time & Space Complexity

| Language | Time | Space |
|----------|------|-------|
| Java | `O(log n)` (≈ 360 operations) | `O(1)` (plus BigInteger temp) |
| Python | Same as Java | `O(1)` |
| C++ | Same | `O(1)` |

---

### 8. Why This Matters for Your Resume

1. **Algorithm Design** – Shows you can reduce a seemingly hard problem to a simple loop by mathematical insight.
2. **Big‑Int Handling** – Demonstrates careful overflow management (Java BigInteger, C++ `__int128`, Python arbitrary ints).
3. **Multi‑Language Proficiency** – You can solve the same problem in Java, Python, and C++—a plus for backend or system‑level roles.
4. **Interview Strategy** – When you face a LeetCode problem on the phone, articulate: *"I first re‑formulate the problem using a geometric series, then iterate over possible lengths. This yields an O(log n) solution that is both fast and numerically safe."*

---

### 9. SEO‑Optimized Take‑Away

- **Keywords**: “Smallest Good Base”, “LeetCode 483”, “Java solution”, “Python solution”, “C++ solution”, “Interview coding problem”, “job interview algorithm”, “coding interview preparation”, “geometric series interview question”.
- **Meta Description**: “Solve LeetCode 483 – Smallest Good Base – with fast Java, Python, and C++ solutions. Understand the math, edge cases, and interview tips. Boost your coding interview score!”
- **Title Tag**: “Smallest Good Base – LeetCode 483 Solution (Java / Python / C++) – Interview Prep”

---

### 10. Final Words for the Job Seeker

- **Practice the math**: Re‑deriving the series gives you confidence when explaining the logic.
- **Test thoroughly**: Always cover boundary conditions (`n = 3`, `n = 10^18`, powers of two).
- **Show the code**: When interviewing, walk through your code, emphasise overflow checks, and mention that you can switch between languages seamlessly.
- **Leverage the blog**: Publish the article on LinkedIn, Medium, or your portfolio. Tag it with the keywords above. Recruiters often skim blogs to gauge depth of knowledge.
- **Keep building**: Add variations like “Smallest Good Base in base‑k representation” or “Finding base for other digit patterns” to showcase versatility.

Good luck on the interview! 🚀