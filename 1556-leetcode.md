---
title: LeetCode 1556. Thousand Separator - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. LeetCode 1556 – Thousand Separator  
**Easy | Time O(d) | Space O(d)**  

| Language | Main Idea | Code |
|----------|-----------|------|
| **Java** | Convert to string, walk from the end inserting a dot after every 3 digits, then reverse. | **[Java Code]** |
| **Python** | Same logic, but Python’s slicing & `join` make it very concise. | **[Python Code]** |
| **C++** | Use `std::to_string`, build a `std::string` in reverse, then reverse it. | **[C++ Code]** |

> **Goal** – Given a non‑negative integer `n`, return a string representation of `n` with a dot (`.`) used as the thousand separator.

> **Examples**  
> - `n = 987` ➜ `"987"`  
> - `n = 1234` ➜ `"1.234"`  

> **Constraints**  
> `0 ≤ n ≤ 2³¹ – 1`

---

## 2. The Solution in Detail  

### 2.1 Intuition  
* The number is simply a sequence of digits.  
* Every group of three digits, counted from the right, must be separated by a dot.  
* If the length of the number is not a multiple of three, the left‑most group will contain 1‑2 digits.  
* Since we only need to insert separators, we can process the string once, building the answer in reverse, and then reverse it at the end.

### 2.2 Algorithm
1. Convert `n` to a string `s`.  
2. Traverse `s` from the last character to the first.  
3. Append each character to a `StringBuilder` (or equivalent).  
4. Every time we have added 3 characters (i.e., `count % 3 == 0`) **and** we are **not** at the first character, append a dot.  
5. After the loop, reverse the `StringBuilder` to get the final string.

### 2.3 Complexity Analysis  
* **Time** – O(d), where `d` is the number of digits in `n` (max 10 for a 32‑bit int).  
* **Space** – O(d) for the output string (the `StringBuilder`).

### 2.4 Edge Cases  
* `n = 0` → `"0"` (no dot needed).  
* Numbers with exactly 3, 6, 9 digits – still work because the loop stops before adding a trailing dot.  
* The problem guarantees non‑negative integers, so we don't need to handle negative signs.

---

## 3. Code

### 3.1 Java

```java
class Solution {
    public String thousandSeparator(int n) {
        String s = String.valueOf(n);
        StringBuilder sb = new StringBuilder();

        int count = 0;
        for (int i = s.length() - 1; i >= 0; i--) {
            sb.append(s.charAt(i));
            count++;
            if (count % 3 == 0 && i != 0) {
                sb.append('.');
            }
        }
        return sb.reverse().toString();
    }
}
```

> **Why this works** – `StringBuilder` lets us append in O(1).  
> We reverse at the end because we built the string backwards.

---

### 3.2 Python

```python
class Solution:
    def thousandSeparator(self, n: int) -> str:
        s = str(n)
        parts = []

        # Process from right to left, 3 digits at a time
        while s:
            parts.append(s[-3:])
            s = s[:-3]

        return '.'.join(reversed(parts))
```

> **Pythonic twist** – Using slicing we split the string into 3‑digit chunks in one pass.  
> `reversed(parts)` turns them back into the original order.

---

### 3.3 C++

```cpp
#include <string>
#include <algorithm>

class Solution {
public:
    std::string thousandSeparator(int n) {
        std::string s = std::to_string(n);
        std::string res;
        int count = 0;

        for (int i = static_cast<int>(s.size()) - 1; i >= 0; --i) {
            res += s[i];
            ++count;
            if (count % 3 == 0 && i != 0)
                res += '.';
        }
        std::reverse(res.begin(), res.end());
        return res;
    }
};
```

> **C++ notes** – `std::to_string` gives the decimal representation; we build the answer in reverse and then reverse it.

---

## 4. The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Simplicity** | One linear pass, no extra data structures beyond a string builder. | None. | None. |
| **Performance** | Extremely fast – O(10) operations for any 32‑bit int. | For very large numbers (beyond 64‑bit) you'd need a big‑int library. | None. |
| **Readability** | Clear variable names, comments, and step‑by‑step logic. | In some languages, the reversal trick can be non‑obvious to beginners. | None. |
| **Extensibility** | Easy to adapt to other separators or locales. | If you need commas instead of dots, you just change one character. | None. |
| **Potential Pitfalls** | Forgetting to stop after the first digit → trailing dot. | None. | None. |

**Bottom line:** The problem is a *micro‑challenge* that showcases clean string manipulation and careful indexing – perfect for polishing interview skills.

---

## 5. SEO‑Optimized Blog Article

### Title  
**“Master LeetCode 1556: Thousand Separator – Java, Python, & C++ Solutions + Interview Tips”**

### Meta Description  
Solve LeetCode “Thousand Separator” in Java, Python, and C++. Read a step‑by‑step algorithm, discuss the good, bad, and ugly parts, and get interview-ready coding tips.

### Heading Structure  

