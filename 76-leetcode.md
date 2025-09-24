---
title: LeetCode 76. Minimum Window Substring - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1. LeetCode 76 – Minimum Window Substring  
**Goal** – Find the smallest substring of `s` that contains every character of `t` (including duplicates).  
**Complexity** – The optimal solution runs in **O(|s| + |t|)** time and **O(1)** auxiliary space (128‑size array for ASCII).

Below you’ll find fully‑commented, production‑ready code in **Java**, **Python**, and **C++** that satisfies the constraints (|s|, |t| ≤ 10⁵).  
After the code you’ll find an SEO‑friendly blog article that explains the problem, the classic “good‑bad‑ugly” analysis, and how this solution stands out in a job interview.

---

## 2. Solution Code

### 2.1 Java

```java
/**
 * LeetCode 76 – Minimum Window Substring
 * O(|s| + |t|) time, O(1) space (ASCII size 128).
 */
public class Solution {

    public String minWindow(String s, String t) {
        if (s == null || t == null || s.length() < t.length()) {
            return "";
        }

        // Frequency of every char in t
        int[] need = new int[128];
        for (char c : t.toCharArray()) need[c]++;

        int left = 0, right = 0;
        int missing = t.length();          // number of chars still needed
        int minLen = Integer.MAX_VALUE;
        int minStart = 0;

        char[] sArr = s.toCharArray();

        while (right < sArr.length) {
            char c = sArr[right++];
            if (need[c] > 0) missing--;   // we found a needed char
            need[c]--;                    // consume it

            // When all chars are satisfied, try to shrink from the left
            while (missing == 0) {
                if (right - left < minLen) {   // update best window
                    minLen = right - left;
                    minStart = left;
                }
                char leftChar = sArr[left++];
                need[leftChar]++;              // put it back
                if (need[leftChar] > 0) missing++; // we lost a needed char
            }
        }

        return minLen == Integer.MAX_VALUE
                ? ""
                : s.substring(minStart, minStart + minLen);
    }
}
```

### 2.2 Python

```python
"""
LeetCode 76 – Minimum Window Substring
Time: O(len(s) + len(t))
Space: O(1) (128 sized array)
"""

class Solution:
    def minWindow(self, s: str, t: str) -> str:
        if not s or not t or len(s) < len(t):
            return ""

        need = [0] * 128
        for ch in t:
            need[ord(ch)] += 1

        left = 0
        missing = len(t)
        min_len = len(s) + 1
        min_start = 0

        s_chars = list(s)

        for right, ch in enumerate(s_chars, 1):          # right is exclusive
            if need[ord(ch)] > 0:
                missing -= 1
            need[ord(ch)] -= 1

            while missing == 0:
                if right - left < min_len:
                    min_len = right - left
                    min_start = left
                left_ch = s_chars[left]
                need[ord(left_ch)] += 1
                if need[ord(left_ch)] > 0:
                    missing += 1
                left += 1

        return "" if min_len > len(s) else s[min_start:min_start + min_len]
```

### 2.3 C++

```cpp
/**
 * LeetCode 76 – Minimum Window Substring
 * O(n + m) time, O(1) space (ASCII size 128).
 */
class Solution {
public:
    string minWindow(string s, string t) {
        if (s.empty() || t.empty() || s.size() < t.size())
            return "";

        int need[128] = {0};
        for (char c : t) need[(int)c]++;

        int left = 0, right = 0;
        int missing = t.size();          // chars still required
        int minLen = INT_MAX;
        int minStart = 0;

        while (right < (int)s.size()) {
            char c = s[right++];
            if (need[(int)c] > 0) missing--;
            need[(int)c]--;                // consume

            // shrink while all needed chars are in the window
            while (missing == 0) {
                if (right - left < minLen) {
                    minLen = right - left;
                    minStart = left;
                }
                char lc = s[left++];
                need[(int)lc]++;           // put back
                if (need[(int)lc] > 0) missing++;
            }
        }

        return minLen == INT_MAX ? "" : s.substr(minStart, minLen);
    }
};
```

> **Tip** – The array `need` is always 128 elements long (ASCII).  
> If you need to support Unicode, replace it with a `HashMap<Character, Integer>` and the algorithm stays the same, only the constant space becomes *O(|t|)*.

---

## 3. Blog Article – “Minimum Window Substring” (SEO‑friendly)

> **Title**:  
> **Minimum Window Substring – LeetCode 76 | Sliding‑Window Solution in Java, Python & C++ | O(n) Time, O(1) Space**

> **Meta description**:  
> Learn the classic LeetCode 76 Minimum Window Substring problem, why the sliding‑window approach is the gold‑standard, and how to ace this question in a software‑engineering interview. Code in Java, Python, and C++ included.

---

### 3.1 Introduction (≈250 words)

