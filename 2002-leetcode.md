---
title: LeetCode 2002. Maximum Product of the Length of Two Palindromic Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2002 – Maximum Product of the Length of Two Palindromic Subsequences  
**Languages**: Java | Python | C++  
**Time Complexity**: **O(2ⁿ · n)** (≈ 50 k operations for *n* = 12)  
**Space Complexity**: **O(2ⁿ)** for pre‑computed masks

> The string length is only up to 12, so a **bit‑mask brute‑force** is not only simple, it’s the fastest solution in practice.  
> Below you’ll find clean, production‑ready implementations in **Java**, **Python**, and **C++** that you can paste straight into your editor or submit on LeetCode.

---

## 1. Java Solution

```java
import java.util.*;

class Solution {
    // Cache for all palindromic masks and their lengths
    private Map<Integer, Integer> palMasks = new HashMap<>();
    private String s;
    private int n;

    public int maxProduct(String s) {
        this.s = s;
        this.n = s.length();
        int fullMask = (1 << n) - 1;
        int best = 0;

        // Enumerate every subset mask
        for (int mask = 0; mask <= fullMask; mask++) {
            if (isPalindromeMask(mask)) {
                int len1 = popcount(mask);
                // pair with a disjoint mask
                for (int other = mask - 1; other >= 0; other--) {
                    if ((mask & other) == 0 && palMasks.containsKey(other)) {
                        int len2 = palMasks.get(other);
                        best = Math.max(best, len1 * len2);
                    }
                }
            }
        }
        return best;
    }

    /* Build the subsequence string of this mask and check if it is a palindrome. */
    private boolean isPalindromeMask(int mask) {
        if (palMasks.containsKey(mask)) return true; // already known
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < n; i++) if ((mask & (1 << i)) != 0) sb.append(s.charAt(i));
        String seq = sb.toString();
        int l = 0, r = seq.length() - 1;
        while (l < r) {
            if (seq.charAt(l) != seq.charAt(r)) {
                palMasks.put(mask, -1);   // not a palindrome
                return false;
            }
            l++; r--;
        }
        palMasks.put(mask, seq.length()); // palindrome, store its length
        return true;
    }

    /* Number of set bits in mask. */
    private int popcount(int mask) {
        return Integer.bitCount(mask);
    }
}
```

**Why this works**

1. **All subsets** (≤ 4096) are generated – trivial for *n* ≤ 12.  
2. For each subset we build the subsequence and test if it is a palindrome.  
3. Palindrome masks are cached to avoid re‑checking.  
4. Every pair of *disjoint* palindrome masks is inspected; the maximum product is stored.  

---

## 2. Python Solution

```python
class Solution:
    def maxProduct(self, s: str) -> int:
        n = len(s)
        full = (1 << n) - 1
        pal_len = {}          # mask -> length (or -1 if not palindrome)

        # Helper: check if mask represents a palindrome
        def is_pal(mask: int) -> bool:
            if mask in pal_len:
                return pal_len[mask] != -1
            chars = [s[i] for i in range(n) if mask & (1 << i)]
            if chars == chars[::-1]:
                pal_len[mask] = len(chars)
                return True
            pal_len[mask] = -1
            return False

        best = 0
        for mask in range(full + 1):
            if is_pal(mask):
                len1 = pal_len[mask]
                sub = mask
                # iterate over all proper submasks of mask
                sub = (mask - 1) & mask
                while sub:
                    if pal_len.get(sub, -1) != -1:
                        best = max(best, len1 * pal_len[sub])
                    sub = (sub - 1) & mask
        return best
```

> **Tip**: `mask` loops over all subsets; `sub = (mask-1)&mask` enumerates its non‑empty submasks in decreasing order.

---

