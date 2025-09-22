---
title: LeetCode 1198. Find Smallest Common Element in All Rows - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1198 – “Find Smallest Common Element in All Rows”  
*Solve it in Java, Python and C++ – plus an SEO‑optimized blog post to land that next job interview.*

---

### 1️⃣ The Problem (easy copy‑paste for your notes)

```
Given an m × n matrix `mat` where every row is sorted in strictly increasing order,
return the smallest common element that appears in **every** row.

If no common element exists, return -1.

Constraints
------------
- 1 ≤ m, n ≤ 500
- 1 ≤ mat[i][j] ≤ 10⁴
- mat[i] is strictly increasing
```

**Examples**

| Input | Output |
|-------|--------|
| `[[1,2,3,4,5],[2,4,5,8,10],[3,5,7,9,11],[1,3,5,7,9]]` | `5` |
| `[[1,2,3],[2,3,4],[2,3,5]]` | `2` |

---

## 2️⃣ The “Good, Bad & Ugly” Solution Roadmap

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | `O(m · n · log n)` (binary search per row) – perfectly fine for the constraints. | If you naïvely check every element against every row (`O(m·n²)`) you’ll TLE on 500×500. | Using a slow nested loop or an un‑optimised language‑specific feature can still hit time limits. |
| **Space Complexity** | `O(1)` auxiliary (in‑place checks). | None – you can keep it constant. | Storing all rows in a hash‑set of sets (`O(m·n)`) is overkill and wastes memory. |
| **Readability** | Clear, small helper for binary search. | Hard‑to‑read recursive solutions. | Ugly lambda hacks that obscure the intent. |
| **Language‑specific** | Java, Python, C++ all have fast binary‑search libraries. | Python’s built‑in `bisect` is best; using a plain loop can be slower. | C++ `std::binary_search` is elegant; avoid manual pointer arithmetic if you want readability. |

---

## 3️⃣ Code – Three Languages

> **NOTE:** All solutions use a helper that does binary search on a *sorted* row.  
> They stop as soon as they find a common element – the smallest due to row ordering.

### 3.1 Java

```java
import java.util.*;

public class Solution {
    // O(m * n * log n) time, O(1) space
    public int smallestCommonElement(int[][] mat) {
        if (mat == null || mat.length == 0) return -1;

        for (int target : mat[0]) {               // candidates from first row
            boolean allRowsContain = true;
            for (int r = 1; r < mat.length; r++) {
                if (!binarySearch(mat[r], target)) {
                    allRowsContain = false;
                    break;
                }
            }
            if (allRowsContain) return target;    // first (smallest) common found
        }
        return -1;                                // no common element
    }

    private boolean binarySearch(int[] arr, int target) {
        int left = 0, right = arr.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (arr[mid] == target) return true;
            if (arr[mid] < target)  left = mid + 1;
            else                    right = mid - 1;
        }
        return false;
    }
}
```

### 3.2 Python

