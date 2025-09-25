---
title: LeetCode 1359. Count All Valid Pickup and Delivery Options - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📦 1359. Count All Valid Pickup and Delivery Options  
**Hard – LeetCode** | **Java | Python | C++** | **Dynamic Programming + Combinatorics**  

---

### TL;DR  
> **Answer** =  `(2n)! / 2ⁿ  (mod 1 000 000 007)`  
> Recurrence:  
> `dp[1] = 1`  
> `dp[i] = dp[i‑1] * i * (2i – 1)  (mod MOD)`  

Time `O(n)` | Space `O(1)`  

---

## 🚀 Why This Problem Rocks for Interview Prep

- **Hard level** but solvable with a *single* insight (factorial + division).
- Shows you can turn a combinatorial counting problem into an *efficient DP*.
- Demonstrates proficiency with modular arithmetic, which is a must‑know for interviews.
- Appears on the “LeetCode Hard” list and is often asked in **coding‑interview** questions.

---

## 📄 Problem Statement (Paraphrased)

You have **n** orders.  
Each order `i` consists of a *pickup* `Pᵢ` and a *delivery* `Dᵢ`.  
You must count the number of sequences of length `2n` that contain every `Pᵢ` and `Dᵢ` exactly once, with the constraint that `Dᵢ` always appears after `Pᵢ`.  
Return the answer modulo `10⁹+7`.

> **Example**  
> `n = 2` → 6 valid sequences:  
> (P1,P2,D1,D2), (P1,P2,D2,D1), (P1,D1,P2,D2), (P2,P1,D1,D2), (P2,P1,D2,D1), (P2,D2,P1,D1).

---

## 💡 The “Good” – A Simple Formula

The problem is a classic *Catalan‑style* counting problem.  
If you think about it:

1. **Arrange the pickups** in any order: `n!` ways.
2. **Insert deliveries**: after each pickup you can insert its delivery in any of the remaining open slots.

A clean combinatorial derivation shows that the total number of valid sequences is

```
(2n)! / 2ⁿ
```

- `2n` factorial: all possible permutations of the 2n items.
- Divide by `2ⁿ`: each order’s pickup–delivery pair is over‑counted twice (once for each possible internal ordering).

Because we work modulo `MOD`, division is handled as multiplication by the modular inverse of `2ⁿ`.

---

## 📈 The “Bad” – Direct Factorials and Mod Inverses

You could pre‑compute factorials up to `2n` and compute `inv(2)` raised to `n`.  
This works, but:

- Requires an extra array of size `2n`.
- Involves modular exponentiation and inverse, which can obscure the core idea.
- Slightly higher constant factors.

For interviews, clarity is usually better than micro‑optimizations.

---

## 🐛 The “Ugly” – Brute‑Force Enumeration

A naive approach would generate all `(2n)!` permutations and test the constraint.  
Complexity explodes (factorial) and is infeasible even for `n=10`.  
You’ll never get past the first few test cases.

---

## 🔑 The “Elegant” – DP Recurrence

From the formula `(2n)! / 2ⁿ` we can derive a simple recurrence:

```
dp[n] = dp[n-1] * n * (2n-1)   (mod MOD)
```

**Why does this work?**

```
dp[n] / dp[n-1] = ((2n)! / 2ⁿ) / ((2n-2)! / 2ⁿ⁻¹)
                = (2n)(2n-1) / 2
                = n(2n-1)
```

Thus, we only need a single loop from `2` to `n`.  
The recurrence uses only `long` arithmetic and no extra memory.

---

## 🧑‍💻 Code Implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.  
All use the DP recurrence described above.

---

### Java (Java 17)

```java
class Solution {
    private static final int MOD = 1_000_000_007;

    public int countOrders(int n) {
        long res = 1;          // dp[1] = 1
        for (int i = 2; i <= n; i++) {
            res = res * i % MOD;          // multiply by i
            res = res * (2L * i - 1) % MOD; // multiply by (2i-1)
        }
        return (int) res;
    }
}
```

---

### Python (Python 3)

```python
class Solution:
    MOD = 10 ** 9 + 7

    def countOrders(self, n: int) -> int:
        res = 1            # dp[1] = 1
        for i in range(2, n + 1):
            res = res * i % self.MOD
            res = res * (2 * i - 1) % self.MOD
        return res
```

---

### C++ (C++17)

```cpp
class Solution {
public:
    int countOrders(int n) {
        const long long MOD = 1'000'000'007LL;
        long long res = 1;                // dp[1] = 1
        for (int i = 2; i <= n; ++i) {
            res = res * i % MOD;                 // multiply by i
            res = res * (2LL * i - 1) % MOD;     // multiply by (2i-1)
        }
        return static_cast<int>(res);
    }
};
```

All three snippets run in **O(n)** time and **O(1)** space – perfect for interviews.

---

## 📊 Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| DP Recurrence | **O(n)** | **O(1)** |
| Factorial + Mod Inverse | **O(n)** | **O(n)** (factorial array) |
| Brute Force | **O((2n)!)** | **O(1)** |

The DP method is the sweet spot for interview settings.

---

## 🎯 Interview Tips

1. **Start with the combinatorial idea**: “I think the number of valid sequences equals (2n)! / 2ⁿ”.  
2. **Explain why division by 2ⁿ works** (each pair over‑counts).  
3. **Derive the recurrence** to show you can avoid heavy factorials.  
4. **Mention modulo arithmetic** and how you handle multiplication safely.  
5. **Time & space** – highlight that the solution is linear and constant space.

---

## 📚 Further Reading

- [Catalan Numbers – Wikipedia](https://en.wikipedia.org/wiki/Catalan_number)  
- [Modular Arithmetic in Programming Interviews](https://leetcode.com/articles/modular-arithmetic/)  
- [Counting Valid Orders – LeetCode Discuss](https://leetcode.com/problems/count-all-valid-pickup-and-delivery-options/discuss/)

---

## 💼 SEO‑Optimized Blog Summary

- **Keywords**: “LeetCode 1359”, “Count All Valid Pickup and Delivery Options”, “Hard LeetCode problem”, “DP solution”, “modular arithmetic”, “coding interview”, “Java Python C++”, “job interview coding”, “algorithm interview”.
- **Meta Description**: “Solve LeetCode 1359 – Count All Valid Pickup and Delivery Options – with elegant DP. Java, Python, C++ code, time/space analysis, interview tips, and SEO‑friendly article.”
- **Header Structure**: H1 – Problem, H2 – Solution Approach, H3 – DP Recurrence, etc.  
- **Internal Links**: Link to other interview blogs (e.g., “Hard LeetCode problems you should master”).  
- **External Links**: LeetCode problem page, Wikipedia pages for combinatorics.

---

### 🎉 Final Takeaway

- The **core insight** is the combinatorial formula `(2n)! / 2ⁿ`.  
- The **DP recurrence** `dp[i] = dp[i-1] * i * (2i-1)` gives a clean O(n) solution.  
- **Java, Python, and C++ implementations** are concise, fast, and interview‑friendly.  

Use this article as a reference when preparing for coding interviews, and feel free to adapt the snippets for your personal projects. Good luck with your next job interview! 🚀