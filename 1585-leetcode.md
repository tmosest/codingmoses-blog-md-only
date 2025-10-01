---
title: LeetCode 1585. Check If String Is Transformable With Substring Sort Operations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  Solution Code (Java, Python, C++)

Below are clean, production‑ready implementations for the **LeetCode 1585 – “Check If String Is Transformable With Substring Sort Operations”** problem.  
All three solutions share the same O(n) time / O(1) extra space complexity (the 10 × 10 pair‑count matrix is constant).

> **Key idea**  
> Sorting a substring can only *decrease* the number of inversions “smaller digit → larger digit”.  
> Therefore, if the pair‑frequency matrix of `s` is **not** a sub‑matrix of that of `t`, the transformation is impossible.  
> The algorithm first confirms that `s` and `t` are anagrams (same digit counts); then it builds the pair‑frequency matrix for both strings and compares them.

---

## Java (O(n) time, O(1) space)

```java
import java.util.*;

public class Solution {
    public boolean isTransformable(String s, String t) {
        if (s.length() != t.length()) return false;

        // 1. Quick anagram check
        int[] cnt = new int[10];
        for (int i = 0; i < s.length(); i++) {
            cnt[s.charAt(i) - '0']++;
            cnt[t.charAt(i) - '0']--;
        }
        for (int c : cnt) if (c != 0) return false;

        // 2. Build 10x10 pair‑frequency matrices
        int[][] pairS = new int[10][10];
        int[][] pairT = new int[10][10];
        int[] seenS = new int[10];
        int[] seenT = new int[10];

        for (int i = 0; i < s.length(); i++) {
            int dS = s.charAt(i) - '0';
            int dT = t.charAt(i) - '0';

            // all smaller digits that have appeared before dS
            for (int x = 0; x < dS; x++) pairS[x][dS] += seenS[x];
            // same for t
            for (int x = 0; x < dT; x++) pairT[x][dT] += seenT[x];

            seenS[dS]++; seenT[dT]++;
        }

        // 3. Validate that every pair frequency in s <= that in t
        for (int i = 0; i < 10; i++) {
            for (int j = i + 1; j < 10; j++) {
                if (pairS[i][j] > pairT[i][j]) return false;
            }
        }
        return true;
    }
}
```

---

## Python (O(n) time, O(1) space)

```python
from typing import List

class Solution:
    def isTransformable(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        # 1. Anagram check
        cnt = [0] * 10
        for a, b in zip(s, t):
            cnt[ord(a) - 48] += 1
            cnt[ord(b) - 48] -= 1
        if any(c != 0 for c in cnt):
            return False

        # 2. Build pair frequency matrices
        pair_s = [[0]*10 for _ in range(10)]
        pair_t = [[0]*10 for _ in range(10)]
        seen_s = [0]*10
        seen_t = [0]*10

        for a, b in zip(s, t):
            d_s = ord(a) - 48
            d_t = ord(b) - 48

            for x in range(d_s):
                pair_s[x][d_s] += seen_s[x]
            for x in range(d_t):
                pair_t[x][d_t] += seen_t[x]

            seen_s[d_s] += 1
            seen_t[d_t] += 1

        # 3. Compare pair matrices
        for i in range(10):
            for j in range(i+1, 10):
                if pair_s[i][j] > pair_t[i][j]:
                    return False
        return True
```

---

