---
title: LeetCode 2584. Split the Array to Make Coprime Products - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Problem (LeetCode 2584)

> **Split the Array to Make Coprime Products**  
>  *Hard*  

You are given a 0‑indexed integer array `nums` of length `n`.  
A split at index `i` ( `0 ≤ i ≤ n‑2` ) is **valid** if

```
product(nums[0 … i])   and   product(nums[i+1 … n-1])
```

are coprime (`gcd == 1`).  
Return the smallest such `i`, or `-1` if no split exists.

```
nums = [4, 7, 8, 15, 3, 5]   →   answer = 2
```

`1 ≤ n ≤ 10⁴`, `1 ≤ nums[i] ≤ 10⁶`.

---

## 2.  The Core Insight

Two products are coprime **iff** no prime factor appears in *both* parts.  
So a split is impossible when a prime that appears in the prefix
also appears later in the suffix.

**Idea**

* For every prime factor `p`, remember the **last index** where `p` appears.
* While scanning from left to right keep track of the **farthest** last‑index
  among all primes seen so far.  
  If the current index `i` equals this farthest index,
  no prime seen in the prefix appears later → split at `i` is valid.

This gives an **O(n log M)** algorithm (`M = 10⁶`) after a linear sieve.



---

## 3.  Algorithm (Pseudo Code)

```
build SPF[1 … 1_000_000]  // Smallest Prime Factor

// 1st pass: record last index of every prime
lastIndex[p] = -1
for i = 0 … n-1
    for each distinct prime p of nums[i] (using SPF)
        lastIndex[p] = i

// 2nd pass: sweep to find split
furthest = 0
for i = 0 … n-2
    for each distinct prime p of nums[i]
        furthest = max(furthest, lastIndex[p])
    if i == furthest            // all primes in prefix finished here
        return i
return -1
```

*Distinct primes* are obtained by repeatedly dividing by the SPF until the
current factor disappears.  
The inner loops run in `O(number_of_distinct_primes)` –  
at most ~7 for any number ≤ 10⁶.

---

## 4.  Implementation

Below are working, ready‑to‑paste solutions in **Java**, **Python**, and **C++**.

> **Tip** – For competitive programming or interview use, pre‑compute the
> sieve once and reuse it across test cases.

---

### 4.1 Java

```java
import java.util.*;

public class Solution {
    private static final int MAX = 1_000_000;
    private static final int[] spf = new int[MAX + 1];

    static {
        // linear sieve for smallest prime factor
        for (int i = 2; i <= MAX; i++) spf[i] = i;
        for (int i = 2; i * i <= MAX; i++) {
            if (spf[i] == i) {                 // i is prime
                for (int j = i * i; j <= MAX; j += i) {
                    if (spf[j] == j) spf[j] = i;
                }
            }
        }
    }

    /** return distinct prime factors of x using spf array */
    private static List<Integer> factorize(int x) {
        List<Integer> res = new ArrayList<>();
        while (x > 1) {
            int p = spf[x];
            res.add(p);
            while (x % p == 0) x /= p;
        }
        return res;
    }

    public int findValidSplit(int[] nums) {
        int n = nums.length;
        int[] lastIndex = new int[MAX + 1];
        Arrays.fill(lastIndex, -1);

        // first pass – last occurrence of each prime
        for (int i = 0; i < n; i++) {
            for (int p : factorize(nums[i])) {
                lastIndex[p] = i;
            }
        }

        // second pass – sweep
        int furthest = 0;
        for (int i = 0; i < n - 1; i++) {
            for (int p : factorize(nums[i])) {
                furthest = Math.max(furthest, lastIndex[p]);
            }
            if (i == furthest) return i;
        }
        return -1;
    }
}
```

---

### 4.2 Python

