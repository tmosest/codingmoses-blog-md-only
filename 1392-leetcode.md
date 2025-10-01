---
title: LeetCode 1392. Longest Happy Prefix - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # The Longest Happy Prefix – From Theory to Job‑Ready Code  
*(Java | Python | C++)*  

---

## 1️⃣ Problem Overview  

> **LeetCode 1392 – Longest Happy Prefix**  
> A *happy prefix* is a non‑empty prefix of a string that is also a suffix **excluding the entire string itself**.  
>  
> **Input**: `s` (1 ≤ |s| ≤ 10⁵, lowercase letters)  
> **Output**: The longest happy prefix; if none exists return `""`.  

```text
Example
Input : "ababa"
Output: "aba"
```

Why does this matter?  
- String manipulation appears in **every** senior‑level interview.  
- The optimal solution demonstrates mastery of *prefix function* (KMP) – a classic algorithm that employers love.  

---

## 2️⃣ Why Brute‑Force Fails  

A naïve solution checks all prefixes against all suffixes:  

```python
for length in range(len(s)-1, 0, -1):
    if s[:length] == s[-length:]:
        return s[:length]
return ""
```

Time complexity: **O(n²)** – impossible for *n = 100 000*.  
Space: O(1).  

> **Bad**: It will TLE on LeetCode and looks lazy in an interview.

---

## 3️⃣ The “Good” – KMP Prefix Function  

The *Longest Proper Prefix which is also Suffix (LPS)* array from KMP gives exactly what we need.  

### 3.1  What is LPS?  

For every position `i` (1‑based), `lps[i]` stores the length of the longest proper prefix of `s[0…i]` that is also a suffix of that substring.  

- `lps[0] = 0` (no proper prefix).  
- The array is built in **O(n)** time by reusing previously computed values.  

### 3.2  Build LPS in One Pass  

```text
len = 0                     // current lps length
i   = 1
while i < n
    if s[i] == s[len]
        len += 1
        lps[i] = len
        i += 1
    else if len > 0
        len = lps[len-1]    // fall back to shorter lps
    else
        lps[i] = 0
        i += 1
```

The last value `lps[n-1]` is the length of the longest happy prefix.  

### 3.3  Complexity  

- **Time**: `O(n)` – each index processed once.  
- **Space**: `O(n)` for the `lps` array.  

This is the “good” algorithm that passes all tests instantly and showcases efficient thinking.

---

## 4️⃣ “The Ugly” – Common Pitfalls  

| Issue | Explanation | Fix |
|-------|-------------|-----|
| Off‑by‑one errors | Using 1‑based vs 0‑based indices incorrectly | Stick to 0‑based (`i = 1`, `len = 0`). |
| Forgetting to skip the whole string | `lps[n-1]` might equal `n` if the string is composed of a single repeating character | The LPS definition guarantees it will never be `n` (proper prefix). |
| Not handling empty string | Problem guarantees length ≥ 1, but defensive code is nice | Return `""` if `n == 0`. |
| Using substring on negative index | If `lps[n-1] == 0` ensure substring call `s[:0]` returns `""` | No special case needed; language handles it. |

---

## 5️⃣ The Code (All Three Languages)

Below are clean, commented implementations that you can paste into LeetCode or your IDE.

---

### 5.1 Java

```java
public class Solution {
    public String longestPrefix(String s) {
        int n = s.length();
        int[] lps = new int[n];
        int len = 0;          // length of previous longest prefix-suffix
        int i = 1;
        while (i < n) {
            if (s.charAt(i) == s.charAt(len)) {
                len++;
                lps[i] = len;
                i++;
            } else if (len > 0) {
                len = lps[len - 1];
            } else {
                lps[i] = 0;
                i++;
            }
        }
        return s.substring(0, lps[n - 1]);  // longest happy prefix
    }
}
```

---

### 5.2 Python

```python
class Solution:
    def longestPrefix(self, s: str) -> str:
        n = len(s)
        lps = [0] * n
        length = 0
        i = 1
        while i < n:
            if s[i] == s[length]:
                length += 1
                lps[i] = length
                i += 1
            elif length > 0:
                length = lps[length - 1]
            else:
                lps[i] = 0
                i += 1
        return s[:lps[-1]]
```

---

### 5.3 C++

```cpp
class Solution {
public:
    string longestPrefix(string s) {
        int n = s.size();
        vector<int> lps(n, 0);
        int len = 0;   // current lps length
        int i = 1;
        while (i < n) {
            if (s[i] == s[len]) {
                ++len;
                lps[i] = len;
                ++i;
            } else if (len > 0) {
                len = lps[len - 1];
            } else {
                lps[i] = 0;
                ++i;
            }
        }
        return s.substr(0, lps[n - 1]);  // longest happy prefix
    }
};
```

---

## 6️⃣ Edge‑Case Checklist  

| Edge Case | Expected Output | Reason |
|-----------|-----------------|--------|
| `"a"` | `""` | No proper prefix. |
| `"aaaa"` | `"aaa"` | Prefix “aaa” matches suffix “aaa”. |
| `"abca"` | `""` | No common prefix/suffix. |
| `"ababab"` | `"abab"` | Overlap allowed. |

---

## 7️⃣ Interview‑Ready Tips  

1. **Explain the intuition**: “We’re basically looking for the longest border of the string.”  
2. **Mention KMP**: “KMP’s LPS array is built exactly for this purpose.”  
3. **Time/Space**: State `O(n)` time, `O(n)` space.  
4. **Edge Cases**: Highlight single‑character strings, repeated patterns.  
5. **Show test coverage**: Provide a few unit tests.  

> **SEO Bonus**: Use keywords like “LeetCode 1392”, “Longest Happy Prefix”, “KMP algorithm”, “string prefix suffix interview”, “coding interview tips”.

---

## 8️⃣ Summary – The Good, The Bad, The Ugly  

- **Good**: KMP LPS is linear, elegant, and shows algorithmic depth.  
- **Bad**: Brute‑force O(n²) is too slow and indicates shallow understanding.  
- **Ugly**: Off‑by‑one bugs and forgetting to exclude the whole string can break the solution.  

By mastering the LPS approach, you’ll not only ace this LeetCode problem but also demonstrate proficiency in **string algorithms**, a highly‑valued skill for backend, full‑stack, and data‑engineering roles.

---

## 9️⃣ Ready for the Next Challenge  

Now that you’ve got the Longest Happy Prefix solved, try the next *Hard* string problems:  
- 1708. Longest Constant‑Repetition Subsequence  
- 1695. Maximum Erasure Value  

Happy coding, and good luck landing that dream job! 🚀