| Heading | Why it matters |
|---------|----------------|
| `# Master LeetCode 1556: Thousand Separator – All Language Solutions` | Main keyword |
| `## Problem Statement & Constraints` | Gives context, keyword heavy |
| `## Intuitive Approach` | Show algorithmic thinking |
| `## Java Implementation` | Language‑specific code block |
| `## Python Implementation` | Code block with `python` tag |
| `## C++ Implementation` | Code block with `cpp` tag |
| `## Complexity Analysis` | Discuss O(d) time & space |
| `## The Good, The Bad, The Ugly` | Insightful reflection for recruiters |
| `## Interview Tips: How to Answer “How would you solve this?”` | Direct hiring relevance |
| `## Takeaway & Practice Ideas` | Encourages further learning |
| `## FAQ` | Common interview follow‑ups |

### Sample Content  

```markdown
# Master LeetCode 1556: Thousand Separator – All Language Solutions

**LeetCode 1556** is an *Easy* problem that tests your ability to manipulate strings and think about digit grouping. Below you’ll find clean, production‑ready code in **Java**, **Python**, and **C++**, as well as a deep dive into what makes this solution good, what could be improved, and why you’ll impress interviewers.

## Problem Statement & Constraints

> *Given an integer `n`, add a dot (`.`) as the thousands separator and return it in string format.*

- `0 <= n <= 2³¹ – 1`
- Example: `1234` ➜ `"1.234"`

## Intuitive Approach

1. Convert the number to a string.  
2. Walk the string from right to left.  
3. Append every third digit with a `.` (except before the first digit).  
4. Reverse the built string to restore the original order.

This guarantees **O(d)** time and **O(d)** space where `d` is the digit count.

## Java Implementation

```java
class Solution {
    public String thousandSeparator(int n) {
        String s = String.valueOf(n);
        StringBuilder sb = new StringBuilder();
        int count = 0;
        for (int i = s.length() - 1; i >= 0; i--) {
            sb.append(s.charAt(i));
            count++;
            if (count % 3 == 0 && i != 0) sb.append('.');
        }
        return sb.reverse().toString();
    }
}
```

## Python Implementation

```python
class Solution:
    def thousandSeparator(self, n: int) -> str:
        s = str(n)
        parts = []
        while s:
            parts.append(s[-3:])
            s = s[:-3]
        return '.'.join(reversed(parts))
```

## C++ Implementation

```cpp
#include <string>
#include <algorithm>

class Solution {
public:
    std::string thousandSeparator(int n) {
        std::string s = std::to_string(n);
        std::string res;
        int count = 0;
        for (int i = static_cast<int>(s.size()) - 1; i >= 0; --i) {
            res += s[i];
            ++count;
            if (count % 3 == 0 && i != 0) res += '.';
        }
        std::reverse(res.begin(), res.end());
        return res;
    }
};
```

## Complexity Analysis

- **Time**: `O(d)` – each digit is processed once.  
- **Space**: `O(d)` – for the resulting string.

## The Good, The Bad, The Ugly

| ✔️ Good | ⚠️ Bad | 😈 Ugly |
|---------|--------|--------|
| Straightforward, no external libraries | None – solution is already optimal | None – the problem itself is simple, so there's no “ugly” bug to fix |

## Interview Tips: How to Answer “How would you solve this?”

1. **State the constraints** – 32‑bit integer, non‑negative.  
2. **Explain the idea** – “I’ll convert to string, walk from the end, insert dots.”  
3. **Mention edge cases** – `0`, numbers with 3, 6, 9 digits.  
4. **Show the algorithm** – linear pass with counter.  
5. **Talk about complexity** – O(d) time & space.  
6. **Mention possible variations** – change separator, locale formatting, big integers.

## Takeaway & Practice Ideas

- Practice similar problems: **Add One to Integer**, **Reverse Integer**, **Reformat Phone Number**.  
- Try implementing the same logic using **locale** libraries (`NumberFormat` in Java, `locale` in Python) to compare with manual string manipulation.  

## FAQ

1. **Can we use built‑in formatting?**  
   Yes, but the exercise is to show manual manipulation – interviewers appreciate that.

2. **What about negative numbers?**  
   The problem guarantees non‑negative. For negatives, just prepend a `-` before the formatted part.

3. **What if the number exceeds 32‑bit?**  
   Use `long` or `BigInteger` in Java, `long` in Python (arbitrary precision), and `int64_t` in C++.

Happy coding, and best of luck on your next interview!

---

### Why This Blog Will Help You Get a Job

- **Keyword‑rich content**: “LeetCode Thousand Separator”, “Java solution”, “Python solution”, “C++ solution”, “coding interview”.
- **Practical code snippets**: recruiters love seeing clean, testable code.
- **Insightful analysis**: shows you can discuss trade‑offs, complexity, and interview strategy.
- **SEO‑friendly structure**: headings, code blocks, FAQs, and meta description make it discoverable.

**Next Steps:** Add this article to your portfolio site, share it on LinkedIn, and tag recruiters. Good luck!