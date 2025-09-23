---
title: LeetCode 1980. Find Unique Binary String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Leetcode 1980 – Find Unique Binary String  
**(Java / Python / C++ Solutions + SEO‑Optimized Blog Post)**  

---

### 1. Code (All Languages)

#### Java

```java
/**
 * Leetcode 1980 – Find Unique Binary String
 * Time:  O(n)
 * Space: O(1) (ignoring output string)
 */
class Solution {
    public String findDifferentBinaryString(String[] nums) {
        int n = nums.length;
        StringBuilder sb = new StringBuilder(n);
        for (int i = 0; i < n; i++) {
            // Flip the bit at position i (Cantor's diagonal)
            sb.append(nums[i].charAt(i) == '0' ? '1' : '0');
        }
        return sb.toString();
    }
}
```

#### Python

```python
class Solution:
    """
    Leetcode 1980 – Find Unique Binary String
    Time:  O(n)
    Space: O(1) (ignoring output string)
    """
    def findDifferentBinaryString(self, nums: List[str]) -> str:
        n = len(nums)
        # Build the diagonal string by flipping each bit
        return ''.join('1' if nums[i][i] == '0' else '0' for i in range(n))
```

#### C++

```cpp
/**
 * Leetcode 1980 – Find Unique Binary String
 * Time:  O(n)
 * Space: O(1) (ignoring output string)
 */
class Solution {
public:
    string findDifferentBinaryString(vector<string>& nums) {
        int n = nums.size();
        string res;
        res.reserve(n);
        for (int i = 0; i < n; ++i) {
            res.push_back(nums[i][i] == '0' ? '1' : '0');
        }
        return res;
    }
};
```

---

### 2. Blog Article – “The Good, the Bad, and the Ugly of Leetcode 1980”

#### Title (SEO‑Friendly)
**“Leetcode 1980 – Find Unique Binary String: Java, Python, C++ Solutions + Interview‑Ready Guide”**

#### Meta Description
*Master Leetcode 1980 with clean Java, Python, and C++ code. Learn the optimal diagonalization trick, pitfalls to avoid, and how to ace this interview question.*

---

## Introduction

When interviewing for a software engineering role, you’ll encounter classic “find a missing element” problems. Leetcode **1980 – Find Unique Binary String** is one such puzzle that tests your reasoning about strings, bit manipulation, and algorithmic elegance. In this article we’ll:

1. **Explain the problem** in plain English.
2. Walk through **Naïve vs. Optimal** solutions, highlighting what’s “good,” “bad,” and “ugly.”
3. Provide **Java, Python, and C++** implementations that run in linear time.
4. Discuss edge cases, complexity, and interview‑friendly tips.

Let’s dive in!

---

## Problem Statement (Rephrased)

You’re given an array `nums` of `n` **unique** binary strings, each of length `n`. Your task: produce any binary string of length `n` that **does not** appear in `nums`.

> **Constraints**  
> - `1 ≤ n ≤ 16`  
> - Each string consists only of `'0'` and `'1'`.  
> - All strings in `nums` are distinct.

---

## The Good: Cantor’s Diagonal Trick

The problem is a textbook application of **Cantor’s diagonalization**. Think of `nums` as an `n × n` matrix of bits. By flipping the diagonal bits, you guarantee a new string that differs from every row.

### Why It Works

- Row `i` has bit `nums[i][i]`.  
- Our new string’s `i`‑th bit is the **opposite** of that diagonal bit.  
- Therefore, string `i` cannot match our new string at position `i`, so it cannot be identical to any row.

**Result:** Linear‑time, constant‑space (ignoring the output) algorithm.

---

## The Bad: Brute‑Force Enumeration

A naive solution would generate every possible binary string of length `n` (there are `2^n` possibilities) and check if it’s in `nums`. This is:

- **Time‑Complexity:** `O(2^n)` – infeasible even for `n = 16` (65 536 checks).
- **Space‑Complexity:** Potentially large if you store all candidates.

While this passes on Leetcode due to small `n`, it’s a textbook **O(2^n) bottleneck** and looks bad to interviewers.

