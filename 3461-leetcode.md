---
title: LeetCode 3461. Check If Digits Are Equal in String After Operations I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 3461 – “Check If Digits Are Equal in String After Operations I”
**Solve it in Java, Python, and C++**  
> A detailed walk‑through, an SEO‑friendly blog post that explains the **good**, the **bad**, and the **ugly** of this problem – perfect for your next coding interview or résumé.

---

### 1. Problem Recap

You’re given a numeric string `s` (`3 ≤ s.length ≤ 100`).  
Repeat the following until `s` has exactly two digits:

```
for every adjacent pair (s[i], s[i+1])
    newDigit = (int(s[i]) + int(s[i+1])) % 10
append newDigit to the next string
```

Return **true** if the final two digits are identical, otherwise **false**.

> Example  
> `s = "3902"`  
> `3902 → 292 → 11` → **true**

---

### 2. Brute‑Force Strategy (Simulation)

The most natural way is to literally simulate the process.

```text
while len(s) > 2:
    new_s = ""
    for i in range(len(s)-1):
        new_s += str((int(s[i]) + int(s[i+1])) % 10)
    s = new_s
return s[0] == s[1]
```

*Time*: `O(n²)` – because each iteration shrinks the string by 1, so we do ~n/2 + n/3 + … ≈ n²/2 operations.  
*Space*: `O(n)` – we build a fresh string each time.

This is fast enough for the given constraints, but let’s dig into the **good**, **bad**, and **ugly** aspects.

---

### 3. The Good

| Aspect | Why it’s good |
|--------|---------------|
| **Simplicity** | The simulation directly models the problem statement. No hidden tricks or math. |
| **Readability** | Anyone can follow the logic in 5 lines. |
| **Maintainability** | If the operation changes (e.g., `% 9` instead of `% 10`), you only adjust one line. |
| **Language‑agnostic** | The same algorithm works in Java, Python, C++, JavaScript, etc. |

---

### 4. The Bad

| Issue | Consequence | Fix |
|-------|-------------|-----|
| **Redundant conversion** (`char -> int`) | Minor performance hit. | Use ASCII arithmetic (`c - '0'`). |
| **Unnecessary String concatenation** | Creates many intermediate `StringBuilder`/`String` objects. | Use a `StringBuilder` (Java), list comprehension (Python), or `std::string` with `reserve` (C++). |
| **O(n²) time** | For the worst case (100 digits) it’s still fine, but not optimal. | Explore mathematical reduction (see below). |

---

### 5. The Ugly

* **Recursive solutions** that build new strings for each call can hit recursion depth limits in Python (`RuntimeError: maximum recursion depth exceeded`) and stack overflows in C++.
* **In‑place mutation** of the string while iterating can produce logic bugs if indices shift.
* **HashSet approach** (checking unique digits first) may give a wrong answer for strings like `"1110"` (unique digits are 1 and 0, but after reduction they become equal).

---

### 6. Possible Optimisation (not required but cool)

If you notice the operation is linear and each step only depends on adjacent digits, you can pre‑compute the coefficient for each original digit using dynamic programming.  
However, the naive simulation is already **< 0.1 ms** on modern hardware for 100 digits, so we’ll stick with it.

---

## 7. Full Code Implementations

Below are clean, beginner‑friendly versions for **Java**, **Python**, and **C++**.  
All three use a `StringBuilder` / list / `std::string` to avoid the quadratic cost of repeated concatenation.

---

### 7.1 Java (Java 17)

```java
// File: Solution.java
public class Solution {
    public boolean hasSameDigits(String s) {
        // While more than two digits remain
        while (s.length() > 2) {
            // Build next string in one pass
            StringBuilder next = new StringBuilder(s.length() - 1);
            for (int i = 0; i < s.length() - 1; i++) {
                int a = s.charAt(i) - '0';
                int b = s.charAt(i + 1) - '0';
                next.append((a + b) % 10);
            }
            s = next.toString();          // Prepare for next iteration
        }
        // Final two digits
        return s.charAt(0) == s.charAt(1);
    }

    // Simple test harness
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.hasSameDigits("3902"));   // true
        System.out.println(sol.hasSameDigits("34789"));  // false
    }
}
```

---

### 7.2 Python (Python 3.11)

```python
# File: solution.py
def has_same_digits(s: str) -> bool:
    # Reduce until length is 2
    while len(s) > 2:
        # Build next string with list comprehension (fast)
        s = ''.join(str((int(s[i]) + int(s[i+1])) % 10) for i in range(len(s)-1))
    return s[0] == s[1]


# Demo
if __name__ == "__main__":
    print(has_same_digits("3902"))   # True
    print(has_same_digits("34789"))  # False
```

---

### 7.3 C++ (C++17)

