---
title: LeetCode 3345. Smallest Divisible Digit Product I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 3345 – *Smallest Divisible Digit Product I*  
## Java, Python & C++ Solutions + A Deep‑Dive Blog Post

> **Want to land your next software‑engineering interview?**  
> This article covers a real LeetCode problem, walks through the *good*, the *bad*, and the *ugly* of typical solutions, and gives you ready‑to‑copy code in three popular languages.  
> We’ll also give you a ready‑made blog post that’s fully SEO‑optimized for keywords like *LeetCode 3345*, *coding interview*, *Java solution*, *Python solution*, *C++ solution*, and *job interview coding*.

---

## Problem Statement

**LeetCode 3345 – Smallest Divisible Digit Product I**

You are given two integers `n` and `t`.  
Return the smallest integer `x` (≥ `n`) such that the product of its decimal digits is divisible by `t`.

**Constraints**

| Variable | Range | Notes |
|----------|-------|-------|
| `n` | 1 … 100 | tiny! |
| `t` | 1 … 10  | tiny! |

**Examples**

| Input | Output | Reason |
|-------|--------|--------|
| `n = 10`, `t = 2` | `10` | 1 × 0 = 0, and 0 % 2 = 0 |
| `n = 15`, `t = 3` | `16` | 1 × 6 = 6, and 6 % 3 = 0 |

---

## Intuition

Because `n ≤ 100` and `t ≤ 10`, the search space is tiny.  
A neat observation: **you will always find a valid number in the interval `[n, n+10]`.**  
Therefore a brute‑force loop over at most 11 candidates is fast enough and far easier than trying to build the number digit by digit.

---

## The Good

| Aspect | Why It’s Good |
|--------|--------------|
| **Simplicity** | One for‑loop, one digit‑product calculation. |
| **Deterministic runtime** | Always checks ≤ 11 numbers → O(1) time, O(1) space. |
| **No special libraries** | Works on any Java, Python, or C++ compiler. |

---

## The Bad

| Aspect | Why It’s Bad |
|--------|--------------|
| **Not generalisable** | The `+10` trick only works because of the constraints. |
| **Redundant product calculation** | Recomputes the product for each candidate from scratch. |

---

## The Ugly

| Aspect | Why It’s Ugly |
|--------|--------------|
| **Zero handling** | A product of digits can be zero (any digit zero → product zero). |
| **Hard‑coded bound** | If constraints change, you’ll break the solution. |

---

## Full Code (Ready to copy)

### Java (LeetCode‑style)

```java
public class Solution {
    public int smallestNumber(int n, int t) {
        for (int i = n; i <= n + 10; i++) {
            int product = 1;
            int x = i;
            while (x > 0) {
                product *= x % 10;
                x /= 10;
            }
            if (product % t == 0) {
                return i;
            }
        }
        // The problem guarantees a solution, but we keep a fallback.
        return -1;
    }
}
```

### Python 3

```python
class Solution:
    def smallestNumber(self, n: int, t: int) -> int:
        for i in range(n, n + 11):
            product = 1
            for d in str(i):
                product *= int(d)
            if product % t == 0:
                return i
```

*Python‑one‑liner (the LeetCode editorial trick)*

```python
class Solution:
    def smallestNumber(self, n: int, t: int) -> int:
        return next(i for i in range(n, n+11) if eval('*'.join(str(i))) % t == 0)
```

### C++ (LeetCode‑style)

```cpp
class Solution {
public:
    int smallestNumber(int n, int t) {
        for (int i = n; i <= n + 10; ++i) {
            int prod = 1;
            int x = i;
            while (x) {
                prod *= x % 10;
                x /= 10;
            }
            if (prod % t == 0) return i;
        }
        return -1; // unreachable under constraints
    }
};
```

---

## Unit Test (Python)

```python
def test():
    s = Solution()
    assert s.smallestNumber(10, 2) == 10
    assert s.smallestNumber(15, 3) == 16
    assert s.smallestNumber(1, 1)  == 1
    assert s.smallestNumber(99, 10) == 100  # product 0
    print("All tests passed.")

test()
```

---

## Performance Analysis

| Language | Time Complexity | Space Complexity |
|----------|-----------------|------------------|
| Java | `O(1)` (≤ 11 iterations) | `O(1)` |
| Python | `O(1)` | `O(1)` |
| C++ | `O(1)` | `O(1)` |

Because `n ≤ 100`, the loop will never exceed 11 iterations.  
Each iteration multiplies at most 3 digits → constant work.

---

## Edge‑Case Checklist

1. **Zero digit** – product becomes 0 → always divisible by any `t`.  
2. **n itself is valid** – the loop will return immediately.  
3. **t = 1** – any product works; return `n`.  
4. **Large t (10)** – still safe because product may be 0 or 10‑divisible.  

---

## Lessons Learned

- **Leverage constraints**: Knowing `n` and `t` are tiny turns a seemingly “hard” problem into trivial brute force.  
- **Avoid premature optimisation**: The +10 trick is a perfectly fine optimisation for this problem.  
- **Always include a fallback** (`return -1`) to satisfy static analysis even though the problem guarantees a solution.  
- **Write readable code** – interviewers value clarity over cleverness when constraints allow it.  

---

## SEO‑Optimized Blog Post

> **Title**: LeetCode 3345 – Smallest Divisible Digit Product I: Java, Python & C++ Solutions + Interview Tips  
> **Meta Description**: Master LeetCode 3345 with clean Java, Python, and C++ code. Learn the algorithm, edge cases, and interview tricks for landing your next software‑engineering job.  
> **Keywords**: LeetCode 3345, Smallest Divisible Digit Product I, Java solution, Python solution, C++ solution, coding interview, algorithm interview, job interview coding, competitive programming, interview tips.  

### 1. Introduction

*What’s LeetCode 3345?*  
It’s a classic “small numbers, simple math” problem that tests your ability to translate constraints into efficient code. If you’re prepping for a technical interview, solving it in a clean, language‑agnostic way demonstrates a solid grasp of problem‑solving fundamentals.

### 2. Problem Breakdown

Explain the problem, constraints, and why a naïve approach works here. Mention the hidden “+10” trick that guarantees a solution.

### 3. Why Brute‑Force Works

Highlight the small domain (`n ≤ 100`, `t ≤ 10`) and show how a loop over `[n, n+10]` is both correct and optimal.

### 4. The Implementation in 3 Languages

Show the Java, Python, and C++ snippets side‑by‑side. Emphasise readability and highlight key lines (product calculation, loop bounds).

### 5. Edge Cases & Pitfalls

Zero digits, `t = 1`, overflow (not an issue here), and the importance of a fallback return.

### 6. Time & Space Complexity

Quick table – all `O(1)` – ideal for interviewers.

### 7. Interview Tips

- **Ask clarifying questions**: “Is there a guarantee of a solution?”  
- **Explain your logic**: “Because `n ≤ 100`, we only need to check up to `n+10`.”  
- **Talk about edge cases**: “If a digit is 0, the product is 0, which is divisible by any `t`.”  

### 8. Bonus: A One‑Line Python Trick

Show the `eval('*'.join(str(i)))` trick that’s elegant but potentially risky for production code.

### 9. Final Takeaway

You’ve built a reusable, interview‑ready solution that demonstrates your ability to read constraints, pick the simplest algorithm, and implement it cleanly in Java, Python, and C++.

---

## Call to Action

Want to impress hiring managers?  
- Clone this repo.  
- Run the test suite.  
- Add your own edge‑case tests.  
- Share the solution on LinkedIn with the hashtag `#LeetCode3345`.

Happy coding and good luck on your next interview! 🚀