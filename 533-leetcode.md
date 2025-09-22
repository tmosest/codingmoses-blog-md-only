---
title: LeetCode 533. Lonely Pixel II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# **LeetCode 533 – Lonely Pixel II**  
**A Complete Guide – Java, Python, C++ & SEO‑Optimised Blog Post**

> *“In coding interviews, the devil is in the details. Mastering problem 533 will let you showcase your algorithmic thinking, your ability to handle edge cases, and your love for clean code.”*

---

## Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Key Observations & Edge‑Case Checklist](#key-observations)  
3. [Optimal Approach (O(m × n))](#optimal-approach)  
4. [Complexity Analysis](#complexity)  
5. [Full Implementations](#implementations)  
   * Java  
   * Python  
   * C++  
6. [Good, Bad & Ugly: Design Trade‑offs](#good-bad-ugly)  
7. [Interview‑Ready Tips](#interview-tips)  
8. [SEO Checklist](#seo-checklist)  

---

## Problem Overview <a name="problem-overview"></a>

> **Lonely Pixel II**  
> Given an `m × n` matrix `picture` of characters `'B'` (black) and `'W'` (white) and an integer `target`, return the number of *black lonely pixels*.

A pixel `picture[r][c] == 'B'` is **lonely** if:

1. Row `r` and column `c` each contain exactly `target` black pixels.  
2. All rows that contain a black pixel in column `c` are **identical** to row `r`.

---

## Key Observations & Edge‑Case Checklist <a name="key-observations"></a>

| Observation | Why it matters | Typical pitfall |
|-------------|----------------|-----------------|
| **Count per row** | Needed for rule 1 | Forgetting to store the count for every row leads to O(n) re‑scans |
| **Count per column** | Needed for rule 1 | Using a map for columns can be slower than an array |
| **Row pattern identity** | Needed for rule 2 | Comparing strings instead of patterns can give false positives if not handled carefully |
| **Target == 1** | Minimal edge case | Must still apply both rules |
| **`m, n <= 200`** | Small but still require O(m × n) | Avoid quadratic or higher solutions |

---

## Optimal Approach (O(m × n)) <a name="optimal-approach"></a>

1. **Pre‑process**  
   * Convert each row to a string `rowStr`.  
   * Compute `rowCount[i]` – number of `'B'` in row `i`.  
   * Compute `colCount[j]` – number of `'B'` in column `j`.

2. **Check each column**  
   For every column `j` with `colCount[j] == target`:
   * Gather all row indices `rowsWithB` where `picture[i][j] == 'B'`.
   * Ensure every such row has `rowCount[i] == target`.  
   * Ensure every row string in `rowsWithB` is identical to the first one.  
   * If all conditions hold, every black pixel in that column is lonely → add `target` to the answer.

3. **Return** the accumulated count.

Why it works  
*Rule 1* is satisfied by the `rowCount` and `colCount` checks.  
*Rule 2* is satisfied by comparing row strings for all rows that share the same column `j`.  
If any condition fails, none of the black pixels in that column can be lonely.

---

## Complexity Analysis <a name="complexity"></a>

| Operation | Time | Space |
|-----------|------|-------|
| Pre‑processing rows & columns | `O(m × n)` | `O(m + n)` (arrays + string list) |
| Scanning columns & verifying rows | `O(m × n)` | `O(1)` extra |
| **Total** | `O(m × n)` | `O(m + n)` |

Both constraints (`m, n ≤ 200`) are easily satisfied.

---

## Full Implementations <a name="implementations"></a>

> All solutions follow the same algorithm but differ in language syntax and idioms.  

### 1. Java

```java
import java.util.*;

public class Solution {
    public int findBlackPixel(char[][] picture, int target) {
        int m = picture.length, n = picture[0].length;
        int[] rowCount = new int[m];
        int[] colCount = new int[n];
        String[] rowStr = new String[m];

        // Pre‑process rows and columns
        for (int i = 0; i < m; i++) {
            StringBuilder sb = new StringBuilder();
            for (int j = 0; j < n; j++) {
                char ch = picture[i][j];
                sb.append(ch);
                if (ch == 'B') {
                    rowCount[i]++;
                    colCount[j]++;
                }
            }
            rowStr[i] = sb.toString();
        }

        int ans = 0;
        // Examine every column that has exactly target black pixels
        for (int j = 0; j < n; j++) {
            if (colCount[j] != target) continue;

            // Gather rows with a black pixel in column j
            List<Integer> rowsWithB = new ArrayList<>();
            for (int i = 0; i < m; i++) {
                if (picture[i][j] == 'B') rowsWithB.add(i);
            }

            boolean valid = true;
            String firstRow = rowStr[rowsWithB.get(0)];

            for (int idx : rowsWithB) {
                if (rowCount[idx] != target) {   // Rule 1 on the row
                    valid = false; break;
                }
                if (!rowStr[idx].equals(firstRow)) {   // Rule 2
                    valid = false; break;
                }
            }

            if (valid) ans += target;   // All target pixels in this column are lonely
        }
        return ans;
    }
}
```

### 2. Python

```python
from typing import List

class Solution:
    def findBlackPixel(self, picture: List[List[str]], target: int) -> int:
        m, n = len(picture), len(picture[0])

        row_count = [0] * m
        col_count = [0] * n
        row_str = [''] * m

        # Pre‑process
        for i in range(m):
            row = picture[i]
            s = []
            for j, ch in enumerate(row):
                s.append(ch)
                if ch == 'B':
                    row_count[i] += 1
                    col_count[j] += 1
            row_str[i] = ''.join(s)

        ans = 0
        # Scan columns
        for j in range(n):
            if col_count[j] != target:
                continue

            rows_with_b = [i for i in range(m) if picture[i][j] == 'B']
            first = row_str[rows_with_b[0]]
            valid = True
            for r in rows_with_b:
                if row_count[r] != target or row_str[r] != first:
                    valid = False
                    break
            if valid:
                ans += target
        return ans
```

### 3. C++

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    int findBlackPixel(vector<vector<char>>& picture, int target) {
        int m = picture.size(), n = picture[0].size();
        vector<int> rowCnt(m, 0), colCnt(n, 0);
        vector<string> rowStr(m);

        // Pre‑process
        for (int i = 0; i < m; ++i) {
            string s;
            s.reserve(n);
            for (int j = 0; j < n; ++j) {
                char ch = picture[i][j];
                s.push_back(ch);
                if (ch == 'B') {
                    ++rowCnt[i];
                    ++colCnt[j];
                }
            }
            rowStr[i] = s;
        }

        int ans = 0;
        // Evaluate each qualifying column
        for (int j = 0; j < n; ++j) {
            if (colCnt[j] != target) continue;

            vector<int> rowsWithB;
            for (int i = 0; i < m; ++i)
                if (picture[i][j] == 'B') rowsWithB.push_back(i);

            bool ok = true;
            const string& first = rowStr[rowsWithB[0]];
            for (int r : rowsWithB) {
                if (rowCnt[r] != target || rowStr[r] != first) {
                    ok = false; break;
                }
            }
            if (ok) ans += target;
        }
        return ans;
    }
};
```

---

## Good, Bad & Ugly: Design Trade‑offs <a name="good-bad-ugly"></a>

| Aspect | **Good** | **Bad** | **Ugly** |
|--------|----------|---------|----------|
| **Data representation** | Using a string per row keeps row comparison O(1). | Using a 2‑D array of chars for comparisons increases time to `O(n)` per check. | Using hash codes alone may produce collisions. |
| **Complexity** | `O(m × n)` is optimal for this problem size. | Attempting to double‑hash or pre‑sort rows unnecessarily adds constant factors. | Brute‑force nested loops (O(m² × n)) lead to TLE for the upper limits. |
| **Readability** | Clear separation: counting + verification. | Mixing loops with nested conditions can obscure intent. | Excessive inline comments or debug prints clutter the solution. |
| **Edge‑case handling** | Explicitly checks target on both row & column. | Forgetting to treat `target == 1` specially causes wrong results. | Not validating that all rows with B in a column are identical leads to false positives. |
| **Memory usage** | `O(m + n)` + row strings (`O(m × n)` but unavoidable). | Storing additional maps for columns or rows wastes space. | Storing a `Map<String, Set<Integer>>` for rows by pattern consumes `O(m²)` in worst case. |

**Takeaway:**  
Stick to the simplest, most direct approach: pre‑count, then iterate columns. Avoid over‑engineering.

---

## Interview‑Ready Tips <a name="interview-tips"></a>

1. **Explain the rules first.**  
   *“Rule 1 checks the counts; Rule 2 ensures row consistency.”*  

2. **Sketch the algorithm on paper.**  
   *Show a 3‑step flow: counting → column selection → row‑pattern verification.*

3. **Mention time‑space trade‑offs.**  
   *“We only need O(m + n) extra space beyond the input; the algorithm is linear.”*

4. **Discuss edge cases** – `target = 1`, all‑white picture, all‑black picture.

5. **Ask clarifying questions** – e.g., “Are the rows guaranteed to be unique? How should we treat duplicate rows?”  
   *It shows you’re thinking about potential pitfalls.*

6. **Highlight code readability** – good variable names, helper functions, and concise comments.

---

## SEO Checklist <a name="seo-checklist"></a>

| Target Keyword | Implementation |
|----------------|----------------|
| **LeetCode 533** | Problem title used in header and intro |
| **Lonely Pixel II** | Mentioned in each language section |
| **Java solution** | Java code block & heading |
| **Python solution** | Python code block & heading |
| **C++ solution** | C++ code block & heading |
| **Interview prep** | Section “Interview‑Ready Tips” |
| **Algorithm** | Complexity analysis |
| **Coding interview** | Intro & conclusion |
| **Coding interview problem** | Problem overview |
| **Good bad ugly** | Section title |
| **Time complexity** | Complexity table |

Adding relevant meta tags and internal links to your other interview blogs will further boost search visibility.

---

### Closing Thought

Solving **Lonely Pixel II** demonstrates your mastery of 2‑D array manipulation, pattern recognition, and efficient counting. Presenting the solution in **Java, Python, and C++** showcases language versatility—a valuable skill for any software‑engineering role.

Happy coding and good luck with your next interview! 🚀