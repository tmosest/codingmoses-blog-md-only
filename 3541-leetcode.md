---
title: LeetCode 3541. Find Most Frequent Vowel and Consonant - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 📈 “Find Most Frequent Vowel and Consonant” – A LeetCode‑Style Deep Dive  
**Problem ID**: 3541 | **Difficulty**: Easy | **Target Languages**: Java | Python | C++  

---

## 🚀 TL;DR  
* **Goal** – For a given lowercase string `s`, return the sum of the highest vowel frequency and the highest consonant frequency.  
* **Approach** – Count each letter once with two arrays (`vowelFreq` & `consonantFreq`), then take the maximum of each and add them.  
* **Complexity** – `O(n)` time, `O(1)` extra space.  
* **Languages** – Code is shown in **Java**, **Python**, and **C++**.

---

## 📖 Problem Statement

You are given a string `s` consisting of lowercase English letters (`'a'`‑`'z'`).  

1. Find the vowel (`'a'`, `'e'`, `'i'`, `'o'`, `'u'`) that occurs the most times.  
2. Find the consonant (any other letter) that occurs the most times.  
3. Return the sum of those two maximum frequencies.  

If there are no vowels or no consonants in the string, treat the missing frequency as **0**.  
If multiple vowels or consonants tie for the maximum, any one of them can be chosen – the result is unchanged.

**Constraints**

| Condition | Value |
|-----------|-------|
| `1 <= s.length <= 100` | – |
| `s` contains only lowercase English letters | – |

---

## 📊 Sample Cases

| Input | Output | Explanation |
|-------|--------|-------------|
| `"successes"` | `6` | Vowel max = 2 (`e`), consonant max = 4 (`s`), sum = 6 |
| `"aeiaeia"` | `3` | Vowel max = 3 (`a`), no consonants → 0, sum = 3 |

---

## 💡 Good, the Bad, and the Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Simplicity** | One-pass counting is easy to understand. | None – the naive solution is already optimal. | Using maps or regex would add unnecessary overhead. |
| **Performance** | O(n) time, constant space – fastest possible. | Slight risk of integer overflow if input were huge (not here). | Re‑scanning the string twice for vowels/consonants wastes time. |
| **Readability** | Clear separation of vowel/consonant logic. | Some may over‑complicate with helper functions. | Hard‑to‑read nested loops or bit‑masking for beginners. |

---

## 🔍 Step‑by‑Step Approach

1. **Initialize two frequency arrays** – one for vowels (size 5) and one for consonants (size 21).  
2. **Traverse the string once**:  
   * Determine if a character is a vowel (`'a'`, `'e'`, `'i'`, `'o'`, `'u'`).  
   * Increment the corresponding counter.  
3. **Find the maximum in each array**.  
4. **Return the sum** of the two maxima.

No extra data structures or libraries are required; the solution fits the constraints comfortably.

---

## 📚 Implementation

### Java

```java
public class Solution {
    public int maxFreqSum(String s) {
        int[] vowelFreq = new int[5];      // a, e, i, o, u
        int[] consonantFreq = new int[21]; // 21 consonants

        // Map a letter to its index in the vowel array or consonant array
        for (char ch : s.toCharArray()) {
            int idx = ch - 'a';
            if (isVowel(idx)) {
                vowelFreq[ch - 'a']++;
            } else {
                consonantFreq[idx - (isBeforeE(idx) ? 0 : 1)]++;
            }
        }

        int maxVowel = 0;
        for (int v : vowelFreq) if (v > maxVowel) maxVowel = v;

        int maxConsonant = 0;
        for (int c : consonantFreq) if (c > maxConsonant) maxConsonant = c;

        return maxVowel + maxConsonant;
    }

    private boolean isVowel(int idx) {
        return idx == 0 || idx == 4 || idx == 8 || idx == 14 || idx == 20; // a e i o u
    }
}
```

### Python

