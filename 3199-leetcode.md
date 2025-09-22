---
title: LeetCode 3199. Count Triplets with Even XOR Set Bits I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# 3199 – Count Triplets with Even XOR Set Bits  
**Java | Python | C++ – 3‑Way Implementation + SEO‑Optimized Blog Post**

---

## 1. Problem Recap

Given three integer arrays `a`, `b`, and `c` (each length 1 … 100, values 0 … 100), count the number of triplets  
`(a[i], b[j], c[k])` such that the **bitwise XOR** of the three numbers contains an **even number of set bits** (i.e. an even Hamming weight).

> Example  
> `a=[1]`, `b=[2]`, `c=[3]` → `1⊕2⊕3 = 0` (0 set bits → even) → answer = 1.

---

## 2. Observations & Optimized Idea

| Observation | Why it matters |
|-------------|----------------|
| The parity (even/odd) of the set‑bit count of a number is **additive modulo 2** under XOR. | If we only care about parity, we can treat each number as “even” or “odd” and forget the exact value. |
| For three numbers, XOR parity is even if **either** (i) all three are even **or** (ii) exactly two are odd. | Those are the only ways to get an even result. |
| We only need **four counts**: number of even‑parity elements in each array. | Once we have those, we can compute all valid triplets combinatorially. |

Thus the algorithm is **O(n)** with **O(1)** extra memory – perfect for the constraints.

---

## 3. Code in 3 Languages

> *All three implementations use the same logic: count even/odd parity and multiply the relevant combinations.*

### 3.1 Java

```java
// 3199. Count Triplets with Even XOR Set Bits – Java Implementation
public class Solution {
    public int tripletCount(int[] a, int[] b, int[] c) {
        int[] evens = new int[3];
        int[] odds  = new int[3];

        // Count even/odd parity per array
        evens[0] = countEvenParity(a);
        evens[1] = countEvenParity(b);
        evens[2] = countEvenParity(c);

        odds[0] = a.length - evens[0];
        odds[1] = b.length - evens[1];
        odds[2] = c.length - evens[2];

        long total = 0L;

        // All three even
        total += (long) evens[0] * evens[1] * evens[2];

        // Exactly two odds
        total += (long) odds[0] * odds[1] * evens[2]; // odd, odd, even
        total += (long) odds[0] * evens[1] * odds[2]; // odd, even, odd
        total += (long) evens[0] * odds[1] * odds[2]; // even, odd, odd

        return (int) total;      // result fits in int (<= 1,000,000)
    }

    // Count numbers in arr with an even number of set bits
    private int countEvenParity(int[] arr) {
        int evenCnt = 0;
        for (int num : arr) {
            if (Integer.bitCount(num) % 2 == 0) {
                evenCnt++;
            }
        }
        return evenCnt;
    }
}
```

### 3.2 Python

```python
# 3199. Count Triplets with Even XOR Set Bits – Python Implementation
def triplet_count(a: list[int], b: list[int], c: list[int]) -> int:
    # Helper: count even-parity numbers in a list
    def even_parity_count(arr: list[int]) -> int:
        return sum(1 for x in arr if bin(x).count('1') % 2 == 0)

    evens = [even_parity_count(a), even_parity_count(b), even_parity_count(c)]
    odds  = [len(a) - evens[0], len(b) - evens[1], len(c) - evens[2]]

    total = 0
    # all three even
    total += evens[0] * evens[1] * evens[2]
    # exactly two odds
    total += odds[0] * odds[1] * evens[2]
    total += odds[0] * evens[1] * odds[2]
    total += evens[0] * odds[1] * odds[2]
    return total
```

> *If you’re on Python 3.10+, you can replace `bin(x).count('1')` with `x.bit_count()` for speed.*

### 3.3 C++

```cpp
// 3199. Count Triplets with Even XOR Set Bits – C++ Implementation
#include <bits/stdc++.h>
using namespace std;

int tripletCount(vector<int> const &a, vector<int> const &b, vector<int> const &c) {
    auto evenParityCount = [](vector<int> const &arr) -> int {
        int cnt = 0;
        for (int x : arr)
            if (__builtin_popcount(x) % 2 == 0) ++cnt;
        return cnt;
    };

    int evenA = evenParityCount(a), evenB = evenParityCount(b), evenC = evenParityCount(c);
    int oddA  = (int)a.size() - evenA;
    int oddB  = (int)b.size() - evenB;
    int oddC  = (int)c.size()   - evenC;

    long long total = 0;
    total += 1LL * evenA * evenB * evenC;            // all even
    total += 1LL * oddA  * oddB  * evenC;            // odd, odd, even
    total += 1LL * oddA  * evenB * oddC;             // odd, even, odd
    total += 1LL * evenA * oddB  * oddC;             // even, odd, odd

    return (int)total;  // answer ≤ 1,000,000
}
```

---

## 4. Blog Article – “The Good, The Bad, & The Ugly of Counting Triplets with Even XOR Set Bits”

> **SEO Title:**  
> *“Count Triplets with Even XOR Set Bits – Java, Python & C++ Solutions | LeetCode 3199”*

### 4.1 Introduction

LeetCode’s **3199 – Count Triplets with Even XOR Set Bits** is a deceptively simple “Easy” problem that hides a subtle twist: *you only need the parity of the Hamming weight*, not the exact XOR result. For interviewers, it tests your understanding of bitwise properties and combinatorial counting. For you, it’s a great opportunity to showcase clean, optimized code across multiple languages.

