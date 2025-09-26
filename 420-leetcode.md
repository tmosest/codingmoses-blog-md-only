---
title: LeetCode 420. Strong Password Checker - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Mastering Leetcode 420 – Strong Password Checker  
> **“The good, the bad, and the ugly”** of solving a hard interview problem

---

## TL;DR

- **Problem**: Minimum steps to make a password strong (length 6‑20, 1 lower, 1 upper, 1 digit, no 3‑in‑a‑row).
- **Solution idea**:  
  1. Count missing character types.  
  2. Find repeating sequences (`len >= 3`) and note how many replacements they need.  
  3. Treat **length** as the main constraint:  
     - If too short → insertions dominate.  
     - If too long → deletions help reduce repeats.  
  4. Merge the three counts optimally.
- **Result**: O(n) time, O(1) extra space.

Below you’ll find **complete implementations** in **Java, Python, and C++**, followed by a **blog‑ready article** that explains the logic, discusses pitfalls, and shows how to nail the interview.

---

## 1. Java Implementation

```java
class Solution {
    public int strongPasswordChecker(String password) {
        int n = password.length();
        boolean hasLower = false, hasUpper = false, hasDigit = false;

        // 1. Identify missing character types
        for (int i = 0; i < n; i++) {
            char c = password.charAt(i);
            if (Character.isLowerCase(c)) hasLower = true;
            else if (Character.isUpperCase(c)) hasUpper = true;
            else if (Character.isDigit(c)) hasDigit = true;
        }
        int missingTypes = (hasLower ? 0 : 1) + (hasUpper ? 0 : 1) + (hasDigit ? 0 : 1);

        // 2. Find repeating sequences and how many replacements they need
        int replace = 0;               // total replacements needed for repeats
        int[] len = new int[n / 3 + 1]; // counts of repeats mod 3 (0,1,2)
        for (int i = 0, count = 1; i < n; i++) {
            if (i + 1 < n && password.charAt(i) == password.charAt(i + 1)) {
                count++;
            } else {
                if (count >= 3) {
                    replace += count / 3;
                    len[count % 3]++;
                }
                count = 1;
            }
        }

        if (n < 6) {
            // Too short – insertions dominate
            return Math.max(missingTypes, 6 - n);
        } else if (n <= 20) {
            // Length OK – only replacements needed
            return Math.max(missingTypes, replace);
        } else {
            // Too long – deletions help reduce replacements
            int over = n - 20;
            // Use deletions on repeats with mod 0 first
            int del = Math.min(over, len[0]);
            replace -= del;
            over -= del;
            // Then on mod 1 repeats
            del = Math.min(over, len[1] * 2) / 2;
            replace -= del;
            over -= del * 2;
            // Finally on mod 2 repeats
            del = Math.min(over, len[2] * 3) / 3;
            replace -= del;
            over -= del * 3;
            // After all deletions
            return (n - 20) + Math.max(missingTypes, replace);
        }
    }
}
```

> **Key points**  
> * `len[0]`, `len[1]`, `len[2]` store how many repeats give a remainder of 0, 1, 2 when divided by 3.  
> * Deleting one character from a repeat of length `k` reduces the number of required replacements by `⌊k/3⌋`.  
> * Deleting wisely (first those with remainder 0, then 1, then 2) gives the most bang for each deletion.

---

## 2. Python Implementation

```python
class Solution:
    def strongPasswordChecker(self, password: str) -> int:
        n = len(password)
        has_lower = has_upper = has_digit = False

        # 1. Check missing types
        for ch in password:
            if ch.islower(): has_lower = True
            elif ch.isupper(): has_upper = True
            elif ch.isdigit(): has_digit = True

        missing = (not has_lower) + (not has_upper) + (not has_digit)

        # 2. Scan repeats
        replace = 0
        mod_cnt = [0, 0, 0]          # how many repeats give k%3==0,1,2
        i = 0
        while i < n:
            j = i
            while j < n and password[j] == password[i]:
                j += 1
            l = j - i
            if l >= 3:
                replace += l // 3
                mod_cnt[l % 3] += 1
            i = j

        if n < 6:
            return max(missing, 6 - n)

        if n <= 20:
            return max(missing, replace)

        # n > 20
        over = n - 20
        # Use deletions on repeats optimally
        for k in range(3):
            use = min(over, mod_cnt[k] * (k + 1))
            replace -= use // (k + 1)
            over -= use

        return (n - 20) + max(missing, replace)
```

