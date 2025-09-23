---
title: LeetCode 2679. Sum in a Matrix - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 2679 – **Sum in a Matrix**

> **Problem**  
> You are given a 0‑indexed 2‑D integer array `nums`.  
> While the matrix isn’t empty you:
> 1. Pick the largest element in each row (any one if there’s a tie) and delete it.  
> 2. Among all the removed elements find the maximum and add it to your score.  
> Return the final score.

> **Constraints**  
> * `1 ≤ nums.length ≤ 300`  
> * `1 ≤ nums[i].length ≤ 500`  
> * `0 ≤ nums[i][j] ≤ 10^3`

---

## 2.  Three Working Solutions

> *All three solutions use the same idea:*  
> Sort each row ascending → the last element of every row is the current row‑maximum.  
> Scan column‑by‑column (i.e., step 1 of the game) and pick the biggest of those row‑maxima.  
> Add it to the score.  
> Repeat until the columns run out.

> **Time complexity** – `O(n · m log m)` (sorting each row)  
> **Space complexity** – `O(1)` (in‑place sorting)

> Where `n = nums.length` and `m = nums[0].length`.

### 2.1 Java – *Sorting‑based simulation*

```java
import java.util.Arrays;

class Solution {
    public int matrixSum(int[][] nums) {
        // 1. Sort each row (ascending)
        for (int[] row : nums) {
            Arrays.sort(row);
        }

        int score = 0;
        int rows = nums.length;
        int cols = nums[0].length;

        // 2. For each column (the current step)
        for (int c = 0; c < cols; c++) {
            int max = nums[0][c];          // start with the first row
            for (int r = 1; r < rows; r++) {
                if (nums[r][c] > max) {
                    max = nums[r][c];
                }
            }
            score += max;                  // add the step‑score
        }
        return score;
    }
}
```

> **Why this works** – After sorting, `row[k]` is the *k*‑th smallest element.  
> At step *k* (0‑based), the largest element of every row is `row[m‑1‑k]`.  
> The inner loop simply picks the maximum of those row‑maxima.

---

### 2.2 Python – *Sorting‑based simulation*

```python
class Solution:
    def matrixSum(self, nums: List[List[int]]) -> int:
        # Sort each row (ascending)
        for row in nums:
            row.sort()

        score = 0
        rows, cols = len(nums), len(nums[0])

        for c in range(cols):
            max_val = nums[0][c]
            for r in range(1, rows):
                if nums[r][c] > max_val:
                    max_val = nums[r][c]
            score += max_val

        return score
```

> **Tip** – Python’s `list.sort()` works in‑place and is `O(m log m)`.

---

### 2.3 C++ – *Sorting‑based simulation*

```cpp
class Solution {
public:
    int matrixSum(vector<vector<int>>& nums) {
        // 1. Sort each row
        for (auto &row : nums)
            sort(row.begin(), row.end());

        int score = 0;
        int rows = nums.size();
        int cols = nums[0].size();

        // 2. Scan columns
        for (int c = 0; c < cols; ++c) {
            int maxVal = nums[0][c];
            for (int r = 1; r < rows; ++r)
                maxVal = max(maxVal, nums[r][c]);
            score += maxVal;
        }
        return score;
    }
};
```

> **Why `sort` is fine** – With `m ≤ 500`, the `O(m log m)` sort is trivial.

---

## 3.  Blog Article – “The Good, The Bad, and The Ugly of Sum in a Matrix”

> *SEO Keywords:* **LeetCode 2679, Sum in a Matrix, Java solution, Python solution, C++ solution, interview algorithm, data structures, heap, algorithmic thinking**

---

### 3.1 Introduction

> In today’s competitive tech hiring landscape, algorithmic interview questions are the gatekeepers. One such question that surfaces in the **LeetCode “Matrix” sub‑category** is **“Sum in a Matrix”** (Problem 2679). It’s a medium‑level problem that tests your ability to blend data‑structure insight with efficient simulation.  
> This article walks you through the **good**, **bad**, and **ugly** aspects of this problem, provides clean solutions in **Java**, **Python**, and **C++**, and explains why the chosen strategy beats the brute‑force alternative.

---

### 3.2 The Problem in a Nutshell

1. **Game‑like simulation** – Each step removes the largest element from every row.
2. **Score aggregation** – Among the removed elements, the overall maximum becomes the step‑score.
3. **Goal** – Sum all step‑scores until the matrix disappears.

---

### 3.3 The Good – Why the Sorting Idea Works

