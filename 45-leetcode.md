---
title: LeetCode 45. Jump Game II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Jump Game II – LeetCode #45  
**A Complete, Job‑Ready Guide (Java / Python / C++)**

> *“The good, the bad, and the ugly”* – how to solve the problem, what to avoid, and how to impress interviewers.

---

## 1. Problem Recap (LeetCode #45)

> **Jump Game II**  
> **Difficulty:** Medium  
> **Signature (Java):** `public int jump(int[] nums);`

You’re standing at index 0 of an array `nums`.  
`nums[i]` is the *maximum* distance you can jump forward from that index.  
Your goal is to reach the last index (`n‑1`) using the **fewest jumps possible**.  
The problem guarantees that the last index is always reachable.

---

## 2. Brute‑Force vs. Optimal

| Approach | Time | Space | Why It’s Not Ideal |
|----------|------|-------|--------------------|
| DFS / Recursion with memo | **O(2ⁿ)** | O(n) recursion | Exponential blow‑up; fails on 10⁴ length |
| Dynamic Programming (DP) | **O(n²)** | O(n) | Nested loops; still too slow for 10⁴ |
| Greedy (optimal) | **O(n)** | O(1) | One pass, constant memory |

The greedy strategy is the only one that scales to the maximum constraints (`n ≤ 10⁴`).  

---

## 3. Greedy Insight

Think of the array as a “road” and each index as a “checkpoint”.  
From any checkpoint you can drive at most `nums[i]` units ahead.  
The key observation: **If you know the furthest position you can reach with `k` jumps, you can decide the optimal `k+1`‑th jump without looking at future jumps.**

Algorithm steps:

1. **Maintain** three variables:  
   * `currentEnd` – the furthest index we can reach with the current number of jumps.  
   * `farthest` – the furthest index we can reach from all indices **up to** `currentEnd`.  
   * `jumps` – number of jumps taken so far.
2. Iterate over each index `i` (except the last one).  
3. Update `farthest = max(farthest, i + nums[i])`.  
4. When `i` reaches `currentEnd`, we must make another jump:  
   * `jumps++`  
   * `currentEnd = farthest`
5. Stop once `currentEnd` reaches or surpasses the last index.

Why it works: Every time we finish the current “reachable segment” (`currentEnd`), we’re forced to jump to the best possible spot within that segment (`farthest`). This is guaranteed to be optimal because any other choice would not let us reach further with the same number of jumps.

---

## 4. Code

Below are **three** full, ready‑to‑paste solutions – one for each of the most common interview languages.

### 4.1 Java (LeetCode‑style)

```java
/**
 * 45. Jump Game II – Greedy solution
 * Time   : O(n)
 * Space  : O(1)
 */
public class Solution {
    public int jump(int[] nums) {
        int jumps = 0;          // number of jumps taken
        int currentEnd = 0;     // furthest index reachable with current jumps
        int farthest = 0;       // furthest index reachable with one more jump

        for (int i = 0; i < nums.length - 1; i++) { // no need to jump from last index
            farthest = Math.max(farthest, i + nums[i]);

            // If we have reached the end of the current jump range,
            // we must make another jump.
            if (i == currentEnd) {
                jumps++;
                currentEnd = farthest;

                // Early exit: if we can already reach the last index.
                if (currentEnd >= nums.length - 1) {
                    break;
                }
            }
        }
        return jumps;
    }
}
```

> **Tips for Interviewers**  
> • Mention that the loop stops before the last index because the job is done when you reach it.  
> • Emphasize the O(1) space usage – a strong point for large inputs.

---

### 4.2 Python (3.x)

```python
class Solution:
    def jump(self, nums: list[int]) -> int:
        """Greedy O(n) solution for Jump Game II."""
        jumps, current_end, farthest = 0, 0, 0

        for i, jump in enumerate(nums[:-1]):   # last index needs no jump
            farthest = max(farthest, i + jump)

            if i == current_end:               # must extend the jump
                jumps += 1
                current_end = farthest
                if current_end >= len(nums) - 1:
                    break

        return jumps
```

> **Why Python?**  
> • Clear readability – `enumerate` hides the index tracking.  
> • Type hints (`list[int]`) help static analysis tools.

---

### 4.3 C++ (Modern, GNU++17)

```cpp
/**
 * 45. Jump Game II – Greedy
 * Complexity: O(n) time, O(1) space
 */
class Solution {
public:
    int jump(vector<int>& nums) {
        int jumps = 0, currentEnd = 0, farthest = 0;

        for (int i = 0; i < nums.size() - 1; ++i) {
            farthest = max(farthest, i + nums[i]);

            if (i == currentEnd) {          // need to jump
                ++jumps;
                currentEnd = farthest;

                if (currentEnd >= nums.size() - 1)
                    break;
            }
        }
        return jumps;
    }
};
```

