---
title: LeetCode 801. Minimum Swaps To Make Sequences Increasing - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧩 LeetCode 801 – Minimum Swaps to Make Sequences Increasing  
## A Complete Guide (Java | Python | C++) + Blog Post  
> **Keywords**: LeetCode 801, Minimum Swaps, DP, interview algorithm, Java solution, Python solution, C++ solution, job interview, coding interview, dynamic programming

---

## 1. Problem Recap

| | |
|---|---|
| **Problem** | Minimum Swaps to Make Sequences Increasing |
| **Difficulty** | Hard |
| **Input** | Two integer arrays `nums1` & `nums2` of equal length `n` (2 ≤ n ≤ 10⁵) |
| **Operation** | Swap `nums1[i]` with `nums2[i]` **once** per index. |
| **Goal** | Minimum number of swaps to make **both** sequences strictly increasing. |
| **Guarantee** | The input always admits a solution. |

> *Example*  
> `nums1 = [1,3,5,4]`  
> `nums2 = [1,2,3,7]`  
> → swap at `i = 3` → `nums1 = [1,3,5,7]`, `nums2 = [1,2,3,4]` (1 swap).

---

## 2. Intuition

For each position `i`, there are two possibilities:

1. **Do not swap** at `i`.  
2. **Swap** at `i`.

Whether a choice is valid depends only on the *previous* choice:

- If we **did not swap** at `i‑1`, then the previous values are `nums1[i-1]` and `nums2[i-1]`.
- If we **swapped** at `i‑1`, the previous values are `nums2[i-1]` and `nums1[i-1]`.

So we can keep track of two DP states:

- `keep[i]` – minimum swaps up to index `i` **if we keep** (do not swap) at `i`.
- `swap[i]` – minimum swaps up to index `i` **if we swap** at `i`.

Because we only ever look at `i-1`, we can compress the DP to **O(1) space**.

---

## 3. Recurrence

Let `a = nums1[i-1]`, `b = nums2[i-1]`, `c = nums1[i]`, `d = nums2[i]`.

```
keep[i] = min(
    keep[i-1] if a < c && b < d,        // keep keeps increasing
    swap[i-1] if a < c && b < d + 1,    // swapped previous, keep current
)

swap[i] = min(
    keep[i-1] + 1 if a < d && b < c,    // keep previous, swap current
    swap[i-1] + 1 if a < d && b < c,    // swapped previous, swap current
)
```

**Base case** (`i = 0`):

```
keep[0] = 0         // no swap needed at first index
swap[0] = 1         // swap at first index
```

The answer is `min(keep[n-1], swap[n-1])`.

---

## 4. Optimized O(1) Space Implementation

We keep only two variables:

```
keep = 0         // dp[0] for keep
swap = 1         // dp[0] for swap

for i = 1 .. n-1:
    newKeep = INF
    newSwap = INF
    ...
    keep = newKeep
    swap = newSwap
```

`INF` can be `n` (max swaps possible).

---

## 5. Code in Three Languages

### 5.1 Java

```java
/**
 * LeetCode 801. Minimum Swaps to Make Sequences Increasing
 * O(n) time, O(1) space
 */
class Solution {
    public int minSwap(int[] nums1, int[] nums2) {
        int n = nums1.length;
        int keep = 0;   // no swap at position 0
        int swap = 1;   // swap at position 0

        for (int i = 1; i < n; i++) {
            int newKeep = Integer.MAX_VALUE;
            int newSwap = Integer.MAX_VALUE;

            // Keep at i
            if (nums1[i - 1] < nums1[i] && nums2[i - 1] < nums2[i]) {
                newKeep = Math.min(newKeep, keep);
            }
            // Swap at i
            if (nums1[i - 1] < nums2[i] && nums2[i - 1] < nums1[i]) {
                newSwap = Math.min(newSwap, keep + 1);
            }

            // Keep at i but swapped at i-1
            if (nums1[i - 1] < nums1[i] && nums2[i - 1] < nums2[i]) {
                newKeep = Math.min(newKeep, swap);
            }
            // Swap at i with swapped at i-1
            if (nums1[i - 1] < nums2[i] && nums2[i - 1] < nums1[i]) {
                newSwap = Math.min(newSwap, swap + 1);
            }

            keep = newKeep;
            swap = newSwap;
        }

        return Math.min(keep, swap);
    }
}
```

