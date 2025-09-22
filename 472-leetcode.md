---
title: LeetCode 472. Concatenated Words - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 472 – Concatenated Words  
**Hard | 1 k + solutions | “Job‑Ready” Interview Question**

---

### TL;DR  
- **Goal** – Find every word in the list that can be built from *at least two* other words from the same list.  
- **Typical solution** – Dynamic Programming (word‑break) + a hash‑set for O(1) look‑ups.  
- **Languages** – Java, Python, C++ (see code below).  
- **Why it matters** – It tests your ability to reason about **sub‑problems**, **caching** and **edge‑case handling**—skills interviewers love.

---

## 1. Problem Recap

```text
Input: words = ["cat","cats","catsdogcats","dog","dogcatsdog",
                "hippopotamuses","rat","ratcatdogcat"]
Output: ["catsdogcats","dogcatsdog","ratcatdogcat"]
```

A *concatenated word* is a string that can be segmented into two or more **shorter** words from the same array (the shorter words can be the same).  

Constraints  
- 1 ≤ words.length ≤ 10⁴  
- 1 ≤ words[i].length ≤ 30  
- All words are unique lowercase letters  
- Sum of all lengths ≤ 10⁵  

---

## 2. Why This Question Is “Hard”

| Good | Bad | Ugly |
|------|-----|------|
| ✔️ **Clear definition** – You only need to check segmentation. | ❌ **Many “almost‑right” solutions** – e.g., ignoring the “at least two” rule. | ❌ **Trick edge‑case** – a word that is itself in the dictionary must **not** be counted unless it’s formed by *other* words. |
| ✔️ **Runs in sub‑quadratic time** with proper caching. | ❌ **Mis‑use of recursion** can blow the stack for long words. | ❌ **Wrong DP base case** (treating the whole word as a single piece) leads to false positives. |
| ✔️ **Can be solved in any language** – good for showcasing versatility. | ❌ **Testing breadth** – need to consider all words, not just the longest. | ❌ **Memory blow‑up** if you build a full trie of all words and forget to prune it. |

---

## 3. The Core Idea – “Word‑Break” DP

1. **Sort the words by length** – ensures we always build longer words from already‑checked shorter ones.  
2. Keep a `Set<String>` of all words that are *valid* concatenated words or “base words”.  
3. For each word `w`:
   - Run a DP that checks if it can be split into two or more words from the set.  
   - If `true`, add `w` to the set and to the answer list.  
4. Return the answer list.

### DP Details

Let `dp[i]` be `true` if the prefix `w[0..i-1]` can be segmented.  
Initialize `dp[0] = true`.  
For every `i` from `1` to `w.length`:
- Scan all `j < i`.  
- If `dp[j] == true` **and** `w[j..i-1]` is in the dictionary, set `dp[i] = true`.  

At the end, `dp[w.length]` tells us whether the entire word can be segmented.  
We must also ensure that at least **two** pieces are used: this is naturally satisfied because the DP starts from `dp[0]` and a single word will never set `dp[w.length] = true` unless it was already in the set.

### Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Building set | O(N) | O(N) |
| Sorting | O(N log N) |
| DP per word | O(L²) (L ≤ 30) | O(L) |

With the constraints (`sum(L) ≤ 10⁵`) this easily fits into the limits.

---

## 4. Reference Implementations

> All codes are **self‑contained**, include necessary imports, and are ready to run on LeetCode.

---

### 4.1 Java

```java
import java.util.*;

class Solution {
    public List<String> findAllConcatenatedWordsInADict(String[] words) {
        List<String> result = new ArrayList<>();
        Set<String> dict = new HashSet<>();
        // Sort words by length ascending
        Arrays.sort(words, Comparator.comparingInt(String::length));

        for (String word : words) {
            if (word.isEmpty()) continue;          // defensive
            if (canForm(word, dict)) result.add(word);
            dict.add(word);                        // word becomes a building block
        }
        return result;
    }

    private boolean canForm(String word, Set<String> dict) {
        if (dict.isEmpty()) return false;
        int n = word.length();
        boolean[] dp = new boolean[n + 1];
        dp[0] = true;
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                if (!dp[j]) continue;
                if (dict.contains(word.substring(j, i))) {
                    dp[i] = true;
                    break;          // found a split, no need to check further j's
                }
            }
        }
        return dp[n];
    }
}
```

> **Why this works** – The set only contains words *strictly shorter* than the current one because we sort by length.  
> **Edge‑case** – If the word itself is already in the set (due to duplicate input, which the problem forbids), the algorithm will still return `true` only if it can be segmented using other words.

---

### 4.2 Python

```python
from typing import List

class Solution:
    def findAllConcatenatedWordsInADict(self, words: List[str]) -> List[str]:
        words.sort(key=len)
        result, dictionary = [], set()

        for word in words:
            if not word:               # safety guard
                continue
            if self._can_form(word, dictionary):
                result.append(word)
            dictionary.add(word)

        return result

    def _can_form(self, word: str, dictionary: set) -> bool:
        if not dictionary:
            return False
        n, dp = len(word), [False] * (n + 1)
        dp[0] = True
        for i in range(1, n + 1):
            for j in range(i):
                if not dp[j]:
                    continue
                if word[j:i] in dictionary:
                    dp[i] = True
                    break
        return dp[n]
```

