---
title: LeetCode 378. Kth Smallest Element in a Sorted Matrix - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 378. Kth Smallest Element in a Sorted Matrix  

**Time Complexity** – `O(n log(maxVal-minVal))`  
**Space Complexity** – `O(1)` (apart from the input matrix)

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++** that use the binary‑search‑on‑value technique.

---

## 1. Java Solution

```java
import java.util.*;

class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        long low  = matrix[0][0];
        long high = matrix[n - 1][n - 1];

        while (low < high) {
            long mid = low + (high - low) / 2;
            int count = countLessEqual(matrix, mid, n);

            if (count < k) {
                low = mid + 1;          // need larger numbers
            } else {
                high = mid;             // mid could be the answer
            }
        }
        return (int) low;
    }

    /** Count how many elements are <= target in the sorted matrix */
    private int countLessEqual(int[][] matrix, long target, int n) {
        int count = 0;
        int row = n - 1, col = 0;           // start from bottom‑left
        while (row >= 0 && col < n) {
            if (matrix[row][col] <= target) {
                count += row + 1;          // all elements above are also <= target
                col++;
            } else {
                row--;
            }
        }
        return count;
    }
}
```

---

## 2. Python 3 Solution

```python
from typing import List

class Solution:
    def kthSmallest(self, matrix: List[List[int]], k: int) -> int:
        n = len(matrix)
        low, high = matrix[0][0], matrix[-1][-1]

        while low < high:
            mid = (low + high) // 2
            if self._count_le(matrix, mid, n) < k:
                low = mid + 1
            else:
                high = mid
        return low

    def _count_le(self, matrix: List[List[int]], target: int, n: int) -> int:
        """Return number of elements <= target."""
        cnt, row, col = 0, n - 1, 0          # bottom‑left corner
        while row >= 0 and col < n:
            if matrix[row][col] <= target:
                cnt += row + 1              # all above are <= target
                col += 1
            else:
                row -= 1
        return cnt
```

---

## 3. C++ Solution

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int kthSmallest(vector<vector<int>>& matrix, int k) {
        int n = matrix.size();
        long long low = matrix[0][0];
        long long high = matrix[n-1][n-1];

        while (low < high) {
            long long mid = low + (high - low) / 2;
            if (countLE(matrix, mid, n) < k)
                low = mid + 1;
            else
                high = mid;
        }
        return static_cast<int>(low);
    }

