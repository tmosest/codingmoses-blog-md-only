---
title: LeetCode 1839. Longest Substring Of All Vowels in Order - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Problem Recap – LeetCode 1839  
**Longest Substring Of All Vowels in Order**  

A string is **beautiful** if  

1. It contains *every* vowel `'a', 'e', 'i', 'o', 'u'` at least once.  
2. All vowels appear in strict alphabetical order (e.g. `"aaeeeiiioouu"` is good, `"aeoiu"` is not).  

Given a string `word` that consists *only* of vowels, return the length of the longest beautiful substring. If no such substring exists, return `0`.  

`1 ≤ word.length ≤ 5·10⁵`  

---

## ⚙️ Brute‑Force vs. Optimal

| Approach | Complexity | Verdict |
|----------|------------|---------|
| Check every possible substring, test its validity | `O(n²)` | Too slow for 5×10⁵ |
| **Two‑pointer greedy / sliding window** | **`O(n)`** | ✔️ Perfect |

The key is that we only need to track *how many* vowels we have seen in the correct order, not the exact counts. A single pass is enough.

---

## 🔧 Code – Three Languages

> **All three solutions are function‑only and run in `O(n)` time and `O(1)` extra space.**

### 1️⃣ Java

```java
public class Solution {
    public int longestBeautifulSubstring(String word) {
        int maxLen = 0;      // longest beautiful substring found
        int start = 0;       // start index of the current window
        int stage = 1;       // how many distinct vowels seen in order

        for (int i = 1; i < word.length(); i++) {
            char curr = word.charAt(i);
            char prev = word.charAt(i - 1);

            if (curr < prev) {           // order broken → restart window
                stage = 1;
                start = i;
            } else if (curr > prev) {    // correct next vowel → advance stage
                stage++;
            } // same vowel → nothing to do

            if (stage == 5) {            // all vowels seen
                maxLen = Math.max(maxLen, i - start + 1);
            }
        }
        return maxLen;
    }
}
```

### 2️⃣ Python

```python
class Solution:
    def longestBeautifulSubstring(self, word: str) -> int:
        max_len = 0
        start = 0
        stage = 1                     # 1 = 'a', 2 = 'e', … , 5 = 'u'

        for i in range(1, len(word)):
            curr, prev = word[i], word[i - 1]

            if curr < prev:           # window reset
                stage = 1
                start = i
            elif curr > prev:         # advance to next vowel
                stage += 1

            if stage == 5:            # all five vowels reached
                max_len = max(max_len, i - start + 1)

        return max_len
```

### 3️⃣ C++

```cpp
class Solution {
public:
    int longestBeautifulSubstring(string word) {
        int maxLen = 0;
        int start = 0;
        int stage = 1;                // 1:'a', 2:'e', … ,5:'u'

        for (int i = 1; i < word.size(); ++i) {
            char curr = word[i];
            char prev = word[i - 1];

            if (curr < prev) {        // reset window
                stage = 1;
                start = i;
            } else if (curr > prev) { // next vowel
                stage++;
            }

            if (stage == 5) {         // beautiful substring found
                maxLen = max(maxLen, i - start + 1);
            }
        }
        return maxLen;
    }
};
```

> All three implementations share the same logic:  
> *Track the current window start (`start`) and how many distinct vowels (`stage`) we’ve encountered in order.*  
> *When the sequence is broken (`curr < prev`), reset both.*  
> *When the next vowel appears (`curr > prev`), bump the stage.*  
> *When `stage` hits 5, we’ve found a beautiful substring and update the answer.*

---

## 📈 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Single pass over `word` | `O(n)` | `O(1)` |

With `n ≤ 5·10⁵`, the linear solution easily runs under 100 ms on modern machines.

---

## 🧩 The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Logic** | Clean greedy: only a few variables, no extra data structures. | None. | Over‑engineering (e.g., using regex or multiple pointers) makes it harder to read. |
| **Edge Cases** | Handles single‑character strings (`return 0`). | None. | Forgetting to reset `stage` on `curr < prev` → wrong results. |
| **Scalability** | `O(n)` works for the full 5e5 limit. | None. | O(n²) brute‑force would time‑out. |
| **Readability** | Each branch does one thing (reset, advance, check). | None. | Code that mixes counting with ordering logic can become confusing. |
| **Maintainability** | Same pattern across Java, Python, C++. | None. | Different implementations diverging in subtle ways (e.g., off‑by‑one errors). |

> **Takeaway:** In an interview, *simplicity* wins.  
> • Start with the greedy scan, explain why it works, and write the minimal code.  
> • Mention the constraints to justify why a linear scan is required.

---

## ⚠️ Common Pitfalls & Fixes

| Pitfall | Fix |
|---------|-----|
| Resetting only the start index but forgetting to set `stage = 1`. | In the reset branch, do both `stage = 1` **and** `start = i`. |
| Assuming any `curr > prev` is the *next* vowel. | Because the input contains only vowels, this is safe. |
| Over‑counting stage when encountering a vowel that’s already in the window (e.g., `"aaaeee"`). | Do **not** increment `stage` on equality; only increment on strictly greater. |
| Off‑by‑one errors in length calculation. | Use `i - start + 1`. |

---

## 🚀 Interview Tips

1. **Explain the constraints** first. Mention why an `O(n²)` solution is not acceptable.  
2. **Describe the greedy idea**: “We only care about the order of vowels, not their exact frequencies.”  
3. **Walk through the code** with a quick mental example (e.g., `"aaeeiiiouu"`).  
4. **Discuss complexity** and show that the solution meets the 5e5 bound.  
5. **Mention edge‑case handling**: single‑char input → `0`.  

> *Why this matters:* Many interviewers love greedy solutions that reduce the state to a handful of counters. It shows you can spot invariants and avoid unnecessary data structures.

---

## 🔎 Quick Reference (Code Snippets)

```text
Java   : public int longestBeautifulSubstring(String word)
Python : def longestBeautifulSubstring(self, word: str) -> int
C++    : int longestBeautifulSubstring(string word)
```

These snippets can be dropped straight into the LeetCode editor for a perfect run.

---

## 🎯 Final Thoughts

- **LeetCode 1839** is a great demonstration of how *ordering constraints* can be exploited with a greedy linear scan.  
- The pattern (`start`, `stage`) is reusable for other “ordered substring” problems.  
- In a real interview, explain the intuition first, then show the compact code.  
- Emphasize the `O(n)` time and `O(1)` space guarantees – interviewers love that.  

Good luck landing that coding interview! 🚀  

---

> **Meta Description:**  
> Master LeetCode 1839 – “Longest Beautiful Substring” – with concise Java, Python, and C++ solutions. Understand the greedy sliding window approach, complexity, and interview insights.  
> 
> **Keywords:** LeetCode 1839, Longest Beautiful Substring, vowel substring, interview coding, Java solution, Python solution, C++ solution, algorithm interview, data structures, job interview.  
> **Tags:** #LeetCode #Interview #Algorithms #Java #Python #C++ #VowelSubstring #JobInterviewTips

---