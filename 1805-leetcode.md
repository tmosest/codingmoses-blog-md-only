---
title: LeetCode 1805. Number of Different Integers in a String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # LeetCode 1805 – “Number of Different Integers in a String”

> **Goal** – Given a mixed string of lowercase letters and digits, replace every non‑digit with a space, split the string into integer tokens, strip leading zeros, and count how many *unique* integers exist.  
> **Typical interview scenario** – A “string‑parsing” question that tests regular‑expression usage, set semantics, and edge‑case handling.  
> **Why it matters for recruiters** – It forces you to think about **time‑space trade‑offs**, handling of big integers, and clean code style – all the traits recruiters look for in a backend / full‑stack engineer.

Below you’ll find a fully‑commented, production‑ready solution in **Java, Python, and C++** (C++17).  
After the code, I’ll give you an SEO‑friendly blog post that talks about the *good*, *bad*, and *ugly* aspects of this problem and why mastering it can help you land your next job.

---

## 📚 Problem Summary

```
Input:  word – a string (1 ≤ |word| ≤ 1000) consisting of [0‑9] and [a‑z]
Output: The number of distinct integers after removing non‑digits
        and discarding leading zeros.
```

*Example*

```
word = "a123bc34d8ef34"  →  tokens: 123, 34, 8, 34
Distinct integers: {123, 34, 8} → answer = 3
```

*Edge cases*

* “0000” → integer 0
* “01” and “1” are the same
* Very long integers that don’t fit into 64‑bit types

---

## ✅ High‑level Approach

1. **Scan the string once** – keep a pointer `i`.  
2. When you see a digit, accumulate all consecutive digits into a `StringBuilder` (or `string`).  
3. Strip leading zeros (`replaceFirst("^0+", "")`).  
4. If the resulting string is empty → the integer is `0`.  
5. Store the canonical form in a `HashSet` / `unordered_set` / `set`.  
6. Return the set’s size.

*Why this works*:  
* Each number is processed in O(length) time.  
* Stripping zeros ensures canonical form; e.g., “001” → “1”.  
* Using a set guarantees uniqueness in O(1) amortized (hash‑based) or O(log n) (tree‑based).

---

## ⏱️ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Scanning `word` | **O(n)** (n = |word|) | |
| Adding to set | **O(1)** amortized (Java/C++ unordered_set) | **O(k)** (k = number of distinct integers) |
| Overall | **O(n)** | **O(k)** |

`n` ≤ 1000, so the algorithm is easily within limits.  

---

## 🧑‍💻 Code Solutions

> **Tip** – Use a `BigInteger` (Java), `int` (Python’s arbitrary‑precision `int`), or `__int128`/`std::string` in C++ when the number can be > 64‑bit.  
> The code below sticks to the standard library only (no third‑party libs).

### 1. Java (Java 17)

```java
import java.math.BigInteger;
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int numDifferentIntegers(String word) {
        Set<String> seen = new HashSet<>();          // store canonical string representation
        int i = 0;
        int n = word.length();

        while (i < n) {
            // skip non‑digits
            if (!Character.isDigit(word.charAt(i))) {
                i++;
                continue;
            }

            // accumulate digits
            int start = i;
            while (i < n && Character.isDigit(word.charAt(i))) i++;
            String num = word.substring(start, i);

            // strip leading zeros
            String canonical = num.replaceFirst("^0+", "");
            if (canonical.isEmpty()) canonical = "0"; // all zeros

            seen.add(canonical);
        }
        return seen.size();
    }
}
```

**Why a `String` set?**  
Java’s `BigInteger` is great but creates a heavy object per number.  
Storing the *canonical string* keeps memory low and avoids parsing overhead – sufficient for `n ≤ 1000`.

---

### 2. Python 3 (Python 3.10+)

```python
class Solution:
    def numDifferentIntegers(self, word: str) -> int:
        seen = set()
        i = 0
        n = len(word)

        while i < n:
            if not word[i].isdigit():
                i += 1
                continue

            start = i
            while i < n and word[i].isdigit():
                i += 1

            num = word[start:i].lstrip('0')
            if num == '':
                num = '0'
            seen.add(num)

        return len(seen)
```

**Pythonic note** – `int` is arbitrary‑precision, so you could convert `num` to an `int` if you prefer numeric uniqueness, but string canonical form is simpler.

---

