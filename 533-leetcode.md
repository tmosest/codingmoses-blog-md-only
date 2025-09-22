---
title: LeetCode 533. Lonely Pixel II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 533. Lonely Pixel II  
**LeetCode** – Medium  
**Signature**  
```java
public int findBlackPixel(char[][] picture, int target)
```

---

### Problem recap  

You are given an `m × n` matrix `picture` consisting only of the characters `'B'` (black) and `'W'` (white).  
A *black lonely pixel* is a black pixel `B` located at position `(r, c)` that satisfies **both** of the following rules:

1. **Exact count** – the row `r` contains exactly `target` black pixels and the column `c` also contains exactly `target` black pixels.
2. **Uniform rows** – every row that has a black pixel in column `c` must be **identical** to row `r`.

Return the total number of black lonely pixels in the picture.

---

## 1.  Why is this problem interesting?

| Aspect | Why it matters |
|--------|----------------|
| **Data‑structure use** | You need to keep row and column black‑counts – simple arrays. |
| **Set comparison** | The “uniform rows” rule forces you to compare whole rows; a common mistake is to only compare a single value. |
| **Avoiding double counting** | A naive nested loop that counts per cell can count the same pixel many times if you’re not careful. |
| **Edge cases** | Empty rows/columns, `target` larger than `min(m, n)` – all need to be handled gracefully. |

If you can solve this cleanly, you’ll impress interviewers with:

* Clear reasoning and correctness proof.
* Good time/space complexity analysis.
* Familiarity with common interview patterns (prefix‑sum, hashmap, string comparison).

---

## 2.  The “Good” – a clean O(m × n) algorithm

1. **Count black pixels per row and per column**  
   ```text
   rowCount[i]   = number of 'B' in picture[i]
   colCount[j]   = number of 'B' in column j
   ```

2. **Find candidate columns** – only columns whose `colCount` equals `target` can contribute.  
   For each such column, collect the set of rows that contain a black pixel in that column.

3. **Check the “uniform rows” rule**  
   * If a candidate column has exactly `target` rows, then all those rows must be the same.  
   * Verify this by comparing the string representation of each row in the set with the first one.

4. **Count**  
   * If a column passes the test, every one of its `target` black pixels is lonely.  
   * Add `target` to the answer for every passing column.

### Pseudocode

```text
for each row i:
    rowCount[i] = count 'B' in picture[i]

for each column j:
    colCount[j] = count 'B' in column j

answer = 0
for each column j:
    if colCount[j] != target: continue

    rowsWithB = list of i where picture[i][j] == 'B'
    if rowsWithB.size() != target: continue

    firstRow = rowsWithB[0]
    uniform = true
    for each i in rowsWithB:
        if picture[i] != picture[firstRow]:
            uniform = false
            break
    if uniform:
        answer += target

return answer
```

**Why does this work?**

* A black pixel can only be lonely if its row and column both have exactly `target` black pixels – step 2 guarantees this.
* If any row in the column is different, rule 2 is violated, so those pixels are not lonely.
* When all rows are identical, every black pixel in that column satisfies both rules; there are `target` of them.

---

## 3.  The “Bad” – pitfalls to avoid

| Mistake | Consequence |
|---------|-------------|
| Counting per *cell* and adding `1` if the row and column counts match | Will double‑count pixels that belong to a column whose rows are **not** identical |
| Using a HashMap of string to list of indices without verifying uniformity | Might count a column that appears “good” because the map size is `target` but rows differ |
| Comparing rows character‑by‑character on the fly for each cell | Adds O(n) per cell → O(m × n²) time, too slow for 200×200 |
| Forgetting to reset or reuse temporary variables between columns | Leads to wrong answers for later columns |

---

## 4.  The “Ugly” – naive brute force

```text
ans = 0
for each cell (i, j):
    if picture[i][j] != 'B': continue
    if countRow(i) != target or countCol(j) != target: continue
    // verify uniform rows by scanning all rows with B at column j
    if allRowsWithBAtColumnJEqualRowI: ans++
return ans
```

*This algorithm* runs in **O(m × n²)** and is a textbook example of “do the simplest thing you can think of” that fails on large inputs.  
It still passes the 200×200 limits on LeetCode but is *un‑friendly* for interview questions where the interviewer expects a linear‑time solution.

---

## 5.  Correctness proof (brief)

1. **Necessity** – If a pixel is counted by the algorithm, it lies in a column `c` with `colCount[c] == target` and in a row `r` with `rowCount[r] == target`.  
   The algorithm further verifies that all rows that contain a black pixel in column `c` are identical to row `r`.  
   Thus all three rules hold → the pixel is indeed a black lonely pixel.