---

## The Ugly: Over‑Complicated Hashing

Another “over‑engineering” route uses a `HashSet` to store all strings and then iteratively flips bits to find a missing one. It works, but:

- You allocate a `HashSet` of size `n`, which is unnecessary.
- You add complexity without any gain in readability or performance.
- The algorithm may appear “heavy” for such a simple problem.

### Takeaway

Stick to the diagonal trick – it’s short, clear, and mathematically guaranteed.

---

## Code Walkthrough

Below are concise, idiomatic solutions in the three major interview languages.

### 1. Java

```java
class Solution {
    public String findDifferentBinaryString(String[] nums) {
        int n = nums.length;
        StringBuilder sb = new StringBuilder(n);
        for (int i = 0; i < n; i++) {
            sb.append(nums[i].charAt(i) == '0' ? '1' : '0');
        }
        return sb.toString();
    }
}
```

*Why this is good:*
- Uses `StringBuilder` for O(n) string construction.
- No extra data structures.

### 2. Python

```python
class Solution:
    def findDifferentBinaryString(self, nums: List[str]) -> str:
        n = len(nums)
        return ''.join('1' if nums[i][i] == '0' else '0' for i in range(n))
```

*Pythonic flair:*
- List‑comprehension turns the loop into a single expression.

### 3. C++

```cpp
class Solution {
public:
    string findDifferentBinaryString(vector<string>& nums) {
        int n = nums.size();
        string res;
        res.reserve(n);
        for (int i = 0; i < n; ++i) {
            res.push_back(nums[i][i] == '0' ? '1' : '0');
        }
        return res;
    }
};
```

*C++ specifics:*
- `reserve(n)` avoids reallocations.
- Ternary operator keeps it compact.

---

## Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Cantor Diagonal | **O(n)** | **O(1)** (output excluded) |
| Brute Force | **O(2ⁿ)** | **O(1)** (output excluded) |
| HashSet Enumeration | **O(n)** | **O(n)** |

The optimal solution beats the alternatives by a factor of `2ⁿ` and needs no auxiliary storage.

---

## Edge Cases & Testing

1. **Minimum Size (`n = 1`)**  
   - `nums = ["0"]` → Output: `"1"`.  
   - `nums = ["1"]` → Output: `"0"`.

2. **All Bits Zero**  
   - `nums = ["00", "01"]` → Output: `"11"`.

3. **All Bits One**  
   - `nums = ["11", "10"]` → Output: `"00"`.

4. **Random Mix**  
   - Test with random binary strings of length `n`.  
   - Validate that the result is not in `nums`.

---

## Interview Tips

| Tip | Reason |
|-----|--------|
| **Explain Cantor’s diagonalization** | Shows mathematical insight. |
| **Mention linearity** | Highlights efficiency. |
| **Avoid unnecessary data structures** | Keeps solution lean. |
| **Show edge‑case handling** | Demonstrates thoroughness. |
| **Comment briefly** | Keeps code readable for recruiters. |

---

## Conclusion

Leetcode 1980 is deceptively simple once you recognize the underlying pattern: a diagonal flip guarantees a missing string. The diagonal trick is your “good” solution—fast, clean, and mathematically sound. Avoid brute force or over‑engineered hash solutions—they’re the “bad” and “ugly” ways that distract interviewers.

By mastering this problem, you’ll not only get the correct answer in a fraction of a second but also impress hiring managers with your algorithmic elegance and coding discipline.

Happy coding, and good luck landing that software engineering role!

---

### SEO Highlights

- **Keywords:** Leetcode 1980, Find Unique Binary String, Java solution, Python solution, C++ solution, coding interview, algorithm interview, Cantor's diagonalization, job interview coding, software engineering interview.
- **Meta Tags:** `find-unique-binary-string, leetcode-1980, interview-questions, coding-interview, algorithm, job-hunting, Java, Python, C++`.

Feel free to copy the code snippets, adapt the blog structure, and tweak the meta tags to match your personal brand or portfolio site. Good luck!