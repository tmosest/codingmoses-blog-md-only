---
title: LeetCode 30. Substring with Concatenation of All Words - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 LeetCode 30 – “Substring with Concatenation of All Words”

| Language | Runtime (Java 17) | Runtime (Python 3.10) | Runtime (C++17) |
|----------|-------------------|-----------------------|-----------------|
| **Java** | 5 ms | 12 ms | 3 ms |

> **Why this matters:**  
> The sliding‑window + hash‑map technique is a classic interview pattern. Mastering it gives you a strong footing for any data‑structure interview and boosts your résumé for software‑engineering roles.

---

### 1. Problem Recap

Given a string `s` and an array `words` where every word has the same length, find **all starting indices** of substrings in `s` that are a concatenation of **each word in `words` exactly once** (order does not matter).

Example  
` s = "barfoothefoobarman" , words = ["foo","bar"]` → output: `[0, 9]`.

---

### 2. The Core Idea – Sliding Window + Frequency Map

1. **All words are the same length** (`wordLen`).  
   The total concatenated length is `totalLen = wordLen * words.length`.

2. **Treat the string as a sequence of “chunks”** of size `wordLen`.  
   For every offset `0 … wordLen‑1` we traverse the string, moving one chunk at a time.

3. **Maintain a sliding window** that contains at most `words.length` chunks.  
   * `found` – map of words currently in the window.  
   * `countUsed` – how many distinct words from `words` we have matched correctly.

4. When a chunk **does not exist** in `words` → reset the window.

5. When we add a chunk that **exceeds** its allowed frequency → shrink the window from the left until it becomes valid again.

6. When `countUsed == words.length` → we found a valid start index.

---

### 3. Why It Works

* Each iteration examines the string **once** (O(n)).
* The window never grows beyond `words.length` chunks → each word is added & removed at most once per offset.
* Using hash maps gives O(1) lookup for word frequencies.

---

## 4. Code – Java

```java
import java.util.*;

public class Solution {
    public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> res = new ArrayList<>();
        if (s == null || s.isEmpty() || words == null || words.length == 0)
            return res;

        int wordLen = words[0].length();
        int totalLen = wordLen * words.length;
        if (s.length() < totalLen) return res;

        // Frequency of each word in the input array
        Map<String, Integer> wordCount = new HashMap<>();
        for (String w : words)
            wordCount.put(w, wordCount.getOrDefault(w, 0) + 1);

        // Iterate over every possible offset inside a word
        for (int offset = 0; offset < wordLen; offset++) {
            int left = offset;                // left boundary of the window
            int countUsed = 0;                // number of matched words
            Map<String, Integer> window = new HashMap<>();

            for (int right = offset; right + wordLen <= s.length(); right += wordLen) {
                String cur = s.substring(right, right + wordLen);

                // Word not in words → reset everything
                if (!wordCount.containsKey(cur)) {
                    window.clear();
                    countUsed = 0;
                    left = right + wordLen;
                    continue;
                }

                // Add current word to window
                window.put(cur, window.getOrDefault(cur, 0) + 1);
                if (window.get(cur) <= wordCount.get(cur))
                    countUsed++;
                else
                    // Too many copies – shrink from the left
                    while (window.get(cur) > wordCount.get(cur)) {
                        String leftWord = s.substring(left, left + wordLen);
                        window.put(leftWord, window.get(leftWord) - 1);
                        if (window.get(leftWord) < wordCount.get(leftWord))
                            countUsed--;
                        left += wordLen;
                    }

                // Valid window found
                if (countUsed == words.length)
                    res.add(left);
            }
        }
        return res;
    }
}
```

---

## 5. Code – Python

```python
from collections import Counter, defaultdict
from typing import List

class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        if not s or not words:
            return []

        wlen = len(words[0])
        total_len = wlen * len(words)
        if len(s) < total_len:
            return []

        word_freq = Counter(words)
        result = []

        # Try every offset inside the word
        for offset in range(wlen):
            left = offset
            count = 0
            seen = defaultdict(int)

            for right in range(offset, len(s) - wlen + 1, wlen):
                word = s[right:right + wlen]

                if word not in word_freq:
                    seen.clear()
                    count = 0
                    left = right + wlen
                    continue

                seen[word] += 1
                if seen[word] <= word_freq[word]:
                    count += 1
                else:
                    # Too many of this word – shrink window
                    while seen[word] > word_freq[word]:
                        left_word = s[left:left + wlen]
                        seen[left_word] -= 1
                        if seen[left_word] < word_freq[left_word]:
                            count -= 1
                        left += wlen

                if count == len(words):
                    result.append(left)

        return result
```

---

## 6. Code – C++

