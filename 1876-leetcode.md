---
title: LeetCode 1876. Substrings of Size Three with Distinct Characters - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1876. Substrings of Size Three with Distinct Characters  
### A Step‑by‑Step Guide – Java, Python & C++ – With a Job‑Ready Blog Article

> **Goal:** Count how many contiguous substrings of length 3 in a string contain *three distinct characters* (no repeats).

> **Why It Matters:**  
> - LeetCode #1876 is a common “easy” interview question.  
> - The problem tests your understanding of *sliding windows* and *time‑space trade‑offs*.  
> - Mastering it boosts your score on coding‑interview platforms and gives you a talking point for recruiters.

Below you’ll find:

1. The **problem statement** (plain English).  
2. A **sliding‑window** solution in **Java, Python, and C++**.  
3. A **blog‑style write‑up** that explores the “good, the bad, and the ugly” of the problem – SEO‑optimized for recruiters.  

Feel free to copy‑paste the code into your IDE, run the test cases, and use the blog draft to showcase your problem‑solving process on LinkedIn, Medium, or a personal portfolio.

---

## 1. Problem Statement (Plain English)

> **Input** – a string `s` of lowercase English letters, length 1 ≤ |s| ≤ 100.  
> **Output** – the number of substrings of length 3 that have *three unique characters*.  
> Each overlapping window counts separately, even if the substring text is identical.

**Example**

```
Input : "xyzzaz"
Substrings of length 3 : ["xyz", "yzz", "zza", "zaz"]
Good ones (no repeats) : ["xyz"] → answer = 1
```

---

## 2. Sliding‑Window Solution

Because the window size is *fixed at 3*, we can move a sliding window across the string once.  
For every window we perform three constant‑time comparisons:

```text
if s[l] != s[m] and s[l] != s[r] and s[m] != s[r]
```

If the test passes, we increment the counter.  
We slide the window by incrementing the three indices (or simply iterating `i` from 0 to `len(s)-3` and checking `s[i]`, `s[i+1]`, `s[i+2]`).

### Complexity

| **Operation** | **Time** | **Space** |
|---------------|----------|-----------|
| Iteration over all windows | **O(n)** | **O(1)** (besides the input string) |

`n` = length of `s`.  
The algorithm is linear and uses constant extra memory.

---

### Java Implementation

```java
// Java 17
public class Solution {
    public int countGoodSubstrings(String s) {
        int count = 0;
        // No need to copy the string into a char array – charAt is O(1).
        for (int i = 0; i + 2 < s.length(); i++) {
            char a = s.charAt(i);
            char b = s.charAt(i + 1);
            char c = s.charAt(i + 2);
            if (a != b && a != c && b != c) {
                count++;
            }
        }
        return count;
    }
}
```

> **Why no array?**  
> Converting to a `char[]` is *O(n)* extra work and memory.  
> Using `charAt()` keeps the solution lean.

---

### Python Implementation

```python
# Python 3
class Solution:
    def countGoodSubstrings(self, s: str) -> int:
        count = 0
        for i in range(len(s) - 2):
            a, b, c = s[i], s[i+1], s[i+2]
            if a != b and a != c and b != c:
                count += 1
        return count
```

> Python’s slicing could be used (`s[i:i+3]`), but it allocates a new string each time – we avoid that for O(1) memory.

---

### C++ Implementation

```cpp
// C++17
class Solution {
public:
    int countGoodSubstrings(string s) {
        int count = 0;
        for (size_t i = 0; i + 2 < s.size(); ++i) {
            char a = s[i];
            char b = s[i + 1];
            char c = s[i + 2];
            if (a != b && a != c && b != c) {
                ++count;
            }
        }
        return count;
    }
};
```

> Using `size_t` prevents signed‑unsigned warnings and handles the maximum input size gracefully.

---

## 3. Blog‑Style Write‑Up: The Good, The Bad, & The Ugly

> **Title (SEO‑friendly):** *Mastering LeetCode 1876 – Counting Good Substrings of Length Three (Java/Python/C++) – A Complete Interview Guide*  
> **Meta‑Description:** *Learn how to solve LeetCode #1876 – “Substrings of Size Three with Distinct Characters.” Find Java, Python, and C++ solutions, understand sliding windows, and boost your coding interview score.*

---

### Introduction

In the world of technical interviews, *LeetCode 1876* is a favourite “quick‑fire” problem.  
Despite its simplicity, it reveals subtle nuances: you must think about *fixed‑size windows*, *overlapping substrings*, and *constant‑time comparisons*.  

Below we dissect the problem, present a clean solution in three major languages, and share the hidden trade‑offs recruiters love to probe.

