---
title: LeetCode 2506. Count Pairs Of Similar Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap – LeetCode 2506 “Count Pairs Of Similar Strings”

> **Goal**  
> Given an array of lower‑case strings `words`, count the number of unordered pairs `(i, j)` (`i < j`) where the two words contain *exactly the same set of characters* (order and frequency do **not** matter).

| Example | Input | Output | Explanation |
|---------|-------|--------|-------------|
| 1 | `["aba","aabb","abcd","bac","aabc"]` | `2` | (0,1) and (3,4) share the same character set |
| 2 | `["aabb","ab","ba"]` | `3` | Every pair has the same set `{a,b}` |
| 3 | `["nba","cba","dba"]` | `0` | No pair shares the same character set |

> **Constraints**  
> `1 ≤ words.length ≤ 100`, `1 ≤ words[i].length ≤ 100`.  
> Only lowercase English letters.

---

## 2. Solution Overview – “Bitmask + Frequency Table”

The key observation: a word can be represented by a **bitmask** of 26 bits, one for each letter `a`‑`z`.  
*Bit i is 1* iff the word contains the character `chr('a' + i)`.

For example  
`"aba"` → mask `0b000…00110` (bits for `a` and `b` are set).

Two words are “similar” **iff** their masks are identical.  
Therefore:

1. Compute the mask for each word.  
2. Count how many times each mask has already appeared.  
3. For every new word with mask `m`, add the current count of `m` to the answer – that is exactly the number of new pairs it creates.  
4. Increment the count for `m`.

This yields **O(N·L)** time (`N` = number of words, `L` = average word length) and **O(K)** space (`K` = distinct masks, at most 26 → `2^26` but in practice ≤ N).

---

## 3. Code Implementations

### 3.1 Java

```java
import java.util.HashMap;

public class Solution {
    public int similarPairs(String[] words) {
        int answer = 0;
        HashMap<Integer, Integer> freq = new HashMap<>();

        for (String w : words) {
            int mask = 0;
            for (char c : w.toCharArray()) {
                mask |= 1 << (c - 'a');
            }
            answer += freq.getOrDefault(mask, 0);
            freq.merge(mask, 1, Integer::sum);
        }
        return answer;
    }

    // Simple main for quick sanity‑check
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.similarPairs(new String[]{"aba","aabb","abcd","bac","aabc"})); // 2
    }
}
```

### 3.2 Python 3

```python
from collections import Counter
from typing import List

class Solution:
    def similarPairs(self, words: List[str]) -> int:
        freq = Counter()
        ans = 0

        for w in words:
            mask = 0
            # Using a set avoids double work for repeated letters
            for ch in set(w):
                mask |= 1 << (ord(ch) - 97)
            ans += freq[mask]
            freq[mask] += 1
        return ans

# Quick test
if __name__ == "__main__":
    print(Solution().similarPairs(["aba","aabb","abcd","bac","aabc"]))  # 2
```

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int similarPairs(vector<string>& words) {
        unordered_map<int, int> freq;
        int ans = 0;
        for (const string& w : words) {
            int mask = 0;
            for (char c : w) {
                mask |= 1 << (c - 'a');
            }
            ans += freq[mask];
            freq[mask]++;          // increment after counting
        }
        return ans;
    }
};

