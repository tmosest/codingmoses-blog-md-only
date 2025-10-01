---
title: LeetCode 2393. Count Strictly Increasing Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap  
**LeetCode 2393 – Count Strictly Increasing Subarrays**  
Given an array `nums` of positive integers, return the number of *contiguous* sub‑arrays that are in strictly increasing order.

*Examples*  
```text
Input:  [1,3,5,4,4,6]  →  Output: 10
Input:  [1,2,3,4,5]    →  Output: 15
```

**Constraints**

| Property | Value |
|----------|-------|
| `1 ≤ nums.length ≤ 10^5` | |
| `1 ≤ nums[i] ≤ 10^6` | |

The task is a classic interview problem that tests your ability to reason about sub‑array patterns, to keep an eye on time and space complexity, and to produce clean, language‑agnostic code.

---

## 2.  Intuition & “Good” Approach  

A sub‑array is strictly increasing **iff** every adjacent pair satisfies `nums[i] < nums[i+1]`.  
If we walk through the array once and keep track of the current “run” of increasing elements, we can count all sub‑arrays that end in the current element in *O(1)* time.

Let  

```
len  = length of the current strictly increasing run
```

All sub‑arrays that end at the current index are exactly the sub‑arrays of the form  
`[start, start+1, …, current]` where `start` can be any index in the run.  
The number of such sub‑arrays is

```
len * (len + 1) / 2
```

Therefore, the algorithm is:

1. Iterate once through `nums`.  
2. When the increasing property is broken, add the contribution of the previous run to the answer, reset the run length.  
3. After the loop, add the contribution of the final run.

The algorithm runs in **O(n)** time, uses **O(1)** extra space, and works for the full constraints.

---

## 3.  “Bad” Alternatives  

1. **Brute Force** – Enumerate all `O(n^2)` sub‑arrays and test each for strict monotonicity.  
   *Time*: `O(n^3)` in the worst case.  
   *Space*: `O(1)`.  
   *Outcome*: TLE on 10⁵ sized arrays.

2. **Dynamic Programming (DP)** – `dp[i]` = length of the longest increasing suffix ending at `i`.  
   While this is also `O(n)` time, it is more verbose than necessary and requires an auxiliary array of size `n`.  
   It’s still fine for the constraints but doesn’t give the cleanest code.

3. **Two‑pointer with a sliding window** – Expand a window while it stays increasing, shrink when it isn’t.  
   This approach is also `O(n)`, but it involves more pointer gymnastics and can be confusing for interviewees.

In interviews, clarity and brevity are prized. The simple “run‑length” method above is the “good” one.

---

## 4.  “Ugly” Corner Cases  

| Edge | What to watch out for |
|------|-----------------------|
| All equal elements | `len` never grows beyond 1; answer = `n`. |
| Strictly decreasing array | Each element starts a new run; answer = `n`. |
| Single element array | Return 1. |
| Very large numbers | Use `long` (Java) or `long long` (C++) to avoid overflow when computing `len*(len+1)/2`. |
| Empty array | According to constraints it never happens, but guard against `nums.length==0` if you reuse code. |

---

## 5.  Reference Code  

Below are idiomatic, commented solutions in **Java**, **Python**, and **C++**.

---

### 5.1 Java  

```java
/**
 * LeetCode 2393 – Count Strictly Increasing Subarrays
 * O(n) time, O(1) extra space
 */
class Solution {
    public long countSubarrays(int[] nums) {
        long answer = 0;          // result
        long runLen = 0;          // length of current increasing run

        for (int i = 0; i < nums.length; i++) {
            if (i == 0 || nums[i] > nums[i - 1]) {
                // still increasing – extend the run
                runLen++;
            } else {
                // run broken – add its contribution
                answer += runLen * (runLen + 1) / 2;
                runLen = 1;      // current element starts a new run
            }
        }

        // add the last run
        answer += runLen * (runLen + 1) / 2;
        return answer;
    }
}
```

---

### 5.2 Python  