> **Python‑specific tips** –  
> * Use `word[j:i]` slicing – it is O(1) because of Python’s substring implementation.  
> * The inner loop exits early with `break` once a split is found – this keeps the runtime low.

---

### 4.3 C++

```cpp
#include <vector>
#include <string>
#include <unordered_set>
#include <algorithm>

class Solution {
public:
    std::vector<std::string> findAllConcatenatedWordsInADict(std::vector<std::string>& words) {
        std::vector<std::string> result;
        std::unordered_set<std::string> dict;

        // Sort words by length (ascending)
        std::sort(words.begin(), words.end(),
                  [](const std::string& a, const std::string& b) {
                      return a.size() < b.size();
                  });

        for (const std::string& word : words) {
            if (word.empty()) continue;   // defensive guard
            if (canForm(word, dict)) result.push_back(word);
            dict.insert(word);             // word becomes a building block
        }
        return result;
    }

private:
    bool canForm(const std::string& word, const std::unordered_set<std::string>& dict) {
        if (dict.empty()) return false;
        int n = word.size();
        std::vector<bool> dp(n + 1, false);
        dp[0] = true;

        for (int i = 1; i <= n; ++i) {
            for (int j = 0; j < i; ++j) {
                if (!dp[j]) continue;
                if (dict.find(word.substr(j, i - j)) != dict.end()) {
                    dp[i] = true;
                    break;  // found a split
                }
            }
        }
        return dp[n];
    }
};
```

> **C++ note** – `word.substr(j, i - j)` creates a new string each time; however, since word length ≤ 30 this is negligible.  
> If you need absolute micro‑optimisation, you could implement a Trie and avoid substring allocation.

---

## 5. Interview‑Ready Tips

| Tip | Why it matters |
|-----|----------------|
| **Explain the sorting step** | Shows you understand the *dependency* (longer words depend on shorter ones). |
| **Show the DP table** | Visualises the “word‑break” idea; interviewers love seeing a matrix. |
| **Discuss edge‑cases** | Empty string, single‑character words, duplicates (even though constraints forbid). |
| **Mention time/space complexity** | Interviewers expect you to justify why your solution is efficient. |
| **If stuck, ask clarifying questions** | E.g., “Can a word be concatenated with itself multiple times?” – This demonstrates problem‑solving skills. |

---

## 6. SEO‑Optimised Blog Outline

> *Use this outline on your personal blog or portfolio to rank for “LeetCode 472 Concatenated Words” and “Java DP interview problem”.*

1. **Title** –  
   `LeetCode 472 – Concatenated Words: Hard Problem Explained (Java/Python/C++)`

2. **Meta Description** –  
   `Solve LeetCode 472 – find all concatenated words in a dictionary. Read the detailed Java, Python, and C++ solutions, plus interview tips and time‑complexity analysis. Boost your coding interview skills now!`

3. **Headers**  
   - `# LeetCode 472 – Concatenated Words: Hard Problem`  
   - `## Problem Statement & Constraints`  
   - `## Why This Question Is Hard`  
   - `## The Word‑Break DP Strategy`  
   - `### Complexity Analysis`  
   - `## Reference Solutions`  
     - `### Java Implementation`  
     - `### Python Implementation`  
     - `### C++ Implementation`  
   - `## Interview‑Ready Discussion`  
   - `## Takeaway & Further Reading`

4. **Internal & External Links**  
   - Link to `https://leetcode.com/problems/concatenated-words/`  
   - Link to your GitHub repo containing the code.  
   - Reference other LeetCode hard DP problems (e.g., 139 “Word Break”, 17 “Letter Combinations”).

5. **Call‑to‑Action** –  
   `Want more interview prep? Subscribe for weekly LeetCode walkthroughs.`

---

## 7. Final Takeaway

- **Sorting by length** + **DP “word‑break”** + **set of building blocks** = a clean, linear‑ish solution.  
- The question forces you to think about **dependency ordering** and **base‑case handling**, which are critical in any hard DP interview.  
- With the three language variants, you’re ready to showcase both algorithmic insight and language fluency—an ideal portfolio entry for senior software‑engineering interviews.

Happy coding, and may your interview stack be ever true!

---


> *If you want to learn more about DP patterns, check out our series on LeetCode “Word‑Break” and “Palindrome Partitioning”.* 

--- 

*Author: [Your Name] – Software Engineer & Interview Coach.  
Keywords: LeetCode 472, Concatenated Words, Hard DP Problem, Java DP, Python DP, C++ DP, Interview Tips.* 
---


> **TL;DR** – Sort, DP, set. O(N log N + ΣL²). Three ready‑to‑paste solutions. Interview‑ready explanation above. 🚀
---
This completes the **“Hard” LeetCode 472 – Concatenated Words** deep dive, with **reference code** and **interview preparation** ready for your next coding interview.