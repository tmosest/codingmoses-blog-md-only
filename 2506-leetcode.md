---
title: LeetCode 2506. Count Pairs Of Similar Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 2506 – *Count Pairs Of Similar Strings*  
> **Java, Python & C++ solutions + a full‑blown interview‑ready blog post**

---

## Table of Contents

| Section | Description |
|---------|-------------|
| 1️⃣ Problem Statement | What we’re asked to do |
| 2️⃣ Intuition | The “aha!” moment |
| 3️⃣ Algorithm | Step‑by‑step |
| 4️⃣ Implementation | Code in Java, Python and C++ |
| 5️⃣ Complexity Analysis | How fast & how much memory |
| 6️⃣ Edge Cases & Testing | Avoid pitfalls |
| 7️⃣ Alternatives | Brute‑force, set‑based, sorting |
| 8️⃣ Good / Bad / Ugly | Trade‑offs in real interviews |
| 9️⃣ Blog Article | SEO‑friendly write‑up |
| 🔗 Links & Resources | Further reading |

---

## 1️⃣ Problem Statement

> **LeetCode 2506 – Count Pairs Of Similar Strings**  
> `public int similarPairs(String[] words)`

You’re given an array `words` of lowercase English words.  
Two words are **similar** *iff* they contain exactly the same set of characters (order & frequency don’t matter).

Return the number of index pairs `(i, j)` with `0 ≤ i < j < words.length` such that `words[i]` and `words[j]` are similar.

**Constraints**

* `1 ≤ words.length ≤ 100`
* `1 ≤ words[i].length ≤ 100`
* `words[i]` consists of only lowercase letters.

---

## 2️⃣ Intuition

The key observation: *the exact characters that appear in a word matters, not how many times they appear*.  
So we can compress each word into a **signature** that records “does this word contain `'a'`? “, “does it contain `'b'`?“, …,“does it contain `'z'`?”.

The natural way in programming contests is a **bitmask**:

```
bit 0  -> 'a'
bit 1  -> 'b'
...
bit 25 -> 'z'
```

For a word, we set a bit when the letter appears.  
Two words are similar **iff** their bitmasks are equal.

Once we can generate a unique integer for each word, we only need to count how many words share the same mask.  
If a mask has `k` words, it contributes `k*(k-1)/2` pairs.

---

## 3️⃣ Algorithm (Linear‑time “mask + hashmap”)

1. Initialise an empty hash map `freq` → *mask* → *occurrence count*.
2. For every word in `words`
   * Build its bitmask `mask`.
   * Add `freq[mask]` to the answer (all previous words with the same mask form a new pair).
   * Increment `freq[mask]`.
3. Return the accumulated answer.

**Why it works**

*Each time we encounter a word, every previous word that had the same mask is a valid pair.*  
Thus we never double‑count and we never need to compare the word with every other word again.

---

## 4️⃣ Implementation

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.

> ⚠️ All three codes are *O(n × m)* time (n = number of words, m = max word length) and *O(n)* extra space.

---

### Java

```java
import java.util.HashMap;

class Solution {
    public int similarPairs(String[] words) {
        int answer = 0;
        HashMap<Integer, Integer> freq = new HashMap<>();

        for (String word : words) {
            int mask = 0;
            for (char c : word.toCharArray()) {
                mask |= 1 << (c - 'a');          // set bit for this letter
            }
            answer += freq.getOrDefault(mask, 0); // pairs with earlier same‑mask words
            freq.merge(mask, 1, Integer::sum);   // increment frequency
        }
        return answer;
    }
}
```

---

### Python 3

```python
from typing import List
from collections import defaultdict

class Solution:
    def similarPairs(self, words: List[str]) -> int:
        answer = 0
        freq = defaultdict(int)

        for word in words:
            mask = 0
            for ch in set(word):              # `set` avoids re‑setting the same bit
                mask |= 1 << (ord(ch) - ord('a'))
            answer += freq[mask]
            freq[mask] += 1

        return answer
```

> *Tip:* `set(word)` removes duplicate characters and makes the inner loop at most 26 iterations, even if the word is long.

---

### C++

```cpp
#include <vector>
#include <string>
#include <unordered_map>
using namespace std;

class Solution {
public:
    int similarPairs(vector<string>& words) {
        int ans = 0;
        unordered_map<int, int> freq;

        for (const string &word : words) {
            int mask = 0;
            for (char c : word) {
                mask |= 1 << (c - 'a');
            }
            ans += freq[mask];
            ++freq[mask];
        }
        return ans;
    }
};
```

---

## 5️⃣ Complexity Analysis

| Metric | Value |
|--------|-------|
| **Time** | `O(n · m)` – one pass over all characters |
| **Space** | `O(n)` – hash map of masks (at most `n` distinct masks) |
| **Auxiliary** | `O(1)` – bitmask uses a single `int` |

With `n ≤ 100` and `m ≤ 100`, this solution is trivial for modern machines, but the pattern scales to larger inputs too.

---

## 6️⃣ Edge Cases & Testing

| Test | Description | Expected Result |
|------|-------------|-----------------|
| `["a"]` | Single word | `0` |
| `["a","a"]` | Two identical words | `1` |
| `["ab","ba"]` | Same letters, different order | `1` |
| `["abc","def"]` | No common characters | `0` |
| `["ab","abc","bac","cba"]` | 3 words share same mask | `3` (C(3,2)) |
| `["aaa","aa","aaaa"]` | All have same mask `'a'` | `3` |

Run the provided code on these cases; all return the correct number of similar pairs.

---

## 7️⃣ Alternatives

