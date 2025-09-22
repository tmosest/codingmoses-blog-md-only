---
title: LeetCode 3307. Find the K-th Character in String Game II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3307 – Find the K‑th Character in String Game II  
*A complete, interview‑ready solution in Java, Python & C++ + a developer‑friendly blog post*

---

## Table of Contents  
1. [Problem Recap](#problem-recap)  
2. [Intuition & Key Insight](#intuition)  
3. [Algorithm Walk‑through](#algorithm)  
4. [Time & Space Complexity](#complexity)  
5. [Code Snippets](#codes)  
   * Java  
   * Python  
   * C++  
6. [Good, Bad & Ugly](#goodbadugly)  
7. [SEO‑Optimised Blog Post](#blog)  
8. [Takeaway & How to Use It in an Interview](#takeaway)

---

## 1. Problem Recap <a name="problem-recap"></a>

Alice starts with the string **“a”**.  
Bob gives a list `operations` of length `n` (1 ≤ n ≤ 100) where

| operation | effect |
|-----------|--------|
| **0** | `word = word + word` (append a copy of itself) |
| **1** | `word = word + shift(word)` where `shift` turns every letter to its next letter in the alphabet (z → a) |

Given a 1‑based index `k` (1 ≤ k ≤ 10¹⁴) that is guaranteed to be within the final string, return the character at position `k`.

**Example**

```text
k = 10, operations = [0,1,0,1]
final word: aabbaabbbbccbbcc
k‑th character (10th) → 'b'
```

---

## 2. Intuition & Key Insight <a name="intuition"></a>

* **Both operations double the length.**  
  Whether we copy the string or copy a shifted copy, the length is always `2 × previous length`.

* **We never need to build the string.**  
  The string can be astronomically large (2¹⁰⁰ > 10³⁰).  
  We only need to locate the *k‑th* character, so we can **walk backwards** through the operations, shrinking `k` and accumulating how many times the characters have been shifted.

* **Reverse simulation**  
  Start from the end of the operation list.  
  For each operation:

  * If `k` is in the *second* half of the current word, we are in the part that was appended last.  
    * For op 0 → no shift, just subtract the length of the first half.  
    * For op 1 → subtract the first half and **add one to the shift counter** (because that half is the *shifted* copy).

  * If `k` is in the first half → nothing changes; keep going.

  After processing all operations, `k` will point to the first character of the original “a”.  
  The final character is simply `'a'` shifted by the accumulated counter (mod 26).

* **Handling huge lengths**  
  We only need to know if a length exceeds `k`.  
  While building the length array, clamp each value to `k + 1` (or `Long.MAX_VALUE`) to avoid overflow and keep comparisons fast.

---

## 3. Algorithm Walk‑through <a name="algorithm"></a>

```
1. Let n = operations.length
2. len[0] = 1                           // length after 0 operations
   For i = 0 … n-1:
        len[i+1] = min(len[i] * 2, k + 1)
3. shift = 0
4. For i = n-1 downto 0:
        half = len[i]                   // length before this operation
        if operations[i] == 0:
             if k > half: k -= half
        else (operations[i] == 1):
             if k > half:
                  k -= half
                  shift = (shift + 1) % 26
5. answer = char('a' + shift)
```

**Why it works**

* When reversing an op 0, the word is `firstHalf + firstHalf`.  
  The *second* half is an identical copy, so the character at `k` (if in the second half) is the same as the character at `k-half` in the first half.

* When reversing an op 1, the word is `firstHalf + shift(firstHalf)`.  
  The second half is the *shifted* copy, so the character at `k` (if in the second half) is the same as the character at `k-half` in the first half, **but shifted by one**.  
  We capture that by increasing `shift`.

* Repeating until we reach the original “a” guarantees we are pointing to the correct character.

---

## 4. Time & Space Complexity <a name="complexity"></a>

| Metric | Complexity |
|--------|------------|
| Time   | **O(n)** (n ≤ 100) |
| Space  | **O(n)** for the length array (≈ 100 long values) |

All operations are constant‑time; the algorithm never touches the actual string.

---

## 5. Code Snippets <a name="codes"></a>

### Java

```java
import java.util.*;

class Solution {
    public char kthCharacter(long k, int[] operations) {
        int n = operations.length;
        long[] len = new long[n + 1];
        len[0] = 1;
        for (int i = 0; i < n; i++) {
            long next = len[i] << 1;                // len[i] * 2
            if (next > k) next = k + 1;              // clamp to avoid overflow
            len[i + 1] = next;
        }

        int shift = 0;  // number of shifts applied
        for (int i = n - 1; i >= 0; i--) {
            long half = len[i];
            if (operations[i] == 0) {               // duplicate
                if (k > half) k -= half;
            } else {                                // shift copy
                if (k > half) {
                    k -= half;
                    shift = (shift + 1) % 26;
                }
            }
        }
        return (char) ('a' + shift);
    }
}
```

### Python

```python
class Solution:
    def kthCharacter(self, k: int, operations: List[int]) -> str:
        n = len(operations)
        lengths = [1] * (n + 1)
        for i in range(n):
            nxt = lengths[i] * 2
            if nxt > k:
                nxt = k + 1          # clamp to avoid overflow
            lengths[i + 1] = nxt

        shift = 0
        for i in range(n - 1, -1, -1):
            half = lengths[i]
            if operations[i] == 0:
                if k > half:
                    k -= half
            else:  # shift operation
                if k > half:
                    k -= half
                    shift = (shift + 1) % 26

        return chr(ord('a') + shift)
```

### C++

```cpp
class Solution {
public:
    char kthCharacter(long long k, vector<int>& operations) {
        int n = operations.size();
        vector<long long> len(n + 1);
        len[0] = 1;
        for (int i = 0; i < n; ++i) {
            long long nxt = len[i] << 1;   // multiply by 2
            if (nxt > k) nxt = k + 1;     // clamp
            len[i + 1] = nxt;
        }

        int shift = 0;
        for (int i = n - 1; i >= 0; --i) {
            long long half = len[i];
            if (operations[i] == 0) {
                if (k > half) k -= half;
            } else {
                if (k > half) {
                    k -= half;
                    shift = (shift + 1) % 26;
                }
            }
        }
        return char('a' + shift);
    }
};
```

> **Tip**: The key is to store *half lengths* and **reverse‑simulate** the operations. No string building ever happens.

---

## 6. Good, Bad & Ugly <a name="goodbadugly"></a>

| Category | What Works | What to Watch For | How to Fix |
|----------|------------|-------------------|------------|
| **Good** | • Time‑linear algorithm<br>• O(1) memory per operation<br>• Handles `k` up to 10¹⁴ without overflow<br>• Works for all edge cases (empty ops, all 0s, all 1s) | | |
| **Bad** | • A naïve implementation that builds the string will TLE/MLE.<br>• Forgetting to clamp length values leads to overflow (2¹⁰⁰ > long max). | • Not modding the shift counter (will exceed 26).<br>• Using 1‑based/0‑based confusion in `k`. | • Use `min(length, k+1)` to clamp.<br>• Always keep `shift % 26`.<br>• Treat `k` as 1‑based consistently. |
| **Ugly** | • Recursive solutions that recompute lengths each call can hit stack limits. | • Using `Math.pow(2, i)` directly—double‑precision loss.<br>• Over‑complicating with bit‑mask tricks that obscure logic. | • Stick to an iterative reverse loop.<br>• Prefer clear integer arithmetic over floating‑point or bit hacks. |

---

## 7. SEO‑Optimised Blog Post <a name="blog"></a>

### Title  
**“Master LeetCode 3307: Find the K‑th Character in String Game II – Java, Python & C++ Solutions”**

### Meta Description  
Solve LeetCode 3307 (hard) in minutes. Learn the reverse‑simulation trick, get Java/Python/C++ code, and interview‑ready explanations. Perfect for software engineers prepping for coding interviews.

### Headings & Content

1. **What is LeetCode 3307?**  
   *Explain the problem, constraints, and why it’s labeled “hard”.*

2. **Why the Straight‑Forward Approach Fails**  
   *Building the string explodes in memory (2¹⁰⁰ chars).*

3. **The Key Insight: Reverse Simulation**  
   *Both ops double length → we can shrink `k`.*

4. **Step‑by‑Step Algorithm**  
   *Show the pseudocode with diagrams.*

4. **Corner Cases & Edge Handling**  
   *Clamping lengths, 1‑based indexing, shift modulo.*

5. **Java Implementation**  
   *Full code + commentary.*

6. **Python Implementation**  
   *Full code + commentary.*

7. **C++ Implementation**  
   *Full code + commentary.*

8. **Complexity Analysis**  
   *O(n) time, O(n) space – great for interviewers.*

9. **Common Pitfalls & Fixes**  
   *Good/Bad/Ugly table.*

10. **Takeaway – What Interviewers Want**  
    *Clear reasoning, optimal time, concise code.*

11. **Ready to Impress in Your Next Interview?**  
    *Practice with this solution, share on LinkedIn, ask recruiters for coding challenges.*

### Keywords  
- LeetCode 3307  
- K-th character string game  
- Reverse simulation coding interview  
- Hard leetcode solutions Java Python C++  
- Coding interview preparation  

### Calls to Action  
> **Try It Now!** Copy the code, run it on LeetCode, and see the time‑savings.  
> **Share Your Thoughts** in the comments—let’s discuss optimisations and alternate tricks.  

### Image & Schema  
Include a diagram of the reverse walk (two arrows pointing left), and add a JSON‑LD schema for *TechArticle*.

---

### Why This Blog Helps Your Career

* **Visibility** – Targeted keywords (“LeetCode 3307 solution”, “Java interview problem”, “coding interview hard”) attract recruiters.  
* **Credibility** – Providing clean, multi‑language solutions shows breadth of knowledge.  
* **Engagement** – The “Good/Bad/Ugly” table is a quick reference that readers bookmark.  

Publish on a personal blog or GitHub Pages, link it in your résumé, and watch hiring managers take note.

---

## 8. Final Thoughts <a name="final"></a>

The LeetCode 3307 challenge is a classic *reverse‑thinking* problem.  
By observing that every operation merely doubles the string and that the appended part is either an identical or a shifted copy, we can collapse the search into a few arithmetic steps.

Remember:  
* **Never build the string.**  
* **Reverse‑simulate** while accumulating shifts.  
* **Clamp lengths** to avoid overflow.  

With the snippets above in Java, Python, or C++, you can answer the 10¹⁴‑th character in less than a millisecond—ready for your next coding interview.

Happy coding! 🚀

--- 

**Happy Interview Prep!**  
Feel free to ask follow‑up questions on the comments or on your personal network.