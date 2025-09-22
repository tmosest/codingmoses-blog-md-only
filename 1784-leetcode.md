---
title: LeetCode 1784. Check if Binary String Has at Most One Segment of Ones - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 1784 – “Check if Binary String Has at Most One Segment of Ones”

**Keywords:** LeetCode 1784, binary string, one‑segment of ones, interview question, algorithm, O(n) solution, Java, Python, C++, coding interview, software engineer, job interview, career advice

---

### TL;DR

- **Problem** – Determine whether a binary string (`s`) contains *at most one* contiguous block of `1`s.  
- **Optimal solution** – Scan once and flag the first transition from `1` to `0`. If a second `1` appears after that transition, return `false`.  
- **Time** – O(n)  
- **Space** – O(1)  

> **Pro tip:** A single one‑liner works (`!s.contains("01")` in Java or `return "01" not in s` in Python), but a manual scan is the “real” interview answer that shows you understand state transitions.

---

## 1. Problem Statement (From LeetCode)

> Given a binary string `s` **without leading zeros** (i.e., `s[0] == '1'`), return `true` if `s` contains **at most one contiguous segment of ones**; otherwise, return `false`.

### Examples

| Input | Output | Reason |
|-------|--------|--------|
| `"1001"` | `false` | Two separate blocks of `1`s. |
| `"110"` | `true` | One block of `1`s followed by zeros. |
| `"1"` | `true` | Single `1`. |
| `"10"` | `true` | One block of `1`s, then zeros. |
| `"111000111"` | `false` | Two blocks of `1`s separated by zeros. |

### Constraints

- `1 <= s.length <= 100`
- Each character is either `'0'` or `'1'`
- `s[0] == '1'` (no leading zeros)

---

## 2. The Good, The Bad, and The Ugly

| Aspect | What it is | Why it matters | How to improve |
|--------|------------|----------------|----------------|
| **Good – Simplicity** | One‑liner using `contains("01")` (Java) or `"01" not in s` (Python) | Fast to write, easy to read | Works for all valid inputs because `s` starts with `1` |
| **Bad – Hidden assumptions** | Assuming `s` always starts with `1` | Violates the constraint if test harness changes | Validate input or document the precondition |
| **Ugly – Over‑engineering** | Using regex (`re.search(r'01', s)`) or building a finite state machine | Adds unnecessary complexity, higher constant factors | Prefer simple linear scan or string methods |

---

## 3. Detailed Walk‑through of the Linear Scan

We’ll treat the string as a sequence of **states**:

1. **Before the first `0`** – still in the first block of `1`s.  
2. **After the first `0`** – we’ve left the first block.  
3. **If we see another `1` after state 2** – invalid (more than one block).

**Pseudo‑code**

```
seenZero = False
for ch in s:
    if ch == '0':
        seenZero = True
    else:          # ch == '1'
        if seenZero:    # a 1 after the first zero
            return False
return True
```

**Why it works**

- The first time we hit a `0`, we set `seenZero = True`.  
- Any subsequent `1` must come **after** this `0`; if it does, we have a second block, so we return `false`.  
- If the loop finishes, every `1` appeared before any `0` (or there were no `0`s), meaning there is only one block.

---

## 4. Code Implementations

Below are clean, idiomatic solutions in **Java**, **Python**, and **C++** that you can paste into LeetCode or any IDE.

### 4.1 Java

```java
class Solution {
    public boolean checkOnesSegment(String s) {
        boolean seenZero = false;
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (ch == '0') {
                seenZero = true;
            } else { // ch == '1'
                if (seenZero) return false;
            }
        }
        return true;
    }
}
```

> **One‑liner alternative** (keeps to the LeetCode style guide)

```java
class Solution {
    public boolean checkOnesSegment(String s) {
        return !s.contains("01");
    }
}
```

### 4.2 Python

```python
class Solution:
    def checkOnesSegment(self, s: str) -> bool:
        seen_zero = False
        for ch in s:
            if ch == '0':
                seen_zero = True
            else:  # ch == '1'
                if seen_zero:
                    return False
        return True
```

> **Pythonic one‑liner**

```python
class Solution:
    def checkOnesSegment(self, s: str) -> bool:
        return "01" not in s
```

### 4.3 C++

