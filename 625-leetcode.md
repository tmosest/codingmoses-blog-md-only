---
title: LeetCode 625. Minimum Factorization - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 625 – “Minimum Factorization”  
### The Good, The Bad, and The Ugly (and How to Nail It in a Coding Interview)

> **Problem** – *Given a positive integer `num`, return the smallest positive integer `x` such that the product of the digits of `x` equals `num`.  
> If no such `x` exists or the result doesn’t fit into a 32‑bit signed integer, return `0`.*

> **Constraints**  
> `1 ≤ num ≤ 2^31 – 1`

---

### TL;DR – 1‑Minute Summary

* The optimal solution is a **greedy factorization**: try to divide `num` by the largest possible digit (9 → 2) until it can’t be divided any more.
* Store the chosen digits in reverse order, then reverse them to get the smallest possible integer.
* Edge cases: `num < 10` → return `num` itself; the final product must equal 1; the final number must fit in a 32‑bit int.

---

## 1️⃣ The Good – What Works & Why It’s Elegant

| ✅ Feature | Why It’s Great |
|-----------|----------------|
| **O(log num)** time | We only divide by digits 9–2, at most 9×log₁₀(num) iterations. |
| **O(1) space** | No extra data structures beyond a few variables. |
| **Deterministic & Simple** | Easy to explain in an interview – “Start from the largest digit and factor it out.” |
| **Handles all edge cases** | Small numbers, perfect powers of 2, 3, etc. automatically. |

The greedy strategy works because any factorization of `num` into digits 2–9 can be reordered to place the larger digits earlier, which *decreases* the overall numeric value when the digits are placed in ascending order. By always pulling the largest possible factor first, we guarantee the smallest numeric result.

---

## 2️⃣ The Bad – Common Pitfalls & “What If” Scenarios

| ⚠️ Issue | What can go wrong | Fix |
|----------|-------------------|-----|
| **Result exceeds 32‑bit range** |  The product of digits may fit into a long but not an int. | Store the result in `long` (or `long long`), then check against `INT_MAX` before casting. |
| **Not reversing the digits** | If you append digits as you factor out, you’ll get the number in *descending* order (e.g., 68 for 48 → 8 then 6). | Append to a string/array and reverse at the end, or prepend while building the number. |
| **Dividing by 0 or 1** | `num` could be 1 or a single digit. | Treat `num < 10` as a base case: return `num` immediately. |
| **Misunderstanding “smallest positive integer”** | Some may think “smallest” means “smallest digits” rather than “smallest numeric value.” | Clarify that we want the *smallest* whole number (e.g., 35 < 53). |

---

## 3️⃣ The Ugly – Edge‑Case Traps and Hidden Test Cases

1. **`num = 1`**  
   *The correct answer is 1, not 0.*  
   Many naive solutions mistakenly return 0 for this case.

2. **`num = 0`** – *not allowed by constraints, but sometimes appears in custom tests.*  
   You must decide whether to treat it as invalid or return 10 (because 1×0 = 0). Stick to constraints.

3. **Very Large `num` (≈2^31‑1)**  
   If the factorization leads to a number like 999999999, the algorithm must still correctly detect overflow.

4. **Non‑factorable numbers (e.g., 17)**  
   The greedy loop will finish with a leftover > 1, indicating no solution → return 0.

---

## 4️⃣ The Algorithm – Step‑by‑Step

1. **Base case**  
   ```text
   if num < 10: return num
   ```
2. **Initialize**  
   ```text
   res = 0        // result as an integer
   factor = 1     // multiplier for each new digit
   ```
3. **Greedy loop (9 → 2)**  
   For each digit `d` from 9 down to 2:  
   while `num % d == 0`:  
   * divide `num` by `d`  
   * add `d` to the front of `res`  
     (i.e., `res = d * factor + res`)  
   * multiply `factor` by 10
4. **Final checks**  
   *If `num != 1` → return 0* (unfactored remainder).  
   *If `res > INT_MAX` → return 0* (overflow).  
   Otherwise, return `(int)res`.

---

## 5️⃣ Code – 3 Languages

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.

### Java

```java
public class Solution {
    public int smallestFactorization(int num) {
        if (num < 10) return num;           // 0–9 are already minimal
        long res = 0, factor = 1;           // long to guard against overflow

        for (int d = 9; d >= 2; d--) {
            while (num % d == 0) {
                num /= d;
                res = d * factor + res;    // prepend digit
                factor *= 10;
            }
        }

        // If num > 1, it contains prime factors > 9 → impossible
        // If res > Integer.MAX_VALUE, overflow
        return (num == 1 && res <= Integer.MAX_VALUE) ? (int)res : 0;
    }
}
```

