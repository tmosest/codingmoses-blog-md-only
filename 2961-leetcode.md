---
title: LeetCode 2961. Double Modular Exponentiation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📘 Double Modular Exponentiation – LeetCode 2961  
### (Java | Python | C++ – Solutions + A Blog‑Style Deep‑Dive)

> **Problem ID:** 2961  
> **Difficulty:** Medium  
> **Keywords:** `modular exponentiation`, `LeetCode`, `Java`, `Python`, `C++`, `coding interview`, `algorithm design`, `O(n)`.

---

## 1️⃣ Problem Recap

You are given an array `variables`, where each element is a quadruple  
`[a, b, c, m]`.  
An index `i` is **good** if

```
((a^b % 10) ^ c) % m  ==  target
```

Return a list of all good indices (any order).

| Example | Input | Output |
|---------|-------|--------|
| 1 | `variables = [[2,3,3,10],[3,3,3,1],[6,1,1,4]]`, `target = 2` | `[0, 2]` |
| 2 | `variables = [[39,3,1000,1000]]`, `target = 17` | `[]` |

Constraints  
- `1 ≤ variables.length ≤ 100`  
- `1 ≤ a, b, c, m ≤ 10^3`  
- `0 ≤ target ≤ 10^3`

---

## 2️⃣ Why is this a “Good” Problem?

| ✅  | Explanation |
|----|-------------|
| **Simplicity + Depth** | The statement is easy to read but hides subtle issues with overflow and modular arithmetic. |
| **Common Interview Topic** | Modular exponentiation is a staple in many coding interviews (RSA, hash functions, etc.). |
| **Multiple Languages** | Demonstrates how the same logic is implemented in Java, Python, and C++. |
| **Scalable Approach** | Even though the constraints are small, the solution scales to very large exponents if needed. |

---

## 3️⃣ The “Bad” – Potential Pitfalls

| ❌  | Pitfall | How to avoid it |
|----|----------|-----------------|
| **Overflow** | `a^b` can explode (e.g., `1000^1000`). | Compute `pow(a, b, 10)` directly with modular exponentiation – never let the intermediate value grow. |
| **Modulo Order** | Mis‑ordering the `%` operations can change the result. | Follow the formula exactly: first `% 10`, then power `c`, then `% m`. |
| **Edge Case `m = 1`** | Anything `% 1` is `0`. | Treat it explicitly or rely on modular arithmetic – `pow(x, y, 1)` will always return `0`. |
| **Using Built‑In `pow` incorrectly** | In Python, `pow(a, b)` returns the exact power, not the modular result. | Use `pow(a, b, mod)` (three‑argument form) or implement fast‑pow. |

---

## 4️⃣ The “Ugly” – Why It Can Look Messy

- Repeated loops for each exponent can make the code lengthy.  
- The naive implementation can look like nested loops, obscuring the modular logic.  
- Using different data types (`int`, `long`, `BigInteger`) across languages can be confusing.

The key to a **clean** solution is *abstracting* the modular exponentiation into a helper function.

---

## 5️⃣ The Cleanest Solution – One‑Liner Core

```text
value = pow(pow(a, b, 10), c, m)
```

In Python this is literally one line.  
In Java and C++ we wrap it in a `modPow` helper.

---

## 6️⃣ Code – Three Languages

### 6.1 📜 Java

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    // Fast modular exponentiation: (base^exp) % mod
    private long modPow(long base, long exp, long mod) {
        long result = 1 % mod;
        base %= mod;
        while (exp > 0) {
            if ((exp & 1) == 1) result = (result * base) % mod;
            base = (base * base) % mod;
            exp >>= 1;
        }
        return result;
    }

    public List<Integer> getGoodIndices(int[][] variables, int target) {
        List<Integer> good = new ArrayList<>();
        for (int i = 0; i < variables.length; i++) {
            int a = variables[i][0];
            int b = variables[i][1];
            int c = variables[i][2];
            int m = variables[i][3];

            long first  = modPow(a, b, 10);   // (a^b) % 10
            long second = modPow(first, c, m); // (first^c) % m

            if (second == target) {
                good.add(i);
            }
        }
        return good;
    }
}
```

> **Why it’s fast** – `modPow` runs in *O(log exp)*, so the whole algorithm is *O(n log max(b,c))*.

---

### 6.2 📜 Python

```python
from typing import List

class Solution:
    def getGoodIndices(self, variables: List[List[int]], target: int) -> List[int]:
        good = []
        for i, (a, b, c, m) in enumerate(variables):
            first = pow(a, b, 10)   # (a^b) % 10
            second = pow(first, c, m)  # (first^c) % m
            if second == target:
                good.append(i)
        return good