```python
import math
from typing import List

MAX = 1_000_000
spf = list(range(MAX + 1))

# linear sieve
for i in range(2, int(MAX**0.5) + 1):
    if spf[i] == i:
        for j in range(i * i, MAX + 1, i):
            if spf[j] == j:
                spf[j] = i

def factorize(x: int) -> List[int]:
    """return distinct prime factors of x using spf"""
    res = []
    while x > 1:
        p = spf[x]
        res.append(p)
        while x % p == 0:
            x //= p
    return res

def findValidSplit(nums: List[int]) -> int:
    n = len(nums)
    last = [-1] * (MAX + 1)

    # 1st pass
    for i, v in enumerate(nums):
        for p in factorize(v):
            last[p] = i

    # 2nd pass
    furthest = 0
    for i in range(n - 1):
        for p in factorize(nums[i]):
            furthest = max(furthest, last[p])
        if i == furthest:
            return i
    return -1
```

*Usage* (LeetCode signature)

```python
class Solution:
    def findValidSplit(self, nums: List[int]) -> int:
        # the function body is identical to the one above
```

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

const int MAX = 1'000'000;
int spf[MAX + 1];

void build_sieve() {
    for (int i = 2; i <= MAX; ++i) spf[i] = i;
    for (int i = 2; i * i <= MAX; ++i)
        if (spf[i] == i)
            for (int j = i * i; j <= MAX; j += i)
                if (spf[j] == j) spf[j] = i;
}

vector<int> factorize(int x) {
    vector<int> res;
    while (x > 1) {
        int p = spf[x];
        res.push_back(p);
        while (x % p == 0) x /= p;
    }
    return res;
}

