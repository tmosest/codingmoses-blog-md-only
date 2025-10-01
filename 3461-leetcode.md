---
title: LeetCode 3461. Check If Digits Are Equal in String After Operations I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 3461 – “Check If Digits Are Equal in String After Operations I”

> **Problem #3461** – *Easy*  
> **Tags**: Array, String, Simulation  
> **Difficulty**: 200/1600 LeetCode points  

---

## Table of Contents

| # | Section | Why it matters |
|---|---------|----------------|
| 1 | 🔍 Problem Statement | Understand the goal |
| 2 | 💡 Intuition | Why the simple simulation works |
| 3 | 🧩 Brute‑Force (O(n²)) | The cleanest solution that most interviewers expect |
| 4 | ⚙️ Alternative (O(n)) | How to squeeze a bit of speed |
| 5 | 📊 Complexity | Time & space |
| 6 | 🐛 Edge Cases | What to watch out for |
| 7 | 📦 Code | Java, Python, C++ |
| 8 | 💬 Good / Bad / Ugly | Interview‑friendly code |
| 9 | 🎯 Interview Tips | What the hiring manager really cares about |
| 10 | 📚 Conclusion | Take‑away |

---

## 1️⃣ Problem Statement

> You’re given a string **s** consisting only of digits (`'0'`–`'9'`).  
> Repeatedly apply the following operation until **s** has **exactly two digits**:
> 1. For each adjacent pair `s[i]` and `s[i+1]`, compute `(int(s[i]) + int(s[i+1])) % 10`.  
> 2. Build a new string from these computed digits (in order).  
> Finally, return **true** if the two remaining digits are equal, otherwise **false**.

**Example**

```text
s = "3902" → "292" → "11" → true
```

**Constraints**

- `3 <= s.length <= 100`
- `s` contains only digits

---

## 2️⃣ Intuition

Each operation reduces the length of the string by **exactly 1** because you always replace two characters with one.  
Thus, after `n-2` operations (`n = s.length`), you’ll be left with two digits.  
The operation is a simple pairwise sum modulo 10 – nothing fancy.  
Because the input size is tiny (`≤ 100`), a straightforward simulation is perfectly fine and easy to reason about.

---

## 3️⃣ Brute‑Force (O(n²))

The most natural solution is to **simulate** the process literally:

```text
while s.length > 2:
    build new string from adjacent sums
    s = new string
return s[0] == s[1]
```

This is what most interviewers expect – a clear, bug‑free implementation that matches the problem description verbatim.

### Why O(n²)?

- In the first pass we examine `n-1` pairs, next pass `n-2`, … until 1 pair.
- The total number of examined pairs is `(n-1)+(n-2)+…+1 = n*(n-1)/2 = O(n²)`.

With `n ≤ 100`, the algorithm runs in milliseconds.

---

## 4️⃣ Alternative (O(n))

Because we only need the final **two** digits, we can avoid building every intermediate string:

1. Keep an **array of integers** `digits`.  
2. Repeatedly compute a new array of length `len-1` with the same formula.  
3. Stop when the array length is 2.

This saves the overhead of string concatenation but still has the same `O(n²)` arithmetic operations.  
In practice, the difference is negligible for `n ≤ 100`, but the array‑based version is a good showcase of thinking about memory layout.

---

## 5️⃣ Complexity

| Approach | Time | Space |
|----------|------|-------|
| Brute‑Force (String) | `O(n²)` | `O(n)` (new string each iteration) |
| Array‑based | `O(n²)` | `O(n)` (two integer arrays) |

Both meet the constraints comfortably.

---

## 6️⃣ Edge Cases

| Case | Why it matters | What to test |
|------|----------------|--------------|
| All digits equal (`"11111"`) | Early exit not required but still works | Should return `true` |
| Single unique digit but not all same (`"10000"`) | Handles zeros correctly | Should return `false` if final two differ |
| Leading zeros (`"00123"`) | `int('0')` is 0, works normally | Should still return correct result |
| Maximum length (`"9"*100`) | Ensures no overflow, modulo 10 keeps digits | Should run quickly |

---

## 7️⃣ Code

Below are clean, idiomatic implementations in **Java**, **Python**, and **C++**.  
All three follow the brute‑force simulation but use language‑specific idioms for brevity and performance.

> **Tip:** For interviews, keep the code simple and comment the intent – recruiters value clarity over micro‑optimizations.

---

### Java (OOP style, LeetCode accepted)

```java
public class Solution {
    public boolean hasSameDigits(String s) {
        // Reduce until two digits remain
        while (s.length() > 2) {
            StringBuilder next = new StringBuilder();
            for (int i = 0; i < s.length() - 1; i++) {
                int a = s.charAt(i) - '0';
                int b = s.charAt(i + 1) - '0';
                int digit = (a + b) % 10;
                next.append(digit);               // append as char
            }
            s = next.toString();
        }
        // Final check
        return s.charAt(0) == s.charAt(1);
    }
}
```

