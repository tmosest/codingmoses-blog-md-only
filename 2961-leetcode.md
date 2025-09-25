---
title: LeetCode 2961. Double Modular Exponentiation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2961. Double Modular Exponentiation  
**Difficulty**: Medium | **Tags**: `Math`, `Modular Arithmetic`, `Fast Exponentiation`  

> **Problem**  
> You’re given a 0‑indexed 2‑D array `variables`, where `variables[i] = [ai, bi, ci, mi]`, and an integer `target`.  
> An index `i` is *good* if  

> \[
> ((a_i^{\,b_i} \bmod 10)^{\,c_i}) \bmod m_i \;=\; \text{target}
> \]

> Return a list of all good indices (order does not matter).

| **Constraints** | |
|-----------------|----------------------------------------------------|
| `1 ≤ variables.length ≤ 100` | `1 ≤ ai, bi, ci, mi ≤ 10³` |
| `0 ≤ target ≤ 10³` | |

The goal is to solve the problem in **Java**, **Python** and **C++** (GNU‑C++17).  
After the code you’ll find a short SEO‑optimized blog article that explains the *good, the bad, and the ugly* of the approach.

---

## 1.  High‑Level Idea

1. For every sub‑array `[a, b, c, m]` compute  
   *`n1 = (a^b) % 10`*  – the first modular exponentiation.  
   *`n2 = (n1^c) % m`*  – the second modular exponentiation.
2. If `n2 == target` append the index to the answer.

The naive way would multiply `a` by itself `b` times – that is `O(b)` per test case.  
Since `b` can be as large as 1000, a faster **binary exponentiation** (also called “fast power”) is perfect.  
The same trick is used for the second exponentiation.  
With binary exponentiation each power takes `O(log exponent)` time, so the whole solution is `O(n log 1000)` – well within limits.

---

## 2.  Reference Implementations

### 2.1 Java

```java
import java.util.*;

public class Solution {
    public List<Integer> getGoodIndices(int[][] variables, int target) {
        List<Integer> ans = new ArrayList<>();
        for (int i = 0; i < variables.length; i++) {
            int a = variables[i][0];
            int b = variables[i][1];
            int c = variables[i][2];
            int m = variables[i][3];

            int n1 = modPow(a, b, 10);       // (a^b) % 10
            int n2 = modPow(n1, c, m);       // (n1^c) % m

            if (n2 == target) ans.add(i);
        }
        return ans;
    }

    // Fast exponentiation: (base^exp) % mod
    private int modPow(int base, int exp, int mod) {
        long res = 1, b = base % mod;
        while (exp > 0) {
            if ((exp & 1) == 1) res = (res * b) % mod;
            b = (b * b) % mod;
            exp >>= 1;
        }
        return (int) res;
    }
}
```

---

### 2.2 Python 3

```python
from typing import List

class Solution:
    def getGoodIndices(self, variables: List[List[int]], target: int) -> List[int]:
        ans = []
        for idx, (a, b, c, m) in enumerate(variables):
            n1 = self.mod_pow(a, b, 10)   # (a^b) % 10
            n2 = self.mod_pow(n1, c, m)   # (n1^c) % m
            if n2 == target:
                ans.append(idx)
        return ans

    @staticmethod
    def mod_pow(base: int, exp: int, mod: int) -> int:
        result = 1
        base %= mod
        while exp:
            if exp & 1:
                result = (result * base) % mod
            base = (base * base) % mod
            exp >>= 1
        return result
```

---

### 2.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> getGoodIndices(vector<vector<int>>& variables, int target) {
        vector<int> ans;
        for (int i = 0; i < (int)variables.size(); ++i) {
            int a = variables[i][0];
            int b = variables[i][1];
            int c = variables[i][2];
            int m = variables[i][3];

            int n1 = modPow(a, b, 10);   // (a^b) % 10
            int n2 = modPow(n1, c, m);   // (n1^c) % m

            if (n2 == target) ans.push_back(i);
        }
        return ans;
    }

