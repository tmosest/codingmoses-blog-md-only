---
title: LeetCode 1181. Before and After Puzzle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1181 – Before and After Puzzle  
### 3‑Way Code & a “How‑To” Blog Post for the Next Interview

---

### 1. Problem Recap

Given a list of phrases, build every possible *Before & After* puzzle:

* A puzzle is formed by merging **two** different phrases.  
* Only the **last word** of the first phrase and the **first word** of the second phrase are merged (the word itself appears only once).  
* The order of the two phrases matters – both `i → j` and `j → i` must be considered.  
* The output is a **sorted** list of **unique** puzzle strings.

> **Example**  
> `["writing code", "code rocks"]` → `"writing code rocks"`

---

### 2. High‑Level Strategy

1. **Pre‑process** each phrase to store its first and last word.  
2. **Map**:
   * `firstWord → List<phrases that start with that word>`
   * `lastWord  → List<phrases that end with that word>`
3. For every phrase `p` (as the *first* phrase)  
   * look up all phrases that start with `p.lastWord`.  
   * For each match `q`, merge:  
     `p + " " + q.substring(q.firstWord.length()+1)`  
     (drop the duplicated word from `q`).
4. Insert each result into a `TreeSet` (automatically dedupes & sorts).  
5. Convert the set to a list and return.

This is **O(n²)** in the worst case, but with `n ≤ 100` it is trivial.  
Memory usage is `O(n)`.

---

### 3. Code Implementations

Below you’ll find ready‑to‑paste solutions in **Java, Python, and C++**.  
All follow the same algorithmic idea and compile with the standard library only.

---

#### 3.1 Java

```java
import java.util.*;

public class Solution {
    public List<String> beforeAndAfterPuzzles(String[] phrases) {
        // Maps for quick lookup
        Map<String, List<String>> firstWordMap = new HashMap<>();
        Map<String, List<String>> lastWordMap = new HashMap<>();

        // Pre‑process each phrase
        for (String p : phrases) {
            String[] parts = p.split(" ");
            String first = parts[0];
            String last = parts[parts.length - 1];

            firstWordMap.computeIfAbsent(first, k -> new ArrayList<>()).add(p);
            lastWordMap.computeIfAbsent(last, k -> new ArrayList<>()).add(p);
        }

        // TreeSet gives deduplication + sorting
        Set<String> result = new TreeSet<>();

        for (String firstPhrase : phrases) {
            String[] firstParts = firstPhrase.split(" ");
            String lastWord = firstParts[firstParts.length - 1];

            List<String> candidates = firstWordMap.getOrDefault(lastWord, Collections.emptyList());
            for (String secondPhrase : candidates) {
                if (firstPhrase.equals(secondPhrase)) continue;   // i != j

                String[] secondParts = secondPhrase.split(" ");
                // Remove duplicated word
                String merged = firstPhrase + " " +
                                String.join(" ", Arrays.copyOfRange(secondParts, 1, secondParts.length));
                result.add(merged);
            }
        }

        return new ArrayList<>(result);
    }
}
```

---

#### 3.2 Python

```python
from collections import defaultdict
from typing import List

class Solution:
    def beforeAndAfterPuzzles(self, phrases: List[str]) -> List[str]:
        first_map = defaultdict(list)   # first word -> phrases
        last_map  = defaultdict(list)   # last word  -> phrases

        # Pre‑processing
        for p in phrases:
            words = p.split()
            first_map[words[0]].append(p)
            last_map[words[-1]].append(p)

        res = set()

        for p in phrases:
            last_word = p.split()[-1]
            for q in first_map.get(last_word, []):
                if p == q:               # i != j
                    continue
                merged = p + " " + " ".join(q.split()[1:])
                res.add(merged)

        return sorted(res)
```

---

#### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<string> beforeAndAfterPuzzles(vector<string>& phrases) {
        unordered_map<string, vector<string>> first, last;
        unordered_set<string> result;

        // Helper to split
        auto split = [](const string &s) {
            vector<string> words;
            string cur;
            istringstream iss(s);
            while (iss >> cur) words.push_back(cur);
            return words;
        };

        // Pre‑process
        for (const string &p : phrases) {
            auto words = split(p);
            first[words.front()].push_back(p);
            last[words.back()].push_back(p);
        }

        // Build puzzles
        for (const string &p : phrases) {
            auto w = split(p);
            const string &lastWord = w.back();
            auto it = first.find(lastWord);
            if (it == first.end()) continue;

            for (const string &q : it->second) {
                if (p == q) continue;   // i != j
                auto wq = split(q);
                string merged = p;
                for (size_t i = 1; i < wq.size(); ++i) {
                    merged += " " + wq[i];
                }
                result.insert(merged);
            }
        }

        vector<string> ans(result.begin(), result.end());
        sort(ans.begin(), ans.end());
        return ans;
    }
};
```

---

### 4. Blog Post: “Before & After Puzzle – LeetCode 1181: The Good, The Bad & The Ugly”

---

#### Title (SEO‑Friendly)

**“Cracking LeetCode 1181 – Before & After Puzzle: Java/Python/C++ Solutions + Interview Tips”**

*Keywords: LeetCode, Before and After Puzzle, interview prep, Java solution, Python solution, C++ solution, algorithm, string manipulation, coding interview, programming interview, algorithmic problem.*

---

#### Introduction

> **“In coding interviews, LeetCode’s *Before & After Puzzle* (Problem 1181) is a deceptively simple string‑manipulation challenge that tests your ability to think about *word boundaries*, *hash‑mapping*, and *deduplication*. In this article, we’ll walk through the problem, show clean solutions in Java, Python, and C++, and discuss pitfalls that can trip up even seasoned interviewees.”**

---

#### Problem Summary

- **Input** – Array of `phrases` (each is a single‑spaced string of lowercase English letters).  
- **Goal** – For every ordered pair `(i, j)` where `i ≠ j`, if the last word of `phrases[i]` equals the first word of `phrases[j]`, merge them into a single phrase:  
  ```
  "phrase[i] phrase[j]"  // but drop the duplicated word
  ```
- **Output** – All distinct merged phrases, sorted lexicographically.

---

#### Why Is It “Good” for Interviews?

1. **Clear Constraints** – `n ≤ 100`, `len ≤ 100`.  Perfect for teaching *brute force* vs *optimized* thinking.  
2. **Encourages Hashing** – The core idea is to match words quickly using hash maps.  
3. **String Handling** – A good test of how you split, join, and avoid off‑by‑one errors.  

---

#### The “Bad” – Common Pitfalls

| Mistake | Why it Happens | Fix |
|---------|----------------|-----|
| **Using nested loops naively** | O(n²) is fine here, but many candidates add unnecessary complexity. | Keep O(n²) but pre‑compute first/last word maps to avoid re‑splitting. |
| **Including self‑puzzle** (`i == j`) | Many solutions accidentally merge a phrase with itself. | Explicitly skip when indices or strings match. |
| **Duplicated words in merge** | Forgetting to strip the first word from the second phrase. | Remove it via `substring` or `split[1:]`. |
| **Not deduplicating** | Different pairs may produce the same merged string. | Use a `Set` or `TreeSet` before converting to list. |
| **Wrong sorting** | Returning unsorted list. | Use built‑in sort or `TreeSet`. |

---

#### The “Ugly” – Edge Cases

1. **Single‑word phrases** – “a” is both first and last word.  
2. **Duplicate phrases in input** – e.g., `["ab ba","ab ba"]`.  
3. **Large number of duplicates** – “a a a a” → many pairings but one unique result.  
4. **Very long phrases** – up to 100 characters; avoid O(length²) string operations.  

---

#### Step‑by‑Step Solution

1. **Split each phrase into words** – `split(" ")`.  
2. **Record**:
   * `firstWordMap[first] → list of phrases starting with that word`
   * `lastWordMap[last]  → list of phrases ending with that word`  
   (Both can be `HashMap<String, List<String>>` in Java, `defaultdict(list)` in Python, or `unordered_map<string, vector<string>>` in C++.)
3. **For every phrase `p` as the first part**:
   * `last = p.lastWord`
   * For each candidate `q` in `firstWordMap[last]` (where `q != p`):
     * Merge: `p + " " + q.substring(q.firstWord.length() + 1)` (Java) or similar in other languages.
4. **Insert into a `Set`** to guarantee uniqueness and order (a `TreeSet` or sorted set).
5. **Return** the sorted list.

---

#### Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Pre‑processing | `O(n * w)` (`w` = average words per phrase) | `O(n)` |
| Pairing & merging | `O(n² * w)` (worst case, but `n ≤ 100`) | `O(n²)` for the set of results |
| Sorting | `O(k log k)` where `k` = number of unique results | `O(k)` |

With the given constraints this is comfortably fast.

---

#### Code Snippets (Ready for Interview)

*Java* – shown earlier.  
*Python* – shown earlier.  
*C++* – shown earlier.

> **Tip:** In interviews, you can first show the brute‑force double loop (clear and correct), then optimize by hashing. This demonstrates both correctness and efficiency.

---

#### Final Thoughts

*Before & After Puzzle* is a **gold‑mine** for interviewers: it’s short, covers essential concepts, and leaves room for a candidate to talk about data structures, edge‑case handling, and code style.  

**Practice Tips:**

1. Implement all three solutions yourself; being fluent in Java, Python, and C++ shows versatility.  
2. Run your code on the sample inputs (and some edge cases you create).  
3. Explain the logic aloud as you write the code—interviewers love clarity of thought.

Good luck, and may your puzzles always “merge” nicely!  

---

#### About the Author

> *[Your Name]* is a seasoned software engineer who has helped teams win coding interviews on LeetCode. Follow me for more algorithm walkthroughs, interview prep guides, and career‑building insights.  

--- 

Happy coding—and happy interviewing!