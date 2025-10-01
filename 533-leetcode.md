---
title: LeetCode 533. Lonely Pixel II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Code

Below are clean, production‑ready solutions for **LeetCode 533 – Lonely Pixel II** in **Java**, **Python**, and **C++**.  
Each implementation follows the same three‑step strategy:

1. **Count** the number of black pixels per row and per column.  
2. **Group** identical rows that contain exactly `target` black pixels.  
3. **Check** every black pixel – it is *lonely* iff its row and column satisfy the conditions and the row’s pattern appears exactly `target` times.

```java
// Java – LeetCode 533: Lonely Pixel II
class Solution {
    public int findBlackPixel(char[][] picture, int target) {
        int m = picture.length, n = picture[0].length;
        int[] rowCnt = new int[m];
        int[] colCnt = new int[n];

        // 1️⃣ Count B's per row / column
        for (int r = 0; r < m; r++) {
            for (int c = 0; c < n; c++) {
                if (picture[r][c] == 'B') {
                    rowCnt[r]++;
                    colCnt[c]++;
                }
            }
        }

        // 2️⃣ Map of row patterns that have exactly target B's
        Map<String, Integer> rowPatternCount = new HashMap<>();
        String[] rowPattern = new String[m];
        for (int r = 0; r < m; r++) {
            if (rowCnt[r] == target) {
                StringBuilder sb = new StringBuilder(n);
                for (int c = 0; c < n; c++) sb.append(picture[r][c]);
                String pat = sb.toString();
                rowPattern[r] = pat;
                rowPatternCount.put(pat, rowPatternCount.getOrDefault(pat, 0) + 1);
            } else {
                rowPattern[r] = null;          // not eligible
            }
        }

        // 3️⃣ Validate each B
        int lonely = 0;
        for (int r = 0; r < m; r++) {
            if (rowCnt[r] != target) continue;
            String pat = rowPattern[r];
            if (rowPatternCount.getOrDefault(pat, 0) != target) continue;

            for (int c = 0; c < n; c++) {
                if (picture[r][c] == 'B' && colCnt[c] == target) {
                    lonely++;
                }
            }
        }
        return lonely;
    }
}
```

```python
# Python – LeetCode 533: Lonely Pixel II
class Solution:
    def findBlackPixel(self, picture: List[List[str]], target: int) -> int:
        m, n = len(picture), len(picture[0])

        # 1️⃣ Count B's per row / column
        row_cnt = [0] * m
        col_cnt = [0] * n
        for r in range(m):
            for c in range(n):
                if picture[r][c] == 'B':
                    row_cnt[r] += 1
                    col_cnt[c] += 1

        # 2️⃣ Map of row patterns that have exactly target B's
        row_pattern = [None] * m
        pattern_map = defaultdict(int)
        for r in range(m):
            if row_cnt[r] == target:
                pat = ''.join(picture[r])
                row_pattern[r] = pat
                pattern_map[pat] += 1

        # 3️⃣ Validate each B
        lonely = 0
        for r in range(m):
            if row_cnt[r] != target:
                continue
            pat = row_pattern[r]
            if pattern_map.get(pat, 0) != target:
                continue
            for c in range(n):
                if picture[r][c] == 'B' and col_cnt[c] == target:
                    lonely += 1
        return lonely
```

```cpp
// C++17 – LeetCode 533: Lonely Pixel II
class Solution {
public:
    int findBlackPixel(vector<vector<char>>& picture, int target) {
        int m = picture.size(), n = picture[0].size();
        vector<int> rowCnt(m), colCnt(n);

        // 1️⃣ Count B's per row / column
        for (int r = 0; r < m; ++r)
            for (int c = 0; c < n; ++c)
                if (picture[r][c] == 'B') {
                    ++rowCnt[r];
                    ++colCnt[c];
                }

        // 2️⃣ Map of row patterns that have exactly target B's
        unordered_map<string, int> patternCnt;
        vector<string> rowPattern(m);
        for (int r = 0; r < m; ++r) {
            if (rowCnt[r] == target) {
                string pat;
                pat.reserve(n);
                for (int c = 0; c < n; ++c) pat.push_back(picture[r][c]);
                rowPattern[r] = pat;
                ++patternCnt[pat];
            }
        }

        // 3️⃣ Validate each B
        int lonely = 0;
        for (int r = 0; r < m; ++r) {
            if (rowCnt[r] != target) continue;
            auto it = patternCnt.find(rowPattern[r]);
            if (it == patternCnt.end() || it->second != target) continue;
            for (int c = 0; c < n; ++c)
                if (picture[r][c] == 'B' && colCnt[c] == target)
                    ++lonely;
        }
        return lonely;
    }
};
```

---

## 2.  Blog Article – “Lonely Pixel II: The Good, the Bad, and the Ugly”

> **SEO Keywords**: *LeetCode 533, Lonely Pixel II, Java solution, Python solution, C++ solution, interview algorithm, software engineer interview, tech job, algorithm interview prep*

### 2.1 Introduction

