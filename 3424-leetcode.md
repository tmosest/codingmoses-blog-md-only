---
title: LeetCode 3424. Minimum Cost to Make Arrays Identical - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Minimum Cost to Make Arrays Identical  
### LeetCode 3424 – A “Good, Bad & Ugly” Deep‑Dive  
*(Java, C++, Python – ready for your interview repo)*  

---

### TL;DR  
For any array `arr` you can either  
1. **Do nothing** – just adjust every element to match `brr` at the same indices.  
2. **Pay a one‑time cost `k`** – split `arr` into arbitrary contiguous blocks, reorder the blocks, then adjust.

The optimal cost is  

```
min( Σ |arr[i] – brr[i]| ,
     Σ |sorted(arr)[i] – sorted(brr)[i]| + k )
```

The first term is the “no‑reorder” cost, the second term is the “reorder‑and‑adjust” cost.  
The trick: after a free block‑reorder you’re free to pair elements in the best way – sort both arrays and pair by index.  

Complexity: **O(n log n)** time (because of sorting) and **O(1)** auxiliary space.  

---

## 1. Problem Recap

| Field | Value |
|-------|-------|
| Problem | Minimum Cost to Make Arrays Identical |
| LeetCode ID | 3424 |
| Difficulty | Medium |
| Input | `int[] arr, int[] brr, long k` (length `n` up to `10⁵`) |
| Operation 1 | Split `arr` into any number of *contiguous* sub‑arrays, then reorder those sub‑arrays arbitrarily. Cost = `k`. |
| Operation 2 | Pick an element of `arr` and add/subtract a positive integer `x`. Cost = `x`. |
| Goal | Turn `arr` into `brr` with minimum total cost. |

---

## 2. The “Good” – Intuition & Correctness

### 2.1 Why a Single Reorder Suffices  
If you split into `n` single‑element blocks you can permute the whole array in any order.  
So the “reorder” operation gives you **full freedom to choose any permutation** of `arr` for a flat cost `k`.

### 2.2 Two Extremes

| Option | What happens | Cost |
|--------|--------------|------|
| **Never reorder** | Keep indices fixed, just adjust values | `Σ|arr[i] – brr[i]|` |
| **Always reorder** | After paying `k`, you can arrange `arr` optimally | `k + min adjustment` |

Because `k` is flat, you only need to decide *whether* to reorder.  

### 2.3 Optimal Adjustment After Reorder  
Once you may rearrange `arr` freely, the problem reduces to a classic **minimum‑weight perfect matching** with the cost `|x – y|`.  
For absolute differences, the optimal matching is obtained by sorting both arrays and pairing the `i`‑th elements.  
Proof sketch: If two pairs cross, swapping them reduces the total absolute difference (standard exchange argument).

So after the reorder:

```
adj_cost = Σ |sorted(arr)[i] – sorted(brr)[i]|
total_cost = k + adj_cost
```

Take the minimum of the two scenarios.

---

## 3. The “Bad” – Common Pitfalls

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| **Assuming any permutation costs `k`** | The operation costs `k` *once*, not per swap. | Treat it as a single “free reorder” operation. |
| **Using a greedy “closest” pairing** | Greedy can be suboptimal for absolute differences. | Sort both arrays first. |
| **Ignoring overflow** | `k` can be up to `2·10¹⁰`. Sum of differences can exceed 32‑bit. | Use 64‑bit (`long`/`long long`). |
| **Not handling `k == 0`** | You might incorrectly skip reorder when it would be free. | Compute both cases regardless of `k`. |
| **Not sorting the right arrays** | Sorting only `arr` or `brr` is insufficient. | Sort *both* and match by index. |

---

## 4. The “Ugly” – Edge Cases & Variations

| Edge | What to watch |
|------|---------------|
| **Equal arrays** | Cost is zero; both scenarios yield zero. |
| **Large negative numbers** | Sorting works fine; absolute difference handles signs. |
| **`k` very large** | It might be cheaper to never reorder. |
| **Very small `n`** | Brute‑force matching still works but is unnecessary; the formula holds. |
| **Multiple reorder operations** | The problem statement allows any number, but since cost is flat, doing more than one never helps. |

---

## 5. Implementation

Below are clean, ready‑to‑paste implementations for **Java**, **C++**, and **Python**.  
All use the same core logic described above.

### 5.1 Java

