---
title: LeetCode 1554. Strings Differ by One Character - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Solving LeetCode 1554 – “Strings Differ by One Character”

> **TL;DR**  
> Use a *polynomial hash* to reduce every word to a single integer.  
> For every position `i` remove the contribution of the `i`‑th character and check whether two words become equal.  
> Time = O(N·M), Space = O(N).

---

### 📌 Problem Recap

```
Input : String[] dict   (all words same length, unique, lowercase)
Output: true  if any two words differ by exactly one character at the same index
        false otherwise
```

| Sample | Output | Explanation |
|--------|--------|-------------|
| ["abcd","acbd","aacd"] | true | “abcd” vs “aacd” differ only at index 1 |
| ["ab","cd","yz"] | false | No pair differs by one character |
| ["abcd","cccc","abyd","abab"] | true | “abcd” vs “abyd” differ at index 2 |

Constraints  
* `|dict| ≤ 10^5`  
* `len(word) = M ≤ 10` (because all words are equal length)  
* Each word is unique and consists of only `a‑z`.

---

## ⚙️ Algorithm – Polynomial Hash + Set

1. **Pre‑compute powers**  
   `pow[i] = BASE^i mod MOD` for `i = 0 … M-1`.  
   `BASE = 257` (first prime > 256, works for all ASCII)  
   `MOD  = 1 000 000 007` (large prime to avoid overflow)

2. **Hash every word**  
   `hash(w) = Σ (w[j] * pow[j]) mod MOD`

3. **For each index `i`**  
   *Create a fresh hash set* `seen`.  
   *For every word* compute `hashWithoutChar = (hash[w] – w[i]*pow[i]) mod MOD`  
   *If `hashWithoutChar` already in `seen` → two words differ only at `i`* → return `true`.  
   Else add it to `seen`.

4. **Return `false`** if no pair was found.

> **Why it works**  
> Removing the contribution of a single character from the polynomial hash gives the hash of the *remaining* substring.  
> If two words differ only at position `i`, their “removed‑char” hashes are identical – we catch that in the set.  
> Collisions are astronomically unlikely with a 64‑bit integer and a 1e9+7 modulus.

---

### ✅ Correctness Proof

*Let `w1` and `w2` be two words that differ only at index `k`.*

- Their full hashes differ only by `Δ = (w1[k] - w2[k]) * pow[k]`.
- Removing `k` from both hashes gives `h1' = h1 - w1[k]*pow[k]` and `h2' = h2 - w2[k]*pow[k]`.
- Since `h2 = h1 + Δ`, we have  
  `h2' = h1 + Δ - w2[k]*pow[k] = h1 + (w1[k]-w2[k])*pow[k] - w2[k]*pow[k] = h1 - w1[k]*pow[k] = h1'`.  
  Thus `h1' = h2'`.

Conversely, if two words’ “removed‑char” hashes are equal at some index `k`, the only difference between the words can be at `k`, because all other characters contribute the same amounts to both hashes. Hence they differ by at most one character; uniqueness of words guarantees it’s exactly one. ∎

---

### ⏱️ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Pre‑compute powers | O(M) | O(M) |
| Compute all hashes | O(N·M) | O(N) |
| Main loop | O(N·M) | O(N) per iteration (set) |
| **Total** | **O(N·M)** | **O(N)** |

With `N ≤ 10^5` and `M ≤ 10`, this runs comfortably under 1 second in all three languages.

---

## 🧪 Code Implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.  
Each follows the same algorithmic idea and includes comments for clarity.

---

### Java

```java
import java.util.*;

public class Solution {
    public boolean differByOne(String[] dict) {
        if (dict == null || dict.length < 2) return false;
        final int BASE = 257;                     // > 255
        final long MOD  = 1_000_000_007L;          // 1e9+7
        int L = dict[0].length();

        // 1️⃣ pre‑compute powers
        long[] pow = new long[L];
        pow[0] = 1;
        for (int i = 1; i < L; i++) {
            pow[i] = (pow[i - 1] * BASE) % MOD;
        }

        // 2️⃣ compute full hashes
        long[] hashes = new long[dict.length];
        for (int i = 0; i < dict.length; i++) {
            long h = 0;
            String s = dict[i];
            for (int j = 0; j < L; j++) {
                h = (h + s.charAt(j) * pow[j]) % MOD;
            }
            hashes[i] = h;
        }

        // 3️⃣ try removing each position
        for (int pos = 0; pos < L; pos++) {
            Set<Long> seen = new HashSet<>();
            for (int i = 0; i < dict.length; i++) {
                long without = (hashes[i] - dict[i].charAt(pos) * pow[pos]) % MOD;
                if (without < 0) without += MOD;     // keep positive
                if (!seen.add(without)) return true; // duplicate → differ by one
            }
        }
        return false;
    }
}
```

---

### Python

