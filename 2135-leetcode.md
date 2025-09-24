---
title: LeetCode 2135. Count Words Obtained After Adding a Letter - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🧩 LeetCode 2135 – “Count Words Obtained After Adding a Letter”

> **Tag**: `Medium` | `Hashing` | `Bitmask` | `Interview`

> **Complexity**:  
> *Time*: **O(n + m + 26 · maxLen)** → practically **O(n + m)**  
> *Space*: **O(n)** for the hash‑set of start‑word masks.

---

### 1️⃣ Problem Recap

You are given two arrays:

| `startWords` | `targetWords` |
|--------------|---------------|
| `["ant","act","tack"]` | `["tack","act","acti"]` |

For each target word you may:

1. **Add** *one* new lowercase letter **not already present** in the word.
2. **Re‑arrange** the letters arbitrarily.

You must decide how many target words can be produced from *any* start word using the above two steps.

**Note:** Start words are **never modified** – they only serve as sources.

---

### 2️⃣ Key Insight: Bit‑Masking

All words consist of *distinct* lowercase letters (`a‑z`).  
We can represent each word as a 26‑bit integer:

```
bit 0  → letter 'a'
bit 1  → letter 'b'
...
bit 25 → letter 'z'
```

* Example: `"act"` → bits 0 (a), 2 (c), 19 (t) → mask `1<<0 | 1<<2 | 1<<19`.

With this representation:

* A **start word** becomes a mask we store in a `HashSet<Integer>`.
* For a **target word** we compute its mask `tMask`.  
  Removing one character `c` is equivalent to toggling the bit of `c` off:
  ```java
  int prevMask = tMask ^ (1 << (c - 'a'));
  ```
  If `prevMask` exists in the start‑word set, the target word is reachable.

Because every word contains at most 26 letters, the inner loop (removing each letter) runs at most 26 times – negligible.

---

### 3️⃣ The Good

| ✅ | Why it shines |
|---|----------------|
| **O(1) lookup** – `HashSet` gives constant‑time membership test. |
| **No sorting** – bit‑masking is faster than sorting every string. |
| **Simplicity** – one integer per word, trivial arithmetic. |
| **Scalable** – works for the maximum input sizes (`5·10⁴` words). |

---

### 4️⃣ The Bad

| ⚠️ | Caveats |
|---|----------|
| **Assumes distinct letters** – the problem guarantees this, but in general you'd need to check. |
| **Mask overflow** – in Java an `int` holds 32 bits, so 26 bits are safe. In languages with smaller ints you must ensure capacity. |
| **Memory** – `HashSet<Integer>` of 50k entries is tiny (~2 MB), but if the input size grows dramatically it could become a concern. |

---

### 5️⃣ The Ugly

| ❌ | Pitfalls if you’re not careful |
|---|--------------------------------|
| **Character‑to‑bit conversion mistakes** – off‑by‑one errors (`'a'` → 0, not 1). |
| **Toggling wrong bit** – forgetting to **unset** the bit (use XOR, not OR). |
| **Double‑counting** – breaking after the first match is crucial; otherwise you’ll inflate the count. |
| **Ignoring the “must add one letter” rule** – you cannot match a target word to a start word that is already an anagram (e.g., `"act"` vs `"act"`). The mask‑removal trick guarantees exactly one letter is removed. |

---

## 6️⃣ Full Code

Below are **three** full, ready‑to‑compile solutions – Java, Python, C++.

> *All three use the same bit‑masking idea and have identical time/space characteristics.*

### 6.1 Java

```java
import java.util.*;

public class Solution {
    public int wordCount(String[] startWords, String[] targetWords) {
        // 1️⃣ Build set of start‑word masks
        Set<Integer> startSet = new HashSet<>();
        for (String s : startWords) {
            startSet.add(toMask(s));
        }

        int count = 0;
        // 2️⃣ For every target word try removing each letter
        for (String t : targetWords) {
            int tMask = toMask(t);
            boolean ok = false;
            for (char c : t.toCharArray()) {
                int removedMask = tMask ^ (1 << (c - 'a'));
                if (startSet.contains(removedMask)) {
                    ok = true;
                    break;          // stop after first success
                }
            }
            if (ok) count++;
        }
        return count;
    }

    // Helper: convert string to 26‑bit mask
    private int toMask(String word) {
        int mask = 0;
        for (char ch : word.toCharArray()) {
            mask |= 1 << (ch - 'a');
        }
        return mask;
    }

    // Driver for quick testing
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.wordCount(
                new String[]{"ant","act","tack"},
                new String[]{"tack","act","acti"}));   // → 2
    }
}
```

### 6.2 Python

```python
class Solution:
    def wordCount(self, startWords: List[str], targetWords: List[str]) -> int:
        # Build set of integer masks for start words
        start_set = {self.to_mask(s) for s in startWords}
        count = 0

        for t in targetWords:
            t_mask = self.to_mask(t)
            # Try removing each character once
            for ch in t:
                removed = t_mask ^ (1 << (ord(ch) - ord('a')))
                if removed in start_set:
                    count += 1
                    break   # only need one match

        return count

    @staticmethod
    def to_mask(word: str) -> int:
        mask = 0
        for ch in word:
            mask |= 1 << (ord(ch) - ord('a'))
        return mask
```

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int wordCount(vector<string>& startWords, vector<string>& targetWords) {
        unordered_set<int> startSet;
        for (const string &s : startWords)
            startSet.insert(toMask(s));

        int ans = 0;
        for (const string &t : targetWords) {
            int tMask = toMask(t);
            bool ok = false;
            for (char c : t) {
                int removed = tMask ^ (1 << (c - 'a'));
                if (startSet.count(removed)) { ok = true; break; }
            }
            if (ok) ++ans;
        }
        return ans;
    }

