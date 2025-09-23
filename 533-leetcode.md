---
title: LeetCode 533. Lonely Pixel II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 533. Lonely Pixel II – Multi‑Language Solutions & SEO‑Optimised Blog Post

---

## Table of Contents  

| Section | Language | Code |
|--------|----------|------|
| **Problem Overview** | – | – |
| **Algorithm & Complexity** | – | – |
| **Java Implementation** | Java | ![Java Code] |
| **Python Implementation** | Python | ![Python Code] |
| **C++ Implementation** | C++ | ![C++ Code] |
| **Blog Post** | – | – |

> **Goal:** Solve LeetCode “Lonely Pixel II” (medium difficulty) in Java, Python and C++, then write a complete, SEO‑friendly blog article that can help you land a tech interview.

---

## 1. Problem Overview

**LeetCode #533 – Lonely Pixel II**

You are given an `m × n` picture of characters `'B'` (black) and `'W'` (white) and an integer `target`.  
A **black lonely pixel** is a `'B'` at position `(r, c)` that satisfies:

1. The row `r` and the column `c` each contain **exactly `target`** black pixels.
2. All rows that contain a black pixel in column `c` are **identical** to row `r`.

Return the number of black lonely pixels.

**Constraints**

| | |
|---|---|
| `1 ≤ m, n ≤ 200` | `picture[i][j] ∈ {'W','B'}` |
| `1 ≤ target ≤ min(m,n)` |  

---

## 2. Algorithm & Complexity

### 2.1 Observation

*If a column `c` contains exactly `target` black pixels and all those rows have identical patterns, every black pixel in that column is a lonely pixel.*

So we can solve the problem in three phases:

1. **Row & Column Counting**  
   * `rowCount[i]` – number of black pixels in row `i`.  
   * `colCount[j]` – number of black pixels in column `j`.  
   * `rowStr[i]` – string representation of row `i` (e.g., `"WBBWW"`).  

2. **Build column → rows map**  
   For each column `j` store the list of row indices that contain a black pixel in that column.

3. **Validate columns**  
   For each column `j` with `colCount[j] == target`:  
   * Let `rows = columnRows[j]`.  
   * All rows in `rows` must have the same `rowStr`.  
   * If they do, add `target` (i.e., `rows.size()`) to the answer.

### 2.2 Correctness Proof

We prove that the algorithm counts every and only valid black lonely pixels.

*Lemma 1*: If a column `c` has exactly `target` black pixels and all corresponding rows have the same pattern, then every black pixel in column `c` satisfies both rules of a lonely pixel.

*Proof*:  
- By construction, `colCount[c] = target`.  
- Each of those rows has exactly `target` black pixels (`rowCount = target`).  
- All those rows are identical, so rule 2 holds.  
Thus each `'B'` in column `c` is a lonely pixel. ∎

*Lemma 2*: If a column `c` fails any of the above two conditions, no black pixel in that column can be a lonely pixel.

*Proof*:  
- If `colCount[c] ≠ target`, rule 1 fails for any pixel in that column.  
- If some two rows that contain a black pixel in column `c` differ, rule 2 fails for those pixels. ∎

*Theorem*: The algorithm outputs the exact number of black lonely pixels.

*Proof*:  
- By Lemma 1, whenever the algorithm adds `target` to the answer, all those pixels are lonely pixels.  
- By Lemma 2, the algorithm never counts a pixel that is not lonely.  
Hence the answer equals the number of valid lonely pixels. ∎

### 2.3 Complexity Analysis

| Step | Work | Reason |
|------|------|--------|
| Counting rows/columns | `O(mn)` | Single scan |
| Building column → rows map | `O(mn)` | Add each black pixel once |
| Validating columns | `O(mn)` | Each black pixel examined once in its column |
| **Total** | `O(mn)` | ≤ 40 000 operations for `m, n ≤ 200` |
| **Space** | `O(mn)` | For `rowStr`, column lists |

---

## 3. Java Implementation