```

> Python’s built‑in `pow(base, exp, mod)` is both concise and highly optimized.

---

### 6.3 📜 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    long long modPow(long long base, long long exp, long long mod) {
        long long result = 1 % mod;
        base %= mod;
        while (exp > 0) {
            if (exp & 1) result = (result * base) % mod;
            base = (base * base) % mod;
            exp >>= 1;
        }
        return result;
    }

public:
    vector<int> getGoodIndices(vector<vector<int>>& variables, int target) {
        vector<int> good;
        for (int i = 0; i < variables.size(); ++i) {
            long long a = variables[i][0];
            long long b = variables[i][1];
            long long c = variables[i][2];
            long long m = variables[i][3];

            long long first  = modPow(a, b, 10);   // (a^b) % 10
            long long second = modPow(first, c, m); // (first^c) % m

            if (second == target) good.push_back(i);
        }
        return good;
    }
};
```

> Uses the same `modPow` helper for clarity and speed.

---

## 7️⃣ Complexity Analysis

| Aspect | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n · (log b + log c))` | `O(n · (log b + log c))` | `O(n · (log b + log c))` |
| **Space** | `O(1)` auxiliary (besides result list) | `O(1)` auxiliary | `O(1)` auxiliary |
| **Worst‑case** | 100 × (10 + 10) operations | Same | Same |

Even for the maximum input size (100 rows), this runs in milliseconds.

---

## 8️⃣ SEO‑Optimized Blog Article

---

# Double Modular Exponentiation – LeetCode 2961  
### Your Complete Guide: Java, Python, C++ Solutions & Interview‑Ready Insights

> **Are you preparing for a coding interview?**  
> This article explains how to crack LeetCode’s *Double Modular Exponentiation* problem (2961) in **Java**, **Python**, and **C++**. We’ll walk through the algorithm, highlight common pitfalls, and give you production‑ready code you can drop into your portfolio or your next interview.

---

## Why This Problem Matters

- **Modular exponentiation** is one of the *most frequently asked questions* in technical interviews.  
- LeetCode 2961 tests your ability to combine **modular arithmetic** with **power operations**—skills essential for roles involving cryptography, security, or data science.  
- Solving it in multiple languages shows *language‑agnostic thinking*, a key trait interviewers look for.

---

## The Problem Statement in Plain English

Given an array of quadruples `[a, b, c, m]`, find all indices where

```
((a^b % 10) ^ c) % m  ==  target
```

returns true.

---

## Why It’s a “Good” Interview Problem

| ✅ | Benefit |
|---|---------|
| **Clear specification** | Easy to read but subtle enough to require careful thinking. |
| **Demonstrates core CS knowledge** | Modular arithmetic is foundational to cryptography and hashing. |
| **Multiple‑language solution** | Shows you can solve it in **Java**, **Python**, **C++** (and more). |
| **Scales** | The same algorithm works for astronomically large exponents if you replace the built‑in `pow` with an efficient modular exponentiation routine. |

---

## Common Mistakes (The “Bad”)

- *Overflow*: `a^b` can explode; never compute the full power.  
- *Modulo order*: Doing `% m` first or swapping `% 10` can change the answer.  
- *Edge case `m = 1`*: `% 1` is always `0`.  
- *Python misuse*: `pow(a, b)` returns the full power, not the modular result.

> Avoid these by using *fast modular exponentiation* (`pow(a, b, mod)` in Python, a helper in Java/C++).

---

## Keeping the Code Clean (The “Ugly” to “Good” Transition)

The core calculation is

```text
value = pow(pow(a, b, 10), c, m)
```

Encapsulate this into a helper (`modPow`) and the rest of the code becomes a concise `for‑loop`.  
No nested loops, no manual `while`‑loops for each exponent.

---

## Solution in Java

```java
private long modPow(long base, long exp, long mod) { … }
public List<Integer> getGoodIndices(int[][] variables, int target) { … }
```

---

## Solution in Python

```python
def getGoodIndices(self, variables: List[List[int]], target: int) -> List[int]:
    good = []
    for i, (a, b, c, m) in enumerate(variables):
        first  = pow(a, b, 10)
        second = pow(first, c, m)
        if second == target:
            good.append(i)
    return good
```

---

## Solution in C++

```cpp
long long modPow(long long base, long long exp, long long mod) { … }
vector<int> getGoodIndices(vector<vector<int>>& variables, int target) { … }
```

---

## Complexity

- **Time**: `O(n · (log b + log c))` – negligible for the given constraints.  
- **Space**: `O(1)` auxiliary.  

---

## Takeaway

- Modular exponentiation is a *must‑know* topic for interviews.  
- Implement a small helper (`modPow`) to keep the code readable across Java, Python, and C++.  
- Test edge cases (`m == 1`, `target == 0`, large exponents).  

With the code snippets above, you’re ready to submit your solution or impress hiring managers who ask: **“How would you solve Double Modular Exponentiation?”**

Good luck, and happy coding! 🚀

---

### 🔑 Keywords & Meta‑Tags

- Double Modular Exponentiation  
- LeetCode 2961  
- Coding Interview Algorithms  
- Java Modular Exponentiation  
- Python `pow` with Mod  
- C++ Fast Modular Power  
- Interview Preparation  
- Technical Interview Questions  
- Secure Coding  
- Job Interview Success  

--- 

**Happy coding and may your next interview be a “Good” one!**