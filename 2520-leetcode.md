---
title: LeetCode 2520. Count the Digits That Divide a Number - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 2520. Count the Digits That Divide a Number  
*LeetCode Easy – 100 % beat rate in Java*  

### TL;DR  
- **Goal**: Count how many digits of `num` evenly divide `num`.  
- **Idea**: Extract digits one‑by‑one from the right and test divisibility.  
- **Complexity**: `O(log₁₀ num)` time, `O(1)` space (constant).  
- **Languages**: Java, Python, C++ – all ready‑to‑paste snippets.  

---

## 🎯 Problem Statement

> **Given** an integer `num` (1 ≤ num ≤ 10⁹) that contains **no zero** digits,  
> **Return** the number of digits in `num` that divide `num` evenly (`num % digit == 0`).  

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `7`   | `1`    | 7 divides itself. |
| `121` | `2`    | Digit `1` appears twice → 2. |
| `1248`| `4`    | All digits divide 1248. |

---

## 🔍 Why This Problem Is a Gold‑Mine for Interview Prep

- **Classic digit‑processing trick** – learn how to peel off digits with `% 10` and `/ 10`.  
- **Edge‑case awareness** – no zero digits → no division‑by‑zero worries.  
- **Time‑space trade‑off** – simple linear scan, O(1) auxiliary memory.  
- **Cross‑language practice** – implement once, read in Java, Python, C++.  

---

## 🧠 Solution Overview

1. **Keep a copy** of the original number (`original = num`).  
2. **Loop** while `original > 0`  
   - `digit = original % 10` – last digit.  
   - `original /= 10` – drop the last digit.  
   - If `num % digit == 0`, increment `count`.  
3. Return `count`.  

Because we never build a string or an array, the algorithm is memory‑efficient.  

---

## 📊 Complexity Analysis

| Metric | Formula | Result |
|--------|---------|--------|
| **Time** | Each loop processes one decimal digit → `O(log₁₀ num)` | ~ 10 iterations for a 32‑bit int |
| **Space** | Only a handful of integers | `O(1)` |

---

## ⚠️ Common Pitfalls & Gotchas

| Mistake | Fix |
|---------|-----|
| *Trying to convert the number to a string and then iterating* | Use numeric operations (`% 10`, `/ 10`) – faster and more idiomatic. |
| *Allowing zero in the digit list* | Problem guarantees no zeros; if you code for a generic case, guard against `digit == 0` to avoid division by zero. |
| *Using a recursive approach that may blow the stack* | Iterative loop is safer for large numbers. |

---

## 📦 Code Snippets

Below are clean, ready‑to‑paste solutions in **Java, Python, and C++**. Feel free to copy, run, or adapt them for your interview prep.

### Java

```java
/**
 * LeetCode 2520: Count the Digits That Divide a Number
 * O(log num) time, O(1) space
 */
class Solution {
    public int countDigits(int num) {
        int original = num;   // keep the original number for divisibility checks
        int count = 0;        // result accumulator

        while (original != 0) {
            int digit = original % 10;   // get last digit
            original /= 10;              // remove last digit

            if (num % digit == 0) {      // check divisibility
                count++;
            }
        }
        return count;
    }
}
```

### Python

```python
# LeetCode 2520: Count the Digits That Divide a Number
# O(log num) time, O(1) space

def countDigits(num: int) -> int:
    original, count = num, 0
    while original:
        digit = original % 10
        original //= 10
        if num % digit == 0:
            count += 1
    return count
```

### C++

```cpp
// LeetCode 2520: Count the Digits That Divide a Number
// O(log num) time, O(1) space
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int countDigits(int num) {
        int original = num;
        int count = 0;
        while (original) {
            int digit = original % 10;
            original /= 10;
            if (num % digit == 0) ++count;
        }
        return count;
    }
};
```

---

## 📚 Walk‑through of the Java Implementation

1. **Variables**  
   - `original` preserves the original `num`.  
   - `count` stores the final answer.  

2. **Loop**  
   - `digit = original % 10` pulls the rightmost digit.  
   - `original /= 10` shifts the number right.  
   - If the digit divides `num`, increment `count`.  

3. **Return**  
   The loop ends when `original` is zero; `count` now holds the number of dividing digits.  

---

## 🔧 “The Good, The Bad, The Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Good** | Linear scan; constant space; no expensive string ops. | Easy to understand; perfect for interview questions. | None – this is a clean solution. |
| **Bad** | If the number were very large ( > 64‑bit ), you’d need BigInteger. | The problem guarantees no zeros, so we skip zero‑check. | If you accidentally divide by zero, the program crashes. |
| **Ugly** | The code’s style can be improved with early return for single‑digit numbers. | N/A | N/A |

---

## 🚀 How This Blog Helps Your Job Hunt

- **SEO Keywords**:  
  `LeetCode`, `Count the Digits That Divide a Number`, `coding interview`, `algorithm`, `Java solution`, `Python solution`, `C++ solution`, `O(log n) time`, `O(1) space`, `interview prep`, `software engineer interview`.  
  Including these terms in your résumé or portfolio will boost visibility on recruiter search engines.

- **Demonstrate Clean Code**:  
  The snippets showcase concise, production‑ready code—something recruiters love.

- **Showcase Cross‑Language Mastery**:  
  Providing solutions in multiple languages signals versatility.

- **Discuss Edge Cases & Complexity**:  
  Interviewers value candidates who think about performance and pitfalls.

- **Add a Call‑to‑Action**:  
  Invite recruiters to test your code on LeetCode or your GitHub repository.

---

## 📈 Next Steps for Your Interview Journey

1. **Run the code on LeetCode** – submit, check your runtime, and study the discussion for more tricks.  
2. **Create a GitHub Gist** – paste the three solutions, add the problem link, and mention the challenge.  
3. **Blog About It** – publish a short article (like this one) on Medium or dev.to, tag `#LeetCode`, `#interview`, `#coding`.  
4. **Apply for Roles** – use the blog link in your LinkedIn profile or résumé under “Projects” or “Technical Writing”.  

---

### 🎉 Final Takeaway

Counting the digits that divide a number is a deceptively simple LeetCode problem that teaches you:
- **Digit extraction** via modulo/division.  
- **Loop invariant** and **time/space complexity**.  
- **Clean, language‑agnostic coding**.  

By mastering it—and sharing your solution in a well‑structured blog—you’ll not only ace this question but also elevate your personal brand as a thoughtful, interview‑ready engineer. Good luck, and may your code always compile!  

---