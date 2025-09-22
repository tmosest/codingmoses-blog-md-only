---
title: LeetCode 1967. Number of Strings That Appear as Substrings in Word - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1967 – “Number of Strings That Appear as Substrings in Word”

> **Problem** | Easy | 1967 | [LeetCode](https://leetcode.com/problems/number-of-strings-that-appear-as-substrings-in-word/)

> **Goal** – Given an array of strings `patterns` and a string `word`, count how many strings in `patterns` occur as a substring in `word`.

> **Why it matters** – This is a classic interview question that tests your understanding of string manipulation, the `contains` method, and how to keep the solution simple yet efficient. Mastering it will boost your chances of cracking the *string* portion of technical interviews.

---

## 📑 Problem Statement (Formal)

```text
Input:  patterns: String[]      (1 ≤ patterns.length ≤ 100)
        word:    String        (1 ≤ word.length ≤ 100)
        All characters are lowercase English letters.

Output: int – the number of patterns that appear as a contiguous substring in word.
```

### Sample

| patterns | word | output |
|----------|------|--------|
| ["a","abc","bc","d"] | "abc" | 3 |
| ["a","b","c"]        | "aaaaabbbbb" | 2 |
| ["a","a","a"]        | "ab" | 3 |

---

## 🧠 Approach – The Good, The Bad, and The Ugly

| Approach | Good | Bad | Ugly |
|----------|------|-----|------|
| **Brute‑force `contains`** | *O(n·m)* time (n patterns, m words) – perfect for the constraints. <br> Simple, readable, no extra data structures. | Repeated scanning could be costly for larger data. | N/A – too simple. |
| **Pre‑build a Suffix Trie/Automaton** | Can answer many queries in *O(1)* after preprocessing. <br> Good for huge `patterns` and repeated `word`s. | Heavy memory usage (~O(m²)). <br> Overkill for LeetCode constraints. | Complex code, prone to bugs. |
| **KMP/Two‑Pointer per pattern** | Linear per pattern in the length of the pattern + word. <br> Avoids worst‑case `contains` cost. | Still *O(n·m)* overall. <br> Implementation overhead. | N/A. |

> **Bottom line:** The brute‑force approach using Java’s `String.contains()`, Python’s `in`, or C++’s `find()` is the most *practical* for this problem. It gives you the correct answer, runs in microseconds for the given limits, and demonstrates a clean coding style—exactly what interviewers look for.

---

## 🛠️ Code Implementations

Below are clean, ready‑to‑run implementations in **Java**, **Python**, and **C++**. Each solution follows the same logic: iterate over every pattern, check if it is a substring of `word`, and increment a counter.

> **Note** – The code includes a small `main`/`if __name__ == "__main__":` block for quick testing. In a LeetCode environment you only need the `Solution` class or function.

---

### 1. Java

```java
import java.util.*;

public class Solution {
    /**
     * Count how many patterns appear as substrings in word.
     *
     * @param patterns Array of candidate patterns.
     * @param word     The target string.
     * @return Number of patterns that are substrings of word.
     */
    public int numOfStrings(String[] patterns, String word) {
        int count = 0;
        for (String pattern : patterns) {
            if (word.contains(pattern)) {   // O(m) for each pattern
                count++;
            }
        }
        return count;
    }

    // ---- quick demo ----
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.numOfStrings(
                new String[]{"a", "abc", "bc", "d"},
                "abc")); // 3

        System.out.println(sol.numOfStrings(
                new String[]{"a", "b", "c"},
                "aaaaabbbbb")); // 2

        System.out.println(sol.numOfStrings(
                new String[]{"a", "a", "a"},
                "ab")); // 3
    }
}
```

**Complexities**

| | Time | Space |
|---|---|---|
| Overall | **O(n · m)** (n ≤ 100, m ≤ 100) | **O(1)** (ignoring input size) |

---

### 2. Python

```python
from typing import List

class Solution:
    def numOfStrings(self, patterns: List[str], word: str) -> int:
        """
        Count patterns that appear as substrings in word.

        :param patterns: List[str] – candidate strings.
        :param word: str – target string.
        :return: int – count of matching patterns.
        """
        count = 0
        for pat in patterns:
            if pat in word:          # Python's substring check
                count += 1
        return count

# ---- quick demo ----
if __name__ == "__main__":
    sol = Solution()
    print(sol.numOfStrings(["a", "abc", "bc", "d"], "abc"))        # 3
    print(sol.numOfStrings(["a", "b", "c"], "aaaaabbbbb"))        # 2
    print(sol.numOfStrings(["a", "a", "a"], "ab"))                # 3
```

**Complexities**

| | Time | Space |
|---|---|---|
| Overall | **O(n · m)** | **O(1)** |

---

### 3. C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int numOfStrings(vector<string>& patterns, string word) {
        int count = 0;
        for (const string& pat : patterns) {
            if (word.find(pat) != string::npos) { // O(m) per pattern
                ++count;
            }
        }
        return count;
    }
};

