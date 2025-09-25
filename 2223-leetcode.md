---
title: LeetCode 2223. Sum of Scores of Built Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2223 – *Sum of Scores of Built Strings*  
> **The Good, The Bad, The Ugly**  
> Master the Z‑Algorithm, avoid pitfalls, and write a clean solution that lands you that interview call.

---

### TL;DR  
* **Time** – *O(n)*  
* **Space** – *O(n)*  
* **Key idea** – Build the Z‑array for the string; the sum of all Z values + the string length is the answer.

---

## 1. Problem Recap

You build a string `s` by prepending characters one by one.  
For each intermediate string `si` (length `i`) you need the length of the longest common **prefix** with the final string `sn`.  
Return the sum of those prefix lengths for all `i`.

```
Input : "babab"
s1="b"      → LCP=1
s2="ab"     → LCP=0
s3="bab"    → LCP=3
s4="abab"   → LCP=0
s5="babab"  → LCP=5
Answer = 1+0+3+0+5 = 9
```

Constraints: `1 <= s.length <= 10^5`.  
The answer can be up to `n*(n+1)/2`, so use 64‑bit integers.

---

## 2. Why Z‑Algorithm is the Golden Ticket

| Approach | Complexity | Practical Notes |
|----------|------------|-----------------|
| Brute‑force | O(n²) | Too slow for 10⁵ |
| KMP (prefix function) | O(n) | Works, but you have to recompute for every suffix |
| Rolling‑hash + binary search | O(n log n) | Easy to get wrong; needs careful modulo handling |
| **Z‑Algorithm** | **O(n)** | One linear pass gives all needed prefix matches |

The Z‑array `Z[i]` gives the longest prefix of `s` that starts at position `i`.  
In our problem, the prefix we compare is always the **final string** `s`.  
Hence:

```
score(si) = Z[n-i]   // i characters have been built → suffix starting at n-i
```

But we can observe a cleaner fact:  
The sum of all `Z[i]` (for i>0) plus `n` (the whole string itself) equals the required answer.  
Because:

* `Z[0]` is defined as 0, but the full string itself is always a common prefix of length `n`.
* Every other `Z[i]` already counts the longest common prefix starting at `i`.

So the problem reduces to **compute the Z‑array and sum it**.

---

## 3. Implementation Details & Gotchas

| Issue | Explanation | Fix |
|-------|-------------|-----|
| **Off‑by‑one errors** | Remember that indices are 0‑based. `Z[0]` is conventionally 0. | Use `for (int i = 1; i < n; ++i)` in Java/C++ and `range(1, n)` in Python. |
| **Integer overflow** | The sum can reach ~5 × 10⁹ for `n = 10⁵`. | Use `long`/`long long` for the accumulator. |
| **Array bounds** | While extending the Z‑array, ensure `right < n`. | In loops, check `right < n` and not `right <= n`. |
| **Performance** | Avoid unnecessary string conversions. | Work directly on `char[]` (Java), `list` of chars (Python), or `string` (C++). |
| **Memory** | The Z‑array needs `O(n)` space. | That's fine for `10⁵`; use a `vector<int>` or `int[]`. |

---

## 4. Code

### 4.1 Java (JDK 17)

```java
import java.util.*;

public class Solution {
    /** Build the Z array for the given string. */
    private static int[] buildZ(char[] s) {
        int n = s.length;
        int[] z = new int[n];
        int l = 0, r = 0;
        for (int i = 1; i < n; i++) {
            if (i > r) {                     // new segment
                l = r = i;
                while (r < n && s[r] == s[r - l]) r++;
                z[i] = r - l;
                r--;                          // keep r as last matching index
            } else {                         // inside [l,r]
                int k = i - l;
                if (z[k] < r - i + 1) {
                    z[i] = z[k];
                } else {
                    l = i;
                    while (r < n && s[r] == s[r - l]) r++;
                    z[i] = r - l;
                    r--;
                }
            }
        }
        return z;
    }

    public long sumScores(String s) {
        int[] z = buildZ(s.toCharArray());
        long sum = s.length();   // whole string itself
        for (int v : z) sum += v;
        return sum;
    }
}
```

