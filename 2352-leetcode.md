---
title: LeetCode 2352. Equal Row and Column Pairs - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 LeetCode 2352 – Equal Row and Column Pairs  
**Target Audience:** Front‑end, Back‑end, Data‑Structure & Algorithms candidates  
**Languages Covered:** Java | Python | C++  
**Goal:** Get a solid interview‑ready solution, understand *good*, *bad*, *ugly* patterns, and write a blog‑style article that’s SEO‑friendly.

---

### 🔍 Problem Recap

> **Grid** – a 0‑indexed *n × n* integer matrix (`1 ≤ n ≤ 200`).  
> Count how many pairs `(ri, cj)` exist such that the entire *row* `ri` equals the *column* `cj` element‑by‑element.  
>  
> **Example**  
> ```
> grid = [[3,1,2,2],
>         [1,4,4,5],
>         [2,4,2,2],
>         [2,4,2,2]]
> Output: 3
> ```
>  
> **Constraints**  
> - `grid[i][j]` fits in a 32‑bit integer  
> - Time limit: ~1 sec  

---

## 🧠 Intuition

A row and a column are equal if the **sequence of numbers** is identical.  
Therefore, each row can be thought of as a *string* (or tuple) and each column as another string.  
If we can **look‑up** a column string in the set of row strings, we have a match.  
The key is to do this in **O(n²)** time instead of a naive **O(n³)**.

---

## 📦 Solution Outline

1. **Encode rows** into a hashable representation (string, tuple, vector).  
2. Store each encoded row in a *hash map* with its frequency.  
3. Iterate through every column, encode it the same way, and **lookup** the map.  
4. Add the retrieved frequency to the answer.

> **Why this works?**  
> - Each row is processed once → `O(n²)`  
> - Each column is processed once → `O(n²)`  
> - Map operations are average `O(1)` → overall `O(n²)`.

---

## ⚙️ Code Snippets

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.  
Feel free to copy‑paste into your IDE or LeetCode editor.

---

### Java

```java
import java.util.*;

public class Solution {
    public int equalPairs(int[][] grid) {
        int n = grid.length;
        int result = 0;

        // 1. Store all rows in a hashmap (rowString -> frequency)
        Map<String, Integer> rowMap = new HashMap<>();
        for (int i = 0; i < n; i++) {
            // Convert row to a comma‑separated string for uniqueness
            StringBuilder sb = new StringBuilder();
            for (int val : grid[i]) {
                sb.append(val).append(','); // trailing comma ensures distinctness
            }
            String key = sb.toString();
            rowMap.put(key, rowMap.getOrDefault(key, 0) + 1);
        }

        // 2. Iterate over columns, build the string, and lookup
        for (int col = 0; col < n; col++) {
            StringBuilder sb = new StringBuilder();
            for (int row = 0; row < n; row++) {
                sb.append(grid[row][col]).append(',');
            }
            String key = sb.toString();
            result += rowMap.getOrDefault(key, 0);
        }

        return result;
    }
}
```

> **Why a `StringBuilder`?**  
> *StringBuilder* is more efficient than `Arrays.toString()` for repeated concatenation.

---

### Python

```python
from typing import List

class Solution:
    def equalPairs(self, grid: List[List[int]]) -> int:
        n = len(grid)
        # 1. Count all rows as tuples (hashable)
        row_counts = {}
        for row in grid:
            key = tuple(row)           # tuples are hashable
            row_counts[key] = row_counts.get(key, 0) + 1

        # 2. Build columns as tuples and look up
        result = 0
        for col in range(n):
            col_tuple = tuple(grid[row][col] for row in range(n))
            result += row_counts.get(col_tuple, 0)

        return result
```

> **Python tip** – tuples are immutable and hashable, making them perfect dictionary keys for sequences.

---

### C++

