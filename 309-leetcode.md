---
title: LeetCode 309. Best Time to Buy and Sell Stock with Cooldown - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 309. Best Time to Buy and Sell Stock with Cooldown  
**Difficulty:** Medium  
**Tag:** DP, State Machine, Greedy, O(1) Space  

---

### TL;DR  
* Use a **state‑machine DP** that keeps only three variables: `hold`, `sold`, and `rest`.  
* Transition in *O(1)* per day → **O(n)** time, **O(1)** space.  
* The core recurrence:

| State | Meaning | Transition |
|-------|---------|------------|
| `hold` | Holding a stock today | `hold = max(hold, rest - price)` |
| `sold` | Sold a stock today | `sold = max(sold, hold + price)` |
| `rest` | In cooldown or do nothing | `rest = max(rest, sold)` |

---

## 1. The Code

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.

---

### 1.1 Java

```java
public class Solution {
    public int maxProfit(int[] prices) {
        int hold = Integer.MIN_VALUE; // max profit when holding a stock
        int sold = 0;                 // max profit when last action was selling
        int rest = 0;                 // max profit when in cooldown / idle

        for (int price : prices) {
            int prevHold = hold;
            int prevSold = sold;

            hold = Math.max(prevHold, rest - price);   // buy today or keep holding
            sold = Math.max(prevSold, prevHold + price); // sell today or keep sold
            rest = Math.max(rest, prevSold);          // cooldown or stay idle
        }
        return sold; // the last action must be a sell to realize profit
    }
}
```

---

### 1.2 Python

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        hold = float('-inf')  # holding a stock
        sold = 0              # just sold
        rest = 0              # cooldown / idle

        for price in prices:
            prev_hold, prev_sold = hold, sold
            hold = max(prev_hold, rest - price)   # buy or keep holding
            sold = max(prev_sold, prev_hold + price)  # sell or keep sold
            rest = max(rest, prev_sold)              # cooldown or idle

        return sold
```

---

### 1.3 C++

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int hold = INT_MIN; // holding a stock
        int sold = 0;       // just sold
        int rest = 0;       // cooldown / idle

        for (int price : prices) {
            int prevHold = hold, prevSold = sold;
            hold = max(prevHold, rest - price);   // buy or keep holding
            sold = max(prevSold, prevHold + price); // sell or keep sold
            rest = max(rest, prevSold);            // cooldown or idle
        }
        return sold; // best profit ends with a sale
    }
};
```

All three implementations run in **O(n)** time and use only **O(1)** additional memory – perfect for interview and production.

---

## 2. The Blog Article

> **Title:** *“Mastering LeetCode 309: Best Time to Buy and Sell Stock with Cooldown – The Good, The Bad, and The Ugly”*  
> **Meta Description:**  
> Learn how to crack LeetCode 309 in minutes. Dive into the DP state‑machine solution, pitfalls, and why O(1) space matters for interviews.  

---

### 2.1 Introduction  

If you’ve hit LeetCode 309 “Best Time to Buy and Sell Stock with Cooldown”, congratulations!  
It’s a classic DP problem that shows up in real‑world interview pipelines: **multiple transactions with a one‑day cooldown**.  

This article breaks down the solution into three parts – the *good* (the clean DP idea), the *bad* (common mistakes and why they fail), and the *ugly* (tricks to make the code even shorter without losing readability).  
We’ll also sprinkle in some **SEO‑friendly keywords** – *LeetCode, DP, stock trading, interview questions, job hunting* – so the post ranks well for recruiters looking for candidates who can solve complex dynamic programming problems.

---

### 2.2 The Good – State‑Machine DP in O(1) Space  

#### 2.2.1 Intuition  

Think of each day as a **machine** that can be in one of three states:

1. **Hold** – you own a stock.  
2. **Sold** – you just sold the stock.  
3. **Rest** – you’re either in cooldown (the day after a sale) or doing nothing.

We don’t need to remember *exactly* when the last buy happened; we only need the best profit achievable for each state up to today.

#### 2.2.2 Recurrence  

| Transition | Formula |
|------------|---------|
| `hold` → `hold` | Keep holding: `hold` stays the same. |
| `hold` → `sold` | Sell today: `sold = hold + price`. |
| `rest` → `hold` | Buy today: `hold = rest - price`. |
| `sold` → `rest` | Cooldown: `rest = sold`. |
| `rest` → `rest` | Do nothing: `rest` stays the same. |

Combining the “do nothing” option gives us the **max** in each assignment:

```text
hold = max(hold, rest - price)
sold = max(sold, hold + price)
rest = max(rest, sold)
```

Because each variable only depends on its previous value (or the previous *rest*), we can iterate through the array once and keep only three integer variables – **constant extra space**.

---

#### 2.2.2 Code‑Ready Implementation  

We’ve already shown Java, Python, and C++ above.  
A key pattern: **update using previous values** (`prevHold`, `prevSold`) to avoid overwriting before the next state is computed.

---

### 2.3 The Bad – Pitfalls That Trip You Up  

1. **Storing the whole DP array (`O(n)` memory)** –  
   *Many candidates start with a classic `dp[i]` array. It works but wastes memory and is often flagged by interviewers as “inefficient”.*

2. **Wrong initial values** –  
   Setting `hold = 0` (instead of `-∞`) incorrectly allows “buying” on day 0 without a sale, leading to negative profits being counted as valid.