```python
from bisect import bisect_left
from typing import List

class Solution:
    # O(m * n * log n) time, O(1) space
    def smallestCommonElement(self, mat: List[List[int]]) -> int:
        if not mat: return -1

        for target in mat[0]:          # iterate through the first row
            if all(self._in_row(row, target) for row in mat[1:]):
                return target
        return -1

    @staticmethod
    def _in_row(row: List[int], target: int) -> bool:
        """Binary search in a sorted row."""
        i = bisect_left(row, target)
        return i < len(row) and row[i] == target
```

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // O(m * n * log n) time, O(1) space
    int smallestCommonElement(vector<vector<int>>& mat) {
        if (mat.empty()) return -1;
        for (int target : mat[0]) {                 // candidates from first row
            bool common = true;
            for (size_t r = 1; r < mat.size(); ++r) {
                if (!binary_search(mat[r].begin(), mat[r].end(), target)) {
                    common = false;
                    break;
                }
            }
            if (common) return target;              // first (smallest) common found
        }
        return -1;                                  // no common element
    }
};
```

---

## 4️⃣ Why These Solutions Land Jobs

| Reason | How It Helps |
|--------|--------------|
| **Use of Binary Search** | Shows mastery of classic algorithmic tricks; efficient for sorted data. |
| **Clear Separation of Concerns** | Small helper (`binarySearch` / `_in_row`) improves readability and unit‑testability. |
| **Time & Space Awareness** | Interviews often ask “Why is this better?” – you can explain `O(m·n·log n)` vs. `O(m·n²)`. |
| **Language‑Specific Best Practices** | Java: `private` helper; Python: `bisect`; C++: `std::binary_search`. |

---

## 5️⃣ Blog Post – “The Good, The Bad, and the Ugly of LeetCode 1198”

> **Meta Description:**  
> Master LeetCode 1198 in Java, Python, & C++ with a clear “good, bad, ugly” analysis. Learn binary search, time‑space trade‑offs, and interview‑ready code. Land your next job!

---

### 5.1 Title

**“The Good, The Bad, and the Ugly of LeetCode 1198 – Find Smallest Common Element in All Rows”**

### 5.2 Introduction

> LeetCode 1198 asks you to find the smallest element that appears in **every** row of a sorted matrix.  
> On the surface it seems trivial, but hidden pitfalls can bite you in an interview.  
> In this post we’ll dissect the problem, walk through a clean binary‑search solution, and explore alternative approaches that might look appealing at first glance but end up as the *bad* or *ugly* options.

### 5.3 Problem Recap (With Constraints)

```text
- m, n ≤ 500
- mat[i][j] ≤ 10⁴
- Every row is strictly increasing
```

> *Why does “strictly increasing” matter?* It guarantees that binary search will never hit duplicate values that could skew our logic.

### 5.4 The “Good” – Binary Search Per Row

- **Why?**  
  Each row is sorted, so checking if a target exists is `O(log n)`.  
  Since we must verify the candidate against every other row, the total cost is `O(m · n · log n)` – well within limits.

- **Code Highlights (Java)**  

  ```java
  for (int target : mat[0]) {
      boolean common = true;
      for (int r = 1; r < mat.length; r++) {
          if (!binarySearch(mat[r], target)) {
              common = false; break;
          }
      }
      if (common) return target;
  }
  ```

- **Key Takeaways**  
  1. **Candidate Source** – Always iterate the first row; any common element must be there.  
  2. **Early Exit** – As soon as a row fails, break – saves time.  
  3. **Small Helper** – Keeps the outer loop clean.

### 5.5 The “Bad” – Brute‑Force Intersection

- **What is it?**  
  Store each row in a `HashSet`, then intersect all sets.  
  Time: `O(m · n)` – linear, but with high constant overhead.  
  Space: `O(m · n)` – can reach 250 000 integers (≈ 1 MB) *per language*.

- **Why It’s Bad**  
  1. **Extra Memory** – Interviewers love low‑space solutions.  
  2. **Insertion Overhead** – Building sets takes time; not strictly needed because rows are already sorted.  
  3. **Edge‑Case Complexity** – Need to handle duplicates carefully, even though the problem guarantees uniqueness.

- **When It Might Work**  
  If the matrix size were huge and you had unlimited memory, the hash‑set approach could be acceptable, but with the given constraints the binary‑search method is cleaner.

### 5.6 The “Ugly” – Nested Loops Without Binary Search

- **What it Looks Like**  

  ```python
  for i in range(m):
      for j in range(n):
          # O(n) linear scan on every row
  ```

- **Why Ugly**  
  - O(m·n²) – Quadratic, will TLE on 500×500.  
  - Hard to read: double nesting obscures intent.  
  - No guarantee of early exit or leveraging sorted property.

- **Common Pitfall**  
  Many new coders write this “simple” solution without realizing the huge time penalty. It’s a classic interview trap.

### 5.7 Cross‑Language Best Practices

| Language | Recommendation |
|----------|----------------|
| **Java** | Use a dedicated private helper for binary search; avoid `Arrays.binarySearch` if you want to show your own logic. |
| **Python** | Leverage `bisect` module – it’s C‑implemented and faster than a manual loop. |
| **C++** | Use `std::binary_search`; if you’re comfortable with iterators, the standard library is concise and fast. |

### 5.8 Time & Space Complexity Summary

| Approach | Time | Space |
|----------|------|-------|
| Binary Search Per Row | **O(m · n · log n)** | **O(1)** |
| HashSet Intersection | **O(m · n)** | **O(m · n)** |
| Nested Linear Scan | **O(m · n²)** | **O(1)** |

> **Bottom line:** The binary‑search approach hits the sweet spot of speed and minimal memory usage, making it the ideal interview answer.

### 5.9 Practical Interview Tips

1. **Explain Constraints Early** – Show you read the problem statement thoroughly.  
2. **Walk Through Edge Cases** – e.g., single row, single column, no common element.  
3. **State Your Complexity** – Interviewers love candidates who can articulate why their solution is efficient.  
4. **Show Clean Code** – Small helper functions, meaningful variable names (`target`, `common`).  

### 5.10 Final Thoughts

- **LeetCode 1198** is a textbook example of *leveraging sorted data*.  
- A well‑structured binary‑search solution demonstrates both algorithmic knowledge and clean coding practices.  
- By understanding the good, the bad, and the ugly, you’ll be prepared to explain why your chosen approach is superior in a real interview.

> Ready to ace that interview? Drop a comment or share your own variant of the solution. 🚀

### 5.11 Call‑to‑Action

> If you found this post helpful, share it on LinkedIn or Twitter with the hashtag **#LeetCode1198**.  
> And stay tuned for more “problem‑deconstruction” posts to sharpen your coding‑interview edge!

---

## 6️⃣ End of the Blog Post

---

> By combining *clean algorithmic solutions* with *interview‑oriented explanations*, you can confidently tackle LeetCode 1198 and impress hiring managers across any tech stack. Happy coding!