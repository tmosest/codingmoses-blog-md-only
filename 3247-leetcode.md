---
title: LeetCode 3247. Number of Subsequences with Odd Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3247. **Number of Subsequences with Odd Sum**  
### Problem (LeetCode 3247) – Medium

> **Given** an integer array `nums`, return the number of subsequences whose sum is odd.  
> The answer can be very large, so return it modulo `1 000 000 007`.  

> **Constraints**  
> * `1 ≤ nums.length ≤ 10⁵`  
> * `1 ≤ nums[i] ≤ 10⁹`  

> **Example**  
> ```text
> Input : nums = [1,1,1]
> Output: 4
> ```
> The odd‑sum subsequences are:  
> `[1]`, `[1]`, `[1]`, `[1,1,1]` (each `[1]` appears 3 times because we can pick any of the three ones).

---

## The “Good, the Bad, and the Ugly”

|  | What’s nice | What’s painful | What’s ugly |
|---|-------------|----------------|-------------|
| **Good** | Only two parity states (odd / even). | Linear time, constant memory. | None! |
| **Bad** | Must handle modulo arithmetic carefully. | Very large counts may overflow 64‑bit if not modulo‑ded. | Recursion depth can explode for `n = 10⁵`. |
| **Ugly** | The naïve DP that remembers all intermediate sums is impossible (2ⁿ states). | A recursive solution with memoization can blow the call stack. | Forgetting to add the empty subsequence when needed can lead to off‑by‑one errors. |

---

## Intuition

When you read an element `x` in the array:

1. **If `x` is even**  
   - Adding `x` to any subsequence does *not* change its parity.  
   - So the number of odd subsequences stays the same.  
   - The number of even subsequences increases by 1 (the subsequence `[x]` itself).

2. **If `x` is odd**  
   - Adding `x` flips the parity of every subsequence it joins.  
   - Every *even* subsequence becomes *odd*.  
   - The odd subsequences remain odd if you *don’t* pick `x`.  
   - Additionally, the single‑element subsequence `[x]` is odd.

Thus we only need to keep two counters:

|  | Current odd count (`odd`) | Current even count (`even`) |
|---|---------------------------|----------------------------|
| **Start** | `0` | `0` |
| **After processing `x`** |  
| `x` odd | `odd' = even + 1` | `even' = odd` |  
| `x` even | `odd' = odd` | `even' = even + 1` |

All operations are done modulo `M = 1 000 000 007`.

---

## Solution Code

Below are three complete, idiomatic implementations: **Java**, **Python**, and **C++**.  
Each runs in `O(n)` time, `O(1)` extra memory, and safely handles the modulo.

### 1. Java (Top‑Down DP → O(n) Iterative)

```java
import java.util.*;

public class Solution {
    private static final long MOD = 1_000_000_007L;

    public int subsequenceCount(int[] nums) {
        long odd = 0, even = 0;   // long to avoid overflow before modulo

        for (int val : nums) {
            if ((val & 1) == 1) {          // odd number
                long newOdd = (even + 1) % MOD; // even subsequences become odd + [val]
                long newEven = odd % MOD;       // odd subsequences stay odd if we skip val
                odd = newOdd;
                even = newEven;
            } else {                       // even number
                long newOdd = odd % MOD;        // parity unchanged
                long newEven = (even + 1) % MOD; // new even subsequence [val]
                odd = newOdd;
                even = newEven;
            }
        }
        return (int) (odd % MOD);
    }
}
```

> **Why iterative?**  
> The original problem statement talks about “top‑down DP”. However, a recursive
> solution with memoization would require `O(n)` stack frames and
> `O(n)` memory for the memo table—unnecessary overhead for a problem that
> is essentially a two‑state counter.

### 2. Python (Simple Iterative DP)

