---
title: LeetCode 555. Split Concatenated Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Answer**

Below you’ll find a clean, production‑ready solution for LeetCode #555 — *Split Concatenated Strings*.  
The code is written in **Java**, **Python** and **C++**, all using the same algorithmic idea:  

1. For every string decide whether to keep it or reverse it – whichever is lexicographically larger.  
2. Concatenate all “best” strings.  
3. Find the lexicographically **maximum** rotation of that big string (the answer is that rotation).  

The method works in **O(L²)** time (L ≤ 1000, so perfectly fine) and is easy to explain in an interview.

---

## 📄 Problem Statement (LeetCode 555)

> **Split Concatenated Strings**  
> You are given a string array `strs`.  
> You can choose to reverse **any** element in the array (or keep it as‑is).  
> After that, you concatenate the array into one long string and treat it as **circular**.  
> From this circular string you may “cut” it at any position to get a linear string of the same length.  
> Return the lexicographically largest possible string that can be obtained.

### Example

| `strs` | Possible loops | Best cut | Result |
|--------|----------------|----------|--------|
| `["abc","xyz"]` | `-abcxyz-`, `-abczyx-`, `-cbaxyz-`, `-cbazyx-` | between `a` and `z` | `zyxcba` |
| `["abc"]` | `-abc-`, `-cba-` | `-abc-` | `abc` → max rotation `cba` |

---

## 🤔 Why is it Hard?

1. **Two choices per string** – either keep or reverse – 2ⁿ combinations (impossible for n≈1000).  
2. **Cyclic nature** – you can start at any character, so a rotation matters.  
3. **String comparison** – lexicographic order of a rotation is not the same as the order of the string itself.

The naïve “try all combinations” is exponential. The key is to discover a **greedy** rule that guarantees optimality.

---

## 🔍 The Good, Bad & Ugly of the Solution

| Stage | Good | Bad | Ugly |
|-------|------|-----|------|
| 1️⃣ Choosing orientation | **O(1)** per string. Easy to reason. | You might think the best orientation depends on other strings. | Forgetting that you can also cut inside a string. |
| 2️⃣ Building the long string | Simple concatenation. | If you skip step 1, the result will be sub‑optimal. | Not handling empty strings or identical originals/reverses. |
| 3️⃣ Max rotation | Quadratic algorithm (`L² ≤ 10⁶`). | Booth’s algorithm is faster but more code‑dense. | Forgetting to handle ties or very long inputs. |

The **ugly** part was the initial misconception: “Maybe I need to explore every reverse choice” – but the greedy proof shows that picking the larger of `s` and `reverse(s)` for each element is optimal. That dramatically reduces complexity from exponential to linear.

---

## 🧩 Algorithm Overview

1. **Greedy Orientation**  
   For each string `s`  
   ```text
   if reverse(s) > s: s ← reverse(s)
   ```  
   After this step, every element is already “best” for the final string.

2. **Concatenate**  
   Join all the chosen strings into one long string `big`.

3. **Maximum Rotation**  
   Find the starting index `pos` of the lexicographically maximum rotation of `big`.  
   *We can duplicate the string (`big + big`) and simply scan all substrings of length `|big|`.*

4. **Return**  
   The substring `big[pos … pos+|big|-1]` (wrapping around) is the answer.

---

## 📊 Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Orientation | `O(total_length)` | `O(1)` |
| Concatenation | `O(total_length)` | `O(total_length)` |
| Max rotation (simple O(L²)) | `O(L²)` where `L ≤ 1000` → ≤ 1 000 000 operations | `O(1)` |
| **Total** | **O(total_length + L²)** | **O(total_length)** |

Both time and memory easily fit within LeetCode’s limits.

---

## 🛠️ Implementation Details

Below are concise, language‑specific snippets that you can copy‑paste into your IDE.

### Java (Java 17)

