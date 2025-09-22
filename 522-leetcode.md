---
title: LeetCode 522. Longest Uncommon Subsequence II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Answer**

Below you’ll find:

| Language | Code |
|----------|------|
| **Java** | `Solution.java` |
| **Python** | `solution.py` |
| **C++** | `solution.cpp` |

After the code, a full‑blown blog‑style article explains the problem, the algorithm, edge cases, pitfalls, and why this knowledge will make you a standout candidate in a software‑engineering interview. The article is SEO‑optimized around keywords like *“LeetCode 522”, “Longest Uncommon Subsequence II”, “Java solution”, “Python solution”, “C++ solution”, “software interview”*.

---

## 1️⃣ Java – `Solution.java`

```java
import java.util.*;

class Solution {
    public int findLUSlength(String[] strs) {
        int n = strs.length;
        // Sort by length descending
        Arrays.sort(strs, (a, b) -> Integer.compare(b.length(), a.length()));

        for (int i = 0; i < n; i++) {
            boolean isUnique = true;
            for (int j = 0; j < n; j++) {
                if (i == j) continue;
                if (isSubsequence(strs[i], strs[j])) {
                    isUnique = false;
                    break;
                }
            }
            if (isUnique) return strs[i].length();
        }
        return -1;
    }

    // Two‑pointer check: is s a subsequence of t ?
    private boolean isSubsequence(String s, String t) {
        if (s.length() > t.length()) return false;
        int i = 0, j = 0;
        while (i < s.length() && j < t.length()) {
            if (s.charAt(i) == t.charAt(j)) i++;
            j++;
        }
        return i == s.length();
    }
}
```

---

## 2️⃣ Python – `solution.py`

```python
from typing import List

class Solution:
    def findLUSlength(self, strs: List[str]) -> int:
        # Sort strings by length descending
        strs.sort(key=len, reverse=True)

        for i, s in enumerate(strs):
            if all(not self.is_subseq(s, t) for j, t in enumerate(strs) if i != j):
                return len(s)
        return -1

    @staticmethod
    def is_subseq(s: str, t: str) -> bool:
        """Return True if s is a subsequence of t."""
        if len(s) > len(t):
            return False
        it = iter(t)
        return all(c in it for c in s)
```

---

## 3️⃣ C++ – `solution.cpp`

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findLUSlength(vector<string>& strs) {
        // Sort by length descending
        sort(strs.begin(), strs.end(),
             [](const string& a, const string& b){ return a.size() > b.size(); });

        for (int i = 0; i < (int)strs.size(); ++i) {
            bool unique = true;
            for (int j = 0; j < (int)strs.size(); ++j) {
                if (i == j) continue;
                if (isSubsequence(strs[i], strs[j])) {
                    unique = false;
                    break;
                }
            }
            if (unique) return strs[i].size();
        }
        return -1;
    }

