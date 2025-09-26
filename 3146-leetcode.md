---
title: LeetCode 3146. Permutation Difference between Two Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Permutation Difference Between Two Strings – LeetCode 3146  
**A Deep Dive (Java / Python / C++) + SEO‑Optimized Blog Post**

> **Tags:** #LeetCode #Java #Python #C++ #HashMap #StringAlgorithms #InterviewPreparation

---

## 1. Problem Overview  

**LeetCode 3146 – “Permutation Difference Between Two Strings”**  

> You are given two strings `s` and `t` such that:
> * Every character in `s` appears **at most once**.
> * `t` is a permutation of `s`.
>  
> The **permutation difference** between `s` and `t` is defined as  
> \[
> \sum_{c\in s} |\,\text{index}_s(c) - \text{index}_t(c)\,|
> \]  
>  
> Return this sum.

**Example**

| s | t | Result |
|---|---|--------|
| "abc" | "bac" | 2 (|0‑1| + |1‑0| + |2‑2|) |
| "abcde" | "edbac" | 12 |

**Constraints**

* `1 ≤ s.length ≤ 26`
* Each character in `s` appears once.
* `t` is a permutation of `s`.

---

## 2. Brute‑Force vs. Optimal Approach

### 2.1 Brute‑Force  
Iterate over every character in `s`, find its index in `t` by scanning the whole string, and sum the absolute differences.

*Time:* `O(n²)` – each lookup scans up to `n` characters.  
*Space:* `O(1)` – only a few counters.

This is fine for `n ≤ 26`, but for interview practice you should aim for an `O(n)` solution.

### 2.2 Optimal Approach – HashMap / Array

Because each character is unique, we can map a character → its position in **O(1)** time.

1. **Build a map for `s`**: `posInS[char] = index`.
2. **Traverse `t`**: for each `char` at index `i`, look up `posInS[char]` and add `abs(i - posInS[char])` to the answer.

*Time:* `O(n)`  
*Space:* `O(n)` (the hash map / array)

Since the alphabet is only lowercase English letters (`a–z`), a simple integer array of length 26 works just as well as a HashMap, giving us the fastest constant‑time lookup.

---

## 3. Code Implementations

Below are clean, idiomatic solutions in **Java**, **Python**, and **C++**. Each includes:

* Clear variable names
* In‑line comments
* Edge‑case safety (though constraints guarantee validity)

### 3.1 Java

```java
import java.util.HashMap;

public class Solution {
    public int findPermutationDifference(String s, String t) {
        // Map each character to its index in 's'
        HashMap<Character, Integer> indexInS = new HashMap<>();
        for (int i = 0; i < s.length(); i++) {
            indexInS.put(s.charAt(i), i);
        }

        int diffSum = 0;
        // Traverse 't', compute absolute differences
        for (int i = 0; i < t.length(); i++) {
            char ch = t.charAt(i);
            int posInS = indexInS.get(ch);
            diffSum += Math.abs(i - posInS);
        }
        return diffSum;
    }
}
```

> **Why HashMap?**  
> For a general solution; if you know the alphabet is small you could replace `HashMap` with an `int[26]`.

### 3.2 Python

```python
class Solution:
    def findPermutationDifference(self, s: str, t: str) -> int:
        # Build dictionary: char -> index in s
        pos_in_s = {ch: i for i, ch in enumerate(s)}

        diff_sum = 0
        for i, ch in enumerate(t):
            diff_sum += abs(i - pos_in_s[ch])

        return diff_sum
```

> **Pythonic Touches**  
> List comprehensions and dictionary literals make the code concise.  
> `abs` is built‑in, so no extra imports needed.

### 3.3 C++

