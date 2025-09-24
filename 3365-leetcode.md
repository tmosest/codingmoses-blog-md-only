---
title: LeetCode 3365. Rearrange K Substrings to Form Target String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3365 – Rearrange K Substrings to Form Target String  
## Java, Python & C++ Solutions + In‑Depth Blog Post

> **TL;DR** –  
> *The problem asks whether two anagram strings can be partitioned into `k` equal‑length pieces and then the pieces of `s` rearranged to form `t`.*  
> *A single `O(n)` pass with a hash map is enough: just count the pieces of `s`, then consume them from `t`.*  

Below you’ll find:

| Language | File | Code |
|----------|------|------|
| **Java** | `Solution.java` | 🗎[Link](#java) |
| **Python** | `solution.py` | 🗎[Link](#python) |
| **C++** | `solution.cpp` | 🗎[Link](#cpp) |

After that, a full‑blown SEO‑friendly blog article that explains the algorithm, walks through the “good, bad, ugly”, and offers interview‑style insights.

---

## 🚀 Java Solution

```java
// Solution.java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public boolean isPossibleToRearrange(String s, String t, int k) {
        int n = s.length();               // n == t.length()
        int blockSize = n / k;            // guaranteed to be integer

        // Count every k‑block of s
        Map<String, Integer> freq = new HashMap<>();
        for (int i = 0; i < n; i += blockSize) {
            String block = s.substring(i, i + blockSize);
            freq.merge(block, 1, Integer::sum);
        }

        // Consume blocks from t
        for (int i = 0; i < n; i += blockSize) {
            String block = t.substring(i, i + blockSize);
            Integer count = freq.get(block);
            if (count == null || count == 0) return false;
            freq.put(block, count - 1);
        }
        return true;
    }
}
```

> **Why it works**  
> * `k` is fixed, so each block length is `n/k`.  
> * Because `s` and `t` are anagrams, every block that appears in `t` must also appear in `s`.  
> * The hash map guarantees `O(1)` lookup for each block.

---

## 🐍 Python Solution

```python
# solution.py
def isPossibleToRearrange(s: str, t: str, k: int) -> bool:
    n = len(s)
    block = n // k

    freq = {}
    # Count blocks of s
    for i in range(0, n, block):
        sub = s[i:i+block]
        freq[sub] = freq.get(sub, 0) + 1

    # Consume blocks of t
    for i in range(0, n, block):
        sub = t[i:i+block]
        if freq.get(sub, 0) == 0:
            return False
        freq[sub] -= 1

    return True
```

> **Python‑specific notes**  
> * Slicing creates a new string, but the overhead is negligible for `n ≤ 2·10^5`.  
> * `dict.get` is `O(1)` average.

---

## 🧩 C++ Solution

```cpp
// solution.cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isPossibleToRearrange(string s, string t, int k) {
        int n = s.size();                    // n == t.size()
        int block = n / k;                   // block size

        unordered_map<string, int> freq;

        // Count substrings of s
        for (int i = 0; i < n; i += block) {
            string sub = s.substr(i, block);
            ++freq[sub];
        }

        // Consume substrings of t
        for (int i = 0; i < n; i += block) {
            string sub = t.substr(i, block);
            auto it = freq.find(sub);
            if (it == freq.end() || it->second == 0) return false;
            --it->second;
        }
        return true;
    }
};
```

> **C++‑specific notes**  
> * `unordered_map` gives average `O(1)` operations.  
> * `substr` copies the substring, but the length is at most `n/k ≤ n`.  
> * No manual memory management required.

---

## 📚 Blog Article – “The Good, the Bad, and the Ugly of LeetCode 3365”

> **Keywords**: LeetCode 3365, Rearrange K Substrings, interview coding problem, algorithmic thinking, Java solution, Python solution, C++ solution, job interview, coding interview, data structures, hash map, substring partition, anagrams.

---

### 1. Introduction

When you’re preparing for a software‑engineering interview, LeetCode problem **3365 – Rearrange K Substrings to Form Target String** is a *gold‑mine* question.  
It tests:

- **Anagram reasoning** – the strings are guaranteed to be anagrams.
- **String partitioning** – you must split a string into equal chunks.
- **Hash‑map counting** – efficient frequency tracking of blocks.
- **Edge‑case vigilance** – watch out for off‑by‑ones, substring copies, and large input sizes.

Below we walk through the problem statement, analyze the algorithm, discuss the pros, cons, and pitfalls, and finish with interview‑style insights.

---

### 2. Problem Restatement

> **Given**  
> • Two strings `s` and `t`, both of length `n` (≤ 2·10⁵) and **anagrams** of each other.  
> • An integer `k` such that `n` is divisible by `k`.  
> **Question**  
> Can you split `s` into `k` equal‑length contiguous substrings, rearrange those substrings arbitrarily, and concatenate them to obtain exactly `t`?

If yes → return `true`, else → `false`.

---

### 3. Naïve Approaches & Their Flaws

| Approach | Time Complexity | Space Complexity | Why It Fails |
|----------|-----------------|------------------|--------------|
| Generate all `k!` permutations of the `k` substrings and compare | `O(k! * n)` | `O(k)` | `k` can be up to `n` (worst case 200k) – impossible. |
| Check each position of `t` against every possible substring of `s` | `O(n^2)` | `O(1)` | Too slow for 200k. |
| Use a sliding window for each block in `t` | `O(n * k)` | `O(n/k)` | Still quadratic when `k` is small. |

Hence we need a *linear* solution.

---

### 4. The Optimal Solution – Frequency Map of Blocks

#### 4.1 Observation

Because `s` and `t` are anagrams, every character in `t` exists somewhere in `s`.  
If we split both strings into blocks of length `len = n/k`, the problem reduces to:

> *Can the multiset of `k` blocks from `s` be rearranged to equal the multiset of `k` blocks from `t`?*

So the solution boils down to comparing two multisets of strings.

#### 4.2 Algorithm (One‑Pass Hash Map)

1. **Compute block length** `len = n / k`.  
2. **Count blocks of `s`**:  
   - For every `i = 0 … n-1` step `len`, take substring `s[i … i+len)` and increment its count in a hash map.
3. **Consume blocks of `t`**:  
   - For every `i = 0 … n-1` step `len`, take substring `t[i … i+len)`.  
   - If the block is missing or its count is zero → return `false`.  
   - Otherwise, decrement its count.
4. **All blocks matched** → return `true`.

**Complexities**

| Operation | Time | Space |
|-----------|------|-------|
| Building map | `O(k)` substrings → `O(n)` total | `O(k)` distinct blocks |
| Consuming map | `O(k)` | `O(k)` |
| **Overall** | `O(n)` | `O(k)` |

Because `k ≤ n`, the solution is linear in the string length and uses constant extra space relative to `k`.

#### 4.3 Edge‑Case Handling

| Edge | What to watch out for | Fix |
|------|-----------------------|-----|
| **`k == 1`** | Entire string as one block | Map will have one entry; algorithm works unchanged. |
| **`k == n`** | Each character is a block | Map holds `n` entries; still `O(n)` operations. |
| **Large `k`** | Substring copies might be expensive | Use `String#substring` in Java (O(1) reference in modern JDK) or `substr` in C++/Python; still fine for 200k. |
| **Very small block length (1)** | Frequent map lookups | Still O(1) per lookup; fine. |
| **Empty string** | Problem constraints forbid | Not needed. |

---

### 5. The “Good, the Bad, and the Ugly”

#### The Good
- **Simplicity** – The algorithm is one‑pass and uses only a hash map.  
- **Linear Time** – Handles the maximum input size comfortably.  
- **Deterministic** – No backtracking or exponential blow‑up.  
- **Language‑agnostic** – Works in Java, Python, C++, Go, etc.

#### The Bad
- **Substring Overhead** – Each block is a new string (Java) or copy (C++/Python).  
- **Hash‑Map Collision** – While average O(1) lookup, pathological inputs (e.g., many identical blocks) may cause performance degradation in some languages if a poor hash function is used.  
- **Memory Usage** – Worst‑case `k == n` leads to `n` distinct keys, which is still `O(n)` but may use significant memory in a tight interview environment.

#### The Ugly
- **Misreading the Problem** – Some candidates incorrectly think they need to *reorder the characters within blocks*, which is wrong. The blocks themselves must stay intact.  
- **Off‑by‑One Bugs** – Forgetting that `substring` takes the end index exclusive can cause out‑of‑bounds.  
- **Assuming Java’s `substring` is O(n)** – In older JDKs `substring` creates a copy, leading to `O(n²)` in worst case. Modern JDKs mitigate this, but a safe approach is to use `StringBuilder` if you’re in an environment where you can’t rely on that guarantee.

---

### 6. Interview‑Ready Tips

1. **Explain the Anagram Constraint** – Show you understand that it guarantees the total multiset of characters matches, eliminating some possibilities.  
2. **Ask Clarifying Questions**  
   - “Is `k` guaranteed to divide `n`?”  
   - “Can `k` be equal to `n`?”  
   - “Will the strings contain only ASCII characters?”  
   This demonstrates attention to detail.  
3. **Time‑Space Trade‑Off** – If the interviewer asks for *constant extra space*, you can note that `k` can be up to `n`, so `O(k)` is inevitable unless you use a multi‑pass algorithm that re‑uses the same memory.  
4. **Testing** – Walk through examples manually:  
   - `s = "abab"`, `t = "baba"`, `k = 2` → blocks `["ab", "ab"]` vs `["ba", "ba"]` → `false`.  
   - `s = "abcabc"`, `t = "bcaabc"`, `k = 3` → blocks `["abc", "abc"]` vs `["bca", "abc"]` → `false`.  
   - `s = "abcd"`, `t = "abcd"`, `k = 2` → blocks `["ab", "cd"]` vs `["ab", "cd"]` → `true`.  
   Show the map updates for each step.  

5. **Alternative Data Structures** – If the interviewer asks, you can discuss using a *multiset* (`Counter` in Python) or a *sorted map* if you want to guarantee deterministic order (useful when you need to output the rearranged order).  

---

### 6.1 “What If” Follow‑Up Questions

- **Q**: *What if `s` and `t` were **not** anagrams?*  
  **A**: You would need to check whether the character counts in `s` match those in `t` before proceeding. The block frequency map would still be valid, but you’d first verify the global anagram property.

- **Q**: *Can we recover the exact permutation of blocks that produces `t`?*  
  **A**: Yes. While consuming `t`, store the block strings in a list and output them in the order of consumption. The algorithm itself already gives you the permutation.

- **Q**: *How would you adapt this solution to a streaming scenario where `s` arrives piece‑by‑piece?*  
  **A**: Incrementally update the hash map as new blocks arrive. For `t`, you still need to buffer at least `blockSize` characters to form a block.

---

### 7. Conclusion

LeetCode 3365 is deceptively simple but packed with classic interview themes:

- Anagram reasoning → ensures the problem is solvable with counting.  
- Partitioning → forces you to think about block boundaries, not just character frequencies.  
- Hash‑map frequency → the go‑to tool for multiset comparison.

By mastering this problem, you’ll strengthen your *algorithmic toolbox* and impress interviewers with a clean, linear solution that scales to the problem’s upper bounds.

---

### 8. Final Interview Takeaway

> **Remember**:  
> 1. Always look for hidden constraints (anagram, divisibility).  
> 2. Reduce the problem to a *multiset* comparison whenever you see “rearrange” or “permute”.  
> 3. Aim for `O(n)` unless you can prove the optimal lower bound is worse.  

If you can articulate the above in a 10‑minute discussion, you’ll demonstrate the core interview skill: *converting problem statements into concise, provably efficient algorithms.*

---

Happy coding and good luck on your next interview! 🚀

---



---

> **Enjoyed the article?**  
> *Share it on LinkedIn or Twitter. Tag a friend who’s preparing for an interview and let’s get them job‑ready together!*

---



---

**End of Document**


--- 

> 👉 **How to run the solutions**  
> *Java* → `javac Solution.java && java Solution` (with a custom test harness).  
> *Python* → `python3 solution.py` (with a test harness).  
> *C++* → `g++ -std=c++17 solution.cpp -O2 && ./a.out` (with a test harness).

Happy coding, and best of luck in your job hunt! 🌟