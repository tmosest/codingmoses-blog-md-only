---
title: LeetCode 2144. Minimum Cost of Buying Candies With Discount - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2144. Minimum Cost of Buying Candies With Discount  
> **Easy** – LeetCode  

**Languages** – Java, C++, Python  

> This post will give you three production‑ready solutions, a deep‑dive into the algorithmic trick, and an SEO‑optimized blog‑style write‑up that can help you land your next software‑engineering interview.  

---

## 1. Problem Overview

> A shop runs a “buy 2, get 1 free” promotion.  
> For any two candies you pay for, you may choose a third candy *whose cost is ≤ the cheaper of the two paid candies* and get it free.  
>  
> Given an array `cost` of the prices of all candies, return the **minimum total cost** needed to acquire every candy.  

### Constraints

| Constraint | Value |
|------------|-------|
| `1 ≤ cost.length ≤ 100` |  |
| `1 ≤ cost[i] ≤ 100` |  |

---

## 2. Key Insight

The promotion is essentially a **greedy “take the cheapest possible free candy”**.  
If we sort all candies in **non‑decreasing** order, then:

- The **most expensive** candy must always be paid for (you can never get it free because there’s no cheaper candy to satisfy the “≤” condition).  
- The **second most expensive** candy also must be paid for (there’s no candy cheaper than it that can be taken free while still obeying the rule).  
- The **third most expensive** candy becomes *free* – it will always be the cheapest among the three most expensive, because it sits exactly at the “third‑largest” position.

By repeating this pattern from the most expensive end, we obtain an optimal strategy:  
> **Pay for the two most expensive remaining candies, skip the third one as free, and repeat.**

In terms of indices (after sorting ascendingly), the candies that are *free* are those whose index satisfies  
`(n – i) % 3 == 0` where `n` is the total number of candies and `i` is the 0‑based index.  
All others contribute to the total cost.

---

## 3. The Algorithm

1. **Sort** the array `cost` in ascending order.  
2. **Iterate** through the sorted array and sum the prices of all candies that are *not* free.  
   *Free candies* are those at indices where `(n - i) % 3 == 0`.  
3. **Return** the computed sum.

The algorithm is **O(n log n)** due to sorting, and **O(1)** auxiliary space (excluding the space used by the sorting routine).

---

## 4. Code Implementations

### 4.1 Java

```java
import java.util.Arrays;

public class Solution {
    public int minimumCost(int[] cost) {
        Arrays.sort(cost);            // O(n log n)
        int total = 0;
        int n = cost.length;

        for (int i = 0; i < n; i++) {
            // If (n - i) % 3 == 0 → free candy
            if ((n - i) % 3 != 0) {
                total += cost[i];
            }
        }
        return total;
    }
}
```

### 4.2 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumCost(vector<int>& cost) {
        sort(cost.begin(), cost.end());          // O(n log n)
        int n = cost.size(), ans = 0;
        for (int i = 0; i < n; ++i) {
            if ((n - i) % 3 != 0) ans += cost[i];
        }
        return ans;
    }
};
```

### 4.3 Python

```python
class Solution:
    def minimumCost(self, cost: List[int]) -> int:
        cost.sort()                              # O(n log n)
        n = len(cost)
        return sum(cost[i] for i in range(n) if (n - i) % 3 != 0)
```

> **Tip** – All three snippets rely on the same math: free candies are every third one from the end. This keeps the code short, readable, and fast.

---

## 5. What Works – The “Good”

* **Greedy & Simple** – The solution follows an intuitive “take the cheapest free candy” strategy that can be proven optimal by exchange argument.  
* **Low Complexity** – Only one sort is required, no DP or recursion.  
* **Language‑agnostic** – The same O(n log n) pattern works in Java, C++, Python, and many other languages.  
* **Readability** – Clear loop condition `(n - i) % 3 != 0` instantly communicates the free‑candy rule.

---

## 6. What to Watch For – The “Bad”

| Potential Pitfall | How to Avoid |
|-------------------|--------------|
| **Off‑by‑one errors** | Verify that you’re iterating from `i = 0` to `n-1` and using `(n - i) % 3`. |
| **Large inputs** | Even though `n ≤ 100`, the algorithm scales fine to millions; keep the sort stable. |
| **Modular arithmetic confusion** | Think of free candies as every third element *from the right*, not from the left. |

---

## 7. Common Mistakes & Debugging

| Mistake | Symptom | Fix |
|---------|---------|-----|
| Using `i % 3 == 0` instead of `(n - i) % 3` | Wrong free candies (first, fourth, …) | Switch to `if ((n - i) % 3 != 0)` |
| Skipping the last two elements when `n % 3 == 1` | Missing a cheap candy | Use the general condition, not a hard‑coded loop. |
| Not sorting the array | Random free candies | Always sort before applying the rule. |

---

## 8. Extensions & Variants

| Variant | What changes? |
|---------|--------------|
| **Buy `k`, get `m` free** | Sort, then skip every `(k+m)`‑th candy from the end. |
| **Different free‑candy rule (≤ vs <)** | Adjust the inequality, but the greedy pattern still holds. |
| **Large input (n up to 10⁵)** | Use counting sort or radix sort if `cost[i]` ≤ 10⁶ for O(n) time. |

---

## 9. SEO‑Optimized Blog Article

> **Title:** *Master LeetCode 2144 – Minimum Cost of Buying Candies with Discount*  
> **Meta Description:** *Learn the greedy O(n log n) solution for LeetCode 2144, see Java, C++, and Python implementations, and get interview‑ready tips.*

### Introduction

The “buy two, get one free” problem is a classic LeetCode challenge that tests your ability to combine sorting with a greedy strategy. Despite its easy difficulty rating, many candidates stumble on the subtle “≤ the cheaper of the two paid candies” condition. In this post, we’ll walk through the optimal solution, provide clear code snippets in Java, C++, and Python, and dissect why the greedy approach works. By the end, you’ll be ready to ace this problem in your next technical interview.

### Problem Statement

(Insert the problem description from the beginning of this article.)

### Why Greedy Works

(Explain the exchange argument: any optimal solution can be reordered so that the most expensive candy is paid for, the second most expensive is paid for, and the third is free, etc.)

### Algorithm Walkthrough

(Detail the steps: sort → iterate → sum except every third from the end.)

### Code Snippets

(Insert the Java, C++, Python codes as shown above.)

### Time & Space Complexity

(Explain O(n log n) time, O(1) space.)

### Common Pitfalls

(Include the bad table.)

### Conclusion

(Wrap up, mention that mastering this pattern helps with many “buy‑X‑get‑Y‑free” variants.)

### Keywords

*LeetCode 2144*, *minimum cost of buying candies*, *buy two get one free*, *greedy algorithm*, *Java solution*, *C++ solution*, *Python solution*, *interview preparation*, *software engineer interview tips*.

---

## 10. Takeaway for Job Seekers

* **Showcase the solution in the interview** – Talk through the greedy insight, write the code on the whiteboard, and explain why sorting is the optimal first step.  
* **Mention extensions** – If the interviewer asks about variations, be ready to discuss “buy `k`, get `m` free” or large‑scale data.  
* **Highlight readability** – A concise loop like `if ((n - i) % 3 != 0)` is both efficient and expressive.

Good luck, and may your interview candy be sweet!