---
title: LeetCode 1477. Find Two Non-overlapping Sub-arrays Each With Target Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1477 – Find Two Non‑overlapping Sub‑arrays Each With Target Sum  
**Time Complexity:**  `O(n)`  
**Space Complexity:** `O(n)`

Below you’ll find clean, production‑ready solutions in **Java**, **Python**, and **C++** that solve LeetCode 1477 in linear time.  
After the code we provide a comprehensive, SEO‑optimised blog post that explains the problem, the pitfalls, and the elegant O(n) trick that makes this a *must‑know* interview question.

---

## 1️⃣ Java Solution

```java
import java.util.*;

class Solution {

    public int minSumOfLengths(int[] arr, int target) {
        int n = arr.length;
        // minLenFromLeft[i] – shortest sub‑array ending at or before i
        int[] left = new int[n];
        // minLenFromRight[i] – shortest sub‑array starting at or after i
        int[] right = new int[n];

        // ---------- First pass : left → right ----------
        int minLen = Integer.MAX_VALUE;
        int start = 0, sum = 0;
        for (int end = 0; end < n; end++) {
            sum += arr[end];
            while (sum > target) {
                sum -= arr[start++];
            }
            if (sum == target) {
                minLen = Math.min(minLen, end - start + 1);
            }
            left[end] = minLen;          // store the best length up to `end`
        }

        // ---------- Second pass : right → left ----------
        minLen = Integer.MAX_VALUE;
        start = n - 1;
        sum = 0;
        for (int end = n - 1; end >= 0; end--) {
            sum += arr[end];
            while (sum > target) {
                sum -= arr[start--];
            }
            if (sum == target) {
                minLen = Math.min(minLen, start - end + 1);
            }
            right[end] = minLen;        // store the best length from `end`
        }

        // ---------- Combine ----------
        int best = Integer.MAX_VALUE;
        for (int i = 0; i < n - 1; i++) {
            if (left[i] < Integer.MAX_VALUE && right[i + 1] < Integer.MAX_VALUE) {
                best = Math.min(best, left[i] + right[i + 1]);
            }
        }

        return best == Integer.MAX_VALUE ? -1 : best;
    }
}
```

> **Why it works**  
> * Sliding window finds every sub‑array whose sum equals `target` in O(n).  
> * We keep the shortest length that ends (or starts) at each index.  
> * The two arrays `left` and `right` encode the “best” sub‑array for the left side and the right side of any split point.  
> * The final loop simply tries every split point – the optimum pair is found in O(n).

---

## 2️⃣ Python Solution

```python
from typing import List

class Solution:
    def minSumOfLengths(self, arr: List[int], target: int) -> int:
        n = len(arr)

        # left[i] = shortest length of sub‑array ending at or before i
        left = [float('inf')] * n
        # right[i] = shortest length of sub‑array starting at or after i
        right = [float('inf')] * n

        # ---- left → right ----
        s = 0
        l = 0
        min_len = float('inf')
        for r in range(n):
            s += arr[r]
            while s > target:
                s -= arr[l]
                l += 1
            if s == target:
                min_len = min(min_len, r - l + 1)
            left[r] = min_len

        # ---- right → left ----
        s = 0
        r = n - 1
        min_len = float('inf')
        for l in range(n - 1, -1, -1):
            s += arr[l]
            while s > target:
                s -= arr[r]
                r -= 1
            if s == target:
                min_len = min(min_len, r - l + 1)
            right[l] = min_len

        # ---- combine ----
        ans = float('inf')
        for i in range(n - 1):
            if left[i] < float('inf') and right[i + 1] < float('inf'):
                ans = min(ans, left[i] + right[i + 1])

        return -1 if ans == float('inf') else ans
```

---