private:
    static int toMask(const string &w) {
        int mask = 0;
        for (char c : w) mask |= 1 << (c - 'a');
        return mask;
    }
};
```

---

## 7️⃣ Blog Article – “The Good, The Bad, and the Ugly of Solving LeetCode 2135”

> **Title**: *“Cracking LeetCode 2135: The Good, The Bad, and the Ugly of a Bit‑Mask Solution”*  
> **Meta‑Description**: Learn how to solve LeetCode 2135 “Count Words Obtained After Adding a Letter” in O(n + m) time. Discover the pitfalls, trade‑offs, and interview secrets that will help you ace your next coding interview.

---

### 📌 Introduction

Interviews love problems that test *your* ability to **think with sets** and **compress data**. LeetCode 2135 is one such “hash‑set + bit‑mask” puzzle that shows up in many hiring pipelines, especially for companies that value algorithmic clarity (e.g., Google, Amazon, Microsoft).  

Below we walk through the **complete thought process**—what works, what could trip you up, and the subtle mistakes that could cost you the interview.

---

### 🏁 Problem Overview

> **Given** two word lists, can you generate each target word by *adding* a new letter and *scrambling* the letters?  
> **Goal**: Count how many target words are attainable from any start word.

The constraints (≤ 50 000 words, distinct letters) hint that a *fast* lookup structure (like a hash‑set) is the right tool.

---

### 📐 The Core Idea – Bit‑Masking

*Why a mask?*  
- Each lowercase letter maps to a bit.  
- Distinctness → a word = a unique 26‑bit integer.  
- Set membership is **O(1)**.  

**Algorithm**  
1. Convert every start word → `mask`, store in `HashSet`.  
2. For each target word:  
   - Compute its mask `tMask`.  
   - For every character `c` in the word:  
     `prevMask = tMask ^ (1 << (c - 'a'))`.  
     If `prevMask` exists → this target word is reachable.  

The XOR trick guarantees *exactly one* letter is removed – precisely what the problem demands.

---

### ✅ The Good

- **Constant‑time lookups**: 50 k operations ≈ *microseconds*.  
- **No sorting or string manipulation** after mask creation.  
- **Clear semantics**: one integer per word = easier debugging.  
- **Scales**: 5 × 10⁴ words → < 2 MB memory, < 0.1 s runtime in most languages.

---

### ⚠️ The Bad

- **Assumes distinct letters** – if you ever reuse this pattern on general strings you must check uniqueness.  
- **Language limits** – a 26‑bit integer is safe in Java’s 32‑bit `int`, but you need to confirm data type width in C++/Rust/Go.  
- **Hash overhead** – `HashSet` is a little heavier than a plain array; not a problem here but be mindful for 10⁶‑size datasets.

---

### 😱 The Ugly

- **Off‑by‑one errors** when mapping `'a'` → bit 0.  
- **Wrong XOR usage** – toggling a bit on (`|=`) instead of off (`^=`).  
- **Double counting** – failing to `break` after the first success inflates the answer.  
- **Misinterpreting the “add one letter” rule** – an anagram of a start word is *not* allowed; the mask‑removal guarantees you always strip one letter.

---

### 📚 Implementation Tips for Your Interview

1. **Write a tiny helper** `toMask()` – keep the core logic clean.  
2. **Test edge cases** locally:  
   ```text
   start  = ["a","ab","abc"]
   target = ["abc", "abcd", "abcdx"]
   ```
   Only `"abcd"` and `"abcdx"` should count.  
3. **Explain the XOR trick** to the interviewer; it’s a great talking point.  
4. **Show the complexity** up front: O(n + m) time, O(n) space.  
5. **Mention alternatives** (sorting strings, trie), then argue why bit‑masking wins.  

---

### 🔎 SEO Highlights

- **Keywords**: *LeetCode 2135, Count Words Obtained After Adding a Letter, Bitmask, HashSet, Interview Preparation, Data Structures*  
- **Header Tags**: `h1` for the title, `h2` for subsections, `h3` for deeper dives.  
- **Rich Snippets**: Provide a short code snippet, complexity table, and a “quick test” section.  
- **Backlinks**: Encourage linking from “Data Structures” pages to this article.  

---

### 🎯 Final Thought

Solving LeetCode 2135 with bit‑masking isn’t just about writing fast code – it’s a **micro‑cosm of interview best practices**:

- **Use the right data structure** (`HashSet` for membership).  
- **Leverage domain constraints** (distinct letters, alphabet size).  
- **Keep the code clean** – helper functions, early exits, and clear variable names.  

By mastering this problem you demonstrate:

- Proficiency in **bit‑wise manipulation** – a prized skill in system‑design interviews.  
- Ability to **translate problem constraints into algorithmic advantages**.  
- Readiness for the “gotchas” that often trip junior candidates.

**Takeaway:** Nail the bit‑mask trick, articulate the good/bad/ugly, and you’ll shine in any coding interview that features LeetCode‑style questions.

---