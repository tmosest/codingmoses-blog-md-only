---
title: LeetCode 3602. Hexadecimal and Hexatrigesimal Conversion - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 3602 – Hexadecimal & Hexatrigesimal Conversion  
**Java | Python | C++ | Blog**  

> *“The good, the bad, and the ugly of this seemingly trivial conversion problem – and why mastering it will land you that next‑level interview.”*  

---

### 📌 Problem Overview  

| Field | Value |
|-------|-------|
| **ID** | 3602 |
| **Difficulty** | Easy |
| **Tags** | Math, String, Base‑Conversion |
| **Constraints** | `1 ≤ n ≤ 1000` |
| **Goal** | For a given integer `n`, return the concatenation of:  
&nbsp;&nbsp;• the hexadecimal representation of `n²`  
&nbsp;&nbsp;• the hexatrigesimal (base‑36) representation of `n³` |

**Example**  
```
n = 13
n² = 169 → "A9" (hex)
n³ = 2197 → "1P1" (base‑36)
Result = "A91P1"
```

---

## 📚 Why This Problem is a Job‑Interview Gem  

| Aspect | Why It Matters |
|--------|----------------|
| **Base conversions** | Interviewers love to see if you can manipulate numbers at a low level. |
| **String manipulation** | Shows proficiency with language‑specific string helpers. |
| **Edge‑case handling** | Small constraints but you still need to guard against negative numbers, zero, or large bases. |
| **Time/space trade‑offs** | A good solution is O(log n) time, O(log n) space – perfect for interview buzz. |

---

## 🔍 Solution Breakdown  

1. **Compute powers**  
   - `sq = n * n`  
   - `cube = n * n * n`  

2. **Convert to required bases**  
   - **Hexadecimal (base‑16)**: built‑in formatting → `format(sq, 'X')` in Python, `Integer.toHexString(sq).toUpperCase()` in Java, `std::stringstream` or `std::to_chars` in C++.  
   - **Hexatrigesimal (base‑36)**: also a built‑in → `format(cube, '36').upper()` in Python, `Integer.toString(cube, 36).toUpperCase()` in Java, `std::to_chars` with radix 36 in C++.

3. **Concatenate**  
   - Return `hex_part + base36_part`.

4. **Edge‑case**  
   - For `n = 1`, `sq = 1`, `cube = 1` → result `"11"`.  
   - No special handling needed for `n = 0` because `n ≥ 1`.

---

## 🛠️ Code – 3 Languages  

### Python 3

```python
class Solution:
    def concatHex36(self, n: int) -> str:
        # 1️⃣ Powers
        sq = n * n
        cube = n * n * n

        # 2️⃣ Base conversions
        hex_part = format(sq, 'X')          # uppercase hex
        base36_part = format(cube, '36').upper()  # base-36

        # 3️⃣ Concatenate
        return hex_part + base36_part
```

**Complexity**

| Metric | Value |
|--------|-------|
| Time   | O(log n) |
| Space  | O(log n) |

---

### Java

```java
public class Solution {
    public String concatHex36(int n) {
        // 1️⃣ Powers
        int sq = n * n;
        int cube = n * n * n;

        // 2️⃣ Base conversions
        String hexPart = Integer.toHexString(sq).toUpperCase();
        String base36Part = Integer.toString(cube, 36).toUpperCase();

        // 3️⃣ Concatenate
        return hexPart + base36Part;
    }
}
```

**Complexity**

