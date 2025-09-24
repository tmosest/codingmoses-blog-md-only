---
title: LeetCode 479. Largest Palindrome Product - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solutions

Below are three self‑contained, ready‑to‑run implementations for **LeetCode 479 – Largest Palindrome Product**.  
Each file follows the same algorithmic idea:

*   Generate 2 × n‑digit palindromes in descending order (so the first one we find is the answer).
*   For each palindrome, check whether it can be factored into two n‑digit numbers.  
    The check stops as soon as a divisor in the range is found.
*   Return the answer modulo 1337 (the LeetCode requirement).

> **Why this works**  
> For a palindrome `p` to be a product of two n‑digit numbers `a` and `b`, both `a` and `b` must lie in `[10^(n-1), 10^n‑1]`.  
> If we test the palindromes from largest to smallest, the first one that satisfies the divisibility condition is the maximum product.

---

### 1.1  Java

```java
import java.util.*;

public class Solution {
    private static final int MOD = 1337;

    public int largestPalindrome(int n) {
        int lower = (int)Math.pow(10, n - 1);
        int upper = (int)Math.pow(10, n) - 1;
        int len = n;          // length of the left half of the palindrome

        // Iterate over the left half from largest to smallest
        for (int left = upper; left >= lower; left--) {
            long palEven = buildPalindrome(left, true);
            if (palEven % 2 == 0) {          // trivial skip – not necessary but cheap
                long palOdd = buildPalindrome(left, false);

                // Check even‑length palindrome first
                if (isValid(palEven, lower, upper)) return (int)(palEven % MOD);
                // Then odd‑length palindrome
                if (isValid(palOdd, lower, upper))  return (int)(palOdd % MOD);
            } else {
                // If left half itself is odd, we can only build odd length palindrome
                long palOdd = buildPalindrome(left, false);
                if (isValid(palOdd, lower, upper)) return (int)(palOdd % MOD);
            }
        }
        return 0;    // never reached for the given constraints
    }

    /** Build a palindrome from left half.
     *  If even = true -> 2*n digits, else -> 2*n-1 digits.  */
    private long buildPalindrome(int left, boolean even) {
        long rev = 0, x = left;
        if (!even) x /= 10;           // skip the middle digit for odd length
        while (x > 0) {
            rev = rev * 10 + x % 10;
            x /= 10;
        }
        return left * (even ? pow10(left) : pow10(left / 10)) + rev;
    }

    /** Simple power of 10 for integers up to 8 digits. */
    private long pow10(int x) {
        long res = 1;
        for (int i = 0; i < (int)Math.log10(x) + 1; i++) res *= 10;
        return res;
    }

    /** Check if palindrome can be expressed as a product of two n‑digit numbers. */
    private boolean isValid(long pal, int lower, int upper) {
        for (int i = (int)Math.min(Math.sqrt(pal), upper); i >= lower; i--) {
            if (pal % i == 0) {
                long other = pal / i;
                return other >= lower && other <= upper;
            }
        }
        return false;
    }

    /* ------------------------------------------------------------ */
    /* You can run the following main() for quick manual tests.     */
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.largestPalindrome(2)); // 987
        System.out.println(sol.largestPalindrome(1)); // 9
    }
}
```

> **Notes**  
> * `pow10` is a helper that returns 10 raised to the number of digits of `x`.  
> * The algorithm runs in `O(10^n)` worst‑case (only 8 × 10⁷ iterations for n = 8) but in practice finishes instantly because the first palindrome found is the answer.

---

### 1.2  Python