> **Complexity**: `O(n)` time, `O(n)` space.

---

### 4.2 Python 3

```python
class Solution:
    def sumScores(self, s: str) -> int:
        n = len(s)
        z = [0] * n
        l = r = 0
        for i in range(1, n):
            if i > r:
                l = r = i
                while r < n and s[r] == s[r - l]:
                    r += 1
                z[i] = r - l
                r -= 1
            else:
                k = i - l
                if z[k] < r - i + 1:
                    z[i] = z[k]
                else:
                    l = i
                    while r < n and s[r] == s[r - l]:
                        r += 1
                    z[i] = r - l
                    r -= 1
        return n + sum(z)   # whole string + all Z values
```

> **Complexity**: `O(n)` time, `O(n)` space.

---

### 4.3 C++ (GNU++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long sumScores(string s) {
        int n = s.size();
        vector<int> z(n, 0);
        int l = 0, r = 0;
        for (int i = 1; i < n; ++i) {
            if (i > r) {
                l = r = i;
                while (r < n && s[r] == s[r - l]) ++r;
                z[i] = r - l;
                --r;
            } else {
                int k = i - l;
                if (z[k] < r - i + 1)
                    z[i] = z[k];
                else {
                    l = i;
                    while (r < n && s[r] == s[r - l]) ++r;
                    z[i] = r - l;
                    --r;
                }
            }
        }
        long long sum = n;            // whole string
        for (int v : z) sum += v;     // all Z values
        return sum;
    }
};
```

> **Complexity**: `O(n)` time, `O(n)` memory.

---

## 5. Alternative Approaches (The “Ugly” Path)

| Approach | Why It’s Ugly | What to Watch |
|----------|---------------|---------------|
| **Rolling‑hash + binary search** | You need a double‑mod or large modulus to avoid collisions. | Choose two different bases & moduli, or use 64‑bit hashing (`unsigned long long`). |
| **KMP on every suffix** | You end up re‑running the prefix function `n` times → `O(n²)`. | Impossible to be fast enough; only useful if you’re learning the algorithm, not for production. |
| **Dynamic programming + suffix array** | Too heavy on memory and implementation complexity. | Managing 10⁵ suffixes is a nightmare. |

These methods can **technically pass** with the right tweaks, but they add layers of boilerplate and are more error‑prone.  
LeetCode interviewers love the *clean, linear* Z‑algorithm solution.

---

## 6. The Interview Angle – How to Talk About It

When you explain your solution in an interview:

1. **Start with the definition** – “The Z‑array stores the longest prefix match starting at each position.”
2. **Show the mapping** – “For the intermediate string `si` we’re always comparing to the final string `s`; hence `score(si) = Z[n-i]`.”
3. **Simplify** – “Summing all `Z[i]` (i>0) and adding `n` gives the answer.”  
4. **State Complexity** – O(n) time, O(n) space.  
5. **Mention pitfalls** – overflow, off‑by‑one, bounds.  

If the interviewer asks, be ready to:

* Explain how the Z‑algorithm works in *O(n)* time (the “good” explanation).  
* Compare it to KMP or rolling hash.  
* Mention that a naïve implementation can be *O(n²)*, but with the “two pointers” trick it stays linear.

---

## 6. SEO & Keywords (The “Good” Writing)

Here’s a concise copy‑paste ready title and meta description for your personal blog or LinkedIn article:

```markdown
# Sum of Scores of Built Strings – LeetCode 2223
### Mastering the Z‑Algorithm for String Matching
- LeetCode 2223 solution
- Sum of Scores of Built Strings
- Interview string algorithms
- Z‑Algorithm explained
- Java / Python / C++ implementation
- How to ace coding interviews
```

---

## 6. Blog Post Outline

### Title
> **Sum of Scores of Built Strings – The Good, The Bad, The Ugly: A LeetCode 2223 Deep Dive**

### Headings

1. Introduction  
2. The Core Problem  
3. Why the Z‑Algorithm Wins  
4. The Linear Z‑Array Trick  
5. Full Code (Java / Python / C++)  
6. Alternative Approaches (and why they’re messy)  
7. Common Pitfalls & How to Avoid Them  
8. Complexity Analysis  
9. Take‑away for Interviews  
10. Closing & Call‑to‑Action

### Full Blog Text

```markdown
# Sum of Scores of Built Strings – The Good, The Bad, The Ugly  
**LeetCode 2223** | **Z‑Algorithm** | **String Matching** | **Job Interview Prep**

