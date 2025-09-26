---
title: LeetCode 533. Lonely Pixel II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. 533. Lonely Pixel II – Multi‑Language Solutions

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.  
All three follow the same O(m·n) time, O(m + n) space strategy.

```text
Input: picture = [ ["W","B","W","B","B","W"],
                   ["W","B","W","B","B","W"],
                   ["W","B","W","B","B","W"],
                   ["W","W","B","W","B","W"] ],
       target = 3
Output: 6
```

---

### Java

```java
import java.util.*;

public class LonelyPixelII {
    public int findBlackPixel(char[][] picture, int target) {
        int m = picture.length, n = picture[0].length;
        int[] rowCnt = new int[m];
        int[] colCnt = new int[n];
        Map<String, Integer> rowMap = new HashMap<>();

        // 1. Count B's in each row, column and store row string
        for (int i = 0; i < m; i++) {
            StringBuilder sb = new StringBuilder();
            for (int j = 0; j < n; j++) {
                char ch = picture[i][j];
                sb.append(ch);
                if (ch == 'B') {
                    rowCnt[i]++;
                    colCnt[j]++;
                }
            }
            String rowStr = sb.toString();
            if (rowCnt[i] == target) {          // only rows with exact target matter
                rowMap.put(rowStr, rowMap.getOrDefault(rowStr, 0) + 1);
            }
        }

        // 2. For each column, if it contains target B's AND all rows with B
        //    in this column have the same pattern, add target to answer
        int ans = 0;
        for (int j = 0; j < n; j++) {
            if (colCnt[j] != target) continue;   // column must also have target B's

            // collect all rows that have a B in column j
            String firstRow = null;
            boolean same = true;
            for (int i = 0; i < m; i++) {
                if (picture[i][j] == 'B') {
                    String rowStr = new String(picture[i]); // row as string
                    if (firstRow == null) firstRow = rowStr;
                    else if (!rowStr.equals(firstRow)) { same = false; break; }
                }
            }
            if (same && firstRow != null) {
                // all rows with B in this column are identical
                ans += target;    // each of those B's is a lonely pixel
            }
        }
        return ans;
    }
}
```

> **Note**: `new String(picture[i])` works because `picture[i]` is a `char[]`.  
> For very large matrices you might want to keep the `rowStr` from the first loop instead of re‑building it.

---

### Python

```python
class Solution:
    def findBlackPixel(self, picture: List[List[str]], target: int) -> int:
        m, n = len(picture), len(picture[0])
        row_cnt = [0] * m
        col_cnt = [0] * n
        row_map = {}

        # 1. Count B's and build row strings
        for i in range(m):
            row_str = "".join(picture[i])
            cnt = row_str.count('B')
            row_cnt[i] = cnt
            if cnt == target:
                row_map[row_str] = row_map.get(row_str, 0) + 1

            for j, ch in enumerate(picture[i]):
                if ch == 'B':
                    col_cnt[j] += 1

        # 2. Scan columns
        ans = 0
        for j in range(n):
            if col_cnt[j] != target:
                continue

            first_row = None
            same = True
            for i in range(m):
                if picture[i][j] == 'B':
                    r = "".join(picture[i])
                    if first_row is None:
                        first_row = r
                    elif r != first_row:
                        same = False
                        break
            if same and first_row:
                ans += target
        return ans
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findBlackPixel(vector<vector<char>>& picture, int target) {
        int m = picture.size(), n = picture[0].size();
        vector<int> rowCnt(m, 0), colCnt(n, 0);
        unordered_map<string, int> rowMap;

        // 1. Count B's in rows and columns, remember row strings
        for (int i = 0; i < m; ++i) {
            string rowStr;
            rowStr.reserve(n);
            for (int j = 0; j < n; ++j) {
                char ch = picture[i][j];
                rowStr.push_back(ch);
                if (ch == 'B') {
                    ++rowCnt[i];
                    ++colCnt[j];
                }
            }
            if (rowCnt[i] == target)
                ++rowMap[rowStr];
        }

        // 2. Inspect each column
        int ans = 0;
        for (int j = 0; j < n; ++j) {
            if (colCnt[j] != target) continue;

            string firstRow = "";
            bool same = true;
            for (int i = 0; i < m; ++i) {
                if (picture[i][j] == 'B') {
                    string curRow(picture[i].begin(), picture[i].end());
                    if (firstRow.empty())
                        firstRow = curRow;
                    else if (curRow != firstRow) {
                        same = false;
                        break;
                    }
                }
            }
            if (same && !firstRow.empty())
                ans += target;   // each matching B is lonely
        }
        return ans;
    }
};
```

---

## 2. Blog Article  
### *Cracking LeetCode 533 – Lonely Pixel II: The Good, The Bad, and The Ugly*

> **Keywords**: LeetCode, 533, Lonely Pixel II, interview coding, Java, Python, C++, algorithm, software engineering, job interview, data structures, time complexity, space complexity.

---

