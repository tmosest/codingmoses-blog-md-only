---
title: LeetCode 2457. Minimum Addition to Make Integer Beautiful - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2457. Minimum Addition to Make Integer Beautiful  
**Difficulty:** Medium  

> *“An integer is beautiful if the sum of its digits is ≤ `target`.  
> Find the smallest non‑negative integer `x` such that `n + x` is beautiful.”*  

Below you’ll find a complete, production‑ready solution in **Java**, **Python**, and **C++**.  
After the code you’ll read a short, SEO‑optimised blog article that explains the
algorithm, discusses the good, the bad and the ugly, and shows why it’s a great
topic for interview prep.

---

## 1. Code

### 1.1 Java

```java
/**
 * 2457. Minimum Addition to Make Integer Beautiful
 * LeetCode problem – Medium
 */
class Solution {

    /** Helper that returns the sum of the decimal digits of n */
    private int digitSum(long n) {
        int sum = 0;
        while (n > 0) {
            sum += (int) (n % 10);
            n /= 10;
        }
        return sum;
    }

    public long makeIntegerBeautiful(long n, int target) {
        long original = n;
        long power = 1;                 // 10^k – the current block that we will increment

        while (digitSum(n) > target) {
            // 1. Move to the next multiple of power
            n = n / 10 + 1;             // integer division – drop the last digit, add 1
            // 2. Restore the trailing zeros
            n *= power;
            // 3. Increase the block for the next iteration
            power *= 10;
        }

        return n - original;   // the smallest x that makes the number beautiful
    }
}
```

---

### 1.2 Python

```python
# 2457. Minimum Addition to Make Integer Beautiful
# LeetCode – Medium
class Solution:
    def makeIntegerBeautiful(self, n: int, target: int) -> int:
        original = n
        power = 1

        def digit_sum(x: int) -> int:
            return sum(int(d) for d in str(x))

        while digit_sum(n) > target:
            n = n // 10 + 1        # jump to next block
            n *= power
            power *= 10

        return n - original
```

---

### 1.3 C++

```cpp
// 2457. Minimum Addition to Make Integer Beautiful
// LeetCode – Medium
class Solution {
public:
    // helper: sum of decimal digits
    static int digitSum(long long x) {
        int sum = 0;
        while (x > 0) {
            sum += static_cast<int>(x % 10);
            x /= 10;
        }
        return sum;
    }

    long long makeIntegerBeautiful(long long n, int target) {
        long long original = n;
        long long power = 1;               // 10^k

        while (digitSum(n) > target) {
            n = n / 10 + 1;                // go to the next block
            n *= power;                   // fill the lower digits with 0
            power *= 10;                   // increase block size
        }
        return n - original;
    }
};
```

All three solutions run in **O(d log d)** time, where *d* is the number of digits of *n*  
(maximum 13 for `n ≤ 10¹²`), and use **O(1)** extra space.

---

## 2. Blog Article – “The Good, The Bad, and The Ugly of Minimum Addition to Make Integer Beautiful”

### 2.1 Introduction

When preparing for a software‑engineering interview, you’ll encounter **LeetCode** problems that test your ability to think greedily, use arithmetic tricks, and reason about digit‑wise operations.  
Problem **2457 – Minimum Addition to Make Integer Beautiful** is a classic example.  
In this article we dissect the algorithm, highlight its pros and cons, and show you how to explain it in an interview.

**Keywords**: *LeetCode 2457, minimum addition to make integer beautiful, interview algorithm, greedy, digit sum, Java, Python, C++*

---

### 2.2 The Good – Why This Problem Is a Great Interview Topic

1. **Clear problem statement**  
   *Given* `n` and `target`, *return* the minimal `x` such that the digit sum of `n + x` ≤ `target`.  
   There is a single, well‑defined answer.

2. **Encourages greedy thinking**  
   The optimal solution is to *round up* `n` to the next multiple of `10`, then `100`, then `1000`, etc.  
   This demonstrates that you can make a locally optimal choice (increment the lowest possible digit) that leads to a globally optimal result.

