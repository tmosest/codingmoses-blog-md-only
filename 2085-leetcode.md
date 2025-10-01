---
title: LeetCode 2085. Count Common Words With One Occurrence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap  
**LeetCode 2085 – Count Common Words With One Occurrence**  
Given two string arrays `words1` and `words2`, return the number of strings that appear **exactly once** in **each** array.

| Example | Input | Output | Explanation |
|---------|-------|--------|-------------|
| 1 | `["leetcode","is","amazing","as","is"]`, `["amazing","leetcode","is"]` | `2` | “leetcode” & “amazing” each appear once in both arrays. |
| 2 | `["b","bb","bbb"]`, `["a","aa","aaa"]` | `0` | No common words. |
| 3 | `["a","ab"]`, `["a","a","a","ab"]` | `1` | “ab” is the only word that appears once in each array. |

**Constraints**

* `1 ≤ words1.length, words2.length ≤ 1000`
* `1 ≤ words1[i].length, words2[j].length ≤ 30`
* Only lowercase English letters.

---

## 2.  Intuition & Approach  

The task is a classic *frequency‑counting* problem.  
A word is **valid** iff

1. Its frequency in `words1` is **exactly 1**.
2. Its frequency in `words2` is **exactly 1**.

So we just need to count occurrences of every word in both arrays and then
count how many words satisfy the two conditions.

A single hash‑map (or dictionary) is enough if we process the second array
carefully:

1. Build a map `freq` for all words in `words1`.  
   `freq[w] = number of times w appears in words1`.
2. Scan `words2`.  
   * If `w` is not in `freq`, ignore it.  
   * If `freq[w] == 1`, set `freq[w] = 0` → we found a word that is a **candidate**.  
   * If `freq[w] != 1` (i.e. `>1` or already `0`), set `freq[w] = -1` → it is **invalid**.
3. Count how many entries in `freq` are `0`.  
   That is the answer.

Why this works?  
* Every word that appears once in both arrays ends up with value `0`.  
* All other words are turned into either `-1` (invalid) or remain `>1`
  (also invalid).

---

## 3.  Complexity Analysis  

| Measure | Complexity |
|---------|------------|
| Time | `O(n + m)` where `n = words1.length`, `m = words2.length` |
| Space | `O(n + m)` – the hash‑maps store at most all distinct words |

Both are optimal for this problem.

---

## 4.  Code Implementations  

### 4.1 Java (Java 17)

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int countWords(String[] words1, String[] words2) {
        Map<String, Integer> freq = new HashMap<>();

        // Count frequencies in words1
        for (String w : words1) {
            freq.put(w, freq.getOrDefault(w, 0) + 1);
        }

        // Process words2
        for (String w : words2) {
            if (!freq.containsKey(w)) continue;

            int val = freq.get(w);
            if (val == 1) {
                freq.put(w, 0);      // candidate found
            } else {
                freq.put(w, -1);     // invalidated
            }
        }

        // Count candidates
        int ans = 0;
        for (int v : freq.values()) {
            if (v == 0) ans++;
        }
        return ans;
    }
}
```

### 4.2 Python 3

```python
from collections import Counter
from typing import List

class Solution:
    def countWords(self, words1: List[str], words2: List[str]) -> int:
        # Frequency of words1
        freq = Counter(words1)

        # Scan words2
        for w in words2:
            if w not in freq:
                continue
            if freq[w] == 1:
                freq[w] = 0          # candidate
            else:
                freq[w] = -1         # invalid

        # Count candidates
        return sum(1 for v in freq.values() if v == 0)
```

### 4.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int countWords(vector<string>& words1, vector<string>& words2) {
        unordered_map<string, int> freq;

        // Count words1
        for (const string &w : words1) freq[w]++;

        // Scan words2
        for (const string &w : words2) {
            auto it = freq.find(w);
            if (it == freq.end()) continue;
            if (it->second == 1) it->second = 0;   // candidate
            else                it->second = -1;  // invalid
        }

        // Count candidates
        int ans = 0;
        for (auto &p : freq)
            if (p.second == 0) ++ans;
        return ans;
    }
};
```

---

## 5.  Blog Article – “The Good, the Bad, and the Ugly of Counting Common Words”

> **Title**: *“The Good, the Bad & the Ugly of Counting Common Words – A LeetCode 2085 Deep‑Dive”*  
> **Meta‑Description**: “Learn how to solve LeetCode 2085 in Java, Python & C++, plus the pros & cons of different approaches. Boost your coding interview prep & get hired!”  
> **Keywords**: LeetCode 2085, count common words, interview coding, hash map, Java interview, Python interview, C++ interview, coding interview prep, algorithm analysis, job interview success

---

### 5.1 Why This Problem Matters

- **Frequency counting is a staple** in coding interviews (LeetCode 347, 1189, etc.).  
- Mastering a simple hash‑map trick means you can solve **many other “counting” problems** in minutes.  
- Shows you can read constraints and produce a **time‑ and space‑optimal** solution.

---

### 5.2 The Good – What We Love

| Aspect | Why It’s Great |
|--------|----------------|
| **Simplicity** | One hash‑map, a handful of loops – no extra data structures. |
| **Speed** | `O(n + m)` runs in milliseconds even for the maximum size. |
| **Readability** | Clear variable names (`freq`, `candidate`), minimal branching. |
| **Language‑agnostic** | The same logic works in Java, Python, C++, Go, etc. |
| **Robustness** | Handles all edge cases (duplicate words, no common words, etc.) without special‑case code. |

### 5.3 The Bad – What Can Go Wrong

| Pitfall | Avoid With |
|---------|------------|
| **Using `Map.getOrDefault` incorrectly** | In Java, be careful to update the value correctly after the second pass. |
| **Counting twice** | If you first count both arrays separately, you might forget to enforce “exactly once” in both. |
| **Using a Set instead of Map** | A Set would only tell you if a word appears, not how many times. |
| **Ignoring constraints** | For larger inputs, a naive O(n²) search would time out. |

### 5.4 The Ugly – Less‑Obvious Tricky Bits

| Ugly Spot | Why It’s Tricky |
|-----------|-----------------|
| **Negative values** | Setting `-1` to mark invalid words might seem arbitrary. It’s a hack that works because we only care about `0`. |
| **Mutable map during iteration** | In Java, we’re mutating the map while iterating over `words2`, which is fine because we’re not iterating over the map itself. |
| **Ordering of operations** | If you process `words2` before `words1`, you’ll need a different strategy. |
| **Large alphabet** | Though the problem restricts to lowercase letters, a generic solution should handle Unicode or non‑ASCII inputs gracefully. |

---

### 5.5 SEO‑Ready Takeaway

- **Title** and **Meta‑Description** contain the target keyword *LeetCode 2085*.  
- Use **H1–H3** headings to structure the article (problem, solution, code, analysis).  
- Include **code snippets** in `<pre><code>` blocks for readability and SEO crawlers.  
- Add a **“Related Topics”** section linking to other LeetCode problems (e.g., 347, 1189).  
- End with a **call‑to‑action**: “Try the solution on LeetCode, submit it, and feel confident in your interview prep!”.

---

### 5.6 Final Words

> “Hash maps are the Swiss Army knife of interview problems. If you can count frequencies in linear time, you can solve a huge portion of the interview book. LeetCode 2085 is just a taste of that power.”

Good luck on your coding journey, and may that clean O(n+m) solution be the first step to landing your dream job! 🚀