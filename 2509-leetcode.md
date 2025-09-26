---
title: LeetCode 2509. Cycle Length Queries in a Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Cycle Length Queries in a Tree – A Complete, SEO‑Optimized Solution Guide  
> *LeetCode 2509 – Hard – Java / Python / C++*

---

## Table of Contents  

| Section | What you’ll learn |
|---------|-------------------|
| 🏁 Problem Overview | Understand the graph problem and why it’s hard |
| 📌 Key Insight | Lowest Common Ancestor (LCA) trick |
| 🧠 Algorithm | Step‑by‑step solution in plain English |
| ⏱️ Complexity Analysis | Time & Space trade‑offs |
| 💻 Code | Java, Python & C++ implementations |
| 🗣️ Good, Bad & Ugly | Where the approach shines, fails, and how to tweak |
| 🔑 SEO Highlights | Why this blog helps your job search |
| 🎯 TL;DR | Quick take‑away |

---

## 1. Problem Overview

> **Given** a perfect binary tree with `2^n − 1` nodes (root = 1).  
> For each query `[ai, bi]`:  
> 1. Add an edge between nodes `ai` and `bi`.  
> 2. Find the length of the unique cycle created.  
> 3. Remove the added edge.  

**Goal:** Return an array of cycle lengths for all queries.

**Why Hard?**  
The tree can have up to 2^30 − 1 nodes (≈1 billion), but we only get at most 10⁵ queries. Building the tree explicitly is impossible; we must answer each query in *logarithmic* time.

---

## 2. Key Insight – LCA is the Secret

A perfect binary tree built like a binary heap has an easy parent rule:

```
parent(v) = v / 2   (integer division)
```

When we connect two nodes `a` and `b`, the only cycle that can appear is:

```
a ──(a to LCA)── LCA ──(LCA to b)── b ──(extra edge)── a
```

The length of the cycle equals

```
distance(a, LCA) + distance(b, LCA) + 1
```

But we can compute it without explicitly finding the distance, by simply moving the *larger* node upward until both meet. Each upward step counts as one edge, and we finally add one more for the newly inserted edge.

---

## 3. Algorithm in Plain English

```
for each query (a, b):
    length = 1            // the added edge
    while a != b:
        if a > b:
            a = a / 2    // move a up
        else:
            b = b / 2    // move b up
        length += 1
    store length
```

Why does it work?

- Moving the larger node upwards keeps the total number of moves minimal: we always reduce the larger of the two values until they become equal.  
- Each division corresponds to traversing one tree edge.  
- The loop ends when `a == b`, which is exactly the LCA.  
- The final `length` is the number of edges in the cycle.

---

## 4. Complexity Analysis

| Metric | Computation | Explanation |
|--------|-------------|-------------|
| **Time per query** | `O(log n)` | Tree height ≤ `log₂(2ⁿ−1)` = `n`. Each loop iteration reduces the larger node by at least a factor of 2. |
| **Total time** | `O(m · log n)` | `m ≤ 10⁵`, `n ≤ 30`. At most 30 * 10⁵ ≈ 3 M operations – easily within limits. |
| **Space** | `O(1)` (apart from output) | No auxiliary data structures are needed. |

---

## 5. Code

Below you’ll find the **stand‑alone** solution for each language.  
Each implementation follows the exact algorithm above.

### 5.1 Java

```java
import java.util.*;

public class Solution {
    public int[] cycleLengthQueries(int n, int[][] queries) {
        int m = queries.length;
        int[] ans = new int[m];
        for (int i = 0; i < m; ++i) {
            int a = queries[i][0];
            int b = queries[i][1];
            int length = 1;                    // the added edge
            while (a != b) {
                if (a > b) a >>= 1;            // a /= 2
                else        b >>= 1;           // b /= 2
                ++length;
            }
            ans[i] = length;
        }
        return ans;
    }

    /* Optional: Main for quick manual testing */
    public static void main(String[] args) {
        Solution sol = new Solution();
        int[][] q = {{5,3},{4,7},{2,3}};
        System.out.println(Arrays.toString(sol.cycleLengthQueries(3, q)));
        // Output: [4, 5, 3]
    }
}
```

