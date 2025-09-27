---
title: LeetCode 1672. Richest Customer Wealth - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 1672 – Richest Customer Wealth  
### Easy – LeetCode | Java | Python | C++ | Interview‑Ready Solution  

---

### TL;DR  
* **Goal** – Find the maximum wealth among all customers.  
* **Idea** – Sum each customer’s balances, keep the highest sum.  
* **Complexity** – **O(m × n)** time, **O(1)** extra space.  
* **Why it matters** – A classic “scan‑and‑compare” problem that shows you can handle 2‑D arrays, loops, and basic math – perfect for a technical interview.

---

## Table of Contents  

| Section | What you’ll learn |
|---------|-------------------|
| ✅ Problem Statement | Clear definition & examples |
| 🚦 Constraints | Edge‑case checklist |
| 💡 Solution | Simple O(m × n) algorithm |
| 📊 Complexity | Time / Space |
| 📦 Code | Java / Python / C++ |
| 🔧 Common Pitfalls | Avoiding the “gotchas” |
| 🧩 Variations | How to adapt for different interview twists |
| 🎯 Interview Tips | What the interviewer cares about |
| 📈 SEO Boost | Keywords that recruiters search for |
| 💬 Final Thought | Why mastering this helps land a job |

---

## 1. Problem Statement

**Richest Customer Wealth**  
You are given a 2‑D integer grid `accounts` where `accounts[i][j]` is the amount of money the *i‑th* customer has in the *j‑th* bank.  
Return the wealth of the richest customer.

> **Wealth of a customer** = sum of all his bank balances.

### Example

| accounts | richest wealth |
|----------|----------------|
| `[[1,2,3],[3,2,1]]` | 6 |
| `[[1,5],[7,3],[3,5]]` | 10 |
| `[[2,8,7],[7,1,3],[1,9,5]]` | 17 |

---

## 2. Constraints & Edge Cases

| Constraint | Reason |
|------------|--------|
| `1 ≤ m, n ≤ 50` | Small grid, but we still aim for linear scan. |
| `1 ≤ accounts[i][j] ≤ 100` | All values positive – no negative totals. |
| **Edge case** – single customer or single bank | Still works with O(1) logic. |

---

## 3. Solution Overview

1. **Iterate** over each customer (row).  
2. **Sum** all balances in that row.  
3. **Track** the maximum sum encountered.  
4. Return the maximum.

Because all numbers are positive, no need to handle zero or negative sums.  
The algorithm is essentially a *nested loop* – one loop for customers, an inner loop for banks.

---

## 4. Complexity Analysis

| Metric | Calculation | Result |
|--------|-------------|--------|
| **Time** | `O(m × n)` – every cell is visited once | **Linear** |
| **Space** | Constant auxiliary variables | **O(1)** |

---

## 5. Code Implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**. Each uses the same logic but is idiomatic for its language.

### Java

```java
/**
 * 1672. Richest Customer Wealth
 * LeetCode easy problem
 */
public class Solution {
    public int maximumWealth(int[][] accounts) {
        int maxWealth = 0;
        for (int[] customer : accounts) {
            int wealth = 0;
            for (int balance : customer) {
                wealth += balance;
            }
            maxWealth = Math.max(maxWealth, wealth);
        }
        return maxWealth;
    }
}
```

### Python

```python
class Solution:
    def maximumWealth(self, accounts: List[List[int]]) -> int:
        max_wealth = 0
        for customer in accounts:
            wealth = sum(customer)          # built‑in sum is efficient
            max_wealth = max(max_wealth, wealth)
        return max_wealth
```

### C++

```cpp
class Solution {
public:
    int maximumWealth(vector<vector<int>>& accounts) {
        int maxWealth = 0;
        for (const auto& customer : accounts) {
            int wealth = 0;
            for (int balance : customer) wealth += balance;
            maxWealth = max(maxWealth, wealth);
        }
        return maxWealth;
    }
};
```

All three codes run in **O(m × n)** time and use **O(1)** extra memory.

---

## 6. Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Using `int[]` vs `int[][]` in Java** | Always treat the outer array as a list of rows. |
| **Summing into `int` but values may overflow** | In this problem it’s safe (`m*n*100 ≤ 250,000`), but for larger constraints consider `long`. |
| **Skipping the first customer’s wealth** | Initialize `maxWealth` with `0` or compute the first sum first. |
| **Using `Collections.max` on a list of sums** | Avoid extra overhead; use a simple loop. |

---

## 7. Variations & Extensions

| Variation | How to adjust |
|-----------|---------------|
| **Find the k‑th richest customer** | Use a min‑heap of size `k` while iterating. |
| **Allow negative balances** | Still works; just don’t assume positivity. |
| **Large input (1e5 × 1e5)** | Need streaming or parallel sum; but typical interview constraints are small. |

---

## 8. Interview Tips

1. **Clarify** – confirm whether customers and banks can be 1‑based or 0‑based indices.  
2. **Edge‑case** – ask what to return if the matrix is empty (though constraints forbid it).  
3. **Complexity** – explain the O(m × n) time and why you cannot do better without extra information.  
4. **Optimizations** – discuss using a built‑in `sum()` in Python for clarity, but keep a manual loop in Java/C++ for fine control.  
5. **Testing** – propose a few test cases: single row, single column, all equal balances, varying sizes.

---

## 9. SEO‑Optimized Summary

- **Keywords**: LeetCode 1672, Richest Customer Wealth, Java solution, Python solution, C++ solution, interview algorithm, coding interview, job interview prep, algorithm design, time complexity, space complexity.
- **Meta Description** (for a blog post):  
  “Solve LeetCode 1672 – Richest Customer Wealth in Java, Python, and C++. Learn the optimal O(m×n) algorithm, edge‑case handling, and interview‑ready tips to ace coding interviews.”

---

## 10. Final Thought

Richest Customer Wealth may be labeled “Easy,” but mastering it demonstrates key interview strengths:

* **Matrix traversal** – an essential skill for many real‑world problems.  
* **Time/Space analysis** – shows you can balance performance with simplicity.  
* **Clean code** – clear logic, minimal bugs, and language‑specific idioms.

Use the provided snippets as a reference, practice writing them from scratch, and you’ll feel confident tackling similar problems in any technical interview. Happy coding and good luck landing that dream job! 🚀