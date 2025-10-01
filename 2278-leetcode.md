---
title: LeetCode 2278. Percentage of Letter in String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  Solving LeetCode 2278 – “Percentage of Letter in String”

Below you’ll find three polished, production‑ready solutions for the **“Percentage of Letter in String”** problem, written in **Java**, **Python**, and **C++**.  
All three share the same simple O(n) logic, but each is tailored to the idioms of its language and includes thorough comments to make the code future‑proof.

---

### 📜 Problem Recap (LeetCode 2278)

> **Input**  
> • `s` – a string of length `1 ≤ s.length ≤ 100`  
> • `letter` – a single lowercase English letter  
> **Output**  
> Return the percentage (rounded *down* to the nearest whole number) of characters in `s` that equal `letter`.

*Examples*

| s      | letter | Output | Explanation                               |
|--------|--------|--------|-------------------------------------------|
| "foobar" | "o" | 33 | 2/6 × 100 ≈ 33.33 → 33 (floor) |
| "jjjj"   | "k" | 0  | 0/4 × 100 = 0                              |

---

## 2️⃣  Three Implementations

### 2.1 Java (Java 17+)

```java
/**
 * LeetCode 2278 – Percentage of Letter in String
 * -------------------------------------------------
 * • Time   : O(n) – single pass over the string
 * • Space  : O(1) – constant extra memory
 */
public class Solution {

    /**
     * Calculates the percentage of `letter` in the string `s`.
     *
     * @param s      the input string (non‑empty, lowercase)
     * @param letter the character to count
     * @return floor( (#letter / s.length) * 100 )
     */
    public int percentageLetter(String s, char letter) {
        // Defensive checks – not strictly required by LeetCode constraints
        if (s == null || s.isEmpty()) {
            return 0;
        }

        int count = 0;
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == letter) {
                count++;
            }
        }

        // Integer division automatically floors the result
        return (count * 100) / s.length();
    }
}
```

> **Why this Java code is “good”**  
> • Clear method documentation (Javadoc)  
> • Single pass, O(n) time, O(1) space  
> • Handles the trivial empty‑string edge case (not required by constraints but defensive)  
> • Uses integer math so no floating‑point surprises

---

### 2.2 Python 3.10+

```python
"""
LeetCode 2278 – Percentage of Letter in String

Complexity
----------
Time   : O(n)  – one pass over the string
Space  : O(1)  – only a few integer counters
"""

def percentage_letter(s: str, letter: str) -> int:
    """
    Return the floor percentage of `letter` in `s`.

    Parameters
    ----------
    s : str
        Input string (1‑100 chars, lowercase English letters)
    letter : str
        A single lowercase letter to count

    Returns
    -------
    int
        Floor percentage (0‑100)
    """
    if not s:
        return 0

    count = s.count(letter)          # Python built‑in is highly optimized
    return (count * 100) // len(s)   # // is integer floor division
```

> **Python niceties**  
> • Uses the built‑in `str.count()` for readability and speed.  
> • Integer floor division (`//`) gives the same “rounded down” behaviour as the Java example.  

---

### 2.3 C++17

```cpp
/*
 * LeetCode 2278 – Percentage of Letter in String
 * ------------------------------------------------
 * Time   : O(n) – single pass
 * Space  : O(1) – only a few ints
 */

class Solution {
public:
    int percentageLetter(string s, char letter) {
        if (s.empty()) return 0;

        int count = 0;
        for (char c : s) {
            if (c == letter) ++count;
        }

        // Integer division automatically floors the result
        return (count * 100) / static_cast<int>(s.size());
    }
};
```

> **C++ best practices**  
> • Range‑based for loop (`for (char c : s)`) keeps the code short and clear.  
> • Explicit cast to `int` avoids accidental promotion to `size_t`.  

---

## 3️⃣  Algorithm & Complexity Recap

| Language | Time | Space | Notes |
|----------|------|-------|-------|
| Java     | O(n) | O(1)  | Integer division floors |
| Python   | O(n) | O(1)  | `//` does integer floor |
| C++      | O(n) | O(1)  | Simple loop |

Because `n ≤ 100`, the solution runs in micro‑seconds on any machine, making it trivial for the LeetCode test harness.

---

## 4️⃣  Common Pitfalls (The Ugly)

| Pitfall | Why it Happens | Fix |
|---------|----------------|-----|
| Using floating‑point and `round()` | Might produce 34 for 33.33 due to rounding up | Stick to integer arithmetic |
| Off‑by‑one in division | `count * 100 / n` works because both operands are ints | Use `//` in Python or plain `/` in Java/C++ |
| Forgetting to handle empty string | LeetCode guarantees non‑empty but defensive code helps in real apps | Add `if (s.empty()) return 0;` |
| Using `Math.round()` in Java | Gives nearest integer, not floor | Use integer division directly |

