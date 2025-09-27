---
title: LeetCode 1392. Longest Happy Prefix - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Longest Happy Prefix – LeetCode 1392  
**Algorithm, Code (Java / Python / C++), and a SEO‑Optimized Blog Post**  

---

### 1️⃣ Problem Recap  

| Item | Detail |
|------|--------|
| **LeetCode ID** | 1392 |
| **Title** | Longest Happy Prefix |
| **Difficulty** | Hard |
| **Definition** | A *happy prefix* is a **non‑empty** prefix of `s` that is also a suffix (but not the whole string). |
| **Goal** | Return the longest happy prefix of the given string `s`. Return `""` if none exists. |
| **Constraints** | `1 ≤ s.length ≤ 10⁵`  <br> `s` contains only lowercase English letters. |

> **Example**  
> `s = "ababab"` → longest happy prefix = `"abab"`  

---

## 2️⃣ Why the Naïve O(n²) Brute Force Fails  

The simplest idea is to check every prefix against every suffix, which takes **O(n²)** time.  
With `n = 10⁵`, that’s ≈ 10¹⁰ operations – far beyond the limits for an online judge or a real interview.

---

## 3️⃣ The Winning Approach – KMP / LPS Array  

### 📌 Key Insight  
The longest prefix that is also a suffix is exactly the last value of the **Longest Prefix‑Suffix (LPS)** array from the KMP string matching algorithm.

- **LPS[i]** = length of the longest proper prefix of `s[0…i]` that is also a suffix of this substring.  
- The answer is simply `s.substr(0, LPS[n‑1])`.

### 🛠️ Algorithm Steps  

1. **Build the LPS array** in a single left‑to‑right scan.  
   - `len` keeps the current LPS length.  
   - When `s[i] == s[len]` → `len++` and set `lps[i] = len`.  
   - Otherwise shrink `len` to `lps[len-1]` until a match or `len==0`.  
2. **Return** the substring `s[0: lps[n-1]]`.  

### 📈 Complexity  
- **Time**: `O(n)` – one pass over the string.  
- **Space**: `O(n)` – the LPS array.  
  (A constant‑space version is possible but unnecessary for `n ≤ 10⁵`.)

---

## 4️⃣ Code in Three Languages  

Below are ready‑to‑run implementations.  
All follow the same logic – build LPS, return prefix.

### Java  

```java
public class Solution {
    public String longestPrefix(String s) {
        int n = s.length();
        int[] lps = new int[n];
        int len = 0;          // length of previous longest prefix suffix
        int i = 1;

        while (i < n) {
            if (s.charAt(i) == s.charAt(len)) {
                len++;
                lps[i] = len;
                i++;
            } else {
                if (len != 0) {
                    len = lps[len - 1];
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }
        // longest happy prefix is the first lps[n-1] characters
        return s.substring(0, lps[n - 1]);
    }
}
```

### Python  

```python
class Solution:
    def longestPrefix(self, s: str) -> str:
        n = len(s)
        lps = [0] * n
        length = 0   # length of previous longest prefix suffix
        i = 1

        while i < n:
            if s[i] == s[length]:
                length += 1
                lps[i] = length
                i += 1
            else:
                if length != 0:
                    length = lps[length - 1]
                else:
                    lps[i] = 0
                    i += 1

        # longest happy prefix
        return s[:lps[n - 1]]
```

### C++  

```cpp
class Solution {
public:
    string longestPrefix(string s) {
        int n = s.size();
        vector<int> lps(n, 0);
        int len = 0;   // length of previous longest prefix suffix
        int i = 1;

        while (i < n) {
            if (s[i] == s[len]) {
                ++len;
                lps[i] = len;
                ++i;
            } else {
                if (len != 0) {
                    len = lps[len - 1];
                } else {
                    lps[i] = 0;
                    ++i;
                }
            }
        }
        return s.substr(0, lps[n - 1]);
    }
};
```

> **Test**  
> ```java
> Solution sol = new Solution();
> System.out.println(sol.longestPrefix("ababa")); // prints "ab"
> ```  

---

