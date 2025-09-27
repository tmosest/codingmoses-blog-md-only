---
title: LeetCode 3571. Find the Shortest Superstring II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3571 – Find the Shortest Superstring II  
### LeetCode | Easy | String | Two‑String Overlap  

> **Goal** – Given two strings *s1* and *s2*, return the shortest string that contains both as contiguous substrings.  
> If several superstrings exist, any one is acceptable.

---

### Quick Code Snippets  

| Language | Solution | Complexity |
|----------|----------|------------|
| **Java** | [see below](#java-code) | **O(n²)** time, **O(1)** extra space |
| **Python** | [see below](#python-code) | **O(n²)** time, **O(1)** extra space |
| **C++** | [see below](#cpp-code) | **O(n²)** time, **O(1)** extra space |

> *All three implementations follow the same logic: 1) handle containment, 2) find the maximum overlap, 3) merge.*

---

## 📘 Problem Walk‑through

1. **Containment** – If one string is already a substring of the other, the answer is the longer string.  
2. **Overlap** – Find the longest suffix of one that matches a prefix of the other *and vice versa*.  
3. **Merge** – Append the non‑overlapped part of the other string to the first.  

Because the input strings are at most 100 characters, a simple double‑loop to test all overlaps (`O(n²)`) is more than fast enough.

---

## 🔍 The “Good, the Bad, and the Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | `O(n²)` is trivial for n≤100. | None. | For larger n, you’d need a suffix‑array or KMP style algorithm. |
| **Space Complexity** | Constant extra space. | None. | The naïve string concatenation in many languages can cause hidden copies. |
| **Readability** | Very short, clear logic. | Requires careful handling of `substring` indices. | The use of `String.contains()` on large strings can be expensive in Java (O(n²) per call). |
| **Edge Cases** | All covered (identical strings, one inside the other, no overlap). | Off‑by‑one errors in substring indices. | Wrong order of overlap checks can lead to sub‑optimal results. |
| **Extensibility** | Easy to adapt for >2 strings. | Need to modify the loop. | For 3‑4 strings you might inadvertently generate an O(2ⁿ) brute‑force solution. |

---

## 🚀 Code Breakdown

### Java – Simple Brute‑Force (O(n²))

```java
// 3571. Find the Shortest Superstring II
class Solution {
    public String shortestSuperstring(String s1, String s2) {
        int m = s1.length(), n = s2.length();

        // 1. If one contains the other, return the longer
        if (s1.contains(s2)) return s1;
        if (s2.contains(s1)) return s2;

        int maxOverlap = 0;
        String best = s1 + s2;   // fallback if no overlap

        // 2. Check all possible overlaps
        for (int i = 1; i <= Math.min(m, n); i++) {
            // s1 suffix == s2 prefix
            if (s1.substring(m - i).equals(s2.substring(0, i))) {
                if (i > maxOverlap) {
                    maxOverlap = i;
                    best = s1 + s2.substring(i);
                }
            }
            // s2 suffix == s1 prefix
            if (s2.substring(n - i).equals(s1.substring(0, i))) {
                if (i > maxOverlap) {
                    maxOverlap = i;
                    best = s2 + s1.substring(i);
                }
            }
        }
        return best;
    }
}
```

**Why it works**  
* `s1.contains(s2)` / `s2.contains(s1)` handles containment.  
* The double `for` loop tries every possible overlap length (`1 … min(m,n)`).  
* The first match that yields the greatest overlap produces the minimal superstring.  
* Because we only store the best string so far, no extra space is used.

---

### Python – Straightforward Implementation

```python
# 3571. Find the Shortest Superstring II
class Solution:
    def shortestSuperstring(self, s1: str, s2: str) -> str:
        # 1. Containment
        if s1 in s2:
            return s2
        if s2 in s1:
            return s1

        m, n = len(s1), len(s2)
        best = s1 + s2
        max_overlap = 0

        # 2. Overlap search
        for i in range(1, min(m, n) + 1):
            # s1 suffix == s2 prefix
            if s1[-i:] == s2[:i]:
                if i > max_overlap:
                    max_overlap = i
                    best = s1 + s2[i:]
            # s2 suffix == s1 prefix
            if s2[-i:] == s1[:i]:
                if i > max_overlap:
                    max_overlap = i
                    best = s2 + s1[i:]

        return best
```

