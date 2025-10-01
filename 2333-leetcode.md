---
title: LeetCode 2333. Minimum Sum of Squared Difference - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # ✅ 2333. Minimum Sum of Squared Difference – A Complete, SEO‑Optimized Guide  
*Solve the problem in **Java**, **Python**, and **C++** – plus a full blog article that covers the good, the bad, and the ugly, perfect for interview prep and boosting your résumé.*

---

## 📌 Problem Summary

- **Arrays** `nums1` and `nums2` (length `n`) contain non‑negative integers (`0 … 10⁵`).  
- **Operations**:  
  - `k1` times you may increment or decrement any element of `nums1` by `1`.  
  - `k2` times you may do the same for `nums2`.  
- The **sum of squared differences** is  

  \[
  \sum_{i=0}^{n-1}\bigl(nums1[i]-nums2[i]\bigr)^2
  \]

- **Goal**: minimise this sum after at most `k1 + k2` total operations.

**Constraints**

| n | 1 … 10⁵ | k1, k2 | 0 … 10⁹ | nums[i] | 0 … 10⁵ |
|---|---------|--------|--------|----------|---------|

---

## 📈 Why This Problem Is a Great Interview Question

| Good | Bad | Ugly |
|------|-----|------|
| ✔ **Math + Greedy** – tests understanding of how operations affect squared differences | ❌ **Large constraints** – naive `O(n*k)` is impossible | ❌ **Off‑by‑one pitfalls** – off‑by‑1 errors in diff manipulation |
| ✔ **Data Structures** – frequency array vs. priority queue vs. TreeMap | ❌ Requires careful handling of large `k` (up to 10⁹) | ❌ Edge cases where all differences become zero early |
| ✔ **Optimization** – `O(n + maxDiff)` is optimal | ❌ Needs intuition to see that you always reduce the *largest* difference | ❌ Implementation can be over‑engineered (too many data structures) |

---

## 🧠 High‑Level Algorithm (The “Good” Way)

1. **Count differences**  
   For each pair `(nums1[i], nums2[i])` compute `d = |nums1[i] - nums2[i]|`.  
   Store how many times each `d` occurs in a frequency array `cnt[d]`.  
   `maxDiff` = largest `d` seen.

2. **Check early termination**  
   If the sum of all differences (`totalDiff`) ≤ `k1 + k2`, you can zero out all differences → answer is `0`.

3. **Greedy reduction**  
   While we still have operations (`k > 0`), walk from `maxDiff` down to `1`:
   - For each `d`, we can reduce all its occurrences by 1 (moving from `d` to `d-1`) using `cnt[d]` operations.  
   - If `cnt[d] ≤ k`, we do it all: `cnt[d-1] += cnt[d]`, `k -= cnt[d]`, `cnt[d] = 0`.  
   - Otherwise we can only reduce `k` of them: `cnt[d] -= k`, `cnt[d-1] += k`, `k = 0`.

4. **Compute final sum**  
   `ans = Σ (d² * cnt[d])`.

**Why greedy works?**  
Reducing the largest current difference always gives the biggest drop in `d²` → `f(d) = d²` is strictly convex, so moving a step from `d` to `d-1` gives more benefit than from a smaller `d`. Therefore, we never lose by focusing on the maximum.

---

## 📊 Complexity Analysis

| Step | Complexity |
|------|------------|
| Counting diffs | `O(n)` |
| Greedy loop | `O(maxDiff)` (≤ 10⁵) |
| Final sum | `O(maxDiff)` |
| **Total** | `O(n + maxDiff)` time, `O(maxDiff)` space |

Both `maxDiff` and the frequency array fit comfortably in memory (`≤ 100 001` integers).

---

## ⚙️ Implementation

Below are clean, self‑contained solutions in **Java**, **Python**, and **C++**.  
All codes use the frequency‑array greedy strategy (no heap needed) and are ready to paste into LeetCode.

---

### 1. Java