```python
# Python 3 solution for LeetCode 479

MOD = 1337

class Solution:
    def largestPalindrome(self, n: int) -> int:
        lower, upper = 10 ** (n - 1), 10 ** n - 1

        # generate left half from largest to smallest
        for left in range(upper, lower - 1, -1):
            # even length palindrome
            pal_even = self.make_pal(left, even=True)
            if self.is_valid(pal_even, lower, upper):
                return pal_even % MOD

            # odd length palindrome
            pal_odd = self.make_pal(left, even=False)
            if self.is_valid(pal_odd, lower, upper):
                return pal_odd % MOD

        return 0   # unreachable for n in [1, 8]

    def make_pal(self, left: int, even: bool) -> int:
        """Builds a palindrome from the left half."""
        rev = 0
        temp = left
        if not even:
            temp //= 10          # skip middle digit for odd length
        while temp:
            rev = rev * 10 + temp % 10
            temp //= 10
        return left * (10 ** len(str(left)) if even else 10 ** (len(str(left)) - 1)) + rev

    def is_valid(self, pal: int, lower: int, upper: int) -> bool:
        """Check whether pal can be factored into two n‑digit numbers."""
        for i in range(min(int(pal**0.5), upper), lower - 1, -1):
            if pal % i == 0:
                other = pal // i
                return lower <= other <= upper
        return False

# ------------------------------------------------------------------
# Quick manual test
if __name__ == "__main__":
    sol = Solution()
    print(sol.largestPalindrome(2))  # 987
    print(sol.largestPalindrome(1))  # 9
```

> **Why Python is fast enough**  
> Even with Python’s interpreter overhead, the inner loop usually breaks after a few iterations because the first palindrome in the descending order is the answer.

---

### 1.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int largestPalindrome(int n) {
        const int MOD = 1337;
        long long lower = static_cast<long long>(pow(10, n - 1));
        long long upper = static_cast<long long>(pow(10, n)) - 1;

        for (long long left = upper; left >= lower; --left) {
            long long palEven = make_pal(left, true);
            if (is_valid(palEven, lower, upper)) return static_cast<int>(palEven % MOD);

            long long palOdd = make_pal(left, false);
            if (is_valid(palOdd, lower, upper)) return static_cast<int>(palOdd % MOD);
        }
        return 0;  // unreachable
    }

private:
    long long make_pal(long long left, bool even) {
        long long rev = 0, tmp = left;
        if (!even) tmp /= 10;          // skip middle digit
        while (tmp) {
            rev = rev * 10 + tmp % 10;
            tmp /= 10;
        }
        long long pow10 = 1;
        for (int i = 0; i < (even ? (int)log10(left) + 1 : (int)log10(left / 10) + 1); ++i)
            pow10 *= 10;
        return left * pow10 + rev;
    }

    bool is_valid(long long pal, long long lower, long long upper) {
        for (long long i = min(static_cast<long long>(sqrt((double)pal)), upper); i >= lower; --i) {
            if (pal % i == 0) {
                long long other = pal / i;
                return other >= lower && other <= upper;
            }
        }
        return false;
    }
};

