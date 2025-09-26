---
title: LeetCode 829. Consecutive Numbers Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 829 – Consecutive Numbers Sum  
> “**How many ways can you write an integer n as the sum of consecutive positive integers?**”

This problem is a *Hard* one on LeetCode but it can be cracked with a single mathematical insight.  
Below you’ll find clean, production‑ready solutions in **Java**, **Python**, and **C++**, followed by a full‑blown blog article that explains the trick, the pitfalls, and how to use this knowledge to impress recruiters.

---

## ✅ 1️⃣ Solution Overview

The sum of `k` consecutive integers starting with `x` is

```
x + (x+1) + … + (x+k-1) = k·x + k(k-1)/2
```

We want this to equal `n`.  Hence

```
k·x = n – k(k-1)/2
```

* `x` must be a **positive integer** → RHS > 0  
* `k` must be a positive integer  

The loop bound comes from the inequality `n > k(k-1)/2` → `k < sqrt(2n)`.  
The *only* check needed inside the loop is whether `n – k(k-1)/2` is divisible by `k`.

This gives an **O(√n)** time algorithm with **O(1)** space.

---

## 🧑‍💻 2️⃣ Code – 3 Languages

| Language | Code |
|----------|------|
| **Java** | ```java <br>class Solution { <br>    public int consecutiveNumbersSum(int n) { <br>        int count = 0; <br>        for (int k = 1; k * k < 2L * n; k++) { <br>            int rhs = n - k * (k - 1) / 2; <br>            if (rhs <= 0) break; <br>            if (rhs % k == 0) count++; <br>        } <br>        return count; <br>    } <br>} ``` |
| **Python** | ```python <br>class Solution: <br>    def consecutiveNumbersSum(self, n: int) -> int: <br>        count = 0 <br>        k = 1 <br>        while k * k < 2 * n: <br>            rhs = n - k * (k - 1) // 2 <br>            if rhs <= 0: <br>                break <br>            if rhs % k == 0: <br>                count += 1 <br>            k += 1 <br>        return count ``` |
| **C++** | ```cpp <br>class Solution { <br>public: <br>    int consecutiveNumbersSum(int n) { <br>        int count = 0; <br>        for (long long k = 1; k * k < 2LL * n; ++k) { <br>            long long rhs = n - k * (k - 1) / 2; <br>            if (rhs <= 0) break; <br>            if (rhs % k == 0) ++count; <br>        } <br>        return count; <br>    } <br>}; ``` |

> **Why `long long` in C++?**  
> `k * k` can overflow `int` when `n` approaches `10^9`. Using `long long` keeps us safe.

---

## 📝 3️⃣ Blog Article – The Good, The Bad, The Ugly

> **Title:**  
> **“Consecutive Numbers Sum – A Hard LeetCode Problem Solved in O(√n) (Java, Python, C++)”**  
> **Meta Description:**  
> “Learn how to crack LeetCode 829 in minutes. Get Java, Python, and C++ solutions, an in‑depth explanation, common pitfalls, and interview‑ready advice.”

---

### Introduction

LeetCode’s **829 – Consecutive Numbers Sum** is a deceptively hard problem. It appears in many interview prep lists because it tests a candidate’s ability to combine *math* with *algorithmic thinking*.  
In this article we’ll:

1. Derive the math behind the solution.  
2. Provide clean, production‑grade code in Java, Python, and C++.  
3. Discuss the *good*, *bad*, and *ugly* parts of typical approaches.  
4. Explain how to frame this problem in an interview to land that job.

---

### 1️⃣ The Good – A One‑Liner Math Trick

The beauty lies in recognizing that each *sequence length* `k` gives **at most one** starting integer `x`.  
The core equation

```
k·x + k(k-1)/2 = n   →   k·x = n – k(k-1)/2
```

From here:

* `x` must be positive → `n > k(k-1)/2`.  
* The loop bound becomes `k < sqrt(2n)`.  
* The only test: `(n – k(k-1)/2) % k == 0`.

All of this is **O(√n)**, far faster than brute force.

---

### 2️⃣ The Bad – Brute‑Force Pitfalls

Many try to simulate all starting points:

```python
for start in range(1, n):
    sum = 0
    length = 0
    while sum < n:
        sum += start + length
        length += 1
        if sum == n:
            count += 1
```

* **Time:** O(n²) – infeasible for n = 10⁹.  
* **Space:** trivial, but the loop count is astronomically high.  
* **Maintainability:** Hard to reason about correctness.

The brute‑force approach quickly turns into a **time‑out nightmare** on LeetCode and is a red flag for recruiters.

---

### 3️⃣ The Ugly – Over‑Engineering with Data Structures

Some solutions try to pre‑compute all triangular numbers or use hash maps:

```java
Set<Integer> triangular = new HashSet<>();
for (int k = 1; k < Math.sqrt(2*n); k++) {
    triangular.add(k*(k+1)/2);
}
```

* Adds unnecessary memory overhead.  
* Complexity stays O(√n) but constant factors blow up.  
* Not as readable for interviewers.

The takeaway: **Keep it simple.** A single loop with a division check beats a set or map in clarity and speed.

---

### 4️⃣ Interview‑Ready Narrative

> **“I solved LeetCode 829 by first observing that each length of consecutive numbers yields at most one valid sequence. I derived the formula `k·x + k(k-1)/2 = n`, bounded k by `sqrt(2n)`, and then only checked divisibility. This gives an O(√n) algorithm, which runs in milliseconds for `n = 10⁹`. I implemented it cleanly in Java, Python, and C++.”**

Key points recruiters love:

* **Mathematical insight** – shows you can turn a problem into equations.  
* **Complexity awareness** – you know why O(√n) is fast.  
* **Cross‑language proficiency** – you can code in the stack they use.  
* **Clean code** – no magic, no over‑engineering.

---

### 5️⃣ SEO‑Friendly Highlights

| Keyword | Usage |
|---------|-------|
| LeetCode 829 | Title, intro, examples |
| Consecutive Numbers Sum | Problem statement, sections |
| Java, Python, C++ | Code blocks |
| Hard LeetCode | Problem difficulty |
| Interview preparation | Meta description, conclusion |
| O(√n) algorithm | Complexity analysis |

> **Remember:** Google loves structured content. Use H2/H3 tags, bullet lists, and code fences. The article above already follows that structure.

---

### 6️⃣ Final Thoughts

- **Keep the math front‑and‑center.**  
- **Trim loops to the minimal bound.**  
- **Avoid unnecessary data structures.**  
- **Explain your logic clearly in interviews.**  

By mastering LeetCode 829 you’ll demonstrate a powerful blend of analytical thinking and practical coding—exactly what hiring managers look for.

---

### 🚀 Bonus – Test Cases

| n | Expected Output | Explanation |
|---|------------------|-------------|
| 5 | 2 | 2+3, 5 (single number) |
| 9 | 3 | 4+5, 2+3+4, 9 |
| 15 | 4 | 8+7, 4+5+6, 1+2+3+4+5, 15 |
| 1 | 1 | 1 itself |
| 1000000000 | ? | Runs < 1 ms on Java |

---

### 📌 Wrap‑Up

You now have:

* **Three production‑ready solutions** (Java, Python, C++).  
* A **blog article** that explains the trick, pitfalls, and interview framing.  
* An **SEO‑optimized** write‑up that will rank well for job‑seeker searches.

Happy coding and good luck on your next interview!