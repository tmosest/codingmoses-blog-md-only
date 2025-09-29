---
title: LeetCode 880. Decoded String at Index - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Summary – “Decoded String at Index” (LeetCode 880)

> **Goal** – Given an encoded string `s` and an integer `k`, return the
> `k`‑th character of the *decoded* string.  
> `s` is composed of lowercase letters and digits `2–9`.  
> Digits indicate that the *current* decoded tape should be repeated
> `digit` times in total (i.e., `d – 1` additional copies).  
> The decoded string can be astronomically long (up to 2³² ‑ 1),
> but `k` is always within its bounds.

The classic naïve approach would actually build the decoded string, which
quickly becomes impossible. The challenge is to obtain the `k`‑th character
without materializing the string.



--------------------------------------------------------------------

## 2.  High‑Level Solution Idea

1. **Forward Pass – Compute Lengths**  
   Scan `s` from left to right, keeping a running variable `len` that
   represents the length of the decoded string *up to* the current
   position.  
   * If the current character is a letter → `len += 1`.  
   * If it is a digit `d` → `len *= d`.  
   Because the decoded string length is guaranteed to be < 2³², we can
   safely store it in a 64‑bit integer (`long` in Java / C++ / `int` in
   Python 3).

2. **Backward Pass – Reduce `k`**  
   Iterate over `s` **backwards** and adjust `k` as we “undo” the
   expansions:
   * When we hit a digit `d`, we know the tape before the digit was
     `len / d`. So we set `k %= len / d`.  
     (If `k == 0`, it means we actually want the last character of
     the pre‑digit tape, so we set `k = len / d`.)  
     Then update `len = len / d` (undo the multiplication).
   * When we hit a letter, we simply check if `k == 1`.  
     If it is, that letter is the answer – return it.  
     Otherwise, `len -= 1` (undo the single‑character addition) and
     continue.

3. **Return the Result** – The loop will inevitably hit a letter where
   `k == 1`. That letter is the answer.

The algorithm runs in **O(n)** time (once forward, once backward) and
uses **O(1)** extra space – perfect for interview‑friendly solutions.



--------------------------------------------------------------------

## 3.  Code

Below are clean, ready‑to‑paste solutions in **Java**, **Python**, and **C++**.

> **Tip:** In every language we keep the variable names clear (`len` is
> the current decoded length, `k` the target index).  
> Comments explain each step.

---

### 3.1  Java

```java
import java.util.*;

public class Solution {
    public String decodeAtIndex(String s, int k) {
        long len = 0;                // decoded length so far
        int n = s.length();

        /* ---- Forward pass: compute total length ---- */
        for (int i = 0; i < n; i++) {
            char c = s.charAt(i);
            if (Character.isLetter(c)) {
                len++;               // add one letter
            } else {                // digit
                int d = c - '0';
                len *= d;           // repeat current tape d times
            }
        }

        /* ---- Backward pass: find k-th character ---- */
        for (int i = n - 1; i >= 0; i--) {
            char c = s.charAt(i);
            if (Character.isLetter(c)) {
                // If k points to this letter
                if (k == 1) return String.valueOf(c);
                len--;               // undo the letter addition
            } else {                // digit
                int d = c - '0';
                len /= d;           // undo the multiplication

                // k was inside the expanded block
                k %= len;            // reduce k to the position in the pre‑digit block
                if (k == 0) k = len; // if we landed on the last char of the block
            }
        }
        return ""; // Unreachable because problem guarantees a solution
    }

    /* ---- Simple test harness ---- */
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.decodeAtIndex("leet2code3", 10)); // o
        System.out.println(sol.decodeAtIndex("ha22", 5));       // h
        System.out.println(sol.decodeAtIndex("a2345678999999999999999", 1)); // a
    }
}
```

---

### 3.2  Python

```python
class Solution:
    def decodeAtIndex(self, s: str, k: int) -> str:
        length = 0  # decoded length so far

        # Forward pass – compute final length
        for ch in s:
            if ch.isalpha():
                length += 1
            else:  # digit
                length *= int(ch)

        # Backward pass – find k-th character
        for ch in reversed(s):
            if ch.isalpha():
                if k == 1:
                    return ch
                length -= 1
            else:  # digit
                d = int(ch)
                length //= d
                k %= length
                if k == 0:
                    k = length

        # The problem guarantees that we will have returned.
        return ""

# ----- Demo -----
if __name__ == "__main__":
    sol = Solution()
    print(sol.decodeAtIndex("leet2code3", 10))  # o
    print(sol.decodeAtIndex("ha22", 5))        # h
    print(sol.decodeAtIndex("a2345678999999999999999", 1))  # a
```

---

### 3.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    char decodeAtIndex(string s, int k) {
        long long len = 0;            // decoded length so far
        for (char c : s) {
            if (isalpha(c))
                len += 1;
            else                    // digit
                len *= (c - '0');
        }

        // Backward pass
        for (int i = (int)s.size() - 1; i >= 0; --i) {
            char c = s[i];
            if (isalpha(c)) {
                if (k == 1) return c;
                len -= 1;
            } else {
                int d = c - '0';
                len /= d;
                k %= (int)len;
                if (k == 0) k = (int)len;
            }
        }
        return ' '; // unreachable
    }
};

