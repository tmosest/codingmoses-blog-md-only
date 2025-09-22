---
title: LeetCode 2133. Check if Every Row and Column Contains All Numbers - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# 🚀 LeetCode 2133 – “Check if Every Row and Column Contains All Numbers”  
### The Good, The Bad, & The Ugly – A Full‑Stack Developer’s Take  
> *Job‑ready code samples (Java, Python, C++), a deep‑dive analysis, and an SEO‑friendly blog post that will get you noticed by recruiters.*

---

## 🎯 Problem Overview

> **Check if Every Row and Column Contains All Numbers**  
> **Difficulty:** Easy (LeetCode 2133)

> **Goal**:  
> Given an `n x n` integer matrix `matrix`, return `true` iff every row and every column contains **all** the integers from `1` to `n` **exactly once**.

> **Constraints**  
> * `1 ≤ n ≤ 100`  
> * `1 ≤ matrix[i][j] ≤ n`

---

## 💻 Code Solutions

> Below are clean, production‑ready solutions in **Java**, **Python 3**, and **C++17**.  
> Each snippet uses the most common and efficient technique (HashSet/Bitset) and includes comments for clarity.

---

### Java – HashSet (O(n²) time, O(n) space)

```java
import java.util.*;

public class Solution {
    /**
     * Checks if every row and column in the matrix contains all numbers from 1 to n.
     *
     * @param matrix The n x n integer matrix.
     * @return true if valid, false otherwise.
     */
    public boolean checkValid(int[][] matrix) {
        int n = matrix.length;

        for (int r = 0; r < n; ++r) {
            Set<Integer> row = new HashSet<>();
            Set<Integer> col = new HashSet<>();

            for (int c = 0; c < n; ++c) {
                if (!row.add(matrix[r][c]) || !col.add(matrix[c][r])) {
                    // Duplicate found in current row or column
                    return false;
                }
            }
        }
        return true;
    }
}
```

> **Why HashSet?**  
> *Fast O(1) average‑time insert/check.*  
> *No need to clear the set after each row/column – a new set is created per outer‑loop iteration.*

---

### Python 3 – Set / Bitset (O(n²) time, O(n) space)

```python
from typing import List

class Solution:
    def checkValid(self, matrix: List[List[int]]) -> bool:
        n = len(matrix)

        # Simple set‑based implementation
        for row, col in zip(matrix, zip(*matrix)):
            if len(set(row)) != n or len(set(col)) != n:
                return False
        return True
```

> **Alternative (bytearray/Bitset)** – even faster in practice when `n` ≤ 100.

```python
class Solution:
    def checkValid(self, matrix: List[List[int]]) -> bool:
        n = len(matrix)
        for r in range(n):
            row_used = bytearray(n + 1)   # indices 1..n
            col_used = bytearray(n + 1)
            for c in range(n):
                val_r, val_c = matrix[r][c], matrix[c][r]
                if row_used[val_r] or col_used[val_c]:
                    return False
                row_used[val_r] = 1
                col_used[val_c] = 1
        return True
```

---

### C++17 – `std::unordered_set` (O(n²) time, O(n) space)

```cpp
#include <vector>
#include <unordered_set>

class Solution {
public:
    bool checkValid(std::vector<std::vector<int>>& matrix) {
        int n = matrix.size();

        for (int r = 0; r < n; ++r) {
            std::unordered_set<int> row, col;
            for (int c = 0; c < n; ++c) {
                if (!row.insert(matrix[r][c]).second || !col.insert(matrix[c][r]).second) {
                    return false;   // duplicate in row or column
                }
            }
        }
        return true;
    }
};
```

> **Why `unordered_set`?**  
> *Average O(1) insert/check; negligible overhead for `n ≤ 100`.*

---

## 📊 Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Brute‑force (nested loops, checking entire row/col each time) | **O(n³)** | **O(1)** |
| HashSet / Bitset (recommended) | **O(n²)** | **O(n)** |
| 2‑D boolean matrix (explicit `row[i][num]`, `col[j][num]`) | **O(n²)** | **O(n²)** |

> **Why the HashSet solution wins**  
> *Runs in the same asymptotic time as the best possible solution*  
> *Uses only linear extra memory (≤ 200 `int`s for `n = 100`)*  
> *Easy to read, maintain, and translate between languages.*

---

## 🌟 The Good, The Bad & The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Simplicity** | Set‑based approach is concise and self‑documenting. | Over‑engineering (e.g., `O(n²)` boolean matrix) adds noise. | Nested loops with manual index juggling and manual duplicate checks is error‑prone. |
| **Performance** | `O(n²)` with tiny constant factors. | `O(n³)` brute force becomes infeasible for `n = 100`. | Using `std::set` (RB‑tree) instead of `unordered_set` or `HashSet` incurs `O(log n)` inserts. |
| **Space** | `O(n)` sets, automatically cleaned each row/column. | `O(n²)` arrays for `row`/`col` duplication. | Memory leak potential if sets aren’t cleared or reused properly. |
| **Readability** | Clear variable names, comments, and one‑liner logic. | Implicit assumptions (e.g., that input is always valid). | Mixing bitwise tricks (`BitSet`, `bytearray`) with complex logic can confuse reviewers. |

> **Takeaway**:  
> In an interview setting, pick the **HashSet / Bitset** solution – it’s fast, clear, and demonstrates solid data‑structure knowledge.

---

## 🎯 Why This Blog Helps You Land a Job

1. **SEO‑Optimized Headings** – “LeetCode 2133”, “Check if Every Row and Column Contains All Numbers”, “Java Python C++ solution” appear in title, H1, and H2 tags.
2. **Keyword‑Rich Content** – Terms such as *“row uniqueness”*, *“column uniqueness”*, *“hashset”*, *“bitset”*, *“time complexity”*, *“space complexity”*, *“job interview”* are sprinkled naturally.
3. **Clear Code Samples** – Recruiters can instantly copy‑paste and run the snippets to verify your understanding.
4. **Problem‑Solving Mindset** – The “Good, Bad, Ugly” section shows you not just how to solve, but how to critique solutions – a highly‑valued interview skill.

---

## 📚 How to Use These Solutions in Your Portfolio

1. **GitHub Repository** – Commit each solution file (`SolutionJava.java`, `solution.py`, `solution.cpp`) with a README that references this blog.
2. **Blog Post** – Publish on Medium/Dev.to, including a live‑code‑embedded demo (e.g., using **repl.it** or **Gist**).
3. **LinkedIn Article** – Post the blog with a short description: “Solving LeetCode 2133 in Java, Python, C++ – Time & Space analysis – Interview‑ready! #LeetCode #CodingInterview #Java #Python #Cpp”.
4. **Resume Bullet** – “Implemented an O(n²) validator for n×n matrices (LeetCode 2133) using language‑agnostic data structures, reducing memory footprint to O(n).”

---

## ✅ Checklist Before Your Next Coding Interview

- ✅ Read the problem constraints *before* writing code.  
- ✅ Choose the **HashSet / Bitset** pattern.  
- ✅ Write clean, commented code.  
- ✅ Explain time/space complexity on the fly.  
- ✅ Critique an alternate (brute‑force) solution – practice the “Good, Bad, Ugly” analysis.  

---

### 🎉 Final Thought

LeetCode 2133 may be labeled “Easy”, but mastering its pattern of **row/column uniqueness** demonstrates a fundamental grasp of hash‑based lookups and algorithmic optimisation.  

> **Show it off, blog it, share it** – your next recruiter will thank you for the clarity and depth.  

Good luck, and happy coding! 🚀

---