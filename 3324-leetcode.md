---
title: LeetCode 3324. Find the Sequence of Strings Appeared on the Screen - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Problem Recap  
**LeetCode 3324 – Find the Sequence of Strings Appeared on the Screen**  
*Difficulty: Medium*

Alice has a special keyboard with only two keys  

| Key | Effect |
|-----|--------|
| **1** | Append `'a'` to the current string |
| **2** | Increment the *last* character of the string (`'c' → 'd'`, `'z' → 'a'`) |

Starting from an empty string, Alice wants to type the target string in **the minimum number of key presses**.  
Your task is to return **every intermediate string** that appears on the screen in the order they appear.

---

## ✅ Why This Problem Rocks for Interviews  

| ✅ | Reason |
|----|--------|
| **Algorithmic insight** | You must think about how to achieve the target with the fewest key presses. |
| **String manipulation** | All languages need to efficiently build & modify strings. |
| **Edge‑case handling** | Empty string → first `'a'`, wrap‑around `'z' → 'a'` is trivial here but still worth mentioning. |
| **Scalability** | Target length up to 400 – O(n²) is easily fast enough, but a greedy O(n) solution is even nicer. |

If you can nail this problem, you’ll show interviewers that you know how to turn a seemingly complicated scenario into a simple greedy algorithm.

---

## 🧠 The Optimal Strategy

1. **Add `'a'` first** – you *must* press key 1 at least once for every new character.  
2. **Increment to the desired letter** – use key 2 until the last character matches the target character.  
3. **Record every intermediate string** – every press (whether key 1 or key 2) produces a new string that must be part of the answer.

The greedy rule: *for each character in the target, press key 1 once, then keep pressing key 2 until the last character equals the target character.*  
This is obviously optimal because you can’t skip key 1 for a new character and you can’t skip any required increments of key 2.

---

## 📚 Code Walk‑through

Below are clean, idiomatic implementations in **Java**, **Python**, and **C++**.  
All three share the same logic and run in O(n²) time (worst‑case 25 increments per character) and O(n²) space for the output.

### 1️⃣ Java

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<String> stringSequence(String target) {
        List<String> result = new ArrayList<>();
        StringBuilder cur = new StringBuilder();

        for (char ch : target.toCharArray()) {
            // Key 1: append 'a'
            cur.append('a');
            result.add(cur.toString());

            // Key 2: increment last char until it matches 'ch'
            while (cur.charAt(cur.length() - 1) < ch) {
                char next = (char) ((cur.charAt(cur.length() - 1) - 'a' + 1) % 26 + 'a');
                cur.setCharAt(cur.length() - 1, next);
                result.add(cur.toString());
            }
        }
        return result;
    }
}
```

> **Why `StringBuilder`?**  
> Constant‑time appends and in‑place edits make this implementation memory‑friendly.

### 2️⃣ Python

```python
class Solution:
    def stringSequence(self, target: str) -> list[str]:
        res = []
        cur = []

        for ch in target:
            # Key 1
            cur.append('a')
            res.append(''.join(cur))

            # Key 2
            while cur[-1] < ch:
                cur[-1] = chr(((ord(cur[-1]) - 97 + 1) % 26) + 97)
                res.append(''.join(cur))

        return res
```

> **Why a list of chars?**  
> Lists are mutable and `''.join` is linear‑time; building the string once per press keeps the runtime low.

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<string> stringSequence(string target) {
        vector<string> res;
        string cur;

        for (char ch : target) {
            // Key 1
            cur.push_back('a');
            res.push_back(cur);

            // Key 2
            while (cur.back() < ch) {
                cur.back() = (cur.back() - 'a' + 1) % 26 + 'a';
                res.push_back(cur);
            }
        }
        return res;
    }
};
```

> **Why `cur.back()`?**  
> Gives O(1) access to the last character, ideal for the “increment last char” step.

---

## 📊 Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | O(n²) (≤ 400 * 25 ≈ 10 000 operations) | O(n²) | O(n²) |
| **Space** | O(n²) for the output list | O(n²) | O(n²) |
| **Why O(n²)?** | Each of the `n` target characters can trigger up to 25 increments of key 2. |

For the constraints (`n ≤ 400`) this is trivial – all three solutions run in < 1 ms on modern judges.

---

## ⚠️ Common Pitfalls

