---
title: LeetCode 2549. Count Distinct Numbers on Board - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 2549. Count Distinct Numbers on Board – One‑liner, One‑Solution (Java / Python / C++)

> **LeetCode #2549** – *Easy* – “Count Distinct Numbers on Board”

> **Time Complexity** – **O(1)**  
> **Space Complexity** – **O(1)**

---

### Problem Recap

You start with a single number `n` on a board.  
Every day you examine **every** number `x` that is already on the board and add all numbers `i` (`1 ≤ i ≤ n`) that satisfy `x % i == 1`.  
After an astronomical 1 000 000 000 days you are asked: *how many distinct numbers are on the board?*

> **Key observation** – After the very first step you can only ever reach numbers `1 … n`.  
> In fact, every number `k` (`2 ≤ k ≤ n`) will eventually be added, while `1` can never be added because `x % 1 == 0` for every `x`.  
> Therefore the board will contain all numbers from `2` to `n`, i.e. `n‑1` numbers (and if `n == 1`, only the number `1` remains).

So the answer is

```text
if n == 1 → 1
else       → n - 1
```

No simulation is required.

---

## 1️⃣ Java

```java
class Solution {
    public int distinctIntegers(int n) {
        return n == 1 ? 1 : n - 1;
    }
}
```

### Why this works

- `n == 1` → only the starting number remains.  
- For any `n > 1`, every integer `2 … n` becomes reachable, giving `n-1` distinct values.

---

## 2️⃣ Python

```python
class Solution:
    def distinctIntegers(self, n: int) -> int:
        return 1 if n == 1 else n - 1
```

---

## 3️⃣ C++

```cpp
class Solution {
public:
    int distinctIntegers(int n) {
        return (n == 1) ? 1 : n - 1;
    }
};
```

---

## 📚 Blog Article – “How to Nail LeetCode 2549 in 3 Minutes (Java, Python, C++)”

### Title
**“Cracking LeetCode 2549 – Count Distinct Numbers on Board: One‑Liner Java/Python/C++”**

### Meta Description
Learn the O(1) solution to LeetCode 2549 “Count Distinct Numbers on Board.” Quick Java, Python, and C++ code + interview insights.

---

### Introduction

If you’re prepping for software‑engineering interviews, LeetCode’s *Count Distinct Numbers on Board* (Problem 2549) is a classic “easy” trick question that tests your ability to spot patterns.  
Below you’ll find a concise explanation, why the naive simulation fails, and the perfect one‑liner in Java, Python, and C++ that you can copy‑paste during a coding interview.

> **Keywords**: LeetCode 2549, Count Distinct Numbers on Board, interview prep, coding interview, Java solution, Python solution, C++ solution, algorithm interview, O(1) solution, coding challenge

---

### The Good

| ✅ | What’s great about this problem? |
|----|---------------------------------|
| **Simple logic** | It reduces to a single mathematical insight: “only numbers 2‑n are ever reachable.” |
| **Deterministic answer** | No randomization, no loops, no data structures – just an `if`. |
| **Fast** | `O(1)` time and space – perfect for interview “quick win.” |

---

### The Bad

| ⚠️ | Why the naive approach is a pitfall |
|----|-------------------------------------|
| **Simulation waste** | People often write nested loops that run up to 10⁹ days – that’s a *gigantic* time complexity and obviously impossible. |
| **Misunderstanding modulo** | Forgetting that `x % 1 == 0` for all `x`, so `1` can never be added. |
| **Boundary error** | Forgetting the `n == 1` case; many solutions incorrectly output `0`. |

---

### The Ugly

| 😈 | Things that can trip you up |
|----|----------------------------|
| **Off‑by‑one** | Mis‑counting the reachable numbers as `n` instead of `n‑1`. |
| **Infinite loops** | Attempting to iterate until no new numbers appear, but forgetting that the process stops after at most `n` unique values. |
| **Assuming all numbers are reachable** | Some will think every `i` is added immediately, but the modulo condition filters out many. |

---

### Step‑by‑Step Insight

1. **Start with `n`** – the only number present.  
2. **Look for `i` where `n % i == 1`.**  
   - For `n = 5`, `i = 2` and `4` satisfy this.  
3. **Add those numbers** – board now holds `{2, 4, 5}`.  
4. **Next day** – examine each of these; e.g. `4 % 3 == 1` → add `3`.  
5. **Notice the pattern** – you will eventually add *every* integer from `2` up to `n`.  
6. **The number `1` is never added** because `x % 1 == 0`.  
7. **After 1 000 000 000 days** – you have exactly `n‑1` distinct numbers if `n > 1`; otherwise just `1`.

---

### The One‑Liner

```java
// Java
public int distinctIntegers(int n) { return n == 1 ? 1 : n - 1; }
```

```python
# Python
def distinctIntegers(n: int) -> int: return 1 if n == 1 else n - 1
```

```cpp
// C++
int distinctIntegers(int n) { return (n == 1) ? 1 : n - 1; }
```

---

### Final Takeaway

*The trick is to look at the modulo condition and the fixed range `1 … n`. Once you realize that every number `k ≥ 2` will eventually be added and `1` never will, the answer collapses to `n‑1` (or `1` when `n == 1`).*

Write this one‑liner, explain the reasoning in a couple of sentences, and you’ll impress interviewers with both speed and clarity.

---

### SEO Checklist

- **Title & Meta** contain target keywords (`LeetCode 2549`, `Count Distinct Numbers on Board`, `Java solution`, `Python solution`, `C++ solution`).
- **Headings** use the keywords naturally (`Java solution`, `Python solution`, `C++ solution`).
- **Bullet points** highlight the good, bad, and ugly – keeps readers engaged.
- **Code snippets** are short and in three languages, catering to a wide audience.
- **Word count** ~800 words – enough for depth but concise enough to rank for quick answer searches.

---

### TL;DR

```text
Answer = (n == 1) ? 1 : n - 1
```

Run it in **Java, Python, or C++** and you’ll have an interview‑ready solution in seconds. Happy coding! 🚀