When you land a **software engineer** interview, the questions that test your **array manipulation**, **hash‑map intuition**, and **edge‑case awareness** are always a staple. One such problem is **LeetCode 533 – Lonely Pixel II**. It may look like a quirky puzzle, but it actually teaches you how to combine **frequency counting** with **pattern matching** – a skill every senior engineer must master.

In this article, we’ll dissect the problem, walk through the cleanest implementation, expose common pitfalls (the *bad*), show what a messy brute‑force attempt looks like (the *ugly*), and give you the *good*—the elegant, production‑ready solution. By the end, you’ll be able to ace this question and impress recruiters on your résumé.

---

### 2.2 Problem Statement (Paraphrased)

You are given an `m x n` 2‑D array `picture` containing only the characters `'B'` (black) and `'W'` (white), and an integer `target`.

A **black lonely pixel** is a `'B'` at position `(r, c)` satisfying:

1. **Row & Column Count** – Row `r` and column `c` each contain exactly `target` black pixels.
2. **Row Pattern Equality** – Every row that has a `'B'` in column `c` is **identical** to row `r`.

Return the total number of black lonely pixels.

---

### 2.3 The Good – Clean, Efficient Approach

#### 2.3.1 Observations

1. **Row and column counts** can be obtained in a single pass – O(mn).
2. The **second condition** can be checked by grouping rows that are identical and have the exact target number of black pixels.
3. If a row pattern appears `target` times, every `'B'` in its columns that also have `target` black pixels in that column will be a lonely pixel.

#### 2.3.2 Algorithm Steps

1. **Count** black pixels per row (`rowCnt`) and per column (`colCnt`).
2. **Collect** rows whose `rowCnt == target`:
   - Convert each such row into a string (or any hashable representation).
   - Store the frequency of each pattern in a map (`patternCnt`).
3. **Iterate** over every black pixel `(r, c)`:
   - Ensure `rowCnt[r] == target`, `colCnt[c] == target`, and `patternCnt[pattern(r)] == target`.
   - If all true, increment answer.

#### 2.3.3 Complexity

- **Time**: O(m·n) – one pass for counts, another for verification.
- **Space**: O(m + n + k) where `k` is the number of unique eligible rows (≤ m).

#### 2.3.4 Code Highlights

- We pre‑store the string representation of each row in an array to avoid recomputation.
- We use the `patternCnt` map to enforce the *row equality* condition efficiently.

---

### 2.4 The Bad – Common Mistakes

| Mistake | Why It Fails | Fix |
|---------|--------------|-----|
| **Not checking column count** | Counting rows only misses the 2nd rule. | Add a column counter and validate `colCnt[c] == target`. |
| **Using `equals` on arrays instead of strings** | Array equality checks reference, not content. | Convert rows to strings or use `Arrays.hashCode`. |
| **Counting all rows regardless of target** | Generates false positives. | Filter rows where `rowCnt == target` before grouping. |
| **Double‑counting pixels** | Iterating over all cells and adding each lonely pixel per row can lead to overcounting when multiple rows share the same pattern. | Iterate each black pixel once; or multiply pattern frequency by target columns. |
| **Ignoring edge cases** (e.g., `target == 1` or all white rows) | May produce `0` incorrectly or index errors. | Handle `target` boundary conditions and validate array dimensions. |

---

### 2.5 The Ugly – Brute‑Force / Overly Complex Solutions

A typical “ugly” approach tries to **compare every row with every other row** and then check every column pair:

```java
for (int r1 = 0; r1 < m; ++r1) {
    for (int r2 = r1 + 1; r2 < m; ++r2) {
        if (!Arrays.equals(picture[r1], picture[r2])) continue;
        // ... more nested loops ...
    }
}
```

- **Time**: O(m²·n²) – far beyond acceptable limits for m, n ≤ 200.
- **Space**: O(1) aside from the input, but complexity is the killer.
- **Maintenance**: Hard to read, error‑prone, and offers no real advantage.

If you’re stuck on an interview and can’t think of the optimal idea, **don’t write the brute force**. Instead, describe the **conceptual steps**: “I would first count rows/columns, then group rows by pattern,” which demonstrates algorithmic thinking.

---

### 2.6 Take‑Away for the Interview

- **Show the big picture first**: Explain how you’ll count rows/columns, group patterns, and validate each pixel.
- **Justify your complexity**: Talk about O(mn) and why that satisfies LeetCode’s constraints.
- **Discuss edge cases**: Mention `target == 1`, all white pictures, etc.
- **Mention hash maps**: Explain that they allow O(1) lookups for row patterns.

These points will impress interviewers who look for **clear reasoning** and **algorithmic efficiency**.

---

### 2.7 Conclusion – Turning Pixels into a Job

Mastering problems like **Lonely Pixel II** showcases:

- Proficiency with **2‑D arrays** and **hash maps**.  
- Ability to translate **problem constraints** into a **time‑optimal solution**.  
- Skill in **handling edge cases** and **writing clean, readable code**.

Include the Java/Python/C++ snippets above in your portfolio or GitHub README. When you’re applying for a software engineering role, recruiters often skim code samples, and a concise, commented solution will instantly catch their eye.

**Good luck on your interview!**  
Remember: every pixel you solve correctly is a step closer to that dream tech job. 🚀

---