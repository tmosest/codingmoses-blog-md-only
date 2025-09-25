---
title: LeetCode 3184. Count Pairs That Form a Complete Day I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 3184 – Count Pairs That Form a Complete Day I  
> **“The Good, The Bad & The Ugly – A Job‑Seeking Guide”**  

---

### 🚀 1. Problem Summary

You’re given an integer array `hours` (1 ≤ `hours.length` ≤ 100, 1 ≤ `hours[i]` ≤ 10⁹).  
Return the number of index pairs `(i, j)` with `i < j` such that  
`hours[i] + hours[j]` is a *multiple of 24* (a “complete day”).

---

### 🏎️ 2. High‑Level Strategy

The key observation:  
Only the remainder of each hour when divided by 24 matters.

| hour | remainder `h % 24` |
|------|--------------------|
| 12   | 12                 |
| 30   | 6                  |
| 24   | 0                  |

Two numbers form a complete day **iff** their remainders sum to 24 (or 0).  
So we just need to:

1. Count how many times each remainder appears (`cnt[0…23]`).
2. For each remainder `r`:
   - If `r == 0`, add `C(cnt[0], 2)` pairs.
   - If `r == 12` (the only other remainder that pairs with itself), add `C(cnt[12], 2)` pairs.
   - Otherwise, add `cnt[r] * cnt[24 - r]` pairs (take care not to double‑count).

Time complexity: **O(n)**, memory: **O(1)**.

---

### 📚 3. The Good, The Bad & The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Clarity** | Simple mod‑24 logic, easy to read | The double‑loop brute‑force solution (O(n²)) is straightforward but slow | Using a hash map for every test case can be overkill if the array is tiny |
| **Performance** | Linear time, works for the upper bound (100) instantly | Brute force can reach 5 000 operations, still fine but unnecessary | An implementation that forgets to use `long` for the answer could overflow when `n=100` |
| **Maintainability** | One small array of size 24 keeps the code compact | None | Mixing data types (`int` vs `long`) and failing to guard against negative remainders can lead to subtle bugs |
| **Interview‑friendly** | Demonstrates awareness of modular arithmetic and counting pairs | Brute force shows problem comprehension but may be penalized for sub‑optimality | Over‑engineering (e.g., a fancy class hierarchy) can distract from the core solution |

---

### 💻 4. Code Solutions

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.

---

#### Java (Java 17)

```java
// 3184. Count Pairs That Form a Complete Day I
public class Solution {
    public long countCompleteDayPairs(int[] hours) {
        // Count remainders modulo 24
        long[] cnt = new long[24];
        for (int h : hours) {
            cnt[h % 24]++;
        }

        long pairs = 0;
        // Remainder 0 pairs with itself
        pairs += cnt[0] * (cnt[0] - 1) / 2;
        // Remainder 12 pairs with itself
        pairs += cnt[12] * (cnt[12] - 1) / 2;

        // Remaining remainders pair r with 24 - r (r < 12)
        for (int r = 1; r < 12; r++) {
            pairs += cnt[r] * cnt[24 - r];
        }
        return pairs;
    }
}
```

---

#### Python (Python 3.10+)

```python
# 3184. Count Pairs That Form a Complete Day I
def countCompleteDayPairs(hours: list[int]) -> int:
    from collections import Counter

    # Remainder counts
    cnt = Counter(h % 24 for h in hours)

    pairs = 0
    pairs += cnt[0] * (cnt[0] - 1) // 2          # 0 with 0
    pairs += cnt[12] * (cnt[12] - 1) // 2        # 12 with 12

    for r in range(1, 12):                       # 1..11
        pairs += cnt[r] * cnt[24 - r]

    return pairs
```

---

#### C++ (C++17)

```cpp
// 3184. Count Pairs That Form a Complete Day I
class Solution {
public:
    long long countCompleteDayPairs(vector<int>& hours) {
        long long cnt[24] = {0};
        for (int h : hours) cnt[h % 24]++;

        long long pairs = 0;
        pairs += cnt[0] * (cnt[0] - 1) / 2;          // 0 with 0
        pairs += cnt[12] * (cnt[12] - 1) / 2;        // 12 with 12

        for (int r = 1; r < 12; ++r)                 // 1..11
            pairs += cnt[r] * cnt[24 - r];

        return pairs;
    }
};
```

---

### 📈 5. Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| **Optimal** (mod‑24 counting) | **O(n)** | **O(1)** (24 counters) |
| **Brute‑Force** | **O(n²)** | **O(1)** |

The constraints (`n ≤ 100`) allow either approach, but the optimal one is the standard interview choice.

---

### 🔑 6. SEO‑Optimized Blog Article

---

# Mastering LeetCode 3184: Count Pairs That Form a Complete Day I – The Good, the Bad, and the Ugly

**Meta Description** – Learn how to crack LeetCode 3184 with clean Java, Python, and C++ solutions. Understand the algorithm, complexity, and interview pitfalls. Get coding interview ready and boost your job prospects!

**Keywords** – LeetCode 3184, Count Pairs Complete Day, Java coding interview, Python algorithm, C++ interview questions, job interview coding, algorithm explanation, modular arithmetic, pair counting problem

---

## Introduction

If you’re gearing up for a software engineering interview, mastering LeetCode problems is a must‑do. One such problem, **3184. Count Pairs That Form a Complete Day I**, tests your ability to think about *modular arithmetic* and *efficient counting*.  

