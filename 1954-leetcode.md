---
title: LeetCode 1954. Minimum Garden Perimeter to Collect Enough Apples - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem Recap – “Minimum Garden Perimeter to Collect Enough Apples”

In an infinite 2‑D grid every integer lattice point **(i, j)** hosts a tree that bears  
`|i| + |j|` apples.  
You own a square plot that is *axis‑aligned* and centered on the origin.  
Your task:  

> **Given `neededApples` (including apples that lie on the plot’s border),  
> return the smallest possible perimeter of a plot that contains at least that many apples.**

The perimeter is always a multiple of **8** – a fact that turns the whole problem into a neat binary‑search puzzle.



--------------------------------------------------------------------

## 🧠 Why This Problem Is a Great “Coding‑Interview” Exercise

| Category | Why it matters |
|----------|----------------|
| **Math + Symmetry** | Shows you can reduce a 2‑D summation to a closed form. |
| **Binary Search** | A classic interview tool – reduces a large search space to ~log‑time. |
| **Big‑Int Handling** | `neededApples` can be up to 10^15, so you must use 64‑bit arithmetic everywhere. |
| **Code‑Reusability** | The same closed‑form works in Java, Python, and C++ – perfect for a “copy‑paste‑and‑run” portfolio. |

If you can walk through this problem and explain the *“good, bad, ugly”* trade‑offs, you’ll shine in any algorithm interview.



--------------------------------------------------------------------

## 📦 Solution Overview

| Approach | Complexity | When to use |
|----------|------------|-------------|
| **Brute‑Force (O(n³))** | Too slow – will never finish for the max input. | **Never** – just for illustration. |
| **Direct Summation (O(√n))** | Still too slow for 10¹⁵ apples. | Not recommended. |
| **Closed‑Form + Binary Search** | **O(log n)** time, **O(1)** space. | **Preferred** – fast, simple, and safe for 64‑bit arithmetic. |
| **O(1) via Cardano’s Formula** | In theory constant‑time, but involves floating‑point and is over‑kill. | Only for academic curiosity. |

We’ll implement the **closed‑form + binary search** solution in all three languages.



--------------------------------------------------------------------

## ✅ Code Implementations

> **Note** – In every language the variable **`n`** represents the distance from the centre to one side of the square (half of the side length).  
> Perimeter = `8 * n`.  
> Apples inside the square (including its border) are given by the exact closed form:

```
apples(n) = 2 * n * (n + 1) * (1 + 2 * n)
```

---

### 1. Java (Java 17)

```java
// 1954. Minimum Garden Perimeter to Collect Enough Apples
public class Solution {
    /*  Returns total apples inside a square whose half‑side is n
        (i.e. side length = 2*n)  */
    private long totalApples(long n) {
        return 2L * n * (n + 1) * (1 + 2 * n);
    }

    public long minimumPerimeter(long neededApples) {
        // n is at most 100_000 because 2 * n^3 < 10^15  (proved in editorial)
        long lo = 1, hi = 100_000;
        while (lo < hi) {
            long mid = lo + (hi - lo) / 2;
            if (totalApples(mid) < neededApples) {
                lo = mid + 1;            // need a bigger plot
            } else {
                hi = mid;                // mid is already big enough
            }
        }
        return 8 * lo;   // perimeter = 8 * n
    }
}
```

Compile & run (JUnit test or a `main` method):

```bash
javac Solution.java
java Solution
```

---

### 2. Python (Python 3.10+)

```python
# 1954. Minimum Garden Perimeter to Collect Enough Apples
class Solution:
    @staticmethod
    def _apples(n: int) -> int:
        """Closed‑form number of apples inside a square of half‑side n."""
        return 2 * n * (n + 1) * (1 + 2 * n)

    def minimumPerimeter(self, neededApples: int) -> int:
        lo, hi = 1, 100_000          # 1 <= n <= 10^5  (see editorial)
        while lo < hi:
            mid = (lo + hi) // 2
            if self._apples(mid) < neededApples:
                lo = mid + 1
            else:
                hi = mid
        return 8 * hi                 # perimeter = 8 * n
```

Run it with:

```bash
python -c "import sys;print(Solution().minimumPerimeter(int(sys.argv[1])))" 1000
```

---

### 3. C++17

```cpp
// 1954. Minimum Garden Perimeter to Collect Enough Apples
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // Closed‑form apples inside a square with half‑side n
    static long long totalApples(long long n) {
        return 2LL * n * (n + 1) * (1 + 2 * n);
    }

    long long minimumPerimeter(long long neededApples) {
        long long lo = 1, hi = 100000;          // 10^5 upper bound
        while (lo < hi) {
            long long mid = (lo + hi) / 2;
            if (totalApples(mid) < neededApples)
                lo = mid + 1;                    // too few apples
            else
                hi = mid;                        // enough apples, try smaller
        }
        return 8 * hi;                           // perimeter = 8 * n
    }
};
```

Compile with `g++ -std=c++17 -O2 -Wall -o solution solution.cpp` and test with:

```bash
./solution <<EOF
1
EOF
```

---

## 📚 Blog Article – “The Good, The Bad & The Ugly of the Minimum Garden Perimeter Problem”

