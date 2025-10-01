---
title: LeetCode 400. Nth Digit - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 400. Nth Digit – A Complete, SEO‑Optimized Solution Guide

> **TL;DR** –  
> •  O(log n) time, O(1) space  
> •  Find the digit length group, locate the exact number, then pick the required digit  
> •  Java, Python, C++ implementations below

---

### Table of Contents  

| Section | What you’ll learn |
|---------|-------------------|
| ✅ Problem Overview | What the challenge asks for |
| 📌 Good Approach | Efficient, clean algorithm |
| 🔍 Complexity Analysis | Why it’s fast |
| ⚠️ Edge‑Case Handling | Safe for 32‑bit limits |
| 🧩 Code in Java | Full, tested code |
| 🧩 Code in Python | Full, tested code |
| 🧩 Code in C++ | Full, tested code |
| 🚨 Bad Practices | What not to do |
| 💔 Ugly Solutions | Why they fail |
| 🎯 SEO & Career Tips | How to write this article so recruiters love it |
| 📚 Final Thoughts | Recap & next steps |

---

## 1. Problem Overview

**LeetCode #400 – Nth Digit**

> **Input:** an integer `n` (1 ≤ n ≤ 2³¹ − 1)  
> **Output:** the nth digit of the infinite sequence  
> `1, 2, 3, …, 9, 10, 11, …`

For example:

| n | Result | Explanation |
|---|--------|-------------|
| 3 | 3 | Sequence: 1,2,**3**… |
| 11 | 0 | Sequence: …9, **10**, 1… The 11th digit is the second digit of 10 |

The goal is to find the digit **without** building the entire sequence – that would be impossible for large `n`.

---

## 2. Good Approach – The Math‑Driven O(log n) Solution

1. **Group numbers by digit length.**  
   - 1‑digit numbers: 1‑9 → 9 × 1 = 9 digits  
   - 2‑digit numbers: 10‑99 → 90 × 2 = 180 digits  
   - 3‑digit numbers: 100‑999 → 900 × 3 = 2700 digits  
   - … and so on.

2. **Find the group that contains the nth digit.**  
   Keep subtracting the count of digits in each group from `n` until the remaining `n` is ≤ the size of the current group.

3. **Locate the exact number.**  
   - `first = 10^(len-1)` (first number with `len` digits).  
   - `index = (n-1) / len` → zero‑based index inside the group.  
   - `number = first + index`.

4. **Pick the digit inside that number.**  
   - `digitIndex = (n-1) % len` (0‑based).  
   - Convert `number` to a string (or use arithmetic) and extract the digit at `digitIndex`.

5. **Return the digit as an integer.**

The algorithm runs in O(log n) time (at most 10 iterations for 32‑bit `n`) and O(1) extra space.

---

## 3. Complexity Analysis

| Metric | Value |
|--------|-------|
| Time   | **O(log n)** (≈ 10 loops max) |
| Space  | **O(1)** (constant) |

The method never touches the full sequence; it only uses a few arithmetic operations.

---

## 4. Edge‑Case Handling

| Edge | Why it matters | Fix |
|------|----------------|-----|
| `n` is very large (≈ 2³¹ − 1) | Integer overflow when multiplying `count * len` | Use `long` (`int64`) for all intermediate counters |
| `n` lands exactly on the boundary of a group (e.g., 9, 189) | Off‑by‑one mistakes in the loop | Subtract **`count * len`** *before* checking `n > groupSize` |
| `n` = 1 | First digit | The loop will break immediately; `len=1`, `first=1` |
| `n` = 10 | First digit of 10 | Works because the group for 2‑digit numbers starts after 9 |

---

## 5. Code – Java

```java
/**
 * 400. Nth Digit
 *
 * Time: O(log n)  – at most 10 iterations for 32‑bit n
 * Space: O(1)
 */
class Solution {
    public int findNthDigit(int n) {
        // Use long to avoid overflow
        long remaining = n;
        long digitLen = 1;          // current number of digits
        long count = 9;             // numbers in current group
        long first = 1;             // first number of current group

        // Step 1: find the group containing the nth digit
        while (remaining > digitLen * count) {
            remaining -= digitLen * count;
            digitLen++;                // move to next length
            count *= 10;               // 9, 90, 900, ...
            first *= 10;               // 1, 10, 100, ...
        }

        // Step 2: locate the exact number
        long index = (remaining - 1) / digitLen;      // zero‑based
        long number = first + index;                  // the target number
        int digitIndex = (int)((remaining - 1) % digitLen); // position inside number

        // Step 3: extract the digit
        String s = Long.toString(number);
        return s.charAt(digitIndex) - '0';
    }
}
```

