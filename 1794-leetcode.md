---
title: LeetCode 1794. Count Pairs of Equal Substrings With Minimum Difference - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🚀 LeetCode 1794 – “Count Pairs of Equal Substrings With Minimum Difference”  
### A Deep‑Dive, “Good‑Bad‑Ugly” Breakdown + 3‑Language Solution (Java | Python | C++)

> **SEO Keywords** – LeetCode, algorithm interview, string matching, coding interview, Java solution, Python solution, C++ solution, job interview, data‑structures, time‑complexity, space‑complexity, interview preparation, software engineering interview

---

### 1️⃣ Problem Overview

> **LeetCode 1794**  
> *Medium*  

You’re given two strings `firstString` and `secondString` (both only lowercase letters).  
Count all index quadruples `(i, j, a, b)` that satisfy:

| Condition | Description |
|-----------|-------------|
| `0 ≤ i ≤ j < firstString.length` | Substring in `firstString` |
| `0 ≤ a ≤ b < secondString.length` | Substring in `secondString` |
| `firstString[i … j] == secondString[a … b]` | Equal substrings |
| `j - a` is **minimal** among all quadruples that satisfy the above | We only care about the smallest possible difference |

Return the number of quadruples that achieve this minimal `j - a`.

> **Why is this interesting?**  
> At first glance it looks like a classic “equal substring” problem that could explode combinatorially.  
> The twist: the answer never needs a substring longer than one character!

---

### 2️⃣ The “Good‑Bad‑Ugly” Analysis

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Observations** | 1‑char substrings suffice → linear time. | Misinterpreting “minimal difference” as “shortest length”. | Forgetting that `j - a` can be negative; ignoring the sign. |
| **Algorithm** | Store earliest occurrence of each letter in `firstString`. Iterate `secondString` from end, computing `diff = firstIdx[char] - i`. | A naïve double loop → `O(n²)` or using suffix arrays → overkill. | Edge‑cases: repeated characters, no common letter → return `0`. |
| **Complexity** | `O(n + m)` time, `O(26)` space. | `O(n²)` would time‑out on `2·10⁵`. | Handling huge strings without overflow (use `int`; difference fits in `int`). |
| **Code** | Clear, concise, no extra data structures. | Too many nested loops → readability suffers. | Uninitialized map entries → NullPointerException in Java; off‑by‑one errors in C++ loops. |
| **SEO** | Uses popular keywords (“LeetCode”, “interview”, “string”, “Java”, “Python”, “C++”). | Poor SEO: no tags, no keyword density. | Over‑optimizing for SEO can hurt readability. |

---

### 3️⃣ The Elegant Solution

The key insight:  
**If two substrings are equal, you can always shrink them to a single matching character without increasing `j - a`.**  
Hence the minimal `j - a` is simply the smallest difference between an index in `firstString` and an index in `secondString` for a common character.  
Count how many pairs achieve that difference.

---

## 4️⃣ 3‑Language Implementations

> All solutions run in **O(n + m)** time and **O(1)** space (only 26‑size arrays).

---

### 4.1 Java

```java
import java.util.*;

class Solution {
    public int countQuadruples(String firstString, String secondString) {
        // Map each char to its first occurrence in firstString
        int[] firstIdx = new int[26];
        Arrays.fill(firstIdx, -1);
        for (int i = 0; i < firstString.length(); i++) {
            int c = firstString.charAt(i) - 'a';
            if (firstIdx[c] == -1) firstIdx[c] = i;   // store earliest
        }

        int minDiff = Integer.MAX_VALUE;
        int answer = 0;

        // Scan secondString from end to the beginning
        for (int i = secondString.length() - 1; i >= 0; i--) {
            int c = secondString.charAt(i) - 'a';
            if (firstIdx[c] == -1) continue;          // char not in firstString

            int diff = firstIdx[c] - i;               // j - a with j=i, b=i
            if (diff < minDiff) {
                minDiff = diff;
                answer = 1;
            } else if (diff == minDiff) {
                answer++;
            }
        }
        return answer;
    }
}
```