// Simple test harness
int main() {
    Solution s;
    vector<string> words = {"aba","aabb","abcd","bac","aabc"};
    cout << s.similarPairs(words) << endl;   // 2
    return 0;
}
```

All three implementations share the same logic and run in linear time.

---

## 4. Blog Article – “The Good, the Bad, and the Ugly of Counting Similar String Pairs”

> **Title**: *Mastering LeetCode 2506: Bitmasking, HashMaps, and What Recruiters Really Look For*  
> **Meta Description**: *Learn how to crack LeetCode 2506 “Count Pairs Of Similar Strings” using bitmasking and hash tables. Get job‑ready interview tips, best practices, and pitfalls to avoid.*

---

### 4.1 Why This Problem Matters

- **Real‑world relevance**: Counting similar strings appears in data deduplication, spell‑checking, and search‑engine indexing.
- **Interview popularity**: It’s a frequent “medium”‑level LeetCode question that tests two core concepts:
  1. **Bit manipulation** – efficient encoding of sets.
  2. **Hash‑based counting** – classic “frequency table” pattern.

Recruiters love problems that force you to combine low‑level tricks with high‑level data‑structure knowledge.  

---

### 4.2 The Good – What Makes the Bitmask + HashMap Approach Shine

| Strength | Explanation |
|----------|-------------|
| **Linear Time** | `O(N·L)` (≤ 10⁴ operations) – far below limits for any interview scenario. |
| **Constant Extra Space per Word** | A 32‑bit integer suffices for the mask; the map size is bounded by `N`. |
| **Simplicity & Readability** | The code is one‑liner‑per‑word logic; easy to explain during an interview. |
| **Scalability** | Works for 10⁵ words if needed – just a matter of using 64‑bit masks or `BitSet`. |
| **Avoids O(N²)** | Naïve pairwise comparison would be 10⁴ checks; we reduce to a single scan. |

---

### 4.3 The Bad – Common Pitfalls & Edge Cases

| Pitfall | How to Avoid |
|---------|--------------|
| **Using the word itself as a key** | Two strings “abc” and “cba” are different objects – you need a *canonical* representation (the mask). |
| **Counting duplicates incorrectly** | Remember to add the *current* frequency before incrementing. |
| **Large character sets** | If the alphabet were > 26, a 32‑bit mask would overflow; switch to `BitSet` or a `String` hash. |
| **Misinterpreting “same characters”** | The problem ignores frequency. A set of characters is enough; use `set(word)` (Python) or deduplicate in C++. |
| **Ignoring case‑sensitivity** | All inputs are lowercase in the constraints, but defensive coding (e.g., `c - 'a'`) is safe. |

---

### 4.4 The Ugly – When Things Go Wrong

- **Time‑out in a naive solution**: Pairwise comparisons (`O(N²)`) blow up even for `N=100`.  
  *Lesson*: Always think in terms of *hashing* or *bitsets* for set‑membership problems.

- **Hash‑collision overhead**: Unordered maps in C++ can degrade to `O(N)` per insert in pathological cases.  
  *Lesson*: Use `unordered_map` with a reserve size (`freq.reserve(words.size())`) or switch to `std::unordered_map<int, int>` with custom hash if needed.

- **Mis‑reading the definition of “similar”**: Some candidates count repeated letters as part of the signature.  
  *Lesson*: Clarify the spec—**“set of characters”** means frequency is irrelevant.

- **Wrong mask width**: Using a `char` or `short` for the mask may truncate bits for letters beyond `z`.  
  *Lesson*: Use at least a 32‑bit integer (or 64‑bit if you support extended alphabets).

---

### 4.5 Interview‑Ready Tips

1. **Explain the core idea first**: “We encode the set of characters as a bitmask; identical masks mean similar strings.”
2. **Mention time/space**: “The scan is `O(N·L)`; map holds at most `N` entries.”
3. **Show a quick sketch**: Write the mask calculation and map update on a whiteboard or the IDE.
4. **Discuss edge cases**: “What if the alphabet is larger? We can use `BitSet`.”  
5. **Walk through the counting logic**: Emphasise adding the current frequency *before* incrementing.
6. **Offer an alternative**: “If the alphabet were huge, we could sort the characters of each word and use that string as a key.”  
   *Recruiters appreciate showing knowledge of multiple approaches.*

---

### 4.6 Final Words

LeetCode 2506 is more than a number; it’s a *micro‑domain* that demonstrates your ability to think in bits and hash tables – skills recruiters are looking for in every mid‑level software engineer role. Master the **bitmask + frequency table** pattern, practice explaining it clearly, and you’ll pass this question – and many similar ones – with flying colors.

--- 

### 4.7 Further Reading & Practice

- **Bit Manipulation Cheat Sheet** – https://www.hackerrank.com/skills-directory/bit-manipulation  
- **HashMap Tricks in Java** – https://www.baeldung.com/java-hashmap  
- **LeetCode 2506 Solutions & Discussions** – https://leetcode.com/problems/count-pairs-of-similar-strings/  

Happy coding, and good luck with your next interview!