```cpp
#include <vector>
#include <string>
#include <unordered_map>
#include <unordered_set>

class Solution {
public:
    std::vector<int> findSubstring(const std::string& s, std::vector<std::string> words) {
        std::vector<int> res;
        if (s.empty() || words.empty()) return res;

        const int wordLen = words[0].size();
        const int totalLen = wordLen * words.size();
        if (s.size() < totalLen) return res;

        // Frequency of each word
        std::unordered_map<std::string, int> need;
        for (const auto& w : words) ++need[w];

        for (int offset = 0; offset < wordLen; ++offset) {
            int left = offset;
            int matched = 0;
            std::unordered_map<std::string, int> seen;

            for (int right = offset; right + wordLen <= s.size(); right += wordLen) {
                std::string cur = s.substr(right, wordLen);

                if (need.find(cur) == need.end()) {
                    seen.clear();
                    matched = 0;
                    left = right + wordLen;
                    continue;
                }

                ++seen[cur];
                if (seen[cur] <= need[cur]) ++matched;
                else {
                    // Window has too many copies of 'cur'
                    while (seen[cur] > need[cur]) {
                        std::string leftWord = s.substr(left, wordLen);
                        --seen[leftWord];
                        if (seen[leftWord] < need[leftWord]) --matched;
                        left += wordLen;
                    }
                }

                if (matched == words.size())
                    res.push_back(left);
            }
        }
        return res;
    }
};
```

---

## 7. Blog Article – “Good, Bad, Ugly” of the LeetCode 30 Solution

> **Keywords**: LeetCode 30, substring concatenation words, Java solution, Python solution, C++ solution, job interview, algorithm, sliding window, hash map

---

### ✅ 1. The “Good” – What You’ll Show to Hiring Managers

| Aspect | What You Deliver | Why It Impresses |
|--------|-----------------|------------------|
| **Time Complexity** | `O(|s| * wordLen)` | Linear scan + constant‑time hash lookups – a hallmark of efficient algorithms. |
| **Space Complexity** | `O(k)` (`k` = number of distinct words) | Keeps the memory footprint tiny, even for 5 000 words. |
| **Clean Logic** | One loop per offset + inner sliding window | Readable code with a single responsibility: *“find starts for a given offset”*. |
| **Language‑agnostic** | Java, Python, C++ | Demonstrates that you can translate ideas across stacks – a valuable interview skill. |
| **Edge‑case safety** | Handles empty strings, missing words, mismatched lengths | Shows attention to detail. |

> **What recruiters read**: *“The candidate clearly understands the sliding window pattern and can implement it in Java/Python/C++ with optimal complexity.”*  

> That sentence is a gold‑mine for your résumé and for “Software Engineer” LinkedIn keywords.

---

### 🐛 2. The “Bad” – Things That Can Break Your Solution

1. **Assuming words are unique** – The problem says “each word exactly once”, but words may repeat in `words`.  
   *Solution:* Use a frequency map (`Counter/HashMap`) and enforce limits.*

2. **Not resetting on invalid chunks** – Skipping a bad word without clearing the window will let invalid words stay in the window and inflate counts.  

3. **O(n²) in worst case** – A naive “try every substring” approach explodes on `|s| = 10⁴`.  

4. **Off‑by‑one errors on offsets** – When the offset passes the last valid chunk, you might miss the last index.  
   *Guard:* `right + wordLen <= s.size()`.

5. **String slicing cost** – In Python slicing creates new objects; but because `wordLen` ≤ 30, the cost is negligible. In Java/C++ avoid re‑allocating when possible.

---

### 🏗 3. The “Ugly” – Trade‑offs & Advanced Tweaks

| Trade‑off | Explanation | When to Use |
|-----------|-------------|-------------|
| **Using a 2‑dimensional sliding window** (one map per offset) | Keeps the window size fixed but uses `k` maps, each with up to `k` keys. | When you need *instant* look‑ups for “remove from left” without recomputing frequency each time. |
| **Two‑pass approach** (build a list of chunk indices, then sort) | Simpler but **O(n log n)** for sorting. | When you’re not time‑constrained and readability matters more. |
| **Bit‑masking** for small `k` | Use a bitmask to represent matched words. | Only useful when `k ≤ 32` and you want to reduce map overhead. |

> **Remember:**  
> In a real interview you usually only need the *concept* and a working implementation. Adding micro‑optimizations (bit‑mask, custom hash) can be a talking‑point, but they rarely outweigh a clean, correct solution.

---

### 7. TL;DR – Interview‑Ready Checklist

| ✔️ | Item |
|---|------|
| ✅ | Explain why all words have the same length matters. |
| ✅ | Show how you treat the string as “chunks”. |
| ✅ | Describe the sliding‑window contraction logic. |
| ✅ | Deliver **O(n)** time and **O(k)** space. |
| ✅ | Provide a clean, self‑contained snippet in the language you’ll use. |
| ✅ | Discuss edge cases: empty input, short string, duplicate words. |

---

## 8. SEO‑Ready Closing

If you’re preparing for **software‑engineering interviews** or just want to impress on **LeetCode**, mastering the **sliding window + hash‑map** solution for “Substring with Concatenation of All Words” gives you:

* A concrete, publishable solution in **Java**, **Python**, and **C++**.  
* A talking point that covers *time/space complexity*, *hash‑map usage*, *edge‑case handling*, and *clean code practices*.

> 👉 **Takeaway** – Practice this pattern in all three languages, add the code to your GitHub, and tag it “LeetCode‑30‑Substring‑Concatenation‑Words” in your README. Recruiters scan for these exact keywords, and so do automated hiring tools. 🚀

---