class Solution {
public:
    int findValidSplit(vector<int> &nums) {
        int n = nums.size();
        static vector<int> lastIndex(MAX + 1, -1);

        // 1st pass
        for (int i = 0; i < n; ++i)
            for (int p : factorize(nums[i])) lastIndex[p] = i;

        // 2nd pass
        int furthest = 0;
        for (int i = 0; i < n - 1; ++i) {
            for (int p : factorize(nums[i]))
                furthest = max(furthest, lastIndex[p]);
            if (i == furthest) return i;
        }
        return -1;
    }
};
```

---

## 5.  The Blog Post

> **Title (SEO‑friendly)**  
> *How to Solve LeetCode 2584 – Split the Array to Make Coprime Products*  

Below is a complete markdown‑style article that you can paste into a
blogging platform, Medium, or your own website.  
It’s written for **beginners** and **interviewees** while still containing
the technical depth that algorithms enthusiasts crave.

---

### 5.1 Introduction  

> ### What is “Split the Array to Make Coprime Products”?
>  
>  LeetCode 2584 is a *Hard* problem that tests your ability to work with
>  prime factorization, prefix‑suffix sweeps, and linear sieves.  
>  In this article we walk through the **optimal O(n log log M)** solution,
>  why it works, and how to implement it in Java, Python and C++.

*Keywords:* LeetCode 2584, split array, coprime products, prime factorization, linear sieve, algorithm analysis, interview prep, coding challenge.

---

### 5.2 Problem Restatement  

> **Goal:** Find the smallest index `i` so that  
>  `product(nums[0 … i])` and `product(nums[i+1 … n-1])` are coprime.  
>  If no such `i` exists, return `-1`.

```
Input:  nums = [4, 7, 8, 15, 3, 5]
Output: 2
```

---

### 5.3 Why It Looks Hard at First Glance  

* Products can be astronomically large (`10⁶`^10⁴) – you cannot multiply them.
* Naively computing gcd for every possible split would be O(n²).
* Repeated factorization by trial division is too slow for the upper bound.

---

### 5.4 The “Good” Insight  

1. **Products are coprime ⇔ No shared prime factor** – reduces the problem
   from arithmetic to combinatorics.
2. **Last‑index trick** turns a global “does this prime appear later?” test
   into a simple linear sweep.
3. **Linear sieve** gives you the smallest prime factor for every number up
   to `10⁶` in `O(M)` time and `O(M)` memory – a one‑time cost.

> **Result:**  
> *Time:* `O(n log M)` (≈ O(n))  
> *Memory:* `O(M)` for the sieve + `O(M)` for the `lastIndex` array  
> *Practical:* Works comfortably within LeetCode limits for 10⁴ elements.

---

### 5.5 The “Bad” Side

| Bad | Why it matters |
|-----|----------------|
| **Memory footprint** – The SPF array (`1 000 001` ints) is ~4 MB. | For environments with 16 MB limit it’s still fine, but it’s noticeable. |
| **Factorization overhead** – Even with SPF, you need to factor each element twice. | Still trivial for 10⁴ elements, but can be a bottleneck if you use a slower sieve or a naive trial division. |
| **Distinct‑prime requirement** – We rely on *distinct* primes; duplicate primes are ignored, but a bug in factorization could double‑count. | Careful implementation avoids this. |

---

### 5.6 The “Ugly” Naïve Alternatives  

1. **Trial Division per element (O(n √M))** –  
   Works for tiny inputs but easily times out on `10⁶`.  
2. **Pre‑computing all prime sets via a map of primes → list of indices** –  
   Uses more memory and more passes.  
3. **Computing actual products** and calling `gcd` – overflow and `O(n²)` time.

> *These “ugly” solutions are great learning experiments, but they’re
> rarely what interviewers or LeetCode expect for a *Hard* problem.*

---

### 5.7 Step‑by‑Step Walkthrough with the Example  

```
nums = [4, 7, 8, 15, 3, 5]
indices: 0 1 2 3 4 5
```

| i | primes in nums[i] | lastIndex[p] (updated) | furthest |
|---|-------------------|------------------------|----------|
| 0 | {2}               | last[2] = 0            | 0        |
| 1 | {7}               | last[7] = 1            | 1        |
| 2 | {2}               | last[2] = 2            | 2        |
| 3 | {3,5}             | last[3] = 4, last[5] = 5 | 5      |

While sweeping, after index **2** the furthest last index is **2**,
so we can split here.  
(Primes `2` and `7` never appear again after `i=2`.)

---

### 5.8 Full Code Blocks (Ready to Paste)

| Language | File name | Key Function |
|----------|-----------|--------------|
| Java | `Solution.java` | `public int findValidSplit(int[] nums)` |
| Python | `solution.py` | `def findValidSplit(nums: List[int]) -> int:` |
| C++ | `solution.cpp` | `int findValidSplit(vector<int>& nums)` |

> All three versions share the same linear sieve + two‑pass sweep logic.

---

## 6.  Summary & Takeaways

| What worked well | What to watch out for | Where it gets messy |
|------------------|-----------------------|---------------------|
| **Linear sieve** gives you SPF in a single pass. | **Memory** – the SPF array is 4 MB; acceptable but not negligible. | Re‑factoring each number twice may look heavy if you’re not using SPF. |
| **Last‑index trick** turns a global constraint into a local one. | **Edge cases** – you must stop at `n‑2` because the split needs at least one element on each side. | Implementing distinct prime extraction incorrectly (e.g., forgetting to skip equal factors) can silently break the logic. |
| **Complexity** – `O(n log log M)` with a small constant. | **Implementation bugs** – off‑by‑one errors, forgetting to reset `furthest` after a split, etc. | The algorithm can feel unintuitive at first; a step‑by‑step diagram helps. |

> **Bottom line:**  
> If you know how to factor numbers fast (SPF) and keep track of the
> *last* position of each prime, the split problem collapses to a simple
> linear scan.  
> This is the exact reasoning behind the official “most efficient”
> LeetCode solution for 2584.

---

### 7.  Further Reading & Resources  

* [Sieve of Eratosthenes – Wikipedia](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)
* [Linear Sieve (Elias–Zaks algorithm)](https://cp-algorithms.com/algebra/prime-sieve-linear.html)
* [GCD of Prefix and Suffix – Codeforces 1542C]  
  *A related problem that uses the same “last index” idea.*

---

### 7.1 Final Call to Action  

> **Try it yourself!**  
> Write the solution in your favorite language, run it against the
> LeetCode test cases, and then push your own twist – e.g., return the
> entire list of all valid split indices.  
> Share your solution on GitHub and discuss it on LinkedIn; the
> community feedback will sharpen your reasoning further.

---

> *Happy coding, and good luck crushing LeetCode 2584!*

---

**End of article.**

--- 

**(Feel free to adapt headings, formatting, or language style to match your audience’s expectations.)**