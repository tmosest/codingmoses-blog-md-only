---
title: LeetCode 96. Unique Binary Search Trees - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📚 Unique Binary Search Trees – 96. LeetCode – A Deep Dive  
**Good, Bad, and Ugly – How to Nail It in Your Next Interview**

> **Keywords:** Unique Binary Search Trees, LeetCode 96, DP Catalan Numbers, Java, Python, C++, Interview Preparation

---

### 🚀 TL;DR  
- **Problem:** Count the number of structurally unique binary search trees that can be built with `n` distinct keys 1…n.  
- **Answer:** The result is the *n‑th Catalan number*.  
- **Complexity:** `O(n²)` time, `O(n)` space.  
- **Why it matters:** This problem is a classic DP exercise that showcases your ability to identify combinatorial patterns, implement efficient recurrence relations, and write clean code across multiple languages.

---

## 1. Problem Statement (LeetCode 96)

> **Given an integer `n`, return the number of structurally unique BSTs (binary search trees) which have exactly `n` nodes with distinct values from `1` to `n`.**  
> **Constraints:** `1 <= n <= 19`.

---

## 2. The Math Behind the Answer

### 2.1 Catalan Numbers

The number of distinct BSTs with `n` nodes is the `n`‑th Catalan number:

\[
C_n = \frac{(2n)!}{n!(n+1)!} \quad\text{or}\quad
C_n = \sum_{i=1}^{n} C_{i-1} \times C_{n-i}
\]

The recurrence arises from choosing a root:  
- For root `i`, the left subtree has `i-1` nodes, the right subtree has `n-i` nodes.  
- The number of trees with root `i` is `C_{i-1} * C_{n-i}`.  
- Sum over all possible roots.

### 2.2 Why `n <= 19`?

The 19th Catalan number is `1767263190`, fitting comfortably in a 32‑bit signed integer (`int`). However, to stay safe (and because Java’s `int` is 32‑bit, Python’s `int` is arbitrary‑precision, and C++’s `long long` is 64‑bit), we’ll use `long`/`long long` in Java/C++ and simply `int` in Python.

---

## 3. Algorithm Design

| Step | Explanation |
|------|-------------|
| **Initialize** an array `dp[0…n]` where `dp[i]` will hold the number of unique BSTs with `i` nodes. |
| **Base cases** | `dp[0] = 1` (empty tree), `dp[1] = 1` (single node). |
| **Iterate** over `nodes = 2 … n` | For each possible number of nodes, compute `dp[nodes]` via the recurrence. |
| **Return** `dp[n]`. |

Time complexity: `O(n²)` (double loop).  
Space complexity: `O(n)` (the DP array).

---

## 4. Code Implementation

Below are clean, idiomatic implementations in **Java**, **Python**, and **C++**. Each one follows the same DP strategy and is ready for an interview.

### 4.1 Java

```java
// LeetCode 96: Unique Binary Search Trees
// Time O(n^2), Space O(n)

public class Solution {
    public int numTrees(int n) {
        long[] dp = new long[n + 1];   // use long to avoid overflow
        dp[0] = 1;                     // empty tree
        dp[1] = 1;                     // single node

        for (int nodes = 2; nodes <= n; nodes++) {
            long count = 0;
            for (int root = 1; root <= nodes; root++) {
                int left  = root - 1;          // nodes in left subtree
                int right = nodes - root;      // nodes in right subtree
                count += dp[left] * dp[right];
            }
            dp[nodes] = count;
        }
        return (int) dp[n];  // result fits in int for n <= 19
    }
}
```

### 4.2 Python

```python
# LeetCode 96: Unique Binary Search Trees
# Time O(n^2), Space O(n)

def numTrees(n: int) -> int:
    dp = [0] * (n + 1)
    dp[0] = 1  # empty tree
    dp[1] = 1  # single node

    for nodes in range(2, n + 1):
        total = 0
        for root in range(1, nodes + 1):
            left, right = root - 1, nodes - root
            total += dp[left] * dp[right]
        dp[nodes] = total

    return dp[n]
```