```java
import java.util.*;

class Solution {
    public long minSumSquareDiff(int[] nums1, int[] nums2, int k1, int k2) {
        final int MAX = 100_000;               // maximum possible difference
        long[] cnt = new long[MAX + 1];        // frequency of each difference
        long totalDiff = 0;                    // sum of all differences
        int maxDiff = 0;                       // largest difference seen

        // Count differences
        for (int i = 0; i < nums1.length; i++) {
            int d = Math.abs(nums1[i] - nums2[i]);
            if (d == 0) continue;
            cnt[d]++;
            totalDiff += d;
            if (d > maxDiff) maxDiff = d;
        }

        long ops = (long) k1 + k2;              // total operations available
        if (totalDiff <= ops) return 0;        // all differences can be zeroed

        // Greedy reduction from maxDiff downwards
        for (int d = maxDiff; d > 0 && ops > 0; d--) {
            if (cnt[d] == 0) continue;
            if (cnt[d] <= ops) {                // reduce all
                cnt[d - 1] += cnt[d];
                ops -= cnt[d];
                cnt[d] = 0;
            } else {                            // reduce only part
                cnt[d] -= ops;
                cnt[d - 1] += ops;
                ops = 0;
            }
        }

        // Compute final sum of squares
        long ans = 0;
        for (int d = 0; d <= maxDiff; d++) {
            if (cnt[d] > 0) ans += cnt[d] * (long)d * d;
        }
        return ans;
    }
}
```

---

### 2. Python

```python
class Solution:
    def minSumSquareDiff(
        self, nums1: List[int], nums2: List[int], k1: int, k2: int
    ) -> int:
        MAX = 100_000
        cnt = [0] * (MAX + 1)
        total_diff = 0
        max_diff = 0

        # Count differences
        for a, b in zip(nums1, nums2):
            d = abs(a - b)
            if d == 0:
                continue
            cnt[d] += 1
            total_diff += d
            if d > max_diff:
                max_diff = d

        ops = k1 + k2
        if total_diff <= ops:
            return 0

        # Greedy reduction
        for d in range(max_diff, 0, -1):
            if cnt[d] == 0:
                continue
            if cnt[d] <= ops:
                cnt[d - 1] += cnt[d]
                ops -= cnt[d]
                cnt[d] = 0
            else:
                cnt[d] -= ops
                cnt[d - 1] += ops
                ops = 0
                break

        # Final sum of squares
        return sum(c * d * d for d, c in enumerate(cnt))
```

---

### 3. C++

```cpp
class Solution {
public:
    long long minSumSquareDiff(vector<int>& nums1, vector<int>& nums2,
                               int k1, int k2) {
        const int MAX = 100000;
        vector<long long> cnt(MAX + 1, 0);
        long long totalDiff = 0;
        int maxDiff = 0;

        // Count differences
        for (size_t i = 0; i < nums1.size(); ++i) {
            int d = abs(nums1[i] - nums2[i]);
            if (d == 0) continue;
            cnt[d]++;
            totalDiff += d;
            if (d > maxDiff) maxDiff = d;
        }

        long long ops = (long long)k1 + k2;
        if (totalDiff <= ops) return 0;

        // Greedy reduction from maxDiff downwards
        for (int d = maxDiff; d > 0 && ops > 0; --d) {
            if (!cnt[d]) continue;
            if (cnt[d] <= ops) {                  // reduce all
                cnt[d - 1] += cnt[d];
                ops -= cnt[d];
                cnt[d] = 0;
            } else {                              // reduce only part
                cnt[d] -= ops;
                cnt[d - 1] += ops;
                ops = 0;
            }
        }

        // Final sum of squares
        long long ans = 0;
        for (int d = 0; d <= maxDiff; ++d) {
            if (cnt[d]) ans += cnt[d] * 1LL * d * d;
        }
        return ans;
    }
};
```

---

## 📚 Blog Article – SEO‑Optimised Interview Deep‑Dive

> **Target Keywords**: *LeetCode 2333*, *Minimum Sum of Squared Difference*, *array difference optimisation*, *greedy algorithm interview*, *job interview problem*, *coding interview tips*  

### 1. Introduction

> The LeetCode problem **2333 – Minimum Sum of Squared Difference** is a staple in technical interviews. It tests a candidate’s ability to think mathematically, use greedy logic, and optimise for huge input sizes. In this post we walk through the problem from start to finish, deliver production‑ready code in three languages, and reveal interview‑winning tips.

### 2. Problem Statement (in Plain English)

> You are given two integer arrays `nums1` and `nums2` and two counters `k1`, `k2`. In at most `k1 + k2` moves you may increment or decrement any element of either array by `1`. You need to minimise the sum of squared differences between corresponding elements.  

> **Why it matters:** The problem forces you to see that *one step of an operation has a different impact on `d²` depending on the current value of `d`*. This is the key insight every recruiter wants to test.

### 3. Why It’s a “Good” Interview Question

