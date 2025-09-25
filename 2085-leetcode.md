---
title: LeetCode 2085. Count Common Words With One Occurrence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣ Problem Recap  
**LeetCode 2085 – Count Common Words With One Occurrence**  

> **Task**  
> You are given two arrays of lowercase words: `words1` and `words2`.  
> Count how many distinct words appear **exactly once** in *both* arrays.  

> **Constraints**  
> * `1 ≤ words1.length, words2.length ≤ 1000`  
> * `1 ≤ words1[i].length, words2[j].length ≤ 30`  
> * All words contain only lowercase English letters.  

The challenge is a classic “frequency‑counting” problem that is a staple in many coding interviews. Below you’ll find a clean, efficient solution in **Java, Python, and C++**, followed by a short blog‑style write‑up that highlights the *good, the bad, and the ugly* of this problem – plus a quick SEO‑boost to help you land that interview call.

---

## 2️⃣ Solutions

> **Key Idea** – Build a frequency map for each array, then intersect the two maps while checking that every common word has frequency 1 in *both*.

### 2.1 Java

```java
import java.util.*;

public class Solution {
    public int countWords(String[] words1, String[] words2) {
        Map<String, Integer> freq1 = new HashMap<>();
        Map<String, Integer> freq2 = new HashMap<>();

        // Count frequencies in words1
        for (String w : words1) {
            freq1.put(w, freq1.getOrDefault(w, 0) + 1);
        }

        // Count frequencies in words2
        for (String w : words2) {
            freq2.put(w, freq2.getOrDefault(w, 0) + 1);
        }

        int common = 0;
        for (String w : freq1.keySet()) {
            if (freq1.get(w) == 1 && freq2.getOrDefault(w, 0) == 1) {
                common++;
            }
        }
        return common;
    }
}
```

> **Complexity**  
> *Time*: `O(n + m)` (linear in the total number of words)  
> *Space*: `O(n + m)` for the two hash maps

---

### 2.2 Python

```python
from collections import Counter
from typing import List

class Solution:
    def countWords(self, words1: List[str], words2: List[str]) -> int:
        freq1 = Counter(words1)
        freq2 = Counter(words2)

        # Intersection of keys with frequency 1 in both counters
        return sum(1 for w in freq1
                   if freq1[w] == 1 and freq2.get(w, 0) == 1)
```

> **Complexity**  
> *Time*: `O(n + m)`  
> *Space*: `O(n + m)` – two `Counter` objects

---

### 2.3 C++

```cpp
#include <unordered_map>
#include <vector>
#include <string>

class Solution {
public:
    int countWords(std::vector<std::string>& words1,
                   std::vector<std::string>& words2) {
        std::unordered_map<std::string, int> freq1;
        std::unordered_map<std::string, int> freq2;

        for (const auto& w : words1)
            ++freq1[w];

        for (const auto& w : words2)
            ++freq2[w];

        int common = 0;
        for (const auto& [word, cnt] : freq1) {
            if (cnt == 1 && freq2[word] == 1)
                ++common;
        }
        return common;
    }
};
```

> **Complexity**  
> *Time*: `O(n + m)`  
> *Space*: `O(n + m)`

---

## 3️⃣ Blog‑Style Explanation

> **Title**  
> **LeetCode 2085 – “Count Common Words With One Occurrence”**: The Good, the Bad, and the Ugly (plus a job‑ready interview strategy)

### 3.1 What’s Going On?

The problem asks for words that appear **exactly once** in both input arrays. It feels trivial, but interviewers love this because it tests:

1. **Hash‑table intuition** – you need a frequency counter.
2. **Edge‑case awareness** – duplicate words, words that appear only in one array, empty intersections.
3. **Efficiency** – a two‑pass O(n + m) solution beats a nested‑loop O(n·m) attempt.

### 3.2 The Good

- **Straightforward**: Count → Intersect → Filter.
- **Scalable**: Handles up to 1 000 words in each array comfortably.
- **Language‑agnostic**: Works in Java, Python, C++, and almost any language with a dictionary / map.

### 3.3 The Bad

- **No built‑in “exactly once” filter**: You still need to check the counts manually.
- **Two maps are optional**: Some clever solutions compress this into a single map, but it can reduce readability for beginners.
- **Misunderstanding the requirement**: Some candidates count words that appear once in *any* array rather than *both*, leading to WA.

### 3.4 The Ugly

- **Nested‑loop brute force**: `O(n·m)` is far too slow for larger constraints.  
  ```python
  # This will time‑out
  for a in words1:
      for b in words2:
          if a == b:
              ...
  ```
- **Off‑by‑one frequency mishandling**: Using a “+1” and “–1” trick without clear semantics can be confusing.  
  > “If you see a word once in words1, set its count to 1. If you see it again, set to -1. Then only values equal to 1 after processing words2 mean valid words.” – this is clever but opaque for many interviewers.

### 3.5 A Clean, Interview‑Friendly Approach

```python
# Python 3
from collections import Counter

def count_words(words1, words2):
    freq1, freq2 = Counter(words1), Counter(words2)
    return sum(1 for w in freq1 if freq1[w] == 1 and freq2.get(w, 0) == 1)
```

- **Readability**: `Counter` is a one‑liner for frequency maps.
- **Explicit checks**: `freq1[w] == 1` & `freq2[w] == 1` leave no doubt.
- **Performance**: Linear time, constant‑space overhead per word.

### 3.6 Interview‑Ready Tips

| Tip | Why It Helps |
|-----|--------------|
| **State the problem in your own words** | Shows you understood the requirement (“exactly once in BOTH arrays”). |
| **Explain your approach before coding** | Allows the interviewer to catch mistakes early. |
| **Talk about time/space complexity** | Demonstrates analytical thinking. |
| **Consider edge cases** (`words1 = ["a"]`, `words2 = ["a","a"]`) | Shows thoroughness. |
| **Mention alternate solutions** (single map trick) | Highlights awareness of trade‑offs. |

### 3.7 SEO & Job‑Landing Hook

If you’re writing a blog post or sharing this on LinkedIn, sprinkle these keywords:

- **LeetCode 2085**
- **Count Common Words With One Occurrence**
- **Java solution**
- **Python solution**
- **C++ solution**
- **HashMap**
- **Frequency counting**
- **Coding interview**
- **Algorithmic problem solving**

A polished article that tackles the problem *and* explains “the good, the bad, and the ugly” demonstrates not only algorithmic chops but also communication skills—exactly what recruiters look for.

---

## 4️⃣ Final Thoughts

- **Master the map‑and‑count pattern**: It’s the backbone of many interview problems.
- **Keep your code clean**: Clear variable names (`freq1`, `freq2`) and explicit checks make your solution interview‑friendly.
- **Practice variants**: Try “count words that appear more than once” or “count words that appear exactly once in *either* array” to deepen your understanding.

Happy coding—and may that interview call be just a “Count Common Words” away! 🚀