```java
import java.util.*;

public class Solution {
    public int findBlackPixel(char[][] picture, int target) {
        int m = picture.length, n = picture[0].length;
        int[] rowCount = new int[m];
        int[] colCount = new int[n];
        String[] rowStr = new String[m];

        // 1️⃣ Build row strings and counts
        for (int i = 0; i < m; i++) {
            StringBuilder sb = new StringBuilder();
            for (int j = 0; j < n; j++) {
                if (picture[i][j] == 'B') rowCount[i]++;
                sb.append(picture[i][j]);
            }
            rowStr[i] = sb.toString();
        }

        // 2️⃣ Build column counts and column → rows map
        Map<Integer, List<Integer>> colRows = new HashMap<>();
        for (int j = 0; j < n; j++) colRows.put(j, new ArrayList<>());
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (picture[i][j] == 'B') {
                    colCount[j]++;
                    colRows.get(j).add(i);
                }
            }
        }

        // 3️⃣ Validate columns
        int ans = 0;
        for (int j = 0; j < n; j++) {
            if (colCount[j] != target) continue;          // rule 1 fails
            List<Integer> rows = colRows.get(j);
            String pattern = rowStr[rows.get(0)];
            boolean same = true;
            for (int r : rows) {
                if (!rowStr[r].equals(pattern) || rowCount[r] != target) {
                    same = false;
                    break;
                }
            }
            if (same) ans += target;   // all rows in this column are valid
        }
        return ans;
    }
}
```

---

## 4. Python Implementation

```python
from typing import List

class Solution:
    def findBlackPixel(self, picture: List[List[str]], target: int) -> int:
        m, n = len(picture), len(picture[0])

        # Row strings and counts
        row_str = [''.join(row) for row in picture]
        row_cnt = [row.count('B') for row in picture]

        # Column counts & column → rows map
        col_cnt = [0] * n
        col_rows = [[] for _ in range(n)]
        for i in range(m):
            for j in range(n):
                if picture[i][j] == 'B':
                    col_cnt[j] += 1
                    col_rows[j].append(i)

        ans = 0
        for j in range(n):
            if col_cnt[j] != target:
                continue
            rows = col_rows[j]
            pattern = row_str[rows[0]]
            if all(row_str[r] == pattern and row_cnt[r] == target for r in rows):
                ans += target
        return ans
```

---

## 5. C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findBlackPixel(vector<vector<char>>& picture, int target) {
        int m = picture.size(), n = picture[0].size();
        vector<int> rowCnt(m), colCnt(n);
        vector<string> rowStr(m);

        // Build row strings and counts
        for (int i = 0; i < m; ++i) {
            string s;
            s.reserve(n);
            for (int j = 0; j < n; ++j) {
                if (picture[i][j] == 'B') ++rowCnt[i];
                s += picture[i][j];
            }
            rowStr[i] = s;
        }

        // Column counts and mapping column → rows
        vector<vector<int>> colRows(n);
        for (int i = 0; i < m; ++i)
            for (int j = 0; j < n; ++j)
                if (picture[i][j] == 'B') {
                    ++colCnt[j];
                    colRows[j].push_back(i);
                }

        int ans = 0;
        for (int j = 0; j < n; ++j) {
            if (colCnt[j] != target) continue;
            const auto& rows = colRows[j];
            const string& pattern = rowStr[rows[0]];
            bool ok = true;
            for (int r : rows) {
                if (rowStr[r] != pattern || rowCnt[r] != target) {
                    ok = false;
                    break;
                }
            }
            if (ok) ans += target;
        }
        return ans;
    }
};
```

---

## 6. SEO‑Optimised Blog Post

> **Title:** *LeetCode 533 – Lonely Pixel II: A Clean Java, Python & C++ Solution for Your Next Interview*  

> **Meta Description:**  
> Master LeetCode “Lonely Pixel II” (533) with clear, interview‑ready solutions in Java, Python, and C++. Understand the algorithm, prove correctness, and get job‑search‑friendly SEO tips.

---

### 6.1 Introduction

If you’re preparing for a software‑engineering interview, you’ll encounter problems that test not only your coding skills but also your ability to **design clean, efficient algorithms**.  
LeetCode 533, “Lonely Pixel II”, is a classic medium‑level problem that covers:

- Two‑dimensional array manipulation  
- Hash‑map grouping  
- Pattern matching  

In this post we’ll:

1. Break down the problem in plain English.  
2. Present a clear algorithm with a correctness proof.  
3. Offer full, production‑ready solutions in **Java, Python, and C++**.  
4. Give SEO‑friendly tips to make your article show up in interview‑related searches.  

---

### 6.2 Problem Summary

You’re given an `m × n` grid of `'B'` and `'W'`.  
A black pixel `(r, c)` is “lonely” if:

1. Row `r` contains exactly `target` black pixels.  
2. Column `c` contains exactly `target` black pixels.  
3. Every row that has a black pixel in column `c` is identical to row `r`.

Return the number of such lonely pixels.

---

### 6.3 Good – Why This Approach Works

| ✅ | Explanation |
|---|---|
| **O(mn) time** | One pass for counting, one for grouping, and one for validation. |
| **O(mn) space** | Storing row strings and column‑to‑rows map is linear. |
| **Clear logic** | Row/column counts + pattern equality is intuitive. |
| **Easy to reason** | Correctness proof boils down to two simple lemmas. |

---

### 6.4 Bad – What to Avoid

| ❌ | Pitfall |
|---|---------|
| Counting columns twice | Leads to `O(mn)` but with hidden constants. |
| Using nested loops for every column & row | Increases to `O(m²n)` – too slow for 200×200. |
| Forgetting to check `rowCount[r] == target` | Gives false positives when row pattern matches but row has more black pixels. |

---

### 6.5 Ugly – Edge‑Case Quirks

| ⚠️ | Edge Case |
|---|-----------|
| **Multiple identical rows** | They must be counted separately – each contributes a lonely pixel. |
| **Large `target`** | If `target > m` or `> n`, answer is trivially `0`. Our algorithm handles it naturally. |
| **Empty columns** | A column with zero `'B'` must not be considered. |

---

### 6.6 Interview‑Ready Code Snippets

Below are three **copy‑paste ready** solutions.  

- **Java** – uses `StringBuilder`, `HashMap`  
- **Python** – list comprehensions, generator expressions  
- **C++** – STL vectors, string concatenation  

Each solution follows the same structure:

```text
1️⃣ Build row strings & counts
2️⃣ Build column counts & column→rows map
3️⃣ For every column with colCount == target:
     • Ensure all rows in that column are identical
     • Ensure each of those rows also has rowCount == target
     • Add target to answer
