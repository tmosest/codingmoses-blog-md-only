---
title: LeetCode 880. Decoded String at Index - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Decoded String at Index – LeetCode 880  
**Languages:** Java | Python | C++  
**Tag:** Medium | String | Stack | Reverse‑Iteration | Interview Question  

> **Why this problem matters:**  
>  * A classic example of “reverse engineering” a decoded string without actually building it.  
>  * Demonstrates clever use of arithmetic and space‑optimal thinking.  
>  * Perfect for Java/Python/C++ interviews; you’ll see it on sites like LeetCode, InterviewBit, and HackerRank.

---

### 🚀 1. Problem Overview (LeetCode 880)

> **Input:**  
>  * `s` – an encoded string consisting of lowercase letters and digits `2‑9`.  
>  * `k` – a 1‑based index into the fully decoded string.  
> **Output:**  
>  * The `k`‑th character of the decoded string.

**Example**

| `s`          | `k` | Decoded string (abbreviated) | Output |
|--------------|-----|------------------------------|--------|
| `"leet2code3"` | 10  | `leetleetcodeleetleetcode...` | `"o"` |
| `"ha22"`         | 5   | `hahahaha` | `"h"` |

**Constraints**

- `2 ≤ s.length ≤ 100`  
- `1 ≤ k ≤ 10^9`  
- The decoded string length fits in a 64‑bit integer (`< 2^63`).  

---

### 🧠 2. Core Idea – “Reverse Iteration”

Instead of decoding the string (which could be astronomically long), we walk **backwards**:

1. **Keep a running `size`** – the length of the decoded string up to the current point.
2. Process characters from the end:
   - **Letter** → `size++` (since the decoded string grows by one).
   - **Digit `d`** → the current string was repeated `d` times.  
     `size = size / d` (undo the repeat).
3. While iterating, reduce `k` with `k %= size` whenever `k` exceeds the current size.  
   This step maps `k` back into the substring that was repeated.

When we finally reach a letter, that letter is the answer.

> **Why it works**  
> Reversing the process is equivalent to “undoing” the expansion.  
> Every time we see a digit, we compress the string length by dividing by that digit, essentially “rolling back” the repeat.  
> `k % size` keeps `k` within the bounds of the current uncompressed portion.

---

### 📊 3. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| One pass over `s` | **O(n)** (`n ≤ 100`) | **O(1)** (only a few variables) |

No additional data structures (e.g., stacks or strings) are needed, which makes this solution extremely efficient.

---

### 📦 4. Code Implementations

> **Tip:** Use `long` / `int64_t` to hold the string length because it can go up to `2^63‑1`.

---

#### Java

```java
public class Solution {
    public char decodeAtIndex(String s, long k) {
        long size = 0;

        // First pass: compute final size
        for (char c : s.toCharArray()) {
            if (Character.isLetter(c)) {
                size++;
            } else {                // digit
                size *= c - '0';
            }
        }

        // Second pass: reverse iterate
        for (int i = s.length() - 1; i >= 0; i--) {
            char c = s.charAt(i);

            if (Character.isLetter(c)) {
                if (k == size) return c;   // we hit the exact position
                size--;                     // shrink one letter
            } else {                          // digit
                size /= c - '0';             // undo repetition
                k %= size;                   // map k back to prefix
            }
        }
        return ' '; // unreachable
    }
}
```

---

#### Python

```python
class Solution:
    def decodeAtIndex(self, s: str, k: int) -> str:
        size = 0

        # Compute final decoded string length
        for c in s:
            if c.isdigit():
                size *= int(c)
            else:
                size += 1

        # Walk backwards
        for c in reversed(s):
            if c.isdigit():
                size //= int(c)
                k %= size
            else:
                if k == size:
                    return c
                size -= 1
```

---

#### C++

```cpp
class Solution {
public:
    char decodeAtIndex(string s, long long k) {
        long long size = 0;

        // First pass – final length
        for (char c : s) {
            if (isdigit(c))
                size *= c - '0';
            else
                size += 1;
        }

        // Reverse traversal
        for (int i = s.size() - 1; i >= 0; --i) {
            char c = s[i];
            if (isdigit(c)) {
                size /= c - '0';
                k %= size;
            } else { // letter
                if (k == size) return c;
                size -= 1;
            }
        }
        return ' '; // shouldn't happen
    }
};
```

---

### 📚 5. Blog‑Style Deep Dive

> **Title**: *Decode the Future: Mastering LeetCode 880 – “Decoded String at Index”*  
> **Keywords**: LeetCode 880, Decoded String at Index, Java solution, Python solution, C++ solution, coding interview, algorithm, reverse iteration, job interview prep