### Python

```python
class Solution:
    def smallestFactorization(self, num: int) -> int:
        if num < 10:
            return num

        res = 0
        factor = 1

        for d in range(9, 1, -1):
            while num % d == 0:
                num //= d
                res = d * factor + res   # prepend digit
                factor *= 10

        if num != 1 or res > 2**31 - 1:
            return 0
        return res
```

### C++

```cpp
class Solution {
public:
    int smallestFactorization(int num) {
        if (num < 10) return num;

        long long res = 0;
        long long factor = 1;

        for (int d = 9; d >= 2; --d) {
            while (num % d == 0) {
                num /= d;
                res = d * factor + res;  // prepend digit
                factor *= 10;
            }
        }

        if (num != 1 || res > INT_MAX) return 0;
        return static_cast<int>(res);
    }
};
```

---

## 6️⃣ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Greedy loop | **O(log num)** (worst‑case ≈ 9 × log₁₀(num) divisions) | **O(1)** |
| Final checks | **O(1)** | – |

---

## 7️⃣ Alternative Approaches

| Method | Description | Pros | Cons |
|--------|-------------|------|------|
| **Depth‑First Search** | Recursively try all digits 2–9, prune when product exceeds `num`. | Handles arbitrary factorization patterns. | Exponential in worst case; not needed for this problem. |
| **Dynamic Programming** | Build the smallest number for each possible product. | Provides insight into sub‑problem structure. | High memory usage; unnecessary for this constrained problem. |
| **Prime Factorization** | Convert `num` into primes 2,3,5,7 and then combine into digits. | Conceptually clean. | Requires careful handling of combinations; essentially the same greedy logic. |

The greedy solution is the most interview‑friendly and runs in constant space.

---

## 8️⃣ Interview‑Ready Talking Points

1. **Explain the greedy rationale** – “We factor out the largest digit first; any valid factorization can be reordered to produce a smaller number if the largest digits are placed later.”  
2. **Show edge‑case handling** – talk through `num < 10`, overflow, and impossible factorizations.  
3. **Complexity discussion** – highlight the O(log num) time and O(1) space.  
4. **Mention overflow guard** – using a 64‑bit variable before checking `INT_MAX`.  
5. **Optional** – mention that the same logic works in any language with a 64‑bit integer type.

---

## 9️⃣ SEO‑Optimized Blog Post Outline

### Title
**LeetCode 625 – Minimum Factorization | Java, Python & C++ Solutions + Interview Tips**

### Meta Description
"Master LeetCode 625: Minimum Factorization. Read the full Java, Python, and C++ solutions, discover the greedy algorithm, edge cases, complexity, and interview‑ready talking points."

### Headings & Keywords
1. **What Is LeetCode 625 – Minimum Factorization?** (LeetCode 625, Minimum Factorization)
2. **Why Greedy Works for Factorization Problems** (greedy algorithm, interview coding)
3. **Java, Python & C++ – Full Working Code** (Java solution, Python solution, C++ solution)
4. **Edge Cases & Overflow in Factorization** (overflow handling, edge cases)
5. **Time & Space Complexity Breakdown** (time complexity, space complexity)
6. **Interview Tips: How to Explain Your Solution** (coding interview, LeetCode interview)
7. **Alternative Approaches (DFS, DP) – When to Use Them** (depth‑first search, dynamic programming)
8. **Common Pitfalls to Avoid** (common bugs, wrong answer, overflow)
9. **Practice Problems Similar to Minimum Factorization** (practice LeetCode problems, algorithm practice)

### Call‑to‑Action
> "Ready to ace your next coding interview? Dive into our full solution guide, experiment with the code, and add **Minimum Factorization** to your problem‑solving repertoire!"

---

## 🔚 Final Takeaway

- **The greedy factorization is optimal**: pull out 9→2 until no more divisions.
- **Edge cases matter**: handle `num < 10`, overflow, and non‑factorable numbers.
- **Keep it simple**: an interview‑friendly O(log num) algorithm that fits in O(1) space.
- **Show confidence**: explain why the greedy works and cover all edge cases.

By mastering this problem you demonstrate a strong grasp of number theory, greedy strategies, and interview best practices—exactly what recruiters look for. Good luck, and happy coding! 🚀