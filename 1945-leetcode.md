---
title: LeetCode 1945. Sum of Digits of String After Convert - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Code – 3 Languages

Below you’ll find **compact, production‑ready** solutions for LeetCode 1945 in **Java, Python, and C++**.  
All three implementations follow the same idea:

1. Convert every letter to its alphabet index (`a=1 … z=26`).  
2. Concatenate those indices into a big decimal string.  
3. Replace the string by the sum of its digits, **k** times.

> **Note** – The first “digit‑sum” is *after* the conversion.  
> So we run the loop **k** times.

---

### 1.1 Java

```java
// Java 17
class Solution {
    public int getLucky(String s, int k) {
        // Build the big decimal string
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            sb.append(c - 'a' + 1);   // 1‑based alphabet position
        }

        String cur = sb.toString();

        // Repeat the digit‑sum k times
        for (int i = 0; i < k; i++) {
            int sum = 0;
            for (char d : cur.toCharArray()) {
                sum += d - '0';
            }
            cur = Integer.toString(sum);
        }
        return Integer.parseInt(cur);   // final answer
    }
}
```

---

### 1.2 Python

```python
# Python 3
class Solution:
    def getLucky(self, s: str, k: int) -> int:
        # Convert each char to its numeric value and concatenate
        num = ''.join(str(ord(c) - 96) for c in s)

        # k rounds of digit‑sum
        for _ in range(k):
            num = str(sum(int(d) for d in num))
        return int(num)
```

---

### 1.3 C++

```cpp
// C++17
class Solution {
public:
    int getLucky(string s, int k) {
        string num;
        for (char c : s)
            num += to_string(c - 'a' + 1);   // 1‑based index

        for (int i = 0; i < k; ++i) {
            int sum = 0;
            for (char d : num)
                sum += d - '0';
            num = to_string(sum);
        }
        return stoi(num);
    }
};
```

---

> **Why this works**  
> The string built in step 1 contains the decimal representation of the
> concatenated indices.  
> Summing its digits is exactly the operation described in the statement,
> and repeating it *k* times yields the required lucky number.

---

## 2. Blog Article  
**Title:** *LeetCode 1945 – Sum of Digits of String After Convert – A Job‑Interview‑Ready Guide (Java / Python / C++)*  

> **Meta‑Description**: Master LeetCode 1945 with clean, efficient solutions in Java, Python, and C++. Learn the algorithm, edge cases, and interview tips to impress hiring managers.

---

### 2.1 Problem Overview

LeetCode 1945 asks you to:

1. Convert a lowercase string `s` into a large integer by replacing each letter with its position in the alphabet (`a=1, …, z=26`).
2. Replace that integer with the sum of its digits.
3. Repeat the digit‑sum step **k** times.
4. Return the resulting integer.

*Examples*  
- `s="iiii", k=1` → 36  
- `s="leetcode", k=2` → 6  
- `s="zbax", k=2` → 8

The constraints (`|s| ≤ 100, k ≤ 10`) allow a straightforward simulation, but a clever observation can cut the code down to a few lines.

---

### 2.2 Key Observations (The “Good”)

