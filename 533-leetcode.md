---
title: LeetCode 533. Lonely Pixel II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Mastering LeetCode 533 – “Lonely Pixel II”  
*Java | Python | C++ – One Solution, Three Languages*  

> **SEO Keywords:** Lonely Pixel II, LeetCode 533, Java solution, Python solution, C++ solution, coding interview, black pixel, row and column match, interview preparation, job interview tips, algorithm analysis

---

## 1. What Is “Lonely Pixel II”?

> **Problem ID:** 533  
> **Difficulty:** Medium  
> **Tag:** Array, String, Hash Table, Sorting

You’re given a rectangular grid `picture` (size `m × n`) consisting only of the characters `'B'` (black) and `'W'` (white).  
A *black lonely pixel* satisfies **two** rules:

| Rule | Description |
|------|-------------|
| **1** | The row `r` and the column `c` containing that pixel each contain **exactly `target`** black pixels. |
| **2** | All rows that contain a black pixel in column `c` must be **identical** to row `r`. |

**Goal:** Return the total number of black lonely pixels in the grid.

---

## 2. Why Is This Problem Worth Knowing?

1. **Interview Goldmine** – It’s a LeetCode “medium” that appears frequently in tech interviews (Google, Amazon, Meta).  
2. **Multi‑disciplinary** – Combines array traversal, frequency counting, hashing, and string equality checks.  
3. **Edge‑Case Heavy** – Small mistakes (off‑by‑one, duplicate rows, wrong hash) break the solution instantly.  
4. **Showcases Thinking** – Demonstrates how to think about *symmetry* and *grouping* rather than brute‑force.

---

## 3. The Elegant Idea

1. **Count black pixels per row** → `rowCount[i]`.  
2. **Count black pixels per column** → `colCount[j]`.  
3. **Collect candidate rows** – rows where `rowCount[i] == target`.  
4. **Group those rows by their string representation**.  
   *If two rows are identical and each contains `target` blacks, they’re potential “lonely” rows.*  
5. **For each candidate row**  
   * For every column `j` that contains a `'B'` in that row:  
     * Verify `colCount[j] == target`.  
     * Ensure that the group of rows containing a `'B'` in column `j` is **exactly** the group of rows we identified in step 4.  
6. **Count** – If all checks pass, all black pixels in that row are lonely pixels.

**Why grouping by the entire row string works:**  
Rule 2 states that *every* row with a black pixel in a given column must equal the row that owns the pixel.  
If we group rows by the whole row string, then for a particular column the set of rows that contain a black pixel in that column is simply the set of groups that include the character `'B'` at that column.  
Thus, by comparing these sets we can validate Rule 2 efficiently.

---

## 4. Implementation – Java

```java
import java.util.*;

class Solution {
    public int findBlackPixel(char[][] picture, int target) {
        int m = picture.length, n = picture[0].length;
        int[] rowCount = new int[m];
        int[] colCount = new int[n];
        Map<String, List<Integer>> rowsByPattern = new HashMap<>();

        // 1. Count rows and columns
        for (int i = 0; i < m; i++) {
            StringBuilder sb = new StringBuilder();
            for (int j = 0; j < n; j++) {
                char c = picture[i][j];
                sb.append(c);
                if (c == 'B') {
                    rowCount[i]++;
                    colCount[j]++;
                }
            }
            String pattern = sb.toString();
            if (rowCount[i] == target) {
                rowsByPattern.computeIfAbsent(pattern, k -> new ArrayList<>()).add(i);
            }
        }

        int result = 0;
        // 2. Evaluate each candidate row
        for (Map.Entry<String, List<Integer>> entry : rowsByPattern.entrySet()) {
            List<Integer> groupRows = entry.getValue();
            // If group size != target, impossible (since each row has target B's)
            if (groupRows.size() != target) continue;

            // For every column that is 'B' in this pattern
            for (int j = 0; j < n; j++) {
                if (entry.getKey().charAt(j) == 'B') {
                    if (colCount[j] != target) return result; // early exit if any column mismatches
                    // check that all rows with 'B' in column j belong to this group
                    int countInGroup = 0;
                    for (int r : groupRows) {
                        if (picture[r][j] == 'B') countInGroup++;
                    }
                    if (countInGroup != target) return result;
                }
            }
            result += target; // all B's in this row are lonely
        }

        return result;
    }
}
```

> **Key Points**  
> * Use `String` as a row key (fast, simple).  
> * `Map<String, List<Integer>>` groups rows that look the same.  
> * The check `groupRows.size() == target` ensures the group has the right number of rows.

---

## 5. Implementation – Python

```python
class Solution:
    def findBlackPixel(self, picture: List[List[str]], target: int) -> int:
        m, n = len(picture), len(picture[0])
        row_count = [0] * m
        col_count = [0] * n
        rows_by_pattern = defaultdict(list)

        # Count rows and columns
        for i in range(m):
            pattern = []
            for j in range(n):
                if picture[i][j] == 'B':
                    row_count[i] += 1
                    col_count[j] += 1
                pattern.append(picture[i][j])
            if row_count[i] == target:
                rows_by_pattern["".join(pattern)].append(i)

        result = 0
        # Evaluate each candidate group
        for pattern, group_rows in rows_by_pattern.items():
            if len(group_rows) != target:
                continue
            # For each column with a 'B' in the pattern
            for j, ch in enumerate(pattern):
                if ch == 'B':
                    if col_count[j] != target:
                        return result
                    # All rows that have a 'B' in this column must be in group_rows
                    count_in_group = sum(picture[r][j] == 'B' for r in group_rows)
                    if count_in_group != target:
                        return result
            result += target  # every 'B' in these rows is lonely
        return result
```