2. **Sufficiency** – Suppose a pixel `(r, c)` is a black lonely pixel.  
   By rule 1, `rowCount[r] == target` and `colCount[c] == target`.  
   By rule 2, every row that contains a black pixel in column `c` is identical to row `r`.  
   Therefore column `c` will be accepted by the algorithm and the algorithm will add `target` to the answer.  
   Since `(r, c)` is one of those `target` pixels, it is counted.

Thus the algorithm counts *exactly* the black lonely pixels.

---

## 6.  Complexity analysis

| Step | Time | Space |
|------|------|-------|
| Count rows & columns | `O(m × n)` | `O(m + n)` |
| Iterate candidate columns | `O(m × n)` (each column scans at most `target` rows, and `target ≤ min(m, n) ≤ 200`) | `O(m)` for temporary list |
| Total | **`O(m × n)`** | **`O(m + n)`** |

For `m, n ≤ 200`, this is trivially fast.

---

## 7.  Code snippets

### Java (LeetCode style)

```java
public class Solution {
    public int findBlackPixel(char[][] picture, int target) {
        int m = picture.length, n = picture[0].length;
        int[] rowCount = new int[m];
        int[] colCount = new int[n];

        // 1. Count black pixels per row and column
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (picture[i][j] == 'B') {
                    rowCount[i]++;
                    colCount[j]++;
                }
            }
        }

        int answer = 0;
        // 2. Inspect each column
        for (int j = 0; j < n; j++) {
            if (colCount[j] != target) continue;

            List<Integer> rowsWithB = new ArrayList<>();
            for (int i = 0; i < m; i++) {
                if (picture[i][j] == 'B') rowsWithB.add(i);
            }

            if (rowsWithB.size() != target) continue;

            // 3. Verify all rows are identical
            int firstRow = rowsWithB.get(0);
            boolean uniform = true;
            for (int r : rowsWithB) {
                if (!Arrays.equals(picture[r], picture[firstRow])) {
                    uniform = false;
                    break;
                }
            }

            if (uniform) answer += target;
        }

        return answer;
    }
}
```

### Python 3

```python
class Solution:
    def findBlackPixel(self, picture: List[List[str]], target: int) -> int:
        m, n = len(picture), len(picture[0])

        row_cnt = [sum(1 for c in row if c == 'B') for row in picture]
        col_cnt = [sum(1 for i in range(m) if picture[i][j] == 'B') for j in range(n)]

        ans = 0
        for j in range(n):
            if col_cnt[j] != target:
                continue

            rows = [i for i in range(m) if picture[i][j] == 'B']
            if len(rows) != target:
                continue

            first = rows[0]
            if all(picture[r] == picture[first] for r in rows):
                ans += target
        return ans
```

### C++17

```cpp
class Solution {
public:
    int findBlackPixel(vector<vector<char>>& picture, int target) {
        int m = picture.size(), n = picture[0].size();
        vector<int> rowCnt(m, 0), colCnt(n, 0);

        // Count
        for (int i = 0; i < m; ++i)
            for (int j = 0; j < n; ++j)
                if (picture[i][j] == 'B') {
                    ++rowCnt[i];
                    ++colCnt[j];
                }

        int ans = 0;
        for (int j = 0; j < n; ++j) {
            if (colCnt[j] != target) continue;

            vector<int> rows;
            for (int i = 0; i < m; ++i)
                if (picture[i][j] == 'B') rows.push_back(i);

            if ((int)rows.size() != target) continue;

            bool uniform = true;
            for (int r : rows)
                if (picture[r] != picture[rows[0]]) {
                    uniform = false;
                    break;
                }

            if (uniform) ans += target;
        }
        return ans;
    }
};
```

All three implementations run in **O(m × n)** time and use **O(m + n)** additional space.

---

## 8.  Blog article – “The Good, the Bad, and the Ugly of Lonely Pixel II”

### 8.1  Title & Meta‑Description

| Title | Meta‑Description |
|-------|-------------------|
| **The Good, the Bad, and the Ugly of LeetCode 533 – Lonely Pixel II** | Learn how to crack LeetCode 533 with an optimal O(m × n) solution, avoid common pitfalls, and impress hiring managers in your next software engineering interview. |

**SEO Keywords:** LeetCode 533, Lonely Pixel II, black lonely pixel, Java coding interview, Python interview, C++ interview, algorithm interview question, interview preparation, software engineering job interview.

### 8.2  Introduction

