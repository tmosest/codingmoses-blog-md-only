---
title: LeetCode 1248. Count Number of Nice Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  The Code – Three Languages

Below you’ll find a clean, one‑pass, O(n) solution that counts the number of “nice” sub‑arrays (exactly *k* odd numbers).  
The idea is to transform the problem into *“sub‑arrays with at most k odds”* and then subtract the count for *k‑1*.  
All three implementations use the same helper routine `atMostK`.

| Language | Code |
|----------|------|
| **Java** | **Solution.java** |
| **Python** | **solution.py** |
| **C++** | **solution.cpp** |

---

## 1.1 Java

```java
// 1248. Count Number of Nice Subarrays
// Sliding‑Window + Prefix‑Sum Trick (O(n) time, O(1) space)

import java.util.*;

public class Solution {
    // Count sub‑arrays with exactly k odd numbers
    public int numberOfSubarrays(int[] nums, int k) {
        return atMostK(nums, k) - atMostK(nums, k - 1);
    }

    // Count sub‑arrays with at most k odd numbers
    private int atMostK(int[] nums, int k) {
        if (k < 0) return 0;          // nothing can satisfy a negative quota
        int left = 0, right = 0;
        int oddCnt = 0;
        int total = 0;
        while (right < nums.length) {
            // add current element to the window
            oddCnt += nums[right] % 2;
            // shrink from the left if we exceed the quota
            while (oddCnt > k) {
                oddCnt -= nums[left] % 2;
                left++;
            }
            // all sub‑arrays ending at right and starting anywhere
            // between left and right are valid
            total += right - left + 1;
            right++;
        }
        return total;
    }
}
```

---

## 1.2 Python

```python
# 1248. Count Number of Nice Subarrays
# Sliding window approach – O(n) time, O(1) space

from typing import List

class Solution:
    def numberOfSubarrays(self, nums: List[int], k: int) -> int:
        return self.at_most_k(nums, k) - self.at_most_k(nums, k - 1)

    def at_most_k(self, nums: List[int], k: int) -> int:
        if k < 0:
            return 0
        left = 0
        odd_cnt = 0
        total = 0
        for right, val in enumerate(nums):
            odd_cnt += val & 1   # 1 if odd, 0 if even
            while odd_cnt > k:
                odd_cnt -= nums[left] & 1
                left += 1
            total += right - left + 1
        return total
```

---

## 1.3 C++

```cpp
// 1248. Count Number of Nice Subarrays
// Sliding window – O(n) time, O(1) space

#include <vector>
using namespace std;

class Solution {
public:
    int numberOfSubarrays(vector<int>& nums, int k) {
        return atMostK(nums, k) - atMostK(nums, k - 1);
    }

private:
    int atMostK(const vector<int>& nums, int k) {
        if (k < 0) return 0;
        int left = 0;
        int oddCnt = 0;
        int total = 0;
        for (int right = 0; right < (int)nums.size(); ++right) {
            oddCnt += nums[right] & 1;          // 1 if odd
            while (oddCnt > k) {
                oddCnt -= nums[left] & 1;
                ++left;
            }
            total += right - left + 1;          // sub‑arrays ending at right
        }
        return total;
    }
};
```

All three implementations have **O(n)** time complexity, scan the array once, and use only a handful of integer variables – perfectly suitable for the LeetCode constraints (up to 50 000 elements).

# 2.  Blog Article – “The Good, The Bad, and The Ugly” of Counting Nice Sub‑Arrays

> **Target audience:** Software engineers, coding interviewees, and recruiters  
> **Goal:** Showcase deep understanding of a classic sliding‑window problem, while boosting SEO for job‑search keywords.

---

## 2.1 Title & Meta‑Data

| Field | Value |
|-------|-------|
| **Title** | *Counting Nice Sub‑Arrays: The Good, The Bad, and The Ugly – A Complete Sliding‑Window Walk‑through* |
| **Meta Description** | Master LeetCode 1248 with an O(n) sliding‑window solution. Learn the pitfalls, best practices, and how to ace the interview. |
| **Keywords** | “LeetCode 1248”, “count number of nice subarrays”, “sliding window”, “two pointers”, “interview question”, “coding interview”, “Java solution”, “Python solution”, “C++ solution”, “exactly k odds”, “algorithm interview”, “job interview prep” |

---

## 2.2 Introduction

> “In algorithm interviews, the most dreaded questions are the ones that look simple but trip you up.”  
>  
>  LeetCode 1248 – **Count Number of Nice Subarrays** – is that classic. It asks you to find how many continuous sub‑arrays contain *exactly* `k` odd numbers.  
>  
>  The trick? Turn the problem into a *prefix sum* variant and solve it in **O(n)** time with **O(1)** extra space. Below, we walk through the **good**, the **bad**, and the **ugly** of this challenge, with code in Java, Python, and C++.

---

## 2.3 Problem Overview

| Parameter | Meaning |
|-----------|---------|
| `nums`    | Integer array (`1 <= nums[i] <= 10⁵`) |
| `k`       | Target count of odd numbers (`1 <= k <= nums.length`) |
| **Goal** | Count sub‑arrays with exactly `k` odd numbers |

**Examples**

1. `nums = [1,1,2,1,1]`, `k = 3` → `2` (`[1,1,2,1]`, `[1,2,1,1]`)  
2. `nums = [2,4,6]`, `k = 1` → `0` (no odd numbers)  
3. `nums = [2,2,2,1,2,2,1,2,2,2]`, `k = 2` → `16`

