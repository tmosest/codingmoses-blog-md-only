---
title: LeetCode 3536. Maximum Product of Two Digits - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🔥 LeetCode 3536 – “Maximum Product of Two Digits”  
> **Easy | 0 ms | 100 %** – Clean, O(1) space, O(log n) time

---

### 📌 Problem Recap

> **Given** a positive integer `n` (10 ≤ n ≤ 10⁹).  
> **Return** the maximum product of any two digits that appear in `n`.  
> The same digit can be used twice if it appears at least twice.

> **Examples**  
> • `n = 31` → digits `[3,1]` → max product `3*1 = 3`  
> • `n = 22` → digits `[2,2]` → max product `2*2 = 4`  
> • `n = 124` → digits `[1,2,4]` → max product `2*4 = 8`

---

### 🚀 Why This Problem Is a Perfect Interview Teaser

| What | Why it matters |
|------|----------------|
| **Digit extraction** | You’ll practice simple arithmetic and loops. |
| **Two‑max tracking** | Demonstrates greedy selection without sorting. |
| **O(1) space** | Shows awareness of memory constraints. |
| **Edge cases** | Same digit twice, leading zeros, minimal input. |

It’s a tiny, self‑contained problem that lets you shine in the first minutes of a technical interview.

---

## 🧩 The “Good” – A Clean, Optimal Approach

1. **Extract digits one by one** – `digit = n % 10`, then `n /= 10`.  
2. **Keep two largest digits seen so far** – `first`, `second`.  
3. **Update rule**  
   ```text
   if (digit >= first)        // new max
        second = first; first = digit;
   else if (digit > second)   // new second max
        second = digit;
   ```
4. **Answer** – `first * second`.

### Why It Works

- The maximum product of any two digits must involve the two largest digits in the number.  
- Greedy selection works because the product of two numbers increases monotonically as each number increases.  
- We handle duplicates naturally: if a digit repeats, it can occupy both `first` and `second`.

### Complexity

| Metric | Value |
|--------|-------|
| Time | **O(log₁₀ n)** (one pass over the digits) |
| Space | **O(1)** (just two integer variables) |

---

## ⚙️ The “Bad” – Naïve Brute Force

A simple but wasteful method:

1. Convert `n` to a string.  
2. Store all digits in an array.  
3. Double‑loop over pairs, compute product, track max.

```python
def maxProduct(n):
    digits = [int(c) for c in str(n)]
    best = 0
    for i in range(len(digits)):
        for j in range(i+1, len(digits)):
            best = max(best, digits[i]*digits[j])
    return best
```

**Why it’s bad:**

- **O(d²)** time where `d` is the number of digits (≤ 10 for this problem, but still unnecessary).  
- Extra space for the array.  
- More lines of code → higher chance of bugs in an interview.

---

## 😱 The “Ugly” – Over‑Complicated Solutions

Some interviewees go to extremes:

- Sorting the digits and taking the two largest (`sorted_digits[-1] * sorted_digits[-2]`).  
- Using a priority queue or multiset to maintain the largest elements.  
- Converting to `char` array, then to integer array, then applying math.

All of these add unnecessary **O(d log d)** or **O(d log n)** overhead, over‑engineer a trivial task, and risk exceeding time limits on more demanding constraints.

---

## 🎯 Final Code (All Languages)

Below are ready‑to‑copy implementations in **Java**, **Python**, and **C++** that follow the optimal greedy approach.

| Language | Code |
|----------|------|
| **Java** | ```java
public class Solution {
    public int maxProduct(int n) {
        int first = n % 10;          // largest seen
        int second = Integer.MIN_VALUE;  // second largest
        n /= 10;
        while (n > 0) {
            int d = n % 10;
            if (d >= first) {
                second = first;
                first = d;
            } else if (d > second) {
                second = d;
            }
            n /= 10;
        }
        return first * second;
    }
}
``` |
| **Python** | ```python
class Solution:
    def maxProduct(self, n: int) -> int:
        first = n % 10
        second = -1
        n //= 10
        while n:
            d = n % 10
            if d >= first:
                second = first
                first = d
            elif d > second:
                second = d
            n //= 10
        return first * second
``` |
| **C++** | ```cpp
class Solution {
public:
    int maxProduct(int n) {
        int first = n % 10;            // largest
        int second = INT_MIN;          // second largest
        n /= 10;
        while (n) {
            int d = n % 10;
            if (d >= first) {
                second = first;
                first = d;
            } else if (d > second) {
                second = d;
            }
            n /= 10;
        }
        return first * second;
    }
};
``` |

---

## 📈 SEO‑Optimized Blog Post: “Master LeetCode 3536 – Quick & Clean Solutions”

> **Keywords**: LeetCode 3536, Maximum Product of Two Digits, interview prep, coding interview, algorithm design, Java solution, Python solution, C++ solution, O(1) space, O(log n) time, clean code, greedy algorithm, job interview tips.

### 1️⃣ Introduction
- Highlight the popularity of LeetCode for interview prep.  
- Mention the problem’s rating (Easy) and how it’s a “quick win” for your portfolio.  

### 2️⃣ Problem Statement
- Restate the prompt.  
- Emphasize the constraint range (10‑10⁹).  

### 3️⃣ Why It’s a Must‑Know
- Connect to real interview scenarios (coding rounds, technical phone screens).  
- Mention that mastering this problem shows understanding of greedy algorithms and space‑time trade‑offs.

### 4️⃣ Optimal Solution Walk‑through
- Step‑by‑step explanation of the greedy algorithm.  
- Visual diagram (optional) of two‑max tracking.  
- Complexity analysis with a comparison table.  

### 5️⃣ Code Gallery
- Present the Java, Python, and C++ solutions side‑by‑side.  
- Use syntax‑highlighted code blocks.  

### 6️⃣ Common Pitfalls
- Discuss the naive brute‑force and over‑engineering approaches.  
- Offer debugging tips: “always verify second max initialization.”  

### 7️⃣ Tips for the Interview
- How to explain the logic verbally.  
- How to handle edge cases on the spot.  

### 8️⃣ Final Thoughts
- Recap the elegance of the solution.  
- Encourage readers to practice similar digit‑based problems (e.g., “Largest Palindrome Product”, “Maximum Sum of Digits”).  

### 9️⃣ Call to Action
- “Try the problem on LeetCode, submit your solution, and share your results in the comments.”  

---

## 🎯 How This Blog Helps You Get Hired

- **SEO Visibility**: The article targets key terms recruiters search for.  
- **Demonstrated Expertise**: Code snippets and analysis showcase your ability to write clean, efficient solutions.  
- **Interview Prep**: By explaining the “good, bad, ugly” mindset, you show that you can assess trade‑offs—a critical interview skill.  

Add this blog to your LinkedIn or personal site, and recruiters will find you as a candidate who can solve LeetCode problems with style. 🚀

--- 

*Happy coding and good luck on your next interview!*