### 5.2 Python

```python
"""
LeetCode 801. Minimum Swaps to Make Sequences Increasing
O(n) time, O(1) space
"""

class Solution:
    def minSwap(self, nums1: list[int], nums2: list[int]) -> int:
        n = len(nums1)
        keep, swap = 0, 1   # dp[0]

        for i in range(1, n):
            new_keep = float('inf')
            new_swap = float('inf')

            # Keep current, keep previous
            if nums1[i-1] < nums1[i] and nums2[i-1] < nums2[i]:
                new_keep = min(new_keep, keep)

            # Keep current, swap previous
            if nums1[i-1] < nums1[i] and nums2[i-1] < nums2[i]:
                new_keep = min(new_keep, swap)

            # Swap current, keep previous
            if nums1[i-1] < nums2[i] and nums2[i-1] < nums1[i]:
                new_swap = min(new_swap, keep + 1)

            # Swap current, swap previous
            if nums1[i-1] < nums2[i] and nums2[i-1] < nums1[i]:
                new_swap = min(new_swap, swap + 1)

            keep, swap = new_keep, new_swap

        return min(keep, swap)
```

### 5.3 C++

```cpp
/**
 * LeetCode 801. Minimum Swaps to Make Sequences Increasing
 * O(n) time, O(1) space
 */
class Solution {
public:
    int minSwap(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size();
        int keep = 0;   // dp[0] when we keep the first element
        int swap = 1;   // dp[0] when we swap the first element

        for (int i = 1; i < n; ++i) {
            int newKeep = INT_MAX, newSwap = INT_MAX;

            // keep current, keep previous
            if (nums1[i-1] < nums1[i] && nums2[i-1] < nums2[i]) {
                newKeep = min(newKeep, keep);
                newKeep = min(newKeep, swap);
            }
            // swap current, keep or swap previous
            if (nums1[i-1] < nums2[i] && nums2[i-1] < nums1[i]) {
                newSwap = min(newSwap, keep + 1);
                newSwap = min(newSwap, swap + 1);
            }

            keep = newKeep;
            swap = newSwap;
        }
        return min(keep, swap);
    }
};
```

> **Why do we have two separate checks for the “keep” state?**  
> Because the condition `nums1[i-1] < nums1[i] && nums2[i-1] < nums2[i]` is the same regardless of whether the previous index was swapped or not. The only difference is whether we add a swap cost (`swap + 0` or `swap + 0`). The same logic applies to the “swap” state.

---

## 6. Blog Article – “The Good, the Bad, the Ugly of LeetCode 801”

### 6.1 Title & Meta

**Title:**  
*Mastering LeetCode 801: Minimum Swaps to Make Sequences Increasing – Java, Python, & C++ Solutions*

**Meta Description:**  
Learn the fastest O(n) DP solution for LeetCode 801, with clear Java, Python, and C++ implementations. Discover pitfalls, trade‑offs, and interview‑ready explanations.

---

### 6.2 Introduction

> “What’s the most efficient way to make two sequences strictly increasing when you can only swap elements at the same index?”  
> That’s the question behind LeetCode 801. The challenge looks simple but quickly turns into a textbook DP exercise if you break it down correctly.  
> In this article we’ll walk through the problem, the dynamic programming trick that reduces it to O(n) time and O(1) space, and the code in three popular languages. We’ll also discuss the *good*, *bad*, and *ugly* aspects of the solution to help you avoid common interview pitfalls.

---

### 6.3 The Problem in Plain English

