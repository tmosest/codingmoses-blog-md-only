---
title: LeetCode 2160. Minimum Sum of Four Digit Number After Splitting Digits - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2160 – Minimum Sum of Four‑Digit Number After Splitting Digits  
###  Java | Python | C++ | Algorithm | Interview Prep

---

### 🔍 Problem Statement  

You’re given a **positive integer** `num` consisting of **exactly four digits** (`1000 ≤ num ≤ 9999`).  
You must split the digits of `num` into two new integers `new1` and `new2` using **all** four digits.  
Leading zeros are allowed.

> **Goal:**  
> Return the **minimum possible** value of `new1 + new2`.

*Example*  
`num = 2932` → digits `[2, 9, 3, 2]`  
Possible pairs: `[29, 23]`, `[223, 9]`, `[2, 329]`, …  
Minimum sum = `29 + 23 = 52`.

---

### 🧠 Intuition

To keep the sum small, we want each number to be as **short** as possible and to use the **smallest digits** in the **high‑order positions**.

If we sort the digits in ascending order:

```
sorted = [d0, d1, d2, d3]   // d0 <= d1 <= d2 <= d3
```

The minimal sum is achieved by:

```
new1 = d0 * 10 + d2   // first and third smallest
new2 = d1 * 10 + d3   // second and largest
```

Why?  
- Each number gets one digit in the tens place (the smaller the better).
- The remaining digit goes to the units place.
- This pairing yields the smallest possible two‑digit numbers, hence the smallest sum.

---

### 🏎️ Complexity Analysis

| Metric | Complexity |
|--------|------------|
| **Time** | `O(1)` – we only sort four elements (`O(4 log 4)` ≈ constant). |
| **Space** | `O(1)` – constant additional memory. |

---

## 📦 Code Implementations

### Java

```java
import java.util.Arrays;

class Solution {
    public int minimumSum(int num) {
        int[] digits = new int[4];
        for (int i = 0; i < 4; i++) {
            digits[i] = num % 10;
            num /= 10;
        }
        Arrays.sort(digits);                      // ascending
        int num1 = digits[0] * 10 + digits[2];     // tens + units
        int num2 = digits[1] * 10 + digits[3];
        return num1 + num2;
    }
}
```

### Python

```python
class Solution:
    def minimumSum(self, num: int) -> int:
        digits = sorted([int(d) for d in str(num)])   # list of four ints
        num1 = digits[0] * 10 + digits[2]
        num2 = digits[1] * 10 + digits[3]
        return num1 + num2
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumSum(int num) {
        vector<int> digits;
        for (int i = 0; i < 4; ++i) {
            digits.push_back(num % 10);
            num /= 10;
        }
        sort(digits.begin(), digits.end());          // ascending
        int num1 = digits[0] * 10 + digits[2];
        int num2 = digits[1] * 10 + digits[3];
        return num1 + num2;
    }
};
```

---

## 📚 Blog Article: “The Good, The Bad, and the Ugly of Solving LeetCode 2160”

### Introduction

If you’re preparing for coding interviews, the LeetCode “Minimum Sum of Four‑Digit Number After Splitting Digits” problem is a perfect example of a **concise, yet subtle** challenge. It forces you to think about digit manipulation, optimization, and the pitfalls of brute‑force solutions. In this article, we’ll dissect the problem, walk through the optimal solution, highlight common mistakes (the “bad”), and discuss how to handle edge cases (the “ugly”).

### Why This Problem Matters

- **Algorithmic Thinking**: Demonstrates how sorting can be used to minimize sums.
- **Time & Space Constraints**: Shows that constant‑time solutions are achievable.
- **Interview Relevance**: Many interviewers ask variations of “split digits” problems.

### Problem Recap (Quick)

You’re given a 4‑digit integer `num`. Split its digits into two new numbers using **all** digits, allowing leading zeros, and return the **minimum** possible sum.

### The “Good” – Elegant & Optimal

#### 1. Sort the Digits

By sorting, we immediately know which digits are smallest and largest.  
Sorting four numbers is trivial (`O(1)` time).

#### 2. Pair the Smallest with the Next Smallest

Form `new1` using the smallest (`d0`) and third smallest (`d2`) digits,  
and `new2` using the second smallest (`d1`) and largest (`d3`).

Why this pairing?  
- It ensures each new number is **two digits** long (except when zeros appear).
- It places the smallest digits in the **tens place**, which has the highest weight.

#### 3. Return the Sum

Add the two numbers; that’s the answer.

This solution is **straightforward**, **readable**, and **optimal**.

### The “Bad” – Common Pitfalls

| Mistake | Why It’s Bad | Fix |
|---------|--------------|-----|
| **Brute‑force all permutations** | There are 4! = 24 ways, but still acceptable. However, generating all 2^4 partitions is overkill and increases time. | Use sorting + greedy pairing. |
| **Wrong digit extraction order** | Taking digits from left to right vs right to left can lead to wrong indices. | Consistently pop from the end or convert to string. |
| **Ignoring leading zeros** | Some people think they must avoid zeros; but the problem explicitly allows them. | Treat zeros like any other digit. |
| **Assuming each number must be 2‑digit** | Edge case `num=4009` → optimal split is `[4,9]` (one‑digit numbers). | The algorithm naturally handles this, as zeros contribute nothing. |

### The “Ugly” – Edge Cases & Tricky Inputs

| Input | Expected Output | Why It Can Trip You Up |
|-------|-----------------|------------------------|
| `num = 1000` | `1` (split `[1, 0]` and `[0, 0]`) | Two zeros in one number, but algorithm still works. |
| `num = 9999` | `99 + 99 = 198` | All digits equal – the sum is just twice the two‑digit number. |
| `num = 4009` | `13` | Leading zeros appear when forming `[4, 9]`. Ensure your code doesn’t accidentally drop zeros from the front. |

### Code Snippets (All Languages)

- **Java** – see above.  
- **Python** – see above.  
- **C++** – see above.

### What Recruiters Look For

- **Clarity**: A concise explanation of the algorithm.
- **Efficiency**: Knowing that constant‑time solutions exist.
- **Edge‑Case Awareness**: Handling inputs with zeros or repeated digits.

### Final Takeaway

LeetCode 2160 is deceptively simple but reveals a classic greedy strategy: *sort, then pair smallest with third smallest, largest with second smallest.* Mastering this problem demonstrates your ability to think algorithmically, write clean code, and anticipate edge cases—all key skills for a software engineering interview.

### Want More?  

- Explore other “digit manipulation” problems on LeetCode (e.g., **2164. Count the Number of Palindromic Subsequences**).
- Practice greedy algorithms in interviews (e.g., **Greedy Gift Givers**, **Maximum Subarray**).
- Build a personal portfolio of solutions on GitHub; recruiters love clean, well‑documented code.

Happy coding, and may your interview scores reflect this elegant solution! 🚀

---  

**SEO Tags**: LeetCode 2160, Minimum Sum of Four Digit Number, Algorithm Interview Question, Java Solution, Python Solution, C++ Solution, Greedy Algorithm, Interview Preparation, Coding Interview, Software Engineer Interview, Job Interview Tips.