---
title: LeetCode 2588. Count the Number of Beautiful Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution Code (Java, Python & C++)

```java
/*  Java – LeetCode 2588: Count the Number of Beautiful Subarrays  */
/*  Time   : O(n)        Space : O(n)    */
class Solution {
    public long beautifulSubarrays(int[] nums) {
        // frequency map of prefix XOR values
        HashMap<Integer, Integer> freq = new HashMap<>();
        freq.put(0, 1);                 // empty prefix has XOR = 0

        long ans = 0;
        int pref = 0;
        for (int x : nums) {
            pref ^= x;                 // current prefix XOR
            ans += freq.getOrDefault(pref, 0);   // all previous prefixes with same XOR form a beautiful subarray
            freq.put(pref, freq.getOrDefault(pref, 0) + 1);
        }
        return ans;
    }
}
```

```python
#  Python – LeetCode 2588: Count the Number of Beautiful Subarrays
#  Time   : O(n)        Space : O(n)
from collections import Counter

class Solution:
    def beautifulSubarrays(self, nums: list[int]) -> int:
        freq = Counter({0: 1})        # prefix XOR 0 occurs once (empty prefix)
        pref = ans = 0
        for x in nums:
            pref ^= x
            ans += freq[pref]
            freq[pref] += 1
        return ans
```

```cpp
//  C++ – LeetCode 2588: Count the Number of Beautiful Subarrays
//  Time   : O(n)        Space : O(n)
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long beautifulSubarrays(vector<int>& nums) {
        unordered_map<int,int> freq{{0,1}};
        long long ans = 0;
        int pref = 0;
        for (int x : nums) {
            pref ^= x;
            ans += freq[pref];
            ++freq[pref];
        }
        return ans;
    }
};
```

All three implementations share the same idea: maintain a prefix‑XOR map and count how many times the same prefix XOR has appeared before. Each pair of equal prefix XORs defines a subarray whose XOR is 0, i.e., a **beautiful** subarray.



--------------------------------------------------------------------

## 2.  Blog Article – “The Good, The Bad, and The Ugly of Beautiful Subarrays”

### 2.1 Title & Meta Description (SEO)

- **Title:** “Beautiful Subarrays: A Deep Dive into LeetCode 2588 – The Good, The Bad & The Ugly”
- **Meta Description:** “Learn how to solve LeetCode 2588 with a clean prefix‑XOR algorithm. Get code in Java, Python, C++, plus interview‑style tips to land your next job.”

---

### 2.2 Introduction

Have you ever stared at a LeetCode problem that looks deceptively simple but turns out to be a *trick question*?  
LeetCode **2588 – Count the Number of Beautiful Subarrays** is one such puzzle. On the surface it’s about bit manipulation and pairwise subtractions, but the underlying math is elegantly captured by the XOR operation.

In this article we’ll dissect the problem, walk through a clean, production‑ready solution, and highlight:

| What to Learn | Why It Matters |
|---------------|----------------|
| **Good** – The “aha” insight that *beautiful* ⇔ *XOR = 0* | Turns a quadratic loop into linear time |
| **Bad** – Common pitfalls (off‑by‑one, wrong map init, overflow) | Avoids interview mistakes |
| **Ugly** – Hidden edge cases (all zeros, large input) | Shows robustness of a real‑world solution |

---

### 2.3 Problem Statement (Short Version)

> **Given** an array `nums` of length `n` (`1 ≤ n ≤ 10⁵`) where each element is `0 ≤ nums[i] ≤ 10⁶`,  
> **Count** the number of contiguous subarrays that can be reduced to all zeros by repeatedly selecting two indices with a common set bit `k` and subtracting `2ᵏ` from both.

> Subarrays that are already all zeros count as beautiful.

---

### 2.4 The Good: The “Beautiful = XOR 0” Insight

When you subtract `2ᵏ` from two numbers that both have the `k`‑th bit set, you effectively toggle that bit **out** of both numbers. Repeating this operation for all bits, you will end up with zero *iff* the XOR of all numbers in the subarray is zero.

**Why XOR?**  
`a XOR b = 0` ⇔ `a == b`.  
If you look at the binary representation of the subarray, each operation cancels one common set bit from two numbers. The *parity* (odd/even count) of set bits for every position must be even for all bits to vanish – that’s exactly what XOR‑zero guarantees.