---

## 6. Code – Python

```python
class Solution:
    def findNthDigit(self, n: int) -> int:
        """
        Time: O(log n)
        Space: O(1)
        """
        remaining = n
        digit_len = 1
        count = 9
        first = 1

        # Find the digit-length group
        while remaining > digit_len * count:
            remaining -= digit_len * count
            digit_len += 1
            count *= 10
            first *= 10

        # Locate the exact number
        index = (remaining - 1) // digit_len
        number = first + index
        digit_index = (remaining - 1) % digit_len

        # Extract the digit
        return int(str(number)[digit_index])
```

---

## 7. Code – C++

```cpp
/**
 * 400. Nth Digit
 *
 * Time: O(log n)
 * Space: O(1)
 */
class Solution {
public:
    int findNthDigit(int n) {
        long long remaining = n;
        long long digitLen = 1;   // current digit length
        long long count = 9;      // how many numbers in this length
        long long first = 1;      // first number of this length

        // Find the group
        while (remaining > digitLen * count) {
            remaining -= digitLen * count;
            digitLen++;
            count *= 10;
            first *= 10;
        }

        // Locate the exact number
        long long index = (remaining - 1) / digitLen;
        long long number = first + index;
        int digitIndex = (int)((remaining - 1) % digitLen);

        // Extract digit
        string s = to_string(number);
        return s[digitIndex] - '0';
    }
};
```

---

## 8. Bad Practices – What NOT to Do

| Bad Practice | Why it’s bad |
|--------------|--------------|
| **String concatenation of entire sequence** (`StringBuilder` building “12345678910…”) | O(n) time and memory, impossible for large `n`. |
| **Using recursion to iterate through numbers** | Stack overflow risk; unnecessary complexity. |
| **Brute‑force loops up to `n`** | Linear time, TLE on LeetCode (2^31 − 1). |
| **Ignoring 64‑bit overflow** | `int` multiplication overflows on `9 * 10^9` etc. |
| **Assuming `n` fits in `int` throughout** | Subtractions can produce negative intermediate values. |

---

## 9. Ugly Solutions – Common Pitfalls

1. **Off‑by‑One Bugs** – forgetting that `remaining` is 1‑based while indices are 0‑based.
2. **Incorrect Group Boundaries** – using `>=` instead of `>` in the loop condition can skip a whole group.
3. **Over‑Optimizing Early** – micro‑optimizing string operations or math when the algorithm is already O(log n).
4. **Hard‑coded Limits** – assuming maximum digits is 9; this fails for 32‑bit `n` (needs 10‑digit group).

---

## 10. SEO & Career Tips

| SEO Tip | How it Helps |
|---------|--------------|
| **Use long‑tail keywords** – “nth digit leetcode solution”, “find nth digit algorithm”, “java python c++ nth digit” | Increases visibility to recruiters searching for problem‑solving posts. |
| **Add a “Read Time”** – e.g., “Read Time: 4 min” | Keeps readers engaged; Google favors concise, digestible content. |
| **Insert code‑blocks with syntax highlighting** – many recruiters copy code directly. | Shows clean implementation and saves time. |
| **Link to official LeetCode page** – `<a href="https://leetcode.com/problems/nth-digit/">LeetCode 400</a>` | Builds trust and gives credibility. |
| **Embed a short demo video or GIF** – shows code running | Improves dwell time and showcases practical skill. |
| **Add a “Takeaway” section** – key concepts in bullet points | Helps hiring managers quickly gauge your understanding. |

**Why this matters for your job hunt**

- Recruiters skim blog posts to assess *technical depth* and *communication skills*.  
- A well‑structured article demonstrates *algorithmic thinking* + *clean coding* + *attention to detail*.  
- SEO tricks make your post appear in search results when recruiters are looking for interview prep or LeetCode walkthroughs.

---

## 11. Final Thoughts

- The Nth Digit problem is a classic example of **digit‑DP / group‑counting**.  
- The O(log n) solution is the only practical approach for large `n`.  
- Implementing it in Java, Python, and C++ showcases your cross‑platform proficiency.  
- Writing a clear, SEO‑optimized blog post turns a simple coding problem into a powerful portfolio piece.

Happy coding—and may the digits be ever in your favor! 🚀