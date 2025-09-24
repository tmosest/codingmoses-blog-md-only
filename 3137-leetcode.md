---
title: LeetCode 3137. Minimum Number of Operations to Make Word K-Periodic - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📘 Mastering LeetCode 3137 – “Minimum Number of Operations to Make Word K‑Periodic”

> **TL;DR**  
> The trick is simple: split the string into blocks of length **k**, keep a frequency map of the blocks, and replace every non‑most‑frequent block with the most common one.  
> **Time** = O(n), **Space** = O(n).

---

### Why This Problem Matters for Your Interview Portfolio

- **Real‑world relevance**: Many systems work with *chunked data* (e.g., packets, blocks, frames). Knowing how to minimise replacements is a useful skill for interviewers in networking, database, and distributed‑systems roles.
- **Showcase of core skills**:  
  - String manipulation  
  - Hash‑map usage  
  - Greedy / frequency‑based optimisation  
  - Correctness proofs  
  - Complexity analysis  
- **High LeetCode ranking**: Solving 3137 gives you a “medium” problem under “strings”, a sweet spot for interview preparation.

---

## 1. Problem Recap

> **Input**  
> `word` – a string of length `n` (1 ≤ n ≤ 10⁵)  
> `k` – an integer that divides `n`  

> **Operation**  
> Pick two indices `i` and `j` such that `i % k == j % k == 0` and copy the block `word[i…i+k-1]` to replace the block `word[j…j+k-1]`.

> **Goal**  
> Make `word` **k‑periodic** – i.e. the whole string is a concatenation of a single block of length `k`.  
> Return the **minimum number of operations**.

---

## 2. Intuition – The “Most Common Block” Principle

1. **Divide** the string into `n/k` disjoint blocks, each of length `k`.  
2. Any valid final string will be a repetition of **one particular block** `s`.  
3. If we already have a block that appears `cnt` times, we *only* need to replace the remaining `n/k - cnt` blocks with `s`.  
4. To minimise operations, pick the block that appears the **maximum number of times**.  
5. The answer is simply  
   ```text
   total_blocks - max_frequency
   ```

---

## 3. Algorithm (Pseudocode)

```
split word into blocks of length k
count frequencies of each block in a hash map
maxFreq = maximum frequency in the map
answer = (n / k) - maxFreq
return answer
```

---

## 4. Correctness Proof (Sketch)

Let `B` be the multiset of all blocks.  
Let `b*` be the block with the highest frequency `maxFreq`.

*Any* k‑periodic string must be `b*` repeated `(n/k)` times.  
If we change a block to `b*`, we perform **exactly one operation**.  
All blocks other than `b*` must be changed, and there are `|B| - maxFreq` of them.  
Hence the algorithm performs the minimal number of operations. ∎

---

## 5. Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time   | **O(n)** | **O(n)** | **O(n)** |
| Space  | **O(n)** | **O(n)** | **O(n)** |

`n` is the length of the input string.

---

## 6. Code Implementations

> **Tip:** The core idea is identical across languages; only syntax differs.

### 6.1 Java (Java 17)

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int minimumOperationsToMakeKPeriodic(String word, int k) {
        int n = word.length();
        int totalBlocks = n / k;
        Map<String, Integer> freq = new HashMap<>();

        for (int i = 0; i < n; i += k) {
            String block = word.substring(i, i + k);
            freq.put(block, freq.getOrDefault(block, 0) + 1);
        }

        int maxFreq = 0;
        for (int f : freq.values()) {
            if (f > maxFreq) maxFreq = f;
        }

        return totalBlocks - maxFreq;
    }
}
```

---

### 6.2 Python (Python 3)

```python
class Solution:
    def minimumOperationsToMakeKPeriodic(self, word: str, k: int) -> int:
        n = len(word)
        freq = {}
        for i in range(0, n, k):
            block = word[i:i + k]
            freq[block] = freq.get(block, 0) + 1
        max_freq = max(freq.values())
        return n // k - max_freq