```java
import java.util.*;

public class Solution {
    // Helper: reverse a string
    private String rev(String s) {
        return new StringBuilder(s).reverse().toString();
    }

    // Naïve max rotation (O(L^2)), L <= 1000
    private String maxRotation(String s) {
        String doubled = s + s;
        int n = s.length();
        int best = 0;
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < n; j++) {
                char a = doubled.charAt(best + j);
                char b = doubled.charAt(i + j);
                if (a > b) break;
                if (a < b) {
                    best = i;
                    break;
                }
            }
        }
        return doubled.substring(best, best + n);
    }

    public String solve(String[] strs) {
        StringBuilder sb = new StringBuilder();
        for (String s : strs) {
            String rev = rev(s);
            sb.append(rev.compareTo(s) > 0 ? rev : s);
        }
        return maxRotation(sb.toString());
    }
}
```

### Python (Python 3)

```python
class Solution:
    def reverse(self, s: str) -> str:
        return s[::-1]

    def max_rotation(self, s: str) -> str:
        doubled = s + s
        n = len(s)
        best = 0
        for i in range(1, n):
            for j in range(n):
                a, b = doubled[best + j], doubled[i + j]
                if a > b: break
                if a < b:
                    best = i
                    break
        return doubled[best:best + n]

    def solve(self, strs: list[str]) -> str:
        sb = []
        for s in strs:
            rev = self.reverse(s)
            sb.append(rev if rev > s else s)
        big = ''.join(sb)
        return self.max_rotation(big)
```

### C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string reverseStr(const string &s) {
        string r = s;
        reverse(r.begin(), r.end());
        return r;
    }

    string maxRotation(const string &s) {
        string dbl = s + s;
        int n = s.size();
        int best = 0;
        for (int i = 1; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                char a = dbl[best + j];
                char b = dbl[i + j];
                if (a > b) break;
                if (a < b) {
                    best = i;
                    break;
                }
            }
        }
        return dbl.substr(best, n);
    }

    string solve(vector<string> &strs) {
        string big;
        for (auto &s : strs) {
            string rev = reverseStr(s);
            if (rev > s) big += rev;
            else          big += s;
        }
        return maxRotation(big);
    }
};
```

---

## ⚠️ Edge Cases & Common Pitfalls

| Edge | Why it matters | Fix |
|------|----------------|-----|
| Strings of length 1 | Reverse is identical | No special handling needed |
| All strings identical | `reverse(s)` equals `s` | The algorithm still works |
| Empty input array | Problem guarantees at least one element | Return empty string or handle separately |
| Very long single string (1000 chars) | `maxRotation` still quadratic but OK | Use Booth for speed if needed |

---

## ✅ Testing Checklist

1. **Sample tests** – ensure outputs match the LeetCode examples.  
2. **Randomized tests** – generate random strings, brute‑force (small n) to confirm optimality.  
3. **Large‑scale tests** – 1000 single‑char strings, to confirm runtime < 1 s.  
4. **Edge tests** – empty strings, all identical, alternating patterns.

---

## 🎯 Interview Take‑aways

- **Explain the greedy orientation rule**: “Choose the larger of `s` and `reverse(s)` because a rotation can start anywhere, making the orientation irrelevant for relative order.”
- **Show Booth’s algorithm**: Mention that you can find maximum rotation in linear time, but an O(L²) scan suffices given constraints.
- **Talk complexity**: O(total_length + L²) is acceptable; emphasize that the solution is scalable for typical interview inputs.
- **Mention pitfalls**: Handling equal strings and empty cases shows thoroughness.

---

## 📚 Final Thoughts

*Split Concatenated Strings* looks intimidating at first glance, but the key insight turns an exponential problem into a simple two‑step greedy + rotation problem.  
Feel free to use the provided code or adapt it to your own coding style. Good luck! 🚀

---

### 📌 Quick “One‑liner” for LeetCode

```python
class Solution:
    def solve(self, strs):
        big = ''.join((s[::-1] if s[::-1] > s else s) for s in strs)
        dbl = big + big
        best = 0
        for i in range(1, len(big)):
            for j in range(len(big)):
                if dbl[best + j] > dbl[i + j]: break
                if dbl[best + j] < dbl[i + j]:
                    best = i
                    break
        return dbl[best:best + len(big)]
```

---

**Happy coding!**