```

> **Tip for recruiters**: When you’re asked to write a solution, comment each section with a brief “what‑this‑does” line – it shows you care about maintainability.

---

### 6.6 How to Make This Post Rank (SEO Tips)

1. **Use Targeted Keywords**  
   - *Lonely Pixel II*  
   - *LeetCode 533*  
   - *Java interview problem*  
   - *Python coding interview*  

2. **Add Structured Data**  
   ```html
   <script type="application/ld+json">
   {
     "@context": "https://schema.org",
     "@type": "TechArticle",
     "headline": "LeetCode 533 – Lonely Pixel II",
     "articleBody": "...",
     "keywords": "Lonely Pixel II, LeetCode 533, interview algorithm"
   }
   </script>
   ```

3. **Anchor Links**  
   ```markdown
   [Problem Summary](#problem-summary) | [Algorithm](#algorithm) | [Java Solution](#java-implementation) | [Python Solution](#python-implementation) | [C++ Solution](#c-implementation)
   ```

4. **Images & Diagrams**  
   - Visualize a grid and highlight a lonely pixel.  
   - Show a quick flowchart of the algorithm.  

5. **Readable Code Blocks**  
   - Use syntax‑highlighted fences (```java, ```python, ```cpp).  
   - Keep indentation consistent; it reduces rendering errors on platforms like Medium or Dev.to.

6. **Call‑to‑Action**  
   End with “Try writing your own test harness” and “Comment your own solutions.”  
   This encourages engagement, boosting your post’s ranking.

---

### 6.6 Final Words

- **Practice**: Run the code on random grids to convince yourself of its robustness.  
- **Explain**: In an interview, be ready to narrate the *why* behind each line.  
- **Show confidence**: If you can present a correctness proof, interviewers will be impressed.  

Good luck cracking *Lonely Pixel II* and landing that next job!  

---

### 6.7 References

- LeetCode Problem 533 – *Lonely Pixel II*.  
- Standard algorithms for 2‑D array grouping.  
- Interview Stack Overflow threads discussing grid‑based problems.  

---

#### Happy coding, and remember: *a clean solution is a great first impression!*  

--- 

> **Word Count:** ~1100  
> **Target Audience:** CS interviewees, LeetCode enthusiasts, recruiters.  
> **Primary Keywords:** *LeetCode 533*, *Lonely Pixel II*, *Java solution*, *Python solution*, *C++ solution*, *interview algorithm*, *software engineering interview*.  

--- 

This completes the **full answer**: we’ve covered the algorithm, correctness proof, time‑space complexity, three production‑ready code samples, and a polished SEO‑friendly blog post.  

Feel free to adapt the code to your preferred coding style or framework. Happy interviewing!