---

#### The Problem in a Nutshell

Given a string `s` (1 ≤ |s| ≤ 100) of lowercase letters, count the number of contiguous substrings of **exactly three** characters that contain **no duplicate characters**.  

*Why 3?*  
Because the window size is fixed, we can use a *sliding‑window* approach that runs in linear time.

---

### The “Good” – What Makes This Problem Great

1. **Clarity of Constraints**  
   - Small input size (≤ 100) means we can afford O(n²) if needed, but the optimal O(n) solution is trivial.  
   - Fixed window size eliminates the need for dynamic programming or nested loops.

2. **Focus on Fundamental Concepts**  
   - *Sliding window* technique is a staple in interviews.  
   - *Set* usage vs. *index comparison* showcases two ways to check uniqueness.

3. **Clear Testing**  
   - Easy to create edge‑case tests (`"aaa"`, `"abc"`, `"abcd"`).  
   - Reproducible results across languages.

---

### The “Bad” – Pitfalls That Can Trip You Up

| Pitfall | Why It Happens | Remedy |
|---------|----------------|--------|
| Converting to a `char[]` unnecessarily | Some coders copy the string for easier indexing | Use `charAt()` (Java) or direct indexing (Python/C++) |
| Off‑by‑one errors in loop bounds | Forget that the last valid start index is `len-3` | `for (i = 0; i + 2 < len; ++i)` |
| Assuming a Set is needed | Using a Set for every window is overkill | Simple pairwise comparison is O(1) |
| Forgetting overlapping substrings | Counting only unique substrings misses duplicates | Each window counts separately |

---

### The “Ugly” – Hidden Complexity

| Issue | Impact | Best Practice |
|-------|--------|---------------|
| **Time‑space trade‑off** | Copying string -> O(n) space | Keep operations in place |
| **Large‑scale input** | While constraints are small, an interview may ask for `|s| ≤ 10⁵` | Still linear, but watch for integer overflow (`int` vs `long`) |
| **Language‑specific quirks** | Python’s slicing allocates new strings | Use indexing or `memoryview` if performance matters |

---

### Code Walkthrough

> We’ll walk through the Java solution in detail; the Python & C++ versions follow the same logic.

```java
public class Solution {
    public int countGoodSubstrings(String s) {
        int count = 0;
        // Sliding window: start index i, window is s[i], s[i+1], s[i+2]
        for (int i = 0; i + 2 < s.length(); i++) {
            char a = s.charAt(i);
            char b = s.charAt(i + 1);
            char c = s.charAt(i + 2);
            // Three pairwise distinct checks
            if (a != b && a != c && b != c) {
                count++;
            }
        }
        return count;
    }
}
```

**What’s happening?**

1. **Loop bounds** – `i + 2 < s.length()` ensures the window never goes out of bounds.  
2. **Direct char access** – `charAt` is O(1) in Java.  
3. **Three comparisons** – each is a constant‑time operation; no set or hash lookup.  
4. **Increment counter** – every good substring is counted, even if identical.

The same logic applies in Python (`s[i]`, `s[i+1]`, `s[i+2]`) and C++ (`s[i]`, `s[i+1]`, `s[i+2]`).

---

### Alternative Approaches (for Fun)

| Approach | Complexity | When to Use |
|----------|------------|-------------|
| **HashSet per window** | O(n) time, O(1) extra space (since set size is 3) | If window size is variable or larger; less efficient for fixed 3 |
| **Bitmask** | O(n) time, O(1) space | Nice for `O(1)` comparisons if you want to encode characters as bits |
| **Regular Expression** | O(n) time, O(1) space (if regex engine is linear) | Quick hack in scripting languages |

---

### What Recruiters Look For

| Skill | Interview Cue |
|-------|---------------|
| **Algorithmic thinking** | Recognize sliding window pattern |
| **Attention to detail** | Correct loop bounds & character comparisons |
| **Coding style** | Clean, commented, language‑idiomatic code |
| **Problem analysis** | Discuss edge cases, time/space trade‑offs |

> *Tip:* In an interview, after presenting the code, briefly explain the *why* behind each decision. Recruiters love candidates who can justify their choices.

---

### Final Thoughts

LeetCode 1876 is deceptively simple but packs essential interview themes:

- **Sliding window** (fixed size).  
- **Uniqueness checks** without heavy data structures.  
- **Edge‑case awareness** (overlaps, single character strings).  

Mastering this problem demonstrates your ability to translate a clear requirement into efficient, clean code—exactly what hiring managers want.

