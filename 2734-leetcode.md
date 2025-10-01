---
title: LeetCode 2734. Lexicographically Smallest String After Substring Operation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2734 – Lexicographically Smallest String After Substring Operation  
**Languages** – Java | Python | C++  
**Keywords** – *LeetCode*, *Lexicographically Smallest String*, *String Transformation*, *Greedy Algorithm*, *Job Interview*, *Python solution*, *Java solution*, *C++ solution*  

---

## 🚀 Quick Code Snippets

| Language | Code |
|---|---|
| **Java** | ```java
class Solution {
    public String smallestString(String s) {
        if (s.isEmpty()) return "";
        int n = s.length();
        StringBuilder sb = new StringBuilder(s);

        // 1️⃣  All 'a's → replace the last one with 'z'
        boolean allAs = true;
        for (int i = 0; i < n; i++) {
            if (s.charAt(i) != 'a') { allAs = false; break; }
        }
        if (allAs) { sb.setCharAt(n-1, 'z'); return sb.toString(); }

        // 2️⃣  Find the first non‑'a' substring and decrement each char
        int i = 0;
        while (i < n && s.charAt(i) == 'a') i++;      // skip leading 'a's
        while (i < n && s.charAt(i) != 'a') {
            sb.setCharAt(i, (char)(s.charAt(i)-1));  // 'c'→'b', 'b'→'a', … 'a'→'z'
            i++;
        }
        return sb.toString();
    }
}
``` |
| **Python** | ```python
class Solution:
    def smallestString(self, s: str) -> str:
        if not s:
            return ""
        n = len(s)
        # 1️⃣  All 'a's → replace last char with 'z'
        if all(ch == 'a' for ch in s):
            return s[:-1] + 'z'
        # 2️⃣  Find first non-'a' and decrement until an 'a' or end
        chars = list(s)
        i = 0
        while i < n and chars[i] == 'a':
            i += 1
        while i < n and chars[i] != 'a':
            chars[i] = chr(ord(chars[i]) - 1)
            i += 1
        return "".join(chars)
``` |
| **C++** | ```cpp
class Solution {
public:
    string smallestString(string s) {
        if (s.empty()) return "";
        int n = s.size();

        // 1️⃣  All 'a's → replace last char with 'z'
        bool allAs = true;
        for (char c : s) if (c != 'a') { allAs = false; break; }
        if (allAs) { s[n-1] = 'z'; return s; }

        // 2️⃣  Find first non-'a' substring and decrement
        int i = 0;
        while (i < n && s[i] == 'a') ++i;          // skip leading 'a's
        while (i < n && s[i] != 'a') {
            s[i] = char(s[i] - 1);                 // 'c'→'b', 'b'→'a', … 'a'→'z'
            ++i;
        }
        return s;
    }
};
``` |

---

## 📝 Problem Recap

You are given a lowercase string `s`.  
You may **once** choose any non‑empty contiguous substring and replace every letter in that substring by its previous alphabet letter (`'b' → 'a'`, …, `'a' → 'z'`).  
Return the lexicographically smallest string possible after this operation.

---

## 🌟 The Greedy Insight – “Good” Part

1. **Earlier letters dominate lexicographic order.**  
   To make the string as small as possible, we must reduce the *earliest* characters that can be lowered.

2. **Why the first non‑`a` block?**  
   All leading `'a'`s are already minimal; changing any `'a'` would make it `'z'`, which is larger.  
   Once we hit the first non‑`a`, lowering each of those letters by one yields the smallest prefix we can achieve.

3. **Special case – string of all `'a'`s.**  
   We are *forced* to perform the operation.  
   The minimal change is to turn the **last** `'a'` into `'z'`, keeping the prefix untouched.

These observations give a **linear‑time, constant‑space** algorithm – exactly what interviewers love.

---

## ⚡️ Complexity Analysis

| Metric | Java | Python | C++ |
|---|---|---|---|
| Time | **O(n)** – one pass to check all `'a'`s + one pass to decrement | **O(n)** – same logic | **O(n)** – same logic |
| Space | **O(n)** – `StringBuilder` | **O(n)** – list conversion | **O(1)** – in‑place string |

