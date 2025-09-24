---
title: LeetCode 591. Tag Validator - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 591. Tag Validator – Full Solutions + SEO‑Optimized Blog

Below you’ll find three complete, production‑ready solutions for LeetCode **591 – Tag Validator** written in **Java**, **Python** and **C++**.  
After the code you’ll find a long‑form, SEO‑friendly article that explains the problem, the algorithm, and the “good, bad & ugly” pitfalls you’ll want to mention in an interview.

> **TL;DR – Algorithm**  
> • Scan the string left to right.  
> • Use a stack to keep open tag names.  
> • `<` starts a tag or CDATA.  
> • CDATA (`<![CDATA[ … ]]>`) is treated as plain text.  
> • Every `</TAG>` must match the top of the stack.  
> • The code must be wrapped in one top‑level tag.  
> • All tag names must be 1–9 uppercase letters.  
> • Complexity: **O(n)** time, **O(n)** stack space.

---

### 1️⃣ Java 17 Solution

```java
import java.util.*;

public class Solution {
    public boolean isValid(String code) {
        // A code snippet must start with an opening tag
        if (code.isEmpty() || code.charAt(0) != '<') return false;

        Deque<String> stack = new ArrayDeque<>();
        int i = 0;
        int n = code.length();

        while (i < n) {
            // If we see a '<'
            if (code.charAt(i) == '<') {
                // 1. CDATA section
                if (i + 9 < n && code.startsWith("<![CDATA[", i)) {
                    int end = code.indexOf("]]>", i + 9);
                    if (end == -1) return false;           // no closing ]]
                    i = end + 3;                          // skip CDATA
                    continue;
                }

                // 2. Closing tag
                if (i + 1 < n && code.charAt(i + 1) == '/') {
                    int j = i + 2;
                    int close = code.indexOf('>', j);
                    if (close == -1) return false;        // no closing >
                    String tag = code.substring(j, close);
                    if (!isValidTagName(tag)) return false;
                    if (stack.isEmpty() || !stack.peek().equals(tag)) return false;
                    stack.pop();
                    i = close + 1;
                    continue;
                }

                // 3. Opening tag
                int j = i + 1;
                int close = code.indexOf('>', j);
                if (close == -1) return false;            // no closing >
                String tag = code.substring(j, close);
                if (!isValidTagName(tag)) return false;
                stack.push(tag);
                i = close + 1;
                continue;
            }

            // Any other character is allowed in the content
            i++;
        }

        // The whole code must be wrapped in one top‑level tag
        return stack.isEmpty();
    }

    // Tag name rules: 1–9 uppercase letters
    private boolean isValidTagName(String s) {
        if (s.length() < 1 || s.length() > 9) return false;
        for (int k = 0; k < s.length(); k++) {
            char c = s.charAt(k);
            if (c < 'A' || c > 'Z') return false;
        }
        return true;
    }
}
```

**Why this works**

* We **never** use regex for validation – the problem statement warns about fragile regexes.  
* The stack guarantees correct nesting and that every opening tag has a matching closing tag.  
* CDATA sections are skipped atomically – we never inspect their inner content.  
* The final `stack.isEmpty()` check ensures that the whole snippet is wrapped in one tag.

---

### 2️⃣ Python 3 Solution

```python
class Solution:
    def isValid(self, code: str) -> bool:
        if not code or code[0] != '<':
            return False

        stack = []
        i, n = 0, len(code)

        while i < n:
            if code[i] == '<':
                # CDATA section
                if i + 9 < n and code.startswith("<![CDATA[", i):
                    end = code.find("]]>", i + 9)
                    if end == -1:
                        return False
                    i = end + 3
                    continue

                # Closing tag
                if i + 1 < n and code[i + 1] == '/':
                    j = i + 2
                    close = code.find('>', j)
                    if close == -1:
                        return False
                    tag = code[j:close]
                    if not self._is_valid_tag(tag):
                        return False
                    if not stack or stack[-1] != tag:
                        return False
                    stack.pop()
                    i = close + 1
                    continue

                # Opening tag
                j = i + 1
                close = code.find('>', j)
                if close == -1:
                    return False
                tag = code[j:close]
                if not self._is_valid_tag(tag):
                    return False
                stack.append(tag)
                i = close + 1
                continue

            # Ordinary character
            i += 1

        return not stack

    @staticmethod
    def _is_valid_tag(name: str) -> bool:
        return 1 <= len(name) <= 9 and name.isupper() and name.isalpha()
```