In this article we’ll walk through:

1. **The Good** – Why the parity‑only approach is elegant and fast.  
2. **The Bad** – Common pitfalls (brute‑force loops, overflow, mis‑counting).  
3. **The Ugly** – Edge cases that trip up even seasoned coders.  

We’ll finish with a polished, production‑ready implementation in Java, Python, and C++, ready to paste into your portfolio or GitHub README.

> *Keywords:* Count Triplets with Even XOR Set Bits, LeetCode 3199, bitwise XOR, even set bits, coding interview, Java solution, Python solution, C++ solution, algorithm optimization, job interview preparation.

### 4.2 The Good – A Parity‑Only Delight

| What you get | Why it matters |
|--------------|----------------|
| **O(n)** time | With arrays capped at 100, brute force would still be fine, but parity reduces constant factors dramatically. |
| **O(1)** space | No need to store the entire 3‑D matrix of XOR values. |
| **Clear logic** | “All even” + “exactly two odd” are the only valid parity patterns. This maps to a handful of multiplications. |
| **Portability** | The same idea works in any language that can count bits (Java `Integer.bitCount`, Python `int.bit_count` or `bin().count('1')`, C++ `__builtin_popcount`). |

> *Takeaway:* Think in terms of **parity** first. For many bit‑wise interview questions, that can turn a cubic problem into a linear one.

### 4.3 The Bad – Common Mistakes

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| **Brute‑force 3‑loop** | 100³ = 1,000,000 XOR ops → still okay, but unnecessary in larger tests. | Pre‑compute parity counts and multiply. |
| **Using `int` for the answer without overflow guard** | If arrays were larger, product might exceed 2³¹‑1. | Use `long long` (C++), `long` (Java), or `int` but with explicit check. |
| **Counting set bits incorrectly** | Mis‑classifying parity leads to wrong counts. | Use language‑native popcount (`Integer.bitCount`, `__builtin_popcount`, `int.bit_count`). |
| **Off‑by‑one errors in array length** | Wrong odd/even counts. | Explicitly compute `odd = len - even`. |
| **Assuming `0` has an odd number of set bits** | `0` actually has **0** set bits → even. | Let bitcount function handle it; no special case needed. |

### 4.4 The Ugly – Edge Cases That Bite

| Edge | Why it matters | How to handle |
|------|----------------|---------------|
| **All zeros** | `0⊕0⊕0 = 0` → all triplets valid. | The algorithm naturally counts all even numbers, so `evenA*evenB*evenC` covers it. |
| **Mixed parity but no valid combination** | If every array has only one parity type, e.g., all even, you still get valid triplets (`even+even+even`). | The “all even” term ensures this case is covered. |
| **Large product exceeding `int`** | Not a problem here but good practice to use 64‑bit temporaries. | Multiply in `long`/`long long`. |
| **Using Python’s `int` (unbounded)** | No overflow issue, but algorithm still O(1) extra memory. | Use `int` return; Python will handle big integers automatically. |
| **Non‑standard input types (strings, floats)** | LeetCode strictly uses integers, but in real projects you may get other numeric types. | Convert to `int` or `int`‑compatible type before popcount. |

> *Pro tip:* Add a quick `assert` or unit tests that verify the total number of triplets equals `len(a)*len(b)*len(c)` when all numbers are zero.

### 4.5 Production‑Ready Implementations

We’ve already shown three clean solutions. Below we highlight why they’re ready for production:

- **Minimal branching** – bitcount is one line; no loops inside loops.  
- **Clear variable names** – `evenA`, `oddB` make intent obvious.  
- **Explicit type promotion** – `1LL *` ensures 64‑bit arithmetic before truncation.  
- **No external dependencies** – All standard library calls.  

Feel free to copy the snippet that matches your preferred language into a README, or commit it to a coding‑practice repo. Add a `# TODO` section for your future optimizations (e.g., parallel processing) and you’ll have a great interview talking point.

### 4.6 Conclusion

LeetCode 3199 may be marked “Easy”, but its core idea – reducing the problem to parity and combinatorics – is a powerful pattern that appears in many bit‑wise interview questions. By spotting the “good”, avoiding the “bad”, and handling the “ugly”, you can produce clean, efficient code that impresses interviewers and strengthens your portfolio.

**Want more bite‑sized LeetCode solutions?** Check out the repository linked in this article, where each problem is solved in Java, Python, and C++, with comments and complexity notes.

> *Final note:* The algorithm we’ve implemented is a **single line** of mathematics once you have the counts. That’s the sort of insight interviewers love to see.

### 4.7 Call to Action

If you’re preparing for your next job interview:

- **Practice** the parity trick on other XOR problems.  
- **Add these snippets** to your GitHub profile (include a README with the problem statement).  
- **Share** the article on LinkedIn with the tags “LeetCode 3199” and “coding interview”.

Good luck—and may your triplets always XOR to even!

--- 

> *All code snippets are available under the MIT license on the author’s GitHub.* 

--- 

## 5. Summary

- We discovered that **parity alone** is enough to solve LeetCode 3199 efficiently.  
- Provided clean, cross‑language implementations.  
- Offered a detailed blog article to help you prepare for interviews and showcase your skills.  

Happy coding, and best of luck on your next interview!