## 3️⃣ C++ Solution

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minSumOfLengths(vector<int>& arr, int target) {
        int n = arr.size();
        vector<int> left(n, INT_MAX), right(n, INT_MAX);

        // left → right
        int l = 0, sum = 0, best = INT_MAX;
        for (int r = 0; r < n; ++r) {
            sum += arr[r];
            while (sum > target) sum -= arr[l++];
            if (sum == target) best = min(best, r - l + 1);
            left[r] = best;
        }

        // right → left
        int r = n - 1, best2 = INT_MAX;
        sum = 0;
        for (int l2 = n - 1; l2 >= 0; --l2) {
            sum += arr[l2];
            while (sum > target) sum -= arr[r--];
            if (sum == target) best2 = min(best2, r - l2 + 1);
            right[l2] = best2;
        }

        // combine
        int ans = INT_MAX;
        for (int i = 0; i < n - 1; ++i)
            if (left[i] != INT_MAX && right[i + 1] != INT_MAX)
                ans = min(ans, left[i] + right[i + 1]);

        return ans == INT_MAX ? -1 : ans;
    }
};
```

---

# 📚 Blog Article – *The Good, The Bad, The Ugly*  
**Title**: *Mastering LeetCode 1477: Find Two Non‑Overlapping Sub‑arrays With Target Sum – The Good, The Bad, The Ugly*

---

## Introduction  

When interviewing for **software‑engineering** roles, LeetCode 1477 “Find Two Non‑overlapping Sub‑arrays Each With Target Sum” is a classic problem that tests a candidate’s ability to combine **prefix sums**, **sliding windows**, and **dynamic programming** in a single pass.  

In this article we’ll:  

1. **State the problem** in plain language.  
2. Walk through **naïve** attempts and why they fail.  
3. Present the **optimal O(n)** solution with a clear walkthrough.  
4. Show **full implementations** in Java, Python, and C++.  
5. Discuss **common pitfalls** and interview tips.  

Feel free to copy‑paste the code snippets, run them on your local machine, and add them to your portfolio.

---

## Problem Statement (Simplified)

You’re given an array `arr` of positive integers and a target sum `t`.  
Find two *non‑overlapping* sub‑arrays such that the sum of each sub‑array equals `t`.  
Return the *minimum* total length of those two sub‑arrays.  
If it’s impossible, return `-1`.

> **Constraints**  
> • `1 ≤ arr.length ≤ 10⁵`  
> • `1 ≤ arr[i] ≤ 10³`  
> • `1 ≤ t ≤ 10⁸`

---

## 1️⃣ The Good – Intuitive Ideas

| Approach | Complexity | Pros | Cons |
|----------|------------|------|------|
| **Brute‑Force** (two nested loops + another nested loop) | `O(n³)` | Simple to implement | Too slow for `n=10⁵` |
| **Two‑Pointer (sliding window)** | `O(n²)` | Handles positive numbers nicely | Still too slow |
| **Prefix Sum + HashMap** | `O(n)` | Linear time | Extra `O(n)` memory, subtle index handling |

The *good* part is that with **positive integers** we can exploit the *monotonic* property of sums: expanding a window only increases the sum, shrinking only decreases it. This is the key to a **two‑pointer** linear solution.

---

## 2️⃣ The Bad – Common Pitfalls

| Mistake | Why it fails |
|---------|--------------|
| **Treating “best sub‑array to the left” as a single global variable** | We need the *shortest* sub‑array that ends *anywhere* on the left of a split point, not just the last one. |
| **Not propagating best lengths** | After finding a sub‑array ending at `i`, you must remember that the best up to `i` might be even shorter at a previous index. |
| **Using two independent scans without merging** | Splitting the array into two parts and solving each independently misses cross‑split optimisations. |
| **Over‑complicating with dynamic programming states** | The state space explodes; a simple two‑pass prefix/suffix approach suffices. |

---

## 3️⃣ The Ugly – Over‑Engineered Solutions

Some candidates add binary search, segment trees, or even `unordered_map` over prefix sums for each possible end index.  
While those are *technically correct*, they are:

- **Hard to read**  
- **Hard to debug**  
- **Hard to explain in an interview**  

When an interview question allows a linear, clean solution, the *ugly* approaches will only hurt your score.

---

## 4️⃣ Optimal O(n) Solution – Sliding Window + Prefix/Suffix

### 4.1 Overview

1. **Left → Right Pass**  
   * For each index `i`, find the shortest sub‑array that ends at or before `i` and sums to `t`.  
   * Store this best length in array `left[i]`.  

2. **Right → Left Pass**  
   * Similarly, for each index `i`, compute the shortest sub‑array that starts at or after `i`.  
   * Store in array `right[i]`.  

3. **Merge**  
   * For every possible split point `i` (`0 ≤ i < n‑1`), combine `left[i]` and `right[i+1]`.  
   * The minimum of `left[i] + right[i+1]` is the answer.

### 4.2 Why It Works

- The **sliding window** guarantees we check *every* sub‑array whose sum is exactly `target`.  
- The `left` array *propagates* the shortest length seen so far, making every split point aware of the best left sub‑array.  
- The `right` array does the same for the right side.  
- Since every sub‑array is discovered in *O(n)*, and the merge step is another *O(n)*, the total runtime is *linear*.

### 4.3 Pseudocode

```
n = length(arr)
left  = array of INF[n]
right = array of INF[n]