---

## 5️⃣  Testing Your Solution

Below are a few quick tests you can run in your IDE or online compiler.

### Java

```java
public static void main(String[] args) {
    System.out.println(new Solution().percentageLetter("foobar", 'o')); // 33
    System.out.println(new Solution().percentageLetter("jjjj", 'k'));   // 0
}
```

### Python

```python
print(percentage_letter("foobar", "o"))  # 33
print(percentage_letter("jjjj", "k"))    # 0
```

### C++

```cpp
int main() {
    Solution sol;
    cout << sol.percentageLetter("foobar", 'o') << endl; // 33
    cout << sol.percentageLetter("jjjj", 'k') << endl;   // 0
}
```

All outputs match the expected results.

---

## 6️⃣  SEO‑Optimized Blog Article

> **Title:**  
> “Cracking LeetCode 2278: Percentage of Letter in String – Java, Python & C++ Solutions + Interview Tips”

> **Meta Description:**  
> Master LeetCode 2278 in minutes. Learn the easy O(n) solution in Java, Python, and C++, understand the pitfalls, and get interview‑ready. Perfect for coding interview prep and landing your next software engineering job.

---

### 🚀 Blog Post

**Cracking LeetCode 2278: Percentage of Letter in String – Java, Python & C++ Solutions + Interview Tips**

---

#### 1️⃣ Why this problem matters

When recruiters ask you to “calculate the percentage of a specific character in a string”, they’re testing:

1. **Basic algorithmic skills** – single pass, counting, simple arithmetic.  
2. **Language fluency** – can you express the idea in Java, Python, or C++ without errors?  
3. **Edge‑case awareness** – how do you deal with empty strings, uppercase letters, or non‑alphabetic characters?

A solid solution to this problem showcases your ability to write clean, efficient, and bug‑free code—exactly what hiring managers look for.

---

#### 2️⃣ Problem Overview

> **Given** a string `s` (length ≤ 100, lowercase letters) and a character `letter`, **return** the floor percentage of `letter` inside `s`.

The solution requires a single linear scan, counting occurrences, then computing  
`(count * 100) / s.length`.

---

#### 3️⃣ The “Good” – Clean, Idiomatic Code

- **Java:** Uses `String.charAt(i)` in a tight loop, constant space.  
- **Python:** Leverages `str.count()` and integer floor division.  
- **C++:** Employs a range‑based for loop and static cast for safety.

All three are O(n) time, O(1) space, and use integer arithmetic to avoid rounding surprises.

---

#### 4️⃣ The “Bad” – Common Mistakes

| Mistake | Impact | Fix |
|---------|--------|-----|
| `Math.round((count * 100.0) / n)` in Java | Rounds **up**, not floor | Use integer division directly |
| `float` or `double` division in Python | Floating‑point inaccuracies | Stick to integer division (`//`) |
| `if (n == 0)` check not needed | Unnecessary branching | Not needed because of problem constraints |

---

#### 5️⃣ The “Ugly” – Why It Happens and How to Avoid It

1. **Over‑engineering** – Adding extra helper methods for such a trivial task can clutter the solution.  
2. **Ignoring defensive programming** – Not checking for `null` or empty strings may lead to runtime errors in real applications.  
3. **Misusing built‑ins** – Using `String.matches()` or regex to count letters is overkill and slower.

---

#### 6️⃣ Complete Code Samples

*(See section 2 for full Java, Python, and C++ code)*

---

#### 7️⃣ Test Your Solution

```bash
# Java
javac Solution.java
java Solution

# Python
python solution.py

# C++
g++ -std=c++17 solution.cpp -o solution
./solution
```

Make sure you get:

```
33
0
```

---

#### 8️⃣ Takeaway for Your Next Interview

- **Keep it simple:** A linear scan with integer math solves most “percentage” questions.  
- **Speak the language:** Show you can write idiomatic code in Java, Python, or C++.  
- **Show awareness of edge cases:** Even if the constraints seem trivial, mention why you handle them.

---

#### 9️⃣ Final Words

LeetCode 2278 is a **classic “easy” problem that packs a punch**. Mastering it demonstrates a strong foundation in string manipulation, algorithmic thinking, and clean coding—skills that recruiters prize. With the code snippets and pitfalls covered above, you’re ready to ace this question and impress interviewers. Good luck on your coding journey and landing that dream software engineering role!

---

#### 📌 SEO Keywords

- LeetCode 2278  
- Percentage of Letter in String  
- Java string counting  
- Python string percentage  
- C++ character frequency  
- Coding interview prep  
- Software engineering interview  
- Algorithmic problem solving  
- Job interview tips  

Feel free to copy, adapt, and publish this blog post on your personal website or Medium. The clear structure, detailed explanations, and SEO‑friendly language will help you attract recruiters searching for “LeetCode solutions” and “coding interview tips.”