*Python notes:*  
* String slicing `s1[-i:]` is O(i), but for n≤100 it’s trivial.  
* The logic mirrors the Java version, keeping code concise.

---

### C++ – Fast and Memory‑Friendly

```cpp
// 3571. Find the Shortest Superstring II
class Solution {
public:
    string shortestSuperstring(string s1, string s2) {
        // 1. Containment
        if (s1.find(s2) != string::npos) return s1;
        if (s2.find(s1) != string::npos) return s2;

        int m = s1.size(), n = s2.size();
        string best = s1 + s2;          // fallback
        int max_overlap = 0;

        // 2. Overlap search
        for (int i = 1; i <= min(m, n); ++i) {
            // s1 suffix == s2 prefix
            if (s1.compare(m - i, i, s2, 0, i) == 0) {
                if (i > max_overlap) {
                    max_overlap = i;
                    best = s1 + s2.substr(i);
                }
            }
            // s2 suffix == s1 prefix
            if (s2.compare(n - i, i, s1, 0, i) == 0) {
                if (i > max_overlap) {
                    max_overlap = i;
                    best = s2 + s1.substr(i);
                }
            }
        }
        return best;
    }
};
```

**C++ tips**  
* `string::compare` is O(i) but efficient.  
* `substr` creates a copy only when needed, keeping memory usage low.

---

## 🔑 Take‑Away Lessons

| Lesson | How It Helps |
|--------|--------------|
| **Check containment first** | Avoids unnecessary overlap work and guarantees minimal output. |
| **Always test both overlap directions** | Strings can overlap in two ways; missing one yields a sub‑optimal answer. |
| **Keep a “best” candidate** | One pass is enough; no need to build all possibilities. |
| **Time‑space trade‑off** | For 100‑character strings, brute‑force is perfectly acceptable; for larger data sets, a suffix‑array or KMP approach would be needed. |
| **Unit‑test thoroughly** | Edge cases: identical strings, one inside the other, no overlap, full overlap. |

---

## 📈 SEO‑Optimized Blog Post (Job‑Interview Focus)

### Title  
**“Cracking LeetCode 3571: Shortest Superstring II – Java, Python, C++ Solutions + Interview Tips”**

### Meta Description  
> Learn how to solve LeetCode 3571 “Find the Shortest Superstring II” in Java, Python, and C++. This post explains the algorithm, complexity, and best coding practices to impress recruiters.

### Keywords  
- LeetCode 3571  
- Shortest Superstring  
- String Manipulation  
- Java Interview Problem  
- Python Coding Challenge  
- C++ Algorithm  
- Job Interview Tips  
- Algorithm Complexity  
- Two‑String Overlap  
- Coding Interview Prep

### Outline

1. **Problem Overview** – Quick statement, constraints, examples.  
2. **Why It Matters** – Relevance to interview questions on string manipulation and algorithm design.  
3. **Solution Strategy** – Containment → Overlap → Merge.  
4. **Detailed Code Walk‑through** – Java, Python, C++.  
5. **Complexity Analysis** – `O(n²)` time, `O(1)` space.  
6. **Edge Cases & Pitfalls** – Off‑by‑one, wrong order of overlap checks.  
7. **Beyond Two Strings** – Hint at dynamic programming for *k* strings.  
8. **Interview Tips** – How to articulate the approach, trade‑offs, and discuss optimizations.  
9. **Conclusion** – Summarize and encourage practicing on similar problems.

### Hook & Call‑to‑Action  
*“Got stuck on string problems? Master the shortest superstring technique and watch recruiters notice your clear, efficient code. Try the solutions below and add them to your portfolio!”*

---

## 🎯 Final Thoughts

- **Simplicity wins** – A clean, O(n²) solution for this problem is not just acceptable; it’s ideal for an interview setting.  
- **Consistency across languages** – Demonstrating the same logic in Java, Python, and C++ shows deep understanding and versatility.  
- **Be prepared to discuss trade‑offs** – If the interviewer pushes for a faster algorithm, be ready to mention suffix arrays or KMP for larger inputs.  

Use this article as a learning checkpoint, add the code snippets to your GitHub, and practice explaining each step aloud. Good luck landing that job interview!