---

## 2.4 The Good – Why the Sliding‑Window Approach Wins

1. **Linear Time, Constant Extra Space**  
   Each element is processed at most twice – once when the right pointer moves right, once when the left pointer moves right. No extra arrays or hash maps.

2. **Avoids Prefix‑Sum + HashMap Overhead**  
   A common first attempt is to compute prefix sums of “oddness” and use a hash map to count sub‑array sums equal to `k`. That works but has O(n) time and O(n) space. Sliding window reduces space to O(1).

3. **Clear Intuition**  
   Transform the array: odd → 1, even → 0. The problem turns into “sub‑array sum equals k”.  
   Then, *exactly k* = *at most k* – *at most (k‑1)*. Each helper call counts how many windows contain at most `k` ones.  
   Counting “at most k” is a textbook sliding‑window problem: expand, shrink when exceeding `k`.

4. **Reusable Helper**  
   The helper function `atMostK` is agnostic of the target. The final answer is a simple subtraction, avoiding code duplication.

5. **Edge‑Case Friendly**  
   Negative `k` in the helper automatically returns 0. This neatly handles `k‑1` when `k = 0`.

---

## 2.5 The Bad – Common Pitfalls and Edge Cases

| Pitfall | Why it Happens | How to Fix |
|---------|----------------|------------|
| **Treating every number as a “sub‑array”** | Mis‑counting when odd/even conversion isn’t performed | Use `num % 2` (or `num & 1`) to detect odds |
| **Off‑by‑one errors in window length** | Forgetting `right - left + 1` for inclusive ranges | Explicitly test small cases (`[1]`, `k=1`) |
| **Using a HashMap but missing the “k‑1” subtraction** | Accumulating exact counts only | Employ the difference trick or double‑loop prefix sums |
| **Ignoring the negative `k‑1` scenario** | `k` may be 1 → helper called with 0 | Add guard `if k < 0: return 0` |
| **Using `val % 2` repeatedly inside inner loop** | Slight performance hit in tight loops | Pre‑compute `val & 1` once per element |
| **Assuming the array is 0‑based** | C++ indices vs Python list slices | Use `for right, val in enumerate(nums):` in Python, `for (int right = 0; right < n; ++right)` in C++ |

---

## 2.6 The Ugly – Deep‑Dive into Optimization & “Nice” Variants

1. **Integer Overflow**  
   In Java, the sum of windows can reach `n * n / 2` (≈ 1.25 billion for n=50k). `int` is safe in Java and C++, but in languages with smaller integer types you might need `long`.

2. **Bit‑wise vs Modulo**  
   Using `val & 1` (bit‑wise AND) is marginally faster than `val % 2`. Modern compilers optimize both, but micro‑benchmarks on large inputs show a ~5–10% speed boost for bit‑wise.

3. **When k is very large (k = n)**  
   `atMostK` will never shrink the window; the entire array is a single window. The helper still works – `total = n * (n + 1) / 2`.

4. **Handling Empty Array**  
   The problem guarantees `nums.length >= 1`, but defensive programming still checks for `k < 0`.

5. **Potential “Ugly” Recursion**  
   A recursive “atMostK” is an easy route to a stack overflow for 50 k elements. Keep the routine iterative.

6. **Testing Strategy**  
   Write tests that cover: all odds, all evens, `k = 1`, `k = n`, alternating odd/even patterns.  

---

## 2.7 How to Show Up in a Recruiter’s Search

| Action | SEO Impact |
|--------|------------|
| **Include multiple language solutions** | Recruiters looking for “Java solution LeetCode 1248” or “Python solution” will click |
| **Use clear function names (`atMostK`)** | Helps search algorithms pick up on algorithmic terminology |
| **Add timestamps & complexity analysis** | Signals professionalism to both ATS and hiring managers |
| **Provide visual diagrams** | Enhances readability, which boosts dwell‑time – a key ranking factor |
| **Embed code snippets in `<pre><code>` tags** | Allows search engines to index the code for query‑level searches |

---

## 2.8 Closing – Turning the Question into a Portfolio Piece

> *“It’s not just about getting the right answer; it’s about showing that you understand why the answer works, how to guard against common errors, and how to explain the algorithm concisely.”*  
>  
>  By mastering LeetCode 1248 with the sliding‑window approach, you demonstrate:
>  - Proficiency with two‑pointer techniques  
>  - Ability to transform problems into simpler sub‑problems  
>  - Efficient use of time & space  
>  - Clean, maintainable code across Java, Python, and C++  

> Deploy this article on your blog, LinkedIn, or Medium with the keywords above, and you’ll attract recruiters who are actively searching for interview‑ready candidates. Good luck—and remember: every “ugly” bug you avoid is one step closer to that next interview call.  

--- 

## 2.9 Quick Checklist for the Interview

- [ ] **Read the problem statement carefully** – note that `k` is “exactly”.
- [ ] **Convert odds to 1, evens to 0** – simple modulo or bit‑wise trick.
- [ ] **Implement `atMostK` sliding window** – expand → shrink → add `right-left+1`.
- [ ] **Return `atMostK(k) - atMostK(k-1)`** – handles all edge cases automatically.
- [ ] **Test on provided examples + edge cases** – all three languages should pass.

Happy coding, and may your next interview be a “nice” one!