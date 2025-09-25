---
title: LeetCode 634. Find the Derangement of An Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Overview  

**Leetcode 634 – Find the Derangement of An Array**  
> Given an integer `n`, we have the original array `[1, 2, … , n]`.  
> Return the number of *derangements* of this array, i.e. the number of
> permutations where **no element stays in its original position**.  
> Because the answer can be huge, return it modulo `10^9 + 7`.

| n | derangements |
|---|---------------|
| 1 | 0 |
| 2 | 1 |
| 3 | 2 |
| 4 | 9 |
| 5 | 44 |
| … | … |

The problem is a classic combinatorial DP that can be solved in linear time
and constant space.

---

## 2.  Core Idea  

For any `n ≥ 3`, consider the element `1` in the original array.  
When we derange the array we have two mutually exclusive cases:

| Case | What happens with element `1` | How many sub‑problems remain |
|------|-------------------------------|------------------------------|
| **A** | `1` moves to position `k` (k ≠ 1) and `k` moves to position 1 | After this forced swap we are left with a derangement of the remaining `n-2` elements. |
| **B** | `1` moves to position `k`, but `k` does **not** move to position 1 | We still need to derange the remaining `n-1` elements (because the element that was originally in position 1 can stay in its own position, but the new element at position 1 is `k` and it can’t stay). |

For a fixed `k` (`k` can be any of the `n-1` positions ≠ 1) the number of
derangements is `D(n-2)` for case A and `D(n-1)` for case B.

Hence:

```
D(n) = (n-1) * ( D(n-1) + D(n-2) )
```

Base values:

```
D(1) = 0
D(2) = 1
```

With this recurrence we can compute `D(n)` iteratively in **O(n)** time
and **O(1)** extra space.

---

## 3.  Implementation

Below are clean, production‑ready solutions in **Java, Python and C++**.  
All use 64‑bit integers to avoid overflow before applying the modulus
`MOD = 1_000_000_007`.

> **Note** – The Leetcode runtime limits are 1 s for each language; these
> implementations run comfortably below that.

---

### 3.1  Java (Java 17)

```java
import java.io.*;
import java.util.*;

public class Solution {
    private static final long MOD = 1_000_000_007L;

    public int findDerangement(int n) {
        if (n <= 2) return n - 1;          // D(1)=0, D(2)=1

        long prevPrev = 0; // D(1)
        long prev     = 1; // D(2)
        long cur      = 0;

        for (int i = 3; i <= n; i++) {
            cur = ((i - 1) * ((prev + prevPrev) % MOD)) % MOD;
            prevPrev = prev;
            prev = cur;
        }
        return (int) cur;
    }

    // Optional main for local testing
    public static void main(String[] args) {
        var s = new Solution();
        System.out.println(s.findDerangement(3)); // 2
        System.out.println(s.findDerangement(5)); // 44
    }
}
```

**Complexity**

* **Time** – `O(n)`  
* **Space** – `O(1)` (only a few variables)

---

### 3.2  Python (Python 3.11)

```python
MOD = 10 ** 9 + 7

def findDerangement(n: int) -> int:
    if n <= 2:
        return n - 1  # D(1)=0, D(2)=1

    d_prev_prev = 0  # D(1)
    d_prev     = 1  # D(2)

    for i in range(3, n + 1):
        d_curr = (i - 1) * (d_prev + d_prev_prev) % MOD
        d_prev_prev, d_prev = d_prev, d_curr

    return d_curr

# Example usage
if __name__ == "__main__":
    print(findDerangement(3))  # 2
    print(findDerangement(5))  # 44
```

**Complexity**

* **Time** – `O(n)`  
* **Space** – `O(1)`

---

### 3.3  C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findDerangement(int n) {
        const long long MOD = 1'000'000'007LL;
        if (n <= 2) return n - 1;          // D(1)=0, D(2)=1

        long long dpPrevPrev = 0; // D(1)
        long long dpPrev     = 1; // D(2)
        long long dpCurr     = 0;

        for (int i = 3; i <= n; ++i) {
            dpCurr = (1LL * (i - 1) * ((dpPrev + dpPrevPrev) % MOD)) % MOD;
            dpPrevPrev = dpPrev;
            dpPrev = dpCurr;
        }
        return static_cast<int>(dpCurr);
    }
};