| ✅ What recruiters are looking for | ❌ Common pitfalls |
|------------------------------------|--------------------|
| • **Greedy + Convexity** – convex cost functions mean reducing the largest value gives the greatest benefit. | • **Scale** – naive `O(n*k)` is impossible; the solution must be linear in `n`. |
| • **Frequency array vs. Heap** – shows knowledge of low‑memory, high‑speed data structures. | • **Large `k`** – up to `10⁹`. You need to avoid overflow and early‑break logic. |
| • **Edge‑Case handling** – zeroing all differences early, off‑by‑1 mistakes. | • **Complexity** – many interviewees write a binary search or BFS that overcomplicates things. |

### 4. The “Good” Approach – Frequency Array + Greedy

1. **Build a frequency table** for the absolute differences (`0 … 100 000`).  
2. **If total diff ≤ ops** → answer is `0`.  
3. **Walk down from the maximum diff**; each time we reduce a bucket of size `d` to `d-1` we use `cnt[d]` operations.  
4. **Compute the final sum** by multiplying each difference squared by its count.

> **Why it’s optimal:** The function `f(d) = d²` is convex, so the marginal benefit of decreasing a larger `d` is always higher than for a smaller one. Greedy is therefore proven optimal.

### 5. “Bad” or “Ugly” Implementations You Should Avoid

- **Priority Queue Approach**  
  *Works fine but over‑engineers the problem.*  
  Each operation pops the maximum diff, decreases it, and pushes it back. Time complexity `O(n log n)` (still acceptable) but memory overhead and constant factors are higher.

- **TreeMap/Multiset Approach**  
  Useful in languages without a fixed difference range but adds log‑factor overhead.

- **Two‑Pointer or Binary Search on `k`**  
  A “try‑and‑error” solution that binary searches the number of operations needed to reach a given threshold. It’s harder to prove correctness and easier to bug.

### 6. Code Snippets (Java, Python, C++)

*See the code section above for clean implementations. Highlight the use of a simple frequency array and a one‑pass greedy reduction.*

### 7. Interview Tips

| Tip | Explanation |
|-----|-------------|
| **Start with a brute‑force sketch** – then optimise | Helps you catch the intuition behind the convexity of `d²`. |
| **Explain early termination** – `totalDiff ≤ ops` → 0 | Shows you’re considering edge cases before doing heavy lifting. |
| **Talk about time complexity** – `O(n + maxDiff)` | Recruiters love candidates who discuss asymptotics. |
| **Write test cases** – zero differences, all differences same, `k = 0` | Guarantees your solution is bullet‑proof. |

### 8. Conclusion

> The **Minimum Sum of Squared Difference** is a classic LeetCode interview problem that blends mathematics, greedy strategy, and careful handling of large `k`. By mastering this solution, you’ll demonstrate the skills recruiters value: *clean thinking, optimal data‑structure choice, and robust coding under constraints.*

---

## 📌 Ready‑to‑Use Code Summary

| Language | Function Signature | Complexity | Notes |
|----------|---------------------|------------|-------|
| **Java** | `long minSumSquareDiff(int[] nums1, int[] nums2, int k1, int k2)` | `O(n + maxDiff)` | Uses `long` for safety, frequency array. |
| **Python** | `def minSumSquareDiff(self, nums1, nums2, k1, k2)` | `O(n + maxDiff)` | Straightforward list of size 100 001. |
| **C++** | `long long minSumSquareDiff(vector<int>& nums1, vector<int>& nums2, int k1, int k2)` | `O(n + maxDiff)` | `vector<long long>` for counts. |

---  

### 🎯 SEO Keywords (to sprinkle in your résumé or LinkedIn posts)

- LeetCode 2333
- Minimum Sum of Squared Difference
- array difference optimization
- greedy algorithm interview
- priority queue vs. frequency array
- job interview coding problem
- convex cost functions
- efficient array manipulation
- technical interview prep
- software engineering interview

Use these tags in your LinkedIn article, GitHub README, or blog to attract recruiters searching for *“LeetCode solutions”* or *“array optimization problems”*.  

---  

### 🚀 Final Thought

You now have:

1. A crystal‑clear algorithm that **always** produces the minimum sum.  
2. Three production‑ready code snippets in **Java**, **Python**, and **C++**.  
3. A **blog post** that covers every angle of the problem – from intuition to pitfalls – and is built to climb Google rankings for interview‑related searches.

**Happy coding & good luck on your next interview!**