1. You have two integer arrays `nums1` and `nums2` of equal length `n`.  
2. In one operation you can swap the two numbers at the same index: `nums1[i] ↔ nums2[i]`.  
3. Your goal: **both arrays become strictly increasing** (`a[i] < a[i+1]` for every `i`).  
4. Find the minimum number of swaps needed.

Because the test data guarantees a solution, you only need to minimize swaps, not worry about “impossible” cases.

---

### 6.4 The Good – Why This DP Is Elegant

- **Linear Time:** We only need one pass over the arrays (`O(n)`).
- **Constant Extra Space:** Only two integer variables (`keep`, `swap`) are necessary.
- **Simple Recurrence:** The state at index `i` depends solely on `i-1`.  
  That’s the hallmark of a clean DP problem.

---

### 6.5 The Bad – Common Missteps

| Misstep | Why It Happens | Fix |
|---------|----------------|-----|
| **Confusing “swap” with “keep” conditions** | People sometimes think the condition for swapping at `i` is the same as for keeping, but it’s not. | Keep the four checks separately: keep‑keep, keep‑swap, swap‑keep, swap‑swap. |
| **Using arrays for DP unnecessarily** | Declaring `int[] keep = new int[n]` wastes memory. | Use two variables and update them in place. |
| **Ignoring the “swap at i-1” effect** | Failing to add `1` when the current index is swapped. | Always add `+1` for the current swap in `newSwap`. |
| **Assuming 1-indexed arrays** | Java, Python, C++ are 0‑based; mixing up indices leads to out‑of‑bounds. | Start the loop at `i = 1`. |

---

### 6.6 The Ugly – Edge Cases & Debugging Tips

- **Equality at boundaries** (`nums1[i] == nums2[i]` or `nums2[i] == nums1[i]`).  
  Remember the problem asks for *strictly* increasing, so you can’t allow `=`. The DP checks (`<`) handle this automatically.
- **Large numbers** (`1e9`).  
  No overflow occurs because we only store counts (`≤ n`), but you should still guard against `Integer.MAX_VALUE` in Java/C++.
- **Arrays of length 1** (`n = 1`).  
  The answer is always `0` because the arrays are trivially increasing; the DP base case handles this automatically.

**Debugging checklist:**

1. Print `keep` and `swap` at each step.  
2. Verify that the four condition branches are hit correctly.  
3. Cross‑check with a brute‑force solution for small `n` to ensure correctness.

---

### 6.7 Interview‑Ready Explanation

> **“We’re using a two‑state DP: keep or swap at each index.”**  
> *Key insight:* Swapping at index `i` does **not** influence the possibility of a future swap; it only changes the current values. Therefore, the decision at `i` only needs to remember whether the previous index was swapped or not, not the exact sequence of swaps.

> **Why two states are enough?**  
> Because there are only two possibilities per index (swap or not). For each, we evaluate whether the strictly increasing condition holds with the previous index’s state. This yields exactly four cases, which are captured in the recurrence.

> **Space optimization:**  
> Since `dp[i]` depends only on `dp[i-1]`, we can overwrite the previous values. This gives us the constant‑space variant that interviewers love.

---

### 6.7 Final Thoughts

LeetCode 801 is a *perfect* interview problem to showcase:

- Mastery of dynamic programming.  
- Ability to write clean, language‑agnostic code.  
- Attention to edge cases and interview etiquette.

Use the solutions above as a starting point. Add comments that explain each branch in your own words – that’s what the interviewers are looking for.

Good luck, and may your arrays stay strictly increasing!

---

### 6.8 Call‑to‑Action

> Have you tackled LeetCode 801 before? Share your experience in the comments, or try the next DP problem: **[LeetCode 725 – Split Array with Same Sum](https://leetcode.com/problems/split-array-with-same-sum/)**. Happy coding!

---

## 7. Summary

We derived a clean DP solution for LeetCode 801 that runs in linear time and constant space. We implemented it in Java, Python, and C++. The article explains the problem’s strengths, pitfalls, and debugging strategies, making it a useful reference for interview preparation and resume building.