```java
import java.util.Arrays;

public class Solution {
    public long minCost(int[] arr, int[] brr, long k) {
        // Cost without any reorder
        long noReorder = 0;
        for (int i = 0; i < arr.length; i++) {
            noReorder += Math.abs((long)arr[i] - (long)brr[i]);
        }

        // Cost after paying k and optimally matching sorted arrays
        int[] aCopy = arr.clone();
        int[] bCopy = brr.clone();
        Arrays.sort(aCopy);
        Arrays.sort(bCopy);

        long reorder = k;      // one-time cost
        for (int i = 0; i < aCopy.length; i++) {
            reorder += Math.abs((long)aCopy[i] - (long)bCopy[i]);
        }

        return Math.min(noReorder, reorder);
    }
}
```

### 5.2 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long minCost(vector<int>& arr, vector<int>& brr, long long k) {
        long long noReorder = 0;
        for (size_t i = 0; i < arr.size(); ++i)
            noReorder += llabs((long long)arr[i] - (long long)brr[i]);

        vector<int> a = arr, b = brr;
        sort(a.begin(), a.end());
        sort(b.begin(), b.end());

        long long reorder = k;
        for (size_t i = 0; i < a.size(); ++i)
            reorder += llabs((long long)a[i] - (long long)b[i]);

        return min(noReorder, reorder);
    }
};
```

### 5.3 Python

```python
class Solution:
    def minCost(self, arr: List[int], brr: List[int], k: int) -> int:
        # No reorder cost
        no_reorder = sum(abs(a - b) for a, b in zip(arr, brr))

        # Reorder cost (sort both lists)
        sorted_a = sorted(arr)
        sorted_b = sorted(brr)
        reorder = k + sum(abs(a - b) for a, b in zip(sorted_a, sorted_b))

        return min(no_reorder, reorder)
```

> **Tip:** Use `int` in Python because it automatically handles big integers; in Java/C++ use `long`/`long long`.

---

## 6. Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Compute no‑reorder cost | **O(n)** | **O(1)** |
| Sort `arr` | **O(n log n)** | **O(1)** (in‑place, or **O(n)** if you clone) |
| Compute reorder cost | **O(n)** | **O(1)** |
| **Total** | **O(n log n)** | **O(1)** (excluding the input arrays) |

This satisfies the limits (`n ≤ 10⁵`) comfortably.

---

## 7. Why Recruiters Love This Solution

| Interview Signal | What the recruiter is looking for |
|-------------------|------------------------------------|
| *Two‑case decision* | Shows you understand the trade‑off between “free” global actions and per‑element costs. |
| *Sorting + matching* | Demonstrates knowledge of classic greedy proofs (exchange argument). |
| *64‑bit arithmetic* | Highlights attention to detail and handling of large numbers. |
| *Clean code* | Easier to review, unit‑test, and discuss on the spot. |

Include this snippet in your GitHub “solutions” folder and be ready to explain the intuition in a 5‑minute “talk‑through”.

---

## 7. Sample Tests

```text
Input: arr = [1,2,3], brr = [2,4,3], k = 3
Output: 4

Input: arr = [1,2,3], brr = [1,2,3], k = 0
Output: 0

Input: arr = [1], brr = [2], k = 5
Output: 1   (never reorder, cheaper than paying 5)
```

> **Run your own tests** – add random arrays, negative numbers, and extremes of `k`.

---

## 7. Take‑away for Your Portfolio

1. **Add a comment** at the top of the file with the LeetCode problem ID and a short description.  
2. **Export the three implementations** into separate files (`Solution.java`, `Solution.cpp`, `solution.py`).  
3. **Include a README** that shows your reasoning and links to this blog post.  
4. **Explain during the interview** that the key observation is the *flat* cost of a reorder, which turns the problem into a “sort & pair” puzzle.  
5. **Highlight the proof** that sorting is optimal for absolute differences – recruiters love that short argument.

---

## 7. Wrap‑Up

> **“The Good”** – A surprisingly simple O(n log n) solution that runs in milliseconds.  
> **The “Bad”** – A handful of subtle mistakes that can trip up even experienced coders.  
> **The “Ugly”** – Edge cases and variations that test your attention to detail.

Master this problem, and you’ll have a concise, elegant answer ready for **any coding interview** (especially data‑engineering roles where array manipulation and greedy proofs are common).  

Happy coding! 🚀