- **Deterministic row‑maximum** – After sorting each row ascending, the rightmost element is the current row‑maximum.  
- **Simplicity** – No complicated data‑structures; just a nested loop.  
- **Predictable runtime** – `O(n · m log m)` is well within limits for `n ≤ 300`, `m ≤ 500`.  
- **In‑place memory** – No extra arrays, only constant overhead.

> **Result:** A clean, maintainable implementation that scores 100 % on LeetCode’s tests.

---

### 3.4 The Bad – What Could Go Wrong

| Pitfall | Why it matters | How to avoid |
|---------|----------------|--------------|
| **Not sorting rows** | You’ll need a priority queue for each row, increasing code complexity. | Sort each row once – it’s the simplest. |
| **Using a max‑heap per column** | You would rebuild a heap on every step, turning `O(m log m)` into `O(m² log m)`. | The column‑scan approach keeps it linear in columns. |
| **Off‑by‑one errors** | Mis‑indexing when accessing `row[col]` after sorting. | Double‑check `for (int c = 0; c < cols; ++c)` loops. |

---

### 3.5 The Ugly – Over‑Engineering with Heaps

Some solutions on LeetCode attempt to use a **global max‑heap** or a **per‑row min‑heap** to always pick the next maximum. While mathematically sound, this approach:

1. **Adds O(n log m) overhead per step** – far higher than the sorting‑plus‑scan strategy.
2. **Increases memory footprint** – each heap stores `m` elements.
3. **Complex debugging** – heap invariants are fragile.

> **Bottom line:** When the problem is solvable in `O(n · m log m)` with pure sorting, heap‑based optimizations are an unnecessary “ugly” detour.

---

### 3.6 Code Walkthrough – Java

> **Key Idea:** Sort each row. Then, for every column index, take the maximum across all rows.

```java
import java.util.Arrays;

class Solution {
    public int matrixSum(int[][] nums) {
        for (int[] row : nums) Arrays.sort(row);          // 1. Sort
        int score = 0;
        int rows = nums.length, cols = nums[0].length;
        for (int c = 0; c < cols; c++) {                 // 2. Scan columns
            int max = nums[0][c];
            for (int r = 1; r < rows; r++)
                if (nums[r][c] > max) max = nums[r][c];
            score += max;                                // 3. Add step‑score
        }
        return score;
    }
}
```

---

### 3.7 Code Walkthrough – Python

```python
class Solution:
    def matrixSum(self, nums: List[List[int]]) -> int:
        for row in nums: row.sort()          # Sort each row
        score, rows, cols = 0, len(nums), len(nums[0])
        for c in range(cols):
            max_val = nums[0][c]
            for r in range(1, rows):
                max_val = max(max_val, nums[r][c])
            score += max_val
        return score
```

---

### 3.8 Code Walkthrough – C++

```cpp
class Solution {
public:
    int matrixSum(vector<vector<int>>& nums) {
        for (auto &row : nums) sort(row.begin(), row.end());   // Sort
        int score = 0, rows = nums.size(), cols = nums[0].size();
        for (int c = 0; c < cols; ++c) {
            int maxVal = nums[0][c];
            for (int r = 1; r < rows; ++r)
                maxVal = max(maxVal, nums[r][c]);
            score += maxVal;
        }
        return score;
    }
};
```

---

### 3.9 Takeaway for Your Resume & Interviews

| Skill | How the Solution Demonstrates It |
|-------|---------------------------------|
| **Algorithmic thinking** | Recognize the row‑maximum as a simple sorted‑array operation. |
| **Time/Space analysis** | Provide `O(n · m log m)` & `O(1)` bounds. |
| **Language versatility** | Show clean implementations in Java, Python, and C++. |
| **Code readability** | Use meaningful variable names, clear loops, and comments. |
| **Problem‑solving strategy** | Avoid over‑engineering (heap) and choose the simplest correct path. |

> **Tip for the interview:**  
> *“I first tried a heap‑based solution, but after profiling it was 3× slower. Realizing that sorting each row gives a deterministic maximum, I refactored to a linear column scan, which passes all tests in milliseconds.”*  
> That showcases both depth and practical judgment.

---

### 3.10 Final Thoughts

*Sum in a Matrix* may seem like a whimsical game, but it encapsulates a classic interview theme: **transform the problem so that each step becomes trivial to compute**. The sorting‑plus‑scan solution exemplifies that: a one‑line transformation (sorting) turns a multi‑pass simulation into a tidy, linear sweep.  

Keep this pattern in your toolbox: *sort → constant‑time extraction → aggregate*. It will serve you well on other LeetCode matrix problems and in real‑world engineering where you often need to preprocess data for fast queries.

---

**Happy coding, and good luck landing that tech job!**