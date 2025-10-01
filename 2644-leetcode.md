---
title: LeetCode 2644. Find the Maximum Divisibility Score - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Find the Maximum Divisibility Score – Easy LeetCode 2644

**Problem**  
You’re given two integer arrays `nums` and `divisors`.  
The *divisibility score* of a value `d` in `divisors` is the number of elements in `nums` that are divisible by `d`.  
Return the divisor that has the highest score. If there’s a tie, return the smallest divisor.

| Constraint | Detail |
|------------|--------|
| `1 ≤ nums.length, divisors.length ≤ 1000` | Both arrays can contain up to 1000 elements |
| `1 ≤ nums[i], divisors[i] ≤ 10⁹` | Numbers can be large, but we only need to check divisibility |

---

## 🧠 Why This Looks Hard (But Isn’t)

- **Naïve expectation:** Because `nums` and `divisors` can each have 1000 elements, a double‑loop gives at most 1 000 000 iterations – perfectly fine in Java, Python, or C++.
- **Edge‑case awareness:** Divisors can be larger than any number in `nums`, so the score can be `0`.  
- **Tie‑breaking:** The smallest divisor wins, so we need to compare both the score and the value itself.

---

## 🛠️ Solution Overview

1. **Brute‑Force Count**  
   For every divisor, iterate over all numbers in `nums` and count the multiples.

2. **Keep Track of Best Score**  
   Maintain `bestDivisor` and `bestScore`.  
   Update when we find a higher score or when the same score is achieved with a smaller divisor.

3. **Return Result**  
   After examining all divisors, return the stored `bestDivisor`.

This algorithm runs in **O(n · m)** time (`n = nums.length`, `m = divisors.length`) and uses **O(1)** extra space.

---

## 📦 Code Implementation

Below are clean, idiomatic solutions in **Java**, **Python**, and **C++**.

---

### Java (LeetCode‑compatible)

```java
class Solution {
    public int maxDivScore(int[] nums, int[] divisors) {
        int bestDivisor = 0;
        int bestScore   = -1;          // ensures first divisor wins when score is 0

        for (int divisor : divisors) {
            int score = 0;
            for (int num : nums) {
                if (num % divisor == 0) score++;
            }

            // Update if better score, or same score but smaller divisor
            if (score > bestScore || (score == bestScore && divisor < bestDivisor)) {
                bestScore   = score;
                bestDivisor = divisor;
            }
        }
        return bestDivisor;
    }
}
```

---

### Python (3.9+)

```python
class Solution:
    def maxDivScore(self, nums: list[int], divisors: list[int]) -> int:
        best_divisor = 0
        best_score   = -1

        for d in divisors:
            score = sum(1 for n in nums if n % d == 0)

            if score > best_score or (score == best_score and d < best_divisor):
                best_score   = score
                best_divisor = d

        return best_divisor
```

---

### C++17

```cpp
class Solution {
public:
    int maxDivScore(vector<int>& nums, vector<int>& divisors) {
        int bestDivisor = 0;
        int bestScore   = -1;

        for (int d : divisors) {
            int score = 0;
            for (int n : nums) {
                if (n % d == 0) ++score;
            }
            if (score > bestScore || (score == bestScore && d < bestDivisor)) {
                bestScore   = score;
                bestDivisor = d;
            }
        }
        return bestDivisor;
    }
};
```

All three solutions are **O(n · m)** time, **O(1)** extra space, and fully satisfy the problem constraints.

---

## 🔍 Good, Bad, and Ugly: A Developer’s Lens

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | Brute‑force is simple and fast enough for 1 000 × 1 000 | No need for sophisticated math or hashing | Over‑engineering (pre‑computing primes, etc.) gives no benefit |
| **Space Usage** | Constant extra memory | None | Storing auxiliary data for each divisor leads to O(n+m) memory |
| **Readability** | Clear loops, explicit variables | None | Obscure one‑liners or excessive use of streams/lambdas |
| **Edge Cases** | Handles score = 0 and ties properly | Forgetting to initialize `bestScore` can give wrong answer | Mis‑ordering tie‑break logic (e.g., using `>=` instead of `>`) |
| **Scalability** | Works for up to 10⁶ operations | None | If constraints grew to 10⁵ × 10⁵, brute‑force would break |

### Takeaway  
For LeetCode problems like this, keep the solution **straightforward**: double loops, careful tie handling, and minimal auxiliary data. This is the “good” that recruiters appreciate – it shows you can solve the core logic without unnecessary complexity.

---

## 🎯 SEO‑Optimized Blog Post

Below is a draft blog post you can publish on a personal blog or Medium.  
It’s formatted in Markdown for easy copy‑paste, uses relevant keywords, and follows best practices for readability.