### 3. C++17 (C++ 17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int numDifferentIntegers(string word) {
        unordered_set<string> seen;
        int i = 0, n = word.size();

        while (i < n) {
            if (!isdigit(word[i])) { ++i; continue; }

            int start = i;
            while (i < n && isdigit(word[i])) ++i;
            string num = word.substr(start, i - start);

            // strip leading zeros
            size_t pos = num.find_first_not_of('0');
            string canonical = (pos == string::npos) ? "0" : num.substr(pos);

            seen.insert(canonical);
        }
        return seen.size();
    }
};
```

**Why `unordered_set<string>`?**  
The same rationale as Java: canonical string saves memory and sidesteps the need for `boost::multiprecision` or `__int128`.

---

## 📑 Blog Article: “The Good, The Bad, & The Ugly of LeetCode 1805”

> *SEO keywords: LeetCode 1805, Number of Different Integers in a String, Java solution, Python solution, C++ solution, coding interview, job interview, algorithm analysis, string parsing.*

---

### 1️⃣ Introduction

When recruiters scour your résumé, they often look for **pattern‑recognition** and **set‑theory** problems—two core pillars of clean, production‑ready code.  
LeetCode problem **1805 – “Number of Different Integers in a String”** is one such jewel.  
It forces you to:

* Convert a messy string into meaningful tokens  
* Deal with edge cases that might seem trivial at first glance  
* Decide on the best data structure for uniqueness  
* Discuss performance trade‑offs

Mastering 1805 shows you can handle real‑world data parsing with confidence – a skill that resonates in backend, data‑engineering, and full‑stack roles.

---

### 2️⃣ The Good

| ✅ Feature | Why It’s a Strength |
|-----------|---------------------|
| **Linear Scan** | Only **O(n)** time; recruiters love linear‑time solutions for small input sizes. |
| **Canonical String** | Avoids numeric overflows; storing `"0"` vs `"0000"` is trivial. |
| **Set Semantics** | Guarantees uniqueness in constant time; highlights knowledge of hash tables. |
| **Cross‑Language Portability** | Solutions in Java, Python, and C++ are short, clean, and easy to explain on a whiteboard. |
| **Clear Edge‑Case Handling** | Demonstrates defensive coding (all zeros → 0). |

These “good” qualities make 1805 a **stand‑alone interview piece**: a perfect “talk‑about‑your‑experience” example in your next coding round.

---

### 3️⃣ The Bad

| ❌ Problem | Impact | Mitigation |
|------------|--------|------------|
| **Potential Integer Overflow** | Very long tokens (e.g., 100 digits) can exceed 64‑bit limits. | Use string canonicalization or big‑integer libraries (Java `BigInteger`, Python’s `int`, C++ `boost::multiprecision`). |
| **Memory Overhead of BigInteger** | Creating a `BigInteger` for each number can be heavy in Java. | Store the *canonical string* instead; parse only if numeric comparison is required. |
| **Performance of Regex** | Some people solve it with a regex like `re.findall(r'\d+', word)` then `int(s)`. | While fast for small inputs, regex can obscure logic and makes it harder to explain the algorithmic trade‑offs. |

Recruiters appreciate candidates who can spot these pitfalls and explain why a simple, clear approach is often preferable.

---

### 4️⃣ The Ugly

| 😱 Ugly Pattern | What It Looks Like | Why It’s Ugly |
|-----------------|--------------------|---------------|
| **Over‑Complicated Regex** | `re.sub(r'[^0-9]', ' ', word).split()` followed by a loop that strips zeros. | The regex hides the *O(n)* scan and makes it hard to see that the algorithm is linear. |
| **Wrong Canonical Form** | Adding `"0"` to the set directly instead of stripping leading zeros → duplicates (`"01"`, `"1"`). | Leads to wrong answers; shows a lack of attention to detail. |
| **Unnecessary BigInteger Conversions** | Converting every token to `BigInteger` even when it fits in 32‑bit. | Adds unnecessary CPU cycles and memory pressure. |

These “ugly” patterns are common traps for candidates who rush to use built‑in functions without understanding the underlying semantics.

---

### 5️⃣ Why Solving 1805 Helps Your Job Hunt

1. **String Manipulation Is Everywhere** – From log parsers to URL routing, parsing mixed data is a daily task.  
2. **Set Usage Demonstrates Data‑Structure Awareness** – Recruiters love to see that you know when a hash table vs. tree set is appropriate.  
3. **Edge‑Case Thinking Shows Maturity** – Handling “0000” vs. “0” is a real‑world problem in data cleaning pipelines.  
4. **Cross‑Language Confidence** – Being able to produce clean, idiomatic code in Java, Python, and C++ shows you can work in any stack.  
5. **Performance‑First Mindset** – You’ll impress interviewers by explaining that the solution is O(n) and O(k) and why that matters for production systems.

---

### 6️⃣ Final Takeaway

LeetCode 1805 is *not* a brain‑teaser; it’s a **code‑craft** exercise that reveals your pragmatic coding style.  
By mastering the **canonical‑string + set** pattern you’ll:

* Avoid overflow pitfalls  
* Keep memory usage low  
* Deliver clean, testable code  
* Be ready to discuss *why* you chose one data‑structure over another in an interview

Add the Java, Python, and C++ snippets above to your GitHub portfolio, and let recruiters see the *good*, *bad*, and *ugly* parts of this problem solved with elegance. Good luck on your next coding interview – you’re one step closer to landing that dream job! 🚀