```python
# LeetCode 2393 – Count Strictly Increasing Subarrays
# Python 3 – O(n) time, O(1) space

class Solution:
    def countSubarrays(self, nums: list[int]) -> int:
        answer = 0
        run_len = 0

        for i, val in enumerate(nums):
            if i == 0 or val > nums[i - 1]:
                run_len += 1
            else:
                answer += run_len * (run_len + 1) // 2
                run_len = 1   # start new run

        # last run
        answer += run_len * (run_len + 1) // 2
        return answer
```

---

### 5.3 C++ (C++17)  

```cpp
/**
 * LeetCode 2393 – Count Strictly Increasing Subarrays
 * O(n) time, O(1) extra space
 */
class Solution {
public:
    long long countSubarrays(vector<int>& nums) {
        long long ans = 0;
        long long runLen = 0;

        for (size_t i = 0; i < nums.size(); ++i) {
            if (i == 0 || nums[i] > nums[i - 1]) {
                ++runLen;
            } else {
                ans += runLen * (runLen + 1) / 2;
                runLen = 1; // start new run with current element
            }
        }

        ans += runLen * (runLen + 1) / 2;
        return ans;
    }
};
```

---

## 6.  Blog Article – “The Good, The Bad, and The Ugly of Counting Strictly Increasing Subarrays”

> **Title**: *LeetCode 2393 – Count Strictly Increasing Subarrays: A Deep Dive into the Good, the Bad, and the Ugly*

> **Meta Description**: Master LeetCode 2393 in Java, Python, and C++ with an easy, fast solution. Learn the pitfalls, best practices, and interview secrets. Boost your coding interview game!

### 6.1 Introduction  

When you’re on the job hunt, the *technical interview* is your stage. One of the most frequent themes is *sub‑array counting* – you’ll meet it under different names (longest increasing sub‑array, longest common sub‑sequence, etc.).  

LeetCode 2393, *Count Strictly Increasing Subarrays*, is a textbook example that blends:

- **Time‑efficiency** (you need an `O(n)` solution).
- **Space‑efficiency** (extra memory must be constant).
- **Clean code** (readable, testable, language‑agnostic).

In this article, we’ll dissect the problem, expose the good, bad, and ugly ways to solve it, and finally deliver a rock‑solid implementation in **Java**, **Python**, and **C++**.

---

### 6.2 Problem Recap  

> **Input** – an array `nums` of `n` positive integers.  
> **Goal** – count the number of *contiguous* sub‑arrays that are strictly increasing.

*Example*:  
`[1, 3, 5, 4, 4, 6]` → 10 sub‑arrays (`[1]`, `[3]`, `[1,3]`, etc.)

---

### 6.3 The Good – One‑Pass Run‑Length Approach  

#### 6.3.1 Why It Works  
Every time the sequence “breaks” (i.e., `nums[i] <= nums[i-1]`), the preceding stretch of numbers is a **maximal strictly increasing run**.  
If that run has length `L`, the number of sub‑arrays that end at the last element of the run is simply the number of ways to choose a start point inside the run:

```
1 + 2 + … + L = L * (L + 1) / 2
```

Thus, by iterating once, tracking the current run length, and adding the contribution each time a run ends, we can count all sub‑arrays.

#### 6.3.2 Complexity  

| Aspect | Result |
|--------|--------|
| Time   | **O(n)** (one traversal) |
| Space  | **O(1)** (constant auxiliary data) |
| Overflow | Use `long` / `long long` to hold the final sum. |

#### 6.3.3 Code Snippet (Java)  

```java
long runLen = 0;
for (int i = 0; i < nums.length; i++) {
    if (i == 0 || nums[i] > nums[i - 1]) runLen++;
    else {
        answer += runLen * (runLen + 1) / 2;
        runLen = 1;
    }
}
answer += runLen * (runLen + 1) / 2;
```

The same pattern is idiomatic in Python (`// 2`) and C++ (`/ 2`), just adjust data types.

---

### 6.4 The Bad – Brute Force & Over‑Engineering  