int main() {
    Solution s;
    cout << s.findDerangement(3) << endl; // 2
    cout << s.findDerangement(5) << endl; // 44
    return 0;
}
```

**Complexity**

* **Time** – `O(n)`  
* **Space** – `O(1)`

---

## 4.  Blog Article – “Leetcode 634: Derangements, the Good, the Bad, the Ugly”

> **Meta description:**  
> Master Leetcode 634 – Derangements of an array. Read a deep dive into the DP solution, edge cases, and why this problem is a must‑know for coding interviews.  
> **Keywords:** Leetcode 634, find the derangement of an array, DP, derangements, interview question, O(n) solution, modulo 1e9+7, coding interview, Java, Python, C++.

---

### 4.1  The Problem in a Nutshell

You’re given a sorted array `[1, 2, … , n]`.  
A *derangement* is a permutation where **no element stays in its original place**.  
The task: count all such permutations modulo `10^9 + 7`.  
`n` can be as large as `10^6`, so you can’t afford exponential time.

---

### 4.2  The “Good” – Why This Problem is Valuable

| Reason | What you learn |
|--------|----------------|
| **Combinatorial DP** | The recurrence `D(n) = (n-1)(D(n-1)+D(n-2))` is a textbook example of dynamic programming on permutations. |
| **Space optimisation** | You can reduce O(n) memory to O(1) by reusing two variables. |
| **Modular arithmetic** | Dealing with large numbers and using `long long`/`int64` is a must for production code. |
| **Edge‑case thinking** | Recognising that `D(1)=0`, `D(2)=1` sets the stage for the recurrence. |

For interviewers, this problem tests your ability to reason about permutations, to derive recurrences, and to implement a fast, memory‑efficient solution.

---

### 4.3  The “Bad” – Common Pitfalls

1. **Recursion + Memoization** – A naive top‑down DP with recursion can hit stack limits for `n = 10^6`.  
2. **Incorrect Base Cases** – Forgetting that `D(1)=0` leads to off‑by‑one errors.  
3. **Overflow** – Multiplying `(n-1)` and a 64‑bit sum without a modulus step may overflow in languages like C++ if you use 32‑bit ints.  
4. **Modulo Mistake** – Applying `% MOD` only at the very end can produce negative numbers in some languages.  

---

### 4.4  The “Ugly” – A Quick Fix That Works But Is Hard to Read

```java
int der(int n){int a=1,b=0,c=0;
for(int i=3;i<=n;i++){c=(i-1)*(a+b)%MOD;a=b;b=c;}return n<=2?n-1:b;}
```

> **Why it’s ugly**  
> * No meaningful variable names.  
> * Mixing of logic and arithmetic makes it unreadable.  
> * Hard to maintain or extend.  

Good code is a *team asset*, not a secret weapon.

---

### 4.5  The Clean Solution – What We Just Built

* **Linear time** – one pass from 3 to `n`.  
* **Constant space** – only two 64‑bit variables.  
* **Clear intent** – `prevPrev` (`D(i-2)`), `prev` (`D(i-1)`), `curr` (`D(i)`).  
* **Robustness** – proper base cases and modular arithmetic at every step.

---

### 4.6  TL;DR – Take‑away for Job Interviews

1. **Derive the recurrence early** – look for symmetries and forced swaps.  
2. **Think in terms of “states”** – here, `D(i)` is the state.  
3. **Implement iteratively** – recursion is great for teaching, but iterative DP wins in contests.  
4. **Modular arithmetic** – always keep it in mind for combinatorial problems.  
5. **Comment your code** – interviewers read your thoughts as much as the code itself.

---

### 4.7  Bonus – What If `n` Were Even Larger?

If `n` were up to `10^9`, the linear DP would be too slow.  
You could pre‑compute factorials modulo `MOD` and use the inclusion‑exclusion formula:

```
D(n) = n! * sum_{k=0..n} (-1)^k / k!
```

But for `n ≤ 10^6` the simple recurrence is the sweet spot.

---

### 4.8  Final Thoughts

Leetcode 634 is a *mini‑saga* of dynamic programming, modular arithmetic, and careful coding.  
Master it, and you’ll be ready for any interview question that asks you to:

* Derive a recurrence,
* Translate it into an efficient algorithm, and
* Deliver clean, maintainable code.

Happy coding, and best of luck on your next interview!