> **Key C++ Highlights**  
> • `vector<int>& nums` – avoid unnecessary copies.  
> • Use `size_t` if you want to avoid signed‑unsigned warnings (optional).

---

## 5. The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithm** | Greedy is *optimal*, O(n) | Brute‑force DP is *O(n²)* | Over‑engineering with recursion + memo, hard to read |
| **Space** | O(1) | O(n) for DP arrays | Recursion stack blowing up |
| **Edge Cases** | Handles `nums[i] == 0` gracefully | DP may mis‑count if not careful | Forgetting to exclude the last element from the loop |
| **Readability** | Clear variable names (`currentEnd`, `farthest`) | Confusing names (`reach`, `maxReach`) | Mixing loops and `if` conditions without comments |
| **Performance** | Fast on 10⁴ inputs | Slow on large tests | Timeout or MLE in contests |
| **Interview Talk** | Explain greedy intuition; mention “reachability segments” | Avoid just saying “I used DP” | Skip explaining why you’re not re‑jumping from the same index |

---

## 6. SEO‑Optimized Blog Article

> **Title:** *Jump Game II – A Masterclass in Greedy Algorithms (Java, Python, C++)*  
> **Meta Description:** Learn the fastest solution to LeetCode’s Jump Game II. Discover the greedy approach, see code in Java, Python, and C++, and boost your interview performance today.

---

### 6.1 Introduction

If you’re prepping for a software engineering interview, you’ll soon encounter **Jump Game II** – a classic problem that tests both your algorithmic thinking and coding skill. In this article, we break down the greedy solution that runs in linear time, provide full implementations in Java, Python, and C++, and highlight the pitfalls interviewers love to ask about.

---

### 6.2 Problem Summary

* **Goal:** Minimum jumps to reach the last array index.  
* **Input:** Array `nums` where `nums[i]` is the max jump length from `i`.  
* **Guarantee:** Last index is always reachable.

---

### 6.3 Why Greedy is the Winning Strategy

- **Linear Pass:** We only iterate once (`O(n)`), perfect for `n ≤ 10⁴`.  
- **Constant Space:** No extra arrays – just a handful of integers.  
- **Intuition:** Treat each index as a “checkpoint.” Once you’ve explored all positions up to the current checkpoint, you’re forced to jump to the farthest reachable point. This is the only choice that keeps the jump count minimal.

---

### 6.4 Code Walkthrough (Java)

```java
public int jump(int[] nums) {
    int jumps = 0, currentEnd = 0, farthest = 0;
    for (int i = 0; i < nums.length - 1; i++) {
        farthest = Math.max(farthest, i + nums[i]);
        if (i == currentEnd) {
            jumps++;
            currentEnd = farthest;
            if (currentEnd >= nums.length - 1) break;
        }
    }
    return jumps;
}
```

- `farthest`: furthest index reachable from the current segment.  
- `currentEnd`: boundary of the current jump.  
- When `i == currentEnd`, we “commit” a new jump.

---

### 6.5 Python and C++ Variants

> See the full code snippets in the **Code** section above.  
> The logic is identical; only syntax changes.

---

### 6.6 Common Interview Questions

1. **Why does the algorithm not revisit indices?**  
   Because once we finish the current reach, we must jump forward – revisiting would waste jumps.

2. **What if `nums[i]` is zero?**  
   The algorithm naturally handles it; `farthest` won’t change, and the next `i == currentEnd` triggers a new jump (or the guarantee ensures we never get stuck).

3. **Can you prove optimality?**  
   By induction on the jump count: the farthest reachable position after `k` jumps is the best we can do with `k` jumps; thus the greedy jump is optimal.

---

### 6.7 How to Nail the Interview

- **Explain the intuition** before writing code.  
- **Use comments** to annotate `currentEnd` vs. `farthest`.  
- **Highlight edge cases** (zeros, single‑element arrays).  
- **Mention complexity**: *O(n)* time, *O(1)* space – a key selling point.

---

### 6.8 Takeaway

Jump Game II is a showcase problem: a short description, a clever greedy solution, and clean code in three popular languages. Master it, and you’ll have a conversation starter that demonstrates problem‑solving, coding proficiency, and an eye for efficiency – exactly what interviewers want.

Happy coding and good luck with your job hunt! 🚀

---

## 7. Final Checklist

- [x] Java solution ready for LeetCode submission.  
- [x] Python 3.x implementation with type hints.  
- [x] C++17 solution with vector reference.  
- [x] Blog article with SEO keywords: *Jump Game II, Greedy Algorithm, LeetCode interview, Java solution, Python solution, C++ solution, job interview coding*.  
- [x] Highlight good, bad, ugly aspects for interview prep.

Feel free to copy, adapt, and share these snippets and article on your portfolio, blog, or LinkedIn to boost your visibility and showcase your algorithmic chops. Good luck!