| Approach | Why it’s Bad |
|----------|--------------|
| **Brute Force** – O(n²) sub‑array enumeration + O(n) check → O(n³) time | Fails fast on large inputs |
| **DP with auxiliary array** – `dp[i] = length of inc suffix` | Extra memory, more code, but still linear |
| **Two‑pointer sliding window** – expand while increasing, shrink otherwise | Works, but pointer gymnastics can be confusing; more prone to off‑by‑one errors |

In interviews, “clever” but unnecessarily complex solutions can backfire: they’re harder to explain and audit on the spot.

---

### 6.5 The Ugly – Edge Cases & Implementation Pitfalls  

1. **All equal numbers** – runs never grow beyond 1; answer is simply `n`.  
2. **Strictly decreasing array** – each element starts a new run; answer is `n`.  
3. **Overflow** – `L*(L+1)/2` can exceed 32‑bit; use 64‑bit integers.  
4. **Single element array** – return 1 (the element itself).  
5. **Empty array** – not allowed per constraints, but defensive code should handle it gracefully.

---

### 6.6 Full Code – Three Languages  

**Java**

```java
class Solution {
    public long countSubarrays(int[] nums) {
        long ans = 0, run = 0;
        for (int i = 0; i < nums.length; i++) {
            if (i == 0 || nums[i] > nums[i - 1]) run++;
            else {
                ans += run * (run + 1) / 2;
                run = 1;
            }
        }
        ans += run * (run + 1) / 2;
        return ans;
    }
}
```

**Python**

```python
class Solution:
    def countSubarrays(self, nums: list[int]) -> int:
        ans, run = 0, 0
        for i, val in enumerate(nums):
            if i == 0 or val > nums[i - 1]:
                run += 1
            else:
                ans += run * (run + 1) // 2
                run = 1
        ans += run * (run + 1) // 2
        return ans
```

**C++**

```cpp
class Solution {
public:
    long long countSubarrays(vector<int>& nums) {
        long long ans = 0, run = 0;
        for (size_t i = 0; i < nums.size(); ++i) {
            if (i == 0 || nums[i] > nums[i-1]) ++run;
            else {
                ans += run * (run + 1) / 2;
                run = 1;
            }
        }
        ans += run * (run + 1) / 2;
        return ans;
    }
};
```

---

### 6.7 Interview Tips  

- **Explain the intuition first**: talk about “runs” and “maximal stretches”.
- **Show the math**: sum of first `L` integers → `L*(L+1)/2`.
- **Mention overflow early**: “I’ll use 64‑bit integers to be safe”.
- **Run through a small example on the board** (e.g., `[1, 2, 1, 2]`).
- **Keep code clean**: single `for` loop, few variables, no nested loops.

---

### 6.8 Conclusion  

LeetCode 2393 is deceptively simple once you understand that *runs* are the building blocks.  
- The **good**: a single pass, constant space, mathematically elegant.  
- The **bad**: over‑engineering or brute force will get you a “No, not that one”.  
- The **ugly**: edge cases, integer overflow, and defensive coding are the real traps.

Armed with the code above in Java, Python, or C++, you’re ready to ace this question and move one step closer to your next job offer.

---

### 6.9 Take‑away  

- **One‑pass, run‑length** is the *gold standard* for LeetCode 2393.
- **Avoid O(n²)** or O(n³) patterns unless the constraints explicitly allow them.
- **Test your edge cases** before the interview.
- **Show the math**—explaining why `L*(L+1)/2` counts the sub‑arrays shows deep understanding.

Happy coding, and may your next interview go smoothly!  

---

### 6.10 Call to Action  

> **Want more LeetCode walkthroughs?** Subscribe to our newsletter and get weekly coding interview tips in Java, Python, and C++.  
> **Apply**: If you’re looking for a Software Engineer role, let’s connect – send me a DM or email your resume at `techinterview@example.com`.

--- 

**End of article**.  

--- 

## 7.  Closing Remarks  

You now own:

- A **conceptually simple** solution.
- **Implementation‑ready** code for three major languages.
- An understanding of the **common pitfalls** that can trip up candidates.

Use this knowledge to practice, write clean tests, and present the solution confidently in your next coding interview. Good luck!