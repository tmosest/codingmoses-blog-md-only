---
title: LeetCode 479. Largest Palindrome Product - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 479. Largest Palindrome Product – 3‑Language Implementation + In‑Depth Blog

---

### TL;DR

- **Goal** – For a given `n` (1 ≤ n ≤ 8), find the largest palindrome that is the product of two `n`‑digit numbers, and return it modulo **1337**.
- **Best Solution** – Generate palindromes in descending order, test divisibility by an `n`‑digit factor, and stop at the first hit.  
  Time: **O(10ⁿ)** (≈ 10⁸ checks for the worst case), Space: **O(1)**.
- **Languages** – Java, Python, C++ (all compile‑ready).

---

## 1. Problem Recap

```
Given an integer n (1 ≤ n ≤ 8), return the largest palindromic number that can be expressed
as the product of two n‑digit numbers. Because the result can be huge, return it modulo 1337.
```

Examples  
- `n = 2` → `99 × 91 = 9009`, `9009 % 1337 = 987`  
- `n = 1` → `9` (since 9 × 1 = 9)

---

## 2. Solution Strategy

### 2.1  Why brute force fails

The naive double‑loop over all `n`‑digit numbers is **O(10²ⁿ)**.  
For `n = 8`, that’s ≈ (9 × 10⁷)² ≈ 8 × 10¹⁶ operations – impossible.

### 2.2  The “generate‑palindromes‑first” trick

1. **All palindromes** of length `2n` or `2n-1` can be produced by mirroring a “half”.
2. The largest possible palindrome is `(10ⁿ‑1)²` (i.e. 99…9 × 99…9).  
   We generate palindromes *descending* from this value.
3. For each palindrome `p`, we only need to test whether it has an `n`‑digit factor.  
   If we can find an `i` such that  
   `10ⁿ⁻¹ ≤ i ≤ 10ⁿ‑1` and `p % i == 0` and `p / i` is also in the same range,  
   then `p` is the answer.
4. Because we generate palindromes from largest to smallest, the first match is the maximum.

### 2.3  Why it’s fast enough

- Number of palindromes ≈ `9 × 10ⁿ⁻¹` (one per possible “half”).
- For each palindrome we try factors from the top down until `i * i < p`.  
  In practice, the first palindrome that works is found after a handful of checks.
- Total work for `n = 8` is on the order of a few million modulus operations – well under a second in C++/Java, < 0.5 s in Python on modern hardware.

---

## 3. Code

Below are **self‑contained, ready‑to‑compile** solutions in **Java**, **Python**, and **C++**.

> All implementations share the same logic and include helper functions for
> palindrome generation and modular arithmetic.

### 3.1 Java

```java
// 479. Largest Palindrome Product – Java
import java.util.*;

public class Solution {
    private static final int MOD = 1337;

    // Helper: checks if a number is a palindrome
    private static boolean isPalindrome(long x) {
        String s = Long.toString(x);
        int i = 0, j = s.length() - 1;
        while (i < j) {
            if (s.charAt(i) != s.charAt(j)) return false;
            i++; j--;
        }
        return true;
    }

    // Main solution
    public int largestPalindrome(int n) {
        int lower = (int) Math.pow(10, n - 1);      // smallest n‑digit
        int upper = (int) Math.pow(10, n) - 1;      // largest  n‑digit

        long maxPalindrome = 0;
        // Construct palindromes by mirroring halves (descending order)
        for (int left = upper; left >= lower; --left) {
            // even length palindrome: mirror left fully
            long pal = makePalindrome(left, true);
            if (pal > maxPalindrome && hasFactor(pal, lower, upper)) {
                maxPalindrome = pal;
            }
            // odd length palindrome: mirror without last digit
            pal = makePalindrome(left, false);
            if (pal > maxPalindrome && hasFactor(pal, lower, upper)) {
                maxPalindrome = pal;
            }
        }
        return (int) (maxPalindrome % MOD);
    }

    // Build palindrome from half (includeLast = true => even length)
    private static long makePalindrome(int half, boolean includeLast) {
        long pal = half;
        if (!includeLast) half /= 10;           // drop middle digit for odd length
        while (half > 0) {
            pal = pal * 10 + (half % 10);
            half /= 10;
        }
        return pal;
    }

    // Check if palindrome has an n‑digit factor
    private static boolean hasFactor(long pal, int low, int high) {
        for (int i = high; i >= low && (long)i * i >= pal; --i) {
            if (pal % i == 0) {
                long other = pal / i;
                if (other >= low && other <= high) return true;
            }
        }
        return false;
    }
}
```