### 5.2 Python

```python
class Solution:
    def cycleLengthQueries(self, n: int, queries: List[List[int]]) -> List[int]:
        ans = []
        for a, b in queries:
            length = 1
            while a != b:
                if a > b:
                    a //= 2
                else:
                    b //= 2
                length += 1
            ans.append(length)
        return ans

# Quick test
if __name__ == "__main__":
    sol = Solution()
    print(sol.cycleLengthQueries(3, [[5,3],[4,7],[2,3]]))
    # Output: [4, 5, 3]
```

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> cycleLengthQueries(int n, vector<vector<int>>& queries) {
        vector<int> ans;
        for (auto &q : queries) {
            int a = q[0], b = q[1];
            int len = 1;                 // added edge
            while (a != b) {
                if (a > b) a /= 2;
                else       b /= 2;
                ++len;
            }
            ans.push_back(len);
        }
        return ans;
    }
};

// Optional main for quick tests
int main() {
    Solution sol;
    vector<vector<int>> q = {{5,3},{4,7},{2,3}};
    vector<int> res = sol.cycleLengthQueries(3, q);
    for (int x : res) cout << x << " ";
    // Output: 4 5 3
    return 0;
}
```

---

## 6. Good, Bad & Ugly

| Aspect | Good | Bad | Ugly / Gotchas |
|--------|------|-----|----------------|
| **Time** | Fast (`O(log n)` per query). | None. | For *extremely* many queries (`>10⁶`) you might hit the constant factor of I/O. |
| **Space** | Minimal (`O(1)`). | – | – |
| **Readability** | Very clear logic. | None. | Requires understanding of integer division as parent move. |
| **Edge Cases** | Handles root & leaf nodes seamlessly. | – | If you accidentally swap `a` and `b`, algorithm still works because of symmetry. |
| **Extensibility** | Works for *any* complete binary tree. | – | Does **not** generalise to arbitrary trees – requires parent function. |
| **Implementation Pitfalls** | – | In Python, use `//` (integer division). In C++, use `/`. In Java, be careful with `>>` vs `/`. | Mis‑reading the problem as “find the longest cycle” – there is only one cycle per query. |

---

## 7. SEO Highlights – Why This Blog Helps Your Career

1. **Keyword‑Rich Title**  
   *Cycle Length Queries in a Tree – LeetCode 2509 Hard Solution (Java / Python / C++)* – matches what recruiters are searching for.

2. **Problem & Language Tags**  
   Uses tags like `BinaryTree`, `LCA`, `LeetCodeHard`, `Interview`, `CodingInterview`, `Java`, `Python`, `C++`.  
   Recruiters often filter candidates by these tags.

3. **Clear, Structured Code**  
   Provides *copy‑paste* solutions, reducing friction for recruiters who want to see clean code.

4. **Performance Discussion**  
   Shows you can solve huge constraints with `O(log n)` complexity – a real interview brag point.

5. **Self‑Contained**  
   Includes `main` / test harnesses so you can run and verify locally.

6. **Explanation of Edge Cases**  
   Shows depth of understanding – recruiters love candidates who think about edge conditions.

---

## 7. TL;DR

- **Insight:** The cycle length equals the number of upward moves to the LCA plus one.  
- **Loop:** `while (a != b) { if (a > b) a /= 2; else b /= 2; length++; }`  
- **Result:** `O(m · log n)` time, `O(1)` extra space.  
- **Languages:** Java, Python, C++ – same algorithm, tiny syntax differences.

---

## 8. Final Words

This blog gives you:

- A *complete* solution for LeetCode 2509 in **Java, Python, and C++**.  
- An **easy‑to‑implement algorithm** that runs in *logarithmic* time – a must‑know for any algorithmic interview.  
- A clear, *SEO‑friendly* write‑up that showcases your problem‑solving and coding skills to potential employers.

Happy coding—and good luck landing that dream role! 🎉