> **Why the same logic?**  
> The Python version mirrors the Java one: we count missing types, scan repeats, then adjust for length.  
> Python’s brevity hides the same algorithmic steps.

---

## 3. C++ Implementation

```cpp
class Solution {
public:
    int strongPasswordChecker(string password) {
        int n = password.size();
        bool hasLower = false, hasUpper = false, hasDigit = false;

        // 1. Missing character types
        for (char c : password) {
            if (islower(c)) hasLower = true;
            else if (isupper(c)) hasUpper = true;
            else if (isdigit(c)) hasDigit = true;
        }
        int missing = (hasLower ? 0 : 1) + (hasUpper ? 0 : 1) + (hasDigit ? 0 : 1);

        // 2. Repeats
        int replace = 0;
        int modCnt[3] = {0, 0, 0};
        for (int i = 0; i < n;) {
            int j = i;
            while (j < n && password[j] == password[i]) ++j;
            int len = j - i;
            if (len >= 3) {
                replace += len / 3;
                modCnt[len % 3]++;
            }
            i = j;
        }

        if (n < 6) return max(missing, 6 - n);
        if (n <= 20) return max(missing, replace);

        // n > 20
        int over = n - 20;
        for (int k = 0; k < 3; ++k) {
            int use = min(over, modCnt[k] * (k + 1));
            replace -= use / (k + 1);
            over -= use;
        }
        return (n - 20) + max(missing, replace);
    }
};
```

---

## 4. Blog Article – “The Good, the Bad, and the Ugly of Leetcode 420”

> *Keywords*: Strong Password Checker, Leetcode 420, coding interview, password strength, algorithm interview, job interview

---

### 📌 Introduction  

When interviewers ask **“How many steps to make a password strong?”**, they’re not only testing your grasp of string manipulation but also your ability to **optimize** under multiple constraints. Leetcode 420 (“Strong Password Checker”) is notorious for being *hard* but *essential* for many software engineering roles. Below is a deep dive into the problem, a robust O(n) solution, and common pitfalls you should avoid.

---

### 1️⃣ The Problem in a Nutshell  

| Requirement | Detail |
|-------------|--------|
| Length | 6 ≤ `len(password)` ≤ 20 |
| Character types | at least 1 lowercase, 1 uppercase, 1 digit |
| Repetition | No three identical chars in a row |

**Goal:** Return the *minimum* number of **insert**, **delete**, or **replace** operations to satisfy all three.

---

### 2️⃣ The Core Insight – *“Three Moving Parts”*  

| Part | What it does | How it affects the answer |
|------|--------------|--------------------------|
| **Missing types** | Count of missing char categories | Each missing type forces at least one operation |
| **Repeats** | Sequences like `"aaa"` or `"bbbb"` | Each group of `k` identical chars needs `⌊k/3⌋` replacements |
| **Length** | `len < 6` → insertions; `len > 20` → deletions | Insertion can solve missing types & repeats; deletion reduces repeats |

Balancing these three parts yields the minimal total steps.

---

### 3️⃣ The Elegant O(n) Solution  

1. **Scan once**  
   - Track missing types.  
   - Detect runs of repeated characters; record `len / 3` and `len % 3`.  
2. **Case 1 – Short password (`len < 6`)**  
   - Insertion dominates.  
   - `steps = max(missing, 6 - len)`.  
3. **Case 2 – Acceptable length (`6 ≤ len ≤ 20`)**  
   - Only replacements needed.  
   - `steps = max(missing, totalReplacements)`.  