**Python notes**

* `str.find()` is used instead of regex – keeps it fast and simple.  
* `stack` is a list; `append`/`pop` give amortized **O(1)** operations.  
* The helper `_is_valid_tag` keeps the rule check isolated.

---

### 3️⃣ C++17 Solution

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isValid(string code) {
        if (code.empty() || code[0] != '<') return false;

        vector<string> st;
        int i = 0, n = code.size();

        while (i < n) {
            if (code[i] == '<') {
                // CDATA section
                if (i + 9 < n && code.compare(i, 9, "<![CDATA[") == 0) {
                    int end = code.find("]]>", i + 9);
                    if (end == string::npos) return false;
                    i = end + 3;
                    continue;
                }

                // Closing tag
                if (i + 1 < n && code[i + 1] == '/') {
                    int j = i + 2;
                    int close = code.find('>', j);
                    if (close == string::npos) return false;
                    string tag = code.substr(j, close - j);
                    if (!isValidTag(tag)) return false;
                    if (st.empty() || st.back() != tag) return false;
                    st.pop_back();
                    i = close + 1;
                    continue;
                }

                // Opening tag
                int j = i + 1;
                int close = code.find('>', j);
                if (close == string::npos) return false;
                string tag = code.substr(j, close - j);
                if (!isValidTag(tag)) return false;
                st.push_back(tag);
                i = close + 1;
                continue;
            }

            ++i;   // normal character
        }

        return st.empty();
    }