| Approach | Complexity | Comments |
|----------|------------|----------|
| **Brute‑force** (compare every pair) | `O(n² · m)` | Simple, but unnecessary for `n ≤ 100`. |
| **Set‑based signature** (convert each word to `set(word)` and use it as key) | `O(n · m)` | Works but hashable sets can be expensive; bitmask is faster and uses only integers. |
| **Sorting** (sort each word and compare sorted strings) | `O(n · (m log m + m))` | Good for very long words; bitmask stays linear. |
| **Frequency table** (26‑int array per word) | `O(n · 26)` | Same as bitmask; memory heavier. |

---

## 8️⃣ Good / Bad / Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time** | Linear with bitmask; fast on large inputs | Brute‑force is `O(n²)` | No major pitfalls in the linear solution |
| **Space** | Only a few integers per word | Brute‑force needs O(1) but `O(n²)` comparisons | Unnecessary arrays or strings increase memory |
| **Readability** | Short, clear code | Brute‑force code is easy to understand but slow | Using bit operations may look opaque to newcomers |
| **Extensibility** | Works for any alphabet size (just change bit count) | Brute‑force doesn't scale | Harder to debug if bit logic is wrong |
| **Interview Score** | ★★★★ (efficient + clean) | ★★ (inefficient) | ★ (over‑engineering or bugs) |

**Bottom line:** In an interview, the bitmask + hashmap solution is the sweet spot—compact, fast, and shows familiarity with bit tricks.

---

## 9️⃣ Blog Article – SEO‑Optimized

> **Title**  
> “Master LeetCode 2506: Count Pairs Of Similar Strings – Java, Python, C++ & Interview Tips”

> **Meta‑Description**  
> “Solve LeetCode 2506 in 3 lines! Get Java, Python, and C++ solutions using bitmask & hashmap. Learn the algorithm, test cases, and interview‑ready discussion.”

> **Keywords**  
> LeetCode 2506, Count Pairs Of Similar Strings, interview coding, bitmask, hashmap, Java, Python, C++, algorithm, time complexity, space complexity, coding interview, job interview tips.

---

### Full Article

> #### 🎓 How to Ace LeetCode 2506 – Count Pairs Of Similar Strings  
> 
> Are you prepping for a *software engineer* interview?  
> One of the classic LeetCode problems that trip people up is **2506 – Count Pairs Of Similar Strings**.  
> This post shows you how to solve it in **Java, Python, and C++**—all in linear time—plus a ready‑to‑copy code snippet for every language.  
> We also dive into the algorithmic intuition, complexity analysis, edge‑case testing, and *why* this solution is a perfect interview answer.

---

### 🚀 Problem Summary

> Given an array of words, count how many pairs contain exactly the same set of letters.  
> Example: `["ab", "ba", "xyz"]` → 1 pair (`"ab"` & `"ba"`).

---

### 🧠 Why Bitmask?

* Only 26 letters → a 32‑bit integer is enough.  
* “Does the word contain `'c'`?” → set bit 2.  
* Two words are similar ↔ their integer masks are equal.

---

### 📈 Efficient Algorithm

1. **Mask each word** (`mask |= 1 << (c - 'a')`).  
2. **Hash it**: `freq[mask]` → how many earlier words had that mask.  
3. **Accumulate answer** while iterating.  
4. **Return**.

This is **O(n·m)** time, **O(n)** space.

---

### 🧪 Ready‑to‑Run Code

*Java* | *Python* | *C++*
--- | --- | ---
```java
// Java solution (mask + hashmap)
class Solution {
    public int similarPairs(String[] words) { /* ... */ }
}
``` | ```python
# Python solution
class Solution:
    def similarPairs(self, words: List[str]) -> int: /* ... */
``` | ```cpp
// C++ solution
class Solution {
public:
    int similarPairs(vector<string>& words) { /* ... */ }
};
```

---

### 🔍 Complexity

* **Time**: Linear – `O(n·m)` (fast even for huge inputs).  
* **Space**: `O(n)` – small hashmap of masks.

---

### 🎯 Edge Cases

| Case | Why it matters | ✔️ Pass |
|------|----------------|--------|
| Only one word | 0 pairs | ✅ |
| All words identical | All share the same mask | ✅ |
| No common letters | 0 pairs | ✅ |
| Repeated letters inside a word | mask only cares about presence | ✅ |

---

### 🛠️ What Interviewers Look For

1. **Efficiency** – linear vs quadratic.  
2. **Clean code** – single `int` mask, concise hashmap usage.  
3. **Explanations** – be ready to talk about “why a bitmask works” and “why we add `freq[mask]` to the answer”.  
4. **Testing** – mention a few corner cases.  

---

### 📚 Final Thoughts

LeetCode 2506 is *simple yet subtle*.  
A linear mask‑hashmap solution proves you can:

* compress data efficiently (bit operations)  
* use a hashmap to avoid repeated work  
* think in terms of *unique signatures*  

All three language implementations are essentially the same logic, just expressed in idiomatic syntax.  

Good luck on your next interview, and remember: **speed + clarity = interview win!**

---

## 🔗 Links & Resources

| Resource | What it’s for |
|----------|---------------|
| Official LeetCode 2506 page | [https://leetcode.com/problems/count-pairs-of-similar-strings/](https://leetcode.com/problems/count-pairs-of-similar-strings/) |
| Bit manipulation tutorial | [GeeksforGeeks – Bit Manipulation](https://www.geeksforgeeks.org/bit-manipulation/) |
| Interview prep – coding challenges | [InterviewBit, HackerRank, CodeSignal] |
| YouTube walkthrough (Java + Python) | 🎥 *search “LeetCode 2506 Java solution”* |

---

> **Feel free to copy/paste any of the code blocks above into your editor and run the sample tests.** Happy coding!