> **Why Java & Python use O(n) extra space?**  
> Strings are immutable; we need a mutable buffer.  
> C++ strings are mutable, so we can modify in place.

---

## 🔎 The “Bad” and “Ugly” Parts – What to Watch Out For

| Pitfall | Why It Happens | Fix |
|---|---|---|
| **Skipping the wrong substring** | Mis‑interpreting “first non‑`a` block” as “first non‑`a` character” | Ensure you decrement *all consecutive* non‑`a` characters until an `'a'` or the end. |
| **Off‑by‑one when replacing last `'a'`** | Mixing 0‑based and 1‑based indices | Use `s.length()-1` (Java, C++) or `s[:-1] + 'z'` (Python). |
| **Treating `'a' → 'z'` as a normal decrement** | Not handling the wrap‑around case | Explicitly check `if allAs` before any decrementing. |
| **Using a string concatenation inside a loop** | O(n²) time in Python | Convert to a list first, then join once. |

---

## 📈 SEO‑Optimized Blog Article

### Title
> **Lexicographically Smallest String After Substring Operation – LeetCode 2734: Java, Python, C++ Solutions & Interview Guide**

### Meta Description
> Learn how to solve LeetCode 2734 “Lexicographically Smallest String After Substring Operation” with a greedy algorithm. Read Java, Python, and C++ implementations, and discover interview insights that help you land your next job.

---

#### 1️⃣ Introduction

Interviews in software engineering teams often revolve around *string manipulation* puzzles.  
LeetCode’s **2734 – Lexicographically Smallest String After Substring Operation** is a perfect example: it’s short, it tests your ability to reason about lexicographic order, and it can be solved cleanly with a greedy algorithm.  

If you’re preparing for an interview or simply want to add a polished solution to your LeetCode library, you’re in the right place.

---

#### 2️⃣ Problem Statement (Re‑Worded)

> **You’re given a string `s` of lowercase letters.**  
> **Once** you may pick any contiguous non‑empty substring and shift every character there to its predecessor in the alphabet (`'b' → 'a'`, `'a' → 'z'`).  
> **Return the lexicographically smallest string you can produce.**

---

#### 3️⃣ Brute‑Force vs. Optimal

| Approach | Time | Space | Why It’s Not Interview‑Ready |
|---|---|---|---|
| **Brute‑Force** – Enumerate every substring, simulate the shift, keep the best | **O(n³)** – n² substrings × n shift | Too slow for n ≤ 10⁵. |
| **Optimized Greedy** – As shown above | **O(n)** | Fast, simple, proven optimal. |

---

#### 4️⃣ The Greedy Algorithm in Plain English

1. **All‑`a` Check**  
   If every character is `'a'`, we *must* change the last `'a'` to `'z'`.  
   The rest of the string remains unchanged.

2. **First Non‑`a` Block**  
   • Skip any leading `'a'`s.  
   • For the next run of consecutive non‑`a` characters, subtract 1 from each.  
   • Stop when you hit an `'a'` or the string ends.

That’s it – the algorithm guarantees the minimal lexicographic prefix.

---

#### 5️⃣ Code Walkthrough (Java)

```java
class Solution {
    public String smallestString(String s) {
        if (s.isEmpty()) return "";
        int n = s.length();
        StringBuilder sb = new StringBuilder(s);

        // 1️⃣  All 'a's → replace the last one with 'z'
        boolean allAs = true;
        for (int i = 0; i < n; i++) {
            if (s.charAt(i) != 'a') { allAs = false; break; }
        }
        if (allAs) { sb.setCharAt(n-1, 'z'); return sb.toString(); }

        // 2️⃣  Find the first non‑'a' substring and decrement each char
        int i = 0;
        while (i < n && s.charAt(i) == 'a') i++;      // skip leading 'a's
        while (i < n && s.charAt(i) != 'a') {
            sb.setCharAt(i, (char)(s.charAt(i)-1));  // 'c'→'b', 'b'→'a', … 'a'→'z'
            i++;
        }
        return sb.toString();
    }
}
```

