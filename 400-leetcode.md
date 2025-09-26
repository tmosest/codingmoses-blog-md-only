---
title: LeetCode 400. Nth Digit - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. The Problem (Leetcode 400 – **Nth Digit**)

Given an integer **n**, return the nth digit of the infinite integer sequence

```
1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, ...
```

> *Example*  
> `n = 11` → **0** (the 11th digit is the second digit of number 10)

> **Constraints**  
> `1 ≤ n ≤ 2³¹ – 1`  

The goal is to do this in **O(log n)** time without generating the entire sequence.



--------------------------------------------------------------------

## 2. Core Idea

Numbers are grouped by their number of digits.

| Digit length | Numbers | Total digits |
|--------------|---------|--------------|
| 1 | 1–9 | 9 |
| 2 | 10–99 | 90 × 2 = 180 |
| 3 | 100–999 | 900 × 3 = 2700 |
| … | … | … |

1. **Find the block** that contains the nth digit.  
   Subtract block sizes from *n* until *n* fits inside the current block.

2. **Locate the exact number** inside that block.  
   `number = start + (n‑1) / digitLength`

3. **Extract the digit** from that number.  
   Convert to string or use division/modulo.

--------------------------------------------------------------------

## 3. Reference Implementations

Below are clean, production‑ready solutions in **Java, Python, and C++**.  
All three use the same O(log n) math‑based approach.

---

### 3.1 Java

```java
public class Solution {
    public int findNthDigit(int n) {
        // 1‑digit numbers (1‑9)
        int digits = 1;
        long count = 9;           // how many numbers in this block
        long base  = 1;           // first number in this block

        while (n > digits * count) {
            n -= digits * count;  // skip the whole block
            digits++;             // next block
            count *= 10;          // 10× more numbers
            base  *= 10;          // first number is 10^digits
        }

        // the number that contains the nth digit
        long number = base + (n - 1) / digits;
        int digitIndex = (int) ((n - 1) % digits);   // 0‑based

        // return the required digit
        String s = Long.toString(number);
        return s.charAt(digitIndex) - '0';
    }
}
```

**Key points**

* `long` is used to avoid overflow when `n` is close to `2³¹ – 1`.  
* The algorithm is `O(log10 n)` because the loop iterates at most 10 times.



---

### 3.2 Python

```python
class Solution:
    def findNthDigit(self, n: int) -> int:
        digits = 1          # current block digit length
        count = 9           # numbers in this block
        start = 1           # first number in this block

        # Step 1 – find the block
        while n > digits * count:
            n -= digits * count
            digits += 1
            count *= 10
            start *= 10

        # Step 2 – locate the number
        number = start + (n - 1) // digits
        # Step 3 – digit inside the number
        digit_index = (n - 1) % digits
        return int(str(number)[digit_index])
```

**Why it works**

* The while‑loop runs ≤ 10 times (for 32‑bit integers).  
* Integer arithmetic is safe in Python – no overflow concerns.



---

### 3.3 C++

```cpp
class Solution {
public:
    int findNthDigit(int n) {
        long long digits = 1;    // length of current block
        long long count  = 9;    // numbers in current block
        long long start  = 1;    // first number in current block

        // 1) Find the block that contains the nth digit
        while (n > digits * count) {
            n -= digits * count;
            digits++;
            count *= 10;
            start *= 10;
        }

        // 2) Locate the exact number
        long long number = start + (n - 1) / digits;
        int digitIndex = (int)((n - 1) % digits);

        // 3) Extract the required digit
        string s = to_string(number);
        return s[digitIndex] - '0';
    }
};
```

* `long long` protects against overflow.  
* The solution follows the same math‑based strategy.



--------------------------------------------------------------------

## 4. Blog Article – “The Good, The Bad, and The Ugly of Leetcode 400”

> **SEO Keywords**:  
> *Nth Digit Leetcode*, *Java solution*, *Python solution*, *C++ solution*, *coding interview*, *algorithm interview*, *job interview*, *O(log n) algorithm*, *digit extraction problem*  

---

### 4.1 Introduction

The **Nth Digit** problem (Leetcode 400) is a classic interview puzzle that tests a candidate’s ability to reason about digit positions, arithmetic sequences, and careful handling of large numbers. While the statement looks simple, the hidden pitfalls can trip up even seasoned programmers. In this post, we dissect the problem into its *good* (best practices), *bad* (common mistakes), and *ugly* (edge‑case headaches) aspects, and we provide clean solutions in **Java, Python, and C++**. 

> *Why does mastering this problem help you land a job?*  
> Because it showcases:
> 1. **Mathematical insight** – turning a seemingly brute‑force problem into a log‑time formula.  
> 2. **Attention to overflow** – critical for interviews on 32‑bit/64‑bit boundaries.  
> 3. **Cross‑language portability** – you can explain the same algorithm in any stack.

---

### 4.2 Problem Recap

> **Goal:** Return the `n`‑th digit of the concatenated sequence `123456789101112…`.  
> **Constraints:** `1 ≤ n ≤ 2³¹ − 1` (≈ 2 billion).  
> **Typical pitfalls:**  
> * Generating numbers or strings until `n` – O(n) time and memory.  
> * Forgetting that `n` is 1‑based while array indices are 0‑based.  
> * Overflow when computing `10^digits` or `digit * count`.  

---