private:
    bool isValidTag(const string &s) {
        if (s.empty() || s.size() > 9) return false;
        for (char c : s)
            if (c < 'A' || c > 'Z') return false;
        return true;
    }
};
```

**C++ tricks**

* `string::compare` gives a constant‑time check for the CDATA prefix.  
* `vector<string>` is used as a stack; it’s fast enough for the 10 000‑character limit.  
* All loops run in **O(n)** – no recursion, no regex.

---

## 📝 Blog Article – “Tag Validator Leetcode 591” (SEO‑Optimized)

> **Keywords**: *Tag Validator, LeetCode 591, XML parsing interview, stack algorithm, job interview coding, software engineer interview, coding interview questions, programming interview patterns*  

### Tag Validator – LeetCode 591 Explained

Imagine you’re a **backend engineer** working on a tiny XML‑like markup engine.  
LeetCode’s **591 – Tag Validator** asks you to decide whether a given string is a valid code snippet following *strict* XML‑style rules:

| Rule | Description |
|------|-------------|
| **Must start with an opening tag** | The entire snippet is wrapped in a single top‑level tag. |
| **Tag name** | 1–9 uppercase alphabetic characters only. |
| **Nesting** | Tags must be properly nested; every `</TAG>` must close the most recently opened tag. |
| **No stray `<` or `>`** | All angle brackets belong to tags or CDATA. |
| **CDATA** | `<![CDATA[ ... ]]>` is treated as raw text; its content is ignored. |

You’re given a single `String code` (≤ 10 000 chars) and must return `true` if it follows all rules, otherwise `false`.

### The “Good” Part – Why a Stack Is the Right Tool

* **Natural representation of nested structures** – each open tag pushes its name onto the stack; a close tag must match the stack top.  
* **Linear time** – you only scan once, every character examined a constant number of times.  
* **Memory bounded by the depth of nesting** – worst‑case `O(n)` but usually far less.  
* **Predictable behaviour** – no surprises from regex engine quirks or backtracking.

### The “Bad” Part – Common Pitfalls

1. **Using regex**  
   Many naive solutions try a big regex to validate tags, but the specification warns that such patterns are *fragile* and hard to maintain.  
   *Why regex fails*: It can’t enforce context‑sensitive nesting (e.g., ensuring `</A>` matches the correct `<A>`).

2. **Missing CDATA handling**  
   Treating CDATA as ordinary text leads to false negatives (e.g., a stray `>` inside CDATA).  
   *Solution*: Skip the entire CDATA block once you detect its opening marker.

3. **Top‑level wrapping check**  
   Forgetting to require that the entire snippet is wrapped in a single tag causes subtle bugs.  
   *Fix*: After the scan, the stack must be empty – any leftover tag means the snippet was not fully closed.

### The “Ugly” Part – Edge Cases That Trip People Up

| Edge Case | Why It’s Ugly | How to Handle |
|-----------|---------------|---------------|
| `<A></A>` – minimal valid snippet | People often forget that the entire string must be wrapped; returning `true` for an empty content inside a tag is easy to miss. | Keep the stack check `stack.empty()` after the loop. |
| Tag name length 10 or mixed case | Subtle off‑by‑one errors when counting characters or checking case. | Encapsulate the rule in a helper (`isValidTagName`). |
| No closing `>` or `]]>` | Index calculations become negative, leading to `StringIndexOutOfBounds`. | Always verify the index is found before slicing. |
| `<` in the middle of content | If not followed by a valid tag or CDATA, it’s an illegal character. | Treat any other `<` as invalid and return `false`. |
| Empty string or first char not `<` | Some people forget this quick pre‑check and let the algorithm run to the end, incorrectly returning `true`. | Return `false` immediately if the string is empty or doesn’t start with `<`. |

### Step‑by‑Step Algorithm Walk‑through

1. **Pre‑check** – The snippet must begin with `<`.  
2. **Initialize an empty stack** – Will hold the names of open tags.  
3. **Scan left → right**  
   * When you hit a `<`:
     * **CDATA** (`<![CDATA[`) – Find the next `]]>` and jump past it.  
     * **Closing tag** (`</TAG>`) – Extract the tag name, validate it, and pop the stack. Return `false` if it doesn’t match the top.  
     * **Opening tag** (`<TAG>`) – Extract the tag name, validate it, and push it onto the stack.  
   * Any other character is allowed in the content – just move on.  
4. **Final check** – The stack must be empty, meaning every open tag was closed and the entire code is wrapped in one top‑level tag.  

### Complexity Analysis

| Metric | Calculation |
|--------|-------------|
| **Time** | Each character is inspected a constant number of times → **O(n)** |
| **Space** | The stack stores at most `n/2` tag names → **O(n)** in the worst case (deep nesting). |

### Interview Tips

* **Explain your stack invariant** – The stack top always represents the most recently opened tag that has not yet been closed.  
* **Show you understand CDATA** – It is not parsed; it’s simply a block of raw text.  
* **Mention the tag name rule** – 1–9 uppercase alphabetic characters.  
* **Avoid regex** – Most interviewers expect you to hand‑craft the parser.  
* **Handle error cases early** – If you find a missing `>` or mismatched tag, exit immediately.  

### Final Thoughts

- **Good** – The stack approach is clean, O(n) and easy to reason about.  
- **Bad** – A complex regex is tempting but fragile; avoid it.  
- **Ugly** – Remember the subtle boundary conditions: the very first `<` must be an opening tag, the very last must be its matching closing tag, and CDATA sections must be skipped atomically.

If you can explain this algorithm, walk through a couple of test cases, and show one of the three solutions above, you’ll have a solid answer for **LeetCode 591 – Tag Validator** that impresses both automated graders and interviewers alike.  

Good luck on your coding interview and your next software‑engineering role! 🚀

--- 

### 📌 Quick Copy‑Paste for Hiring Interviews

```java
// Java 17 – Tag Validator
public class Solution { … }  // see code above
```

```python
# Python 3 – Tag Validator
class Solution: …  # see code above
```

```cpp
// C++17 – Tag Validator
class Solution { … }  // see code above
```

Happy coding!