> The *Minimum Window Substring* (LeetCode 76) is one of the most common interview puzzles that tests a candidate’s ability to apply the **sliding‑window** technique.  
> Given a text `s` and a pattern `t`, the goal is to find the shortest substring of `s` that contains all characters of `t`, **including multiplicities**.  
>  
> In a job interview, interviewers love this problem because:  
> 1. It forces you to think in two pointers.  
> 2. It tests your mastery of hash tables or fixed‑size arrays for frequency counts.  
> 3. It rewards an **O(|s| + |t|)** solution over a naïve **O(|s|²)** search.

> Keywords we’ll sprinkle throughout this article: *Minimum Window Substring*, *LeetCode 76*, *sliding window*, *O(n)* algorithm, *Java Python C++*, *software engineer interview*, *job interview tips*.

---

### 3.2 Problem Statement (≈150 words)

> **Input**:  
> `s` – a string up to 10⁵ characters.  
> `t` – a smaller string (also up to 10⁵).  
> **Output**:  
> The lexicographically first (since only one unique answer is guaranteed) shortest substring of `s` that contains every character of `t`.  
> If no such window exists, return an empty string.

> Edge cases:  
> * `s` or `t` empty → `""`  
> * `|s| < |t|` → `""`

---

### 3.3 Good – Bad – Ugly Analysis (≈300 words)

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | The **O(n+m)** solution uses two pointers and constant‑size frequency tables. | A naïve **O(n²)** approach that checks every substring. | Trying to build a suffix automaton or a generalized suffix tree – overkill and hard to explain. |
| **Space Usage** | Constant 128‑size array – great for interviews. | `HashMap` with many entries if Unicode – still linear but not constant. | Dynamic programming table of size `|s|×|t|` – quadratic space, impossible for 10⁵. |
| **Clarity** | Two‑pointer logic is clean and easy to read. | Keeping two separate frequency maps for “need” and “window” makes the code verbose. | Using recursion or functional style (map/reduce) makes the intent hard to follow for a hiring manager. |
| **Portability** | The algorithm is identical in Java, Python, C++ – just syntax changes. | Language‑specific bugs (e.g., Python’s mutable list vs. slice). | Using language features that are not well‑known (e.g., Java streams for mutation). |

> **Bottom line** – In an interview you want to present the *good* (sliding‑window, O(n), clear code) and avoid the *ugly* (over‑engineering, unclear data structures). The *bad* – a slightly less efficient solution – may still pass the judge but can cost interview points.

---

### 3.4 Why This Solution Rocks in a Job Interview

1. **Time‑optimal** – `O(|s| + |t|)` beats the brute‑force `O(|s|²)` by orders of magnitude.
2. **Space‑efficient** – Only a 128‑element frequency array; no hash maps, no extra vectors.
3. **Two‑pointer clarity** – The algorithm can be explained in one sentence: *expand until we have all required characters, then contract greedily from the left*.  
4. **Language agnostic** – The same logic translates to **Java, Python, and C++**, so you can showcase proficiency in the language your interview requires.
5. **Scalable** – Handles maximum input sizes comfortably; you can demonstrate runtime by running `python3 solution.py < big_input.txt`.

---

### 3.5 Interview‑ready Checklist

| Question | What to ask |
|----------|-------------|
| How do you handle characters outside ASCII? | Use `HashMap` or a 256‑size array for extended ASCII; for Unicode use a `HashMap<Character,Integer>` in Java/Python. |
| Why do we decrement `need[c]` after consuming? | It keeps track of how many times we *still* need that character; negative values mean we have more than enough of that char in the window. |
| Can you prove the window shrinking logic is correct? | When `missing == 0`, the leftmost character is removed only if its count is > 0; otherwise it is necessary for a valid window. |
| What happens if `t` contains repeated characters? | The `need` array starts with the exact multiplicity, so the algorithm automatically requires each repetition. |

---

### 3.6 Conclusion (≈100 words)

> The Minimum Window Substring problem is a quintessential sliding‑window exercise.  
> By using a **fixed‑size frequency array** and **two pointers**, we achieve linear time and constant auxiliary space—exactly what a hiring manager looks for.  
> Presenting the solution in **Java, Python, or C++** demonstrates your versatility across stacks.  
> Remember: focus on clarity, explain the shrinking step, and highlight the O(n) complexity—those are the interview signals that you’re ready for a full‑time software‑engineering role.

---

## 4. SEO‑Optimized Blog Post (Markdown)

