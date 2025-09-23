---
title: LeetCode 3299. Sum of Consecutive Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Problem Recap – Sum of Consecutive Subsequences (LeetCode 3299)

Given an integer array `nums` (1 ≤ `nums[i]` ≤ 10⁵, 1 ≤ `nums.length` ≤ 10⁵), a **consecutive subsequence** is a non‑empty subsequence whose elements form a strictly increasing or decreasing arithmetic progression with difference ±1.  
The *value* of a subsequence is the sum of its elements.  

**Goal** – return the sum of the values of **all** consecutive non‑empty subsequences modulo 10⁹+7.

> **Example**  
> `nums = [1, 4, 2, 3]`  
> Consecutive subsequences:  
> `[1]`, `[4]`, `[2]`, `[3]`, `[1,2]`, `[2,3]`, `[4,3]`, `[1,2,3]`  
> Sum of values = 31

---

## 🔑 Solution Overview

The naive O(2ⁿ) enumeration is impossible.  
Observe:

* A consecutive subsequence can be split into an increasing run and a decreasing run.
* If we process the array **forward** we can compute, for each value `v`,  
  *`cntInc[v]`* – how many increasing subsequences end with value `v`  
  *`sumInc[v]`* – the total value of those subsequences  
* Similarly, a **backward** pass gives the decreasing parts.

With these two passes we can combine the counts and sums to get the final answer in **O(n + maxValue)** time and **O(maxValue)** memory (maxValue ≤ 10⁵).

### Why it works

For a fixed value `x` that appears at position `i`:

1. **Increasing side** – any subsequence that ends at `x` must have been extended from a subsequence that ended at `x‑1` immediately before `i`.  
   *New count* = `cntInc[x‑1] + 1` (`+1` for the subsequence consisting of only `x`).  
   *New sum*   = `sumInc[x‑1] + x * (cntInc[x‑1] + 1)`.

2. **Decreasing side** – symmetric, using `x+1` as the previous value.

The total contribution of all subsequences ending at `x` is the sum of the two sides, minus the double counted single‑element subsequence (handled naturally by the DP equations).

The same DP works on the backward pass for the decreasing direction. Finally, for each value `v` we add the sums from both passes and subtract the over‑counted single‑element subsequence.

All operations are performed modulo **M = 1 000 000 007**.

---

## 📄 Code Implementations

### 1. Java (O(n) DP on values)

```java
import java.util.HashMap;

public class Solution {
    private static final int MOD = 1_000_000_007;

    public int sumOfConsecutiveSubsequences(int[] nums) {
        int maxVal = 0;
        for (int v : nums) maxVal = Math.max(maxVal, v);

        long[] cntInc = new long[maxVal + 2];
        long[] sumInc = new long[maxVal + 2];
        long[] cntDec = new long[maxVal + 3];
        long[] sumDec = new long[maxVal + 3];

        // Forward pass – increasing direction
        for (int v : nums) {
            long c = cntInc[v - 1] + 1;
            long s = sumInc[v - 1] + v * c;
            cntInc[v] = (cntInc[v] + c) % MOD;
            sumInc[v] = (sumInc[v] + s) % MOD;
        }

        // Backward pass – decreasing direction
        for (int i = nums.length - 1; i >= 0; --i) {
            int v = nums[i];
            long c = cntDec[v + 1] + 1;
            long s = sumDec[v + 1] + v * c;
            cntDec[v] = (cntDec[v] + c) % MOD;
            sumDec[v] = (sumDec[v] + s) % MOD;
        }

        long ans = 0;
        for (int v = 1; v <= maxVal; ++v) {
            ans = (ans + sumInc[v] + sumDec[v]) % MOD;
            // subtract double counted single element subsequence
            ans = (ans - v + MOD) % MOD;
        }
        return (int) ans;
    }
}
```

**Complexities**

* Time: `O(n + maxValue)` (≤ O(n) in practice)
* Memory: `O(maxValue)` (≈ 4 × 10⁵ longs → < 3 MB)

---

### 2. Python (O(n) DP with dictionaries)

```python
MOD = 1_000_000_007

def sum_of_consecutive_subsequences(nums):
    max_val = max(nums)
    cnt_inc = [0] * (max_val + 2)
    sum_inc = [0] * (max_val + 2)
    cnt_dec = [0] * (max_val + 3)
    sum_dec = [0] * (max_val + 3)

    # Forward pass
    for v in nums:
        c = cnt_inc[v - 1] + 1
        s = (sum_inc[v - 1] + v * c) % MOD
        cnt_inc[v] = (cnt_inc[v] + c) % MOD
        sum_inc[v] = (sum_inc[v] + s) % MOD

    # Backward pass
    for v in reversed(nums):
        c = cnt_dec[v + 1] + 1
        s = (sum_dec[v + 1] + v * c) % MOD
        cnt_dec[v] = (cnt_dec[v] + c) % MOD
        sum_dec[v] = (sum_dec[v] + s) % MOD

    ans = 0
    for v in range(1, max_val + 1):
        ans = (ans + sum_inc[v] + sum_dec[v]) % MOD
        ans = (ans - v + MOD) % MOD  # remove double counted single element
    return ans
```

---

### 3. C++ (O(n) DP with vector)