```python
MOD = 1_000_000_007

class Solution:
    def subsequenceCount(self, nums: list[int]) -> int:
        odd, even = 0, 0

        for val in nums:
            if val & 1:                     # odd
                new_odd = (even + 1) % MOD
                new_even = odd % MOD
                odd, even = new_odd, new_even
            else:                           # even
                new_odd = odd % MOD
                new_even = (even + 1) % MOD
                odd, even = new_odd, new_even

        return odd % MOD
```

> **Python’s readability** makes the two‑state logic extremely clear.

### 3. C++ (Iterative DP, 64‑bit integer)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int subsequenceCount(vector<int>& nums) {
        const long long MOD = 1'000'000'007LL;
        long long odd = 0, even = 0;

        for (int val : nums) {
            if (val & 1) {   // odd
                long long newOdd  = (even + 1) % MOD;
                long long newEven = odd % MOD;
                odd = newOdd;
                even = newEven;
            } else {        // even
                long long newOdd  = odd % MOD;
                long long newEven = (even + 1) % MOD;
                odd = newOdd;
                even = newEven;
            }
        }
        return static_cast<int>(odd % MOD);
    }
};
```

> **C++** leverages `long long` to keep the intermediate values safe before applying the modulo.

---

## Complexity Analysis

| Approach | Time | Space | Modulo Operations |
|----------|------|-------|-------------------|
| Iterative (Java/Python/C++) | **O(n)** | **O(1)** | O(1) per iteration |
| Recursive DP (Top‑down) | **O(n)** | **O(n)** stack + memo | Same but more overhead |

> The iterative solution is optimal and is what interviewers expect for this problem.

---

## Common Pitfalls & How to Avoid Them

| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Forgetting the modulo after each update | Overflow or wrong answer for large inputs | Apply `% MOD` right after each addition |
| Using `int` for counters in Java/Python/C++ | Integer overflow before modulo | Use `long` (Java `long`, Python `int`, C++ `long long`) |
| Recursion depth > 10⁵ | StackOverflowError (Java), RecursionError (Python) | Switch to iterative DP |
| Off‑by‑one: counting empty subsequence | Wrong answer for all‑even array | Do *not* count the empty subsequence – only subsequences with at least one element |
| Mis‑interpreting the parity of 0 | 0 is even, but input guarantees nums[i] ≥ 1 | No special case needed |

---

## SEO‑Optimized Blog Article

---

### Title
**Mastering LeetCode 3247: Number of Subsequences with Odd Sum – Java, Python & C++ Solutions + Interview Tips**

---

### Meta Description
> Learn how to solve LeetCode 3247 – “Number of Subsequences with Odd Sum” – in Java, Python, and C++. Understand the optimal O(n) DP approach, avoid common pitfalls, and boost your coding interview preparation.

---

### Introduction

If you’re hunting for that next coding interview question to ace, “Number of Subsequences with Odd Sum” (LeetCode 3247) is a perfect candidate.  
It’s **medium** difficulty, yet it teaches you a valuable dynamic‑programming pattern: **parity tracking**.  
In this post we’ll walk through the problem, reveal a clean solution in **Java**, **Python**, and **C++**, and highlight the *good*, *bad*, and *ugly* parts you’ll encounter on interview day.

---

#### Why This Problem Rocks

1. **Simplicity** – only two states: odd and even.  
2. **Scalability** – works for arrays of size up to `10⁵`.  
3. **Real‑World Relevance** – parity checks pop up in hashing, cryptography, and network protocols.

---

### The Problem Statement (Summarized)

> **Input** – Integer array `nums` (`1 ≤ len ≤ 10⁵`, `1 ≤ nums[i] ≤ 10⁹`).  
> **Output** – Count of subsequences whose sum is **odd**, modulo `1 000 000 007`.

A subsequence is any set of indices in increasing order; the empty set is *not* counted.

---

### The Key Insight

Adding an **even** number does **not** change the sum’s parity.  
Adding an **odd** number flips it.  

Thus, you only need two counters:

- `odd` – number of odd‑sum subsequences seen so far.
- `even` – number of even‑sum subsequences seen so far.

The recurrence is:

```text
if x is odd:
    newOdd  = even + 1          // even subsequences become odd + the single [x]
    newEven = odd               // odd subsequences stay odd if we skip x
