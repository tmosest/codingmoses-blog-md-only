---
title: LeetCode 2927. Distribute Candies Among Children III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2927 – Distribute Candies Among Children III  
**Hard | LeetCode | O(1) combinatorics**

> **Problem**  
> Given two positive integers `n` and `limit`, return the number of ways to distribute `n` candies among **three** children so that no child receives more than `limit` candies.  
>  
> **Examples**  
> ```text
> n = 5, limit = 2   → 3  ( (1,2,2), (2,1,2), (2,2,1) )
> n = 3, limit = 3   → 10 (all 10 non‑negative triples summing to 3)
> ```
>  
> **Constraints**  
> 1 ≤ n, limit ≤ 10⁸  

---

### Why This Problem Is a “Good” Interview Question  

* **Pure math + programming** – No data structures, only combinatorics.  
* **Scales to large inputs** – Requires a constant‑time solution.  
* **Elegant use of inclusion‑exclusion** – A classic technique that every senior engineer should be comfortable with.  

---

### The Bad Part

* The constraints (10⁸) force you to think about integer overflow and 64‑bit arithmetic.  
* If you forget that `C(x, 2)` is zero when `x < 2`, you’ll get negative results.  

---

### The Ugly Part

* The final formula has many “offsets” (like `limit+1`) that are easy to slip up on.  
* A single typo in the combinatorial term can turn the solution from correct to wildly wrong.  

---

## Intuition – From Stars & Bars to Inclusion–Exclusion

Without an upper bound, the number of non‑negative integer triples `(a,b,c)` with `a+b+c = n` is the classic *stars & bars* result:

```
T = C(n + 2, 2)   // choose 2 separators among n+2 positions
```

The difficulty is the **upper bound** `limit`.  
We must remove all triples where at least one child receives more than `limit`.  
The most straightforward way is Inclusion–Exclusion (PIE):

```
answer = T
        – 3 * (# solutions with a > limit)
        + 3 * (# solutions with a > limit AND b > limit)
        –  (# solutions with a > limit AND b > limit AND c > limit)
```

Why 3 and why +3?  
* There are 3 symmetric children for the first subtraction.  
* There are 3 unordered pairs for the second addition.  
* Only one way to have all three exceed.

---

## Calculating Each PIE Term

Define

```
C2(x) = x*(x-1)/2   if x >= 2
        0           otherwise
```

### 1. All triples (no bound)

```
T = C2(n + 2)
```

### 2. One child exceeds `limit`

Let `a' = a - (limit+1)  ≥ 0`.  
Then `a' + b + c = n - (limit+1)`.  
Number of solutions:

```
t1 = C2( n - limit + 1 )
```

(If `n - limit + 1 < 2` the term is 0.)

### 3. Two children exceed `limit`

Let `a' = a - (limit+1)`, `b' = b - (limit+1)`.  
Then `a' + b' + c = n - 2*(limit+1)` → `n - 2*limit - 2`.  
Number of solutions:

```
t2 = C2( n - 2*limit )
```

### 4. All three children exceed `limit`

Let all be shifted: `a' + b' + c' = n - 3*(limit+1)` → `n - 3*limit - 3`.  
Number of solutions:

```
t3 = C2( n - 3*limit - 1 )
```

---

## Final Closed‑Form Formula

```
answer = C2(n + 2)
        – 3 * C2(n - limit + 1)
        + 3 * C2(n - 2*limit)
        –   C2(n - 3*limit - 1)
```

All intermediate values fit into 64‑bit signed integers because  
`n ≤ 10⁸` → the largest binomial `C(n+2,2)` ≈ 5·10¹⁵ < 2⁶³.

---

## Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Compute 4 binomials | **O(1)** | **O(1)** |

The solution is constant‑time and constant‑space – perfect for the given limits.

---

## Code Implementations

Below are ready‑to‑paste solutions in **Java**, **Python**, and **C++**.  
All use 64‑bit (`long` / `int64_t`) arithmetic.

---

### Python 3

```python
class Solution:
    def distributeCandies(self, n: int, limit: int) -> int:
        # helper: C(x, 2)  (0 if x < 2)
        def C2(x: int) -> int:
            return x * (x - 1) // 2 if x >= 2 else 0

        total   = C2(n + 2)
        t1 = C2(n - limit + 1)          # one child > limit
        t2 = C2(n - 2 * limit)          # two children > limit
        t3 = C2(n - 3 * limit - 1)      # all three > limit

        return total - 3 * t1 + 3 * t2 - t3
```

---

### Java 17

```java
class Solution {
    private long C2(long x) {
        return (x >= 2) ? (x * (x - 1) / 2) : 0;
    }

    public long distributeCandies(int n, int limit) {
        long total = C2((long) n + 2);
        long t1 = C2((long) n - limit + 1);           // one child > limit
        long t2 = C2((long) n - 2L * limit);          // two children > limit
        long t3 = C2((long) n - 3L * limit - 1);      // all three > limit

        return total - 3 * t1 + 3 * t2 - t3;
    }
}
```

---

### C++17

```cpp
class Solution {
    long long C2(long long x) {
        return (x >= 2) ? (x * (x - 1) / 2) : 0;
    }
public:
    long long distributeCandies(int n, int limit) {
        long long total = C2(static_cast<long long>(n) + 2);
        long long t1 = C2(static_cast<long long>(n) - limit + 1);          // one > limit
        long long t2 = C2(static_cast<long long>(n) - 2LL * limit);        // two > limit
        long long t3 = C2(static_cast<long long>(n) - 3LL * limit - 1);    // all > limit

        return total - 3 * t1 + 3 * t2 - t3;
    }
};
```

---

## How to Test

```text
Input: n = 5, limit = 2
Output: 3

Input: n = 3, limit = 3
Output: 10

Input: n = 1, limit = 1
Output: 3  ( (1,0,0), (0,1,0), (0,0,1) )
```

Feel free to plug these into LeetCode’s online IDE.

---

## Take‑away Tips for Your Next Coding Interview

1. **Look for combinatorial shortcuts** – `stars & bars` almost always yields a `C(n+2, 2)` formula.  
2. **Use Inclusion–Exclusion for upper bounds** – it’s a one‑liner once you write the `C2` helper.  
3. **Guard against overflow** – always cast to 64‑bit before multiplication.  
4. **Validate the formula on boundary cases** (`n` ≤ `limit`, `n` > 3*`limit`) to catch bugs.  

---

### SEO Tags & Keywords

- `distribute candies among children`
- `leetcode 2927`
- `O(1) solution combinatorics`
- `inclusion exclusion interview`
- `coding interview tips`
- `job interview algorithm`
- `c++ java python leetcode`

---

Happy coding – may this clean, O(1) solution land you the job you’re after!