> **Next Step:** Practice variations:  
> - “Substrings of size K with distinct characters” (generalize to any K).  
> - “Count substrings with at most K distinct characters.”  
> - “Find the longest substring with distinct characters.”  

Good luck, and happy coding!

---

## 4. Ready‑to‑Publish Blog Post (Markdown)

```markdown
# Mastering LeetCode 1876 – Counting Good Substrings of Length Three (Java/Python/C++)

**LeetCode #1876** – *Substrings of Size Three with Distinct Characters*  
_A complete interview guide with solutions, trade‑offs, and SEO‑friendly insights._

---

## 🚀 Problem Overview

- **Input**: string `s` (1 ≤ |s| ≤ 100), lowercase letters.  
- **Output**: number of **contiguous** 3‑character substrings that contain **no duplicate** characters.  
- **Why “3”?** Because the window size is fixed → we can slide it in O(n) time.

---

## 🔧 Optimal Solution: Sliding Window

| Language | Time | Extra Space |
|----------|------|-------------|
| Java | O(n) | O(1) |
| Python | O(n) | O(1) |
| C++ | O(n) | O(1) |

**Key Idea**: For each start index `i`, examine `s[i]`, `s[i+1]`, `s[i+2]`.  
If all three are pairwise distinct, increment the counter.

---

## 🧪 Code Snippets

### Java

```java
public class Solution {
    public int countGoodSubstrings(String s) {
        int count = 0;
        for (int i = 0; i + 2 < s.length(); i++) {
            char a = s.charAt(i);
            char b = s.charAt(i + 1);
            char c = s.charAt(i + 2);
            if (a != b && a != c && b != c) count++;
        }
        return count;
    }
}
```

### Python

```python
class Solution:
    def countGoodSubstrings(self, s: str) -> int:
        count = 0
        for i in range(len(s)-2):
            a, b, c = s[i], s[i+1], s[i+2]
            if a != b and a != c and b != c:
                count += 1
        return count
```

### C++

```cpp
class Solution {
public:
    int countGoodSubstrings(string s) {
        int count = 0;
        for (size_t i = 0; i + 2 < s.size(); ++i) {
            char a = s[i], b = s[i+1], c = s[i+2];
            if (a != b && a != c && b != c) ++count;
        }
        return count;
    }
};
```

---

## 📊 Trade‑Offs & Interview Tips

- Use **direct indexing**; avoid unnecessary `char[]` copies.  
- Remember loop bounds: last start index is `len-3`.  
- Pairwise comparisons (`a!=b && a!=c && b!=c`) are faster than using a `Set`.  
- Discuss edge cases: `"aaa"` → 0, `"abc"` → 1, `"abcd"` → 2.

---

## 📈 Recruiter Checklist

| Skill | What Recruiters Want |
|-------|---------------------|
| Algorithmic clarity | Identify sliding window |
| Code readability | Idiomatic language, comments |
| Problem‑analysis | Explain edge cases, time/space |

---

## 🔁 Variants to Practice

1. `K`‑size substrings with distinct characters.  
2. Substrings with *at most* `K` distinct characters.  
3. Longest substring with all distinct characters.

---

> *Pro Tip*: After coding, walk through the logic: why did you choose this approach? How does it scale? Recruiters love justifications.

---

## 📚 Final Words

LeetCode 1876 is a **micro‑problem** that showcases macro‑skills.  
Solve it efficiently, explain your reasoning, and you’ll shine in your next technical interview.

Happy coding! 💻

---

*Author: [Your Name] – Software Engineer, Interview Prep Enthusiast*
```

> Save the above markdown to a `.md` file or paste into your blog platform. It’s already optimized for **keywords** like *LeetCode 1876*, *good substrings*, *Java solution*, *Python solution*, *C++ solution*, *interview guide*.

---

## 5. Quick Test Cases

```text
Input   | Expected Output
--------|-----------------
"aaa"   | 0
"abc"   | 1
"abcd"  | 2
"abca"  | 1
"abcaab" | 2
"abcdefghijklmnopqrstuvwxyz" | 24
```

All three implementations produce identical results.

---

> **Wrap‑up**: Keep the code snippets ready in your IDE, practice explaining the *why*, and use the blog post as a portfolio piece for your personal website or LinkedIn articles. Happy interview hunting!
```

---

**Congratulations!**  
You now have:

1. **Optimized, language‑specific solutions** for LeetCode 1876.  
2. **A ready‑to‑publish blog post** that’s SEO‑friendly and recruiter‑friendly.  
3. **Insights** into the subtle pitfalls recruiters love to test.  

Go forth and ace that interview! 🚀
```

--- 

*End of Document*  
