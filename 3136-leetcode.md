---
title: LeetCode 3136. Valid Word - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 3136. Valid Word – Java / Python / C++ Solutions + Blog Post  
*(Everything you need to ace this LeetCode problem and land your dream job.)*  

---

## 1️⃣  Problem Recap (LeetCode 3136)

| Requirement | What it means |
|-------------|---------------|
| **Length** | ≥ 3 characters |
| **Characters** | Only digits `0‑9` or English letters (upper‑ or lower‑case) |
| **Content** | At least one vowel (`a e i o u`, case‑insensitive) **and** at least one consonant (any other letter) |

Return `true` if the string satisfies *all* rules, otherwise `false`.

---

## 2️⃣  Solution Idea

1. **Early exit** – if length < 3 → `false`.  
2. Scan the string once:  
   * If a character is a letter → check if it’s a vowel or consonant.  
   * If it’s a digit → okay.  
   * Any other symbol → `false` immediately.  
3. After the loop, ensure that we saw **at least one vowel** and **at least one consonant**.  
4. Complexity: **O(n)** time, **O(1)** space.

This is a single‑pass algorithm with no extra data structures – perfect for interview settings.

---

## 3️⃣  Code

### 3.1 Java

```java
// 3136. Valid Word
public class Solution {
    public boolean isValid(String word) {
        if (word.length() < 3) return false;

        boolean hasVowel = false;
        boolean hasConsonant = false;
        for (char c : word.toCharArray()) {
            if (Character.isLetter(c)) {
                if ("aeiouAEIOU".indexOf(c) >= 0) {
                    hasVowel = true;
                } else {
                    hasConsonant = true;
                }
            } else if (!Character.isDigit(c)) {
                return false;                 // illegal symbol
            }
        }
        return hasVowel && hasConsonant;
    }
}
```

### 3.2 Python

```python
# 3136. Valid Word
class Solution:
    def isValid(self, word: str) -> bool:
        if len(word) < 3:
            return False

        vowels = set('aeiouAEIOU')
        has_vowel = has_consonant = False

        for ch in word:
            if ch.isalpha():
                if ch in vowels:
                    has_vowel = True
                else:
                    has_consonant = True
            elif not ch.isdigit():
                return False

        return has_vowel and has_consonant
```

### 3.3 C++

```cpp
// 3136. Valid Word
class Solution {
public:
    bool isValid(string word) {
        if (word.size() < 3) return false;

        bool hasVowel = false, hasConsonant = false;
        const string vowels = "aeiouAEIOU";

        for (char c : word) {
            if (isalpha(c)) {
                if (vowels.find(c) != string::npos) hasVowel = true;
                else hasConsonant = true;
            } else if (!isdigit(c)) {
                return false;           // illegal symbol
            }
        }
        return hasVowel && hasConsonant;
    }
};
```

> All three implementations run in **O(n)** time, **O(1)** space.

---

## 4️⃣  Blog Post – “The Good, the Bad, and the Ugly of LeetCode 3136: Valid Word”

### 4.1 Title (SEO‑optimized)

> **“Valid Word (LeetCode 3136) – Java, Python, C++ Solutions + Interview Tips for Your Next Job”**

### 4.2 Introduction

When you hit LeetCode’s *“Valid Word”* problem (3136), you’re stepping into a classic *string validation* interview question. It’s easy to solve, but it teaches you the fundamentals of parsing, boundary checks, and early exits—skills that every software engineer needs.

In this post, we’ll walk through:

1. **The good** – why this problem is a *must‑solve* for coding interviews.  
2. **The bad** – common pitfalls and how to avoid them.  
3. **The ugly** – over‑engineering it (e.g., regex) and why it’s a bad idea for interviews.  

By the end, you’ll have a clean, production‑ready solution in Java, Python, and C++, plus interview‑style talking points to impress hiring managers.

### 4.3 Problem Statement (quick recap)

- Minimum length 3  
- Only digits or English letters  
- Must contain at least one vowel *and* one consonant  

Return `true` if all conditions are met.

### 4.4 The Good – Why This Problem Matters

| Skill | Why it’s valuable |
|-------|-------------------|
| **Single‑pass scanning** | Demonstrates algorithmic efficiency –  O(n) time. |
| **Character classification** | Showcases use of language built‑ins (`isalpha`, `isdigit`). |
| **Early exit** | Reduces complexity and shows practical thinking. |
| **Edge‑case awareness** | Handles short strings, non‑alphanumerics, mixed case. |

### 4.5 The Bad – Common Mistakes

| Mistake | Why it hurts | Fix |
|---------|--------------|-----|
| Forgetting the length check | Wrongly validates “a2” | `if (len < 3) return false;` |
| Ignoring case in vowel check | Misses “A” or “E” | Use a case‑insensitive set or `toLowerCase()` |
| Allowing any symbol | Fails “a$3” | Explicitly reject anything that’s not a digit or a letter |
| Counting digits as consonants | Mis‑classifies “123abc” | Separate digit handling from letter handling |

### 4.6 The Ugly – Over‑Engineering with Regex

A regex‑only solution can look compact:

```python
re.fullmatch(r'(?=.*[aeiouAEIOU])(?=.*[bcdfghjklmnpqrstvwxyzBCDFGHJKLMNPQRSTVWXYZ])[0-9A-Za-z]{3,}', word)
```

But in an interview:

- **Readability suffers** – recruiters might not understand it quickly.  
- **Performance** – regex engines can be slower for long strings.  
- **Debugging is hard** – if the pattern fails, you’ll have to rewrite it.

Stick to the straightforward, imperative approach above unless the interviewer explicitly asks for a regex.

### 4.7 Time & Space Complexity

- **Time:** O(n) – one pass over the string.  
- **Space:** O(1) – only a few boolean flags.

### 4.8 Test Cases (Mini‑Test Suite)

| Input | Expected | Reason |
|-------|----------|--------|
| `"234Adas"` | `true` | Valid – digits + vowels + consonants |
| `"b3"` | `false` | Too short, no vowel |
| `"a3$e"` | `false` | Contains `$` (illegal symbol) |
| `"AEIOU123"` | `false` | No consonants |
| `"bcd123"` | `false` | No vowels |
| `"aB1c"` | `true` | Mix of cases, all rules satisfied |

### 4.9 Interview Tips

1. **Explain your approach** – Mention early exit, single pass, and why you count vowels/consonants separately.  
2. **Discuss edge cases** – Highlight length, symbol handling, and mixed case.  
3. **Mention complexity** – Show you care about efficiency.  
4. **Ask clarifying questions** – e.g., “Do we consider ‘y’ a vowel?”  
5. **Show your code** – Share the Java/Python/C++ snippet; be ready to refactor on the spot.

### 4.10 Conclusion

The *Valid Word* problem is a small but powerful interview staple. It forces you to think about:

- **Input validation**  
- **Character classification**  
- **Boundary conditions**

With the solutions above and the interview strategy outlined, you’re ready to tackle 3136 confidently—and to impress recruiters with clear, efficient code. Good luck landing that dream job!  

---  

**Got any more LeetCode challenges you want to master?**  
Drop a comment or email me – I’ll help you nail the next problem!