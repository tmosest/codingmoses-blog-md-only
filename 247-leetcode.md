---
title: LeetCode 247. Strobogrammatic Number II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 247. Strobogrammatic Number II  
**Java / Python / C++ – Full Solutions + Interview‑Ready Blog Post**

> *LeetCode 247 – Strobogrammatic Number II*  
> *Difficulty: Medium*  
> *Time‑Limit: 1 s (≈ 5 × 10⁵ recursive calls max)*  
> *Space‑Limit: 256 MB*

---

### Table of Contents  
1. [Problem Statement](#problem-statement)  
2. [What Is a Strobogrammatic Number?](#what-is-a-strobogrammatic-number)  
3. [High‑Level Strategy](#high-level-strategy)  
4. [Good – The Simple Recursion Approach](#good)  
5. [Bad – Edge Cases & Leading Zeros](#bad)  
6. [Ugly – Handling Extremely Large `n`](#ugly)  
7. [Java Implementation](#java-implementation)  
8. [Python Implementation](#python-implementation)  
9. [C++ Implementation](#c++-implementation)  
10. [Complexity Analysis](#complexity-analysis)  
11. [Alternative Approaches](#alternative-approaches)  
12. [Interview Tips & Common Mistakes](#interview-tips)  
13. [SEO‑Optimized Summary](#seo-summary)  
14. [Conclusion](#conclusion)

---

## 1. Problem Statement <a name="problem-statement"></a>  

> **Given an integer `n`, return *all* strobogrammatic numbers of length `n`.**  
> A strobogrammatic number is a number that appears the same after rotating it 180° (upside‑down).  

**Examples**

| `n` | Output |
|-----|--------|
| 2   | `["11","69","88","96"]` |
| 1   | `["0","1","8"]` |

**Constraints**

* `1 ≤ n ≤ 14`

---

## 2. What Is a Strobogrammatic Number? <a name="what-is-a-strobogrammatic-number"></a>

| Digit | Rotated (180°) | Valid Pair |
|-------|----------------|------------|
| 0     | 0              | 0–0 |
| 1     | 1              | 1–1 |
| 6     | 9              | 6–9 |
| 8     | 8              | 8–8 |
| 9     | 6              | 9–6 |

A number is strobogrammatic if the *i*‑th digit from the left equals the *i*‑th digit from the right after applying the above mapping.

---

## 3. High‑Level Strategy <a name="high-level-strategy"></a>

* **Recursive Backtracking** – Build the number from the outside in.  
* **Base Cases** –  
  * `n == 0` → empty string (used for building even‑length numbers).  
  * `n == 1` → `["0","1","8"]` (the middle digit of odd‑length numbers).  
* **Recursive Step** – For every string `inner` from `helper(n-2, m)` prepend/append each valid pair.  
* **Avoid Leading Zeros** – Skip `"0" + inner + "0"` when `n == m` (outermost layer).  

This yields all combinations in `O(5^(n/2))` time and space.

---

## 4. Good – The Simple Recursion Approach <a name="good"></a>

* **Readability** – One function, one clear loop.  
* **Natural Fit** – The problem is literally “build from the edges in.”  
* **Extensibility** – Easy to add new digit pairs if the definition changes.  
* **No Extra Data Structures** – Uses only lists/vectors of strings.

---

## 5. Bad – Edge Cases & Leading Zeros <a name="bad"></a>

* Forgetting to exclude `"0...0"` at the outermost level generates numbers like `"00"`, `"000"`, which are **invalid** because they have leading zeros.  
* A naive implementation that always adds the zero pair will produce `4` extra strings for each outer layer, inflating the result set.  
* When `n` is odd, the middle layer should only contain `"0"`, `"1"`, `"8"`. Adding `"0"` on the sides would create numbers of length `n+2` – a classic off‑by‑one bug.

---

## 6. Ugly – Handling Extremely Large `n` <a name="ugly"></a>

* **Stack Overflow** – Recursion depth grows with `n`. For `n = 14` the depth is only `7`, so it’s safe, but for generalization (e.g., `n = 10⁴`) you’d hit stack limits.  
* **Memory Footprint** – The number of results is `5^(n/2)`; for `n = 14` it is `5^7 = 78 125`.  
  * Each string is length 14 → ~1 KB per result → ~78 MB total.  
  * If the interviewer asks for *count* only, you can avoid materializing all strings.  

---

## 7. Java Implementation <a name="java-implementation"></a>

```java
import java.util.*;

public class Solution {
    public List<String> findStrobogrammatic(int n) {
        return helper(n, n);
    }

    private List<String> helper(int n, int m) {
        // Base cases
        if (n == 0) return new ArrayList<>(Collections.singletonList(""));
        if (n == 1) return new ArrayList<>(Arrays.asList("0", "1", "8"));

        List<String> res = new ArrayList<>();
        // Recurse for the inner part
        List<String> inner = helper(n - 2, m);

        for (String s : inner) {
            if (n != m) res.add("0" + s + "0"); // skip leading zero at outermost level
            res.add("1" + s + "1");
            res.add("6" + s + "9");
            res.add("8" + s + "8");
            res.add("9" + s + "6");
        }
        return res;
    }
}
```

> **Why it’s clean** – The helper receives the *original* `m` to know when to avoid `"0"` pair.  
> **Time** – O(5^(n/2))  
> **Space** – O(5^(n/2))

---

## 8. Python Implementation <a name="python-implementation"></a>

```python
from typing import List

class Solution:
    def findStrobogrammatic(self, n: int) -> List[str]:
        return self._helper(n, n)

    def _helper(self, n: int, m: int) -> List[str]:
        if n == 0:
            return [""]
        if n == 1:
            return ["0", "1", "8"]

        res = []
        for inner in self._helper(n - 2, m):
            if n != m:            # avoid leading zeros
                res.append("0" + inner + "0")
            res.append("1" + inner + "1")
            res.append("6" + inner + "9")
            res.append("8" + inner + "8")
            res.append("9" + inner + "6")
        return res
```

> **Pythonic note** – Using recursion keeps the code short; if you prefer an iterative BFS, just replace the helper with a queue loop.

---

## 9. C++ Implementation <a name="c++-implementation"></a>

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<string> findStrobogrammatic(int n) {
        return helper(n, n);
    }
private:
    vector<string> helper(int n, int m) {
        if (n == 0) return {""};
        if (n == 1) return {"0", "1", "8"};

        vector<string> res;
        vector<string> inner = helper(n - 2, m);

        for (const string &s : inner) {
            if (n != m) res.push_back("0" + s + "0"); // avoid leading zeros
            res.push_back("1" + s + "1");
            res.push_back("6" + s + "9");
            res.push_back("8" + s + "8");
            res.push_back("9" + s + "6");
        }
        return res;
    }
};
```

> **Why C++?** – The same recursion logic; the only difference is manual memory management via `std::vector`.  

---

## 10. Complexity Analysis <a name="complexity-analysis"></a>

| Operation | Time | Space |
|-----------|------|-------|
| Recursion tree depth | `O(n/2)` | `O(n/2)` (call stack) |
| Number of generated strings | `5^(n/2)` | `5^(n/2)` (output list) |
| Total | `O(5^(n/2))` | `O(5^(n/2))` |

*Reason*: At each level we generate 5 times more strings than the previous inner level (except the outermost level where we skip the “00” pair, giving 4 * 5^(k-1) for even `n`).

---

## 11. Alternative Approaches <a name="alternative-approaches"></a>

| Method | Pros | Cons |
|--------|------|------|
| **Iterative BFS (queue)** | No recursion, easier to understand for beginners | Slightly more boilerplate, same asymptotic complexity |
| **DP / Memoization** | Reuse sub‑problems for larger `n` | Extra memory for memo tables, overkill for `n ≤ 14` |
| **Count‑only** | O(1) space if only the number of solutions is needed | Cannot return the strings |

> **When to pick BFS?** If your interview panel prefers iterative solutions or if you fear stack overflow for larger `n`.  

---

## 12. Interview Tips & Common Mistakes <a name="interview-tips"></a>

1. **Clarify the definition** – Confirm that digits “6” and “9” are allowed and “0” may appear in the middle only for odd lengths.
2. **Ask about leading zeros** – Mention that numbers like “00” are invalid unless `n == 1`.
3. **State the complexity** – Mention `O(5^(n/2))` time/space; if `n` were huge, discuss how to modify the algorithm.
4. **Edge cases first** – Discuss base cases explicitly before diving into recursion.
5. **Testing** – After writing code, test for `n=1,2,3,4,5` to ensure correct handling of odd/even lengths.
6. **Avoid hard‑coding 4 or 5** – Use a data structure of pairs to keep the code flexible.

---

## 13. SEO‑Optimized Summary <a name="seo-summary"></a>

> This guide delivers the **LeetCode “Strobogrammatic Number II”** solution in **Java**, **Python**, and **C++**.  
> The concise recursive algorithm builds all strobogrammatic numbers of length `n` (1 ≤ n ≤ 14) in `O(5^(n/2))` time.  
> Key points: base cases (`n==0` → `""`, `n==1` → `["0","1","8"]`), avoid leading zeros at the outermost level, and skip unnecessary recursion.  
> Complexity: exponential `5^(n/2)`.  
> Alternative BFS/iterative versions have identical performance.  
> Interviewers often look for the ability to handle edge cases and discuss complexity; avoid “0…0” outputs and stack overflows.  

---

## 13. Conclusion <a name="conclusion"></a>

The recursive backtracking solution is the most **natural** and **readable** way to generate all strobogrammatic numbers. It cleanly handles odd/even lengths, skips leading zeros, and works comfortably within the problem’s constraints. For interviewers who prefer iterative logic, a queue‑based BFS yields the same result with no recursion.  

> **Pro tip** – Always ask whether the interviewer wants the *count* or the *list*; if only the count matters, you can return `4 * 5^(n/2-1)` for even `n` and `4 * 5^((n-1)/2)` for odd `n`, saving memory.

Good luck cracking “Strobogrammatic Number II” in your next coding interview! 🚀

---  

### References

* LeetCode problem: https://leetcode.com/problems/strobogrammatic-number-ii/
* Discussed solutions on GitHub (Java, Python, C++)

--- 

**Keywords for SEO**: Strobogrammatic Number II, LeetCode solution, recursive backtracking, Java solution, Python solution, C++ solution, interview tips, time complexity, space complexity, BFS alternative.