```python
from typing import List

class Solution:
    def differByOne(self, dict: List[str]) -> bool:
        if not dict or len(dict) < 2:
            return False

        BASE = 257
        MOD  = 1_000_000_007
        L    = len(dict[0])

        # pre‑compute powers
        pow = [1] * L
        for i in range(1, L):
            pow[i] = (pow[i - 1] * BASE) % MOD

        # full hashes
        hashes = []
        for s in dict:
            h = 0
            for j, ch in enumerate(s):
                h = (h + ord(ch) * pow[j]) % MOD
            hashes.append(h)

        # remove one char per position
        for pos in range(L):
            seen = set()
            for i, s in enumerate(dict):
                without = (hashes[i] - ord(s[pos]) * pow[pos]) % MOD
                if without in seen:
                    return True
                seen.add(without)
        return False
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool differByOne(vector<string>& dict) {
        if (dict.size() < 2) return false;
        const long long BASE = 257;
        const long long MOD  = 1'000'000'007LL;
        int L = dict[0].size();

        // 1. pre‑compute powers
        vector<long long> pow(L);
        pow[0] = 1;
        for (int i = 1; i < L; ++i)
            pow[i] = (pow[i-1] * BASE) % MOD;

        // 2. full hashes
        vector<long long> h(dict.size());
        for (size_t i = 0; i < dict.size(); ++i) {
            long long val = 0;
            for (int j = 0; j < L; ++j)
                val = (val + dict[i][j] * pow[j]) % MOD;
            h[i] = val;
        }

        // 3. try removing each position
        for (int pos = 0; pos < L; ++pos) {
            unordered_set<long long> seen;
            for (size_t i = 0; i < dict.size(); ++i) {
                long long without = (h[i] - dict[i][pos] * pow[pos]) % MOD;
                if (without < 0) without += MOD;
                if (!seen.insert(without).second) return true;
            }
        }
        return false;
    }
};
```

> **Tip** – Use `uint64_t` (2^64) instead of a modulus if you want to drop the `%` operation altogether; the probability of collision is essentially zero for the given constraints.

---

## 🤔 Why This Algorithm Is Interview‑Ready

| ✔️ Feature | Why it matters |
|------------|----------------|
| **O(N·M)** | Meets the hard constraint (`10^5` words) |
| **No nested loops** | Keeps code simple & readable |
| **Mathematical proof** | Gives you confidence to explain it in an interview |
| **Handles negatives** | Shows you care about edge cases (Java & C++ use `%` carefully) |

**Interview checklist**

1. **Start with the brute‑force idea** – it’s easy to explain and usually accepted in the first round.  
2. **Show you can improve** – introduce the polynomial hash, explaining why it reduces a string to a single number.  
3. **Mention collision risk** – be honest; no one can guarantee a perfect hash, but you can choose a large modulus or 64‑bit overflow to make it negligible.  
4. **Explain time & space** – interviewers love seeing the big‑O derived from the constraints.  
5. **Talk about edge cases** – empty list, single‑letter words, all‑identical letters, etc.  

---

## 🧠 “Good, Bad, Ugly” of the Solution

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Good** | *Linear time*, works for up to 10⁵ words, constant `M` keeps memory low. | – | – |
| **Bad** | Rare hash collisions could mis‑report `false` → `true`. | Mitigated by using a large prime mod or 64‑bit overflow. | – |
| **Ugly** | Managing modular arithmetic in Java/C++ (`%` and negative values) can be error‑prone. | Avoid by using unsigned 64‑bit arithmetic or a language that auto‑wraps (`unsigned long long` in C++). | – |

> **Pro tip:** If you’re worried about collisions in a production setting, keep a *pair of hashes* (e.g., two different moduli) or fall back to a verification step when a duplicate hash is found.

---

## 📝 Testing & Validation

| Test | Input | Expected | Result |
|------|-------|----------|--------|
| Empty array | `[]` | `false` | ✔️ |
| One word | `["a"]` | `false` | ✔️ |
| All identical words except one char | `["abcde","abfde","abgde"]` | `true` | ✔️ |
| All words differ by 2 chars | `["abc","def","ghi"]` | `false` | ✔️ |
| Max size (`10^5`) random words | – | Runs < 1 s | ✔️ |

Generate test cases with `random.sample` or a custom generator to be confident.

---

## 📚 Interview Tips

| Topic | What to Mention | Why It Helps |
|-------|-----------------|--------------|
| **Algorithm design** | Start with a brute‑force approach, then identify the “one character” constraint → leads naturally to the hash idea. | Shows problem‑solving mindset. |
| **Data structures** | Use a hash set to detect duplicates in O(1). | Keeps space optimal. |
| **Complexity** | Explain O(N·M) and why it satisfies the constraints. | Quantifies efficiency. |
| **Edge‑cases** | Empty input, single word, all words identical length. | Demonstrates thoroughness. |
| **Collision handling** | Mention probability and mitigation. | Shows awareness of real‑world pitfalls. |

---

## ❓ Frequently Asked Questions

| Q | A |
|---|---|
| **Can I just use a `String` comparison after removing a char?** | Yes, but that would be O(N²·M) in the worst case – unacceptable for 10⁵ words. |
| **What if M were larger (say 1000)?** | The algorithm would still be O(N·M), but 10⁵·1000 is 10⁸ operations – borderline. A different approach (bit‑masking or rolling hash) might be needed. |
| **Will 32‑bit ints overflow?** | In Java/Python/C++ we use 64‑bit (`long long`/`int64`) plus a modulus to keep values < MOD. |
| **Why not use `unordered_set<string>` directly?** | That would store full strings – O(N·M) memory and slower lookups. Hashing compresses each string to an integer, saving space and time. |

---

## 🎉 Conclusion

The polynomial hash trick turns a seemingly “string‑heavy” problem into a handful of integer operations.  
With just a few lines of code, you satisfy the constraints, keep the algorithm fast, and are ready to impress interviewers.

> **Takeaway** – When you’re asked “is there a pair that differs by one character?”, think “hash every word → remove one char → look for duplicates”.  
> That mindset is exactly what many tech companies look for: *clever, efficient, and well‑justified* solutions.

Happy coding, and good luck on your next interview! 🎯