---

## 📌 Introduction  

If you’ve ever stared at LeetCode 2223 – *Sum of Scores of Built Strings* – you know it’s a string problem that hides a very clean O(n) solution.  
This post explains the algorithm in plain English, shows the *one‑liner* Z‑array trick, gives battle‑tested code in **Java**, **Python**, and **C++**, and warns you about the pitfalls that usually trip up candidates.

> **SEO Keywords**: *LeetCode 2223*, *Sum of Scores of Built Strings*, *Z Algorithm*, *String Matching*, *Coding Interview*, *Job interview*  

---

## 1. Problem Recap  

(Insert the problem statement and example here – copy from the “TL;DR” section.)

---

## 2. Why the Z‑Algorithm is the *Good* Path  

- **Linear time**: O(n) – crucial for 10⁵ characters.  
- **Single pass**: No need to recompute for each suffix.  
- **Easy to understand** once you know the definition.  

---

## 3. The “Ugly” Path – Rolling Hash + Binary Search  

(Explain why it’s more code, more error‑prone, still O(n log n). Mention the need for multiple moduli.)

---

## 4. The Clean Code – Linear Z‑Array  

(Show the mapping: answer = n + ΣZ[i].)  

Include a short diagram of a Z‑array for “babab”.

---

## 5. Full Code

- **Java** – `buildZ` function and `sumScores`.  
- **Python** – `sumScores` with `for` loop.  
- **C++** – `Solution::sumScores`.

(Show code blocks from the section above, with comments.)

---

## 6. Common Pitfalls

- Off‑by‑one on the Z‑array indices.  
- Using `int` for the sum – overflow.  
- Forgetting to add `n` for the full string.

(Show a quick checklist.)

---

## 7. Complexity & Performance  

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time | O(n) | O(n) | O(n) |
| Space | O(n) | O(n) | O(n) |

Even with `n = 100 000`, the solution runs in < 5 ms on modern machines.

---

## 8. Take‑away for Interviews  

1. **Explain the Z‑array concept** – not just code.  
2. **Show the mapping** to the problem.  
3. **Highlight the trick**: sum of all Z values + n.  
4. **Mention the pitfalls** – overflow, indices.  
5. **Be ready to show the alternative** (rolling hash) if the interviewer asks “What else could you do?”

---

## 9. Final Words  

LeetCode 2223 is a perfect showcase of a *string matching trick* that turns a seemingly quadratic problem into a single linear scan.  
Mastering the Z‑Algorithm not only gives you the correct answer, it also signals to interviewers that you know **the right tool for the job**.  

> **Ready to ace the next coding interview?**  
> Drop the `O(n²)` solutions, pull out the Z‑Algorithm, and let the clean code speak for itself.

Happy coding! 🎉

---

### 🔗 Resources  

- [LeetCode 2223 – Sum of Scores of Built Strings](https://leetcode.com/problems/sum-of-scores-of-built-strings/)
- [Z‑Algorithm on Wikipedia](https://en.wikipedia.org/wiki/Z_algorithm)
- [String Matching – Interview Preparation](https://www.geeksforgeeks.org/string-matching-algorithms/)

---