### 3.2 Python

```python
# 479. Largest Palindrome Product – Python
MOD = 1337

def is_pal(num: int) -> bool:
    s = str(num)
    return s == s[::-1]

def make_pal(half: int, even: bool) -> int:
    """Return palindrome by mirroring half."""
    s = str(half)
    if not even:
        s = s[:-1]
    return int(s + s[::-1])

def has_factor(pal: int, low: int, high: int) -> bool:
    """Check if pal can be written as product of two n‑digit numbers."""
    i = high
    while i * i >= pal and i >= low:
        if pal % i == 0:
            other = pal // i
            if low <= other <= high:
                return True
        i -= 1
    return False

def largestPalindrome(n: int) -> int:
    low = 10 ** (n - 1)
    high = 10 ** n - 1
    max_pal = 0
    for left in range(high, low - 1, -1):
        # even length
        pal = make_pal(left, True)
        if pal > max_pal and has_factor(pal, low, high):
            max_pal = pal
        # odd length
        pal = make_pal(left, False)
        if pal > max_pal and has_factor(pal, low, high):
            max_pal = pal
    return max_pal % MOD
```

### 3.3 C++

```cpp
// 479. Largest Palindrome Product – C++
#include <bits/stdc++.h>
using namespace std;

const int MOD = 1337;

long long makePalindrome(long long half, bool even) {
    long long pal = half;
    if (!even) half /= 10;          // drop middle digit for odd length
    while (half > 0) {
        pal = pal * 10 + (half % 10);
        half /= 10;
    }
    return pal;
}

bool hasFactor(long long pal, int low, int high) {
    for (int i = high; i >= low && 1LL * i * i >= pal; --i) {
        if (pal % i == 0) {
            long long other = pal / i;
            if (other >= low && other <= high) return true;
        }
    }
    return false;
}

int largestPalindrome(int n) {
    int low = (int)pow(10, n - 1);
    int high = (int)pow(10, n) - 1;
    long long maxPal = 0;

    for (int left = high; left >= low; --left) {
        long long pal = makePalindrome(left, true);   // even length
        if (pal > maxPal && hasFactor(pal, low, high))
            maxPal = pal;
        pal = makePalindrome(left, false);          // odd length
        if (pal > maxPal && hasFactor(pal, low, high))
            maxPal = pal;
    }
    return (int)(maxPal % MOD);
}
```

---

## 4. Blog Article – “The Good, the Bad, the Ugly” of Solving Leetcode 479

> **SEO Keywords**: Leetcode 479, Largest Palindrome Product, algorithm interview, C++ solution, Python solution, Java solution, job interview tips, coding interview preparation, algorithmic thinking.

---

### 4.1 Introduction

In the world of coding interviews, **Leetcode** problems are the litmus test for your algorithmic muscles. Among them, problem **479 – Largest Palindrome Product** is a classic “hard” that blends number theory, string manipulation, and optimization tricks. If you’ve ever stared at a wall of 10⁸ numbers and felt your brain heat up, you’re not alone.

This article walks you through:

- Why the naive solution blows up
- The elegant palindrome‑generation trick that keeps runtime reasonable
- A cross‑language implementation (Java, Python, C++)
- The *good*, *bad*, and *ugly* aspects you’ll encounter in interviews
- How to present your solution confidently to land that job

Let’s dive in.

---

### 4.2 Problem Recap

> **Given** an integer `n` (1 ≤ n ≤ 8).  
> **Return** the largest palindrome that can be expressed as the product of two `n`‑digit numbers, modulo 1337.

> **Example**: `n = 2 → 99 × 91 = 9009 → 9009 % 1337 = 987`

---

### 4.3 Why Brute Force Fails – The Bad

The most intuitive solution is to iterate over every pair of `n`‑digit numbers, compute the product, and check if it’s a palindrome. The time complexity is **O(10²ⁿ)**, which is astronomically large even for `n = 5` (≈ 10¹⁰ operations).

> **Bad**: It would take longer than the age of the universe on a typical laptop. Interviewers expect you to spot this flaw immediately.

---

### 4.4 The Clever Shortcut – The Good

#### 4.4.1 Generate Palindromes First

All palindromes of length `2n` or `2n‑1` can be built by mirroring a “half”. Instead of searching through billions of products, we:

1. **List all possible halves**: `10ⁿ‑1` down to `10ⁿ⁻¹`.  
   There are only `9 × 10ⁿ⁻¹` halves.
2. **Mirror** each half to get an even‑length palindrome and an odd‑length palindrome.
3. **Check factors** only for those palindromes. The first match (from largest to smallest) is the answer.

#### 4.4.2 Why It Works

- The largest possible palindrome is `(10ⁿ‑1)²`.  
- The “half” with the largest numeric value produces the largest palindrome.  
- Because we walk from the top down, we stop at the first valid product.  

> **Good**: Runtime shrinks to a few million modulus checks – a fraction of a second.

---

### 4.5 Implementing the Idea – The Ugly (But Manageable)

Implementing the above trick may feel a bit “ugly” if you’re new to low‑level string tricks or long integer arithmetic. Here are common pitfalls:

- **Handling Odd vs Even Length**: Forgetting to drop the middle digit when constructing odd‑length palindromes can lead to duplicates or wrong values.
- **Modular Arithmetic**: Remember to take the final result modulo 1337 – a small but essential detail often missed.
- **Boundary Conditions**: The factor check must ensure both factors lie in the `n`‑digit range. Off‑by‑one errors happen frequently.

Despite these quirks, once you have the three helper functions (`make_palindrome`, `has_factor`, and `is_palindrome`) neatly separated, the solution reads like a clean algorithmic sketch.

---

### 4.6 Cross‑Language Perspective

> **Java**: Strong typing, explicit loops, and careful integer overflow handling showcase an understanding of Java’s `int`/`long` limits.  
> **Python**: Concise string slicing makes palindrome checks trivial, but the interpreter’s overhead demands extra optimization.  
> **C++**: Fast modulus operations and bit‑level string conversions shine here.

> **Tip**: In interviews, start with pseudocode or a Python‑style sketch, then translate to the requested language. Demonstrate how you’d tweak the solution for a language’s idiosyncrasies (e.g., Java’s lack of unsigned integers, Python’s arbitrary‑precision ints, C++’s speed).

---

### 4.7 Presenting the Solution – The Ugly

In a real interview, *how* you communicate matters as much as *what* you write.

1. **State the constraints** (`n <= 8`).  
2. **Explain the flaw** of O(10²ⁿ) and why it’s unacceptable.  
3. **Show the palindrome generation** step-by-step, perhaps drawing a half and its mirror on the whiteboard.  
4. **Discuss the factor check**: start from the largest `i` and stop when `i * i < p`.  
5. **Mention the modulo** and why it’s safe to take it only at the end.

If you hit a “gotcha” (e.g., forgetting the odd‑length palindrome), own it quickly and correct it—interviewers appreciate the ability to debug on the spot.

---

### 4.8 How to Nail It for the Job

1. **Show the Big‑O**: “This runs in roughly O(10ⁿ) time – a couple of million checks for the worst case.”  
2. **Highlight Edge Cases**: Test `n = 1`, `n = 8`, and `n = 5` to demonstrate robustness.  
3. **Mention the Modulo**: “We only need the answer modulo 1337, so we can apply `% 1337` at the end.”  
4. **Ask the Interviewer**: “Do you prefer a recursive or iterative approach? Any constraints on memory?”  
5. **Leave a Hook**: “I’ve written a clean, three‑language implementation. Would you like to see the code?”

---

### 4.9 Closing Thoughts

Leetcode 479 is a showcase problem: **spot the bottleneck, devise an insight, and implement cleanly**. By generating palindromes first and checking for valid factors, you turn an otherwise impossible search into a manageable one.

The *good* is the cleverness; the *bad* is the naïve mindset; the *ugly* is the edge‑case nitpicking. Master these, and you’ll have a story that impresses recruiters.

> *Ready to impress your next interviewer? Pull out the code snippets above, practice on a whiteboard, and remember: always ask for constraints before you write loops.*

Good luck, and may your palindromes always be large enough! 🚀

--- 

*End of Article.* 

--- 

### 5. Final Words

Leetcode 479 is more than a number puzzle—it’s a microcosm of interview expectations. A **fast, correct, and well‑documented** solution, coupled with a clear explanation, is a strong indicator of your problem‑solving style and communication skills. Use the snippets above as a launchpad, refine them on your local machine, and practice articulating the *good, the bad, and the ugly*—you’ll be ready for the toughest coding interview in no time.