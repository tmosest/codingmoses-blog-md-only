---
title: LeetCode 30. Substring with Concatenation of All Words - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 30 – Substring with Concatenation of All Words  
**Hard | 3‑Language Solution (Java / Python / C++)**  

| Language | Complexity | Notes |
|----------|------------|-------|
| Java | O(n × k) | Uses a sliding window and two hash maps |
| Python | O(n × k) | Same idea, slightly more concise with `collections.Counter` |
| C++ | O(n × k) | Uses `unordered_map` and two‑pointer technique |

> **Goal** – Find every starting index in a string `s` where a *concatenation* of all words from an array `words` (in any order) appears.  
> All words have the same length.

---

## 📌 TL;DR (Quick Code Snippets)

```java
class Solution {
    public List<Integer> findSubstring(String s, String[] words) {
        // … (Java implementation)
    }
}
```

```python
class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        # … (Python implementation)
```

```cpp
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        // … (C++ implementation)
    }
};
```

---

## 🧠 Why This Problem is a “Hot Topic” for Interviews

* **String manipulation + Hashing** – tests your ability to think about substrings, word boundaries, and hash maps.  
* **Sliding Window** – a classic interview pattern; many candidates get tripped on edge‑cases (repeated words, overlapping windows).  
* **Performance** – with `n ≤ 10^4` and `k ≤ 5000`, a naïve O(n × k²) approach will TLE.  
* **Language agnostic** – a perfect way to show your versatility in Java, Python, and C++.

---

## 📚 The Algorithm (Sliding Window + Hash Map)

1. **Pre‑processing**  
   * `wordLen` = length of each word.  
   * `totalLen` = `wordLen * k` – length of a full concatenation.  
   * Build a frequency map `need` for the `words`.

2. **Outer loop over possible offsets** (`0 … wordLen-1`)  
   Because all words share the same length, we can start a sliding window at each offset and only consider indices that align with word boundaries.

3. **Sliding Window**  
   * Two pointers `left` and `right` move in steps of `wordLen`.  
   * Maintain a hash map `window` for words seen in the current window.  
   * Keep counters:  
     * `have` – number of words that match required frequency.  
     * `excess` – flag if any word in the window appears more times than needed.

4. **Adjust window**  
   * If a word is not in `need`, reset the window to the next offset.  
   * If adding the new word would exceed `totalLen` or create an excess, shift `left` forward, updating `window`, `have`, and `excess`.  

5. **Record result**  
   Whenever `have == k` **and** there is no `excess`, the substring starting at `left` is a valid answer.

The sliding window ensures each character is examined at most twice, yielding **O(n × wordLen)** time and **O(k)** extra space.

---

## 🛠️ Code Walk‑Through (Java)

```java
import java.util.*;

public class Solution {

    // Main entry point
    public List<Integer> findSubstring(String s, String[] words) {
        List<Integer> res = new ArrayList<>();

        if (s == null || words == null || words.length == 0) return res;

        int wordLen = words[0].length();
        int totalLen = wordLen * words.length;
        int n = s.length();

        if (n < totalLen) return res;

        // Build required word counts
        Map<String, Integer> need = new HashMap<>();
        for (String w : words) need.merge(w, 1, Integer::sum);

        // Try each offset: 0 … wordLen-1
        for (int offset = 0; offset < wordLen; offset++) {
            int left = offset;
            int right = offset;
            Map<String, Integer> window = new HashMap<>();
            int have = 0;            // words that matched frequency
            boolean excess = false;  // flag if any word is over‑represented

            while (right + wordLen <= n) {
                String cur = s.substring(right, right + wordLen);
                right += wordLen;

                // If cur not needed → reset window
                if (!need.containsKey(cur)) {
                    window.clear();
                    have = 0;
                    excess = false;
                    left = right;
                    continue;
                }

                // Add cur to window
                window.merge(cur, 1, Integer::sum);

                // Update have/excess
                if (window.get(cur) <= need.get(cur)) {
                    have++;
                } else {
                    excess = true;
                }

                // Shrink while window too large or excess
                while (right - left == totalLen || excess) {
                    String leftWord = s.substring(left, left + wordLen);
                    left += wordLen;

                    int count = window.get(leftWord);
                    if (count <= need.get(leftWord)) {
                        have--;
                    } else {
                        excess = false;
                    }
                    window.put(leftWord, count - 1);
                }

                // Record result
                if (have == words.length && !excess) {
                    res.add(left);
                }
            }
        }

        return res;
    }
}
```

**Key Highlights**

| Line | Why It Matters |
|------|----------------|
| `for (int offset = 0; offset < wordLen; offset++)` | Guarantees that each word boundary is respected. |
| `while (right + wordLen <= n)` | Safely stops before exceeding string length. |
| `window.merge(cur, 1, Integer::sum)` | Efficient hash‑map update. |
| `if (have == words.length && !excess)` | Only a perfect match qualifies. |

---

## 🐍 Python Version (Python 3.10+)

```python
from collections import Counter, defaultdict
from typing import List

class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:
        if not s or not words: 
            return []

        word_len = len(words[0])
        total_len = word_len * len(words)
        n = len(s)

        if n < total_len: 
            return []

        need = Counter(words)
        res = []

        for offset in range(word_len):
            left = right = offset
            window = defaultdict(int)
            have = 0
            excess = False

            while right + word_len <= n:
                cur = s[right:right + word_len]
                right += word_len

                if cur not in need:
                    window.clear()
                    have = 0
                    excess = False
                    left = right
                    continue

                window[cur] += 1

                if window[cur] <= need[cur]:
                    have += 1
                else:
                    excess = True

                while right - left == total_len or excess:
                    left_word = s[left:left + word_len]
                    left += word_len

                    if window[left_word] <= need[left_word]:
                        have -= 1
                    else:
                        excess = False
                    window[left_word] -= 1

                if have == len(words) and not excess:
                    res.append(left)

        return res
```