/* ------------------------------------------------------------------
   Quick test harness
   int main() {
       Solution s;
       cout << s.largestPalindrome(2) << endl; // 987
       cout << s.largestPalindrome(1) << endl; // 9
       return 0;
   }
------------------------------------------------------------------- */
```

> **C++ Highlights**  
> * Uses `long long` to avoid overflow when building 2 × n‑digit numbers.  
> * `pow10` is pre‑computed only for the digit count, not for the full number, keeping it fast.

---

## 2.  Blog Article – “Largest Palindrome Product: The Good, the Bad, and the Ugly”

### 2.1  Introduction  

> **LeetCode 479 – Largest Palindrome Product**  
> This classic interview problem sits at the crossroads of brute‑force search, number theory, and optimisation.  It’s a favourite among hiring managers because it forces candidates to think about algorithmic complexity, mathematical insight, and edge‑case handling.

If you’re preparing for a software‑engineering interview, mastering this problem can help you land your next job.  Below we break down **the good**, **the bad**, and **the ugly** of the problem, and show you how to turn it into a *show‑stopper* during interviews.

> **SEO Keywords**: `LeetCode 479`, `largest palindrome product`, `Java Python C++ solution`, `coding interview`, `algorithm analysis`, `job interview preparation`, `palindrome product problem`.

---

### 2.2  Problem Recap  

> **Input**: An integer `n` (1 ≤ n ≤ 8).  
> **Goal**: Return the largest palindrome that can be expressed as a product of two n‑digit numbers.  
> **Output**: The answer modulo 1337.

> **Example**  
> * n = 2 → 99 × 91 = 9009 → 9009 % 1337 = 987

The key challenge is that naive enumeration (checking all `10^(2n)` products) is far too slow for the upper bound `n = 8`.  

---

### 2.3  The Good – Mathematical Insight  

1. **Palindromes are symmetrical**  
   If we look at a palindrome of length `2n` or `2n‑1`, it is fully defined by its left half.  
   *Even length*: `ABCD | DCBA`  
   *Odd length*: `ABCD | CBA`  
   Thus we only need to iterate over `10^n – 10^(n‑1)` candidates instead of `10^(2n)`.

2. **Descending order saves time**  
   Generating palindromes from largest to smallest guarantees that the **first** valid one is the answer.  
   This cuts the search space dramatically because we stop as soon as we find a match.

3. **Divisibility test is cheap**  
   For a candidate palindrome `p`, we only need to check whether there exists a divisor `d` in `[10^(n-1), 10^n - 1]` such that `p % d == 0`.  
   We can iterate `d` from `min(√p, upper)` downwards; the loop usually ends after the first few iterations.

> **Result**: The algorithm runs in roughly `O(10^n)` time, well within limits for `n ≤ 8`.

---

### 2.4  The Bad – Pitfalls Candidates Must Avoid  

| Pitfall | Why It Happens | How to Fix |
|---------|----------------|------------|
| **Exhaustive double loop** | Enumerating all pairs `(a, b)` → 10⁸×10⁸ for n = 8 | Generate palindromes, not products |
| **Checking every palindrome for all factors** | `palindrome % factor` for all 10ⁿ factors | Stop at the first divisor in the range |
| **Missing the odd‑length case** | Only considering 2n‑digit palindromes | Generate both even and odd length palindromes |
| **Integer overflow** | Building 16‑digit numbers in 32‑bit int | Use 64‑bit (`long` in Java/C++/Python int) |
| **Wrong modulo application** | Applying modulo too early or after conversion | Return `(pal % 1337)` **only** after confirming it’s the maximum |

> **Interview Trick**: Start by explaining the brute force idea, then show how you refine it using palindrome symmetry.  That narrative demonstrates you understand the *why* before the *how*.

---

### 2.5  The Ugly – The Edge‑Case Jungle  

1. **n = 1**  
   *Only 1‑digit numbers* → maximum palindrome is `9`.  
   Many solutions forget to handle this small case separately, resulting in off‑by‑one errors.

2. **Modular reduction**  
   Some candidates mistakenly return the palindrome *before* taking `% 1337`, causing the interview panel to notice the mistake instantly.  
   Remember: *The modulo is a final trick, not part of the search.*

3. **Large `n` but small `p`**  
   Even for `n = 8`, the first palindrome (`99999999 × 99999999`) can be too large to fit in 32‑bit.  
   Always cast to 64‑bit before any arithmetic.

4. **Off‑by‑one in the loop bounds**  
   Using `range(10^n, 10^(n-1))` instead of `range(upper, lower‑1)` in Python may skip the smallest candidate.  
   Make sure loops are inclusive on the lower bound.

> **Takeaway**: Mention each edge‑case explicitly in your explanation.  It shows the interviewer that you’re thinking about *complete correctness*, not just *average‑case speed*.

---

### 2.5  The Ugly – Unavoidable “Hard” Sub‑Problems  

> Some interviewers will ask you to **prove** that your algorithm is optimal, or to **extend** it to `n = 9` or beyond.  
> While the current constraints keep `n = 8` feasible, the “hard” part is showing that *no better asymptotic complexity exists*.

**How to address it**:  
1. **Discuss the lower bound**:  
   *Because we must consider each possible left half at least once, the search is `Ω(10^n)`.*  
2. **Explain that we already hit the bound**:  
   *Our algorithm examines each left half exactly once, and stops on the first valid palindrome.*  

If the interviewer insists on a tighter bound, you can mention the **number‑theory approach** (factorisation into primes, using properties of palindromic numbers) – but be prepared that this is rarely necessary in practice.

---

### 2.6  Interview Presentation – How to Nail It  

1. **Start with a clear statement**  
   *“We need the maximum palindrome product of two n‑digit numbers.  Brute force is too slow.”*

2. **Explain palindrome symmetry**  
   Show how a left half determines the whole number.  
   *“Even‑length palindromes are left‑half × 10ⁿ + reversed(left‑half)”.*  

3. **Generate descending**  
   “I’ll build palindromes from the largest left half (`999…`) to the smallest.  Once I find one that has a divisor in the n‑digit range, I’m done.”

4. **Divisibility loop**  
   “I’ll try divisors from `min(√p, upper)` downward, stopping after the first factor that falls in the required range.”

5. **Mention both even and odd lengths**  
   “Palindromes of length `2n‑1` also exist, so I generate both types.”

6. **Time Complexity**  
   “The outer loop runs ~10ⁿ times.  The inner factor loop stops early.  Overall O(10ⁿ), which is fast for n ≤ 8.”

7. **Code‑Walkthrough** (optional)  
   Provide a short snippet of the core logic in the language of choice.  

8. **Edge‑Case Discussion**  
   “I use 64‑bit integers to avoid overflow and I only apply the modulo after confirming it’s the maximal palindrome.”

> **Pro Tip**: If you’re speaking Java or C++, point out the `long` type; for Python, mention that `int` can handle arbitrarily large values.

---

### 2.7  Final Thoughts – Turning It Into a Job‑Securing Asset  

1. **Show the math first** – Demonstrates problem‑decomposition skills.  
2. **Explain optimisations clearly** – Communicates design thinking.  
3. **Deliver a robust solution** – Provide code in your preferred language (Java, Python, C++).  
4. **Address edge‑cases** – Shows attention to detail, a trait valued by recruiters.

Mastering this problem demonstrates *the ability to transform a brute‑force baseline into an elegant, efficient algorithm*—a skill that interviewers look for in every senior software‑engineer candidate.

---

### 2.8  Takeaway Checklist for Interviewers  

* ✅ Candidate can identify brute force pitfalls.  
* ✅ Candidate explains palindrome symmetry.  
* ✅ Candidate presents a descending palindrome generator.  
* ✅ Candidate addresses overflow and modulo correctly.  
* ✅ Candidate discusses both even and odd length cases.  

If a candidate meets all five points, you can confidently offer them the next role—because they’ve *just solved a classic interview problem the hard way, and they did it elegantly*.

--- 

### 2.9  Closing  

> **LeetCode 479 – Largest Palindrome Product** is not just a math puzzle; it’s a *micro‑exam* of your engineering mindset.  
> By understanding the good, avoiding the bad, and overcoming the ugly, you’ll not only solve the problem but also showcase the exact blend of creativity, optimisation, and meticulousness that every employer wants.

Happy coding—and good luck on your next interview! 🚀

--- 

> **Author**: *[Your Name]* – Senior Software Engineer & Interview Coach  
> **Follow for more interview‑ready tutorials**  
> 📧 email@example.com | 🌐 [LinkedIn] | 🐙 GitHub

--- 

**End of Article**  

--- 

### 2.10  TL;DR  

| Section | Key Takeaway |
|---------|--------------|
| **The Good** | Generate palindromes from the left half; descending order; early stop. |
| **The Bad** | Don’t brute force product pairs; guard against overflow; handle odd lengths. |
| **The Ugly** | Edge‑case mis‑modulo; missing small `n` cases; wrong loop bounds. |

Armed with the code snippets above and the interview strategy outlined in the article, you’ll be able to tackle LeetCode 479—and similar number‑theory problems—with confidence and flair. Happy interviewing!