| Metric | Value |
|--------|-------|
| Time   | O(log n) |
| Space  | O(log n) |

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string concatHex36(int n) {
        long long sq   = 1LL * n * n;
        long long cube = 1LL * n * n * n;

        // Helper to convert a number to any base (2‑36)
        auto toBase = [](long long num, int base) -> string {
            const string digits = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
            if (num == 0) return "0";
            string res;
            while (num > 0) {
                res.push_back(digits[num % base]);
                num /= base;
            }
            reverse(res.begin(), res.end());
            return res;
        };

        string hexPart   = toBase(sq, 16);
        string base36Part = toBase(cube, 36);
        return hexPart + base36Part;
    }
};
```

**Complexity**

| Metric | Value |
|--------|-------|
| Time   | O(log n) |
| Space  | O(log n) |

---

## 📈 SEO‑Optimized Blog Article  

> *Title:* **LeetCode 3602 – Master Hexadecimal & Hexatrigesimal Conversion in Java, Python, and C++**  
> *Meta Description:* Solve LeetCode 3602 in seconds. Get Java, Python, and C++ solutions, understand the algorithm, and learn interview‑ready tips.  

---

### 1️⃣ What Is Hexatrigesimal?  

- Base‑36 (0–9, A–Z) is often used for compact identifiers.  
- In this problem, you must convert `n³` to base‑36.  
- The same logic works for any base up to 36, a useful trick for many interview puzzles.

---

### 2️⃣ The “Good” – Clean, Idiomatic Code  

- **Built‑ins**: Use language helpers (`Integer.toString(..., 36)` in Java, `format(..., '36')` in Python).  
- **Single‑pass**: No extra loops or recursion.  
- **Readability**: Variable names (`sq`, `cube`, `hexPart`, `base36Part`) are self‑explanatory.

---

### 3️⃣ The “Bad” – Things to Avoid  

- **Manual loops for base‑16**: Re‑implementing hex conversion when the standard library already exists.  
- **Integer overflow**: Using `int` for `n³` (e.g., in C++ if you use `int` instead of `long long`).  
- **Ignoring edge cases**: Forgetting `n = 1` returns `"11"` or mishandling zero.

---

### 4️⃣ The “Ugly” – A Common Pitfall  

> **Mis‑using the base‑conversion function** – e.g., `Integer.toString(n, 36)` returns a **lower‑case** string. Forgetting to call `.toUpperCase()` makes the output `"1p1"` instead of `"1P1"`.  
>  
> **Solution**: Always enforce uppercase in the final string or document that your conversion function returns upper‑case.

---

### 5️⃣ Interview‑Ready Tips  

1. **Show the Math**  
   - Quickly explain `n²` and `n³`.  
   - Mention that the number of digits grows logarithmically: `digits = ⌈log_base(value)⌉`.

2. **Time & Space**  
   - Emphasize O(log n) time and space – perfect for large `n`.

3. **Language‑Specific Tricks**  
   - Python: `format(num, 'X')` → uppercase hex; `format(num, '36').upper()` → base‑36.  
   - Java: `Integer.toHexString` + `toUpperCase`; `Integer.toString(num, 36).toUpperCase()`.  
   - C++: Custom base conversion or `std::to_chars` (C++17+) with radix support.

4. **Edge‑Case Demonstration**  
   - Run through `n = 1`, `n = 36`, `n = 1000` in your mind. Show that the solution handles them correctly.

---

### 6️⃣ Why Mastering This Problem Helps You Land a Job  

- **Demonstrates algorithmic thinking**: You’re converting numbers, not just brute‑forcing strings.  
- **Highlights language knowledge**: Knowing built‑in conversion functions and how to use them is a strong signal to recruiters.  
- **Shows attention to detail**: Converting bases correctly and managing case sensitivity matters in real code.  
- **Easy to explain**: It fits in the “10‑minute coding question” slot for many interviews.  

---

## 🎯 Wrap‑Up  

LeetCode 3602 is a *quick win* for interview prep. With a one‑liner solution in each of Java, Python, and C++, you can demonstrate:

- **Strong fundamentals** (math, strings, bases)  
- **Language fluency** (built‑ins, edge‑case handling)  
- **Efficiency** (O(log n) time/space)

Now you can confidently tackle this problem and turn it into a talking point during your next technical interview. Happy coding! 🚀

--- 

**Happy Interviewing!**