- **Alphabet Mapping is trivial:** `c - 'a' + 1` yields 1‑based index in O(1).
- **The concatenated number can be huge (up to 200 digits),** but we *never need* to store it as an integer type—just as a string.
- **Summing digits of the concatenated string equals summing digits of each character’s numeric representation** (`1‑digit numbers keep their value, 2‑digit numbers sum to `tens + units`).  
  This lets you avoid building a long string if you want a micro‑optimised solution.
- **`k` is tiny (≤10),** so repeated digit‑sums are cheap.

---

### 2.3 Algorithm Walk‑through (The “Bad”)

1. **Build the concatenated string.**  
   `num = ''.join(str(ord(c) - 96) for c in s)` (Python)  
   or `num += to_string(c - 'a' + 1)` (C++/Java).

2. **Loop `k` times.**  
   In each iteration:
   - Compute `sum = sum(int(d) for d in num)`.
   - Replace `num` with `str(sum)`.

3. **Return the final integer.**  
   `return int(num)` / `return Integer.parseInt(num)` / `return stoi(num)`.

> **Why it might look wrong** – If you think “convert → transform” counts as one of the *k* transformations, you’ll run the loop one time too many and get wrong answers on hidden tests.

---

### 2.4 Edge Cases & “Ugly” Mistakes

| Edge Case | What Happens | Fix |
|-----------|--------------|-----|
| **Letter with two digits (`j–z`).** | `to_string(10)` → `"10"`. Digit sum = `1+0`. | Use `sumDigits(val)` logic to handle two‑digit values directly. |
| **k = 1.** | You should *only* perform the first digit‑sum after conversion. | Make sure the loop runs exactly `k` times, not `k‑1`. |
| **Very long `s` (100 chars).** | The concatenated string could be 200 digits long – still fine in a string, but beware of integer overflow if you use `long`. | Keep everything as `string` or use `int` for the intermediate sums. |
| **Leading zeros?** | The concatenation never produces leading zeros because alphabet indices are 1‑based. | No special handling needed. |

---

### 2.4 Complexity Analysis (The “Ugly”)

| Step | Time | Space |
|------|------|-------|
| Convert string → concatenated string | **O(|s|)** | **O(|s|)** (string length ≤ 200) |
| Repeated digit‑sums (`k` ≤ 10) | **O(k · len(num))** → at most **O(2000)** operations | **O(1)** (only the current sum) |

In practice, both **time and memory** are negligible for the given limits.

---

### 2.5 Implementation Highlights – 3 Languages

- **Java:** Uses `StringBuilder` for concatenation, a simple `for‑each` loop for the digit‑sum, and `Integer.parseInt()` for the final result.
- **Python:** List‑comprehensions and generator expressions keep the code short and readable.
- **C++:** `to_string` and `stoi` make the code as concise as the Java/Python versions.

All solutions are **fully commented** and **idiomatic** for their respective languages, which is a huge plus in a hiring interview.

---

### 2.6 Common Pitfalls (The “Ugly” Revisited)

| Mistake | Symptoms | Fix |
|---------|----------|-----|
| **Counting the conversion as a transformation.** | Wrong answer for `k=1`. | Remember the loop starts *after* the conversion. |
| **Using `long` to store the concatenated number.** | `OverflowError` in Python, `std::overflow` in C++/Java. | Keep it as a string or compute the digit sum of each value directly. |
| **Off‑by‑one in alphabet mapping (`c - 'a'`).** | Wrong answer for single‑digit letters. | Always add `+1` to get 1‑based index. |
| **Ignoring the 2‑digit case (`10–26`).** | Digit‑sum will be too large. | Sum the tens and units (`val / 10 + val % 10`). |

---

### 2.7 Interview Tips – How to Turn This into a Job

1. **Explain the idea first.**  
   “I’ll build the decimal representation, then sum the digits k times.”  
   This shows you’re not just blindly writing code.

2. **Show the optimized trick.**  
   *“Instead of building a 200‑digit number, I can pre‑sum the digit contributions of each letter.”*  
   Even if you don’t implement it, the interviewer will notice your awareness.

3. **Write clean, commented code.**  
   Use meaningful variable names (`cur`, `sumDigits`, `sb`) and keep the logic in a single, self‑contained loop.

4. **Discuss edge cases.**  
   Mention `k=1`, two‑digit indices, and why the string approach is safe.

5. **Talk performance.**  
   “Time complexity is O(|s| + k·len(num)), space is O(|s|).”  
   Even though the constraints are small, showing you can analyze complexity will impress senior engineers.

6. **Optional: Unit tests.**  
   “I’d add a few assert statements for the examples and random cases.”  
   A hiring manager loves candidates who think about testing.

---

### 2.8 SEO Checklist (The “Good”)

| SEO Element | How it’s covered |
|-------------|-----------------|
| **Title** | Contains “LeetCode 1945”, “Sum of Digits”, “Java / Python / C++” |
| **Meta‑Description** | Concise 155‑char summary with main keywords |
| **Headings (h1‑h4)** | Structured, keyword‑rich (`Problem Overview`, `Algorithm`, etc.) |
| **Code blocks** | Fenced with language tags (`java`, `python`, `cpp`) |
| **Bullet lists** | Easy to skim, good for readability |
| **Internal linking** | Not needed here, but a real blog would link to related LeetCode articles |
| **Alt‑text (if images)** | “Java solution diagram” etc. |
| **Canonical URL** | Avoid duplicate content by publishing only once |

---

### 2.9 Conclusion

LeetCode 1945 is deceptively simple, but it teaches a few valuable lessons:

- **Map characters quickly** (`c - 'a' + 1`).  
- **Keep heavy numbers as strings** to avoid overflow.  
- **Count operations correctly** – conversion is *not* a “transform”.  
- **Iterate only as many times as required** (`k ≤ 10`).

With the Java, Python, and C++ snippets above, you can hand‑write the solution in any interview, explain every step confidently, and show that you’re a well‑rounded software engineer ready for your next role.  

Good luck, and happy coding!