```cpp
#include <vector>
#include <string>
#include <unordered_map>

class Solution {
public:
    int equalPairs(std::vector<std::vector<int>>& grid) {
        int n = grid.size();
        int result = 0;

        // Helper to convert a row/column to a string key
        auto toKey = [n](const std::vector<int>& v) -> std::string {
            std::string key;
            key.reserve(n * 12);          // rough reserve
            for (int x : v) {
                key += std::to_string(x);
                key += ',';                // delimiter
            }
            return key;
        };

        // 1. Store rows
        std::unordered_map<std::string, int> rowMap;
        for (const auto& row : grid) {
            std::string key = toKey(row);
            ++rowMap[key];
        }

        // 2. Check columns
        for (int col = 0; col < n; ++col) {
            std::vector<int> colVec(n);
            for (int row = 0; row < n; ++row)
                colVec[row] = grid[row][col];
            std::string key = toKey(colVec);
            auto it = rowMap.find(key);
            if (it != rowMap.end())
                result += it->second;
        }

        return result;
    }
};
```

> **Why string conversion in C++?**  
> The `std::vector` itself isn’t hashable by default; converting to a string keeps code simple and fast enough for `n ≤ 200`.

---

## 📊 Complexity Analysis

| Metric | Formula | Result |
|--------|---------|--------|
| **Time** | `O(n²)` (process n rows + n columns) | ≤ 40 000 operations for `n = 200` |
| **Space** | `O(n²)` for the map (worst case each row is unique) | ≤ 40 000 key/value pairs |

---

## 🎭 Good, Bad, and Ugly

| Category | What to Do | What to Avoid |
|----------|------------|---------------|
| **Good** | • Use a hash map (or dictionary) to store rows. <br>• Convert rows/columns to a *unique* key (string/tuple). <br>• Leverage language‑native hash tables for O(1) average lookup. | |
| **Bad** | • Naïve O(n³) three‑nested loops (compare each row‑column pair element‑by‑element). <br>• Re‑creating entire column array for every comparison without caching. | |
| **Ugly** | • Over‑complicated custom hash functions for vectors. <br>• Manual string concatenation with `%s`/`+` inside loops leading to quadratic string builds. <br>• Forgetting delimiters causing hash collisions (e.g., `[1,23]` vs `[12,3]`). | |

---

## 🚀 Interview Tips

1. **Explain your idea first** – talk through the hash map trick; interviewers love seeing you break down the problem.  
2. **Mention edge cases** – `n = 1`, duplicate rows/columns, all numbers the same, all distinct.  
3. **Discuss time/space trade‑offs** – “This is O(n²) which is optimal; O(n³) would timeout for n=200.”  
4. **Optional optimization** – Store columns first, then iterate rows (symmetrical).  
5. **Code readability** – Use meaningful variable names (`rowMap`, `colKey`, etc.).

---

## 📚 Further Reading

| Resource | Language | Key Takeaway |
|----------|----------|--------------|
| LeetCode Solution Discuss | Multiple | Community‑approved optimal solutions |
| “Cracking the Coding Interview” – Hash Tables | All | Fundamentals of hashing |
| “Algorithms, 4th Edition” – Section on Hash Tables | All | Theoretical background |

---

## 📝 SEO‑Friendly Blog Structure

```markdown
# LeetCode 2352 – Equal Row and Column Pairs
## Java, Python, C++ Solutions & Interview Guide

### Meta Description
"Learn the fastest O(n²) solution for LeetCode 2352 – Equal Row and Column Pairs. Find Java, Python, and C++ code, plus interview strategies."

### Keywords
LeetCode 2352, Equal Row and Column Pairs, algorithm interview, hash map solution, Java coding interview, Python interview, C++ coding interview

### Outline
1. Problem Recap
2. Intuition & Solution Outline
3. Code (Java / Python / C++)
4. Complexity Analysis
5. Good/Bad/Ugly Patterns
6. Interview Tips
7. Further Reading
```

> The article above is ready to be published on Medium, Dev.to, or your personal blog.  
> Just copy the Markdown, adjust the meta description, and you’re good to go!  

---

### 🎉 Wrap‑Up

- **Optimal solution:** `O(n²)` with a hash map.  
- **All three languages** demonstrate the same idea; choose the one you’re most comfortable with.  
- **Interview‑ready explanation** + clean code = your ticket to the next round.  

Happy coding, and best of luck on your next technical interview! 🚀