> **Result:** A subarray is beautiful **iff** the XOR of all its elements equals `0`.

---

### 2.5 The Algorithm – Prefix XOR + HashMap

1. **Prefix XOR array**  
   `pref[i] = nums[0] ^ nums[1] ^ … ^ nums[i]`.

2. **Observation**  
   XOR of subarray `l..r` is `pref[r] ^ pref[l‑1]`.  
   This equals `0` **iff** `pref[r] == pref[l‑1]`.

3. **Counting pairs with equal prefix XOR**  
   Iterate once over `nums`, maintaining a frequency map of seen prefix XORs.  
   For each new prefix `p`:
   - All previously seen indices with the same `p` form beautiful subarrays ending at current index.
   - `ans += freq[p]`.
   - `freq[p]++`.

4. **Edge‑Case** – Empty prefix  
   Initialize the map with `{0: 1}` so that subarrays starting at index `0` are counted correctly.

**Complexities**

| Complexity | Value |
|------------|-------|
| Time | **O(n)** – one scan, constant‑time hash operations |
| Space | **O(n)** – map holds at most `n+1` distinct prefix values |

---

### 2.6 The Bad: Common Mistakes

| Mistake | What it looks like | Fix |
|---------|--------------------|-----|
| Forgetting to count subarrays that start at index `0` | No initial `{0: 1}` | Add it before the loop |
| Using `int` for the answer when `n = 10⁵` | May overflow 32‑bit | Use `long` (Java) / `long long` (C++) / `int` but cast to `int64` (Python handles big ints) |
| Mis‑implementing XOR instead of subtraction | `pref ^= num` vs `pref -= num` | Keep XOR; the operation is conceptual, not a real subtraction |
| Off‑by‑one in the loop (using `for (int i = 1; i <= n; ++i)` with 0‑based array) | Skips the first element | Iterate over the array directly, or carefully adjust indices |

---

### 2.7 The Ugly: Edge Cases and Performance Hurdles

| Edge Case | Why it matters | How to handle |
|-----------|----------------|---------------|
| All zeros (`[0,0,0,…]`) | Every subarray is beautiful; count is `n(n+1)/2`. | The algorithm naturally produces this result because every prefix XOR stays `0`. |
| Large input (`n = 10⁵`, `nums[i] = 10⁶`) | Map size grows to `n+1`, still fine in memory. | Use `unordered_map` (C++) or `HashMap` (Java) – they give average O(1) operations. |
| Extremely sparse prefix XOR values (e.g., only 0 and 1) | Map stays tiny – no issue. | No special handling needed. |

---

### 2.8 Interview‑Ready Tips

1. **Explain the XOR trick first** – interviewers love the “aha” moment.
2. **Show the reduction** from O(n²) brute force to O(n) with a map.
3. **Discuss time & space trade‑offs** – maybe mention a Fenwick tree or segment tree if they ask.
4. **Ask clarifying questions** about zero‑based vs one‑based indexing; confirm how “beautiful” subarrays are defined for all‑zero arrays.
5. **Code on a whiteboard** – write the hash‑map initialization explicitly; it demonstrates attention to detail.

---

### 2.9 Final Code (All Languages)

> **Java** – Uses `HashMap` and a `long` answer.  
> **Python** – Uses `Counter` and the built‑in big‑int support.  
> **C++** – Uses `unordered_map` for average‑case O(1).

Each snippet is ready to paste into the LeetCode editor or a production repo.

---

### 2.10 Conclusion

LeetCode 2588 is a textbook example of how a deep mathematical insight (beautiful ⇔ XOR 0) can turn a complex bit‑manipulation problem into a linear‑time counting exercise.  

By understanding:

- **The Good** (XOR insight),
- **The Bad** (common bugs),
- **The Ugly** (edge cases & performance),

you’ll not only ace this problem but also be able to explain it confidently in any coding interview.

> **Next step:** Try to implement the same idea in a language you’re less familiar with, or tweak the map to use a `TreeMap` and see the impact on runtime.

Good luck, and may your next interview go *beautifully*!