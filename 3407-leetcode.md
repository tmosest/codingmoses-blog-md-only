---
title: LeetCode 3407. Substring Matching Pattern - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # ✅ LeetCode 3407 – Substring Matching Pattern  
**Problem**  
> You are given a string `s` and a pattern string `p`.  
> `p` contains exactly one `*` character. The `*` can be replaced with any
> (possibly empty) sequence of characters.  
> Return `true` if `p` can be made a *substring* of `s`, otherwise return `false`.  

**Examples**  
| s | p | result | explanation |
|---|---|--------|-------------|
| `leetcode` | `ee*e` | `true` | `*` → `tcod` → `eetcode` |
| `car` | `c*v` | `false` | No matching substring |
| `luck` | `u*` | `true` | `*` → `{}` → `u` (or `uc`, `uck`) |

**Constraints**  

- `1 ≤ s.length ≤ 50`  
- `1 ≤ p.length ≤ 50`  
- `s` contains only lowercase English letters  
- `p` contains only lowercase English letters and exactly one `*`

---

## 🚀 Three Lightning‑Fast Solutions

The trick is simple:  
Split the pattern into the part before `*` (`prefix`) and the part after `*` (`suffix`).  
If both `prefix` and `suffix` appear in `s` **in that order** (with the suffix starting after the end of the prefix), then the pattern matches a substring of `s`.

### 1. Java (O(n) time, O(1) space)

```java
class Solution {
    public boolean hasMatch(String s, String p) {
        int star = p.indexOf('*');            // position of '*'
        String prefix = p.substring(0, star);
        String suffix = p.substring(star + 1);

        // Find the first occurrence of prefix
        int start = s.indexOf(prefix);
        if (start == -1) return false;

        // Find suffix starting after prefix ends
        int end = s.indexOf(suffix, start + prefix.length());
        return end != -1;
    }
}
```

### 2. Python (3‑lines, O(n) time, O(1) space)

```python
class Solution:
    def hasMatch(self, s: str, p: str) -> bool:
        pre, post = p.split('*')
        i = s.find(pre)
        return i != -1 and s.find(post, i + len(pre)) != -1
```

### 3. C++ (O(n) time, O(1) space)

```cpp
class Solution {
public:
    bool hasMatch(string s, string p) {
        size_t star = p.find('*');
        string pre = p.substr(0, star);
        string post = p.substr(star + 1);

        size_t i = s.find(pre);
        if (i == string::npos) return false;

        size_t j = s.find(post, i + pre.length());
        return j != string::npos;
    }
};
```

> **Why it works**  
> `indexOf` / `find` scans `s` only once for each part, so the overall complexity is linear in `|s|`.  
> We never need a dynamic‑programming table or backtracking – the pattern has only one wildcard.

---

## 📖 Blog Article: “The Good, The Bad, and the Ugly of Substring Matching Pattern”

### Introduction  
The **Substring Matching Pattern** (LeetCode 3407) is a deceptively simple problem that tests your ability to think about string manipulation and algorithmic efficiency. As an interview candidate, showcasing a clean, efficient solution here can impress hiring managers looking for *concise* and *correct* code. In this article we’ll walk through the *good*, *bad*, and *ugly* aspects of the problem, present three idiomatic solutions (Java, Python, C++), and give you a ready‑to‑paste reference to help you land that dream role.

> **SEO Keywords**: LeetCode 3407, Substring Matching Pattern, interview coding, Java solution, Python solution, C++ solution, job interview prep, algorithm, string matching, wildcard pattern.

---

### The Good

| Aspect | What We Love |
|--------|--------------|
| **One Wildcard** | Only a single `*` makes the problem linear‑time; we don’t need DP or backtracking. |
| **Readable Split** | Splitting `p` into `prefix` and `suffix` is intuitive and eliminates edge‑case confusion. |
| **Built‑in Search** | `String.indexOf`, `str.find`, and `std::string::find` do the heavy lifting in constant space. |
| **Clear Complexity** | `O(|s|)` time, `O(1)` auxiliary space—perfect for interview constraints. |
| **Cross‑Language Portability** | The same logic applies to Java, Python, and C++, making it easy to adapt. |

---

### The Bad

| Issue | Why it can trip you up |
|-------|------------------------|
| **Substring Overlap** | If the prefix and suffix overlap, naive `indexOf` may skip valid matches.  The algorithm above handles this because the suffix search starts **after** the prefix ends. |
| **Empty Prefix/Suffix** | When the pattern starts or ends with `*`, one of the parts is empty.  Our code still works because `indexOf("")` returns `0`, allowing matches anywhere. |
| **Large Strings** | Although constraints are tiny, an interviewer might test the worst case (`|s| = 50, |p| = 50`).  The solution still runs in microseconds. |

---

### The Ugly

The *ugly* part is what many beginners stumble over:

1. **Off‑by‑One Errors**  
   Starting the suffix search at `start + prefix.length()` is critical.  Starting at `start` can incorrectly accept overlaps that violate the ordering constraint.

2. **Edge Cases with `*` at Ends**  
   Forgetting that `""` is a valid substring can cause false negatives when `p = "*abc"` or `p = "abc*"`.  Our implementation’s split handles this automatically.

3. **Multiple Wildcards**  
   Some interviewers tweak the problem to allow more than one `*`.  The presented solution breaks then; you’d need a more sophisticated DP or regex approach.

---

### Why This Matters for Your Job Hunt

- **Demonstrates Algorithmic Clarity** – You solved a real interview problem with a clean O(n) solution.  
- **Showcases Cross‑Language Proficiency** – Knowing how to translate the same logic to Java, Python, and C++ signals versatility.  
- **Highlights Attention to Detail** – Handling empty prefixes/suffixes and off‑by‑one bugs shows you’re thorough.  
- **SEO‑Ready Portfolio** – Publishing this article (with the code snippets) on GitHub or a personal blog gives recruiters a quick reference and boosts your online visibility.

---

### Quick Reference

| Language | Code |
|----------|------|
| **Java** | ```java<br>class Solution {<br>    public boolean hasMatch(String s, String p) {<br>        int star = p.indexOf('*');<br>        String pre = p.substring(0, star);<br>        String post = p.substring(star + 1);<br>        int i = s.indexOf(pre);<br>        if (i == -1) return false;<br>        return s.indexOf(post, i + pre.length()) != -1;<br>    }<br>}``` |
| **Python** | ```python<br>class Solution:<br>    def hasMatch(self, s: str, p: str) -> bool:<br>        pre, post = p.split('*')<br>        i = s.find(pre)<br>        return i != -1 and s.find(post, i + len(pre)) != -1``` |
| **C++** | ```cpp<br>class Solution {<br>public:<br>    bool hasMatch(string s, string p) {<br>        size_t star = p.find('*');<br>        string pre = p.substr(0, star);<br>        string post = p.substr(star + 1);<br>        size_t i = s.find(pre);<br>        if (i == string::npos) return false;<br>        return s.find(post, i + pre.length()) != string::npos;<br>    }<br>};``` |

---

### Final Thoughts

Substring Matching Pattern is a *gateway problem* in many coding interviews.  
By mastering the **prefix‑suffix split strategy** and avoiding the common pitfalls, you can confidently tackle this problem and impress interviewers with clean, production‑ready code.

> **Ready to code?** Grab your IDE, copy the snippets above, and try some custom tests!  
> **Want to boost your résumé?** Write a short blog post or a GitHub Gist, tag it with `#LeetCode3407`, and let recruiters discover you.  

Happy coding, and good luck on the next interview! 🚀