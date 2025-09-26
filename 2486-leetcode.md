---
title: LeetCode 2486. Append Characters to String to Make Subsequence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Append Characters to String to Make Subsequence – 2486 (LeetCode)

## TL;DR  
Find the longest prefix of **t** that already appears as a subsequence in **s**.  
The answer is simply `len(t) – matched`.  
Complexity: **O(n + m)** time, **O(1)** space.

---

## Problem Statement

You are given two strings `s` and `t` containing only lowercase English letters.

Return the minimum number of characters that must be appended to the *end* of `s` so that `t` becomes a subsequence of the new string.

> **Subsequence** – a string that can be derived from another by deleting zero or more characters without changing the order of the remaining characters.

> **Examples**

| s        | t        | Output | Explanation |
|----------|----------|--------|-------------|
| "coaching" | "coding" | 4 | Append "ding". |
| "abcde"  | "a"      | 0 | Already a subsequence. |
| "z"      | "abcde"  | 5 | Append entire `t`. |

**Constraints**

- `1 ≤ s.length, t.length ≤ 10⁵`
- `s` and `t` consist only of lowercase English letters.

---

## The Good, the Bad, and the Ugly

| Aspect | Why it matters | How to handle it |
|--------|----------------|------------------|
| **Good** | *Two‑pointer approach* is linear and constant‑space. | Iterate once over both strings, incrementing pointers only when a match is found. |
| **Bad** | Many candidates confuse *subsequence* with *substring* or *prefix*. | Keep the definition in mind; we’re matching characters in order, but they can be non‑contiguous in `s`. |
| **Ugly** | Edge cases where `s` is empty or `t` is longer than `s`. | The algorithm naturally returns `len(t)` when no characters match. No special handling needed. |

---

## Algorithm – Two Pointers

1. **Initialize two indices**  
   `i` – index in `s` (0 … `s.length‑1`)  
   `j` – index in `t` (0 … `t.length‑1`)

2. **Traverse `s`**  
   While `i < s.length` and `j < t.length`:  
   - If `s[i] == t[j]`, move **both** `i` and `j`.  
   - Otherwise, move only `i`.

3. **Result**  
   The number of characters left in `t` is `t.length - j`.  
   This is the minimal number of characters that must be appended to `s`.

---

## Correctness Proof (Sketch)

Let `k = j` after the loop.  
All indices `0 … k‑1` of `t` have been matched in order inside `s`.  
By construction, no later character of `t` (`k … t.length‑1`) has a corresponding character in `s` after the matched prefix, otherwise the loop would have matched it and increased `j`.  

Appending the suffix `t[k …]` to the end of `s` creates a new string where the first `k` characters of `t` are already a subsequence (the matched part) and the remaining suffix appears verbatim at the end.  
Thus `t` becomes a subsequence of the new string.  

No smaller number of characters can suffice because every character of `t[k …]` is not present in the matched part of `s`.  
Therefore, `t.length – k` is optimal.

---

## Complexity Analysis

| Metric | Calculation |
|--------|-------------|
| **Time** | Each character of `s` is examined at most once. `O(|s| + |t|)` |
| **Space** | Only two integer indices. `O(1)` |

---

## Implementation

Below are clean, idiomatic solutions in **Java**, **Python**, and **C++**.

### 1. Java

```java
class Solution {
    public int appendCharacters(String s, String t) {
        int i = 0, j = 0;
        int n = s.length(), m = t.length();

        while (i < n && j < m) {
            if (s.charAt(i) == t.charAt(j)) {
                j++;                     // matched a character of t
            }
            i++;                         // always move in s
        }
        return m - j;                   // characters left to append
    }
}
```

> **Why this works** – `i` scans `s` linearly. Whenever we hit a character of `t`, we move `j`. After the loop `j` equals the longest prefix of `t` that is a subsequence of `s`.

### 2. Python

```python
class Solution:
    def appendCharacters(self, s: str, t: str) -> int:
        i = j = 0
        while i < len(s) and j < len(t):
            if s[i] == t[j]:
                j += 1
            i += 1
        return len(t) - j
```

### 3. C++

```cpp
class Solution {
public:
    int appendCharacters(string s, string t) {
        int i = 0, j = 0;
        while (i < (int)s.size() && j < (int)t.size()) {
            if (s[i] == t[j]) ++j;
            ++i;
        }
        return t.size() - j;
    }
};
```

All three snippets follow the same two‑pointer pattern.

---

## Unit Tests (Python example)

```python
def test():
    sol = Solution()
    assert sol.appendCharacters("coaching", "coding") == 4
    assert sol.appendCharacters("abcde", "a") == 0
    assert sol.appendCharacters("z", "abcde") == 5
    assert sol.appendCharacters("", "abc") == 3
    assert sol.appendCharacters("abc", "") == 0
    assert sol.appendCharacters("aaa", "aaab") == 1
    print("All tests passed.")
```

Run `python -m unittest` to validate the implementation.

---

## SEO‑Optimized Blog Post

> **Title:** *Append Characters to String to Make Subsequence – 2486 | LeetCode Solution (Java/Python/C++)*  
> **Meta Description:** Master LeetCode #2486 with a clean two‑pointer solution in Java, Python, and C++. Understand subsequences, edge cases, and optimal runtime.  

### Introduction  

LeetCode 2486, “Append Characters to String to Make Subsequence,” is a favorite for string manipulation interviews. In this post we break down the problem, present a **fast** and **memory‑friendly** solution, and deliver ready‑to‑copy code in Java, Python, and C++.  

### The Problem in One Line  

> Find the minimal number of characters you need to append to `s` so that `t` becomes a subsequence of the new string.  

### Why It’s a Classic Interview Question  

- Tests your understanding of **subsequence vs. substring**.  
- Requires an **O(n+m)** solution; many naïve solutions run in quadratic time.  
- Shows off your skill with **two‑pointer** techniques—a staple in coding interviews.  

### Approach Overview  

1. Scan `s` with a pointer `i`.  
2. Keep a second pointer `j` for `t`.  
3. Every time `s[i]` equals `t[j]`, increment `j`.  
4. After the scan, `j` indicates how many characters of `t` were matched.  
5. The answer is `len(t) – j`.  

This is **linear time** and **constant space**—the best you can hope for.  

### Handling Edge Cases  

- `s` empty → answer = `len(t)`.  
- `t` longer than `s` → the same formula works.  
- Duplicate letters – the two‑pointer scan still works because we only care about order, not contiguity.  

### Code Snippets  

*(Insert Java, Python, C++ code blocks from above.)*  

### Quick‑Start Test Suite  

*(Insert test code.)*  

### Summary  

The two‑pointer solution is the **good** part: fast, simple, and easy to reason about. The **bad** part is the confusion between subsequence and substring; remember the definition! The **ugly** part often shows up in edge cases (empty strings, very long `t`), but the algorithm gracefully handles them without extra branches.

**Want to ace your next coding interview?** Practice this problem, tweak the code for different languages, and explain the two‑pointer idea clearly to your interviewer.  

---

## Final Thoughts

- **Keep the definition of subsequence in mind.**  
- The two‑pointer solution is the gold standard.  
- The implementation is identical across languages—just adapt syntax.  

Good luck with your LeetCode practice and your upcoming interviews! If you liked this article, feel free to share it on LinkedIn or Twitter. Happy coding!