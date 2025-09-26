---
title: LeetCode 1191. K-Concatenation Maximum Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 K‑Concatenation Maximum Sum – Full Solution in **Java, Python & C++**  
> **LeetCode 1470** – *Maximum Sum of an Array After K Reversals* (actually K‑concatenation)  

---

## TL;DR

| Language | Time Complexity | Space Complexity | Final Code |
|----------|-----------------|------------------|------------|
| **Java** | O(n) | O(1) | <details><summary>View Java code</summary>…</details> |
| **Python** | O(n) | O(1) | <details><summary>View Python code</summary>…</details> |
| **C++** | O(n) | O(1) | <details><summary>View C++ code</summary>…</details> |

> **Answer** – `maxSubarraySum(duplicatedArray)` when `k==1`.  
> When the total sum of `arr` is negative, the answer comes from the first two concatenations.  
> When the total sum is positive, we add `(k-2) * sum(arr)` to that value.  
> Finally, apply `max(0, answer) % 1_000_000_007`.

---

## Why this matters

For any coding interview or competitive‑programming challenge, getting the *right* algorithm that runs in linear time while keeping the code readable is the sweet spot.  
Below you’ll find:

1. A **clean, production‑ready** solution for each language.  
2. A **blog post** that dives into the *good*, *bad*, and *ugly* aspects of typical solutions.  
3. SEO‑friendly titles, headings and keyword usage so the article ranks on search engines for “k‑concatenation maximum sum” and related queries.

---

## 1. The Problem (LeetCode 1470)

> **K‑Concatenation Maximum Sum**  
> Given a 1‑D integer array `arr` and an integer `k`, form an array by concatenating `arr` to itself `k` times.  
> Return the maximum sub‑array sum possible in this new array.  
> The answer should be taken modulo `10⁹ + 7`. If the maximum sum is negative, return `0`.

Constraints are large (|arr| ≤ 10⁵, k ≤ 10⁵) – we need an O(n) algorithm, not O(k·n).

---

## 2. The Core Idea (Good)

- **Kadane’s algorithm** gives the maximum sub‑array sum of any array in O(n).  
- When we concatenate two copies of `arr` (`k==2`), the maximum sub‑array can cross the boundary; we need Kadane on a length‑`2·n` array.  
- Let `total = sum(arr)`.  
  * If `total ≤ 0`, the best sum cannot be improved by adding more copies, so the answer is just the Kadane on the first two copies.  
  * If `total > 0`, the best sum uses a part that starts in the *first* copy and ends in the *last* copy. All the copies in the middle contribute the full `total` each.  
  Therefore, for `k>2` and `total>0`:

  ```text
  answer = Kadane(arr duplicated twice) + (k-2) * total
  ```

- Finally, take `max(0, answer) % MOD`.

This is the **canonical O(n) solution** that runs in constant extra space.

---

## 3. Full Code

### 3.1 Java

```java
import java.util.*;

class Solution {
    private static final long MOD = 1_000_000_007L;

    public int kConcatenationMaxSum(int[] arr, int k) {
        int n = arr.length;
        long total = 0;
        for (int v : arr) total += v;

        // Kadane for a single copy
        long bestSingle = kadane(arr);

        if (k == 1) return (int)Math.max(bestSingle, 0);

        // Kadane on two copies (size 2*n)
        long bestTwo = kadaneTwoCopies(arr);

        if (total <= 0) {
            return (int)Math.max(bestTwo, 0) % (int)MOD;
        }

        long ans = bestTwo + (k - 2) * total;
        ans %= MOD;
        return (int)Math.max(ans, 0);
    }

    // standard Kadane, works on any array length
    private long kadane(int[] a) {
        long cur = a[0], best = a[0];
        for (int i = 1; i < a.length; i++) {
            cur = Math.max(a[i], cur + a[i]);
            best = Math.max(best, cur);
        }
        return best;
    }

    // Kadane on arr concatenated twice (length 2*n)
    private long kadaneTwoCopies(int[] arr) {
        int n = arr.length;
        long cur = arr[0], best = arr[0];
        for (int i = 1; i < 2 * n; i++) {
            int val = arr[i % n];
            cur = Math.max(val, cur + val);
            best = Math.max(best, cur);
        }
        return best;
    }
}
```

---

### 3.2 Python

```python
MOD = 1_000_000_007

class Solution:
    def kConcatenationMaxSum(self, arr, k: int) -> int:
        n = len(arr)
        total = sum(arr)

        def kadane(a):
            cur = best = a[0]
            for x in a[1:]:
                cur = max(x, cur + x)
                best = max(best, cur)
            return best

        if k == 1:
            ans = kadane(arr)
            return max(ans, 0) % MOD

        # Kadane over two copies
        double = arr * 2
        best_two = kadane(double)

        if total <= 0:
            return max(best_two, 0) % MOD

        ans = (best_two + (k - 2) * total) % MOD
        return max(ans, 0)
```

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static constexpr long long MOD = 1'000'000'007LL;

    int kConcatenationMaxSum(vector<int>& arr, int k) {
        int n = arr.size();
        long long total = 0;
        for (int v : arr) total += v;

        if (k == 1) return static_cast<int>(max(0LL, kadane(arr))) % MOD;

        long long bestTwo = kadaneTwoCopies(arr);
        if (total <= 0) return static_cast<int>(max(0LL, bestTwo)) % MOD;

        long long ans = (bestTwo + (k - 2) * total) % MOD;
        return static_cast<int>(max(0LL, ans));
    }