// ---- quick demo ----
int main() {
    Solution sol;
    vector<string> p1 = {"a", "abc", "bc", "d"};
    cout << sol.numOfStrings(p1, "abc") << endl;          // 3

    vector<string> p2 = {"a", "b", "c"};
    cout << sol.numOfStrings(p2, "aaaaabbbbb") << endl;   // 2

    vector<string> p3 = {"a", "a", "a"};
    cout << sol.numOfStrings(p3, "ab") << endl;           // 3

    return 0;
}
```

**Complexities**

| | Time | Space |
|---|---|---|
| Overall | **O(n · m)** | **O(1)** |

---

## 🔍 Deep Dive – Why This Works

1. **Substring check** (`contains` / `in` / `find`) is already a **linear-time** operation: it scans `word` until it either finds `pattern` or reaches the end.  
2. Since `word` length ≤ 100, even the worst‑case scanning of every pattern is trivial for modern CPUs.  
3. The algorithm is *stable* – it correctly handles duplicate patterns (like `["a","a","a"]`) because each occurrence is counted separately, matching the problem’s requirement.

---

## 📈 Alternatives – When to Go Bigger

| Scenario | Recommended Strategy | Complexity |
|----------|---------------------|------------|
| **Large `patterns` array** (tens of thousands) | Build a **Trie** or **Aho‑Corasick automaton** for patterns and run a single scan of `word`. | O(total pattern length + word length) |
| **Very long `word`** (millions of chars) | Use a **Suffix Automaton** or **Suffix Array** to answer substring existence queries in *O(1)* after O(m) preprocessing. | O(m) preprocessing, O(1) queries |
| **Multiple queries on the same `word`** | Preprocess `word` into a suffix trie/automaton once, then check all patterns in *O(1)* each. | O(m) preprocessing, O(1) per query |

> *But* for LeetCode’s constraints, the simple `contains` solution is the most concise and passes comfortably.

---

## 📌 Takeaways for Interview Success

- **Read the constraints first.** A small input size often permits a simple solution.
- **Explain your solution’s time/space trade‑offs.** Interviewers love to see you can justify your choices.
- **Mention edge cases.** Duplicate patterns, empty strings (if allowed), and the difference between substring and subsequence.
- **Keep the code clean and commented.** A tidy solution reflects professionalism.

---

## 📣 SEO‑Optimized Summary

Looking to land a software engineering role? Mastering LeetCode #1967 “Number of Strings That Appear as Substrings in Word” is a quick win. The problem tests basic string search skills and your ability to write clear, efficient code. Our guide offers:

- ✅ Java, Python, and C++ implementations
- 📚 Step‑by‑step reasoning: why `contains` is enough
- 🚀 Complexity analysis to discuss in interviews
- 🧩 Tips on when to scale up with tries or suffix automata

Use this article as your interview prep companion and impress recruiters with both your coding chops and your strategic thinking. Happy coding! 🎉

---