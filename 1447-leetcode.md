---
title: LeetCode 1447. Simplified Fractions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  LeetCode 1447 – Simplified Fractions  
**Programming‑language‑agnostic solution** (Java / Python / C++)  
**Time Complexity** : \(O(n^2)\)  
**Space Complexity** : \(O(n^2)\) (worst‑case output size)

---

## 1.1  The Idea

For every denominator `d` from **2** to `n`  
&nbsp;&nbsp;for every numerator `num` from **1** to `d-1`  

*If* `gcd(num, d) == 1` the fraction `num/d` is **irreducible** (simplified).  
Store it as a string `"num/d"`.

The `gcd` test guarantees that we never add a reducible fraction like `2/4`.  
No additional data structures (hash‑maps, sets) are required beyond the output list.

---

## 1.2  Code

| Language | Code |
|-----------|------|
| **Java** | ```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<String> simplifiedFractions(int n) {
        List<String> result = new ArrayList<>();
        for (int denom = 2; denom <= n; denom++) {
            for (int num = 1; num < denom; num++) {
                if (gcd(num, denom) == 1) {
                    result.add(num + "/" + denom);
                }
            }
        }
        return result;
    }

    // Euclidean algorithm – O(log min(a,b))
    private int gcd(int a, int b) {
        while (b != 0) {
            int tmp = a % b;
            a = b;
            b = tmp;
        }
        return a;
    }
}
``` |
| **Python** | ```python
from typing import List

class Solution:
    def simplifiedFractions(self, n: int) -> List[str]:
        def gcd(a: int, b: int) -> int:
            while b:
                a, b = b, a % b
            return a

        res = []
        for denom in range(2, n + 1):
            for num in range(1, denom):
                if gcd(num, denom) == 1:
                    res.append(f"{num}/{denom}")
        return res
``` |
| **C++** | ```cpp
#include <vector>
#include <string>
#include <numeric>   // std::gcd in C++17

class Solution {
public:
    std::vector<std::string> simplifiedFractions(int n) {
        std::vector<std::string> res;
        for (int denom = 2; denom <= n; ++denom) {
            for (int num = 1; num < denom; ++num) {
                if (std::gcd(num, denom) == 1) {
                    res.push_back(std::to_string(num) + "/" + std::to_string(denom));
                }
            }
        }
        return res;
    }
};
``` |

---

## 1.3  Why This Works

| ✔️  | Explanation |
|-----|-------------|
| ✅ | **Correctness** – The Euclidean algorithm correctly identifies coprime pairs. |
| ✅ | **Completeness** – Every reduced fraction with denominator ≤ n is generated. |
| ✅ | **Performance** – `O(n^2)` is optimal because there are about \(\frac{n(n-1)}{2}\) possible fractions. |
| ✅ | **Simplicity** – No floating point arithmetic, no hash maps. |

---

# 2.  Blog Article  
**Title:** *Simplified Fractions (LeetCode 1447) – The Good, The Bad, and The Ugly*  

## 2.1  Introduction (SEO Meta‑Description)

> Master LeetCode 1447 “Simplified Fractions” in Java, Python, and C++. Learn the optimal GCD‑based solution, pitfalls of floating‑point tricks, and how to impress hiring managers with clean, interview‑ready code. Ideal for data‑structure & algorithm candidates.

**Keywords:** LeetCode 1447, simplified fractions, interview question, GCD algorithm, Java solution, Python solution, C++ solution, job interview coding, algorithmic thinking.

---

## 2.2  The Problem at a Glance

> Given an integer **n** (1 ≤ n ≤ 100), return *all* irreducible fractions between 0 and 1 (exclusive) whose denominator does not exceed **n**. Order is irrelevant.

Example  
`n = 4 → ["1/2","1/3","1/4","2/3","3/4"]`  
(Notice **2/4** is omitted because it simplifies to **1/2**.)

---

## 2.3  The “Good” – A Clean, GCD‑Based Solution

| Aspect | Why It’s Good |
|--------|---------------|
| **Correctness** | Uses the Euclidean algorithm to test `gcd(num, denom) == 1`. No chance of false positives. |
| **Simplicity** | Only two nested loops; no extra containers or floating‑point conversions. |
| **Performance** | `O(n²)` time, which is optimal given the output size; `O(1)` auxiliary space besides the result list. |
| **Readability** | Clear variable names (`num`, `denom`) and a small helper `gcd`. |
| **Portability** | Works identically in Java, Python, C++ – perfect for interview prep across languages. |

### Code Snippet (Java)

```java
for (int denom = 2; denom <= n; denom++) {
    for (int num = 1; num < denom; num++) {
        if (gcd(num, denom) == 1) {
            result.add(num + "/" + denom);
        }
    }
}
```

---

## 2.4  The “Bad” – Floating‑Point Hash Maps

Some solutions use a map of `float` values to detect duplicates:

```cpp
float val = static_cast<float>(j) / i;
if (!mp.count(val)) { /* add fraction */ }
```

### Problems

| Issue | Impact |
|-------|--------|
| **Precision Loss** | `0.3333333333` and `0.3333333334` may be treated as equal or distinct incorrectly. |
| **Extra Space** | Hash map adds \(O(n²)\) overhead; unnecessary. |
| **Complexity** | Same `O(n²)` loops, but the map operations add log‑time overhead. |

In practice, this approach works for `n ≤ 100` but fails on larger inputs or strict interview settings where exactness matters.

---

## 2.5  The “Ugly” – Brute‑Force without GCD

A naive approach loops over all numerator/denominator pairs and *re‑reduces* each fraction:

```python
for d in range(2, n+1):
    for num in range(1, d):
        simplified = reduce_fraction(num, d)  # expensive
        if simplified not in seen:
            seen.add(simplified)
```

### Why It’s Ugly

| Issue | Consequence |
|-------|-------------|
| **Repeated Work** | Each fraction is reduced even if it’s already known. |
| **Higher Complexity** | Reduction can be `O(log min(a,b))`; repeated for every pair leads to a noticeable slowdown. |
| **Less Readable** | Extra helper functions, extra data structures, harder to audit during an interview. |

---

## 2.6  Quick Checklist for Interviewers

| ✔️ | Checklist Item |
|----|-----------------|
| ✅ | Use Euclidean GCD (built‑in or custom). |
| ✅ | Two nested loops; denominators from 2 to `n`. |
| ✅ | No floating‑point math. |
| ✅ | Return `List<String>` (Java), `vector<string>` (C++), `List[str]` (Python). |
| ✅ | Keep code compact (≤ 20 lines). |
| ✅ | Add comments for clarity. |

---

## 2.7  Final Thoughts

- The GCD solution is the **gold standard** for LeetCode 1447.  
- Avoid floating‑point tricks unless explicitly allowed.  
- Keep your code *readable*, *correct*, and *efficient*—the interviewers care about *how* you solve, not just *what* you solve.

Good luck landing that coding interview! 🚀  

--- 

**References**  
- LeetCode Problem 1447: <https://leetcode.com/problems/simplified-fractions/>  
- Euclidean Algorithm Wikipedia: <https://en.wikipedia.org/wiki/Euclidean_algorithm>  

Feel free to adapt the snippets for your own interview prep or open‑source contributions.