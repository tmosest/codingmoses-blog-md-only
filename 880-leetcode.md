---
title: LeetCode 880. Decoded String at Index - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 880 – *Decoded String at Index* – 3‑Language Solution + SEO‑Optimized Blog

> **Keywords**: LeetCode 880, Decoded String at Index, Java solution, Python solution, C++ solution, interview algorithm, job interview, coding challenge, algorithm interview question

---

### 1. Problem Summary  

> **Given** an encoded string `s` (letters + digits `2–9`) and an index `k` (1‑based).  
> **Goal**: Return the `k`‑th character in the fully decoded string.

> **Examples**  
> `s = "leet2code3", k = 10  →  "o"`  
> `s = "ha22", k = 5          →  "h"`  
> `s = "a2345678999999999999999", k = 1 → "a"`

> **Constraints**  
> * `2 ≤ s.length ≤ 100`  
> * `1 ≤ k ≤ 10⁹`  
> * The decoded string length < 2³²

---

### 2. Why This Problem Rocks for Interviews

* **String manipulation + arithmetic** – tests your ability to reason about dynamic lengths without actually constructing huge strings.  
* **Reverse engineering / “look‑back” logic** – you must figure out the character that produced the given index after multiple expansions.  
* **Edge‑case awareness** – huge repetition counts, large `k`, off‑by‑one pitfalls.

Interviewers love this because it forces candidates to think in *logarithmic* time instead of brute‑force.

---

### 3. The Efficient Approach (O(n) time, O(1) space)

1. **Compute the decoded length (`size`)** while scanning the string left‑to‑right.  
   * For a letter → `size++`.  
   * For a digit `d` → `size *= d`.  
   * Use a 64‑bit integer (`long` in Java / `long long` in C++ / `int` in Python) to avoid overflow.

2. **Traverse the string in reverse** to find the character that maps to index `k`.  
   * While moving backwards:
     * If the current char is a digit `d`:  
       `k %= size / d` (the length *before* this expansion).  
       `size /= d`.
     * If the current char is a letter:  
       If `k == 0` (or `k == size`) → this letter is the answer.  
       Otherwise → `size--`.

3. Return the found letter.

The intuition: After we know the final length, we “fold back” the expansion steps. Each digit tells us that the current section is a repeated block. We can shrink `k` modulo the block size to figure out which position inside the original block we really need. When we hit a letter, if our `k` points to it, we’re done.

---

### 4. Code Implementations

> **All three solutions use the same algorithmic skeleton.**  
> They are ready to paste into LeetCode or your local IDE.

---

#### 4.1 Java

```java
// LeetCode 880 – Decoded String at Index
public class Solution {
    public char decodeAtIndex(String s, int k) {
        long size = 0; // use long to avoid overflow

        // First pass: compute final decoded length
        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
                size *= c - '0';
            } else {
                size += 1;
            }
        }

        // Second pass: reverse traversal
        for (int i = s.length() - 1; i >= 0; i--) {
            char c = s.charAt(i);
            if (Character.isDigit(c)) {
                int d = c - '0';
                size /= d;
                k %= size;
            } else {
                if (k == 0 || k == size) return c;
                size -= 1;
            }
        }
        return '?'; // should never reach here
    }
}
```

---

#### 4.2 Python

```python
# LeetCode 880 – Decoded String at Index
class Solution:
    def decodeAtIndex(self, s: str, k: int) -> str:
        size = 0

        # Build final length
        for ch in s:
            if ch.isdigit():
                size *= int(ch)
            else:
                size += 1

        # Reverse traverse
        for ch in reversed(s):
            if ch.isdigit():
                size //= int(ch)
                k %= size
            else:
                if k == 0 or k == size:
                    return ch
                size -= 1
```

---

#### 4.3 C++

```cpp
// LeetCode 880 – Decoded String at Index
class Solution {
public:
    char decodeAtIndex(string s, int k) {
        long long size = 0;

        // Compute final length
        for (char c : s) {
            if (isdigit(c)) size *= (c - '0');
            else ++size;
        }

        // Reverse traverse
        for (int i = (int)s.size() - 1; i >= 0; --i) {
            char c = s[i];
            if (isdigit(c)) {
                int d = c - '0';
                size /= d;
                k %= size;
            } else {
                if (k == 0 || k == size) return c;
                --size;
            }
        }
        return '?'; // unreachable
    }
};
```

---

### 5. Blog Article – “The Good, The Bad, and The Ugly of LeetCode 880”

> **Target audience**: Front‑end developers, backend engineers, data‑science coders prepping for interviews, recruiters looking for interview questions.

---

#### 5.1 Title & Meta

```
Title: Decoded String at Index (LeetCode 880) – The Good, The Bad & Ugly | Java, Python, C++ Solutions
Meta Description: Master LeetCode 880 with step‑by‑step solutions in Java, Python, and C++. Learn the optimal reverse traversal trick, common pitfalls, and interview tips.
```