> **Keywords:** Minimum Garden Perimeter, LeetCode 1954, algorithm interview, binary search, closed‑form, job interview coding, C++, Java, Python

---

### 1️⃣ Introduction – Why This Problem Matters

Interviewers love problems that blend **grid math** with **algorithmic optimisation**.  
LeetCode 1954 forces you to:

* Think about lattice points and **sum of Manhattan distances**.
* Spot a **symmetry** that collapses a 4‑fold 2‑D problem into a neat 1‑D formula.
* Apply a **binary search** over a very small search space (≤ 10⁵) despite the input reaching 10¹⁵.

If you can explain this in a one‑hour mock interview, you’re already a step ahead of many candidates.



### 2️⃣ The Three Approaches – Good, Bad & Ugly

| Approach | Complexity | Pros | Cons |
|----------|------------|------|------|
| **Direct Summation** (O(n²)) | **Very slow** – `n` can be 10⁵, so 10¹⁰ iterations. | Simple to code; no arithmetic pitfalls. | Impossible on LeetCode’s 2‑second limit. |
| **Loop‑Accumulation** (O(√n)) | **O(n^(1/3))** | Eliminates the inner sum; still faster than direct but *not* ideal. | Requires an “off‑by‑one” mental model; still > 1 ms for the largest input. |
| **Binary Search + Closed‑Form** (O(log n)) | **Fastest** – 17 iterations for 10¹⁵. | O(1) space, O(log n) time, no floating‑point errors. | Needs careful handling of overflow (use 64‑bit). |

> **Good** – Binary search with the derived closed‑form.  
> **Bad** – Naïve brute‑force or plain summation.  
> **Ugly** – Over‑engineering with Cardano’s formula or floating‑point approximations – perfectly fine in theory but overkill in practice.



### 3️⃣ Deriving the Closed‑Form

1. **Half‑Side `n`** – The square extends from `-n` to `+n` on both axes.  
2. **Apples at `(i, j)`** – `|i| + |j|`.  
3. **Total apples** inside the square:

   ```
   apples(n) = Σ_{i=-n}^{n} Σ_{j=-n}^{n} (|i| + |j|)
             = 2 * n * (n + 1) * (1 + 2 * n)      (proved by symmetry)
   ```

4. **Perimeter** of the square: `side = 2*n` → `perimeter = 8*n`.

Thus the problem reduces to **find the smallest `n` such that**  
`2 * n * (n + 1) * (1 + 2*n) ≥ neededApples`.



### 4️⃣ Why Binary Search Works

For `neededApples ≤ 10¹⁵`:

```
apples(10⁵) = 2 * 10⁵ * 10⁵ * 2*10⁵ ≈ 4 * 10¹⁵   > 10¹⁵
```

So `n` never exceeds 10⁵.  
The function `apples(n)` is strictly increasing, so we can safely bisect.



### 5️⃣ Handling 64‑bit Overflow

All intermediate multiplications must stay within `int64_t`:

```
2LL * n * (n + 1) * (1 + 2 * n)
```

Using **`long long` in C++**, **`long` in Java**, and **`int` with 64‑bit** in Python (`int` is unbounded) guarantees no overflow.



### 5️⃣ Final Thoughts – Communicating the Idea

During an interview, you can:

1. Sketch the **lattice** and point out the symmetry.
2. Write down the **closed‑form** on a whiteboard.
3. Explain that `n ≤ 100 000` and that a binary search will finish instantly.
4. Share the code snippets (Java / Python / C++).

> **Result:** You demonstrate **mathematical intuition**, **algorithmic skill**, and **language versatility** – exactly what recruiters look for.



--------------------------------------------------------------------

## 🎯 Take‑away Checklist

* ✅ Write the closed‑form **once** – reuse it in any language.
* ✅ Use **64‑bit arithmetic** (Java `long`, Python `int`, C++ `long long`).
* ✅ Apply **binary search** over `[1, 100000]`.
* ✅ Test your solution with edge cases (`1`, `1000`, `10¹⁵`).

When you’re explaining the problem in an interview, remember: *the “good” solution is the one that solves the largest input in 2 seconds and requires only a handful of integer multiplications.*



--------------------------------------------------------------------

## 🎯 Final Words – How This Boosts Your Interview Score

* **Clear Math** – Symmetry & closed form show deep understanding.  
* **Algorithmic Rigor** – Binary search proves you know how to reduce complexity.  
* **Language‑agnostic Code** – Portfolio‑ready snippets for Java, Python, and C++.  

Use this problem as a talking‑point. You’ll not only impress your interviewer but also give yourself a reusable code template for any future coding challenge. Happy coding! 🚀



--------------------------------------------------------------------

> **Follow me on LinkedIn** for more algorithm‑interview prep, coding tutorials, and job‑search hacks.  

*Happy Interviewing!*



--------------------------------------------------------------------

**End of article**



--------------------------------------------------------------------

> **Meta‑note** – If you liked this solution, drop a comment below or share it with your friends. The more we solve, the better we get. Happy hacking! 🚀



--------------------------------------------------------------------

---


*(Prepared for an audience of recruiters, hiring managers and fellow interview‑ready coders.)*