```markdown
# 🚀 Mastering LeetCode 2644: Find the Maximum Divisibility Score

> “When you master easy LeetCode problems, you sharpen the skills that recruiters look for.”  
> – *Interview Preparation 101*

## 📌 Problem Summary

- **Title:** *Find the Maximum Divisibility Score*  
- **Difficulty:** Easy  
- **LeetCode ID:** 2644  
- **Key Task:** For each divisor, count how many numbers in `nums` are divisible by it. Return the divisor with the highest count; break ties by choosing the smallest divisor.

## 🔍 Why This Problem is a “Job‑Interview Essential”

- **Core Concepts:** Looping, modulo operation, tie‑breaking logic.  
- **Real‑world Skill:** Clear problem decomposition and straightforward implementation.  
- **Recruiter Angle:** Demonstrates you can translate a spec into working code quickly.

## ✅ Efficient Solution in 3 Languages

| Language | Complexity | Highlights |
|----------|------------|------------|
| **Java** | O(n·m) | Straightforward loops, explicit tie logic. |
| **Python** | O(n·m) | Uses generator expression for conciseness. |
| **C++** | O(n·m) | Classic `vector` loops, optimal memory use. |

*See the code snippets below for each implementation.*

### Java

```java
class Solution {
    public int maxDivScore(int[] nums, int[] divisors) {
        int bestDivisor = 0, bestScore = -1;
        for (int d : divisors) {
            int score = 0;
            for (int n : nums) if (n % d == 0) score++;
            if (score > bestScore || (score == bestScore && d < bestDivisor)) {
                bestScore = score; bestDivisor = d;
            }
        }
        return bestDivisor;
    }
}
```

### Python

```python
class Solution:
    def maxDivScore(self, nums, divisors):
        best_divisor, best_score = 0, -1
        for d in divisors:
            score = sum(1 for n in nums if n % d == 0)
            if score > best_score or (score == best_score and d < best_divisor):
                best_score, best_divisor = score, d
        return best_divisor
```

### C++

```cpp
class Solution {
public:
    int maxDivScore(vector<int>& nums, vector<int>& divisors) {
        int bestDivisor = 0, bestScore = -1;
        for (int d : divisors) {
            int score = 0;
            for (int n : nums) if (n % d == 0) ++score;
            if (score > bestScore || (score == bestScore && d < bestDivisor)) {
                bestScore = score; bestDivisor = d;
            }
        }
        return bestDivisor;
    }
};
```

## 💡 Problem‑Solving Deep Dive

### 1️⃣ Brute‑Force is Best‑Fit

- The input size (`≤ 1000` each) allows an **O(n·m)** solution.
- Over‑engineering (prime factorization, hash maps) would add unnecessary complexity and runtime.

### 2️⃣ Tie‑Breaking Logic

- `bestScore` starts at `-1` to guarantee the first divisor is chosen even if its score is `0`.
- Use `score > bestScore || (score == bestScore && d < bestDivisor)` to handle ties correctly.

### 3️⃣ Edge Cases to Remember

- All divisors larger than all numbers → score = 0 for every divisor.
- Duplicate divisors in the input – still processed independently.
- Single element arrays – trivial but must still work.

## 📚 Common Pitfalls & How to Avoid Them

| Pitfall | Fix |
|---------|-----|
| Forgetting to initialize `bestScore` → returns 0 incorrectly | Initialize to `-1` or `Integer.MIN_VALUE`. |
| Using `>=` in tie logic → picks larger divisor when scores tie | Use `>` for score comparison, then `&& d < bestDivisor`. |
| Ignoring the possibility of large numbers (≥ 10⁹) → overflow | Modulo operation in Java/Python/C++ handles this; no multiplication needed. |

## 🎯 Why Recruiters Love This Problem

- **Clarity of Thought:** Demonstrates you can translate a problem statement into loops and conditions without getting lost.
- **Attention to Detail:** Handling edge cases and tie‑breaking shows care for correctness.
- **Clean Code:** Short, readable, and well‑documented solutions are a sign of professionalism.

## 🚀 Next Steps

1. **Practice Variants** – Try swapping `nums` and `divisors` or increasing the size to stress‑test your algorithm.
2. **Time Yourself** – Aim to implement the solution in under 3 minutes for interview confidence.
3. **Write a Blog Post** – Share your solution and insights on LinkedIn or Medium to showcase your coding chops.

---

## 🔗 Useful Resources

- [LeetCode 2644: Find the Maximum Divisibility Score](https://leetcode.com/problems/find-the-maximum-divisibility-score/)
- [Interview Coding Interview Prep](https://example.com/interview-prep)
- [Java Collections Fundamentals](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ArrayList.html)

---

### TL;DR

- **Problem:** Find divisor with highest divisible count in `nums`.  
- **Solution:** Double loop, keep best score & divisor, O(n·m) time, O(1) space.  
- **Key Takeaway:** Simple, clean, correct – that’s what recruiters value.

Good luck! 🚀

```

---

## 🎉 Final Thoughts

You now have:

1. **Working code** in the three most popular interview languages.  
2. A **clear mental model** of why the brute‑force approach is optimal for this problem.  
3. A **developer‑friendly cheat sheet** (Good/Bad/Ugly) that highlights the most important aspects to showcase to recruiters.  
4. An **SEO‑friendly blog post** that you can publish to demonstrate your expertise and attract hiring managers.

Happy coding—and may your LeetCode victories land you the job of your dreams! 🚀
```

This completes the comprehensive response: code, analysis, and ready‑to‑publish blog content.