---

### Python (Concise, LeetCode style)

```python
class Solution:
    def hasSameDigits(self, s: str) -> bool:
        while len(s) > 2:
            s = ''.join(str((int(a) + int(b)) % 10)
                       for a, b in zip(s, s[1:]))
        return s[0] == s[1]
```

---

### C++ (Fast, 17/20 style)

```cpp
class Solution {
public:
    bool hasSameDigits(string s) {
        while (s.size() > 2) {
            string next;
            next.reserve(s.size() - 1);
            for (size_t i = 0; i + 1 < s.size(); ++i) {
                char d = char(((s[i] - '0') + (s[i+1] - '0')) % 10);
                next.push_back(d);
            }
            s.swap(next);
        }
        return s[0] == s[1];
    }
};
```

---

## 8️⃣ Good / Bad / Ugly – Interview‑friendly Coding

| ✅ Good | ⚠️ Bad | 👿 Ugly |
|--------|--------|--------|
| **Clarity** – The algorithm follows the statement verbatim. | **Unnecessary complexity** – Over‑engineering (e.g., trying to skip loops) can hurt readability. | **Magic numbers** – Using `10` without explaining modulo can look like a hack. |
| **Correctness** – Handles all edge cases (zeros, repeated digits, leading zeros). | **Lack of comments** – Recruiters might not understand the intent. | **Poor variable names** – `a`, `b`, `c` instead of `digit1`, `digit2`. |
| **Test‑friendly** – Easy to write unit tests for each pass. | **Ignoring constraints** – Trying to over‑optimize for `n = 10⁶` when `n ≤ 100` can waste time. | **Wrong data type** – Using `int` overflow in Java or C++ (not needed but shows lack of attention). |

**Interview‑gold standard**: Keep the code *simple*, *correct*, *well‑commented*, and *free of unnecessary micro‑optimizations*.

---

## 8️⃣ 💬 Interview Discussion: Good / Bad / Ugly

| Category | What the recruiter wants | Common pitfalls |
|----------|--------------------------|-----------------|
| **Good** | *Readable code.* Use descriptive variable names (`next`, `digit`). Add a one‑line comment before the loop: “simulate the digit folding.” | None |
| **Bad** | *Unnecessary abstraction.* Splitting the logic into multiple private helper methods for such a small problem can appear overkill. | Avoid premature abstraction. |
| **Ugly** | *Hidden bugs.* Using `s.charAt(i+1)` without bounds checking may throw `StringIndexOutOfBoundsException`. | Always guard with `i < s.length()-1`. |

---

## 9️⃣ Interview Tips

1. **Ask clarifying questions**  
   - “Do you want me to stop early if the two digits become equal before finishing all operations?”  
   - “Is it acceptable to use integer arrays instead of strings?”  

   Recruiters love candidates who verify assumptions.

2. **Explain the algorithm before coding**  
   - Talk about the reduction factor (`len-1` pairs per iteration).  
   - Mention the modulo operation keeps the result in `0–9`.

3. **Mention complexity**  
   - Even though `O(n²)` is trivial for the given constraints, explaining the arithmetic series shows you understand the underlying math.

4. **Write clean code**  
   - Use `StringBuilder` in Java, `join` in Python, `reserve` in C++ to avoid dynamic reallocations.

5. **Test your solution**  
   - Try edge cases on the console or LeetCode’s “Run Code” button.  
   - Verify that `"11111"` returns `true`, `"00123"` returns whatever is expected.

6. **Show problem‑solving mindset**  
   - If asked for a more efficient solution, discuss the idea of using an array or noting that the input size is tiny, so the naive simulation is preferable for clarity.

---

## 🎯 Keywords to Rank Your Blog

- LeetCode 3461  
- Check If Digits Are Equal  
- Java solution  
- Python solution  
- C++ solution  
- String manipulation interview  
- Digit folding algorithm  
- Simulation problems  
- Coding interview tips  
- Algorithm complexity  

Adding these keywords in the title, headers, and meta description will help recruiters find this post when they’re searching for LeetCode interview prep.

---

## 📚 Take‑away

- **LeetCode 3461** is all about simulating a simple pairwise digit sum.  
- A *literal* implementation in Java, Python, or C++ is both correct and interview‑friendly.  
- Understand the `O(n²)` cost, but know that it’s trivial for the problem’s constraints.  
- Edge cases (leading zeros, all‑same digits) don’t break the solution.  
- For an interview, focus on **clarity** and **correctness**; micro‑optimizations are secondary.

---

### 👋 Final Thought

Mastering small “simulation” problems like 3461 builds a strong foundation for more complex array transformations and dynamic programming.  
Use this as a stepping stone toward problems like *“Array Reduction”*, *“Additive Array”*, and *“Binary Tree Folding”* – all of which appear in higher‑level LeetCode contests and technical interviews.

Good luck in your next interview – you’ve got this!