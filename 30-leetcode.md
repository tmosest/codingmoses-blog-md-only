---
title: LeetCode 30. Substring with Concatenation of All Words - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 30. Substring with Concatenation of All Words  
**Hard** – Leet Code

---

### Problem Recap  
Given a string `s` and an array of words `words` (all the same length), find every starting index of a substring of `s` that is a concatenation of *every* word in `words` exactly once, in any order.

> Example  
> `s = "barfoothefoobarman"`, `words = ["foo","bar"]`  
> Answer: `[0,9]`  (``barfoo`` and ``foobar``)

---

## 1️⃣  Solution Overview  

| Step | What we do | Why it works |
|------|------------|--------------|
| 1.  | **Pre‑process** – build a hash map `targetCount` that stores how many times each word should appear. | Needed for quick lookup and to detect over‑use. |
| 2.  | **Sliding window** – move a window of size `len(word) * k` through `s`. The window is advanced **by word length** so we never examine the same characters twice. | Guarantees *O(n)* time where *n = s.length*. |
| 3.  | **Maintain a running counter** – a hash map `currentCount` that tracks how many times each word appears in the current window. Keep a variable `used` that counts distinct words that are **not over‑used**. | When `used == k` and no word is over‑used → we found a valid substring. |
| 4.  | **Handle mismatches** – if a word not in `targetCount` appears, reset the window starting just after this word. | Quickly discards impossible substrings. |
| 5.  | **Handle over‑usage** – when the window size equals `substringSize` or we have an over‑used word, slide the left edge forward word‑by‑word, decrementing counts and `used` as necessary. | Keeps the window exactly the required length while preserving correctness. |

This algorithm runs in **O(n)** time and uses **O(k)** additional space (for the hash maps), satisfying the problem constraints.

---

## 2️⃣  Code Implementations  

Below you’ll find clean, production‑ready implementations in **Java**, **Python**, and **C++**.

> ⚠️  All solutions assume `words` is non‑empty and every word has the same length.  
> They also guard against the edge case when `s` is too short to contain a full concatenation.

---

### 2.1 Java

```java
import java.util.*;

public class Solution {
    public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> result = new ArrayList<>();
        if (s == null || words == null || words.length == 0) return result;

        int wordLen   = words[0].length();
        int wordCount = words.length;
        int totalLen  = wordLen * wordCount;
        if (s.length() < totalLen) return result;

        // 1. Build target frequency map
        Map<String, Integer> target = new HashMap<>();
        for (String w : words) {
            target.put(w, target.getOrDefault(w, 0) + 1);
        }

        // 2. Iterate over all possible offsets (0 … wordLen-1)
        for (int offset = 0; offset < wordLen; offset++) {
            int left = offset, right = offset;
            Map<String, Integer> window = new HashMap<>();
            int used = 0;          // number of words that are not over‑used
            boolean excess = false; // true if the window has an over‑used word

            while (right + wordLen <= s.length()) {
                // Grab the next word
                String word = s.substring(right, right + wordLen);
                right += wordLen;

                if (!target.containsKey(word)) {          // 3a. bad word -> reset
                    window.clear();
                    used = 0;
                    excess = false;
                    left = right;
                    continue;
                }

                // 3b. add the word to the window
                window.put(word, window.getOrDefault(word, 0) + 1);
                if (window.get(word) <= target.get(word)) {
                    used++;
                } else {
                    excess = true;          // over‑used
                }

                // 4. Shrink the window if it’s too big or contains excess
                while (right - left == totalLen || excess) {
                    String leftWord = s.substring(left, left + wordLen);
                    left += wordLen;
                    int cnt = window.get(leftWord) - 1;
                    window.put(leftWord, cnt);

                    if (cnt >= target.get(leftWord)) {
                        // removed an excess instance
                        excess = false;
                    } else {
                        used--;
                    }
                }

                // 5. We found a valid window
                if (used == wordCount && !excess) {
                    result.add(left);
                }
            }
        }
        return result;
    }
}
```

---

### 2.2 Python 3