### 4.3 The *Good* – Elegant O(log n) Solution

#### 4.3.1 Mathematics at Work

1. **Block sizes**  
   * 1‑digit numbers: 9 × 1 = 9 digits  
   * 2‑digit numbers: 90 × 2 = 180 digits  
   * 3‑digit numbers: 900 × 3 = 2700 digits  

2. **Finding the block** – subtract block sizes until the remaining `n` fits.  
   Because each block’s size grows by a factor of 10, we need at most **10 iterations** for a 32‑bit integer.

3. **Exact number** – once we know the block, the target number is  
   `number = start + (n‑1) / digits`.

4. **Target digit** – the index inside that number is  
   `digitIndex = (n‑1) % digits`.  
   We can either convert to a string or use integer division/multiplication to isolate the digit.

#### 4.3.2 Why It’s Fast

The algorithm is **O(log n)**: the loop runs ~10 times, and the rest is constant‑time arithmetic.  
Space usage is **O(1)**.  
This efficiency is what interviewers want to see.

---

### 4.4 The *Bad* – Common Mistakes

| Mistake | Why it fails | Fix |
|---------|--------------|-----|
| **Brute‑force string building** (`for i in 1..n: result += i`) | `n` can be 2 billion → memory blow‑up & time out | Use math to jump directly |
| **Using int instead of long/long long** | `10^10` overflows `int` in Java/C++ | Use 64‑bit types |
| **Off‑by‑one errors** | `n` is 1‑based; many people mistakenly use 0‑based indices | Keep `(n-1)` until the final step |
| **Ignoring 0‑digit numbers** | 10 has two digits; counting digits incorrectly leads to wrong block | Subtract `digit * count` correctly |
| **Assuming the first digit is always non‑zero** | Numbers like 100 contain 0; forgetting to convert to string can miss leading zeros | Convert to string or handle via division |

---

### 4.5 The *Ugly* – Edge‑Case Quirks

1. **Largest `n`**  
   * `n = 2³¹ – 1 ≈ 2.1 billion`  
   * The 10‑digit block (`100 000 0000`‑`9 999 999 999`) starts at digit `9 999 999 999`  
   * Our algorithm gracefully handles this because `count` becomes `9 000 000 000` (still within `long`).

2. **Zero digit inside numbers**  
   * The first zero appears at position 10 (the number 10).  
   * Our digit extraction (`(n-1)%digits`) works because it indexes into the string representation, not the numeric value.

3. **Non‑decimal bases**  
   * The algorithm assumes base‑10. For interviews, clarify the base; otherwise the math changes.

---

### 4.6 Complete Code Snippets

> **Java**

```java
public class Solution {
    public int findNthDigit(int n) {
        int digits = 1;
        long count = 9;
        long base  = 1;
        while (n > digits * count) {
            n -= digits * count;
            digits++;
            count *= 10;
            base  *= 10;
        }
        long number = base + (n - 1) / digits;
        int digitIndex = (int) ((n - 1) % digits);
        return String.valueOf(number).charAt(digitIndex) - '0';
    }
}
```

> **Python**

```python
class Solution:
    def findNthDigit(self, n: int) -> int:
        digits, count, start = 1, 9, 1
        while n > digits * count:
            n -= digits * count
            digits += 1
            count *= 10
            start *= 10
        number = start + (n - 1) // digits
        return int(str(number)[(n - 1) % digits])
```

> **C++**

```cpp
class Solution {
public:
    int findNthDigit(int n) {
        long long digits = 1, count = 9, start = 1;
        while (n > digits * count) {
            n -= digits * count;
            digits++;
            count *= 10;
            start *= 10;
        }
        long long number = start + (n - 1) / digits;
        int digitIndex = (int)((n - 1) % digits);
        return to_string(number)[digitIndex] - '0';
    }
};
```

---

### 4.7 Testing Your Implementation

| Input | Expected | Reason |
|-------|----------|--------|
| 3 | 3 | First 3 digits are 1,2,3 |
| 11 | 0 | 10 contains 0 |
| 100 | 1 | 100th digit is the first '1' of number 10 |
| 190 | 9 | Last digit of block of 2‑digit numbers |
| 1900 | 1 | First digit of block of 3‑digit numbers |
| 2147483647 | 7 | Edge case, largest 32‑bit int |

Use unit tests or quick scripts to validate each case.

---

### 4.8 Take‑Away Points for Your Job Hunt

1. **Showcase mathematical thinking** – interviewers love algorithms that skip the naive O(n) route.  
2. **Handle edge cases confidently** – demonstrate overflow awareness and 1‑based vs 0‑based thinking.  
3. **Deliver clean, cross‑language code** – be prepared to explain your solution in Java, Python, or C++ depending on the interview stack.  
4. **Explain time & space complexity** – *O(log n)* time, *O(1)* space.  
5. **Tie back to interview patterns** – this problem is a variant of “digit DP” and “sequence position” questions that appear on many interview tracks.

---

### 4.9 Final Thoughts

The Nth Digit problem is deceptively simple but packed with interview‑relevant concepts: arithmetic sequences, logarithmic search, overflow handling, and clean code. Mastering it not only earns you a 6‑month “solve” on Leetcode but also shows recruiters that you can transform a seemingly brute‑force challenge into an elegant O(log n) solution.

Happy coding, and may your next interview be *n*th‑to‑the‑best!