```cpp
class Solution {
public:
    bool checkOnesSegment(string s) {
        bool seenZero = false;
        for (char ch : s) {
            if (ch == '0') seenZero = true;
            else if (seenZero) return false; // ch == '1' after a zero
        }
        return true;
    }
};
```

> **C++11 one‑liner**

```cpp
class Solution {
public:
    bool checkOnesSegment(string s) {
        return s.find("01") == string::npos;
    }
};
```

---

## 5. Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Linear Scan | **O(n)** | **O(1)** |
| One‑liner `contains`/`in` | **O(n)** (searches the whole string) | **O(1)** |
| Regex | **O(n)** | **O(1)** (but with higher constant factors) |

`n` is the length of `s` (≤ 100). All approaches are fast enough, but the linear scan is the “clean” interview answer.

---

## 6. Common Pitfalls & How to Avoid Them

1. **Assuming `s` can start with `0`.**  
   *Fix*: Either validate input or document that `s` is guaranteed to start with `1`.  
   ```java
   if (s.charAt(0) == '0') throw new IllegalArgumentException();
   ```

2. **Mis‑interpreting “segment” as “subsequence”.**  
   *Fix*: Clarify that the ones must be contiguous; any `1` after a `0` breaks the segment.

3. **Off‑by‑one errors in loops.**  
   *Fix*: Use `for (char ch : s)` in Java/C++ or iterate over the string directly in Python.

4. **Using expensive operations in a tight loop** (e.g., `s.contains` inside a loop).  
   *Fix*: Do the check only once, outside the loop.

---

## 7. Interview‑Ready Discussion Points

- **Explain the state machine intuition**: “First block → zero → second block.”  
- **Mention alternative solutions**: one‑liner, regex, or two‑pointer approach.  
- **Discuss edge cases**: single character, all ones, all zeros after the first block.  
- **Talk about scalability**: Even though `n ≤ 100`, the algorithm is O(n) and works for huge inputs.  
- **Show you can write clean, readable code**: Prefer the explicit loop over a cryptic one‑liner.

> *“I prefer the explicit loop because it clearly communicates the state transitions, which is essential in a team setting where code readability matters more than a clever trick.”*

---

## 8. SEO‑Optimized Blog Article Draft

> **Title:**  
> _Master LeetCode 1784: “Check if Binary String Has at Most One Segment of Ones” – The Interview‑Ready Solution in Java, Python, and C++_

> **Meta Description:**  
> Learn how to solve LeetCode 1784 in O(n) time. Find Java, Python, and C++ solutions, understand the good/bad/ugly, and get interview tips to impress recruiters.

> **Slug:**  
> `leetcode-1784-binary-string-one-segment-solutions`

### Outline

1. **Introduction** – Why this question is a staple for data‑structures & strings.  
2. **Problem statement** – Clear description with examples.  
3. **The Good, Bad, Ugly** – Quick sanity check.  
4. **Algorithm Walk‑through** – State machine, pseudo‑code.  
5. **Full Code** – Java, Python, C++ with comments.  
6. **Time & Space Complexity** – Bottom line numbers.  
7. **Edge Cases & Common Mistakes** – What interviewers expect.  
8. **Alternative Approaches** – One‑liners, regex, etc.  
9. **Interview Talk‑track** – How to explain your solution.  
10. **Conclusion** – Recap and next steps (try variations, optimize further).  

> **Target Keywords**: LeetCode 1784, binary string, one segment of ones, algorithm interview, coding interview questions, Java solution, Python solution, C++ solution, O(n) string problem, interview preparation, software engineering interview.

---

## 9. How This Blog Helps Your Job Hunt

- **Visibility** – Search‑engine‑friendly content on a popular LeetCode problem attracts recruiters looking for interview practice.
- **Depth** – Demonstrates your ability to explain algorithms, handle edge cases, and write clean code.
- **Portfolio** – Add the code snippets to your GitHub README or personal website; showcase your understanding of multiple languages.
- **Networking** – Share the article on LinkedIn, Medium, or dev.to; engage with comments to show active participation in the developer community.

---

## 10. Final Words

LeetCode 1784 is a quick win: it tests string manipulation, state management, and the ability to write clean, efficient code. By mastering the one‑liner trick and the linear‑scan “real” solution, you’ll be ready for the question in any coding interview. Pair the code with a polished blog post, and you’ll have a strong, interview‑ready portfolio piece that recruiters can find and admire.

Happy coding, and good luck landing that software engineer role! 🚀