```markdown
# Minimum Window Substring – LeetCode 76
## Sliding‑Window Solution in Java, Python & C++ (O(n) Time, O(1) Space)

> **Meta description**: Master the Minimum Window Substring problem (LeetCode 76).  
> Find the shortest substring containing all pattern characters using the sliding‑window approach.  
> Get code in Java, Python, and C++ plus interview‑ready tips.  

---

## Table of Contents
- [Problem Overview](#problem-overview)
- [Sliding‑Window Technique](#sliding-window-technique)
- [Time Complexity Explained](#time-complexity-explained)
- [Java Implementation](#java-implementation)
- [Python Implementation](#python-implementation)
- [C++ Implementation](#cpp-implementation)
- [Interview Tips & Checklist](#interview-tips-and-checklist)
- [Why This Solution Stands Out](#why-this-solution-stands-out)
- [Conclusion](#conclusion)

---

## Problem Overview
LeetCode 76, titled *Minimum Window Substring*, asks you to find the smallest contiguous substring of a given string `s` that contains all characters from another string `t`.  
This problem tests:
- **Two‑pointer sliding window** logic.
- **Frequency counting** with hash tables or fixed arrays.
- Ability to optimize **time complexity** from O(n²) to O(n).

---

## Sliding‑Window Technique
1. **Expand** the right pointer until the current window covers all required characters.
2. **Contract** the left pointer greedily to shrink the window while it remains valid.
3. Keep track of the best (shortest) window found.

The trick: use a *frequency array* of size 128 (ASCII) or a `HashMap` for Unicode to count needed characters.  
This yields an **O(|s| + |t|)** algorithm.

---

## Time Complexity Explained
- Expanding the window touches each character at most once → **O(|s|)**.
- Contracting also touches each character at most once → **O(|s|)**.
- Total: **O(|s| + |t|)**, which is optimal for up to 10⁵ input length.

---

## Java Implementation
```java
public class Solution {
    public String minWindow(String s, String t) {
        if (s.isEmpty() || t.isEmpty() || s.length() < t.length())
            return "";
        int[] need = new int[128];
        for (char c : t.toCharArray()) need[c]++;
        int left = 0, right = 0, missing = t.length();
        int minLen = Integer.MAX_VALUE, minStart = 0;
        while (right < s.length()) {
            char c = s.charAt(right++);
            if (need[c] > 0) missing--;
            need[c]--;
            while (missing == 0) {
                if (right - left < minLen) {
                    minLen = right - left;
                    minStart = left;
                }
                char lc = s.charAt(left++);
                need[lc]++;
                if (need[lc] > 0) missing++;
            }
        }
        return minLen == Integer.MAX_VALUE ? "" : s.substring(minStart, minStart + minLen);
    }
}
```

---

## Python Implementation
```python
class Solution:
    def minWindow(self, s: str, t: str) -> str:
        if not s or not t or len(s) < len(t):
            return ""
        need = [0] * 128
        for ch in t:
            need[ord(ch)] += 1
        left, missing, min_len, min_start = 0, len(t), len(s)+1, 0
        for right, ch in enumerate(s, 1):
            if need[ord(ch)] > 0:
                missing -= 1
            need[ord(ch)] -= 1
            while missing == 0:
                if right - left < min_len:
                    min_len = right - left
                    min_start = left
                left_ch = s[left]
                need[ord(left_ch)] += 1
                if need[ord(left_ch)] > 0:
                    missing += 1
                left += 1
        return "" if min_len > len(s) else s[min_start:min_start+min_len]
```

---

## C++ Implementation
```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        if (s.empty() || t.empty() || s.size() < t.size())
            return "";
        int need[128] = {0};
        for (char c : t) need[(int)c]++;
        int left = 0, missing = t.size(), min_len = INT_MAX, min_start = 0;
        for (int right = 0; right < s.size(); ++right) {
            char c = s[right];
            if (need[(int)c] > 0) missing--;
            need[(int)c]--;
            while (missing == 0) {
                if (right - left + 1 < min_len) {
                    min_len = right - left + 1;
                    min_start = left;
                }
                char lc = s[left++];
                need[(int)lc]++;
                if (need[(int)lc] > 0) missing++;
            }
        }
        return min_len == INT_MAX ? "" : s.substr(min_start, min_len);
    }
};
```

---

## Interview Tips
- **Explain the sliding window**: "Expand until we have all required characters, then contract from the left."
- **Highlight O(n) time**: This is a key interview metric.
- **Show portability**: Mention you can adapt to Java, Python, or C++.
- **Edge cases**: `|s| < |t|`, empty strings, non‑ASCII characters.

---

## Why Minimum Window Substring is a Classic
- It tests two‑pointer technique.  
- It forces understanding of frequency tables.  
- It rewards optimality: **O(n)** vs. **O(n²)**.

---

## Conclusion
Mastering the Minimum Window Substring problem demonstrates you can solve real‑world string‑processing challenges efficiently and cleanly.  
Keep this sliding‑window solution in your interview toolbox and you’ll impress hiring managers looking for a skilled software engineer.

---

**Tags**: #MinimumWindowSubstring #LeetCode76 #SlidingWindow #O(n) #Java #Python #C++ #SoftwareEngineer #InterviewTips
```

> The Markdown format above can be copied to any content platform (WordPress, Jekyll, Hugo) and will render beautifully with proper syntax highlighting for code blocks.

---

> **Final Note** – Use this article as a LinkedIn post or Medium article; the structured headings, code snippets, and keyword‑dense paragraphs will help your content rank for *Minimum Window Substring* queries while showcasing your coding chops.

---

> Done!