Python’s `Counter` and `defaultdict` make the code a little more compact, but the underlying logic is identical to Java.

---

## 🧩 C++ Version (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        vector<int> res;
        if (s.empty() || words.empty()) return res;

        int wordLen = words[0].size();
        int totalLen = wordLen * words.size();
        int n = s.size();

        if (n < totalLen) return res;

        unordered_map<string, int> need;
        for (const string& w : words) ++need[w];

        for (int offset = 0; offset < wordLen; ++offset) {
            int left = offset, right = offset;
            unordered_map<string, int> window;
            int have = 0;
            bool excess = false;

            while (right + wordLen <= n) {
                string cur = s.substr(right, wordLen);
                right += wordLen;

                if (!need.count(cur)) {
                    window.clear();
                    have = 0;
                    excess = false;
                    left = right;
                    continue;
                }

                ++window[cur];
                if (window[cur] <= need[cur]) ++have;
                else excess = true;

                while (right - left == totalLen || excess) {
                    string leftWord = s.substr(left, wordLen);
                    left += wordLen;

                    if (window[leftWord] <= need[leftWord]) --have;
                    else excess = false;
                    if (--window[leftWord] == 0) window.erase(leftWord);
                }

                if (have == (int)words.size() && !excess) res.push_back(left);
            }
        }
        return res;
    }
};
```

---

## 🔍 Edge‑Case Checklist

| Scenario | What to watch for |
|----------|-------------------|
| **Repeated words** – e.g. `["foo","foo"]` | `window` may temporarily contain an excess; must shrink until frequencies match. |
| **Overlapping windows** – e.g. `s="barfoobar"` | The outer offset loop ensures we never miss a valid start. |
| **Word not present** – e.g. `"xyz"` | Immediate reset avoids wasteful work. |
| **Large k** – up to 5000 | Hash map size is `k`, not `n`. |

---

## 🎯 SEO‑Ready Blog Article

> **Title**: *Master LeetCode 30 – Substring Concatenation Solutions in Java, Python & C++*  
> **Meta Description**: Learn the fastest O(n×k) algorithm for LeetCode 30, complete with Java, Python, and C++ code, sliding‑window tricks, and interview‑ready explanations.  

### 📄 Blog Post

---

### 🔍 What is LeetCode 30: Substring with Concatenation of All Words?

LeetCode problem **30** challenges you to find every substring that is a *concatenation* of an array of equal‑length words. This is a **hard** string manipulation problem that appears frequently in **coding interviews** for software engineers.

---

### 📈 Why This Problem Stands Out

- **Complex string and hash map usage** – shows deep knowledge of data structures.  
- **Sliding window** – an interview staple; many candidates over‑think the solution.  
- **Performance‑critical** – O(n × k²) is unacceptable; you need the optimal O(n × wordLen) approach.  

---

### 🧩 Three Languages, One Solution

#### Java
```java
class Solution { public List<Integer> findSubstring(String s, String[] words) { /* … */ } }
```

#### Python
```python
class Solution:
    def findSubstring(self, s: str, words: List[str]) -> List[int]:  # …
```

#### C++
```cpp
class Solution { public: vector<int> findSubstring(string s, vector<string>& words) { /* … */ } };
```

All three use the **same sliding‑window + hash map** logic. Copy‑paste, compile, and test with your favourite framework.

---

### 📌 How to Explain the Solution in an Interview

1. **Start with a sketch** – show the window and the two hash maps on a whiteboard.  
2. **Walk through the outer offset loop** – explain why word boundaries matter.  
3. **Describe the sliding window** – emphasize that pointers move by `wordLen`.  
4. **Highlight the counter logic** – `have == k` means every word meets its required frequency.  
5. **Wrap up with complexity** – O(n × wordLen) time, O(k) space.  

When you break it down this way, interviewers see that you *understand* the pattern, *know* how to implement it, and *can* discuss its trade‑offs.

---

### 🔄 Running the Code Locally

| Language | Command |
|----------|---------|
| Java | `javac Solution.java && java -cp . Solution` (your testing harness can call `findSubstring`). |
| Python | `python3 - <<EOF\n# paste the Python solution above\nEOF` |
| C++ | `g++ -std=c++17 -O2 -pipe -static -s solution.cpp -o solution && ./solution` |

---

### 🏆 Interview Tips

1. **Start small** – test with a single word, then add repetitions.  
2. **Edge cases first** – empty input, word longer than `s`, all words equal, etc.  
3. **Use a helper function** for substring extraction (`s.substr` / `s.slice`).  
4. **Avoid nested loops over the words array** – they blow up the runtime.  
5. **Think in terms of *windows* of fixed length** – reduces the search space dramatically.  

---

### 🎉 Takeaway

LeetCode 30 is not just a string problem—it’s a *show‑case* for algorithmic thinking. Mastering the sliding‑window + hash‑map approach gives you:

- A reusable pattern for similar “search in string” problems.  
- Proof of your ability to write *efficient*, *bug‑free* code in multiple languages.  
- A compelling talking point for your next technical interview.

Happy coding, and best of luck on your interview journey!