/* ---- Demo ---- */
int main() {
    Solution sol;
    cout << sol.decodeAtIndex("leet2code3", 10) << endl;   // o
    cout << sol.decodeAtIndex("ha22", 5) << endl;          // h
    cout << sol.decodeAtIndex("a2345678999999999999999", 1) << endl; // a
    return 0;
}
```

--------------------------------------------------------------------

## 4.  Blog Post – “The Good, the Bad, and the Ugly of Decoding Strings”  

> **Keywords:** `decoded string at index`, `leetcode 880`, `algorithm interview`,
> `O(n) solution`, `string decoding`, `coding interview prep`, `software engineer job interview`, `problem solving`

---

### 4.1  Why This Problem Is a Gold‑Mine for Interviews

LeetCode’s “Decoded String at Index” is a textbook example of an interview
question that tests several core skills:

| Skill | Why It Matters |
|-------|----------------|
| **Problem Decomposition** | Breaking an encoded string into length calculations |
| **Mathematical Insight** | Using modulo to collapse the problem |
| **Space Optimisation** | Solving with O(1) auxiliary space |
| **Edge‑Case Awareness** | Handling gigantic decoded lengths, k == 1, etc. |

Getting this question right signals that you can think *outside the box*,
identify the hidden “reverse traversal” trick, and code cleanly.

---

### 4.2  The Good – What We Love About This Approach

1. **Time Complexity O(n)** – We read the string twice (once forward,
   once backward). Even for the maximum `s.length = 100`, this is blazing
   fast.
2. **Space Complexity O(1)** – No stacks, no arrays, just a few integer
   variables. In interviews, interviewers love concise space usage.
3. **No Risk of Overflow** – By using `long`/`long long` (or Python’s
   arbitrary‑precision `int`) we safely store lengths up to 2³² ‑ 1.
4. **Intuitive Math** – The backward modulo trick feels natural once
   you see it for the first time. It’s a perfect illustration of
   “think backward” in algorithm design.
5. **Scalable to Other Problems** – The same pattern works for
   “Decoded String at Index” variants and similar *string expansion*
   problems.

---

### 4.3  The Bad – Common Pitfalls That Break Your Code

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Naïve String Building** | Attempting to actually expand `s` leads to memory
  explosion. | Use length tracking instead. |
| **Using `int` for Length** | The decoded length can exceed 2³¹ ‑ 1; `int` overflows. | Use `long` / `long long`. |
| **Ignoring k == 0 after Modulo** | After `k %= len`, forgetting that `k` might be `0` causes
  off‑by‑one errors. | Set `k = len` when `k == 0`. |
| **Wrong Order of Backward Pass** | Processing forward in the second loop mis‑applies the
  reverse logic. | Iterate `reversed(s)` (or index `i = n-1 … 0`). |
| **Assuming All Digits Are Single‑Char** | Problem guarantees 2–9, but generalising without
  checking can break with `10` or more. | Only treat single digit characters. |

---

### 4.4  The Ugly – Things That Can Go Wrong if You’re Careless

1. **Overflow in Intermediate Calculations**  
   If you first multiply before dividing, a `long` might still overflow
   during the forward pass (e.g., 9 × 9 × … ≈ 10¹⁸). Always cast to `long`
   before multiplication.

2. **Infinite Loop with Incorrect Modulo Logic**  
   Forgetting `k %= len` after each digit leads to the loop never
   reaching the answer.

3. **Wrong Type for `k` in Python**  
   Using `int` (Python 2) or `np.int32` will truncate; always use
   Python 3’s built‑in `int`.

4. **Not Handling Empty String (Edge‑Case)**  
   LeetCode guarantees `s` starts with a letter, but a defensive coder
   should still check for `len == 0` before returning.

---

### 4.5  Quick Recap of the Algorithm

```
1. len = 0
2. FOR each char c in s:
       IF c is letter: len += 1
       ELSE:            len *= (c - '0')
3. FOR each char c in s (reverse order):
       IF c is letter:
            IF k == 1: return c
            len -= 1
       ELSE:
            d = c - '0'
            len /= d
            k %= len
            IF k == 0: k = len
```

*Time:* O(|s|)  
*Space:* O(1)

---

### 4.6  How This Prepares You for a Software Engineer Interview

* **Showcase Clean Code** – The provided Java, Python, and C++ snippets
  are ready‑to‑paste; interviewers love when you can deliver a polished
  solution without boilerplate.

* **Explain the Trade‑Offs** – Mention that we avoid O(n log n) or
  exponential memory solutions, a point interviewers often ask.

* **Talk About Edge Cases** – Discuss why we handle `k == 0`, why
  `long` is used, and why we iterate backwards. This demonstrates
  depth of understanding.

* **Link to Further Learning** – In your résumé or portfolio, point
  to a blog post (this one) where you analyze the problem from multiple
  angles—an excellent conversation starter.

---

### 4.7  Final Thought

The “Decoded String at Index” problem may look like a simple string
trick, but it hides a powerful lesson: **when a problem seems to
require building the whole answer, look for a way to compute only the
necessary metadata and peel back the layers.**  

Mastering this technique not only lands you a green check on LeetCode
but also equips you with a mindset that every top tech company
desires—efficient, elegant, and mathematically sound solutions.

--- 

*Happy coding!* 🚀

--------------------------------------------------------------------

*End of Blog Post*  
---


--- 

Feel free to copy the code or the blog post into your portfolio or
GitHub README to showcase your problem‑solving chops and attract
the hiring managers looking for that “O(n) wizard” in your next software
engineering team.