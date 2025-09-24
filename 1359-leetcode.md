---
title: LeetCode 1359. Count All Valid Pickup and Delivery Options - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📦 1359 – Count All Valid Pickup and Delivery Options  
### The Hard LeetCode Problem That Will Make Your Interviewer Go “Wow!”

> **Problem link:** <https://leetcode.com/problems/count-all-valid-pickup-and-delivery-options/>  
> **Difficulty:** Hard  
> **Tags:** Dynamic Programming, Math, Recursion

---

### 1️⃣ Problem Recap

You’re given **`n`** orders.  
Each order *i* has a **pickup** (`Pi`) and a **delivery** (`Di`).  
A sequence of the 2·n actions is **valid** if for every order `Di` comes **after** `Pi`.

> **Return** the number of valid sequences modulo **1 000 000 007**.

*Examples*

| n | Output | Explanation |
|---|--------|-------------|
| 1 | 1 | Only `(P1, D1)` |
| 2 | 6 | `(P1,P2,D1,D2)`, `(P1,P2,D2,D1)`, `(P1,D1,P2,D2)`, `(P2,P1,D1,D2)`, `(P2,P1,D2,D1)`, `(P2,D2,P1,D1)` |
| 3 | 90 | … |

**Constraints**

* 1 ≤ n ≤ 500

---

### 2️⃣ Intuition – The “Add‑One‑Order” Observation

Suppose you already know how many sequences exist for **`n‑1`** orders.  
Now you add the *n*-th order:

1. **Insert the new pickup**  
   In the current sequence of 2(n‑1) events you have **2(n‑1) + 1 = 2n‑1** positions to place `Pn` (before any event or at the end).

2. **Insert the delivery**  
   After putting `Pn`, there are **n** free spots after it where you can place `Dn` (exactly one of them will be the last, one before the last, …, or the very first after `Pn`).  
   *Why n?*  
   Every time you add a pickup, you increase the total number of “open” positions by one, so when inserting the delivery you have exactly `n` positions available.

Thus, for each valid sequence of length `2(n‑1)` you get exactly `(2n‑1)·n` new sequences when adding the nth order.

> **Recurrence**  
> `dp[n] = dp[n‑1] · (2n‑1) · n`  
> with `dp[1] = 1`.

That’s it – **one simple multiplicative formula**. No DP table needed, just a loop.

---

### 3️⃣ Complexity

| Metric | Time | Space |
|--------|------|-------|
| For n ≤ 500 | **O(n)** | **O(1)** (only a 64‑bit accumulator)

The modulo keeps the numbers in 32‑bit range, but we use 64‑bit integers to avoid overflow during multiplication.

---

### 4️⃣ Reference Implementations

Below are clean, ready‑to‑copy solutions in **Java**, **Python**, and **C++**. All run in a fraction of a millisecond for the largest input.

---

#### Java

```java
class Solution {
    private static final long MOD = 1_000_000_007L;

    public int countOrders(int n) {
        long result = 1;
        for (int i = 2; i <= n; i++) {
            result = result * (2L * i - 1) % MOD;
            result = result * i % MOD;
        }
        return (int) result;
    }
}
```

> **Why it works**  
> `result` starts with `dp[1] = 1`.  
> Each loop applies the recurrence `dp[i] = dp[i‑1] · (2i‑1) · i`, taking the modulus after each multiplication to keep the value within bounds.

---

#### Python

```python
class Solution:
    MOD = 1_000_000_007

    def countOrders(self, n: int) -> int:
        res = 1
        for i in range(2, n + 1):
            res = res * (2 * i - 1) % self.MOD
            res = res * i % self.MOD
        return res
```

> Python’s arbitrary‑precision integers make the modulo step optional, but we keep it for clarity and speed.

---

#### C++

```cpp
class Solution {
public:
    static const long long MOD = 1'000'000'007LL;

    int countOrders(int n) {
        long long res = 1;
        for (int i = 2; i <= n; ++i) {
            res = res * (2LL * i - 1) % MOD;
            res = res * i % MOD;
        }
        return static_cast<int>(res);
    }
};
```

---

### 5️⃣ The “Good, the Bad, and the Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Good** | • Extremely simple recurrence – O(n) time. <br>• Handles the modulo cleanly. <br>• Works for all n ≤ 500. | – | – |
| **Bad** | • The recurrence can be unintuitive if you first think in terms of DP tables. <br>• Requires a small mental step to realize that the delivery has `n` possible slots. | – | – |
| **Ugly** | • Some naive solutions over‑count by mistakenly adding the delivery before any pickup. <br>• A DP table of size 501×501 is wasteful but still passes due to small constraints. | – | – |

> **Takeaway:** Keep the recurrence in mind – it is the *golden ticket* for this problem.

---

### 6️⃣ Quick Checklist for Interviews

1. **State the problem** in your own words.  
2. **Explain the intuition**: “Insert the new pickup into 2n‑1 slots, then the delivery into n slots.”  
3. **Derive the recurrence** and write it down.  
4. **Show the loop** and modulo arithmetic.  
5. **Discuss complexity**: O(n) time, O(1) space.  
6. **Mention edge cases** (`n==1`).  

> A clean, concise explanation wins points.

---

### 7️⃣ Testing Your Solution

```bash
# Java
javac Solution.java
java Solution

# Python
python - <<'PY'
print(__import__('solution').Solution().countOrders(3))  # 90
PY

# C++
g++ -std=c++17 -O2 solution.cpp -o sol && ./sol
```

---

### 8️⃣ FAQ

| Question | Answer |
|----------|--------|
| *Can I use factorials?* | Yes, `dp[n] = (2n)! / (2^n)` mod 1e9+7, but you’d need pre‑computed factorials and modular inverses. The recurrence is simpler. |
| *Why does the formula use `n` and `2n-1`?* | `n` is the number of positions after the new pickup; `2n-1` is the positions for the pickup itself. |
| *What about memory constraints?* | O(1) – only a 64‑bit variable is used. |

---

### 9️⃣ Wrap‑Up & Job‑Interview Boost

> **Why this problem matters**  
> 1359 is a classic LeetCode “hard” that blends **combinatorics** and **dynamic programming**. Solving it demonstrates to hiring managers that you can:

* Translate a story (orders, pickups, deliveries) into a formal recurrence.  
* Spot hidden combinatorial patterns.  
* Implement modulo arithmetic correctly.  
* Write clean, efficient code in multiple languages.

> **Show it in your portfolio**  
> Add the three implementations as a single “code‑repo” and write the article above as a readme.  
> Recruiters love to see both *thinking* and *coding* showcased.

> **Final words**  
> Keep the recurrence `dp[n] = dp[n‑1] · (2n‑1) · n` on your mental board – it’s your cheat‑sheet for this and many similar sequence‑counting problems. Good luck, and remember: interviewers are looking for *thoughtful* engineers, not just brute‑force solutions. 🚀

---