**Explanation**  
- `firstIdx` stores the *earliest* index of each letter.  
- While iterating `secondString` from back to front, we guarantee that `i` is the *latest* possible `a` for that letter, which together with the earliest `i` from `firstString` gives the minimal `j - a`.  
- We keep the global `minDiff` and count how many times it occurs.

---

### 4.2 Python

```python
class Solution:
    def countQuadruples(self, firstString: str, secondString: str) -> int:
        first_idx = [-1] * 26
        for i, ch in enumerate(firstString):
            idx = ord(ch) - 97
            if first_idx[idx] == -1:
                first_idx[idx] = i            # first (smallest) occurrence

        min_diff = float('inf')
        count = 0

        # iterate secondString from end to start
        for i in range(len(secondString) - 1, -1, -1):
            idx = ord(secondString[i]) - 97
            if first_idx[idx] == -1:
                continue                      # char not in firstString

            diff = first_idx[idx] - i         # j-a with j=i, b=i
            if diff < min_diff:
                min_diff = diff
                count = 1
            elif diff == min_diff:
                count += 1

        return count
```

**Pythonic Highlights**  
- Uses a simple list of size 26 instead of a dict for speed.  
- `range(len(secondString) - 1, -1, -1)` gives a reverse loop.

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int countQuadruples(string firstString, string secondString) {
        vector<int> firstIdx(26, -1);
        for (int i = 0; i < (int)firstString.size(); ++i) {
            int c = firstString[i] - 'a';
            if (firstIdx[c] == -1) firstIdx[c] = i;   // store earliest
        }

        int minDiff = INT_MAX;
        int answer = 0;

        for (int i = (int)secondString.size() - 1; i >= 0; --i) {
            int c = secondString[i] - 'a';
            if (firstIdx[c] == -1) continue;           // char not in firstString
            int diff = firstIdx[c] - i;                // j - a
            if (diff < minDiff) {
                minDiff = diff;
                answer = 1;
            } else if (diff == minDiff) {
                ++answer;
            }
        }
        return answer;
    }
};
```

**Why C++ works**  
- `vector<int>` of size 26 gives O(1) lookups.  
- No dynamic memory beyond the two strings – perfect for interview constraints.

---

## 5️⃣ Common Pitfalls & How to Avoid Them

| Mistake | Why it Happens | Fix |
|---------|----------------|-----|
| Using `HashMap<Character,Integer>` in Java and accessing a missing key → `NullPointerException` | Forgetting to check existence | Use `containsKey()` or an array of size 26 |
| Starting `minDiff` at `0` | The minimal difference can be negative | Initialize with `Integer.MAX_VALUE` / `INT_MAX` |
| Counting quadruples incorrectly when `j-a` is negative | Confusion between sign and magnitude | Treat `diff = firstIdx - i` directly |
| Ignoring that repeated characters may produce *multiple* minimal pairs | Assumed only one pair per character | Loop over all indices of `secondString` (reverse) to catch all |
| Using O(n²) substring comparisons | Over‑engineering | Realize 1‑char substrings suffice |

---

## 6️⃣ Why This Problem Rocks for Job Interviews

1. **String Handling + Hashing** – Core skill for many backend positions.  
2. **Insightful Optimization** – Demonstrates ability to find the key observation (“only single characters matter”).  
3. **Scalable Code** – O(n+m) solution easily passes 200k constraints.  
4. **Language Agnostic** – You can present the same logic in Java, Python, or C++—perfect for a portfolio.  
5. **Edge‑Case Thinking** – Handling negative differences and repeated letters shows thoroughness.  

> **Pro Tip**: When you solve LeetCode 1794, be ready to explain *why* the minimal difference is achieved by 1‑character substrings. That insight is often the *aha!* moment interviewers look for.

---

## 7️⃣ Final Takeaway

> **The minimal `j - a` is just the smallest difference between an index in `firstString` and a later index in `secondString` for any shared letter.**  
> Count the occurrences of that difference – you’re done.

With the 3‑language solutions above, you can drop the code into your interview toolkit, flash your **O(n+m)** brilliance, and impress recruiters with clean, well‑documented, and SEO‑friendly explanations. 🚀

---

**Happy coding, and good luck landing that software engineering role!**  
*(Feel free to reach out if you’d like a deeper walkthrough of the Java implementation or help crafting a blog post with these solutions.)*