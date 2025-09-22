---
title: LeetCode 1837. Sum of Digits in Base K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1837 – Sum of Digits in Base K  
### Easy | `int sumBase(int n, int k)` – Java | Python | C++

| Language | Time | Space |
|----------|------|-------|
| **Java** | O(logₖ n) | O(1) |
| **Python** | O(logₖ n) | O(1) |
| **C++** | O(logₖ n) | O(1) |

> **Why this problem?**  
> *It’s a classic interview‑style question that tests your understanding of base conversion, modulo arithmetic, and loop invariants. The solution is elegant, O(log n) and uses constant space, making it a perfect “must‑know” for technical interviews.*

---

## 1️⃣ Problem Statement (LeetCode 1837)

> **Input**  
> * `n` – a positive integer in base‑10 (1 ≤ n ≤ 100)  
> * `k` – the target base (2 ≤ k ≤ 10)  

> **Output**  
> Sum of the digits of `n` when expressed in base‑`k`.  
> The sum is returned in base‑10.

> **Example**  
> ```text
> n = 34, k = 6  → 34₁₀ = 54₆  → 5 + 4 = 9
> n = 10, k = 10 → 10₁₀ = 10₁₀ → 1 + 0 = 1
> ```

---

## 2️⃣ The Brute‑Force Idea

The most naïve way is to:

1. Convert `n` to base‑`k` and store it as a string/array of digits.
2. Iterate over the array, convert each digit back to an integer, and sum them.

While correct, this adds extra space for the intermediate string/array and unnecessary overhead.

> **Bad:**  
> *Extra memory.*  
> *Redundant conversions.*  
> *Not O(log n) in the strictest sense – although still O(log n) time, the constant factors are higher.*

---

## 3️⃣ The Optimal O(log n) Solution

We can compute the digits on‑the‑fly using modulo and integer division:

```text
while n > 0:
    digit = n % k   // remainder → current least‑significant digit in base k
    sum   += digit
    n     //= k      // move to the next higher digit
```

* The loop runs as many times as there are digits in base‑`k`, which is ⌊logₖ n⌋ + 1.
* No auxiliary data structures are required → O(1) space.

### Edge Cases

| n | k | Result | Reason |
|---|---|--------|--------|
| 1 | 2 | 1 | 1 in any base is 1. |
| 100 | 10 | 1 | 100₁₀ = 100₁₀ → 1 + 0 + 0 = 1. |
| 100 | 2 | 1 | 100₁₀ = 1100100₂ → 1+1+0+0+1+0+0 = 3 (not 1) → shows importance of correct modulo/ division. |

> **Good:**  
> *Constant space.*  
> *Clear, readable logic.*  
> *Directly uses arithmetic operations.*  

> **Ugly (if you mis‑apply integer division or forget that `k` is <= 10):**  
> *Using floating‑point division can lead to precision issues.*  
> *Off‑by‑one errors when initializing `sum` or the loop condition.*

---

## 4️⃣ Full Implementations

### 4.1 Java

```java
// 1837. Sum of Digits in Base K
// Time:  O(log_k n)
// Space: O(1)

class Solution {
    public int sumBase(int n, int k) {
        int sum = 0;
        while (n != 0) {
            sum += n % k;   // current least‑significant digit
            n /= k;         // shift right in base k
        }
        return sum;
    }
}
```

> *Why this works:*  
> `n % k` extracts the current digit in base k. Dividing by `k` discards that digit, preparing the next iteration.

---

### 4.2 Python

```python
# 1837. Sum of Digits in Base K
# Time:  O(log_k n)
# Space: O(1)

class Solution:
    def sumBase(self, n: int, k: int) -> int:
        total = 0
        while n:
            total += n % k
            n //= k
        return total
```

> *Pythonic notes:*  
> `//` guarantees integer division. No extra imports needed.

---

### 4.3 C++

```cpp
// 1837. Sum of Digits in Base K
// Time:  O(log_k n)
// Space: O(1)

class Solution {
public:
    int sumBase(int n, int k) {
        int sum = 0;
        while (n > 0) {
            sum += n % k;   // remainder: digit in base k
            n /= k;         // drop the processed digit
        }
        return sum;
    }
};
```

> *Why no long long needed:*  
> Constraints guarantee `n` ≤ 100, so `int` is sufficient.  
> For larger inputs, you could cast to `long long`.

---

## 5️⃣ Running the Code

