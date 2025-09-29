---
title: LeetCode 2506. Count Pairs Of Similar Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 📌 2506 – Count Pairs Of Similar Strings  
> **A complete guide with Java, Python & C++ solutions + an SEO‑friendly blog article that will help you land your next tech job**

---

## 🚀 TL;DR
- **Problem**: Count pairs of words that consist of the same set of characters (ignoring frequency & order).  
- **Key Insight**: Every word can be represented by a 26‑bit mask – one bit per alphabet letter.  
- **Optimal Solution**: Build the mask, store the frequency of each mask in a hash map, and for every new word add the current frequency to the answer.  
- **Complexity**:  
  - **Time** O(n · m) (where *n* = number of words, *m* = max word length)  
  - **Space** O(n) (hash map for masks)

```java
int similarPairs(String[] words) // Java
```

```python
def similarPairs(words: List[str]) -> int   # Python
```

```cpp
int similarPairs(vector<string>& words) // C++
```

> **Why this matters** – The bitmask + hash‑map pattern is a *stand‑out* trick that appears frequently in interview prep, especially for senior roles.  

---

## 📄 Blog Article (SEO‑Optimized)

> **Title**  
> **Count Pairs of Similar Strings (LeetCode 2506) – A Full Guide, Code, and Interview Tips**

> **Meta Description**  
> Master LeetCode 2506: Count Pairs of Similar Strings. Get a step‑by‑step solution, Java/Python/C++ code, time‑space analysis, and interview tricks to impress hiring managers.  

> **Keywords**  
> LeetCode 2506, Count Pairs of Similar Strings, Bitmask, HashMap, Java, Python, C++, Algorithm, Data Structures, Coding Interview, Technical Interview, Job Interview, Senior Software Engineer

---

### 1️⃣ Problem Recap

> **Input**: `words[0 … n-1]` – a list of lowercase strings.  
> **Goal**: Count the number of index pairs `(i, j)` with `i < j` such that the *set* of characters in `words[i]` equals the set in `words[j]`.

> **Example**  
> ```
> words = ["aba", "aabb", "abcd", "bac", "aabc"]
> → 2 pairs (0,1) and (3,4)
> ```

---

### 2️⃣ The Good: Why Bitmask Works

| ✅ | Reason |
|---|--------|
| **Fast** | Checking if two strings are similar is *O(1)* once we have the mask. |
| **Memory‑efficient** | 26 bits → a single `int`. |
| **Simple to implement** | No need to sort, use sets, or hash all characters. |
| **Scales** | Works for up to 10^5 words, 100 characters each – well within limits. |

#### Mask Construction

```text
bit 0 → 'a'
bit 1 → 'b'
...
bit 25 → 'z'
```

For each character `c` in a word, set `mask |= 1 << (c - 'a')`.  
Because we only care about the *presence* of a character, duplicates are ignored automatically.

---

### 3️⃣ The Bad: Edge Cases & Pitfalls

| ⚠️ | Issue | Fix |
|---|-------|-----|
| Duplicate words | Treat each instance separately – each pair counts. | Use a frequency map, not a set. |
| Empty strings (not in constraints) | Would create mask = 0; still valid. | No special handling needed. |
| Very long words | Still O(m) per word, fine for ≤100. | Keep loops tight; avoid unnecessary allocations. |

---

### 4️⃣ The Ugly: A Brute‑Force Approach

> **What not to do**  
> ```python
> ans = 0
> for i in range(n):
>     for j in range(i+1, n):
>         if set(words[i]) == set(words[j]):
>             ans += 1
> ```
> This O(n²·m) solution will TLE for large `n`. It also uses `set()` for every pair, doubling memory churn.

> **Takeaway** – Always look for a *signature* (bitmask, hash) to reduce pairwise comparisons.

---

### 5️⃣ Full Code – Three Languages

#### Java (LeetCode 2506)

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int similarPairs(String[] words) {
        int ans = 0;
        Map<Integer, Integer> freq = new HashMap<>();

        for (String word : words) {
            int mask = 0;
            for (char ch : word.toCharArray()) {
                mask |= 1 << (ch - 'a');
            }
            ans += freq.getOrDefault(mask, 0);
            freq.merge(mask, 1, Integer::sum);
        }
        return ans;
    }
}
```

#### Python 3

```python
from collections import defaultdict
from typing import List

class Solution:
    def similarPairs(self, words: List[str]) -> int:
        ans = 0
        freq = defaultdict(int)

        for word in words:
            mask = 0
            for ch in word:        # duplicates auto‑ignored
                mask |= 1 << (ord(ch) - 97)
            ans += freq[mask]
            freq[mask] += 1

        return ans
```

#### C++ (GNU‑C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int similarPairs(vector<string>& words) {
        int ans = 0;
        unordered_map<int, int> freq;

        for (auto& w : words) {
            int mask = 0;
            for (char c : w) {
                mask |= 1 << (c - 'a');
            }
            ans += freq[mask];
            freq[mask]++;           // store new occurrence
        }
        return ans;
    }
};
```

---

### 6️⃣ Complexity Analysis

| Metric | Explanation |
|--------|-------------|
| **Time** | `O(n * m)` – each word scans its characters once; `m ≤ 100`. |
| **Space** | `O(n)` – the frequency map can contain up to `n` different masks. |

---

### 7️⃣ Interview‑Ready Tips

1. **Mention the bitmask idea immediately** – interviewers love low‑level tricks.  
2. **Explain why duplicates don’t matter** – the mask only captures *presence*.  
3. **Talk about the hash map’s role** – constant‑time accumulation of pairs.  
4. **Show edge‑case handling** – empty string, very long words, repeated letters.  
5. **Compare with a naive O(n²) solution** – why it would fail.

---

### 8️⃣ TL;DR Cheat Sheet

| Step | Code Snippet (Java) |
|------|---------------------|
| Build mask | `mask |= 1 << (ch - 'a');` |
| Count pairs | `ans += freq.getOrDefault(mask, 0);` |
| Store mask | `freq.merge(mask, 1, Integer::sum);` |

---

## 🎯 Why This Blog Helps You Land a Job

1. **SEO‑Friendly** – The article is structured for search engines (headings, keywords).  
2. **Hands‑On Code** – Real, tested solutions in three languages.  
3. **Interview‑Centric** – Walks through “good, bad, ugly” scenarios.  
4. **Showcases Best Practices** – Bitmask + hash map pattern is a coveted interview tool.  

> **Next Step**: Copy the code, run it against the LeetCode platform, and add the article to your GitHub portfolio. LinkedIn readers will see the problem statement, your solution, and the insights—exactly what recruiters look for.

Happy coding, and good luck on the interview! 🚀