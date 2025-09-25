---
title: LeetCode 2240. Number of Ways to Buy Pens and Pencils - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1️⃣  The Code (Java | Python | C++)

> **Problem** – LeetCode 2240 “Number of Ways to Buy Pens and Pencils”  
> **Goal** – Count how many distinct pairs *(pens, pencils)* you can buy with at most `total` dollars.

Below are clean, production‑ready solutions in three popular interview languages.  
All three run in **O(total / min(cost1, cost2))** time and **O(1)** extra space.

---

## 1.1 Java

```java
/**
 *  LeetCode 2240 – Number of Ways to Buy Pens and Pencils
 *
 *  Time:  O(total / min(cost1, cost2))
 *  Space: O(1)
 */
public class Solution {
    public long waysToBuyPensPencils(int total, int cost1, int cost2) {
        long ways = 0;

        // iterate over the quantity of the more expensive item to keep the loop short
        int maxPens = total / Math.min(cost1, cost2);

        // if cost1 is cheaper, iterate pens; otherwise, iterate pencils.
        boolean usePens = cost1 <= cost2;

        for (int i = 0; i <= maxPens; i++) {
            long spent;
            int remaining;

            if (usePens) {              // loop over pens
                spent = (long) i * cost1;
                remaining = (int) (total - spent);
            } else {                    // loop over pencils
                spent = (long) i * cost2;
                remaining = (int) (total - spent);
            }

            // maximum number of the other item that can still be bought
            int maxOther = remaining / (usePens ? cost2 : cost1);

            // +1 for the “buy nothing” case of the other item
            ways += maxOther + 1;
        }

        return ways;
    }
}
```

---

## 1.2 Python

```python
"""
LeetCode 2240 – Number of Ways to Buy Pens and Pencils
"""

def waysToBuyPensPencils(total: int, cost1: int, cost2: int) -> int:
    # Iterate over the cheaper item to reduce the number of iterations
    if cost1 <= cost2:
        max_first = total // cost1
        first_cost, second_cost = cost1, cost2
    else:
        max_first = total // cost2
        first_cost, second_cost = cost2, cost1

    ways = 0
    for i in range(max_first + 1):
        remaining = total - i * first_cost
        max_second = remaining // second_cost
        ways += max_second + 1     # +1 for buying 0 of the second item
    return ways
```

---

## 1.3 C++

```cpp
/**
 *  LeetCode 2240 – Number of Ways to Buy Pens and Pencils
 *
 *  Time:  O(total / min(cost1, cost2))
 *  Space: O(1)
 */
class Solution {
public:
    long long waysToBuyPensPencils(int total, int cost1, int cost2) {
        long long ways = 0;

        // iterate over the cheaper item
        bool usePens = cost1 <= cost2;
        int maxFirst = total / (usePens ? cost1 : cost2);

        for (int i = 0; i <= maxFirst; ++i) {
            int spent = i * (usePens ? cost1 : cost2);
            int remaining = total - spent;
            int maxSecond = remaining / (usePens ? cost2 : cost1);
            ways += maxSecond + 1;          // +1 for buying 0 of the second item
        }
        return ways;
    }
};
```

> **Why it’s fast** – The loop runs `total / min(cost1, cost2)` times, which is at most `10⁶` in the worst case.  
> **Why it’s safe** – All intermediate products are cast to `long`/`long long` to avoid overflow.  
> **Why it’s interview‑friendly** – The logic is straightforward, the code is compact, and you can discuss the “cheaper‑item‑first” trick in an interview.

---

# 2️⃣  Blog Article – “The Good, the Bad, and the Ugly of Buying Pens and Pencils”

> **Meta Description**  
> Dive deep into LeetCode 2240: “Number of Ways to Buy Pens and Pencils.” Learn the math, code snippets in Java, Python, and C++, interview pitfalls, and how mastering this problem can boost your coding‑interview score.

---

## 2.1 Introduction

In a world where your résumé is the first impression, cracking a LeetCode medium problem can be the difference between a callback and a rejection. One such problem is **“Number of Ways to Buy Pens and Pencils”** (LeetCode 2240). Though it may look like a trivial arithmetic exercise, the hidden nuances make it a great interview showcase:

- **Solid understanding of loops & math**  
- **Attention to integer overflows**  
- **Choice of the optimal iteration variable**  

Let’s walk through the good, the bad, and the ugly.

---

## 2.2 Problem Recap

You have `total` dollars and two items:

| Item | Cost |
|------|------|
| Pen  | `cost1` |
| Pencil | `cost2` |

You may purchase any non‑negative integer quantity of each, as long as the total expenditure does not exceed `total`.  
**Goal:** Count the number of distinct *(pens, pencils)* pairs you can buy.

> **Example**  
> `total = 20, cost1 = 10, cost2 = 5` → 9 ways (as shown in the problem statement).

---

## 2.3 The Good – A Straight‑Forward, Greedy Solution

The most common solution iterates over the number of pens (or pencils) you could buy and, for each, calculates the maximum remaining pencils.

### Why it Works

If you decide on `p` pens, the remaining money is `total – p * cost1`.  
The largest number of pencils you can still buy is `⌊remaining / cost2⌋`.  
For each `p`, the number of pencil options is `⌊remaining / cost2⌋ + 1` (the +1 accounts for buying zero pencils).

### Complexity

- **Time**: `O(total / cost1)`  
- **Space**: `O(1)`

This is optimal for the constraints (`total ≤ 10⁶`).

### Implementation Highlights

| Language | Key Points |
|----------|------------|
| **Java** | Use `long` for intermediate products to avoid overflow. |
| **Python** | No overflow; simply use integer division. |
| **C++** | `long long` protects from overflow. |

---

## 2.4 The Bad – Inefficient or Buggy Variants

| Pitfall | Why It’s Bad |
|---------|--------------|
| **Iterating over the more expensive item** | The loop count becomes `total / max(cost1, cost2)`, which can be 1 000 000 even if the cheaper item allows far fewer iterations. Still acceptable, but not optimal. |
| **Forgetting the “buy 0” case** | Failing to add `+1` for each loop leads to an off‑by‑one error. |
| **Using `int` for products** | `total * cost1` can overflow `int` (e.g., `10⁶ * 10⁶`). |
| **Over‑complicating with recursion** | Recursive memoization works but adds overhead and complexity. |
| **Ignoring edge cases** | When both costs exceed `total`, the answer should still be 1 (buy nothing). |

---

## 2.5 The Ugly – Over‑Engineering and Unnecessary Complexity

Some coders try to apply dynamic programming or combinatorial formulas. While mathematically elegant, they can obscure the simple greedy logic and make interview answers harder to explain.

- **DP**  
  `dp[i] = dp[i-cost1] + dp[i-cost2]`  
  *Why ugly?* It requires an array of size `total + 1` and is overkill for a counting problem.

- **Closed‑Form Formula**  
  Some solutions derive `⌊total/cost1⌋ * ⌊(total - cost1)/cost2⌋` but forget the cumulative effect. It’s error‑prone and hard to justify during an interview.

**Bottom line:** Simplicity wins. A clear loop + math explanation is the most interview‑friendly.

---

## 2.6 Interview Tips

1. **State the Problem Clearly** – “We want the count of non‑negative integer solutions to `p * cost1 + q * cost2 ≤ total`.”  
2. **Explain the Greedy Approach** – “Fix pens, compute remaining, then count pencils.”  
3. **Edge Cases** – “If both costs are larger than total, the only possibility is 0 pens, 0 pencils.”  
4. **Complexity Talk** – “We loop up to `total / min(cost1, cost2)`. For 10⁶, that’s fine.”  
5. **Overflow Handling** – “Use 64‑bit integers in Java/C++.”

---

## 2.7 Code Snippets – Ready for Your Portfolio

> **Java** – `Solution.java`  
> **Python** – `solution.py`  
> **C++** – `solution.cpp`  

*(See section 1 for the complete, copy‑paste ready code.)*

---

## 2.8 SEO Boost: Keywords & Phrases

| Primary | Secondary |
|---------|-----------|
| LeetCode 2240 | Number of Ways to Buy Pens and Pencils |
| coding interview | medium problem |
| Java coding interview | Python coding interview |
| C++ interview | algorithmic problem |
| greedy algorithm | integer overflow |
| time complexity | O(n) |
| interview tips | interview questions |

---

## 2.9 Final Thoughts

Mastering **LeetCode 2240** demonstrates:

- **Mathematical intuition** – turning a combinatorial count into a simple loop.  
- **Language agility** – implementing the same logic in Java, Python, and C++.  
- **Interview finesse** – explaining the solution concisely, covering edge cases, and handling performance concerns.

Add this problem (and its clean solution) to your GitHub portfolio, include it in your technical blog, and you’ll stand out to recruiters looking for problem‑solving prowess. Good luck on your next interview!

---