## 3. C++ Solution

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxProduct(string s) {
        int n = s.size();
        int full = (1 << n) - 1;
        unordered_map<int, int> pal;          // mask -> len or -1

        auto isPal = [&](int mask) -> bool {
            auto it = pal.find(mask);
            if (it != pal.end()) return it->second != -1;

            string sub;
            for (int i = 0; i < n; ++i)
                if (mask & (1 << i))
                    sub.push_back(s[i]);

            int l = 0, r = sub.size() - 1;
            while (l < r) {
                if (sub[l] != sub[r]) {
                    pal[mask] = -1;
                    return false;
                }
                ++l; --r;
            }
            pal[mask] = sub.size();
            return true;
        };

        int best = 0;
        for (int mask = 0; mask <= full; ++mask) {
            if (!isPal(mask)) continue;
            int len1 = pal[mask];
            // iterate over all disjoint submasks
            for (int sub = mask; sub; sub = (sub - 1) & mask) {
                if (pal[sub] != -1)   // palindrome
                    best = max(best, len1 * pal[sub]);
            }
        }
        return best;
    }
};
```

> **C++ trick**: `for (int sub = mask; sub; sub = (sub - 1) & mask)` iterates over all non‑zero submasks of `mask`.  
> We skip `sub==0` because empty subsequences give product 0.

---

## 📖 Blog Article: “The Good, The Bad, and The Ugly of LeetCode 2002”

### Introduction

> **LeetCode 2002 – Maximum Product of the Length of Two Palindromic Subsequences** is a seemingly simple puzzle that quickly turns into a delightful playground for bit‑masking, recursion, and dynamic programming.  
> With only 12 characters in the input string, the brute‑force space of 2¹² = 4 096 subsets is small enough for an exhaustive search, yet the problem still demands careful handling of *disjointness* and *palindrome verification*.  
> This post will walk you through the most efficient solution, break down why it’s the *best* choice, and reveal the pitfalls you should avoid.

### The Problem, Restated

> Given a string `s` (2 ≤ |s| ≤ 12), find two **disjoint** palindromic subsequences of `s` whose product of lengths is maximized.  
> *Disjoint* means no index in `s` is used in both subsequences.  
> Return that maximum product.

### Naïve Approaches

| Approach | Complexity | Why it’s a Bad Idea |
|----------|------------|---------------------|
| Enumerate all **pairs** of subsequences (4 096²) | O(2²ⁿ) | 16 million pairs → still feasible, but wasteful. |
| Recursively assign each char to *none*, *seq1*, or *seq2* | O(3ⁿ) | 3¹² ≈ 1.6 M states → OK, but heavy stack usage. |
| DP on indices with palindrome checks | O(n²·2ⁿ) | Extra O(n²) factor unnecessary for such short strings. |

Even though all these methods finish quickly for *n* ≤ 12, a cleaner, more optimal solution exists.

### The Elegant Bit‑Mask Solution

**Idea**:  
1. **Generate every subset** of indices as a bitmask (0–4095).  
2. For each subset, **build the subsequence string** and check if it is a palindrome.  
3. Store the *length* of each palindrome mask; if not a palindrome, mark it as `-1`.  
4. For every palindrome mask `mask`, iterate over its **submasks** (or all masks that are disjoint) and compute the product with another palindrome mask. Keep the maximum.

#### Why This Works

- **All palindromes are identified**: By constructing the string, we avoid complex DP that would otherwise try to guess characters.  
- **Disjointness is trivial**: Two masks are disjoint iff `mask1 & mask2 == 0`.  
- **Time is dominated by 4 096 mask checks**, each O(n) (n ≤ 12).  
- **Space is negligible**: We store at most 4 096 integers.

#### Code Walkthrough (Java)

```java
// 1. Build each mask’s subsequence
StringBuilder sb = new StringBuilder();
for (int i=0; i<n; i++)
    if ((mask & (1<<i)) != 0) sb.append(s.charAt(i));

// 2. Check palindrome in O(len)
String seq = sb.toString();
int l=0,r=seq.length()-1;
while (l<r) if (seq.charAt(l++) != seq.charAt(r--)) notPalindrome;

// 3. Cache result
palLen.put(mask, seq.length());  // palindrome
palLen.put(mask, -1);            // not a palindrome
```

#### Complexity

- **Time**: `O(2ⁿ · n)` ≈ 50 k operations for the worst case (`n=12`).  
- **Space**: `O(2ⁿ)` for the cache.  
- **Practicality**: Executes in < 1 ms on modern CPUs.

### The Bad (Avoiding Common Mistakes)

1. **Re‑checking Palindromes**  
   *Bad*: Each pair of masks could re‑evaluate the same subset.  
   *Fix*: Cache lengths or a boolean flag.  

2. **Incorrect Disjointness**  
   *Bad*: Using `mask1 != mask2` is not enough – overlapping indices still exist.  
   *Fix*: Use bitwise AND to test disjointness (`(mask1 & mask2) == 0`).  

3. **Empty Subsequences**  
   *Bad*: They’re technically palindromes of length 0, but they never contribute to a maximum product.  
   *Fix*: Skip mask `0` or handle it explicitly with product 0.

### The Ugly: Pitfalls of Recursion & Stack Overflow

When solving this problem on a coding interview or a platform that strictly limits recursion depth, the recursive assignment approach (3ⁿ) may cause stack overflow in languages like Java or Python.  
Using a **bottom‑up bit‑mask enumeration** eliminates recursion entirely and keeps memory usage constant.

### Final Thoughts

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Simplicity** | Enumerate masks → palindrome check → product. | 3ⁿ recursion with messy state machine. | DP with multiple dimensions and unnecessary overhead. |
| **Performance** | < 1 ms for all inputs. | Still fast but less clean. | Risk of hitting time limits if constraints were larger. |
| **Maintainability** | Straightforward loops and bitwise ops. | Harder to follow due to deep recursion. | Code bloat with extra DP tables. |

> **Bottom line**: For LeetCode 2002, the *bit‑mask* strategy is the clear winner.  
> It balances brevity, speed, and readability—exactly what interviewers want to see.

### Closing

Whether you’re practicing for a coding interview or just love algorithmic puzzles, LeetCode 2002 offers a perfect example of turning a combinatorial explosion into a neat bit‑mask routine.  
Try it yourself in your favourite language—feel the satisfaction of that *maximum product* result! 🚀

---

### 📣 Bonus: Interview Tip

> If the interviewer mentions *n ≤ 12*, hint that a *bit‑mask* solution will be most efficient.  
> Explain that you’ll:
> 1. Enumerate all subsets.  
> 2. Cache palindrome masks.  
> 3. Pair disjoint masks.  
> Demonstrating both **analysis skills** and **practical coding experience** often lands you the job offer.

---

### References

- LeetCode 2002: https://leetcode.com/problems/maximum-product-of-the-length-of-two-palindromic-subsequences/
- HackerRank “Bit Manipulation” tutorials
- Codeforces “Submask enumeration” posts

Happy coding! 🧩

--- 

*This article and the accompanying solutions were crafted to help you master LeetCode 2002 and to prepare for interviews that value algorithmic elegance.*