```python
class Solution:
    def maxFreqSum(self, s: str) -> int:
        vowel_count = [0] * 5          # a e i o u
        consonant_count = [0] * 21     # 21 consonants

        vowels = set('aeiou')
        for ch in s:
            if ch in vowels:
                vowel_count[ord(ch) - ord('a')] += 1
            else:
                # shift to 0‑based consonant index
                idx = ord(ch) - ord('a')
                consonant_index = idx - 1 if idx > 4 else idx
                consonant_count[consonant_index] += 1

        max_vowel = max(vowel_count)
        max_consonant = max(consonant_count)
        return max_vowel + max_consonant
```

### C++

```cpp
class Solution {
public:
    int maxFreqSum(string s) {
        int vowelFreq[5] = {0};
        int consonantFreq[21] = {0};

        for (char ch : s) {
            int idx = ch - 'a';
            bool isVowel = (idx == 0 || idx == 4 || idx == 8 || idx == 14 || idx == 20);
            if (isVowel) {
                vowelFreq[idx]++;              // idx 0,4,8,14,20 map to array positions
            } else {
                int consonantIdx = idx - (idx > 4 ? 1 : 0);
                consonantFreq[consonantIdx]++; // shift after vowels
            }
        }

        int maxVowel = *max_element(begin(vowelFreq), end(vowelFreq));
        int maxConsonant = *max_element(begin(consonantFreq), end(consonantFreq));
        return maxVowel + maxConsonant;
    }
};
```

---

## ⏱️ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Single pass over string | **O(n)** (`n <= 100`) | **O(1)** (fixed-size arrays) |
| Finding maxima | **O(1)** (constant 5 + 21 elements) | – |
| **Total** | **O(n)** | **O(1)** |

---

## ⚠️ Edge Cases & Pitfalls

| Scenario | What to watch out for |
|----------|-----------------------|
| **All vowels** | Consonant array stays zero → max = 0. |
| **All consonants** | Vowel array stays zero → max = 0. |
| **Empty string** | Problem guarantees length ≥ 1, but guard against it if reused. |
| **Multiple ties** | Any maximum works; we simply use `max_element`. |
| **Unicode / uppercase** | Input guarantees lowercase; otherwise additional checks are needed. |

---

## 🎯 Interview Tips

1. **Explain the logic clearly** – “I’ll count vowels and consonants separately; then I’ll pick the highest frequency of each.”
2. **Mention time/space trade‑offs** – This solution is optimal and requires no extra data structures beyond constant arrays.
3. **Ask clarifying questions** – e.g., “Do we need to handle uppercase letters?” or “Is the string guaranteed to be non‑empty?”
4. **Show edge‑case handling** – Talk about no vowels/consonants and tie cases.
5. **Walk through a sample** – Write out the frequency arrays for a simple string on the whiteboard.

---

## 📚 Variations You Might Encounter

| Variation | Idea |
|-----------|------|
| **Return the letter itself** | Keep the index of the max element and map back to the letter. |
| **Case‑insensitive** | Convert the string to lower/upper case before processing. |
| **Include 'y' as vowel** | Extend the vowel set accordingly. |
| **Return positions of max letters** | Record indices while iterating. |

---

## 🏁 Conclusion

The “Find Most Frequent Vowel and Consonant” problem is a textbook example of **frequency counting** and **array indexing**.  
Its optimal solution runs in linear time with constant space, making it an excellent choice for an interview problem that tests:

* Basic data structures (arrays, loops).  
* Boundary‑condition handling.  
* Clear communication of algorithmic steps.

By mastering this problem in Java, Python, and C++, you’ll have a versatile solution that demonstrates both **algorithmic efficiency** and **language‑agnostic design**—a solid talking point in any coding interview. Good luck, and happy coding! 🚀

---

## 🎯 SEO Checklist

| Keyword | Usage |
|---------|-------|
| LeetCode | Title, problem description, conclusion |
| Java | Code section, explanation |
| Python | Code section, explanation |
| C++ | Code section, explanation |
| Interview | Tips section |
| Coding interview | Tips section |
| Algorithmic problem | Introduction |
| Time complexity | Complexity analysis |
| Space complexity | Complexity analysis |
| Job interview | Conclusion, motivation |

*All sections are keyword‑rich yet natural, ensuring higher visibility on search engines for job‑seeker blogs.*

---