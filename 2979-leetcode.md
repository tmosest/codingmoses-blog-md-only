---
title: LeetCode 2979. Most Expensive Item That Can Not Be Bought - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem Overview – “Most Expensive Item That Can Not Be Bought”

| LeetCode ID | 2979 |  
|-------------|------|  
| Difficulty  | Medium |  
| Tag         | Math, Number Theory |  

**Statement**  
You’re given two distinct prime numbers `primeOne` and `primeTwo`.  
You have an infinite amount of coins in those two denominations.  
For every positive integer `x` there is an item that costs `x`.  
Return the highest price of an item that **cannot** be paid exactly with any
combination of the two coins.

**Examples**

| `primeOne` | `primeTwo` | Output | Explanation |
|------------|------------|--------|-------------|
| 2 | 5 | 3 | Unbuyable prices: {1,3}. All >3 are buyable. |
| 5 | 7 | 23 | Unbuyable prices: {1,2,3,4,6,8,9,11,13,16,18,23}. All >23 are buyable. |

**Constraints**

* 1 < `primeOne`, `primeTwo` < 10⁴  
* `primeOne` and `primeTwo` are prime  
* `primeOne` × `primeTwo` < 10⁵  

---

## 🔑 Insight – Chicken McNugget Theorem

When you have two coprime positive integers `a` and `b`, the largest integer that cannot be expressed as  
`a * x + b * y` with non‑negative integers `x, y` is

```
maxNotRepresentable = a * b - a - b
```

Both primes are distinct, so they are automatically coprime.
Hence the answer is simply

```
primeOne * primeTwo - primeOne - primeTwo
```

No loops or DP are needed – the solution runs in **O(1)** time and **O(1)** space.

---

## 🧑‍💻 Implementation – Three Languages

### 1️⃣ Java

```java
// 2979. Most Expensive Item That Can Not Be Bought
class Solution {
    public int mostExpensiveItem(int primeOne, int primeTwo) {
        // Chicken McNugget theorem for coprime primes
        return primeOne * primeTwo - primeOne - primeTwo;
    }
}
```

### 2️⃣ Python

```python
# 2979. Most Expensive Item That Can Not Be Bought
class Solution:
    def mostExpensiveItem(self, primeOne: int, primeTwo: int) -> int:
        # O(1) formula: a*b - a - b
        return primeOne * primeTwo - primeOne - primeTwo
```

### 3️⃣ C++

```cpp
// 2979. Most Expensive Item That Can Not Be Bought
class Solution {
public:
    int mostExpensiveItem(int primeOne, int primeTwo) {
        // Chicken McNugget theorem for coprime primes
        return primeOne * primeTwo - primeOne - primeTwo;
    }
};
```

All three snippets are ready to drop into LeetCode’s “Submit” button and pass every test case instantly.

---

## 📚 Blog Article – “The Good, the Bad, and the Ugly of the Chicken McNugget Problem”

> **Title**:  
> “How to Crack LeetCode 2979 in 10 Seconds: The Chicken McNugget Theorem Explained”

> **Meta Description**:  
> “Learn how to solve LeetCode 2979 in O(1) time with the Chicken McNugget Theorem. Get Java, Python, and C++ solutions, plus a detailed blog on the math behind it.”

> **Keywords**:  
> LeetCode 2979, most expensive item that can not be bought, chicken mcnugget theorem, coin problem, number theory, coding interview, Java solution, Python solution, C++ solution, O(1) algorithm, DP vs math.

---

### Introduction

When I first saw *LeetCode 2979 – Most Expensive Item That Can Not Be Bought*, I assumed a dynamic‑programming (DP) approach was inevitable: “We have two coin denominations, can we compute all representable amounts?”  
But the constraints were tiny (`primeOne * primeTwo < 10⁵`) and the test cases were simple.  
The real secret? A classic number‑theory result called the **Chicken McNugget Theorem**.

---

### The Good – Why the Formula Works

1. **Coprime Primes** – Any two distinct primes are coprime, i.e., `gcd(primeOne, primeTwo) == 1`.  
   This is the exact condition the theorem requires.

2. **Largest Unattainable Sum** – The theorem tells us the largest integer that **cannot** be written as  
   `primeOne * x + primeTwo * y` (x, y ≥ 0) is  
   `primeOne * primeTwo – primeOne – primeTwo`.

3. **Constant‑Time Answer** – Once you know the formula, the answer is a single arithmetic operation.  
   That’s why all three solutions run in *O(1)* time.

---

### The Bad – Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Off‑by‑One Errors** | Forgetting that the theorem applies to *positive* integers > 0. | Use `primeOne * primeTwo - primeOne - primeTwo`. |
| **Overflow (Java)** | `int` multiplication could overflow for larger primes (but constraints keep it safe). | Still safe because `primeOne * primeTwo < 10⁵`. |
| **Misinterpreting “Distinct”** | Some people assume primes could be equal, which would break the coprime condition. | Problem guarantees they’re distinct. |

---

### The Ugly – When You Don’t Know the Theorem

If you’re stuck and haven’t heard of the theorem, you might:

1. **Write a DP** that explores all sums up to `primeOne * primeTwo`.  
   Time: `O(n²)` in the worst case (n ~ 10⁵) → ~10⁹ operations → too slow.  

2. **Implement a BFS/DP with a Queue** that keeps adding `primeOne` and `primeTwo`.  
   Still ends up exploring ~10⁵ states, but it’s unnecessary.

3. **Trial‑and‑Error Enumeration** until you notice the pattern.  
   This wastes time and leads to flaky solutions that may fail hidden tests.

---

### Final Takeaway

The LeetCode 2979 problem is a textbook example of how a little math can turn a seemingly hard DP problem into a one‑liner.  
Remember:

```
answer = primeOne * primeTwo - primeOne - primeTwo
```

Add this to your interview toolkit, and you’ll impress interviewers with both your coding and mathematical chops!

---

### SEO Checklist

- **Title** & **Meta** contain main keyword (“LeetCode 2979”).
- **Intro** hooks readers with a story (first encounter).
- **Headings** (`#`, `##`, `###`) break up content for readability & SEO.
- **Code blocks** are language‑specific and ready for copy‑paste.
- **Bullet lists** for pitfalls make the article skimmable.
- **Keywords** sprinkled naturally throughout.

---

**Happy coding, and may your interview be as smooth as that one‑liner!**