private:
    bool isSubsequence(const string& s, const string& t) {
        if (s.size() > t.size()) return false;
        size_t i = 0, j = 0;
        while (i < s.size() && j < t.size()) {
            if (s[i] == t[j]) ++i;
            ++j;
        }
        return i == s.size();
    }
};
```

---

## 📚 Blog Article – “Longest Uncommon Subsequence II: Good, Bad, & Ugly – A Job‑Ready Interview Guide”

> **Keywords**: *LeetCode 522, Longest Uncommon Subsequence II, Java solution, Python solution, C++ solution, software engineering interview, algorithm interview question, job interview*

### 1. The Problem in Plain English

Given an array of lowercase strings `strs`, find the length of the **Longest Uncommon Subsequence (LUS)**.  
A string `x` is an uncommon subsequence if:

* `x` is a subsequence of **one** string in `strs`.
* `x` is **not** a subsequence of **any** other string in `strs`.

If no such subsequence exists, return `-1`.

A subsequence can be obtained by deleting zero or more characters from a string without changing the order of the remaining characters.

> **Examples**  
> *Input:* `["aba","cdc","eae"]` → *Output:* `3`  
> *Input:* `["aaa","aaa","aa"]`  → *Output:* `-1`

### 2. Why This Problem is Interview Gold

* **Small constraints, big mental load** – lengths ≤ 10, but you must reason about subsequence relationships, not just string equality.  
* **Hidden edge‑case** – identical strings vs. same length but different strings.  
* **Multiple languages** – demonstrating the same algorithm in Java, Python, and C++ shows deep understanding.  
* **Scales to the “Longest Uncommon Subsequence” (LeetCode 521)** – once you nail `522`, you can tackle the harder version.

### 3. The “Good” – A Clean, Intuitive Solution

#### 3.1 Core Idea

*Sort the strings by length descending, then test each string to see if it is a subsequence of any other string. The first (i.e., longest) that passes is the answer.*

Why does this work?

* A longer string cannot be a subsequence of a shorter string.  
* If a string is equal to another, it is automatically a subsequence of that string.  
* Therefore, checking strings from longest to shortest guarantees we find the maximum length as soon as we find a unique subsequence.

#### 3.2 Complexity

With `n` strings (≤ 50) and each string length `L` (≤ 10):

* **Subsequence check** – two‑pointer scan: `O(L)`  
* **Total checks** – worst‑case `n²` comparisons: `O(n² * L)`  
  → at most `50² * 10 = 25,000` operations – trivial for any interview.

#### 3.3 Code Snippets

| Language | Highlight |
|----------|-----------|
| **Java** | Uses `Arrays.sort` with a lambda, and a private helper `isSubsequence`. |
| **Python** | Sorts in place with `strs.sort(key=len, reverse=True)`, and uses generator expression to short‑circuit. |
| **C++** | Uses `std::sort` with a lambda, and a `size_t` pointer scan. |

These snippets are already included above – copy‑paste into your IDE, run the test harness, and you’re done.

### 4. The “Bad” – Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Assuming a unique string is always the answer** | Even a unique string can be a subsequence of another string (e.g., `"aa"` vs. `"aaa"`). |
| **Using O(2^L) brute force to generate all subsequences** | With `L` up to 10, this is still feasible, but unnecessary and error‑prone. |
| **Skipping the duplicate check** | Two identical strings always invalidate each other. |
| **Sorting lexicographically instead of by length** | Longer strings must come first; otherwise you might return a shorter LUS. |

### 5. The “Ugly” – Hidden Edge Cases

1. **All strings identical** – e.g., `["abc","abc"]`.  
   *Answer:* `-1` because any subsequence of `"abc"` is also a subsequence of the other identical string.

2. **One string is a subsequence of all others** – e.g., `["ab", "abc", "abcd"]`.  
   *Answer:* `-1`.  
   The algorithm correctly checks against all others, and will not return the shortest string.

3. **Multiple strings of the same length but distinct** – e.g., `["ab", "cd"]`.  
   Both are candidates; the algorithm picks the first after sorting, which is fine because they are of equal length. Return `2`.

### 6. Interview‑Ready Tips

| Tip | Why It Matters |
|-----|----------------|
| **Explain the subsequence helper** | Shows you understand pointer‑based scanning and why it’s linear. |
| **Discuss time‑space trade‑offs** | Even though constraints are small, articulate that `O(n²)` is acceptable and that we avoid unnecessary memory. |
| **Mention sorting by length** | Demonstrates you’re thinking about how to guarantee optimality early. |
| **Show you can handle duplicate strings** | Often interviewers test this edge case. |
| **Write clean, commented code** | Readability scores highly in pair‑programming interviews. |

### 7. SEO‑Optimized Summary

> Want to ace your next **software engineering interview**? Master the **Longest Uncommon Subsequence II** (LeetCode 522) problem with our foolproof Java, Python, and C++ solutions. Learn the *good*, *bad*, and *ugly* of this classic algorithmic challenge and impress hiring managers with clear reasoning, optimal code, and real‑world edge‑case handling. 🚀

### 8. Final Takeaway

The “Longest Uncommon Subsequence II” is deceptively simple but packed with interview insight. The key is **sorting by length** and then **verifying subsequence relationships**. With the code above, you can confidently solve it in any of the three major languages. Show this solution in your portfolio, talk through the design decisions in your next interview, and let it be a testament to your algorithmic sharpness—exactly the quality hiring managers look for. Good luck!  

--- 

**Happy coding and best of luck on your job hunt!**