### 4.3 C++

```cpp
// LeetCode 96: Unique Binary Search Trees
// Time O(n^2), Space O(n)

#include <vector>
using namespace std;

class Solution {
public:
    int numTrees(int n) {
        vector<long long> dp(n + 1, 0);
        dp[0] = 1; // empty tree
        dp[1] = 1; // single node

        for (int nodes = 2; nodes <= n; ++nodes) {
            long long count = 0;
            for (int root = 1; root <= nodes; ++root) {
                int left  = root - 1;
                int right = nodes - root;
                count += dp[left] * dp[right];
            }
            dp[nodes] = count;
        }
        return static_cast<int>(dp[n]); // fits into int for n <= 19
    }
};
```

---

## 5. The “Good, the Bad, and the Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Mathematical Insight** | Catalan numbers give a clean recurrence. | Not obvious to beginners. | Mis‑interpreting the recurrence leads to off‑by‑one bugs. |
| **Time Complexity** | `O(n²)` is optimal for DP. | Naïve recursion (`O(n!)`) is infeasible. | Over‑engineering (e.g., matrix exponentiation) is unnecessary. |
| **Space Optimization** | One‑dimensional DP array. | Two‑dimensional DP with `n²` memory. | Recursion stack overflow if not memoized. |
| **Edge Cases** | Handles `n=0` (empty tree) gracefully. | Forgetting `dp[0] = 1` breaks the formula. | Returning `int` when `long` needed for larger `n`. |
| **Language Nuances** | Java `long` to avoid overflow. | Python’s arbitrary precision hides overflow but costs speed. | C++ `long long` necessary for safety. |
| **Testing** | Use `n=3 → 5`, `n=1 → 1`, `n=19 → 1767263190`. | Missing tests for `n=0` or large `n`. | Wrong base case leads to `0` for all `n`. |

**Takeaway:** A clear DP solution, correct base cases, and proper type handling are the “good.” Avoid overcomplicating with recursion or memo tables beyond necessity. The “ugly” usually comes from not thinking through the recurrence or mis‑handling integer sizes.

---

## 6. Interview‑Ready Tips

1. **Explain the recurrence first.** Talk about choosing a root and multiplying left/right subtree counts.  
2. **Show the Catalan link** – employers love when you connect a problem to a well‑known sequence.  
3. **Discuss time/space trade‑offs.** Mention why `O(n²)` DP beats naive recursion.  
4. **Highlight base cases.** Stress the importance of `dp[0] = 1`.  
5. **Mention constraints.** Show you checked that the 19th Catalan number fits in `int`.  

---

## 7. SEO‑Optimized Blog Summary

### Meta Description
> Master LeetCode 96 – Unique Binary Search Trees. Read our in‑depth guide with Java, Python, and C++ solutions, dynamic programming insights, and interview prep tips. Perfect for software engineers ready to land their next job.

### Suggested Headings
- `Unique Binary Search Trees – 96`
- `Why Catalan Numbers Matter in DP`
- `Step‑by‑Step Java Solution`
- `Python Implementation for Quick Grabs`
- `C++ Fast and Safe Code`
- `The Good, the Bad, and the Ugly of BST Counting`
- `Interview Hacks: Talking About This Problem`

### Keywords to Sprinkle
- unique binary search trees
- leetcode 96 solution
- dynamic programming Catalan
- interview question BST
- Java BST count
- Python DP example
- C++ coding challenge
- software engineer interview prep

---

## 8. Final Thoughts

- **Keep it simple.** A clear DP recurrence wins interviews.
- **Show versatility.** Present solutions in multiple languages—demonstrates adaptability.
- **Relate to fundamentals.** Catalan numbers tie combinatorics, graph theory, and DP together.
- **Practice.** Try variations: count BSTs with node values not 1…n, or count BSTs with height constraints.

Good luck landing that job! 🚀