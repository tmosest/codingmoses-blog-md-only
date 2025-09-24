---
title: LeetCode 400. Nth Digit - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 400 – *Nth Digit*  
**Solution in Java, Python & C++ + a deep‑dive interview blog**

---

### Problem Recap  
> Given an integer `n`, return the nth digit (1‑indexed) in the infinite sequence  
> `1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, …`.  
> **Constraints**: `1 ≤ n ≤ 2^31 – 1`.

> **Examples**  
> *Input*: `n = 3` → *Output*: `3`  
> *Input*: `n = 11` → *Output*: `0` (the 11th digit is the second digit of 10)

---

## 1. Intuition

The sequence is essentially the concatenation of all positive integers in decimal form.  
If we group the numbers by their *digit length* we get:

| digits | numbers in this group | total digits contributed |
|--------|----------------------|---------------------------|
| 1 | 1 … 9 | 9 × 1 = 9 |
| 2 | 10 … 99 | 90 × 2 = 180 |
| 3 | 100 … 999 | 900 × 3 = 2700 |
| … | … | … |

We can walk through these groups, subtracting their digit contributions from `n` until `n` falls inside a particular group.  
Once we know the group, we can locate the exact number and then extract the desired digit.

---

## 2. Algorithm (Pseudocode)

```
digits = 1
count  = 9          // numbers with 'digits' digits
start  = 1          // first number with 'digits' digits

while n > digits * count:
    n -= digits * count
    digits += 1
    count  = 9 * 10^(digits-1)
    start  = 10^(digits-1)

# n is now within the current group
number = start + (n-1)/digits          // integer division
digitIndex = (n-1) % digits            // 0‑based index inside 'number'
return digit of 'number' at position digitIndex
```

The trickiest part is the “digit of number” extraction.  
Convert `number` to a string (or use math) and pick the character at `digitIndex`.

---

## 3. Edge Cases

| Case | Why it matters | Handling |
|------|----------------|----------|
| `n` exactly equals the last digit of a group | Need to move to next group correctly | The loop uses `>` not `>=`, so we stay in the right group |
| `n` > 2,147,483,647 (int limit) | Potential overflow when calculating `digits * count` | Use `long` / `long long` for intermediate calculations |
| `n` very large (close to `Integer.MAX_VALUE`) | Ensures we don’t run into stack/overflow issues | All arithmetic is O(1), no recursion |

---

## 4. Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time   | **O(log n)** (≤ 10 iterations for 32‑bit int) | **O(log n)** | **O(log n)** |
| Space  | **O(1)** | **O(1)** | **O(1)** |

The algorithm is essentially a constant‑time arithmetic solution.

---

## 5. Alternative Approaches

| Approach | Pros | Cons |
|----------|------|------|
| **Brute force string building** (append numbers until length ≥ n) | Simple to code | Extremely slow for large n (O(n) time, O(n) memory) |
| **Binary search on number value** | Direct mapping between position and number | Slightly more complex, still O(log n) |
| **Math + Modulus without string conversion** | Avoids string conversion overhead | Slightly trickier to implement correctly |

For interviewers, the math‑based group approach is often the most appreciated due to its clarity and efficiency.

---

## 6. Interview Tips

1. **Explain the grouping idea first** – show you understand the structure of the sequence.  
2. **Discuss edge cases** – demonstrate defensive coding (overflow, last digit of a group).  
3. **Walk through an example** (e.g., n = 11) to prove your logic.  
4. **Mention time/space complexity** – interviewers love a concise Big‑O analysis.  
5. **Ask clarifying questions** if the interviewer isn’t explicit (e.g., “Are we allowed to use BigInteger?”).  

---

## 7. Code Snapshots

### Java

```java
public class Solution {
    public int findNthDigit(int n) {
        long digits = 1;          // current digit length
        long count  = 9;          // how many numbers have 'digits' digits
        long start  = 1;          // first number in this group

        // Find the group containing the nth digit
        while (n > digits * count) {
            n -= digits * count;
            digits++;
            count  = 9 * (long) Math.pow(10, digits - 1);
            start  = (long) Math.pow(10, digits - 1);
        }

        // Locate the exact number
        long number = start + (n - 1) / digits;
        int digitIndex = (int) ((n - 1) % digits);

        // Extract the digit
        String numStr = Long.toString(number);
        return numStr.charAt(digitIndex) - '0';
    }
}
```

### Python