3. **Fast and clean implementation**  
   The solution is about 10 lines of code in each language.  
   You can finish it before the interview is over and focus on explaining the reasoning.

4. **Edge‑case handling**  
   The constraints guarantee that a solution exists, but you still need to handle cases where the digit sum is already ≤ `target`.  
   Demonstrating how to detect and shortcut that case shows careful thought.

5. **Language‑agnostic**  
   The algorithm uses only integer arithmetic and simple loops – perfect for Java, Python, or C++ interviews.

---

### 2.3 The Bad – Common Pitfalls

| Pitfall | Why it’s bad | How to avoid |
|---------|--------------|--------------|
| **Using modulo 10** on the whole number repeatedly | Inefficient; can overflow if `n` is large | Divide by 10 to drop digits and use a separate `power` multiplier |
| **Assuming you can add 1 to the whole number** | Not respecting digit boundaries; may increase digit sum | Increment the *prefix* and set trailing digits to zero |
| **Forgetting to update `power`** | Infinite loop or wrong answer | After each iteration multiply `power` by 10 |
| **Using recursion without memoisation** | Stack overflow for large numbers | Iterative loop is simpler and safer |

---

### 2.4 The Ugly – Why Some Solutions Go Wrong

Sometimes interviewees attempt a brute‑force “try every `x`” approach or use a **dynamic programming** table of size `target`.  
While theoretically correct, these approaches are overkill:

- **Brute‑force** runs in O(10¹²) in the worst case, impossible under time limits.
- **DP** over digit sum is unnecessary; the greedy “round‑up” property is far simpler.
- Using **strings** to manipulate digits (Python `str(n)`) can lead to subtle bugs when converting back to integer.

A well‑structured greedy algorithm, as presented above, avoids all of these pitfalls and keeps the code readable and maintainable.

---

### 2.5 Step‑by‑Step Walkthrough

Let’s walk through the algorithm with the example `n = 467`, `target = 6`.

| Iteration | `n` before | `digitSum(n)` | Action | `n` after |
|-----------|------------|---------------|--------|-----------|
| 0 | 467 | 17 | >6 → round up to next 10’s multiple | 470 |
| 1 | 470 | 11 | >6 → round up to next 100’s multiple | 500 |
| 2 | 500 | 5 | ≤6 → stop |

The answer is `500 - 467 = 33`, exactly as the problem states.

**Why does this work?**  
When the sum of digits is too high, the only way to lower it is to increase the most significant digit that can affect the sum while resetting the less significant digits to zero.  
Incrementing the last digit would not reduce the sum enough, so we “carry” to the next higher place value.

---

### 2.6 Complexity Analysis

| Language | Time | Space |
|----------|------|-------|
| Java, Python, C++ | **O(d log d)** (d = number of digits ≤ 13) | **O(1)** |

Since `d` is tiny, the algorithm runs in microseconds – perfect for an interview setting.

---

### 2.7 How to Explain It in an Interview

1. **State the observation**  
   *If the digit sum is too large, we can safely increment the next higher place value and zero out the lower digits without ever increasing the digit sum beyond necessity.*

2. **Outline the greedy step**  
   *At each step, replace `n` with `(n // 10 + 1) * power`, where `power` starts at 1 and multiplies by 10 each iteration.*

3. **Show the stopping condition**  
   *When `digitSum(n) ≤ target`, we’re done.*

4. **Present the final answer**  
   *Return `n - original_n`.*

4. **Mention edge cases**  
   *If the digit sum of the original `n` is already fine, return 0 immediately.*

5. **Optional – Code‑walk**  
   *Briefly run through the code lines, especially the division, carry‑over, and power update.*

---

### 2.8 Conclusion

Problem 2457 teaches you a neat greedy trick that’s both elegant and fast.  
It’s a **must‑know** for any candidate aiming to ace data‑structure and algorithm questions on *LeetCode* or in *technical interviews*.  

By mastering this problem you’ll:

- Gain confidence in greedy reasoning,
- Learn how to keep code concise across Java, Python, and C++,
- Demonstrate clear problem‑solving communication to interviewers.

Happy coding and good luck with your interview prep!