## 5️⃣ The Good, The Bad, and The Ugly  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic elegance** | KMP gives an *O(n)* solution – the ultimate interview “wow” factor. | The brute‑force O(n²) approach is elegant in its simplicity but practically unusable for big strings. | Mis‑implementing the LPS update (off‑by‑one errors) can silently give wrong results, especially when the entire string is a repetition of a smaller pattern. |
| **Code readability** | Clear variable names (`len`, `lps`, `i`) help future maintainers. | Over‑complicated macros or hidden global state can hide bugs. | Using a one‑liner `lps[i] = s.substr(...)` inside the loop creates unnecessary substrings, hurting performance. |
| **Edge‑case safety** | Handles strings of length 1 → returns `""`. | Forgetting to handle `len == 0` in the else branch leads to infinite loops. | Not checking that the returned prefix is *strictly* smaller than the original string can misclassify the whole string as a happy prefix (invalid per problem). |
| **Memory usage** | `O(n)` array is negligible for `n ≤ 10⁵`. | Storing a full 2‑D DP table would be overkill. | Attempting to reduce memory to `O(1)` by discarding the LPS array is possible but adds complexity that is rarely worth it. |
| **Reusability** | The LPS function is a classic sub‑routine used in many pattern‑matching problems. | Writing a custom brute‑force check for each interview is redundant. | Over‑engineering a “super‑generic” LPS library that handles patterns of different character sets can obfuscate the core logic. |

---

## 6️⃣ SEO‑Optimized Blog Article

> **Title**: *Longest Happy Prefix (LeetCode 1392) – KMP in Java, Python, C++ | Interview Prep Guide*  

> **Meta Description**: Master the “Longest Happy Prefix” problem with a clean KMP solution. Get ready for coding interviews with Java, Python, and C++ code snippets, plus an SEO‑friendly guide.

---

### 📚 Understanding the Longest Happy Prefix

When interviewers ask for the longest prefix that is also a suffix (excluding the whole string), they’re essentially testing your familiarity with string algorithms, particularly the Knuth‑Morris‑Pratt (KMP) algorithm. Mastery of KMP unlocks a whole class of problems—from pattern matching to computing border lengths in DNA sequences.

### ✅ Why KMP Beats Brute‑Force

- **Linear Time**: `O(n)` versus `O(n²)` for naive checks.  
- **Predictable Performance**: Handles the worst‑case (e.g., `"aaaa…a"`) efficiently.  
- **Widely Recognized**: Demonstrates you understand algorithmic fundamentals.

### 🛠️ Implementing LPS in Your Favorite Language

| Language | Quick Code | Notes |
|----------|------------|-------|
| **Java** | *See Java snippet above* | Use `String.charAt()` for O(1) char access. |
| **Python** | *See Python snippet above* | List comprehension for `lps` is fine, but the loop keeps memory usage low. |
| **C++** | *See C++ snippet above* | `vector<int>` is the most natural container for LPS. |

### 📈 Step‑by‑Step Walkthrough

1. **Initialize** `lps[0] = 0`, `len = 0`, `i = 1`.  
2. **Loop** while `i < n`:  
   - **Match** → `len++`, set `lps[i] = len`, `i++`.  
   - **Mismatch** → if `len > 0` → `len = lps[len-1]`; else `lps[i] = 0`, `i++`.  
3. **Result**: `s.substr(0, lps[n-1])`.  

### 📌 Edge Cases to Watch

- Single‑character strings → no happy prefix.  
- Entire string is a repeated pattern → answer is the largest proper prefix/suffix.  
- Empty string input is disallowed by constraints but still worth guarding against in production code.

### 📈 Performance Summary

| Complexity | Explanation |
|------------|-------------|
| **Time** | `O(n)` – each character is examined at most twice. |
| **Space** | `O(n)` – the LPS array. |

### 🚀 How to Nail This Problem in an Interview

1. **Explain the intuition**: "The problem is a classic LPS query."
2. **Show the algorithm**: Outline the KMP construction and the final slice.
3. **Code in the interviewer's language**: Provide a clean, tested implementation.
4. **Validate edge cases**: Ask about single characters or repeated patterns.
5. **Discuss extensions**: Mention how the same LPS logic solves border queries in other problems.

### 🎯 SEO‑Friendly Keywords

- Longest Happy Prefix  
- LeetCode 1392 solution  
- KMP algorithm  
- LPS array  
- String algorithm interview  
- Java string matching  
- Python coding interview  
- C++ interview problems  
- Algorithmic interview prep  
- Efficient string solutions  

Use these keywords naturally in headings, bullet points, and the meta description to attract recruiters looking for algorithmic talent.

---

## 7️⃣ Final Takeaway  

The **Longest Happy Prefix** problem is a textbook application of the **KMP LPS array**. With a single linear scan you can return the longest proper prefix that is also a suffix. The clean Java, Python, and C++ solutions above should help you ace the interview and land that tech role.

Happy coding! 🚀