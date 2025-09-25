---
title: LeetCode 3461. Check If Digits Are Equal in String After Operations I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 3461 – “Check If Digits Are Equal in String After Operations I”

> **Problem**  
> You are given a string **s** of digits (length 3–100).  
> While the string has more than two digits, replace it with a new string formed by taking each adjacent pair `s[i]` + `s[i+1]`, mod 10.  
> Return **true** if the final two digits are identical, otherwise **false**.

> **Examples**  
> * `s = "3902"` → `true` (`11` → both digits are 1)  
> * `s = "34789"` → `false` (`48` → digits differ)

> **Goal** – Write clean, interview‑ready code in **Java, Python, C++** and accompany it with an SEO‑optimized blog post that explains the algorithm, its pros/cons, and why you’re a great fit for a software job.

---

## 📚 Why This Problem is a Gold‑Mine for Interviews

* **String manipulation** – common in coding interviews.  
* **Simulation** – teaches you how to translate a process into code.  
* **Edge‑case awareness** – the modulo operation can trip up newbies.  
* **Complexity discussion** – helps you articulate time/space trade‑offs.  

---

## 🏁 Quick Solution (Brute‑Force / Simulation)

The straightforward approach is to repeatedly reduce the string until its length is two.

```text
while len(s) > 2:
    new_s = ""
    for i in range(len(s)-1):
        new_digit = (int(s[i]) + int(s[i+1])) % 10
        new_s += str(new_digit)
    s = new_s
return s[0] == s[1]
```

* **Time Complexity** – `O(n²)` in the worst case (n = len(s)).  
  Each reduction shrinks the string by one, and each reduction scans the current string.  
* **Space Complexity** – `O(n)` for the temporary string built in each iteration.

For the given constraints (`n ≤ 100`) this is perfectly fine and keeps the code readable.

---

## 🧩 Algorithmic Insight – Why `O(n²)` Is Acceptable

Because every iteration reduces the length by one, the total number of digit additions is

```
(n-1) + (n-2) + … + 1 = n(n-1)/2  ≤ 4950  (for n = 100)
```

This is tiny – even a million operations would finish in milliseconds.  
If you were asked for an *optimized* solution, you could pre‑compute the cumulative sums modulo 10 in a single pass, but the simplicity of the simulation is a virtue in interviews.

---

## 🏎️ Implementation

Below are clean, interview‑ready implementations in **Java, Python, and C++**.

> **Tip** – Use `StringBuilder` / `list` / `std::string` to build the new string efficiently.

---

### Java

```java
public class Solution {
    public boolean hasSameDigits(String s) {
        while (s.length() > 2) {
            StringBuilder next = new StringBuilder();
            for (int i = 0; i < s.length() - 1; i++) {
                int a = s.charAt(i) - '0';
                int b = s.charAt(i + 1) - '0';
                next.append((a + b) % 10);
            }
            s = next.toString();
        }
        return s.charAt(0) == s.charAt(1);
    }
}
```

---

### Python

```python
class Solution:
    def hasSameDigits(self, s: str) -> bool:
        while len(s) > 2:
            nxt = []
            for i in range(len(s) - 1):
                nxt.append(str((int(s[i]) + int(s[i+1])) % 10))
            s = ''.join(nxt)
        return s[0] == s[1]
```

---

### C++

```cpp
class Solution {
public:
    bool hasSameDigits(string s) {
        while (s.length() > 2) {
            string nxt;
            nxt.reserve(s.length() - 1);
            for (size_t i = 0; i + 1 < s.size(); ++i) {
                int a = s[i] - '0';
                int b = s[i+1] - '0';
                nxt.push_back(char('0' + (a + b) % 10));
            }
            s = std::move(nxt);
        }
        return s[0] == s[1];
    }
};
```

---

## 🎯 What Makes This Solution “Good, Bad, and Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Readability** | Uses a single loop and a `StringBuilder`/`list`. | None. | None. |
| **Performance** | `O(n²)` is fine for `n ≤ 100`. | If `n` were 10⁵, you'd need an O(n) solution. | None. |
| **Edge‑cases** | Handles strings with repeated digits, zeros, etc. | None. | None. |
| **Memory** | Linear temporary storage. | None. | None. |
| **Maintainability** | Clear variable names. | None. | None. |

> **Why no “Ugly” part?**  
> In this case, the naive simulation is already elegant enough. If we wanted a *theoretical* O(n) solution, we’d need a more complex approach (prefix sums + modulo arithmetic), which could be overkill and harder to explain quickly in an interview.

---

## 💡 Interview‑Ready Tips

1. **State the approach up front.**  
   *“I’ll iterate, shrinking the string until two digits remain, performing the modulo operation at each step.”*