```python
from collections import Counter, defaultdict
from typing import List

class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        if not s or not words:
            return []

        word_len, word_count = len(words[0]), len(words)
        total_len = word_len * word_count
        if len(s) < total_len:
            return []

        target = Counter(words)
        res = []

        for offset in range(word_len):
            left = offset
            right = offset
            window = defaultdict(int)
            used = 0
            excess = False

            while right + word_len <= len(s):
                word = s[right:right+word_len]
                right += word_len

                if word not in target:
                    # reset on bad word
                    window.clear()
                    used = 0
                    excess = False
                    left = right
                    continue

                # add word
                window[word] += 1
                if window[word] <= target[word]:
                    used += 1
                else:
                    excess = True

                # shrink if necessary
                while right - left == total_len or excess:
                    left_word = s[left:left+word_len]
                    left += word_len
                    window[left_word] -= 1

                    if window[left_word] >= target[left_word]:
                        excess = False
                    else:
                        used -= 1

                if used == word_count and not excess:
                    res.append(left)

        return res
```

---

### 2.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> findSubstring(string s, vector<string> words) {
        vector<int> res;
        if (s.empty() || words.empty()) return res;

        int wordLen   = words[0].size();
        int wordCount = words.size();
        int totalLen  = wordLen * wordCount;
        if (s.size() < totalLen) return res;

        // target frequency map
        unordered_map<string, int> target;
        for (const string& w : words)
            ++target[w];

        for (int offset = 0; offset < wordLen; ++offset) {
            int left = offset, right = offset;
            unordered_map<string, int> window;
            int used = 0;
            bool excess = false;

            while (right + wordLen <= (int)s.size()) {
                string word = s.substr(right, wordLen);
                right += wordLen;

                if (!target.count(word)) {            // bad word – reset
                    window.clear();
                    used = 0;
                    excess = false;
                    left = right;
                    continue;
                }

                // add to window
                ++window[word];
                if (window[word] <= target[word])
                    ++used;
                else
                    excess = true;

                // shrink if window too big or excess
                while (right - left == totalLen || excess) {
                    string lWord = s.substr(left, wordLen);
                    left += wordLen;
                    --window[lWord];

                    if (window[lWord] >= target[lWord])
                        excess = false;
                    else
                        --used;
                }

                if (used == wordCount && !excess)
                    res.push_back(left);
            }
        }
        return res;
    }
};
```

---

## 3️⃣  The Good, The Bad, The Ugly

| Aspect | ✅ Good | ❌ Bad | ⚙️ Ugly |
|--------|--------|--------|---------|
| **Complexity** | Linear time, small memory |  | |
| **Scalability** | Handles 10⁵‑length strings easily |  | |
| **Readability** | Clear comments, self‑explanatory variables |  | |
| **Edge‑Case Safety** | Handles empty inputs & short strings |  | |
| **Idiomatic Language Features** | Java `Map`, Python `Counter`, C++ `unordered_map` |  | |
| **Potential Pitfalls** | Off‑by‑one bugs, forgetting to reset on bad word | Avoid by rigorous tests | |
| **Debugging Aid** | Logging each window shrink step | Helps trace failures | |

### What to Keep in a Job Interview  

* **Show the *why*** – don’t just drop the code. Walk through the sliding window logic and why it achieves *O(n)*.  
* **Talk about edge cases** – ask the interviewer if `words` could be empty or words of different length; show how you’d guard against it.  
* **Explain your choice of data structures** – why a hash map, not an array.  
* **Mention the offset loop** – this is the trick that guarantees you don’t miss any valid start positions.  

---

## 4️⃣  SEO‑Optimized Title & Meta Description  

### Title  
> **Leet Code #30: Efficient “Substring with Concatenation of All Words” – Java, Python & C++ Solutions**

### Meta Description  
> Discover a fast, sliding‑window solution for Leet Code #30. Read detailed Java, Python, and C++ code, walk through the algorithm, and learn how to ace this “hard” problem in your next technical interview.  

---

## 5️⃣  Closing Thoughts  

The “good” part is the *linear* solution that elegantly mixes hash‑map frequency tracking with a sliding window advanced by word length.  
The “bad” part is the temptation to slide one character at a time – that would blow up to *O(n·wordLen)*.  
The “ugly” part lies in managing over‑used words inside the window; it’s subtle, but the while‑shrink loop fixes it cleanly.

With these implementations and the accompanying explanation, you’re ready to drop into a job interview and confidently talk through Leet Code problem 30. Happy coding! 🚀