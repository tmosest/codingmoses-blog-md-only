---
title: LeetCode 400. Nth Digit - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 400. Nth Digit – The Good, The Bad, and The Ugly  
**A Deep‑Dive into the LeetCode Interview Classic**

> **Keywords:** `Nth Digit`, `LeetCode 400`, `coding interview`, `Java solution`, `Python solution`, `C++ solution`, `O(1)` algorithm, `job interview`, `software engineer`

---

### 1. What is the Problem?

> *Given an integer `n`, return the nth digit of the infinite integer sequence*  
> `1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, …`

The sequence is simply all positive integers concatenated together:  
`1234567891011121314…`  
We must return the **digit** at position `n` (1‑based).

**Examples**

| n | Result | Explanation |
|---|--------|-------------|
| 3 | `3` | The 3rd digit is the number 3 itself. |
| 11 | `0` | The 11th digit falls inside number 10 → `1` `0`. |

**Constraints**

```
1 ≤ n ≤ 2^31 - 1  (≈ 2.1 × 10^9)
```

---

### 2. Why is it a “Must‑Know” Interview Question?

* **Mathematical Insight** – It tests whether you can see the pattern: 1‑digit numbers, 2‑digit numbers, etc.  
* **O(1) vs O(n)** – A naïve simulation (concatenate until length ≥ n) will TLE on the upper limit.  
* **Language‑agnostic** – You can solve it in any language; the core idea is the same.  
* **Common Follow‑up** – “What if we want the n‑th *number*?” (LeetCode 402).

---

## 3. The Elegant Solution (O(1))

1. **Group by digit length**  
   * 1‑digit numbers: 1‑9 → 9 numbers → 9 × 1 = 9 digits  
   * 2‑digit numbers: 10‑99 → 90 numbers → 90 × 2 = 180 digits  
   * 3‑digit numbers: 100‑999 → 900 numbers → 900 × 3 = 2700 digits  
   * … and so on.

2. **Find the group that contains the nth digit**  
   Subtract the number of digits per group from `n` until `n` ≤ digits in the current group.

3. **Locate the exact number**  
   *The offset* inside the group gives the exact integer:
   ```text
   number = start_of_group + (n-1)/digitLength
   ```
   where `start_of_group` is 1, 10, 100, …

4. **Extract the required digit**  
   Convert the `number` to a string (or use arithmetic) and pick the `(n-1)%digitLength`‑th character.

Because the loop runs at most 10 times (digit lengths 1‑10 for 32‑bit ints), the runtime is O(1).

---

## 4. Code Implementations

Below are clean, production‑ready implementations for **Java**, **Python**, and **C++**. Each follows the same O(1) logic.

---

### 4.1 Java

```java
/**
 * LeetCode 400 – Nth Digit
 * 
 * Time Complexity : O(1) – at most 10 iterations
 * Space Complexity: O(1)
 */
class Solution {
    public int findNthDigit(int n) {
        long digitLen = 1;          // current digit length (1,2,3,…)
        long count = 9;             // numbers in the current group
        long start = 1;             // first number in the current group

        // 1. Find the digit-length group that contains the nth digit
        while (n > digitLen * count) {
            n -= digitLen * count;      // remove whole group
            digitLen++;                 // move to next group
            count *= 10;                // 9, 90, 900, …
            start *= 10;                // 1, 10, 100, …
        }

        // 2. Find the exact number containing the nth digit
        long number = start + (n - 1) / digitLen;

        // 3. Pick the correct digit from that number
        int digitIndex = (int) ((n - 1) % digitLen);
        String s = Long.toString(number);
        return s.charAt(digitIndex) - '0';
    }
}
```

**Why this is good:**

* Uses `long` to avoid overflow when `n` is close to `2^31-1`.
* No string concatenation loop – only one string conversion per call.
* Clear variable names (`digitLen`, `count`, `start`) make the math easy to verify.

---

### 4.2 Python

```python
class Solution:
    def findNthDigit(self, n: int) -> int:
        """
        LeetCode 400 – Nth Digit
        Time: O(1)  (max 10 iterations)
        Space: O(1)
        """
        digit_len = 1       # 1,2,3,...
        count = 9           # 9,90,900,...
        start = 1           # 1,10,100,...

        # Find the digit-length block
        while n > digit_len * count:
            n -= digit_len * count
            digit_len += 1
            count *= 10
            start *= 10

        # Locate the target number
        number = start + (n - 1) // digit_len

        # Extract the digit
        digit_index = (n - 1) % digit_len
        return int(str(number)[digit_index])
```

**Highlights**

* Uses Python’s arbitrary precision integers – no overflow worries.
* One string conversion (`str(number)`) is negligible.
* The logic is identical to the Java version for consistency.

---

### 4.3 C++

```cpp
/**
 * LeetCode 400 – Nth Digit
 * Time Complexity: O(1) – at most 10 iterations
 * Space Complexity: O(1)
 */
class Solution {
public:
    int findNthDigit(int n) {
        long long digitLen = 1;   // length of current numbers
        long long count = 9;      // how many numbers of that length
        long long start = 1;      // first number of that length

        // Find the block that contains the nth digit
        while (n > digitLen * count) {
            n -= digitLen * count;
            digitLen++;
            count *= 10;
            start *= 10;
        }

        // The exact number containing the nth digit
        long long number = start + (n - 1) / digitLen;

        // Extract the digit
        int digitIndex = (n - 1) % digitLen;
        string s = to_string(number);
        return s[digitIndex] - '0';
    }
};
```

**Why it’s great**

* `long long` handles the maximum `n` safely.
* The code mirrors the Java/Python logic, making it easier for interviewers to spot your reasoning.

---

## 5. The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Math Intuition** | Clean use of arithmetic progressions. | Over‑thinking can lead to a simulation approach that TLEs. | Forgetting to convert `n` from 1‑based to 0‑based when indexing. |
| **Algorithmic Complexity** | O(1) time, O(1) space. | O(n) if you build the string iteratively. | Using recursion that grows stack depth. |
| **Edge Cases** | Handles `n` up to `2^31‑1` without overflow. | Mishandling `n` exactly at the boundary of a digit group. | Off‑by‑one errors in `digitLen * count`. |
| **Language‑agnostic** | Same core logic works everywhere. | Forgetting that `int` in Java/C++ may overflow; need `long`. | Using string concatenation loops which are slow. |

---

## 6. SEO‑Optimized Takeaway

*If you’re preparing for a **software engineering interview**, mastering LeetCode 400 (Nth Digit) is a must.  
- The problem tests **mathematical insight**, **constant‑time complexity**, and **edge‑case handling**.  
- A clean O(1) solution shows you can think mathematically, code efficiently, and stay within language limits.*

Add the code snippets above to your portfolio or GitHub repo – they’re ready to paste into any interview environment.  
Pro tip: **Explain the math** to your interviewer; they’ll appreciate the depth of understanding.

---

## 7. Quick Reference Cheat‑Sheet

| Step | What to do | Code fragment |
|------|------------|---------------|
| 1 | Determine digit length group | `while (n > digitLen * count) …` |
| 2 | Find target number | `number = start + (n - 1) / digitLen;` |
| 3 | Pick the digit | `digitIndex = (n - 1) % digitLen;` |
| 4 | Convert to string / arithmetic to get digit | `return s[digitIndex] - '0';` |

---

### Final Thought

The **Nth Digit** problem is deceptively simple but packed with interview‑testing sugar.  
Write the O(1) solution, comment clearly, and be ready to walk through the math.  
Good luck landing that job—your interviewers will love your clean, efficient, and well‑understood solution!