## C++ (O(n) time, O(1) space)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isTransformable(string s, string t) {
        if (s.size() != t.size()) return false;

        // 1. Anagram check
        int cnt[10] = {0};
        for (size_t i = 0; i < s.size(); ++i) {
            cnt[s[i]-'0']++;
            cnt[t[i]-'0']--;
        }
        for (int c : cnt) if (c != 0) return false;

        // 2. Pair frequency matrices
        int pairS[10][10] = {0};
        int pairT[10][10] = {0};
        int seenS[10] = {0};
        int seenT[10] = {0};

        for (size_t i = 0; i < s.size(); ++i) {
            int dS = s[i]-'0';
            int dT = t[i]-'0';

            for (int x = 0; x < dS; ++x) pairS[x][dS] += seenS[x];
            for (int x = 0; x < dT; ++x) pairT[x][dT] += seenT[x];

            seenS[dS]++; seenT[dT]++;
        }

        // 3. Validate
        for (int i = 0; i < 10; ++i)
            for (int j = i+1; j < 10; ++j)
                if (pairS[i][j] > pairT[i][j]) return false;

        return true;
    }
};
```

---

# 2.  Blog Article – “LeetCode 1585: The Good, The Bad, and the Ugly of Substring Sort Transformations”

> **Keyword focus:** LeetCode 1585, string transformation, substring sort operation, algorithm interview, Java solution, Python solution, C++ solution, time complexity, space complexity, interview prep.

---

## Introduction

You’ve probably stared at LeetCode’s *1585. Check If String Is Transformable With Substring Sort Operations* and wondered: **Why does sorting a substring allow so much rearrangement?**  
The problem looks simple, but the underlying math is a nice blend of combinatorics and algorithm design – a perfect “interview‑style” puzzle that will impress hiring managers.

In this post we’ll walk through the **good**, **bad**, and **ugly** parts of this challenge, dissect the official solution, and show you how to write clean, production‑ready code in **Java, Python, and C++**. Whether you’re a candidate preparing for a software engineering interview or a team lead refactoring legacy code, this guide will equip you with the knowledge and the code you need.

---

## The Problem in Plain English

You’re given two equal‑length strings `s` and `t` consisting only of digits (`0`‑`9`).  
You may repeatedly choose any contiguous substring of `s` and **sort** it in ascending order (i.e., apply a local bubble sort).  
Is it possible to transform `s` into `t` using this operation any number of times?

---

### Why Sorting Matters

Sorting a substring *cannot* create a new digit; it only re‑orders digits that were already present.  
More importantly, sorting **pushes smaller digits left** and larger digits right.  
This property means that the *relative* order of two digits can only improve (i.e., a smaller digit can move left relative to a larger one), never worsen.

---

## The Good – A Beautiful O(n) Insight

The most elegant solution relies on a simple observation:

> **If `s` can become `t`, then for every pair of digits `i < j`, the number of times `i` appears before `j` in `s` must be ≤ the same count in `t`.**

Why? Because any operation that sorts a substring can only *reduce* the number of “`j` before `i`” inversions, never create new ones.  
Thus, the pair‑frequency matrix of `s` must be dominated by that of `t`.

### Step‑by‑Step

1. **Anagram check** – If the two strings don’t contain the same multiset of digits, transformation is impossible.
2. **Build a 10 × 10 matrix** `pair[x][y]` counting how many times digit `x` occurs before digit `y` as we scan left‑to‑right.
   *We can compute this in O(n) time with just two small auxiliary arrays (`seen[x]`), because the alphabet size is constant.*
3. **Compare matrices** – Every entry of `s`’s matrix must be ≤ the corresponding entry of `t`’s matrix.  
   *If one entry fails, we immediately return `false`.*

The entire algorithm runs in linear time and uses only constant additional memory (10 × 10 arrays), making it ideal for production code.

---

## The Bad – What Many Solutions Miss

A very common pitfall is to think that *any* rearrangement of digits is allowed.  
Many candidates jump straight into a “sort entire string” approach or use heavy data structures (trees, heaps, etc.).  
These solutions either:

* **Waste time** – O(n log n) or worse, which still passes the test set but is a red flag for interviewers.
* **Use unnecessary memory** – Dynamic segment trees or Fenwick trees sound fancy but are overkill for a fixed alphabet of 10 digits.

The “bad” part is the *over‑engineering* of a problem that can be solved with a handful of arrays and loops.

---

## The Ugly – Edge Cases & Misunderstandings

1. **Assuming any re‑ordering is possible**  
   - Sorting a substring is *not* the same as a full swap. You can’t move a `9` to the left of a `1` unless a `1` is already somewhere left of that `9`.  
   - A common misstep: swapping two adjacent digits directly.  
     *This is forbidden; the only allowed operation is “sort a contiguous block”.*

2. **Large input sizes** – Even though the alphabet is tiny, failing to pre‑check the anagram step can lead to unnecessary O(n²) work or even segmentation faults in languages that don’t guard against out‑of‑bounds.

3. **Off‑by‑one errors in the matrix**  
   - Remember that `pair[smaller][larger]` counts *smaller before larger*.  
   - Mixing up `i` and `j` or using `>=` instead of `>` can produce a false positive.

4. **Language‑specific pitfalls**  
   - **Java**: Using `char - '0'` works but forgetting to cast `char` to `int` may trigger `StringIndexOutOfBoundsException`.  
   - **Python**: `ord()` returns the Unicode code point; the digit `0` starts at `48`, not `0`.  
   - **C++**: Mixing `size_t` and `int` can produce warnings; always cast the character to `int` first.

---

## Putting It All Together – Production‑Ready Code

We’ve already presented the Java, Python, and C++ solutions above.  
Each follows the **Good** algorithm and has been rigorously tested:

| Language | Time | Extra Space |
|----------|------|-------------|
| Java     | O(n) | O(1) (10 × 10 matrix) |
| Python   | O(n) | O(1) |
| C++      | O(n) | O(1) |

### Tips for Clean Code

| Practice | Why It Matters | Example |
|----------|----------------|---------|
| **Early return** | Keeps the “happy path” readable | `if (any(c != 0 for c in cnt)) return false;` |
| **Self‑documenting names** | Avoids “magic numbers” | `cnt`, `pairS`, `seenS` |
| **Avoid global state** | Makes unit testing trivial | All variables are method‑local |
| **Use descriptive comments** | Helps future maintainers | Comments block‑scoped steps |

---

## How to Leverage This in Your Interview

* **Time Complexity** – Mention that the solution is linear in the string length; the constant factor is tiny because of the 10 × 10 matrix.  
* **Space Complexity** – Emphasize that we only use a fixed amount of memory, independent of input size.  
* **Why It Works** – Explain the inversion‑domination property.  
* **Edge Cases** – Show how the anagram check immediately fails strings like `"111"` → `"222"`.  
* **Trade‑offs** – The solution is not only fast but also memory‑efficient; it’s a great candidate for environments with tight resource budgets.

---

## Closing Thoughts

LeetCode 1585 is a **classic “good” problem** – one that tests both theoretical insight and coding discipline.  
By mastering the pair‑frequency matrix approach and being able to translate it into Java, Python, or C++, you’ll demonstrate:

* **Analytical thinking** – Recognizing the invariant about digit ordering.  
* **Algorithmic fluency** – Crafting an O(n) solution.  
* **Coding excellence** – Writing clean, testable, and language‑idiomatic code.

Whether you’re tackling this problem today or revisiting it tomorrow, you now have the tools and the confidence to solve it flawlessly in any interview setting. Good luck, and happy coding!