*Comments*  
- Lines `1️⃣` and `2️⃣` mirror the two key ideas from the greedy proof.  
- `StringBuilder` preserves immutability while staying linear in space.

---

#### 6️⃣ Code Walkthrough (Python)

```python
class Solution:
    def smallestString(self, s: str) -> str:
        if not s:
            return ""
        n = len(s)

        # 1️⃣  All 'a's → replace last char with 'z'
        if all(ch == 'a' for ch in s):
            return s[:-1] + 'z'

        # 2️⃣  Find first non-'a' and decrement until an 'a' or end
        chars = list(s)
        i = 0
        while i < n and chars[i] == 'a':
            i += 1
        while i < n and chars[i] != 'a':
            chars[i] = chr(ord(chars[i]) - 1)
            i += 1
        return "".join(chars)
```

*Why `list(s)`?*  
Python strings are immutable; converting to a list gives a mutable sequence, allowing us to change characters in O(1) time per position.

---

#### 7️⃣ Code Walkthrough (C++)

```cpp
class Solution {
public:
    string smallestString(string s) {
        if (s.empty()) return "";
        int n = s.size();

        // 1️⃣  All 'a's → replace last char with 'z'
        bool allAs = true;
        for (char c : s) if (c != 'a') { allAs = false; break; }
        if (allAs) { s[n-1] = 'z'; return s; }

        // 2️⃣  Find first non-'a' substring and decrement
        int i = 0;
        while (i < n && s[i] == 'a') ++i;          // skip leading 'a's
        while (i < n && s[i] != 'a') {
            s[i] = char(s[i] - 1);                 // 'c'→'b', 'b'→'a', … 'a'→'z'
            ++i;
        }
        return s;
    }
};
```

*Why C++ is O(1) extra space?*  
`std::string` is mutable; we can change characters in place without allocating a new buffer.

---

#### 8️⃣ Interview‑Ready Tips

| Topic | What Interviewers Look For | How to Show It |
|---|---|---|
| **Problem Decomposition** | Ability to break a puzzle into sub‑problems. | Start by explaining the all‑`a` special case and the “first non‑`a` block” rule. |
| **Edge‑Case Awareness** | Real‑world inputs may be edge‑cases. | Test with `"a"`, `"z"`, `"abc"`, `"aaa"`, `"baaaa"`, etc. |
| **Algorithmic Complexity** | Candidates often write quadratic solutions. | Emphasize the linear time and minimal space properties. |
| **Clean Code** | Spaghetti code slows down the review process. | Use clear variable names (`i`, `n`, `allAs`) and separate concerns into helper functions if desired. |
| **Language‑Specific Gotchas** | String immutability in Java/Python vs. mutability in C++ | Explain why we need a mutable buffer in some languages. |

> **Pro‑tip:** In your interview, say *“The optimal solution is greedy because we only need to touch the earliest non‑`a` run; all preceding `'a'`s are already minimal, and if the string is all `'a'` we must change the last one to `'z'`. This gives an O(n) solution.”*  
> This concise explanation shows that you understand the underlying logic and can communicate it effectively.

---

#### 9️⃣ Final Thoughts

- **Simplicity beats cleverness.**  
  A clean greedy approach often beats a fancy DP or backtracking solution in interviews.

- **Test, test, test.**  
  Verify your code against the edge cases listed above before submitting on LeetCode.

- **Prepare to discuss.**  
  In an interview, walk through the algorithm on paper, discuss the “why” behind each decision, and be ready to answer follow‑up questions about complexity and variations.

---

#### 🎉 Closing

By mastering LeetCode 2734 you demonstrate:

- Understanding of lexicographic ordering  
- Ability to design a linear‑time greedy algorithm  
- Skillful handling of immutable data structures  

These are exactly the traits hiring managers look for in a software engineer. Good luck on your next interview!  

--- 

*If you found this post helpful, consider sharing it on your LinkedIn or Twitter feed. Happy coding!*