| Language | Test Input | Output |
|----------|------------|--------|
| Java | `new Solution().sumBase(34, 6)` | `9` |
| Python | `Solution().sumBase(34, 6)` | `9` |
| C++ | `Solution().sumBase(34, 6)` | `9` |

---

## 6️⃣ SEO‑Friendly Blog Article

---

### 📰 “Sum of Digits in Base K – The Interview‑Ready LeetCode 1837 Tutorial”

> **Meta Title**: Sum of Digits in Base K – Java, Python, C++ Solutions (LeetCode 1837)  
> **Meta Description**: Master LeetCode 1837 – Sum of Digits in Base K. Learn O(log n) Java, Python, and C++ solutions, detailed explanations, edge cases, and interview prep tips.

---

#### Introduction

When you see **“Sum of Digits in Base K”** in a technical interview, the first question that pops up is: *“Can you convert a number to a different base and sum its digits?”* This seemingly simple task actually showcases a candidate’s grasp of number theory, loops, and algorithmic optimization.

In this article, we walk through the problem statement, give a clean O(log n) solution in **Java, Python, and C++**, dissect the logic, cover edge cases, and sprinkle some interview‑friendly insights. By the end, you’ll be ready to impress hiring managers with your problem‑solving chops.

---

#### 1. Problem Recap

| Parameter | Constraints | Meaning |
|-----------|-------------|---------|
| `n` | 1 ≤ n ≤ 100 | Number in base‑10 |
| `k` | 2 ≤ k ≤ 10 | Target base |

**Goal**: Return the sum of the digits of `n` after converting `n` from base‑10 to base‑`k`.

*Example*: `n = 34`, `k = 6` → `34₁₀ = 54₆` → sum = `5 + 4 = 9`.

---

#### 2. The Optimal O(log n) Strategy

##### 2.1 Why O(log n)?

The number of digits of `n` in base `k` is approximately `logₖ n`. Each iteration of the algorithm handles one digit, so the loop count is proportional to that logarithm.

##### 2.2 Step‑by‑Step

1. **Initialize** `sum = 0`.
2. **While** `n > 0`:
   * `digit = n % k` (extract the current least‑significant digit).
   * `sum += digit`.
   * `n /= k` (integer division → remove the processed digit).
3. **Return** `sum`.

This loop stops when all digits have been processed (`n` becomes 0). No auxiliary arrays or strings are needed, guaranteeing **O(1)** space.

---

#### 3. Code Samples

*See the “Full Implementations” section above for Java, Python, and C++ code.*

---

#### 4. Edge Cases & Common Pitfalls

| Case | Why it matters | How to avoid mistakes |
|------|----------------|-----------------------|
| `n = 1` | Minimal input | Works naturally; loop runs once. |
| `k = 2` | Binary conversion | Still works; modulo by 2 is fast. |
| `n` divisible by `k` | Trailing zeros in base `k` | Modulo yields 0, but sum remains correct. |
| Integer division vs. float division | Wrong digit extraction | Use integer division (`/` in Java/C++, `//` in Python). |

---

#### 5. Interview Tips

1. **Explain the invariant**: Each iteration processes the least‑significant digit.
2. **Mention time & space complexity**: “O(log n) time, O(1) space.”
3. **Show a quick mental check**: For `n=34`, `k=6`, walk through two iterations.
4. **Discuss constraints**: No need for big‑int handling with given limits, but note that for larger `n`, you might use `long long` in C++ or `long` in Java.

---

#### 6. Why This Problem Is a “Must‑Know”

* **Base conversion** is a fundamental concept that appears in many interviews (e.g., “convert decimal to binary”).  
* **Modulo & division** operations test familiarity with integer arithmetic.  
* The solution is *concise*, *optimal*, and demonstrates good coding style—exactly what recruiters look for.

---

#### 7. Final Thoughts

Mastering LeetCode 1837 is more than just writing a loop; it’s about understanding the relationship between number bases and algorithmic efficiency. By implementing the same logic across **Java, Python, and C++**, you show versatility and depth—qualities highly valued in software engineering roles.

---

#### 🚀 Next Steps

1. **Practice**: Run the solution with different `n` and `k` values.  
2. **Optimize**: Try to express the same idea in functional style (e.g., using recursion).  
3. **Share**: Add this problem to your GitHub portfolio with clear comments and a README.

Happy coding, and best of luck in your next interview! 🎉

---

> **Keywords**: LeetCode 1837, Sum of Digits in Base K, Java solution, Python solution, C++ solution, interview question, algorithmic interview, time complexity, space complexity, job interview, technical interview, coding interview, number base conversion, modulo operation.

---