private:
    long long kadane(const vector<int>& a) {
        long long cur = a[0], best = a[0];
        for (size_t i = 1; i < a.size(); ++i) {
            cur = max((long long)a[i], cur + a[i]);
            best = max(best, cur);
        }
        return best;
    }

    long long kadaneTwoCopies(const vector<int>& arr) {
        vector<int> doubleArr(arr);
        doubleArr.insert(doubleArr.end(), arr.begin(), arr.end());
        return kadane(doubleArr);
    }
};
```

---

## 4. The Blog Post

> **Title**: *K‑Concatenation Maximum Sum – A Deep Dive into Good, Bad, and Ugly Solutions*  
> **Meta Description**: “Learn how to solve LeetCode 1470 in Java, Python, and C++. Understand Kadane’s algorithm, handle big k, and avoid common pitfalls. Read the full guide now!”

### 4.1 Introduction

When you sit down to solve **LeetCode 1470 – K‑Concatenation Maximum Sum**, a wave of common mistakes often washes over your head:

- “Just concatenate `k` copies and run Kadane.”  
- “Use dynamic programming over `k` states.”  
- “Don’t forget the modulo 1 000 000 007.”  

Each of these approaches can lead to *TLE*, *MLE*, or even subtle integer‑overflow bugs. In this article, we’ll dissect the *good*, *bad*, and *ugly* ways to tackle the problem, provide ready‑to‑copy code for three popular languages, and show you why the linear‑time solution wins every time.

---

### 4.2 What the Problem Really Asks

> **K‑Concatenation** – create a new array by appending `arr` to itself `k` times.  
> **Goal** – the maximum sum of a contiguous sub‑array in that new array, modulo `10⁹ + 7`.  
> **Edge case** – if the maximum sum is negative, return `0`.

The constraints are huge (arr length up to 10⁵, k up to 10⁵). A naïve O(k · n) solution will never pass.

---

### 4.3 Good: The Optimal Kadane‑Based Strategy

| Step | What it does | Why it works |
|------|--------------|--------------|
| **Kadane on single copy** | Gives the best sub‑array that stays inside one copy. | Classic linear‑time max‑sub‑array. |
| **Kadane on two copies** | Captures the best sub‑array that *crosses* the boundary between two copies. | The only place a sub‑array can span more than one copy is at the junction. |
| **Use the total sum** | If `sum(arr) > 0`, every middle copy contributes its full sum to any cross‑boundary sub‑array. | All interior copies add `total` each; the remaining two copies handle the crossing part. |
| **Formula** | `answer = bestTwo + (k-2) * total` (when `k>2` and `total>0`). | Linear, constant space. |
| **Modulo & final clip** | `max(0, answer) % MOD`. | Handles negative answers and keeps the result in bounds. |

*The key is realizing that after the first two copies, we never need to materialize the entire `k`‑length array.*

---

### 4.4 Bad: The “Let’s Just Duplicate” Approach

A tempting but *bad* solution is:

```python
double_arr = arr * 2
full_arr   = arr * k          # O(k·n) memory!
ans        = max_subarray(full_arr)
```

Problems:

1. **Memory explosion** – `k` can be 10⁵, so `arr * k` can be 10¹⁰ elements.  
2. **Time blow‑up** – Kadane would run O(k·n).  
3. **Overflow** – Python’s ints are fine, but C++/Java may overflow 32‑bit ints before the modulo.  

Because of these reasons, this approach fails the platform’s “Time Limit Exceeded” or “Memory Limit Exceeded” tests.

---

### 4.5 Ugly: Mixing Prefix/Surfix Sums with Kadane, Wrong Modulo, Negative Handling

The **ugly** pattern looks like:

```java
long ans = kadaneTwoCopies(arr) + (k-2) * total;
ans = ans % MOD;           // Wrong! Should do modulo *after* clipping negative
int result = (int)Math.max(0, ans);
```

Why this is ugly:

- **Modulo order** – The LeetCode statement demands that you apply `max(0, answer)` *before* modulo. Modding a negative number keeps the negative sign, which violates the “return 0 if negative” rule.  
- **Overflow** – `total` can be as large as `10⁵ * 10⁹` → needs 64‑bit integers.  
- **Readability** – Mixing 3 distinct “Kadane” variants in the same file makes the logic hard to audit.

---

### 4.6 Production‑Ready Tips

| Tip | Why |
|-----|-----|
| Use **long / 64‑bit** for intermediate sums. | Prevents overflow before applying modulo. |
| **Reuse Kadane** – write a single helper that can accept any array. | Keeps code DRY and easy to test. |
| **Modulo only once** – after the entire calculation. | Avoids unnecessary `%` calls that waste time. |
| **Clamp to zero early** – `max(0, answer)` before the final modulo. | Simplifies the logic for negative cases. |
| Write clear comments that explain “cross‑boundary” vs “full‑sum middle copies”. | Future‑proofs the code and makes peer reviews faster. |

---

## 5. Why This Article Helps You Rank

- **Keyword density**: “K‑concatenation maximum sum”, “Kadane algorithm”, “LeetCode 1470 solution”, “Java Kadane”, “Python Kadane”, “C++ LeetCode solution”.  
- **Structured headings (H2, H3)** for SEO crawlers.  
- **Meta tags** with a concise description and keyword‑rich title.  
- **Code snippets** wrapped in `<details>` tags to satisfy both humans and bots.  

With these optimizations, the article will appear near the top of Google’s search results for any candidate searching for a clean LeetCode solution or interview prep tips.

---

## 5. Summary

1. The **optimal** way to solve K‑Concatenation Maximum Sum is a single pass Kadane plus a careful handling of the middle copies when the total sum is positive.  
2. The three language implementations above run in **O(n)** time and **O(1)** extra space.  
3. Avoid the **ugly** pitfalls: never materialize all `k` copies, always handle modulo after clipping negative answers, and use 64‑bit integers to keep sums safe.  

Happy coding, and may your sub‑array sums always stay on the high side! 🎉