```python
class Solution:
    def findNthDigit(self, n: int) -> int:
        digits = 1
        count  = 9
        start  = 1

        while n > digits * count:
            n -= digits * count
            digits += 1
            count = 9 * 10 ** (digits - 1)
            start = 10 ** (digits - 1)

        number = start + (n - 1) // digits
        digit_index = (n - 1) % digits
        return int(str(number)[digit_index])
```

### C++

```cpp
class Solution {
public:
    int findNthDigit(int n) {
        long long digits = 1;          // current digit length
        long long count  = 9;          // numbers with 'digits' digits
        long long start  = 1;          // first number in this group

        while (n > digits * count) {
            n -= digits * count;
            digits++;
            count = 9 * static_cast<long long>(pow(10, digits - 1));
            start = static_cast<long long>(pow(10, digits - 1));
        }

        long long number = start + (n - 1) / digits;
        int digitIndex = (n - 1) % digits;

        // Convert to string to fetch the digit
        std::string s = std::to_string(number);
        return s[digitIndex] - '0';
    }
};
```

> **Note**: In production code, you can avoid `pow` and `to_string` by using integer arithmetic and a simple loop, but the above keeps the logic clear.

---

## 8. SEO‑Optimized Blog Article: “The Good, The Bad, and The Ugly of LeetCode 400 – Nth Digit”

> **Keywords**: LeetCode 400, Nth Digit, coding interview, algorithm interview, Java solution, Python solution, C++ solution, job interview, technical interview, coding challenge

---

### Introduction

The *Nth Digit* problem (LeetCode 400) is a classic “medium” interview question that tests your ability to think mathematically and handle large integers efficiently. Many candidates stumble on it because they fall into the naïve string‑building trap. In this article we’ll dissect the problem, walk through the optimal solution, highlight pitfalls, and give you interview‑ready talking points.

---

### The Good – Why This Problem is Valuable

1. **Mathematical Thinking**: It forces you to view the sequence as groups of digit lengths, not as a continuous string.  
2. **Edge‑Case Handling**: Large `n` values push you to consider integer overflow and boundary conditions.  
3. **Constant‑Time Solution**: Demonstrates mastery of algorithmic optimization; you can solve it in *O(log n)* time.

---

### The Bad – Common Mistakes

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| Building the string until length ≥ `n` | Time: *O(n)*, Memory: *O(n)* | Use arithmetic grouping. |
| Using `int` for intermediate calculations | Overflow for `n` near `2^31‑1` | Switch to `long` / `long long`. |
| Forgetting that the digit index is 0‑based after division | Wrong answer for numbers with multiple digits | Subtract 1 before division / modulo. |
| Not handling the case when `n` lands exactly on the last digit of a group | Off‑by‑one error | Use `>` in the loop, not `>=`. |

---

### The Ugly – Why People Dislike It

- **Implicit Knowledge**: Some interviewers expect you to instantly recognize the grouping strategy.  
- **Math Heavy**: Candidates unfamiliar with combinatorial reasoning may feel lost.  
- **Multiple Languages**: You might be asked to write the solution in Java, Python, or C++ on the spot, testing language‑specific nuances (e.g., `pow`, `to_string`, integer division).

---

### Interview‑Ready Talking Points

1. **State the Problem Clearly**  
   “We’re looking for the nth digit in the infinite concatenation of all positive integers.”

2. **Explain the Grouping Strategy**  
   “Digits come in blocks: 1‑digit numbers give 9 digits, 2‑digit numbers give 180 digits, and so on.”

3. **Walk Through an Example**  
   “If `n = 11`, we subtract 9 (1‑digit block) → `n = 2`. It falls in the 2‑digit block. The 2nd digit of the first 2‑digit number (10) is 0.”

4. **Complexity**  
   “Only up to 10 iterations for a 32‑bit integer; O(1) space.”

5. **Edge Cases**  
   “If `n` is exactly the last digit of a block, we stay in the same block because of the ‘>’ condition.”

---

### Conclusion

Mastering LeetCode 400 shows recruiters you can:

- Think beyond brute force.  
- Apply combinatorial math to reduce time complexity.  
- Safely handle large integers.  

With the Java, Python, and C++ solutions above, you’re ready to ace this question in any coding interview. Good luck, and happy coding! 🚀

---

*If you found this article helpful, share it on LinkedIn or Twitter to boost your personal brand. For more interview prep, subscribe to our newsletter.*