3. **Forgetting the cooldown** –  
   Some solutions try to skip the `rest` state and just set `hold = max(hold, prices[i-2] - price)`.  
   This forgets that after selling you must **idle** for one day, so the transition must involve the *sold* state explicitly.

4. **Using a double‑nested loop** –  
   A naïve `O(n^2)` solution that scans all previous buy days for each sell day passes the tests but gets marked “TLE” on LeetCode’s large inputs.

**Fixes:**  
* Always initialize `hold` with `-∞` (or `INT_MIN`) to indicate “no stock held yet”.  
* Keep the `rest` variable, even if you never “do nothing” on day 0 – it keeps the recurrence clean.  
* Avoid a nested loop; use the rolling `maxDiff` trick described in the *ugly* section.

---

### 2.4 The Ugly – Shortening the Code (While Staying Readable)  

| Technique | How It Helps | Example |
|-----------|--------------|---------|
| **Ternary update** | Combine `max` calls into one line | `hold = Math.max(hold, rest - price);` |
| **Pre‑compute `rest`** | `rest` is always the best of itself and `sold` → no separate var | `rest = Math.max(rest, sold);` |
| **Inline constants** | `-∞` via `Integer.MIN_VALUE` or `float('-inf')` | `int hold = Integer.MIN_VALUE;` |

> **Tip for Java:**  
> Using `Math.max` is preferable to `?:` because it is *type‑safe* and self‑documenting.

---

### 2.5 Putting It All Together – A Final Checklist  

| Check | Why |
|-------|-----|
| **Time** – `O(n)` | LeetCode time limit is usually 1–2 seconds. |
| **Space** – `O(1)` | Recruiters love candidates who can write memory‑efficient code. |
| **Readability** – Keep variable names descriptive (`hold`, `sold`, `rest`). | Avoid code‑review rejections. |
| **Edge Cases** – Empty array → return `0`. | Handles 1‑day inputs gracefully. |
| **Testing** – Write unit tests for: 1‑day array, decreasing prices, increasing prices, random mixes. | Shows you can’t just “guess” the solution. |

---

### 2.6 Closing – How This Helps Your Job Hunt  

* **Showcase in Resume** – Add a bullet: “Solved LeetCode 309 in O(n) time & O(1) space using DP state‑machine.”  
* **LinkedIn Post** – Share the short solution snippet and tag recruiters.  
* **Interview Prep** – Practice this pattern on other LeetCode stock problems (e.g., 123, 188) – the *state‑machine* DP framework is reusable.  

By mastering LeetCode 309, you demonstrate:

1. **Understanding of DP fundamentals** – essential for roles in algorithm engineering, fintech, and data‑driven product teams.  
2. **Space‑time trade‑off awareness** – recruiters value candidates who can produce clean, efficient code.  
3. **Problem‑solving mindset** – you can tackle real‑world scenarios like trade scheduling or resource allocation.

---

### 2.7 SEO‑Friendly Keywords & Tags  

| Primary Keywords | Secondary Keywords |
|------------------|--------------------|
| LeetCode 309 | dynamic programming |
| Best Time to Buy and Sell Stock | cooldown problem |
| DP stock trading | interview questions |
| Job hunting | coding interview |
| O(1) space | algorithm interview |

---

### 2.8 Call to Action  

> **Ready to land your next role?**  
> Add this solution to your portfolio, post the code on GitHub, and share your own variations in the comments.  
> Recruiters on LinkedIn love seeing clean DP code paired with thoughtful explanations – make sure your posts are indexed with the right keywords.

---

### 2.9 FAQ  

1. **Can we ignore the cooldown?**  
   Removing the cooldown turns the problem into LeetCode 122 (best time to buy & sell once). The state machine still works; just set `rest = hold` instead of `max(rest, sold)`.

2. **What if we want to limit the number of transactions?**  
   Add another dimension to the state: `k` (transactions left). The recurrence remains the same, but you’ll need an array of size `k`.

3. **Why not just use a greedy solution?**  
   Greedy works for the simple “no cooldown” version but fails when a cooldown forces you to skip a day. DP is the only way to capture that dependency.

---

### 2.10 Final Thoughts  

*The Good:* A concise, O(1) DP that elegantly models the “hold / sold / rest” states.  
*The Bad:* Nested loops, incorrect initial values, and forgetting the cooldown.  
*The Ugly:* Slightly less readable one‑liners that sacrifice clarity for brevity.

With this knowledge, you’re not only ready to ace LeetCode 309, but you’re also demonstrating the kind of algorithmic maturity that employers look for in software engineers and data scientists.  

Good luck, and may your interview stack overflow with “Yes, I solved it!”  

---  

### 2.11 References  

1. LeetCode 309 – Best Time to Buy and Sell Stock with Cooldown  
2. *Dynamic Programming – State Machine* – https://www.geeksforgeeks.org/dynamic-programming-state-machine/  
3. *Interview Prep: LeetCode 309* – https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/  

---  

> **© 2024 Your Name – All rights reserved.**  

---  

### 2.12 Quick Checklist for Interviewers  

* [ ] Does the candidate explain the 3 states clearly?  
* [ ] Are they able to derive the recurrence from the problem statement?  
* [ ] Do they write the code with `O(1)` space, and can they justify the choice?  
* [ ] Can they identify common pitfalls (like forgetting the cooldown)?  

If you answer **yes** to all of the above, you’re ready to impress any hiring manager. Happy coding!