```cpp
// File: solution.cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool hasSameDigits(string s) {
        while (s.size() > 2) {
            string next;
            next.reserve(s.size() - 1);
            for (size_t i = 0; i + 1 < s.size(); ++i) {
                int a = s[i] - '0';
                int b = s[i+1] - '0';
                next.push_back('0' + (a + b) % 10);
            }
            s.swap(next);               // Reuse memory, no allocation
        }
        return s[0] == s[1];
    }
};

int main() {
    Solution sol;
    cout << boolalpha;               // print true/false instead of 1/0
    cout << sol.hasSameDigits("3902") << '\n';   // true
    cout << sol.hasSameDigits("34789") << '\n';  // false
}
```

---

## 8. Complexity Analysis

| Language | Time | Space |
|----------|------|-------|
| **Java** | `O(n²)` (≈ 5 000 operations for `n = 100`) | `O(n)` |
| **Python** | `O(n²)` | `O(n)` |
| **C++** | `O(n²)` | `O(n)` |

> **Why it still passes:**  
> The loop length shrinks by 1 each pass, so the total number of operations is about `n(n‑1)/2`.  
> With `n ≤ 100`, this is far below the time budget of LeetCode’s test suite.

---

## 9. SEO‑Friendly Blog Post

> **Title**: *“LeetCode 3461 – String Reduction Algorithm (Java, Python, C++) – Interview‑Ready Explanation”*  
> **Meta Description**: *“Learn how to solve LeetCode 3461 with clear Java, Python, and C++ code. Understand the algorithm, complexities, and interview tips. Prepare for your next coding interview.”*

### 9.1 Introduction

```markdown
### What’s Inside This Post?
- Problem breakdown
- Simulation approach & why it’s a great interview staple
- “Good/Bad/Ugly” critique – real‑world pitfalls
- Three production‑ready code snippets (Java, Python, C++)
- Interview‑style explanation you can drop into your résumé
- SEO‑heavy keywords to boost your personal brand
```

### 9.2 Problem Summary

> **Check If Digits Are Equal in String After Operations I** – LeetCode #3461  
> Reduce a numeric string pairwise with modulo 10 and compare the last two digits.

### 9.3 Why It Matters

- **String manipulation** is a staple in coding interviews.
- **Modulo arithmetic** showcases your mathematical mindset.
- The problem tests **simulation** skills, which are frequent in real‑world bug hunting.

### 9.4 The Brute‑Force Solution (Simulation)

> The core idea: build the next string in a single linear scan, repeat until two digits remain, then compare.

```python
while len(s) > 2:
    s = ''.join(str((int(s[i]) + int(s[i+1])) % 10) for i in range(len(s)-1))
return s[0] == s[1]
```

- **Time**: `O(n²)` – acceptable for `n ≤ 100`.  
- **Space**: `O(n)` – one auxiliary string per iteration.

### 9.5 Code for All Languages

(Insert Java, Python, C++ snippets from section 7)

### 9.6 Good / Bad / Ugly Recap

(Insert table from section 3–5)

### 9.7 Interview Tips

| Skill | How the Solution Shows It | What to Highlight |
|-------|---------------------------|-------------------|
| **Algorithmic Thinking** | Modeling the process exactly as described | “I first built a simulation to mirror the statement; it made edge‑case analysis trivial.” |
| **Time‑Space Trade‑offs** | Awareness of `O(n²)` vs. `O(n)` solutions | “I used `StringBuilder` to keep allocations linear.” |
| **Readability** | Clean code in all languages | “I chose the most straightforward approach so reviewers can focus on logic.” |
| **Adaptability** | Same algorithm works in any language | “I can port this to Go, Rust, or JavaScript if needed.” |

> *Tip*: During an interview, narrate the *good* and *bad* while you code. Interviewers love seeing you reason about trade‑offs.

### 9.8 Closing Thoughts

- **LeetCode 3461** is a great showcase problem: it forces you to think about **simulation**, **string manipulation**, and **modular arithmetic**.
- The **simulation** solution is fast, clear, and language‑agnostic.  
- Remember: **clean code > clever code** in most interview scenarios.

---

### 10. Call‑to‑Action

- **Add this problem to your portfolio** – it shows your ability to translate a problem statement into working code.
- **Share the solution on GitHub** – attach a `README.md` that walks through the algorithm (as we did here).
- **Practice the “good/bad/ugly” analysis** – interviewers love seeing you critique your own solutions.

---

### 📌 Keywords for SEO

```
LeetCode 3461, Check If Digits Are Equal, coding interview, Java solution, Python solution, C++ solution,
string manipulation, modulo operation, interview preparation, algorithmic problem,
job interview, technical interview, data structures, algorithm, programming interview, interview tips
```

---

Happy coding, and may this solution land you that next job offer! 🎯