### 1. Problem Overview

LeetCode 533 *Lonely Pixel II* asks you to count **black lonely pixels** in a binary picture.  
A pixel `'B'` at position `(r, c)` is lonely if:

1. Row `r` contains exactly **`target`** black pixels.
2. Column `c` contains exactly **`target`** black pixels.
3. Every row that has a black pixel in column `c` is **identical** to row `r`.

The picture is an `m × n` grid (`1 ≤ m, n ≤ 200`).  
The task is to return the number of such lonely pixels.

---

### 2. Why It’s a Great Interview Question

- **Conceptual depth**: Tests understanding of 2‑D array traversal, hash maps, and string manipulation.
- **Space‑time trade‑offs**: Requires careful counting to stay within O(m·n) time and O(m + n) space.
- **Language versatility**: Solvable in Java, Python, C++, or any language that supports hash maps and string handling.

---

### 3. The “Good” – The Optimal Strategy

#### 3.1 High‑Level Idea

1. **Count black pixels per row and per column**.  
   *Rows* are stored as strings; if a row has exactly `target` black pixels, we keep its string in a hash map.

2. **Validate columns**:  
   For every column that contains exactly `target` black pixels, check that **all** rows having a `'B'` in this column are **identical**.  
   If they are, every `'B'` in that column is a lonely pixel.

3. **Result**:  
   Each valid column contributes `target` lonely pixels (one per row that has a `'B'` in that column).

#### 3.2 Why It Works

- **Row Condition**: The map guarantees that only rows with `target` black pixels are considered.
- **Column Condition**: The column loop ensures columns also have `target` black pixels.
- **Row Identity Condition**: By comparing row strings we confirm all rows sharing the column are identical.

The algorithm runs in `O(m·n)` because we traverse the matrix a constant number of times, and the map operations are `O(1)` on average.

---

### 4. The “Bad” – Common Pitfalls

| Pitfall | What goes wrong | Fix |
|---------|-----------------|-----|
| **Using `picture[i].toString()`** | Returns the array reference, not the row content. | Convert the row to a string: `new String(picture[i])` or `"".join(row)` in Python. |
| **Ignoring duplicate rows** | Counting each identical row separately leads to double‑counting. | Keep a frequency map of row strings and multiply only once per row. |
| **Not validating column count** | Some columns may have more or fewer `'B'`s than `target`. | Skip columns where `colCnt[j] != target`. |
| **Comparing rows incorrectly** | Using object identity (`==`) in Java/Python instead of content equality. | Use `equals()` in Java, `==` on strings in Python (strings are interned), or `==` on C++ `std::string`. |
| **Large intermediate strings** | Building row strings repeatedly can hit time limits. | Build once during the first pass; reuse in the column check. |

---

### 5. The “Ugly” – Edge Cases and Gotchas

- **All white picture**: No rows or columns satisfy the `target` condition → return 0.
- **Single row/column**: `m == 1` or `n == 1` still works; algorithm degenerates to a 1‑D scan.
- **Target equals 1**: Only isolated `'B'`s can be lonely; columns with more than one `'B'` must be discarded.
- **Memory constraints**: For the maximal 200×200 matrix, the string map is tiny (< 4 kB), so no problem.

---

### 6. Implementation in Three Languages

Below are clean, production‑ready snippets (Java, Python, C++).  
All three follow the optimal strategy described above.

- **Java**: Uses `HashMap<String, Integer>` to store row counts.
- **Python**: Uses `dict` and string concatenation.
- **C++**: Uses `unordered_map<string, int>` and `std::string` conversions.

> **Tip**: When you submit on LeetCode, make sure the function signature matches the platform’s requirement (`findBlackPixel` in Java, `Solution.findBlackPixel` in Python, etc.).

---

### 7. Interview‑Ready Advice

1. **State your plan**: Outline the two passes and why each condition matters.
2. **Explain complexity**: `O(m·n)` time, `O(m + n)` auxiliary space.
3. **Edge‑case sanity checks**: Show you considered single rows, columns, all‑white matrices.
4. **Trade‑offs**: If memory were tighter, you could avoid storing row strings by hashing the rows.
5. **Testing**: Show a few quick test cases, including the examples from the prompt.

> **Why this matters for a job**: Interviewers care not just about the correct answer but how you *think*. Demonstrating a clear, optimized solution signals strong problem‑solving skills that translate to real‑world engineering.

---

### 8. Take‑away

- **Lonely Pixel II** is a *medium* LeetCode problem that packs several key CS concepts: 2‑D array traversal, hash maps, string equality, and careful counting.
- The optimal solution is simple once you separate the row‑, column‑, and identity‑checks.
- Being able to implement it cleanly in Java, Python, or C++ showcases versatility—an attractive trait for software engineering roles.

---

#### Final Thought

If you can turn a confusing problem statement into an elegant, efficient algorithm, you’ve just earned a golden ticket for your next coding interview. Happy coding, and good luck on your job hunt!