---

#### 1️⃣ Introduction

If you’re preparing for technical interviews, you’ll soon hit the *decoded string* problem. LeetCode #880 – “Decoded String at Index” – is a textbook example of how a seemingly complex task can be solved with a surprisingly simple trick: reverse iteration. In this article, we’ll walk through the problem, explain the “good, the bad, and the ugly” of typical solutions, and show you clean, production‑ready code in Java, Python, and C++.

---

#### 2️⃣ Problem Snapshot

- **Goal**: Find the `k`‑th character of a decoded string.  
- **Encoded format**: `letter digit digit …` where digits repeat the entire current string.  
- **Constraints**: Up to `10^9` length – you *cannot* build the string.

---

#### 3️⃣ Why the Reverse Approach is the Hero

| Traditional Method | Reverse Iteration |
|---------------------|-------------------|
| Build the whole string → O(2^n) memory/time | Walk backwards → O(n) memory/time |
| Easy to write, but fails on large inputs | Slightly more complex, but elegant |
| Fails the “decoded string < 2^63” rule | Meets constraints automatically |

The reverse strategy is essentially *undoing* the expansions: each digit indicates that the current string was repeated; dividing the length by that digit is the inverse operation. While iterating backwards, we keep a running `size` (the current decoded length) and adjust `k` with modulus to stay within bounds.

---

#### 4️⃣ Implementation Walk‑through

**Step 1 – Compute Final Length**

```text
size = 0
for each char c in s:
    if c is digit: size *= int(c)
    else: size += 1
```

*Why?* We need the full length to map `k` correctly when reversing.

**Step 2 – Reverse Pass**

```text
for i from len(s)-1 down to 0:
    c = s[i]
    if c is digit:
        size /= int(c)   // undo repeat
        k %= size        // keep k in the current prefix
    else:
        if k == size: return c
        size -= 1        // remove the last letter
```

The key is `k == size`: when we encounter a letter and `k` matches the current size, we’ve pinpointed the answer.

---

#### 5️⃣ Common Pitfalls (The Ugly)

| Pitfall | What Happens | Fix |
|---------|--------------|-----|
| **Using `int` for size** | Overflow for large decoded lengths. | Use `long` / `int64_t`. |
| **Iterating forward** | Must allocate gigantic string → Memory Error. | Iterate backwards; no large data structures. |
| **Modding too early** | `k % size` before size is updated → Wrong mapping. | Apply `k %= size` *after* dividing size by the digit. |
| **Off‑by‑one** | `k` is 1‑based, but size counts 0‑based. | Keep `size` as 1‑based (increment for letters), use `k == size`. |

---

#### 6️⃣ Why This Problem Rocks for Interviews

- **Shows algorithmic thinking**: Recognizing that you can reverse the process.
- **Highlights space optimization**: Solving in `O(1)` space is a prized skill.
- **Languages cross‑platform**: Same logic in Java, Python, C++ – good for multi‑language interviewers.
- **Discussable edge cases**: Talk about large numbers, overflow, and the 2^63 constraint.

When asked about it in an interview, you can confidently say:

> “Instead of building the string, I first compute its final length and then walk backwards, undoing each repetition. This keeps both time and space linear in the length of the encoded string, satisfying the problem’s strict constraints.”

---

#### 7️⃣ SEO‑Friendly Takeaway

If you’re looking to land your dream tech role, mastering this problem is a *must‑have* on your résumé. Search terms like “Decoded String at Index solution”, “LeetCode 880 Java”, or “reverse iteration interview question” are high‑traffic. Adding this problem to your portfolio (e.g., GitHub, LeetCode profile) signals to recruiters that you’re comfortable with clever algorithmic tricks and can write clean, efficient code across major languages.

---

#### 8️⃣ Final Thoughts

- **The good**: A one‑pass, constant‑space solution that works for gigantic decoded strings.  
- **The bad**: The temptation to naively build the string and run into memory issues.  
- **The ugly**: Overlooking overflow or misapplying modulus.  

With the Java, Python, and C++ code snippets above, you’re ready to ace the “Decoded String at Index” question in any technical interview. Keep practicing, keep explaining your logic clearly, and you’ll impress both hiring managers and automated graders alike.

Happy coding! 🚀

---

#### 📚 References

- LeetCode Problem 880: <https://leetcode.com/problems/decoded-string-at-index>  
- Official Solutions: Java, Python, C++ (reverse‑iteration)  
- Interview prep blogs on reverse‑iteration strategies  
- Job interview checklist: “Algorithmic Patterns”  

---