else:    // x is even
    newOdd  = odd
    newEven = even + 1
```

All arithmetic is modulo `1 000 000 007`.

---

### Full Code Examples

#### Java

```java
import java.util.*;

public class Solution {
    private static final long MOD = 1_000_000_007L;

    public int subsequenceCount(int[] nums) {
        long odd = 0, even = 0;

        for (int val : nums) {
            if ((val & 1) == 1) {          // odd
                long newOdd  = (even + 1) % MOD;
                long newEven = odd % MOD;
                odd  = newOdd;
                even = newEven;
            } else {                       // even
                long newOdd  = odd % MOD;
                long newEven = (even + 1) % MOD;
                odd  = newOdd;
                even = newEven;
            }
        }
        return (int) (odd % MOD);
    }
}
```

#### Python

```python
MOD = 1_000_000_007

class Solution:
    def subsequenceCount(self, nums: list[int]) -> int:
        odd, even = 0, 0

        for val in nums:
            if val & 1:  # odd
                odd, even = (even + 1) % MOD, odd % MOD
            else:        # even
                odd, even = odd % MOD, (even + 1) % MOD

        return odd % MOD
```

#### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int subsequenceCount(vector<int>& nums) {
        const long long MOD = 1'000'000'007LL;
        long long odd = 0, even = 0;

        for (int val : nums) {
            if (val & 1) {          // odd
                long long newOdd  = (even + 1) % MOD;
                long long newEven = odd % MOD;
                odd  = newOdd;
                even = newEven;
            } else {                // even
                long long newOdd  = odd % MOD;
                long long newEven = (even + 1) % MOD;
                odd  = newOdd;
                even = newEven;
            }
        }
        return static_cast<int>(odd % MOD);
    }
};
```

---

### Complexity Recap

| Language | Time Complexity | Space Complexity |
|----------|------------------|------------------|
| Java / Python / C++ | **O(n)** | **O(1)** |

No recursion, no memo tables, no backtracking.

---

### Interview Tips

| Question | What Interviewer Might Ask |
|----------|---------------------------|
| *Why does an odd number flip parity?* | Because `odd + odd = even` and `odd + even = odd`. |
| *Can you explain why we add 1 when encountering an odd number?* | The single‑element subsequence `[x]` is odd, giving one extra odd subsequence. |
| *What if the array were all even?* | The answer would be 0 (no odd sums). |
| *How do you handle the modulo?* | Apply `% MOD` after every addition. |
| *Why not use recursion?* | It would risk stack overflow on `n = 10⁵`. |

---

### Common Interview Pitfalls

1. **Off‑by‑one errors** – forgetting to exclude the empty subsequence.  
2. **Integer overflow** – using 32‑bit ints in Java or C++ before modulo.  
3. **Unnecessary recursion** – leads to time & space overhead.  
4. **Not handling large `MOD`** – misreading the problem’s modulo requirement.

---

### Final Thoughts

LeetCode 3247 is a lightweight problem that elegantly demonstrates parity‑based DP.  
The same approach applies to many other “sum parity” questions, such as counting subarrays with an even sum.  
Master the two‑state counter, practice the code in the language you’re most comfortable with, and you’ll be ready to hit that “Number of Subsequences with Odd Sum” question with confidence.

Happy coding and best of luck on your next interview!

---

### Call to Action

> Want to see more **medium‑level** LeetCode problems?  
> Subscribe to our newsletter for weekly interview‑ready solutions and interview‑hacking tips.

---

#### End of Article

--- 

With this comprehensive walkthrough and ready‑to‑copy code snippets, you’re fully equipped to tackle LeetCode 3247 in any language you prefer.  
Drop your questions in the comments below or share your own solutions – let’s keep the interview prep community thriving!