```cpp
#include <bits/stdc++.h>
using namespace std;

const int MOD = 1'000'000'007;

int sumOfConsecutiveSubsequences(vector<int>& nums) {
    int maxVal = *max_element(nums.begin(), nums.end());
    vector<long long> cntInc(maxVal + 2, 0), sumInc(maxVal + 2, 0);
    vector<long long> cntDec(maxVal + 3, 0), sumDec(maxVal + 3, 0);

    // Forward pass
    for (int v : nums) {
        long long c = cntInc[v - 1] + 1;
        long long s = (sumInc[v - 1] + 1LL * v * c) % MOD;
        cntInc[v] = (cntInc[v] + c) % MOD;
        sumInc[v] = (sumInc[v] + s) % MOD;
    }

    // Backward pass
    for (int i = nums.size() - 1; i >= 0; --i) {
        int v = nums[i];
        long long c = cntDec[v + 1] + 1;
        long long s = (sumDec[v + 1] + 1LL * v * c) % MOD;
        cntDec[v] = (cntDec[v] + c) % MOD;
        sumDec[v] = (sumDec[v] + s) % MOD;
    }

    long long ans = 0;
    for (int v = 1; v <= maxVal; ++v) {
        ans = (ans + sumInc[v] + sumDec[v]) % MOD;
        ans = (ans - v + MOD) % MOD;          // remove double counted single element
    }
    return static_cast<int>(ans);
}
```

---

## 📚 Blog Article – “The Good, The Bad, and The Ugly of Solving LeetCode 3299”

### Title  
**Sum of Consecutive Subsequences – The Good, The Bad, and The Ugly (O(n) DP on Values)**  

### Meta Description  
Learn how to crack LeetCode 3299 in interview‑ready time. We walk through the DP trick, pitfalls, and code in Java, Python, and C++. Get interview tips that recruiters love!

### Why This Matters  
If you’re prepping for a **software‑engineering interview** or looking to showcase your problem‑solving chops on your résumé, LeetCode 3299 is a *great talking point*. It tests:

* **DP insight** – you must see the *value* of a subsequence as a sum of a run of `±1` values.
* **Space‑time trade‑offs** – handling 10⁵ values efficiently.
* **Careful modulo arithmetic** – a classic “gotcha” for many candidates.

Below is a full guide that will keep interviewers impressed.

---

### 1. The Good – Elegant DP on Value Space

* **Linear time** – Once you think in terms of *values* rather than positions, the solution collapses to two passes.  
* **Intuitive recurrence** – `cnt[v] = cnt[v-1] + 1` and `sum[v] = sum[v-1] + v * cnt[v]`.  
  These are simple to derive and easy to remember.  
* **Low memory** – a vector of size `max(nums)+2` is tiny compared to the input.

**Take‑away**: If you can rewrite a complex “subsequence” problem into a recurrence on element values, you often unlock a linear solution.

---

### 2. The Bad – Common Pitfalls That Break Your Code

| Pitfall | Why it Happens | Fix |
|---------|----------------|-----|
| **Using `int` for sums** | `sumInc` can reach 10⁵ * 10⁵ * 10⁵ ≈ 10¹⁵ | Use `long long` (Java `long`, Python `int` is unbounded, C++ `long long`) |
| **Missing modulo** | Subtractions can go negative | Add `MOD` before `% MOD` |
| **Double counting single elements** | Increasing and decreasing passes both add `[x]` | Subtract `x` once at the end |
| **Indexing out of bounds** | `cntInc[v-1]` when `v==1` | Allocate size `maxVal+2` and start from `v-1 >= 0` |
| **Time‑outs on large `maxValue`** | If you allocate an array for 10⁹ values | Cap the size to `max(nums)+2` (≤ 10⁵) or use `unordered_map` |

---

### 3. The Ugly – When the Brute Force Idea Misleads

Many candidates start by enumerating all subsequences, hoping that pruning will help. Even after pruning, the branching factor remains huge. The “ugly” part is realizing that **you cannot iterate over subsequences** – you must iterate over **values**.  
When you finally figure out the DP, the code looks compact, but the real struggle is remembering the *plus‑1* terms for the new single‑element subsequence and correctly handling the decreasing direction.

---

### 📈 Complexity Recap (SEO Keywords)

| Language | Time | Space |
|----------|------|-------|
| Java | O(n) | O(10⁵) |
| Python | O(n) | O(10⁵) |
| C++ | O(n) | O(10⁵) |

*“O(n) DP on values”* and *“modulo 10⁹+7”* are the terms recruiters often ask about in coding interviews.

---

### 🎯 Interview Tips

1. **Sketch the recurrence** on a whiteboard. Draw a small example, write `cntInc[v]`, `sumInc[v]` on the board, and show how they’re updated.
2. **Explain the two passes**: forward for increasing, backward for decreasing. This demonstrates you can think about symmetry.
3. **Mention the modulo trick**: “All operations are done mod M, so we need to add `M` before taking `% M` to avoid negatives.”
4. **Show your code** – highlight the two loops. Interviewers love clean, self‑commented code.
5. **Talk about edge cases** – single element arrays, all elements equal, alternating patterns. This signals deep understanding.

---

### 💼 How to Use This Article in Your Job Hunt

* **SEO Keywords** – “Sum of Consecutive Subsequences”, “LeetCode 3299 solution”, “DP on values”, “Java DP interview”, “Python coding interview”, “C++ LeetCode solutions”, “software engineering interview tips”.
* **Social Media Hook** – “Cracked LeetCode 3299 in O(n) – here's the trick that made recruiters nod!”  
* **Call‑to‑Action** – Invite readers to drop a comment with their own solutions or ask for a pair‑programming session.

---

### Final Thought

The *Good* is the clean DP that runs in linear time.  
The *Bad* is the temptation to brute‑force or to over‑think the subsequence definition.  
The *Ugly* is the subtle off‑by‑one and modulo handling that trips up even seasoned engineers.  

Mastering this problem shows you can **abstract a seemingly combinatorial problem into a simple recurrence** – a skill recruiters look for in every senior software engineer. 🚀  

--- 

Happy coding, and best of luck on your next interview!