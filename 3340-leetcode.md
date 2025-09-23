---
title: LeetCode 3340. Check Balanced String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 3340 – Check Balanced String  
> **Good, Bad & Ugly** – A Practical Guide to Nail This Interview Question (and land your dream job)

---

### 📌 TL;DR

- **Problem**: For a digit‑only string `num`, check if the sum of digits at even indices equals the sum of digits at odd indices.
- **Solution**: One linear scan, keeping two running totals.
- **Time**: `O(n)`  
- **Space**: `O(1)`
- **Languages**: Java, Python, C++
- **Why It Matters**: Simple yet perfect for demonstrating O(n) thinking, clean code, and interview fundamentals.

---

## 1. Problem Statement (LeetCode)

> **Check Balanced String**  
> *Difficulty:* Easy  
> **Definition**  
> A string of digits is *balanced* if the sum of digits at even indices equals the sum of digits at odd indices.  
> Return `true` if `num` is balanced, otherwise `false`.  
> **Constraints**  
> - `2 <= num.length <= 100`  
> - `num` consists only of digits.

*Examples*

| Input | Output | Explanation |
|-------|--------|-------------|
| `"1234"` | `false` | Even sum = 1+3=4, Odd sum = 2+4=6 |
| `"24123"` | `true`  | Even sum = 2+1+3=6, Odd sum = 4+2=6 |

---

## 2. The “Good” – Why This Problem is Ideal for Interviews

| Reason | Why It Helps Your Career |
|--------|--------------------------|
| **Clear, deterministic** | No ambiguous cases – perfect for first‑round screens. |
| **One‑pass algorithm** | Demonstrates O(n) reasoning, a staple in coding interviews. |
| **Minimal edge‑case complexity** | Focuses your attention on algorithmic clarity, not defensive coding. |
| **Language‑agnostic** | You can solve it in Java, Python, or C++ – pick the language most comfortable for you. |
| **Opportunity for a clean O(1) space solution** | Shows you understand the difference between algorithmic and auxiliary space. |

---

## 3. The “Bad” – Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Using `int` overflow on large strings** | Not an issue here due to max length 100, but always be mindful of integer limits. |
| **Treating indices as 1‑based** | Remember that array indices start at 0 in most languages. |
| **Confusing “even indices” with “even digits”** | Even indices means the position (0, 2, 4, …), not the digit’s parity. |
| **Using heavy data structures** | No need for lists or maps; a simple loop is enough. |
| **Ignoring whitespace/invalid input** | LeetCode guarantees digit‑only strings; production code should still validate. |

---

## 4. The “Ugly” – Advanced Variations & Extensions

| Variation | How to Extend the Solution |
|-----------|---------------------------|
| **Large string (10⁶+)** | Still O(n) with constant space, but watch for `long` vs `int` if sum can exceed `Integer.MAX_VALUE`. |
| **Streaming input** | Process characters as they arrive; maintain two sums without storing the whole string. |
| **Multiple queries** | If you’re asked for many strings, pre‑compute prefix sums to answer each query in O(1). |
| **Generalization to arbitrary positions** | Replace “even/odd” with any mod condition; keep two sums accordingly. |

---

## 5. Algorithm Walkthrough

1. **Initialize two counters**: `evenSum = 0`, `oddSum = 0`.
2. **Iterate over the string** character by character.
3. **Convert** each character to its numeric value (`ch - '0'` in C++/Java, `int(ch)` in Python).
4. **Add** to `evenSum` if the index is even, otherwise to `oddSum`.
5. After the loop, **return** `evenSum == oddSum`.

No auxiliary data structures are needed—just a single loop and two integer variables.

---

## 6. Code Solutions

> ✅ All three solutions run in **O(n)** time and **O(1)** space.

### 6.1 Java

```java
class Solution {
    public boolean isBalanced(String num) {
        int evenSum = 0, oddSum = 0;
        for (int i = 0; i < num.length(); i++) {
            int digit = num.charAt(i) - '0';
            if ((i & 1) == 0) evenSum += digit;
            else oddSum += digit;
        }
        return evenSum == oddSum;
    }
}
```

### 6.2 Python

```python
class Solution:
    def isBalanced(self, num: str) -> bool:
        even_sum, odd_sum = 0, 0
        for idx, ch in enumerate(num):
            digit = int(ch)
            if idx % 2 == 0:
                even_sum += digit
            else:
                odd_sum += digit
        return even_sum == odd_sum
```

### 6.3 C++

```cpp
class Solution {
public:
    bool isBalanced(string num) {
        int evenSum = 0, oddSum = 0;
        for (int i = 0; i < num.size(); ++i) {
            int digit = num[i] - '0';
            if (i % 2 == 0) evenSum += digit;
            else oddSum += digit;
        }
        return evenSum == oddSum;
    }
};
```

---

## 7. Alternative Approach – Using `map` / `reduce` (Python only)

```python
def is_balanced(num: str) -> bool:
    even = sum(int(num[i]) for i in range(0, len(num), 2))
    odd  = sum(int(num[i]) for i in range(1, len(num), 2))
    return even == odd
```

*Pros*: Very concise.  
*Cons*: Slightly higher constant overhead; not ideal for performance‑critical interviews.

---

## 8. Interview Tips

| Tip | Why It Helps |
|-----|--------------|
| **State your approach** | "I'll use a single pass O(n) scan" – shows clear algorithmic thinking. |
| **Talk about time/space** | Demonstrates understanding of Big‑O notation. |
| **Ask clarifying questions** | Confirm whether indices are 0‑based or 1‑based; show thoroughness. |
| **Mention edge cases** | Although minimal here, you can discuss what happens if the string length is odd/even. |
| **Discuss testing** | Run your code on the provided examples, and also on edge cases like `"00"` and `"9999"`. |
| **Show clean code** | Prefer readability over micro‑optimizations in interviews. |

---

## 9. SEO‑Optimized Conclusion

> Looking to land a front‑end, back‑end, or full‑stack developer role? Mastering **LeetCode 3340 – Check Balanced String** proves you can think *O(n)*, write clean Java/Python/C++ code, and avoid common interview pitfalls. Share this solution on your GitHub, blog, or portfolio and watch recruiters notice your algorithmic sharpness.  

**Keywords**: LeetCode, Check Balanced String, Balanced String, Java interview, Python interview, C++ interview, coding interview prep, algorithm, O(n), O(1), job interview, software engineer, technical recruiter.

---  

**Happy coding, and good luck on your next interview!**