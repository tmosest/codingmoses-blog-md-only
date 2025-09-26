---
title: LeetCode 2085. Count Common Words With One Occurrence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Count Common Words With One Occurrence – LeetCode 2085  
**Easy** | **Time O(n + m)** | **Space O(n + m)**  

> **Keywords:** LeetCode 2085, Count Common Words With One Occurrence, hash map, Java solution, Python solution, C++ solution, interview preparation, coding interview, algorithmic thinking

---

## TL;DR  
We need to count the number of distinct strings that appear **exactly once** in *both* input arrays.  
The cleanest solution is a single `HashMap` (or `unordered_map` / `dict`) that stores frequencies from the first array and then updates counts while scanning the second array.  
Complexity is linear in the total size of the two arrays and the memory usage is proportional to the number of unique words.

---

## Problem Recap

```text
Input:  words1 = ["leetcode","is","amazing","as","is"]
        words2 = ["amazing","leetcode","is"]
Output: 2
```

Only `"leetcode"` and `"amazing"` satisfy the condition.  

---

## Why a HashMap Is the Right Tool

1. **Frequency counting** – We need to know how many times each word occurs in each array.  
2. **O(1) lookup and update** – `HashMap` lets us add and adjust counts in constant time.  
3. **Compact solution** – No need for two separate maps; we can fold the logic into a single pass after the first frequency pass.

---

## The “Good” Solution – One HashMap

1. **First pass** – Count frequencies of every word in `words1`.  
2. **Second pass** – For each word in `words2`:  
   * If it existed in the first map and its count was `1`, set it to `0` (valid candidate).  
   * If it existed but its count was > 1, set it to `-1` (invalid).  
3. **Final tally** – Count how many entries have value `0`.  

Why does setting to `-1` help? It guarantees that any word that appears multiple times in *either* array is *never* counted.

### Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int countWords(String[] words1, String[] words2) {
        Map<String, Integer> freq = new HashMap<>();

        // Count words in words1
        for (String w : words1) {
            freq.put(w, freq.getOrDefault(w, 0) + 1);
        }

        // Update counts based on words2
        for (String w : words2) {
            if (freq.containsKey(w)) {
                int cur = freq.get(w);
                if (cur == 1) {          // appears once in words1
                    freq.put(w, 0);      // candidate
                } else if (cur > 0) {    // appears more than once in words1
                    freq.put(w, -1);     // invalidate
                }
                // cur < 0 means already invalid, do nothing
            }
        }

        // Count valid entries (value == 0)
        int ans = 0;
        for (int val : freq.values()) {
            if (val == 0) ans++;
        }
        return ans;
    }
}
```

### Python

```python
from collections import Counter

class Solution:
    def countWords(self, words1: list[str], words2: list[str]) -> int:
        freq = Counter()
        for w in words1:
            freq[w] += 1

        for w in words2:
            if w in freq:
                if freq[w] == 1:
                    freq[w] = 0          # candidate
                elif freq[w] > 0:
                    freq[w] = -1         # invalidate

        return sum(1 for v in freq.values() if v == 0)
```

### C++

```cpp
#include <unordered_map>
#include <vector>
#include <string>

class Solution {
public:
    int countWords(std::vector<std::string>& words1,
                   std::vector<std::string>& words2) {
        std::unordered_map<std::string, int> freq;
        for (const auto& w : words1) freq[w]++;   // count words1

        for (const auto& w : words2) {
            auto it = freq.find(w);
            if (it != freq.end()) {
                if (it->second == 1)
                    it->second = 0;      // candidate
                else if (it->second > 0)
                    it->second = -1;     // invalidate
            }
        }

        int ans = 0;
        for (const auto& p : freq)
            if (p.second == 0) ++ans;
        return ans;
    }
};
```

---

## The “Bad” Approach – Two Separate Maps

```java
Map<String,Integer> m1 = new HashMap<>();
Map<String,Integer> m2 = new HashMap<>();
// fill both maps, then iterate over one and check counts in the other
```

**Drawbacks**

- Extra memory (`O(n + m)` each).  
- Two separate loops, though still linear.  
- Slightly more code to keep in sync.

---

## The “Ugly” Approach – Brute Force

```java
int count = 0;
for (String w1 : words1) {
    if (Arrays.stream(words2).filter(w2 -> w2.equals(w1)).count() == 1
        && Arrays.stream(words1).filter(w -> w.equals(w1)).count() == 1)
        count++;
}
```

**Problems**

- O(n m) time, far too slow for 1000 elements.  
- Repeatedly scans arrays; very hard to read and maintain.  
- Prone to subtle bugs (duplicate counting, off‑by‑one errors).

---

## Edge Cases & Testing

| Case | words1 | words2 | Expected |
|------|--------|--------|----------|
| Empty array not allowed by constraints | — | — | — |
| All words same, > 1 occurrence | `["a","a"]` | `["a"]` | 0 |
| All words distinct, one occurrence | `["a","b"]` | `["b","c"]` | 1 |
| Large random arrays | (1000 elements) | (1000 elements) | O(1) time per element |

Always test the intersection, uniqueness, and boundary limits.

---

## Interview‑Ready Tips

1. **Explain the idea first** – Mention frequency counting, hash maps.  
2. **Walk through the algorithm** – Show the two passes and why `-1` works.  
3. **Discuss complexity** – O(n + m) time, O(n + m) space.  
4. **Mention edge cases** – e.g., words appearing multiple times in either array.  
5. **Optional optimization** – Use a single map if you want to be fancy, but keep the code readable.

---

## SEO‑Optimized Takeaway

If you’re preparing for coding interviews and want to land a job, mastering **LeetCode 2085: Count Common Words With One Occurrence** is a great showcase of:

- **Hash map proficiency**  
- **Efficient O(n + m) solutions**  
- **Clear reasoning**  

Add this problem to your portfolio, and mention the **one‑hashmap** trick in your resume or LinkedIn profile. Recruiters love concise, elegant solutions that solve the problem in linear time.

Happy coding, and good luck on your next interview!