---

#### 5.2 Introduction (≈200 words)

> “During a recent interview, my candidate faced *LeetCode 880 – Decoded String at Index*. The problem looked deceptively simple: just expand a string and pick a character. But with a 10⁹ index and a string that can encode billions of characters, naive decoding crashes fast. In this article we dissect the problem, reveal the efficient reverse‑traversal trick, present clean code in three popular languages, and explore the common pitfalls. Whether you’re a fresher polishing your interview skills or a senior engineer refreshing your algorithm toolbox, you’ll find the ‘good’, ‘bad’, and ‘ugly’ aspects of this classic string challenge.”

---

#### 5.3 Problem Recap (1‑2 paragraphs)

> *Define the input, output, and constraints.*  
> *Show the three examples.*  
> *Mention why the encoded string is guaranteed to start with a letter – this simplifies the logic.*

---

#### 5.4 The “Good” – What Works

1. **Linear Time, Constant Extra Space**  
   * Only a single pass to compute the final length, another reverse pass to backtrack.  
   * No need to build huge strings or recursion stack.

2. **Intuitive Reverse Logic**  
   * Think of the encoded string as layers of repetition.  
   * By moving backwards we peel layers off, reducing `k` at each digit.

3. **Robust with Large Numbers**  
   * Using `long` (or Python's arbitrary‑precision `int`) protects against overflow.  
   * The algorithm never stores more than a few integers.

---

#### 5.5 The “Bad” – Where Naive Implementations Fail

| Naive Idea | Why It Fails |
|------------|--------------|
| **Fully decode the string** | Expands to billions of characters → MLE/TLE |
| **Iterate left‑to‑right, keep a stack of partial strings** | Stack grows with decoded length, same memory issue |
| **Recursive expansion** | Stack overflow for deep recursions, plus same memory blowup |
| **Use `pow(10, k)` to guess length** | Floating point inaccuracies, not needed |

> *Lesson*: Always think *in terms of lengths*, not *in terms of content*.

---

#### 5.6 The “Ugly” – Common Gotchas

1. **Off‑by‑One Errors**  
   * Remember that `k` is 1‑based. When you see `k % size == 0`, the answer is the last character of the current block.

2. **Digit vs. Letter Parsing**  
   * In Java `Character.isDigit()` vs `Character.isLetter()`.  
   * In C++ `isdigit()` needs `<cctype>` and uses ASCII.  
   * In Python `ch.isdigit()` is straightforward.

3. **Overflow in 32‑bit Int**  
   * Even though the final length < 2³², intermediate `size` may exceed 32‑bit. Use 64‑bit (`long` / `long long`).

4. **Large `k` Modulo**  
   * `k %= size` must happen *after* updating `size` for a digit.  
   * Don’t forget to update `size` first: `size /= d; k %= size;`

---

#### 5.7 Step‑by‑Step Code Walkthrough (Java Example)

```java
for (char c : s.toCharArray()) {
    if (Character.isDigit(c)) size *= c - '0';
    else size++;
}
```

*Explain: We accumulate the final length; a digit multiplies the current length.*

```java
for (int i = s.length() - 1; i >= 0; i--) {
    char c = s.charAt(i);
    if (Character.isDigit(c)) {
        int d = c - '0';
        size /= d;      // shrink to pre‑expansion length
        k %= size;      // map k into that block
    } else {
        if (k == 0 || k == size) return c;
        size -= 1;
    }
}
```

*Explain each branch.*

---

#### 5.8 Time & Space Complexity

| Implementation | Time | Space |
|----------------|------|-------|
| Efficient Reverse Approach | **O(n)** (n ≤ 100) | **O(1)** |
| Naive Full Decoding | O(length of decoded string) | O(length of decoded string) |

---

#### 5.9 Interview Tips

* **Clarify constraints**: Ask if `k` is 1‑based, confirm digits only `2–9`.  
* **Explain your approach verbally**: Outline length computation, reverse traversal, and why you use `long`.  
* **Ask about edge cases**: What if `k` equals final length? What about single letter string?  
* **Mention optimization**: “We avoid building the string; we just track lengths.”  

---

#### 5.10 Takeaway

> *Decoding string problems are a classic interview gold‑mine.*  
> *With the reverse‑traversal trick you can turn a seemingly monstrous problem into a 2‑pass linear solution.*  
> *Master this pattern, and you’ll be ready for any “string expansion” question that surfaces in your next coding interview.*

---

### 6. Conclusion & Next Steps

* Copy the code snippets into LeetCode, run the provided test cases, and add your own edge cases.  
* Practice variations: *Decode String at Index* with 0‑based `k`, or with `s` containing multiple digits.  
* Share your solution on GitHub with a short README; recruiters love clean, well‑commented code.

Happy coding—and good luck on your next interview! 🎯