private:
    long long modPow(long long base, long long exp, long long mod) {
        long long res = 1;
        base %= mod;
        while (exp) {
            if (exp & 1) res = (res * base) % mod;
            base = (base * base) % mod;
            exp >>= 1;
        }
        return res;
    }
};
```

---

## 3.  Blog Article – “Double Modular Exponentiation: The Good, the Bad, and the Ugly”

> **Title**: *Double Modular Exponentiation – The Good, the Bad, and the Ugly*  
> **Meta Description**: Master LeetCode 2961 in Java, Python, and C++. Learn fast power, common pitfalls, and interview‑ready tricks for double modular exponentiation.  

### 3.1 Why Does This Problem Matter in Interviews?

- **Math + Coding**: It checks if you can translate a mathematical expression into efficient code.
- **Modular Arithmetic**: Many real‑world problems (cryptography, hashing) rely on modular exponentiation.
- **Algorithmic Thinking**: The “double” step tests whether you know that you can reuse a small result for a second exponentiation.

### 3.2 The Good – What We Got Right

| Aspect | Why It’s Good |
|--------|---------------|
| **Fast Power (Binary Exponentiation)** | Runs in `O(log exp)` time, far faster than naive multiplication. |
| **Modular Reduction at Every Step** | Prevents overflow and keeps numbers small – vital when `ai`, `bi` can be 1000. |
| **Clear Separation of Concerns** | Two helper functions (`modPow`) keep the main loop readable. |
| **Language‑agnostic** | The same idea works in Java, Python, C++ – demonstrates solid algorithmic understanding. |

### 3.3 The Bad – Common Mistakes to Avoid

| Mistake | Consequence |
|---------|-------------|
| Using `int` for intermediate products in Java or C++ | Integer overflow → wrong result. |
| Computing `a^b` first, then applying `% 10` only once | Even if `a^b` fits in a 64‑bit integer, the next step can overflow again. |
| Forgetting that the modulus of the second power is *m*, not `10` | Misinterprets the formula, giving all answers wrong. |
| Not handling `m = 1` case | Division by zero or modulo by one → always 0, must be handled correctly. |

### 3.4 The Ugly – Edge Cases & Tricky Situations

1. **Zero Modulus (`m = 1`)**  
   Any number modulo 1 is 0, so `n2` will always be 0. The correct check is still `n2 == target`.  
2. **Large Exponents with Small Modulus**  
   For example, `a = 999`, `b = 1000`, `mod = 10`. Using naive multiplication would overflow even 64‑bit. Binary exponentiation avoids this by squaring modulo at each step.  
3. **Negative Numbers (not in constraints but worth noting)**  
   If the problem were extended, you’d need to normalize the base before taking modulo (`base % mod + mod) % mod`).  
4. **Performance on 100 Inputs**  
   Even though the constraints are tiny, a poorly written Python solution that uses exponentiation by multiplication could time out on an online judge that has a higher hidden limit.  

### 3.5 Interview Tips

- **Explain Binary Exponentiation** – Show the bit‑wise exponent reduction (`exp >>= 1`).  
- **Show Modulo Property** – Highlight `(x*y) % m = ((x % m) * (y % m)) % m`.  
- **Discuss Overflow** – Mention using `long long` (C++), `long` (Java) or Python’s arbitrary‑precision ints.  
- **Time Complexity** – State `O(n log 1000)` ≈ `O(n * 10)` – practically linear for `n ≤ 100`.  

### 3.6 Final Thought

LeetCode 2961 is a *mini‑quiz* on modular arithmetic combined with efficient exponentiation. By mastering it, you’re proving to interviewers that you can:

1. Translate a formula into clean, efficient code.  
2. Spot potential overflow traps.  
3. Apply classic algorithmic patterns (binary exponentiation).  

> **Ready to ace the interview?**  
> Save the three snippets above, practice explaining the algorithm out loud, and you’ll be job‑ready in no time.  

---  

> **SEO Keywords**: *Double Modular Exponentiation, LeetCode 2961, modular arithmetic, binary exponentiation, interview algorithm, Java solution, Python solution, C++ solution, job interview tips, competitive programming*  

Happy coding and good luck with your next interview!