4. **Case 3 – Long password (`len > 20`)**  
   - **Delete** first to shrink length.  
   - Use deletions on repeats where they reduce the number of needed replacements most efficiently:  
     * Delete 1 char from a group where `len % 3 == 0` → reduce one replacement.  
     * Delete 2 chars from a group where `len % 3 == 1` → reduce one replacement.  
     * Delete 3 chars from a group where `len % 3 == 2` → reduce one replacement.  
   - After all deletions: `steps = deletions + max(missing, remainingReplacements)`.

The logic above is **exactly** what the Java/Python/C++ code snippets implement.

---

### 4️⃣ Good – What Makes This Solution Great  

| ✅ | Explanation |
|---|-------------|
| **Linear time** | One pass over the string → O(n) (n ≤ 50) |
| **Constant space** | Only a few counters (missing types, replace, modCnt[3]) |
| **Deterministic** | No backtracking or recursion – no hidden time‑outs |
| **Clear logic** | Each case handles a distinct length regime, making the algorithm easy to reason about |

---

### 5️⃣ Bad – Common Missteps to Avoid  

| ❌ | Mistake | Fix |
|---|---------|-----|
| *Counting replacements incorrectly* | `replace += length / 3` but forgetting to subtract after deletions | Track `len % 3` groups and adjust `replace` after each deletion |
| *Handling short passwords by only inserting* | Missing the fact that an insertion can simultaneously satisfy a missing type and break a repeat | `max(missing, 6 - len)` handles both |
| *Ignoring the order of deletions* | Deleting from a repeat of length 5 before one of length 4 may waste operations | Delete in order of `len % 3` (0 → 1 → 2) |
| *Using recursion or backtracking* | Exponential blow‑up on worst‑case strings | Prefer greedy, linear logic |

---

### 6️⃣ Ugly – Edge Cases That Trip Up Even Smart Coders  

| Edge | Why It’s Tricky | Quick Check |
|------|----------------|-------------|
| **All repeats & too long** (`"aaaaaaaaaaaaaaaaaaaaa"`) | You must delete to reduce length *and* fix repeats | Verify `replace` updates after deletions |
| **Missing type but no repeats** (`"aaaaaa"`) | Deleting won’t help; you need replacements | Ensure `max(missing, replace)` is applied |
| **Length exactly 20 but with many repeats** | Deletion isn’t allowed, only replacements | Replace every third character in repeats |
| **Length exactly 6 but still missing types** | No insertions allowed, but replacements might create missing types | Use replacements that change the character type |

---

### 7️⃣ How This Helps Your Interview Game  

- **Shows algorithmic thinking**: You’re balancing three constraints simultaneously.  
- **Demonstrates optimization**: Greedy deletions vs. replacements show you can reduce operations.  
- **Highlights edge‑case awareness**: Discussing bad/ugly cases shows thoroughness.  

> **Pro tip**: In an interview, start by explaining the high‑level strategy before diving into code. This signals clarity of thought and keeps the interviewer engaged.

---

### 8️⃣ SEO Boost – Make Your Blog Post Findable  

- **Title**: *Strong Password Checker – Leetcode 420 Explained (Java, Python, C++)*  
- **Meta Description**: *Master Leetcode 420 with step‑by‑step solutions in Java, Python, and C++. Learn the O(n) algorithm, common pitfalls, and interview tips.*  
- **Keywords**: “Strong Password Checker”, “Leetcode 420”, “Java password strength checker”, “Python password checker solution”, “C++ Leetcode 420”, “coding interview password”, “job interview algorithm”.

---

### 9️⃣ Wrap‑Up  

Leetcode 420 may look intimidating, but with a clear greedy strategy you can solve it efficiently and confidently. Remember: **balance missing types, repeats, and length**. The code is straightforward – just translate the logic into your language of choice.

If you nailed this problem, you’re ready to **show up in any software engineering interview** with confidence. Happy coding—and good luck landing that dream job! 🚀

---

*Feel free to bookmark this post, share it on your LinkedIn profile, and drop a comment with any other Leetcode challenges you’d like explained.*


---



That completes the **full answer**: code in three languages and a comprehensive blog article.