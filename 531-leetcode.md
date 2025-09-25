---
title: LeetCode 531. Lonely Pixel I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 531. Lonely Pixel I – “The Good, The Bad & The Ugly”  
> **LeetCode #531 | Medium | Java | Python | C++ | Algorithm | Interview Prep**

> *“When you’re in a job interview, you’re not just answering a question – you’re showcasing how you solve problems, how you think, and how you communicate.”*

---

### Table of Contents  
1. [Problem Statement](#problem-statement)  
2. [Thought Process & “Good” Design](#thought-process-good-design)  
3. [Common Pitfalls – The “Bad”](#common-pitfalls-bad)  
4. [Deep‑Dive – The “Ugly”](#deep-dive-ugly)  
5. [Optimal Solution](#optimal-solution)  
6. [Implementation in Three Languages](#implementation-in-three-languages)  
   - Java  
   - Python  
   - C++  
7. [Complexity Analysis](#complexity-analysis)  
8. [Testing & Edge Cases](#testing-edge-cases)  
9. [Takeaway & Interview Tips](#takeaway-interview-tips)  
10. [SEO Meta & Keywords](#seo-meta-keywords)  

---

## Problem Statement <a name="problem-statement"></a>

> You are given a 2‑D array `picture` of size `m × n`.  
> Each cell is either `'B'` (black) or `'W'` (white).  
> A **black lonely pixel** is a pixel `'B'` such that **its entire row and entire column contain no other `'B'`**.  
> Return the count of all black lonely pixels.

*Constraints*  

- `1 ≤ m, n ≤ 500`  
- `picture[i][j]` is `'B'` or `'W'`

---

## Thought Process – The “Good” Design <a name="thought-process-good-design"></a>

1. **Brute‑Force**  
   - For each `'B'`, scan its entire row & column → `O(m*n*(m+n))`.  
   - Works for small data, but **unacceptable** for `500×500`.

2. **Prefix Counting**  
   - Pre‑count black pixels in every row & column.  
   - Then, for each `'B'`, simply check if `rowCount[i] == 1 && colCount[j] == 1`.  
   - **Optimal**: `O(m*n)` time, `O(m+n)` space.

3. **Why This Is Good**  
   - **Simplicity** – clear logic, easy to reason.  
   - **Performance** – linear time, fits within limits.  
   - **Extensibility** – can be adapted to “Lonely Pixel II” (same row & column counts) with minimal changes.

---

## Common Pitfalls – The “Bad” <a name="common-pitfalls-bad"></a>

| Pitfall | Why It Happens | Consequence |
|---------|----------------|-------------|
| Counting inside the loop | Updating counts for each `'B'` as you iterate | O(n²) complexity, possible double counting |
| Off‑by‑one in indexing | Mixing up row/column indices | Wrong results, hard to debug |
| Using `HashMap` per row | High constant factors & memory overhead | Slower than array approach |
| Forgetting to treat `'W'` as 0 | Negative counts in columns | Wrong final answer |

> **Avoid these** by:  
> *Pre‑computing counts once,*  
> *Using plain arrays,*  
> *Testing on the edge cases (`all W`, `all B`, `single B`).*

---

## Deep‑Dive – The “Ugly” <a name="deep-dive-ugly"></a>

Sometimes interviewers want to see how you handle **extra constraints** or **edge cases** you might have overlooked:

- **Immutable Input** – If you cannot modify `picture`.  
  *Solution:* Count in separate arrays, do not mutate `picture`.

- **Space‑Optimized** – `O(1)` auxiliary space.  
  *Trick:* Use two arrays of size `m + n` for counts, which is `O(m+n)` – still optimal.

- **Parallel Execution** – If using multi‑threading.  
  *Caveat:* Ensure thread safety when updating counts.

- **Large Input** – Beyond 500x500 (e.g., 10⁵ × 10⁵).  
  *Approach:* Streaming input, counting on the fly, or using a hash set for rows/cols that have a single `'B'`.

---

## Optimal Solution <a name="optimal-solution"></a>

1. Create two integer arrays: `rowCount[m]` and `colCount[n]`.  
2. **First pass** – Count `'B'` in each row & column.  
3. **Second pass** – For each `'B'`, if `rowCount[i]==1 && colCount[j]==1` → increment answer.  
4. Return answer.

This algorithm uses only two passes and no extra data structures beyond the count arrays.

---

## Implementation in Three Languages <a name="implementation-in-three-languages"></a>

### Java

```java
import java.util.*;

class Solution {
    public int findLonelyPixel(char[][] picture) {
        int m = picture.length;
        int n = picture[0].length;

        int[] rowCount = new int[m];
        int[] colCount = new int[n];

        // 1st pass – count black pixels
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (picture[i][j] == 'B') {
                    rowCount[i]++;
                    colCount[j]++;
                }
            }
        }

        // 2nd pass – check loneliness
        int lonely = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (picture[i][j] == 'B' &&
                    rowCount[i] == 1 &&
                    colCount[j] == 1) {
                    lonely++;
                }
            }
        }
        return lonely;
    }
}
```

### Python

```python
class Solution:
    def findLonelyPixel(self, picture: List[List[str]]) -> int:
        m, n = len(picture), len(picture[0])
        row_cnt = [0] * m
        col_cnt = [0] * n

        # Count
        for i in range(m):
            for j in range(n):
                if picture[i][j] == 'B':
                    row_cnt[i] += 1
                    col_cnt[j] += 1

        # Check
        lonely = 0
        for i in range(m):
            for j in range(n):
                if picture[i][j] == 'B' and row_cnt[i] == 1 and col_cnt[j] == 1:
                    lonely += 1
        return lonely
```

### C++

```cpp
class Solution {
public:
    int findLonelyPixel(vector<vector<char>>& picture) {
        int m = picture.size();
        int n = picture[0].size();

        vector<int> rowCnt(m, 0), colCnt(n, 0);

        // Count black pixels
        for (int i = 0; i < m; ++i)
            for (int j = 0; j < n; ++j)
                if (picture[i][j] == 'B') {
                    ++rowCnt[i];
                    ++colCnt[j];
                }

        // Check loneliness
        int lonely = 0;
        for (int i = 0; i < m; ++i)
            for (int j = 0; j < n; ++j)
                if (picture[i][j] == 'B' &&
                    rowCnt[i] == 1 &&
                    colCnt[j] == 1)
                    ++lonely;

        return lonely;
    }
};
```

> **Tip:** In all three languages, you can swap the two passes if you prefer a single pass with a *hashset* of “candidate rows/cols” that have exactly one `'B'`.

---

## Complexity Analysis <a name="complexity-analysis"></a>

|   | Time | Extra Space |
|---|------|-------------|
| **Brute‑Force** | `O(m*n*(m+n))` | `O(1)` |
| **Optimal** | **`O(m*n)`** | **`O(m+n)`** |

- For `m, n ≤ 500`, the optimal solution runs in ~250,000 operations – well under 1 ms in Java, Python, and C++.
- Memory usage: two integer arrays of size ≤ 1 000 → < 4 KB.

---

## Testing & Edge Cases <a name="testing-edge-cases"></a>

| Test | Input | Expected |
|------|-------|----------|
| All White | `[['W','W'],['W','W']]` | `0` |
| All Black | `[['B','B'],['B','B']]` | `0` |
| One Lonely | `[['B','W'],['W','W']]` | `1` |
| Mixed | `[['B','W','W'],['W','B','W'],['W','W','B']]` | `0` |
| Large random | 500×500 with 1000 `'B'`s in random positions | O(1) – just run the solution |

> **Best practice:** Run the two‑pass algorithm on a random 500×500 grid 10× and assert the result is consistent.

---

## Takeaway & Interview Tips <a name="takeaway-interview-tips"></a>

- **Explain your plan** before coding.  
- **Discuss** why the array‑based counting is optimal.  
- **Show** the code in at least one language the interviewer is comfortable with (Java/Python/C++).  
- **Mention** the `O(m+n)` space trade‑off and that it’s still optimal.  
- **Ask** about constraints: immutable input, streaming data, or huge dimensions.  
- **Finish** by summarizing the complexity and giving a quick unit‑test snippet.

> **Remember:** In interviews, clarity beats cleverness. A clean, linear‑time solution that you can explain confidently is worth far more than a micro‑optimized but unreadable one.

---

## SEO Meta & Keywords <a name="seo-meta-keywords"></a>

**Meta Title**  
Lonely Pixel I (LeetCode 531) – Optimal Java, Python & C++ Solutions for Interviews

**Meta Description**  
Solve LeetCode 531 – Lonely Pixel I in Java, Python, and C++. Read our “Good, Bad & Ugly” analysis, learn the optimal O(m*n) algorithm, and get interview‑ready code snippets.

**Primary Keywords**  
- Lonely Pixel I  
- LeetCode 531  
- Interview algorithm  
- Java solution  
- Python solution  
- C++ solution  
- Time complexity  
- Space complexity  
- Job interview prep  

**Secondary Keywords**  
- Job interview tips  
- Coding interview  
- Data structures  
- Algorithm design  
- Black pixel counting  
- Prefix sums  

---

### Final Thought

> Mastering **Lonely Pixel I** demonstrates your ability to translate a problem statement into an **optimal algorithm** and implement it cleanly in multiple languages.  
> That’s exactly the skill set hiring managers look for in senior software engineers, data scientists, and backend developers.  

Happy coding, and best of luck on your next interview! 🚀