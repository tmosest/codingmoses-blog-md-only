---
title: LeetCode 3146. Permutation Difference between Two Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 3146. Permutation Difference between Two Strings – Complete Solutions in Java, Python & C++  

> **LeetCode #3146** – *Easy*  
> **Problem ID**: 3146  
> **Tag**: String, Hash Table, Two Pointers  

> **Target Keywords**: *Permutation Difference, LeetCode 3146, Java solution, Python solution, C++ solution, interview coding problem, hash map, string permutation difference, job interview preparation*  

---

### 📌 Problem Summary

You’re given two strings **`s`** and **`t`** such that:

- Every character in **`s`** occurs **at most once**.
- **`t`** is a **permutation** of **`s`** (same characters, reordered).
- Both strings contain only lowercase English letters and have a maximum length of 26.

The *permutation difference* between the strings is defined as:

\[
\text{difference} = \sum_{c \in s}\left|\,\text{index}_s(c) - \text{index}_t(c)\,\right|
\]

Return that sum.

> **Example 1**  
> `s = "abc"`, `t = "bac"`  
> difference = |0‑1| + |1‑0| + |2‑2| = **2**

> **Example 2**  
> `s = "abcde"`, `t = "edbac"`  
> difference = |0‑3| + |1‑2| + |2‑4| + |3‑1| + |4‑0| = **12**

---

## 🔍 The “Good, The Bad, The Ugly”

| Category | What it means | Why it matters |
|---------|----------------|----------------|
| **The Good** | Simple O(n) algorithm, uses a hash‑map (or array) to store indices → *O(n) time, O(1) space* | Keeps the solution fast and easy to understand. |
| **The Bad** | Using a `TreeMap` or `LinkedHashMap` in Java adds ordering overhead; array of size 26 is simpler and uses less memory | Extra log‑factor on insertions & look‑ups, unnecessary for a problem with only 26 possible keys. |
| **The Ugly** | Forgetting that characters are unique → double‑count or missing a character → wrong answer | Invariant that each character appears only once is often overlooked. |

---

## 📚 Algorithmic Idea

1. **Map** each character in `s` to its index.
2. **Iterate** over `t`, look up the index of each character in the map and add the absolute difference to a running total.
3. Return the total.

Because every character is unique, a simple array of length 26 (or a hash map) works perfectly.

---

## 📈 Complexity Analysis

|   | **Time** | **Space** |
|---|----------|-----------|
| **Java/Python/C++** | **O(n)** – a single pass over each string | **O(1)** – constant 26‑size array or map |

`n` ≤ 26, so the algorithm is essentially constant time for practical inputs.

---

## 🛠️ Code Implementations

### 1️⃣ Java (Using an int array)

```java
import java.util.*;

public class Solution {
    public int findPermutationDifference(String s, String t) {
        // Since only lowercase letters are used, we can store indices in an int[26].
        int[] idxS = new int[26];
        for (int i = 0; i < s.length(); i++) {
            idxS[s.charAt(i) - 'a'] = i;
        }

        int sum = 0;
        for (int i = 0; i < t.length(); i++) {
            int idxInS = idxS[t.charAt(i) - 'a'];
            sum += Math.abs(i - idxInS);
        }
        return sum;
    }
}
```

> **Why this is good** – no overhead of a `Map`, constant‑time look‑ups, and no extra ordering.

---

### 2️⃣ Python (Using a dictionary)

```python
class Solution:
    def findPermutationDifference(self, s: str, t: str) -> int:
        pos = {ch: i for i, ch in enumerate(s)}
        return sum(abs(pos[ch] - i) for i, ch in enumerate(t))
```

> Python’s dictionary comprehension keeps the code concise while maintaining O(n) time.

---

### 3️⃣ C++ (Using a fixed array)

```cpp
class Solution {
public:
    int findPermutationDifference(string s, string t) {
        int pos[26];
        for (int i = 0; i < s.size(); ++i) {
            pos[s[i] - 'a'] = i;
        }
        int sum = 0;
        for (int i = 0; i < t.size(); ++i) {
            sum += abs(i - pos[t[i] - 'a']);
        }
        return sum;
    }
};
```

> C++ arrays give the fastest look‑ups; no heap allocations.

---

## 📦 Alternative Approach (Brute‑Force)

If you’re new to hash maps, you could brute‑force compute the indices:

```java
int sum = 0;
for (char c : s.toCharArray()) {
    int i = s.indexOf(c);          // O(n)
    int j = t.indexOf(c);          // O(n)
    sum += Math.abs(i - j);        // O(1)
}
```

But this would be **O(n²)** and far less efficient – avoid it for production or interview settings.

---

## 📚 Testing & Edge Cases

| Test | `s` | `t` | Expected |
|------|-----|-----|----------|
| 1 | "a" | "a" | 0 |
| 2 | "abc" | "bac" | 2 |
| 3 | "abcdefghijklmnopqrstuvwxyz" | shuffled | compute via script |
| 4 | "ab" | "ba" | 2 |
| 5 | "zxy" | "xyz" | 4 |

> **Tip:** Use random shuffles for larger tests to confirm the algorithm is robust.

---

## 📈 Why This Matters for Your Job Hunt

1. **Hash‑map mastery** – Interviewers love clean hash‑table solutions.
2. **Time‑space trade‑offs** – Show you can pick the simplest data structure for the problem.
3. **Python & C++** – Demonstrates language versatility.
4. **LeetCode 3146** – A real interview problem. Add it to your portfolio on GitHub and LinkedIn.

---

## 🎯 Final Takeaway

- **The best solution**: O(n) time, O(1) space – a single array lookup per character.
- **Avoid over‑engineering**: No need for `TreeMap`, `LinkedHashMap`, or any sorted structure.
- **Understand the invariant**: characters are unique; t is a permutation of s.

---

### 🚀 Ready to Impress?  

- **Clone** this repository and run the solutions in your favorite IDE.  
- **Add** your own test cases and push the commit to GitHub.  
- **Show** the problem, your analysis, and your solution on LinkedIn or in your portfolio.  

Good luck with your interview prep – let the permutation difference be the *difference* that lands you the job!