private:
    // Count how many elements are <= target
    int countLE(const vector<vector<int>>& matrix, long long target, int n) {
        int cnt = 0, row = n-1, col = 0;   // bottom-left corner
        while (row >= 0 && col < n) {
            if (matrix[row][col] <= target) {
                cnt += row + 1;            // all elements above are also <= target
                ++col;
            } else {
                --row;
            }
        }
        return cnt;
    }
};
```

---

## 4. Blog Article – “The Good, The Bad, and The Ugly of LeetCode 378”

> **Title:** *Kth Smallest Element in a Sorted Matrix – The Good, The Bad, & The Ugly*  
> **Keywords:** LeetCode 378, Kth Smallest Element, Java, Python, C++, Binary Search Matrix, Interview Coding, Algorithm Design, Job Interview Tips

---

### 🚀 Introduction

LeetCode 378 – *Kth Smallest Element in a Sorted Matrix* – is a staple of technical interviews.  
It forces you to think beyond brute force, blending **binary search** with **matrix traversal**.  

In this article we dissect the problem, present **clean code** in Java, Python, and C++, and dive into the *good*, *bad*, and *ugly* aspects that can trip even seasoned developers.

---

## 🛠️ Problem Recap

You’re given an `n × n` matrix where **every row and column is sorted** in ascending order.  
Return the **k‑th smallest element** in the matrix (counting duplicates).  
Constraints: `1 ≤ n ≤ 300`, values may range from `-10⁹` to `10⁹`.

Key requirement: **Memory < O(n²)** – you cannot flatten the matrix into a list.

---

## 🔧 The Good – Why Binary Search on Value Works

1. **Elegant** – We treat the matrix as a sorted “array” of numbers by their **value** rather than position.
2. **No Extra Space** – Only a few integer variables; O(1) auxiliary memory.
3. **Fast** – `O(n log(maxVal - minVal))` time. For typical 32‑bit integers, the log term is about 31–32, so it’s practically linear in `n`.
4. **Deterministic** – No heap priority queue overhead; you never have to store more than `O(n)` pointers.
5. **Scalable** – Works for the upper constraint `n = 300` comfortably (≈ 9 000 elements).

---

## ⚙️ The Bad – What Makes It Tricky

| Issue | Why It Matters | Mitigation |
|-------|----------------|------------|
| **Range of values** | `maxVal - minVal` can be huge (`2·10⁹`), so the binary search loop runs many iterations if we use a naive “mid = (low + high)/2” with 32‑bit ints. | Use 64‑bit (`long long`/`long`) arithmetic to avoid overflow. |
| **Duplicates** | The matrix may contain repeated numbers; the algorithm must handle “k‑th smallest” including duplicates, not distinct values. | The count helper counts all elements ≤ mid; duplicates automatically increase the count. |
| **Edge cases** | Single‑element matrices, negative numbers, k = 1 or k = n². | Start and end pointers initialized to actual matrix corners; loop exits when `low == high`. |
| **Performance** | Counting ≤ mid is `O(n)` per iteration. | The count routine walks from bottom‑left to top‑right in a single pass. |

---

## 😱 The Ugly – Common Pitfalls & How to Avoid Them

1. **Off‑by‑One in Binary Search**  
   *Mistake*: Using `while (low < high)` but setting `low = mid` or `high = mid` incorrectly.  
   *Fix*: When `count < k`, set `low = mid + 1`. Otherwise set `high = mid`.  

2. **Integer Overflow**  
   *Mistake*: `mid = (low + high) / 2` with 32‑bit ints may overflow.  
   *Fix*: `mid = low + (high - low) / 2` or use 64‑bit types.

3. **Wrong Count Logic**  
   *Mistake*: Incrementing count by 1 each time you see an element ≤ mid.  
   *Fix*: Because we start at bottom‑left, each time we move right, *all* elements in that column up to the current row are ≤ mid. So add `row + 1`.

4. **Assuming Matrix Is Square**  
   *Mistake*: Some solutions assume `matrix.length == matrix[0].length`.  
   *Fix*: Use `n = matrix.size()` only after confirming the matrix is non‑empty.

5. **Not Handling Negative Numbers**  
   *Mistake*: Initializing `low` and `high` to 0 instead of actual corners.  
   *Fix*: Always set `low = matrix[0][0]`, `high = matrix[n-1][n-1]` – this works for negative values too.

---

## 📘 Full Code Walkthrough

### 1. Java

- Uses `long` for `low/high/mid`.
- Count helper walks **bottom‑left → top‑right**.
- Comments explain each step.

### 2. Python

- Uses built‑in `int` (unbounded), so overflow is not an issue.
- Still careful with the `low = mid + 1` logic.

### 3. C++

- Uses `long long` for the value range.
- `countLE` uses the same bottom‑left trick.

---

## 📈 Alternative Approaches

| Approach | Complexity | When to Use |
|----------|------------|-------------|
| **Min‑Heap (k‑times extraction)** | `O(n log n)` time, `O(n)` space | Simpler to implement if you’re comfortable with priority queues. Good for interview “quick‑win” but less elegant. |
| **Flatten + nth_element (C++)** | `O(n²)` space → violates constraints | Only suitable when the interviewer relaxes memory limits. |

---

## 🎯 How These Solutions Help You Land a Job

- **Showcase Algorithmic Thinking** – Binary search on value demonstrates a deep understanding of data structure invariants.
- **Cross‑Language Mastery** – Having Java, Python, and C++ implementations shows you can adapt to any tech stack.
- **Performance‑First Mindset** – The O(1) space and O(n log range) time illustrate a production‑ready mindset.

**Interview Tip:**  
*When asked to solve 378, first describe the idea, then talk through the count logic, and finally present the code. Mention the O(1) memory requirement and the trick of walking from the bottom‑left corner.*

---

### 🔚 Conclusion

LeetCode 378 is more than a test of binary search—it’s a test of careful reasoning, boundary‑handling, and clean coding.  
Mastering this “good, bad, ugly” analysis will not only impress hiring managers but also give you confidence to tackle a wide range of interview problems.

Happy coding, and may your k‑th smallest element always land on the job you’re after! 🚀

--- 

*Feel free to copy the code snippets into your preferred IDE, run the official test cases, and share this article with peers who are prepping for their next interview.*