# Left pass
sum = 0; start = 0; best = INF
for end in 0..n-1:
    sum += arr[end]
    while sum > target:
        sum -= arr[start]; start++
    if sum == target:
        best = min(best, end - start + 1)
    left[end] = best

# Right pass
sum = 0; end = n-1; best = INF
for start in n-1..0:
    sum += arr[start]
    while sum > target:
        sum -= arr[end]; end--
    if sum == target:
        best = min(best, end - start + 1)
    right[start] = best

# Merge
answer = INF
for i in 0..n-2:
    if left[i] < INF and right[i+1] < INF:
        answer = min(answer, left[i] + right[i+1])

return answer == INF ? -1 : answer
```

---

## 5️⃣ Full Implementations

We already presented the full code in the previous sections.  
Feel free to drop them into your repository; we recommend adding **unit tests** for the following cases:

1. `arr = [1,2,2,1,2,1,1,1]`, `t=3` → `answer = 3` (e.g., `[1,2]` and `[2,1]`).  
2. `arr = [1,1,1,1,1,1,1]`, `t=7` → `answer = -1` (no two disjoint sub‑arrays).  
3. Edge case: `arr` length 1 → always `-1`.  

---

## 6️⃣ Interview Tips

| Tip | How to use it |
|-----|--------------|
| **Explain your plan first** | Outline “two scans + combine” before writing code. |
| **Use a dummy value** (`INF`) to denote “no sub‑array found yet.” | Avoids off‑by‑one errors. |
| **Ask clarifying questions** | Confirm that all numbers are positive (guaranteed by constraints). |
| **Time‑complexity check** | “`O(n)` will run in about 0.01 s for `n=10⁵` on a typical laptop.” |
| **Discuss edge cases** | Show you considered empty splits and overlapping conditions. |

---

## 7️⃣ Wrap‑Up

LeetCode 1477 is a perfect illustration of how a *simple* data structure (two arrays) and a *simple* algorithm (sliding window) can solve a seemingly complex problem in linear time.  

**Key takeaways**:  

- Remember to **propagate** the best sub‑array length to all indices.  
- A **two‑pass** approach (prefix + suffix) is often the cleanest.  
- Keep the code **readable** and **well‑commented**—that’s what interviewers look for.  

Happy coding, and good luck with your next interview! 🚀

---

## References

- Official LeetCode problem: https://leetcode.com/problems/find-two-non-overlapping-sub-arrays-each-with-target-sum/
- Solutions and discussion on LeetCode discuss forum.  
- Sliding window tutorial: https://leetcode.com/articles/two-sum-ii/
- Prefix sum technique: https://leetcode.com/articles/shortest-subarray-with-sum-at-least-k/

--- 

**Happy Interviewing!** 🎯

--- 

*Author: [Your Name]* – *Software Engineer | Algorithm Enthusiast*  
*Follow me on GitHub / LinkedIn for more algorithmic content.*


--- 

*Word Count: ~1300*  
*Time to Read: ~8 minutes*  
*Keywords: LeetCode, algorithm, interview, sliding window, prefix sum, Java, Python, C++*


--- 

Enjoy!