```cpp
#include <string>
#include <unordered_map>
#include <cmath>

class Solution {
public:
    int findPermutationDifference(std::string s, std::string t) {
        std::unordered_map<char, int> posInS;
        for (int i = 0; i < s.size(); ++i)
            posInS[s[i]] = i;

        int diffSum = 0;
        for (int i = 0; i < t.size(); ++i)
            diffSum += std::abs(i - posInS[t[i]]);

        return diffSum;
    }
};
```

> **Why `unordered_map`?**  
> Provides average‑case O(1) lookup.  
> For an even faster constant factor you could use `int[26]` and index by `c - 'a'`.

---

## 4. Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Brute‑Force | `O(n²)` | `O(1)` |
| HashMap / Array | **`O(n)`** | **`O(n)`** |

With `n ≤ 26`, either approach works, but interviewers expect the linear solution and will discuss your choice.

---

## 5. Good, Bad, Ugly – Lessons Learned

| Category | Good | Bad | Ugly |
|----------|------|-----|------|
| **Algorithmic Choice** | Using a hash map gives linear time – the “sweet spot” for interviewers. | Skipping the map and using nested loops is easy but wastes time. | Over‑engineering: building a balanced tree (`TreeMap`), sorting, or using recursion. |
| **Code Clarity** | One‑liner dictionary in Python, short loops in Java/C++. | Heavy variable names (`map1`, `map2`) or overly verbose comments can clutter the solution. | Mixing languages or platform‑specific constructs (e.g., Java’s `TreeMap` when `HashMap` suffices). |
| **Edge Cases** | None – constraints guarantee valid input. | Forgetting to handle empty strings (though not allowed). | Unnecessary null checks that make the code longer without benefit. |
| **Readability** | Inline `abs(i - posInS[ch])` shows intent clearly. | Splitting the logic into multiple helper functions unnecessarily. | Using magic numbers or unclear indices (e.g., `s.charAt(i)`, `t.charAt(i)`). |
| **Performance** | Using an array of size 26 gives constant lookup – fastest in practice. | Using `HashMap` in Java still fine, but slower than array. | Using `TreeMap` leads to `O(n log n)` even though input size is tiny. |

**Takeaway:**  
Keep the solution *simple*, *fast*, and *clear*. Avoid over‑complicating when the problem constraints are tight and straightforward.

---

## 6. SEO‑Optimized Blog Summary

- **Title:** “LeetCode 3146 – Permutation Difference Between Two Strings: HashMap & Array Solutions (Java, Python, C++)”
- **Meta Description:** “Learn how to solve LeetCode 3146 – Permutation Difference Between Two Strings – with efficient HashMap and array approaches. Complete Java, Python, and C++ code examples plus a performance analysis.”
- **Keywords:** LeetCode 3146, permutation difference, string algorithm, hash map solution, Java string problem, Python string challenge, C++ interview question, algorithm complexity, interview prep.
- **Header Structure:**  
  - H1: LeetCode 3146 – Permutation Difference Between Two Strings  
  - H2: Problem Statement  
  - H2: Brute‑Force vs. Optimal Approach  
  - H2: Code Implementations (Java, Python, C++)  
  - H2: Complexity Analysis  
  - H2: Good, Bad, Ugly – Lessons Learned  
  - H2: Final Thoughts & Interview Tips

Adding an engaging introduction, illustrative examples, and a concise conclusion will keep readers hooked and help the post rank for job‑related search queries. Include a “Next Steps” section encouraging readers to explore related LeetCode problems or to share the article on LinkedIn, boosting its social signals.

---

## 7. Final Checklist for a Winning Interview

1. **Understand the constraints** – single unique characters → constant‑size alphabet.  
2. **Choose the right data structure** – `HashMap` or `int[26]`.  
3. **Keep the code concise** – one loop for mapping, one for summation.  
4. **Explain your thought process** – talk about time/space trade‑offs.  
5. **Test edge cases** – small strings, reverse order, identical strings.  

With the code snippets above and the lessons from the *Good, Bad, Ugly* analysis, you’re ready to ace this LeetCode problem—and land that interview! 🚀

---