| Mistake | Why it breaks |
|---------|---------------|
| **Skipping the final increment** | If you forget to record the string when the last character finally matches the target, the answer will be incomplete. |
| **Using `+=` on immutable strings (Java/Python)** | Creates many intermediate objects and blows up memory/time. Use `StringBuilder` or a list. |
| **Over‑optimizing to “O(n)”** | It’s impossible; you must record every intermediate string, which inherently is O(n²). |
| **Mis‑handling `'z' → 'a'`** | Though the problem’s target consists only of lowercase letters, wrap‑around logic is essential for a generic solution. |

---

## 🎯 Take‑aways for Your Interview

1. **Greedy is king** – always look for the “smallest possible step” that still keeps you on track.  
2. **Explain your rationale** – “I’m pressing key 1 once per new character because you can’t skip it; I press key 2 until the last character matches because that’s the minimal number of increments.”  
3. **Show the code is clean** – use language‑idiomatic data structures (Java `StringBuilder`, Python list, C++ string).  
4. **Mention complexity** – interviewers love seeing you think about performance.  
5. **Test edge cases** – empty target (invalid per constraints but still good to think about), single character, or target containing `'z'`.

Mastering this problem will give you a solid footing for other “string simulation” and “incremental construction” questions you may encounter.

---

## 📝 Blog Article: “The Good, The Bad, and the Ugly of LeetCode 3324”

> **Keywords**: *LeetCode 3324, Find the Sequence of Strings Appeared on the Screen, algorithm, Java solution, Python solution, C++ solution, interview question, string manipulation, greedy algorithm, job interview tips, software engineer interview*

---

### Introduction

LeetCode’s *Find the Sequence of Strings Appeared on the Screen* (Problem 3324) is a deceptively simple problem that turns into a great showcase of greedy reasoning and efficient string handling. In this article, I’ll walk through the **good**, **bad**, and **ugly** ways people solve it, then reveal the clean, optimal solution that will impress recruiters.

---

### The Good – A Clean Greedy Approach

> *Why is this good?*  
> 1. **Minimal key presses**: The solution follows a proven greedy strategy—press “add 'a'” once for every new character, then increment the last character until it matches the target.  
> 2. **Linear logic**: One pass over the target string, constant work per character (except the inevitable increments).  
> 3. **Readable code**: Uses idiomatic language constructs (`StringBuilder`, `list`, `std::string`).  

The Java, Python, and C++ snippets above exemplify this style.

---

### The Bad – Over‑Complicated or Inefficient Variants

| Bad Approach | Why It’s Bad |
|--------------|--------------|
| **Brute‑force BFS** | Generates all possible strings up to the target length; exponential blow‑up. |
| **Recursive backtracking** | Re‑computes the same prefixes many times; still O(n²) but with heavy recursion overhead. |
| **Using `+` to concatenate strings** | In languages like Java/Python, creates a new string object on every press; quadratic memory churn. |

These patterns exist because people get **trapped in “find the shortest sequence”** thinking it requires a search. Once you realize you *must* record every intermediate string, you can drop the search entirely.

---

### The Ugly – Ignoring Edge Cases and Misusing Data Structures

> *What makes this ugly?*  
> 1. **Immutable string abuse** – `current = current + 'a'` in Java or `current += 'a'` in Python.  
> 2. **Incorrect wrap‑around logic** (`(char)(current.back() + 1)` without modulo 26).  
> 3. **Hard‑coded limits** – Assuming 25 increments always; neglecting the special case where a character is `'a'` itself.  

Ugly code often results in memory limits or TLE even on trivial test cases.

---

### The Ugly – The “One‑liner” Misconception

> *Some candidates try to squeeze the entire solution into a single line, using fancy list comprehensions or lambda functions.*  
> This looks clever but actually hides a complex loop, making debugging near‑impossible. Recruiters rarely appreciate code that looks good but can’t be traced.

---

### Your Final Toolkit – Why The Greedy is Enough

- **Explain the “why”** – not just the “how”.  
- **Discuss time & space** – O(n²) is the best you can do because the output itself has n² elements.  
- **Show test coverage** – Verify single‑character targets, all `'z'` targets, and wrap‑around behavior.

---

### Conclusion

Problem 3324 is a micro‑classroom of **algorithm design, language mastery, and interview communication**. The *good* solution uses a straightforward greedy strategy and clean code. The *bad* solutions waste time and memory, and the *ugly* ones obfuscate the core idea.

By mastering this problem, you’ll demonstrate to recruiters that you can:

- Translate a real‑world constraint (minimal key presses) into an optimal algorithm.  
- Handle string manipulation elegantly in any of the major languages.  
- Communicate complexity and edge‑case handling confidently.

**Take the next time you see a string‑simulation question—remember the greedy “add 'a' then increment” pattern, and you’ll be ready to impress any hiring manager. Happy coding! 🚀**