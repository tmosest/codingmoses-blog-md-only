---
title: LeetCode 481. Magical String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 “Magical String” – LeetCode 481  
**Java | Python | C++ Solutions + a deep‑dive SEO‑optimized blog article**

---

## 1️⃣ Problem Recap

> **LeetCode 481 – Magical String**  
> Given a *magical string* `s` composed only of the characters **‘1’** and **‘2’**.  
> `s` is magical because *concatenating the lengths of its consecutive runs* produces the string itself.  
>  
> Example:  
> `s = "1221121221221121122…"`  
> Grouping the runs: `"1 | 22 | 11 | 2 | 1 | 22 | 1 | 22 | 11 | 2 | 11 | 22 …"`  
> Lengths of each run: `1, 2, 2, 1, 1, 2, 1, 2, 2, 1, 2, 2 …` → which is exactly `s` again.  
>  
> **Task** – Given `n (1 ≤ n ≤ 10⁵)`, return the number of `'1'`s in the first `n` characters of `s`.

---

## 2️⃣ Why It Matters for Your Resume

- **Common interview puzzle** – demonstrates understanding of *two‑pointer*, *sequence generation*, and *time‑space trade‑offs*.
- Showcases *clean, idiomatic code* in multiple languages (Java, Python, C++).
- Great for a **portfolio** or a **coding‑blog** that can be indexed by search engines.

---

## 3️⃣ Quick & Dirty Brute‑Force

| Language | Time | Space | Notes |
|----------|------|-------|-------|
| Java | O(n²) | O(n) | Build string by appending runs until length ≥ n |
| Python | O(n²) | O(n) | `+=` on strings – costly |
| C++ | O(n²) | O(n) | `std::string` concatenation is also quadratic |

> **Bad** – will TLE for `n = 10⁵`.

---

## 4️⃣ The “Good” – Optimal O(n) Two‑Pointer Approach

### Core Idea

1. **Generate** the string `s` *on the fly* until it reaches `n` characters.
2. Use two indices:
   - `idx` – current position while **reading** the generated string (to determine the next run length).
   - `i`   – current position while **building** the string (where we append the next run).
3. Keep a counter `ones` to track `'1'`s as we build.

### Why It Works

- The run lengths are *the characters themselves*: the value at `s[idx]` (`'1'` → 1, `'2'` → 2) tells how many characters to write next.
- By stepping `idx` forward by one after using its value, we never revisit any character – linear scan.

### Pseudocode

```
ones = 1            // s[0] == '1'
idx = 0
i   = 1             // already have the first character
while i < n:
    run = s[idx] - '0'          // length of next run (1 or 2)
    char = '1' if i % 2 == 0 else '2' // alternating characters
    repeat run times:
        if i == n: break
        if char == '1': ones++
        i++
    idx++
return ones
```

> **Excellent** – `O(n)` time, `O(1)` auxiliary space (only a few ints).

---

## 5️⃣ Code in All Three Languages

> **Tip** – In Java, use `StringBuilder`; in Python, `list` + `join`; in C++, `std::string`.

---

### Java (clean, O(n))

```java
public class Solution {
    public int magicalString(int n) {
        if (n <= 0) return 0;
        // string builder – we only care about first n chars
        StringBuilder sb = new StringBuilder(n);
        sb.append('1');            // s[0]
        sb.append('2');            // s[1]
        sb.append('2');            // s[2]

        int ones = 1;              // first char is '1'
        int idx = 0;               // read pointer
        int i = 3;                 // write pointer

        while (i < n) {
            int run = sb.charAt(idx) - '0'; // 1 or 2
            char ch = (i % 2 == 0) ? '1' : '2'; // alternate
            for (int k = 0; k < run && i < n; k++) {
                sb.append(ch);
                if (ch == '1') ones++;
                i++;
            }
            idx++;
        }
        return ones;
    }
}
```

---

### Python (list for speed)

```python
class Solution:
    def magicalString(self, n: int) -> int:
        if n <= 0:
            return 0

        s = ['1', '2', '2']          # first three chars
        ones = 1                     # s[0] is '1'
        idx = 0                      # read pointer
        i = 3                         # write pointer

        while i < n:
            run = int(s[idx])          # 1 or 2
            ch = '1' if i % 2 == 0 else '2'
            for _ in range(run):
                if i == n: break
                s.append(ch)
                if ch == '1':
                    ones += 1
                i += 1
            idx += 1
        return ones
```

---

### C++ (vector<char> for speed)

```cpp
class Solution {
public:
    int magicalString(int n) {
        if (n <= 0) return 0;

        vector<char> s = {'1', '2', '2'};
        int ones = 1;          // s[0] is '1'
        int idx = 0;           // read pointer
        int i = 3;             // write pointer

        while (i < n) {
            int run = s[idx] - '0';           // 1 or 2
            char ch = (i % 2 == 0) ? '1' : '2';
            for (int k = 0; k < run && i < n; ++k) {
                s.push_back(ch);
                if (ch == '1') ++ones;
                ++i;
            }
            ++idx;
        }
        return ones;
    }
};
```

---

## 6️⃣ Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time   | O(n) | O(n)   | O(n) |
| Space  | O(n) | O(n)   | O(n) |  

> **Space** can be reduced to `O(1)` if we don't store the entire string, only the *last few* characters needed to generate the next run. The code above keeps a growing string of length `n` – still fine for `n ≤ 10⁵`.

---

## 7️⃣ Edge Cases & Testing

| Test | n | Expected | Reason |
|------|---|----------|--------|
| 1 | 1 | 1 | Only first character `'1'`. |
| 2 | 2 | 1 | `"12"` – one `'1'`. |
| 3 | 6 | 3 | `"122112"` – three `'1'`. |
| 4 | 100000 | *calculated* | Stress test. |
| 5 | 0 | 0 | Invalid but defensive. |

> Run `assert` statements in each language to confirm correctness.

---

## 8️⃣ The “Ugly” – What Can Go Wrong

- **Off‑by‑one errors**: forgetting that indices are 0‑based or that the run length includes the current char.
- **Infinite loop**: if `idx` never increments because you forget to advance after using its value.
- **Wrong alternation**: misusing `i % 2` can flip the sequence incorrectly.
- **Memory blow**: storing too much data when `n` is near the upper bound (`10⁵`) can be problematic in languages with lower memory overhead.

---

## 9️⃣ SEO‑Optimized Summary for Job Hunting

> **Keyword‑rich headline**: “LeetCode 481 – Magical String Solution in Java, Python, C++ – A Complete Interview Guide”  
> **Meta description**: “Solve LeetCode’s Magical String problem (481) with clean Java, Python, and C++ implementations. Learn the two‑pointer technique, analyze time & space complexity, and ace your coding interview.”

- Include tags: `LeetCode`, `magical string`, `two pointers`, `algorithm interview`, `coding challenge`, `Java`, `Python`, `C++`, `data structures`.
- Add internal links to related LeetCode problems (`1229`, `1155`) for cross‑traffic.
- Provide downloadable `.zip` of the three files for recruiters.

---

## 10️⃣ Final Thoughts

- **Good**: Linear‑time generation, minimal state, easy to explain in an interview.  
- **Bad**: If you forget the two‑pointer pattern, you’ll end up with a quadratic solution.  
- **Ugly**: Over‑engineering with additional data structures (e.g., `LinkedList`) that obscure the simple logic.

By mastering this problem, you’ll show recruiters you can *translate a weird mathematical definition into an efficient algorithm*—a highly valued skill in software engineering roles.

Good luck on your next interview! 🚀