In this article, we’ll dissect the problem, walk through a clean solution in **Java, Python, and C++**, and discuss the *good, the bad, and the ugly* of common approaches. We’ll also highlight interview‑friendly strategies to impress recruiters.

> **Why this matters:**  
> - It showcases your *algorithmic thinking*.  
> - It proves you can write **concise, bug‑free code**.  
> - It demonstrates awareness of **time/space trade‑offs**—key interview criteria.

---

## Problem Statement (Restated)

Given an array `hours` (size ≤ 100, values up to 10⁹), count the number of index pairs `(i, j)` (`i < j`) such that the sum of the two values is a multiple of 24.

---

## The Good: Elegant Modulo Counting

The *core insight* is that only the remainder when each hour is divided by 24 matters.

- **Why**?  
  `a + b` is a multiple of 24  
  ⟺ `(a % 24) + (b % 24)` is 0 or 24.  
  ⟺ The remainders are complementary: `r` and `24 - r`.

So we:
1. Count how many times each remainder appears (`cnt[0…23]`).
2. Compute pair counts using combinations for `r = 0` and `r = 12`, and product counts for `1 ≤ r ≤ 11`.

The algorithm runs in linear time, uses constant space, and is trivial to implement.

---

## The Bad: Brute Force Quadratic Time

A common beginner approach loops over all pairs:

```python
for i in range(n):
    for j in range(i+1, n):
        if (hours[i] + hours[j]) % 24 == 0:
            ans += 1
```

While this works for `n ≤ 100`, it is **O(n²)**. In larger interview settings, such a solution might be penalized for not being optimal.

---

## The Ugly: Over‑engineering & Edge‑Case Pitfalls

- **Using HashMaps** for every call adds unnecessary overhead.
- **Neglecting long/64‑bit arithmetic** can cause overflow for large inputs.
- **Mis‑handling negative remainders** (not an issue here, but a common bug in modulo problems).

Aim for a **simple, clear implementation** that covers the constraints.

---

## Solution Walk‑through (Java)

```java
public class Solution {
    public long countCompleteDayPairs(int[] hours) {
        long[] cnt = new long[24];
        for (int h : hours) cnt[h % 24]++;

        long pairs = 0;
        pairs += cnt[0] * (cnt[0] - 1) / 2;          // 0 + 0
        pairs += cnt[12] * (cnt[12] - 1) / 2;        // 12 + 12

        for (int r = 1; r < 12; r++)                // 1..11
            pairs += cnt[r] * cnt[24 - r];

        return pairs;
    }
}
```

- **Why `long`?**  
  The maximum answer for `n = 100` is `C(100, 2) = 4950`, which fits in `int`. However, using `long` protects against future scale‑ups and keeps the code safe.

---

## Python Implementation

```python
def countCompleteDayPairs(hours: list[int]) -> int:
    from collections import Counter
    cnt = Counter(h % 24 for h in hours)
    pairs = 0
    pairs += cnt[0] * (cnt[0] - 1) // 2
    pairs += cnt[12] * (cnt[12] - 1) // 2
    for r in range(1, 12):
        pairs += cnt[r] * cnt[24 - r]
    return pairs
```

Python’s `Counter` is a natural fit for frequency counting, and integer overflow is handled automatically.

---

## C++17 Implementation

```cpp
class Solution {
public:
    long long countCompleteDayPairs(vector<int>& hours) {
        long long cnt[24] = {0};
        for (int h : hours) cnt[h % 24]++;

        long long pairs = 0;
        pairs += cnt[0] * (cnt[0] - 1) / 2;
        pairs += cnt[12] * (cnt[12] - 1) / 2;

        for (int r = 1; r < 12; ++r)
            pairs += cnt[r] * cnt[24 - r];

        return pairs;
    }
};
```

The array of size 24 keeps memory usage trivial. Using `long long` safeguards against overflow.

---

## Complexity Recap

| Approach | Time | Space |
|----------|------|-------|
| **Optimal (Modulo Counting)** | **O(n)** | **O(1)** |
| **Brute‑Force** | **O(n²)** | **O(1)** |

Given the constraints, the optimal solution is both **efficient** and **clean**.

---

## Interview‑Ready Tips

| Tip | Why It Helps |
|-----|--------------|
| **Explain the modulo logic** | Shows conceptual depth. |
| **Show a simple counter array** | Keeps the code readable. |
| **Use `long`/`long long`** | Demonstrates defensive coding. |
| **Mention edge cases** (`0`, `12`, duplicates) | Reveals thoroughness. |
| **Compare to brute force** | Gives a benchmark and shows you know the trade‑offs. |

---

## Final Thought

LeetCode 3184 is a *micro‑problem* that can showcase a lot about your coding style:

- **Simplicity** → Keep the logic focused on remainders.  
- **Performance** → Use a single pass and constant extra space.  
- **Robustness** → Guard against overflow and special remainders.

By mastering this problem—and being able to articulate the *good*, *bad*, and *ugly* aspects—you’ll demonstrate to hiring managers that you can write efficient, maintainable code that scales. Happy coding, and good luck landing that interview!  

---  

### 🎯 Call to Action

- **Try** the code in your favorite IDE.  
- **Run** the provided test cases.  
- **Push** the solutions to GitHub with a clear README.  
- **Share** your results on LinkedIn—show recruiters you’re ready for the next role!