> “A good algorithm is one that **solves the problem** with **minimal resources**.”  
> That sentence rings true for *Lonely Pixel II*, an interview staple that tests not just brute‑force skills but also your ability to spot the right data structure, eliminate redundant work, and prove correctness.

In this post we’ll dissect the problem, walk through a clean O(m × n) solution, show how the naive approach goes wrong, and give you the confidence to talk it over at your next interview.

---

### 8.3  Problem Breakdown

* **Input**: `m × n` grid (`'B'`/`'W'`) and integer `target`.
* **Output**: Number of *black lonely pixels*.
* **Rules**:
  1. Row and column both contain exactly `target` black pixels.
  2. All rows that have a black pixel in that column are identical.

Why does this seem deceptively simple?  
Because a single mis‑step—like counting rows and columns separately but never checking uniformity—can give you the wrong answer.

---

### 8.4  The “Good”: Linear‑Time Strategy

1. **Pre‑compute counts**: `rowCount[i]`, `colCount[j]`.  
   *Time*: `O(m × n)`.
2. **Filter columns**: Only keep those with `colCount[j] == target`.
3. **For each candidate column**:
   * Build a list of rows that contain `'B'` at this column.  
     Since `colCount[j] == target`, this list will have at most `target` rows.
   * If the list size ≠ `target` → skip.
   * Compare every row in the list with the first one.  
     If any differ → rule 2 is broken → skip.
   * Otherwise add `target` to the answer.

> **Result**: Every pixel in a “good” column is counted exactly once.

---

### 8.5  The “Bad”: Where People Go Wrong

| Bad practice | What to watch for |
|--------------|-------------------|
| Counting per *cell* | Leads to double‑counting when rows differ. |
| Relying on hash maps | Works for size but not uniformity. |
| Comparing rows on the fly | Extra `O(n)` per cell → quadratic time. |

We’ll see why the “brute‑force” code passes the 200×200 grid on LeetCode but *is not interview‑ready*.

---

### 8.6  The “Ugly” Brute Force

```java
for each cell
    if rowCnt == target && colCnt == target
        if all rows with B at col equal current row
            count++
```

*O(n) per cell for the row check* → **O(m × n²)**.  
Still under 200×200 limits, but it’s a textbook example of *“I didn't optimize, I just coded”*.

### 8.7  From Ugly to Good: The Optimal Linear Solution

1. **Two one‑pass scans** to compute row and column counts.
2. **Iterate columns** and collect only those that satisfy rule 1.
3. **Verify uniformity** in a single pass over the at most `target` rows.

Why is this elegant?  
* The algorithm never re‑counts rows or columns.  
* It guarantees O(m × n) time and uses only linear auxiliary memory.  
* The proof of correctness is concise and fits the “explain your algorithm” moment in an interview.

---

### 8.8  Talking Points for Interviews

1. **Explain the two‑phase counting** – it’s the foundation of any linear solution.
2. **Show how you prune columns** – you can skip 80% of the grid in many cases.
3. **Highlight uniformity check** – a quick `Arrays.equals` in Java, `picture[r] == picture[first]` in Python, or `vector` comparison in C++.
4. **Mention complexity** – `O(m × n)` time, `O(m + n)` space.  
5. **Proof of correctness** – a bullet‑point outline, not the entire formal write‑up.

---

### 8.9  Final Takeaway

> *Lonely Pixel II is a microcosm of software engineering: gather information, avoid redundant work, and confirm that everything fits together.*

Master the O(m × n) strategy above, keep the pitfalls in mind, and you’ll have a ready‑to‑present solution that satisfies both the problem and the interview panel.

Happy coding and best of luck on your next job interview!

---

### 8.10  Call‑to‑Action

> **Want more interview‑ready solutions?** Subscribe to our newsletter for weekly algorithm explanations, mock interview practice, and interview‑prep hacks that land you a software engineering role.

--- 

### 8.11  About the Author

> *Your Name* – Full‑stack developer, 5 years of hiring experience, author of several open‑source projects, and a frequent speaker at coding bootcamps.  
> *Contact*: `you@example.com`

---

## 9.  Final words

*Lonely Pixel II* is a prime example of how a seemingly small problem can be a great interview challenge.  
By focusing on **row/column counting** + **uniformity verification**, you deliver a succinct, provably correct, and *interview‑ready* solution.  

Remember the three layers:

1. **Good** – Linear algorithm that hits all the rules.
2. **Bad** – Common mistakes that break the logic.
3. **Ugly** – Brute‑force that works only because the limits are tiny.

With the code, proof, and blog outline above, you’re now fully equipped to ace LeetCode 533 and shine in any software engineering interview. Happy coding!