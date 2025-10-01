---
title: LeetCode 3324. Find the Sequence of Strings Appeared on the Screen - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## LeetCode 3324 – *Find the Sequence of Strings Appeared on the Screen*  
### The Good, The Bad, and The Ugly  
*Optimized, language‑agnostic solution, plus SEO‑friendly blog article to help you land your next software‑engineering job.*

---

## Problem Recap

| Item | Detail |
|------|--------|
| **ID** | 3324 |
| **Difficulty** | Medium |
| **Input** | `target` – a non‑empty string of lowercase English letters (≤ 400 chars) |
| **Output** | `List<String>` – every string that appears on the screen while Alice types `target` using the **minimum** number of key presses. |
| **Keys** | 1️⃣ Append `'a'` to the end.<br>2️⃣ Increment the last character (wrap `'z' → 'a'`). |

The screen starts empty. We must output the *exact* intermediate strings in the order they appear when Alice types the target string with the fewest key presses possible.

---

## Why This Problem is a Gold‑Mine for Interviews

1. **Simulation + Greedy** – You’ll demonstrate your ability to model real‑world constraints in code.
2. **Edge‑Case Handling** – Wrap‑around (`'z' → 'a'`) tests your attention to detail.
3. **Complexity Awareness** – A naïve solution can be O(n²) in the worst case; we’ll show a clean O(n·26) (≈ O(n)) solution that still respects the constraints.

---

## The Good

- **Intuitive, Step‑by‑Step** – Build the string from left to right, mimicking exactly what Alice does.
- **Linear Time** – Only up to 26 key presses per target character, so even the longest input is trivial for modern CPUs.
- **Clear Code** – Easy to read, easy to review.

---

## The Bad

- **Potential Off‑By‑One** – Forgetting to add the first `'a'` for each new target character results in an empty output.
- **Ignoring Wrap‑Around** – Treating `'z'` as a dead end will break cases where the target contains letters after `'z'`.

---

## The Ugly

- **Unnecessary Re‑Calculations** – Re‑building the entire string each time instead of mutating it leads to O(n²) memory and time.
- **Hidden State Mutations** – In languages like Java, using a `String` (immutable) in a tight loop can cause heavy allocation.

---

## Optimal Solution – “Append‑then‑Increment” Strategy

The key insight:  
**To type a new target character `c`, first add an `'a'` (press 1), then repeatedly press 2 until the last character equals `c`.**  

Why?  
- Press 1 guarantees we’re always adding a new character.
- Press 2 changes the *last* character; we never touch the earlier part of the string.
- Because we’re always incrementing the last character, the path is the shortest possible.

Pseudo‑logic:

```
cur = ""
for each character ch in target:
    cur += 'a'              // press 1
    record cur
    while last(cur) != ch:  // press 2 until target char reached
        cur[last] = nextChar(cur[last])
        record cur
```

`nextChar(x)` is `x+1` unless `x == 'z'`, then `nextChar(x) = 'a'`.

---

## Code Implementations

Below you’ll find clean, production‑ready code for **Java, Python, and C++**. All three use the same algorithmic idea and have the same O(n·26) time and O(n) space complexity.

> **Tip for Interviews** – Show the “Append‑then‑Increment” intuition before diving into the code.

### Java

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<String> stringSequence(String target) {
        List<String> result = new ArrayList<>();
        StringBuilder cur = new StringBuilder();

        for (char ch : target.toCharArray()) {
            // Step 1: press key 1 → append 'a'
            cur.append('a');
            result.add(cur.toString());

            // Step 2: press key 2 until last char equals target char
            while (cur.charAt(cur.length() - 1) != ch) {
                char last = cur.charAt(cur.length() - 1);
                char next = last == 'z' ? 'a' : (char) (last + 1);
                cur.setCharAt(cur.length() - 1, next);
                result.add(cur.toString());
            }
        }
        return result;
    }
}
```

### Python

```python
from typing import List

class Solution:
    def stringSequence(self, target: str) -> List[str]:
        res = []
        cur = []

        for ch in target:
            # press key 1
            cur.append('a')
            res.append(''.join(cur))

            # press key 2 until last char == ch
            while cur[-1] != ch:
                nxt = chr((ord(cur[-1]) - ord('a') + 1) % 26 + ord('a'))
                cur[-1] = nxt
                res.append(''.join(cur))

        return res
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<string> stringSequence(string target) {
        vector<string> res;
        string cur;
        for (char ch : target) {
            // press key 1
            cur.push_back('a');
            res.push_back(cur);

            // press key 2 until last char equals ch
            while (cur.back() != ch) {
                char last = cur.back();
                cur.back() = (last == 'z') ? 'a' : last + 1;
                res.push_back(cur);
            }
        }
        return res;
    }
};
```

---

## Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(26 · n)`  (≤ ≈ 10400 operations) | `O(26 · n)` | `O(26 · n)` |
| **Space** | `O(n)` (output list) | `O(n)` | `O(n)` |

The algorithm is linear with a small constant factor. Even for the maximum `n = 400`, the runtime is well below 1 ms in all three languages.

---

## Common Pitfalls & How to Avoid Them

| Pitfall | Fix |
|---------|-----|
| Forgetting to add `'a'` for the first character of each segment | Always `cur.append('a')` before the `while` loop. |
| Not handling `'z' → 'a'` wrap | Use modulo arithmetic or a conditional check (`last == 'z' ? 'a' : last + 1`). |
| Using immutable strings in a tight loop (Java) | Use `StringBuilder` and mutate in place. |
| Mis‑indexing the last character in C++ (`cur.back()`) | `cur.back()` is safe and readable. |

---

## When to Use This Approach

- **Interview setting** – Clear simulation, linear complexity, minimal edge‑case code.
- **Coding competitions** – Fast implementation, no need for fancy data structures.
- **Production code** – If you need to log every intermediate state of a string-building process (e.g., UI animation).

---

## Take‑away Checklist

- ✅ Understand the two keys and their effect.
- ✅ Build the string from left to right: append `'a'` then increment until target.
- ✅ Wrap around `'z'` → `'a'` correctly.
- ✅ Use mutable structures (`StringBuilder`, `list`, `string`) to keep the algorithm O(n).
- ✅ Test with edge cases: `"a"`, `"z"`, `"az"`, `"zzzz"`.

---

## How This Blog Helps Your Career

- **SEO‑friendly** – Keywords: *LeetCode 3324*, *string simulation*, *Java/Python/C++ solution*, *interview problem*, *software engineering interview*.
- **Readable, actionable** – Clear code snippets, complexity table, and pitfalls.
- **Showcases** – Demonstrates your ability to explain trade‑offs (“good, bad, ugly”) – a key interview skill.

Add this article to your portfolio, share it on LinkedIn, and let recruiters know you’ve nailed this *medium‑difficulty* LeetCode challenge.

---

### Ready for the next step?

- Try implementing the solution on your own in a new language.
- Add a *unit test suite* (JUnit, PyTest, GoogleTest) to show test‑driven development.
- Post the code on GitHub with a clear `README.md`. Recruiters love *open‑source proof of work*.

> **Good luck!** 🎯   
> If you found this helpful, drop a comment or share the article. Your next interview might be just a click away!