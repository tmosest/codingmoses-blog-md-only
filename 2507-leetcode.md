---
title: LeetCode 2507. Smallest Value After Replacing With Sum of Prime Factors - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  The Code – 2507. Smallest Value After Replacing With Sum of Prime Factors  

Below are three clean, production‑ready implementations (Java, Python, C++).  
Each one follows the same algorithmic idea:

1. **Prime‑factorise `n`.**  
   Sum the prime factors *with multiplicity*.  
2. **Replace `n` by that sum.**  
3. **Repeat until the sum equals `n`** – this happens only when `n` is prime.  
4. **Return the prime that the process converges to.**

All solutions run in \(O(\sqrt{n})\) per iteration and converge in at most a handful of iterations because the value of `n` monotonically decreases until it becomes prime.  
For the given constraint \(2 \le n \le 10^5\) the runtime is trivial – far below 1 ms in practice.

---

## 1.1  Java 17

```java
import java.util.*;

public class Solution {
    /**
     * Returns the smallest value reachable by repeatedly replacing
     * n with the sum of its prime factors.
     */
    public int smallestValue(int n) {
        while (true) {
            int sum = primeFactorSum(n);
            if (sum == n) {          // n is prime – stable
                return n;
            }
            n = sum;                  // continue with the new value
        }
    }

    /** Helper: sum of prime factors (with multiplicity). */
    private int primeFactorSum(int x) {
        int sum = 0;
        int num = x;

        // handle factor 2 separately to skip even numbers
        while (num % 2 == 0) {
            sum += 2;
            num /= 2;
        }

        // only odd factors remain
        for (int i = 3; i * i <= num; i += 2) {
            while (num % i == 0) {
                sum += i;
                num /= i;
            }
        }

        // if any prime > sqrt(x) remains
        if (num > 1) sum += num;

        return sum;
    }

    // --------------------------------------------------
    // Optional: a main method for quick manual testing
    // --------------------------------------------------
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.smallestValue(15)); // 5
        System.out.println(s.smallestValue(3));  // 3
    }
}
```

**Key Points**

- Uses **trial division** (optimized by skipping even numbers).  
- Works in **constant extra space** – only a few integer variables.  
- Very readable and easy to audit – perfect for an interview.

---

## 1.2  Python 3

```python
class Solution:
    def smallestValue(self, n: int) -> int:
        """
        Return the smallest value obtainable by repeatedly replacing
        n with the sum of its prime factors.
        """
        while True:
            s = self._prime_factor_sum(n)
            if s == n:          # n is prime
                return n
            n = s

    def _prime_factor_sum(self, x: int) -> int:
        """Return the sum of prime factors of x (with multiplicity)."""
        total = 0
        num = x

        # factor 2
        while num % 2 == 0:
            total += 2
            num //= 2

        # odd factors
        f = 3
        while f * f <= num:
            while num % f == 0:
                total += f
                num //= f
            f += 2

        if num > 1:
            total += num
        return total
```

**Pythonic Highlights**

- Helper function keeps the core logic readable.  
- Uses integer arithmetic only; no external libraries required.  
- The algorithm is O(√n) and runs in microseconds for `n <= 10^5`.

---

## 1.3  C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int smallestValue(int n) {
        while (true) {
            int sum = primeFactorSum(n);
            if (sum == n)          // n is prime
                return n;
            n = sum;               // continue
        }
    }

private:
    int primeFactorSum(int x) {
        int sum = 0;
        int num = x;

        // factor 2
        while (num % 2 == 0) {
            sum += 2;
            num /= 2;
        }

        // odd factors
        for (int i = 3; 1LL * i * i <= num; i += 2) {
            while (num % i == 0) {
                sum += i;
                num /= i;
            }
        }

        if (num > 1) sum += num; // remaining prime factor > sqrt(x)
        return sum;
    }
};
```

**C++ Notes**

- Uses `1LL * i * i` to avoid overflow (though not needed for `n ≤ 1e5`).  
- All logic lives in a single method – great for interview style.  
- Compiles with `-std=c++17` (or `-std=c++20` if you prefer).

---

# 2.  Blog Article – “The Good, the Bad, and the Ugly of LeetCode 2507”  

## 2.1  Title (SEO‑Optimized)

**“LeetCode 2507 – Smallest Value After Replacing With Sum of Prime Factors: The Good, The Bad, and The Ugly – Java, Python, C++ Solutions & Interview Tips”**

## 2.2  Meta Description

> Master LeetCode 2507 in seconds. Discover clean Java, Python, and C++ solutions, understand the algorithmic insights, and learn how to ace the interview with prime‑factorisation tricks.

## 2.3  Outline

1. Introduction  
2. Problem Recap  
3. Intuition – Why the Process Stabilises  
4. The Good  
   * Straightforward algorithm  
   * Low‑complexity factorisation  
   * Easy to implement in any language  
5. The Bad  
   * Naïve trial division vs. sieve  
   * Risk of overflow in other constraints  
6. The Ugly  
   * Recursive depth errors  
   * Poor caching leading to repeated work  
7. Code Walkthrough  
   * Java version  
   * Python version  
   * C++ version  
8. Complexity Analysis  
9. Interview‑Ready Tips  
10. Take‑away & Final Thoughts  

---

## 2.4  The Article (Markdown)

```markdown
# LeetCode 2507 – Smallest Value After Replacing With Sum of Prime Factors  
**The Good, The Bad, and The Ugly – Java, Python, C++ Solutions & Interview Tips**

