---
title: LeetCode 2734. Lexicographically Smallest String After Substring Operation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 How to Master LeetCode 2734 – *Lexicographically Smallest String After Substring Operation*

> **Want to land a Software Engineer role?**  
>  Practice this one‑liner greedy trick, write clean Java/Python/C++ code, and explain it in an interview.

---

### 🔍 Problem Recap

You are given a string `s` of lowercase English letters.  
You may perform **exactly one** operation:

1. Pick any non‑empty contiguous substring.  
2. Replace every character in that substring with its *preceding* alphabet letter (`'b' → 'a'`, `'a' → 'z'`).

Return the **lexicographically smallest** string you can obtain after one operation.

```
Input  : "cbabc"
Output : "baabc"   // subtract from index 0..1
```

Constraints  
- `1 ≤ s.length ≤ 3 * 10^5`  
- `s` contains only lowercase English letters

---

### 🎯 The Key Insight

*The earlier a character becomes smaller, the more it dominates the lexicographical order.*  

Thus, to get the globally smallest string we should:

1. **Target the first run of non‑‘a’ characters** (i.e. the leftmost contiguous block that is not already minimal).  
2. Decrease every character in that block by one.  
3. If the entire string is all `'a'`, decreasing any character would *increase* the string.  
   In that special case, change the **last** `'a'` to `'z'` – this is the least harmful change.

This greedy strategy is optimal and runs in linear time.

---

## 💡 Solution Outline (Greedy)

| Step | What we do | Why it works |
|------|------------|--------------|
| 1 | Scan from the left until we hit a non‑`'a'`. | The first such block determines the earliest possible improvement. |
| 2 | Keep decreasing characters until we hit an `'a'` or the string ends. | Each decrement makes the prefix strictly smaller; stopping at an `'a'` keeps the rest unchanged. |
| 3 | If we never find a non‑`'a'`, change the last character to `'z'`. | All characters are already minimal (`'a'`). Only way to make an operation that satisfies “exactly one operation” is to bump the last `'a'` to `'z'`, which minimally worsens the string. |

---

## 📊 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Scan for first non‑`'a'` | **O(n)** | – |
| Decrement block | **O(k)** where `k ≤ n` | – |
| Overall | **O(n)** | **O(1)** extra (in‑place modifications) |

`n` is the length of `s`.  
The algorithm handles up to `3·10^5` characters comfortably.

---

## 🧑‍💻 Code Implementations

Below are clean, production‑ready solutions in **Java, Python, and C++**.  
Each implementation follows the same greedy logic and uses *in‑place* modifications for maximum efficiency.

### 1️⃣ Java

```java
public class Solution {
    public String smallestString(String s) {
        int n = s.length();
        // Convert to mutable char array
        char[] arr = s.toCharArray();

        // Check if the entire string is 'a'
        boolean allA = true;
        for (char c : arr) {
            if (c != 'a') { allA = false; break; }
        }

        if (allA) {
            // Change the last character to 'z'
            arr[n - 1] = 'z';
            return new String(arr);
        }

        // Find first non-'a' substring and decrement
        int i = 0;
        while (i < n && arr[i] == 'a') i++;   // skip leading 'a'
        while (i < n && arr[i] != 'a') {
            arr[i] = (char) (arr[i] - 1);      // shift back by one
            i++;
        }
        return new String(arr);
    }
}
```

---

### 2️⃣ Python

```python
class Solution:
    def smallestString(self, s: str) -> str:
        n = len(s)
        arr = list(s)

        # All 'a' ?
        if all(c == 'a' for c in arr):
            arr[-1] = 'z'
            return ''.join(arr)

        i = 0
        while i < n and arr[i] == 'a':
            i += 1                     # skip leading 'a'
        while i < n and arr[i] != 'a':
            arr[i] = chr(ord(arr[i]) - 1)
            i += 1

        return ''.join(arr)
```

---

### 3️⃣ C++

```cpp
class Solution {
public:
    string smallestString(string s) {
        int n = s.size();
        bool allA = true;
        for (char c : s) {
            if (c != 'a') { allA = false; break; }
        }

        if (allA) {          // all 'a' → change last to 'z'
            s[n-1] = 'z';
            return s;
        }

        int i = 0;
        while (i < n && s[i] == 'a') ++i;     // skip leading 'a'
        while (i < n && s[i] != 'a') {
            s[i] = s[i] - 1;                  // shift back by one
            ++i;
        }
        return s;
    }
};
```

---

## 🎉 Quick Test

| Input | Expected Output |
|-------|-----------------|
| `"cbabc"` | `"baabc"` |
| `"aa"` | `"az"` |
| `"acbbc"` | `"abaab"` |
| `"leetcode"` | `"kddsbncd"` |
| `"aaa"` | `"aaz"` |

Run the snippets above with these examples to see the greedy solution in action.

---

## 📌 Common Pitfalls & How to Avoid Them

| Pitfall | Why it Happens | Fix |
|---------|----------------|-----|
| **Skipping the entire string** (thinking we must change all characters) | Misinterpreting “exactly one operation” as “change all” | Only transform the first non‑`'a'` block; stop when you hit an `'a'`. |
| **Changing an `'a'` to `'z'` too early** | Forgetting the all‑`'a'` special case | Perform the `'z'` change *only* when no better block exists. |
| **Off‑by‑one errors in character math** | Using `c += 1` instead of `c - 1` | Remember the operation subtracts the alphabet index; use `c = chr(ord(c)-1)` (or `c-1` in C++). |
| **O(n²) in Python** | Building new strings inside loops | Modify a list and join once at the end. |

---

## 📚 Why This Blog Helps Your Interview Prep

1. **Clear Problem Explanation** – In interviews you’ll need to restate the problem in your own words.  
2. **Greedy Argument** – Demonstrate your ability to reason about optimality.  
3. **Three Language Implementations** – Show versatility across Java, Python, and C++.  
4. **Edge‑Case Awareness** – Highlight the special “all a’s” case – a common interview surprise.  
5. **Performance Discussion** – Linear time for `3·10^5` chars is a real‑world constraint.

By writing this solution **and explaining the reasoning** in a typical interview, you’ll demonstrate:

- **Algorithmic thinking** (greedy, linear‑time).  
- **Coding discipline** (in‑place changes, minimal allocations).  
- **Problem‑solving clarity** (handling edge cases, proving optimality).

---

## 📣 SEO‑Friendly Title & Meta‑Description

> **Title** – “How to Solve LeetCode 2734: Lexicographically Smallest String After Substring Operation (Java/Python/C++)”  
> **Meta‑Description** – “Master the greedy trick for LeetCode 2734 and impress hiring managers. Learn clean Java, Python, and C++ solutions, edge‑case handling, and interview talking points.”  

Use the following keywords throughout the article:  
`Lexicographically Smallest String`, `LeetCode 2734`, `string manipulation`, `greedy algorithm`, `coding interview`, `software engineer`, `Java solution`, `Python solution`, `C++ solution`.

---

## 🤝 Final Take‑Away

- **Greedy** is the star: target the first non‑`'a'` block.  
- **Edge cases matter** – all‑`'a'` strings need a special tweak.  
- **Code clarity** beats micro‑optimisation in most interviews.  
- Practice explaining the *why* – that’s what interviewers are after.

If you can write the solution in 30‑60 seconds and explain the reasoning clearly, you’ll shine in any software‑engineering interview. Happy coding! 🎉👩‍💻👨‍💻

---