```

---

### 6.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumOperationsToMakeKPeriodic(string word, int k) {
        int n = word.size();
        unordered_map<string, int> freq;
        for (int i = 0; i < n; i += k) {
            string block = word.substr(i, k);
            ++freq[block];
        }
        int maxFreq = 0;
        for (const auto &p : freq)
            maxFreq = max(maxFreq, p.second);
        return n / k - maxFreq;
    }
};
```

---

## 7. The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic simplicity** | O(n) greedy | None | – |
| **Memory overhead** | Uses hash‑map of strings | Copying substrings may use O(k) per block | For very large `k`, each key may be expensive – consider rolling hash or integer encoding |
| **String operations** | `substring`/`substr` in constant time on LeetCode | Still copies characters | In tight environments, the cost can add up |
| **Edge‑case safety** | Integer division `n/k` is exact | Off‑by‑one errors at block boundaries | Forgetting that `k` divides `n` → division by zero |
| **Portability** | Works in Java, Python, C++ | – | – |
| **Potential optimization** | Rolling‑hash or integer encoding | Complexity‑trade‑off | Not needed for n ≤ 10⁵ but worth knowing |

> **How to avoid the ugly:**  
> If you’re working with huge blocks (`k` close to `n`), replace the string keys with a *rolling hash* (e.g., 64‑bit integer) so that you never copy the block itself.  
> The code stays almost the same – just replace `block` with `hash(word, i, k)`.

---

## 8. Common Pitfalls & Interview Tips

1. **Off‑by‑One Errors** – Always use `i + k` (not `k-1`) when slicing; many candidates forget this.
2. **Integer Division** – In Java/Python/C++ the result of `n / k` is guaranteed to be an integer because `k` divides `n`. Still, use integer division (`//` in Python, `/` in C++/Java).
3. **Large k** – If `k` ≈ `n`, the hash‑map will contain at most one key; memory cost is negligible.  
   If `k` ≈ 1, you’re essentially counting individual characters – still fine.
4. **String Immutability in Python** – `word[i:i + k]` creates a new string; for `n = 10⁵` and `k = 5` this is ~500 KB – perfectly fine, but keep it in mind.
5. **Hash‑map vs. array** – Because blocks are arbitrary, a hash‑map is the natural choice. No need for fancy data structures unless you’re optimizing for micro‑seconds.

---

## 8. Real‑World Takeaways

- **Chunk‑based data** is everywhere: databases store pages, video encoders store frames, network protocols use packets.  
- Knowing *how many chunks you need to replace* is the core of many optimisation problems (e.g., *cache replacement*, *memory compaction*, *bandwidth throttling*).  
- A job interview may ask you to **extend** this logic:  
  *“What if you could only replace a block with the *closest* block lexicographically?”* – a perfect follow‑up challenge.

---

## 9. Final Thoughts & Call‑to‑Action

> 🎯 **Ready to land that software‑engineering role?**  
> Mastering 3137 shows you can:
> * split a problem into independent sub‑problems,
> * leverage frequency maps for greedy solutions,
> * prove optimality, and
> * write clean, portable code in Java, Python, and C++.

💡 **Next step**:  
- Try the *rolling‑hash* variant to eliminate substring copying.  
- Add unit tests for the edge cases (`k == 1`, `k == n`).  
- Post your solution on GitHub with a README that explains the algorithm—this is a great portfolio project!

✨ **Want to impress recruiters?** Show them you can solve 3137, explain your approach in an interview, and discuss the trade‑offs you considered. That’s the “good, the bad, the ugly” in a nutshell.

--- 

### 📚 Blog‑Ready Markdown

```markdown
# How to Ace LeetCode 3137 – Minimum Number of Operations to Make Word K‑Periodic

## Problem Overview
...

```

(Insert the article text above with Markdown headings and code blocks.)

Feel free to copy the article to your blog, LinkedIn, or portfolio site. Good luck crushing interviews and landing that dream job! 🚀