---

## 📌 Problem Recap

You’re given an integer `n (2 ≤ n ≤ 10⁵)`.  
Repeatedly replace `n` with the sum of its prime factors (counting multiplicity).  
Return the smallest value that `n` will ever take on.

> **Example**  
> `n = 15 → 3 + 5 = 8 → 2 + 2 + 2 = 6 → 2 + 3 = 5`.  
> The answer is `5`.

---

## 🔎 Intuition

- **Prime numbers stay the same**:  
  The sum of the prime factors of a prime `p` is `p` itself.  
- **Composite numbers shrink**:  
  For any composite `m`, the sum of its prime factors is strictly smaller than `m`.  
- **Convergence**:  
  Therefore the sequence `n, f(n), f(f(n)), …` is strictly decreasing until it reaches a prime, which is the fixed point.  

Hence the answer is simply the first prime you encounter when repeatedly applying the “prime‑factor sum” function.

---

## 👍 The Good

| Feature | Why It Matters |
|---------|----------------|
| **O(√n) per step** | With `n ≤ 10⁵`, each factorisation takes at most ~300 iterations. |
| **Constant space** | Only a handful of integers – perfect for interview constraints. |
| **Language‑agnostic** | Same logic in Java, Python, C++. |
| **Readable code** | Clear separation of factorisation and loop. |

---

## ❌ The Bad

| Pitfall | Fix |
|---------|-----|
| **Naïve trial division** | Use step‑2 for odd numbers to halve the workload. |
| **Overflow** | Not an issue for `n ≤ 10⁵`; but if extended, use `long long`. |
| **Hard‑coded limits** | Keep the algorithm generic so it scales to larger `n`. |

---

## 😱 The Ugly

| Issue | What to avoid |
|-------|---------------|
| **Recursive implementation** | Can cause stack overflows if recursion depth grows. |
| **Repeated factorisation** | Caching the factor sums for seen numbers would be overkill here but may help for larger inputs. |
| **Inefficient I/O** | For interview practice, console I/O is fine; but in production use buffered streams. |

---

## 🛠️ Code Walkthrough

Below are the three implementations you can copy‑paste into your IDE.

### Java

```java
public class Solution {
    public int smallestValue(int n) {
        while (true) {
            int sum = primeFactorSum(n);
            if (sum == n) return n;  // prime found
            n = sum;
        }
    }
    private int primeFactorSum(int x) { … }  // see code above
}
```

### Python

```python
class Solution:
    def smallestValue(self, n: int) -> int:
        while True:
            s = self._prime_factor_sum(n)
            if s == n: return n
            n = s
    def _prime_factor_sum(self, x: int) -> int: …  # see code above
```

### C++

```cpp
class Solution {
public:
    int smallestValue(int n) {
        while (true) {
            int sum = primeFactorSum(n);
            if (sum == n) return n;
            n = sum;
        }
    }
private:
    int primeFactorSum(int x) { … }  // see code above
};
```

All three share the same helper `primeFactorSum`, which runs in `O(√x)`.

---

## ⏱️ Complexity Analysis

- **Time**: `O(√n * k)` where `k` is the number of iterations (≤ 6 for `n ≤ 10⁵`).  
- **Space**: `O(1)` – only a few local variables.

---

## 🎤 Interview‑Ready Tips

1. **Explain the convergence**: Talk about why the sequence hits a prime and stays there.  
2. **Mention optimisation**: “We skip even numbers after handling factor 2.”  
3. **Show off readability**: Keep helper methods small and well‑named.  
4. **Prepare for extensions**: If the constraints change (`n ≤ 10¹²`), use `long long` and consider a simple sieve for the first few prime checks.

---

## 🚀 Take‑away

LeetCode 2507 is a perfect example of how **simple number theory** can lead to a clean algorithm that is easy to implement, test, and explain.  
Master this one and you’ll have a handy interview trick for any problem involving prime factorisation.

Happy coding, and good luck on your next interview!
```

---

### Final Thoughts

- The key to *LeetCode 2507* is recognizing that the process always ends at a prime.  
- Stick to an **iterative, trial‑division** approach.  
- Showcase **clean code** and **clear reasoning** – that’s what interviewers love.

Happy solving! 🚀

```

## 2.5  Why This Helps Your Career

- **Google‑style readability**: Clean, bug‑free code is prized at tech giants.  
- **Language coverage**: Interviewers often test in multiple languages; having all three ready demonstrates versatility.  
- **Algorithmic insight**: Explaining the convergence and complexity shows deeper understanding than just “I coded it.”

---

# 3.  Closing Summary

We delivered:

- **Three production‑ready solutions** (Java, Python, C++).  
- **Detailed article** with SEO keywords, interview guidance, and complexity analysis.  
- **Clear explanations** of the algorithm’s behaviour – the good, the bad, and the ugly.

With these resources in your toolbox, you’re fully prepared to nail LeetCode 2507 and any interview question that touches prime factorisation. Happy coding! 🎯

```

---  

*Feel free to tweak the article for your personal blog platform (WordPress, Medium, Dev.to). The structure, headings, and keywords are crafted to rank high on search queries like “LeetCode 2507 solutions” or “prime factorisation interview problems.”*
```

---  

**Happy Interviewing!**