2. **Mention complexity.**  
   *“Time: O(n²), space: O(n). For `n ≤ 100`, this runs instantly.”*

3. **Show you can think beyond brute‑force.**  
   *“If the input were huge, we could pre‑compute cumulative sums modulo 10 and reduce in O(n).”*

4. **Ask clarifying questions.**  
   *“Do we need to handle non‑digit characters? Is leading zero acceptable?”*  
   Interviewers appreciate that you consider edge cases.

---

## 📈 SEO‑Optimized Blog Post

Below is a ready‑to‑publish article you can drop on your personal blog, Medium, or LinkedIn. It’s rich with keywords that recruiters searching for “LeetCode solutions” or “coding interview Java” will love.

```markdown
# LeetCode 3461 – “Check If Digits Are Equal in String After Operations I”  
A Deep‑Dive into Simulation, Complexity, and Interview‑Ready Code

---

## Problem Overview

LeetCode 3461 asks you to reduce a string of digits by repeatedly summing adjacent characters modulo 10 and to determine whether the last two digits are equal. It’s a textbook example of **string manipulation** and **simulation** that appears frequently in **coding interviews**.

---

## Why This Problem Matters for Your Coding Portfolio

- **Java, Python, C++** solutions that showcase clear string handling.  
- Demonstrates your ability to simulate a process step‑by‑step.  
- Perfect for a **coding interview** where recruiters look for **clean, efficient code**.

---

## Step‑by‑Step Solution

The most interview‑friendly approach is a **simulation**:

1. **While the string has more than two digits…**  
2. Compute each adjacent pair sum, mod 10.  
3. Build the next string and repeat.

The code below is your go‑to answer for **Java, Python, and C++**.

---

### Java

```java
public class Solution {
    public boolean hasSameDigits(String s) {
        while (s.length() > 2) {
            StringBuilder next = new StringBuilder();
            for (int i = 0; i < s.length() - 1; i++) {
                int a = s.charAt(i) - '0';
                int b = s.charAt(i + 1) - '0';
                next.append((a + b) % 10);
            }
            s = next.toString();
        }
        return s.charAt(0) == s.charAt(1);
    }
}
```

### Python

```python
class Solution:
    def hasSameDigits(self, s: str) -> bool:
        while len(s) > 2:
            nxt = []
            for i in range(len(s) - 1):
                nxt.append(str((int(s[i]) + int(s[i+1])) % 10))
            s = ''.join(nxt)
        return s[0] == s[1]
```

### C++

```cpp
class Solution {
public:
    bool hasSameDigits(string s) {
        while (s.length() > 2) {
            string nxt;
            nxt.reserve(s.length() - 1);
            for (size_t i = 0; i + 1 < s.size(); ++i) {
                int a = s[i] - '0';
                int b = s[i+1] - '0';
                nxt.push_back(char('0' + (a + b) % 10));
            }
            s = std::move(nxt);
        }
        return s[0] == s[1];
    }
};
```

---

## 📊 Complexity Breakdown

- **Time:** `O(n²)` – perfect for `n ≤ 100`.  
- **Space:** `O(n)` – temporary storage for the next string.

> **If scaling mattered:** Pre‑computing prefix sums modulo 10 would bring it down to `O(n)`.

---

## ⚙️ What to Discuss in an Interview

1. **Approach** – simulation until length = 2.  
2. **Complexity** – why it’s acceptable for the problem constraints.  
3. **Optimizations** – prefix sums for a theoretical `O(n)` solution.  
4. **Edge Cases** – zeros, repeated digits, leading zeros, etc.

---

## 🎯 Why I’m the Right Fit for Your Team

- **Hands‑on experience** solving LeetCode problems in Java, Python, and C++.  
- Ability to explain algorithmic trade‑offs in **plain language**.  
- Proven track record of turning **simulation** into clean, production‑ready code.  
- Passion for **continuous learning** – always exploring better data‑structures and patterns.

If you’re hiring for **software development, backend engineering, or algorithmic roles**, I’m eager to bring this mindset and skill set to your team.

---

### 📣 Call to Action

> Want to see more LeetCode solutions in Java, Python, and C++?  
> Subscribe to my newsletter or connect with me on LinkedIn. Let’s code something amazing together!

---

## 🎉 Wrap‑Up

- **Problem** – Clear restatement.  
- **Solution** – Simple simulation with solid complexity.  
- **Code** – Clean, language‑specific implementations.  
- **Blog** – SEO‑rich article ready for publication.  

You now have everything you need to nail the LeetCode interview, impress recruiters, and showcase your coding chops across Java, Python, and C++! 🚀