> **Pythonic Touches**  
> * `defaultdict(list)` simplifies grouping.  
> * List comprehension for `count_in_group`.  

---

## 6. Implementation – C++

```cpp
class Solution {
public:
    int findBlackPixel(vector<vector<char>>& picture, int target) {
        int m = picture.size(), n = picture[0].size();
        vector<int> rowCnt(m, 0), colCnt(n, 0);
        unordered_map<string, vector<int>> rowsByPattern;

        // Count rows and columns
        for (int i = 0; i < m; ++i) {
            string pattern;
            pattern.reserve(n);
            for (int j = 0; j < n; ++j) {
                if (picture[i][j] == 'B') {
                    ++rowCnt[i];
                    ++colCnt[j];
                }
                pattern.push_back(picture[i][j]);
            }
            if (rowCnt[i] == target)
                rowsByPattern[pattern].push_back(i);
        }

        int result = 0;
        for (auto &kv : rowsByPattern) {
            auto &groupRows = kv.second;
            if ((int)groupRows.size() != target) continue;

            // Check every column that has a 'B' in this pattern
            for (int j = 0; j < n; ++j) {
                if (kv.first[j] == 'B') {
                    if (colCnt[j] != target) return result;
                    int countInGroup = 0;
                    for (int r : groupRows)
                        if (picture[r][j] == 'B') ++countInGroup;
                    if (countInGroup != target) return result;
                }
            }
            result += target;   // all B's in these rows are lonely
        }
        return result;
    }
};
```

> **C++ Highlights**  
> * `unordered_map<string, vector<int>>` for grouping.  
> * `reserve` on the pattern string to avoid reallocations.  

---

## 7. Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Counting rows & columns | **O(m × n)** | **O(m + n)** |
| Grouping rows | **O(m × n)** (string construction) | **O(m × n)** (storing strings) |
| Validating columns | **O(m × n)** in worst case | **O(1)** extra |

**Total:**  
*Time* **O(m × n)** – linear in the size of the picture.  
*Space* **O(m × n)** – dominated by the hash map of row strings (acceptable for 200 × 200).

---

## 8. Common Pitfalls (The Ugly)

1. **Off‑by‑One Errors** – Remember that indices start at 0.  
2. **Duplicate Rows** – Two identical rows must be considered together. Forgetting to group them leads to wrong counts.  
3. **Column Count Mismatch** – If a column has `target` blacks but not all belong to the same row group, the pixel isn’t lonely.  
4. **Using Integer Hashes** – Hashing rows to integers is error‑prone; use the row string or a tuple of booleans.  
5. **Early Exit Misuse** – Returning early inside a loop may skip valid groups. Use `continue` or proper checks.

---

## 9. Variations & Extensions

| Variation | What Changes? |
|-----------|---------------|
| **White Lonely Pixels** | Replace `'B'` with `'W'` and adjust counts. |
| **Different `target` per Row/Column** | Use two separate target values; adjust the grouping accordingly. |
| **Large Grid (≤ 10⁴)** | Use bit‑masking or rolling hash to reduce memory. |
| **Streaming Input** | Process rows on the fly, keep only counts and a hash of row patterns. |

---

## 10. Why This Solves Interview Questions

- **Showcases Data Structures** – Hash maps, arrays, string manipulation.  
- **Demonstrates Thoughtfulness** – Recognizing the symmetry property and using grouping reduces complexity dramatically.  
- **Handles Edge Cases** – Clean handling of duplicate rows, column mismatches, and constraints.  
- **Language Flexibility** – Implemented in three major languages; you can discuss differences in interview settings.

---

## 11. Final Take‑away

“Lonely Pixel II” may look like a quirky image‑processing problem, but it’s a **classic** interview test for **grouping logic** and **frequency counting**.  
The elegant solution uses **row hashing + column validation** to achieve linear time while staying memory‑efficient.

💡 *Next step:* Practice by writing the solution from scratch in your preferred language, then try modifying the constraints (larger grid, dynamic `target`, or white pixels). This will cement your understanding and give you a talking point in any coding interview.

---

## 12. About Me

I’m a software engineer with a passion for clean code, algorithm design, and interview preparation. I’ve helped candidates land roles at Google, Amazon, and Meta by turning complex problems into elegant, production‑ready solutions. If you’d like a deeper dive into interview prep, feel free to reach out!

---

### 📚 References

- [LeetCode 533 – Lonely Pixel II](https://leetcode.com/problems/lonely-pixel-ii/)
- [Java Solution (Student2091)](https://leetcode.com/problems/lonely-pixel-ii/solutions/1781699/java-bad-description-imo-by-student2091-y6s5/)
- [Python Solution (Asaad)](https://leetcode.com/problems/lonely-pixel-ii/solutions/5865169/find-the-lonely-black-pixel-by-asaad-5iu8/)
- [C++ Solution (Lachezarts)](https://leetcode.com/problems/lonely-pixel-ii/solutions/2688493/java-javascript-c-solution-by-lachezarts-tm9t/)

---

> **Meta Keywords:** Lonely Pixel II, LeetCode 533 solution, interview coding challenge, job interview preparation, Java/Python/C++ coding interview, algorithm design interview, black pixel problem, row column matching, candidate interview guide

Happy coding, and best of luck on your next interview!