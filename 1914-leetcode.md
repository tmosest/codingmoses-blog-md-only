---
title: LeetCode 1914. Cyclically Rotating a Grid - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – LeetCode 1914

**Problem**  
> Given an even‑sized matrix `grid` (m × n) and an integer `k`,  
> rotate every “shell” (or layer) of the matrix anti‑clockwise by `k` positions.  
> `k` can be as large as 10⁹, so the rotation must be performed in **O(1)** per element.

A *shell* is the set of cells that share the same Manhattan distance from the outer border.  
The outermost shell contains all the border cells, the next shell is the
border of the sub‑matrix that remains after peeling the outer layer, and so on.

> **Return the grid after all shells have been rotated.**

**Constraints**

| Constraint | Value |
|-----------|-------|
| `grid.length == m` | 2 ≤ m ≤ 30 |
| `grid[0].length == n` | 2 ≤ n ≤ 30 |
| `m, n` even | yes |
| `0 ≤ grid[i][j] ≤ 1000` | yes |
| `0 ≤ k ≤ 10⁹` | yes |

The goal is an **O(m·n)** time solution that uses only **O(m·n)** extra memory (or even less).

---

## 2.  High‑Level Algorithm

1. **Peel each shell**  
   * Move clockwise / counter‑clockwise along the four edges (top → left → bottom → right) and collect the elements into a 1‑D array `shell[]`.
2. **Rotate the 1‑D array**  
   * Because `k` can be huge, rotate only `k % shell.length` times.  
   * A left rotation by `r` can be done in place with a *reverse* trick (or by slicing).
3. **Write the rotated shell back**  
   * Iterate over the same four edges in the same order and overwrite the original matrix with the rotated values.
4. **Repeat for every shell**  
   * Start with the outer shell (`top = 0`, `left = 0`, `bottom = m-1`, `right = n-1`) and shrink the borders until `top >= bottom` or `left >= right`.

---

## 2.  Code Implementations

Below you’ll find three independent solutions, one in each of the three most common interview languages.

> **Tip:** In every interview‑ready implementation we use `k %= shellLen` immediately after the shell is extracted so we never iterate more than necessary.

---

### 2.1  Java (O(m·n) time, O(m·n) extra space)

```java
import java.util.*;

class Solution {
    public int[][] rotateGrid(int[][] grid, int k) {
        int m = grid.length, n = grid[0].length;
        int top = 0, left = 0, bottom = m - 1, right = n - 1;

        while (top < bottom && left < right) {
            // 1. Extract the current shell into a 1‑D array
            int len = 2 * (bottom - top + right - left);
            int[] shell = new int[len];
            int idx = 0;

            for (int r = top; r < bottom; r++)       // left column (top→bottom)
                shell[idx++] = grid[r][left];
            for (int c = left; c < right; c++)       // bottom row (left→right)
                shell[idx++] = grid[bottom][c];
            for (int r = bottom; r > top; r--)       // right column (bottom→top)
                shell[idx++] = grid[r][right];
            for (int c = right; c > left; c--)       // top row (right→left)
                shell[idx++] = grid[top][c];

            // 2. Rotate the shell
            int shift = k % len;
            if (shift != 0) {
                int[] rotated = new int[len];
                for (int i = 0; i < len; i++) {
                    rotated[i] = shell[(i + shift) % len];
                }
                shell = rotated;
            }

            // 3. Write the rotated shell back
            idx = 0;
            for (int r = top; r < bottom; r++)       // left column
                grid[r][left] = shell[idx++];
            for (int c = left; c < right; c++)       // bottom row
                grid[bottom][c] = shell[idx++];
            for (int r = bottom; r > top; r--)       // right column
                grid[r][right] = shell[idx++];
            for (int c = right; c > left; c--)       // top row
                grid[top][c] = shell[idx++];

            // shrink the rectangle for the next shell
            top++; bottom--; left++; right--;
        }
        return grid;
    }
}
```

**Complexity**

| | |
|---|---|
| **Time** | `O(m·n)` – each element is read and written once per shell |
| **Space** | `O(m·n)` in the worst case (the outer shell) – can be reduced to `O(min(m,n))` by re‑using a single array |

---

### 2.2  Python 3 (O(m·n) time, O(m·n) extra space)

```python
class Solution:
    def rotateGrid(self, grid: List[List[int]], k: int) -> List[List[int]]:
        m, n = len(grid), len(grid[0])
        top, left, bottom, right = 0, 0, m - 1, n - 1

        while top < bottom and left < right:
            # 1. Extract the current shell
            shell = []

            for r in range(top, bottom):          # left column
                shell.append(grid[r][left])

            for c in range(left, right):          # bottom row
                shell.append(grid[bottom][c])

            for r in range(bottom, top, -1):       # right column
                shell.append(grid[r][right])

            for c in range(right, left, -1):       # top row
                shell.append(grid[top][c])

            # 2. Rotate left by k % len(shell)
            shift = k % len(shell)
            if shift:
                shell = shell[shift:] + shell[:shift]

            # 3. Write back
            idx = 0
            for r in range(top, bottom):          # left column
                grid[r][left] = shell[idx]; idx += 1

            for c in range(left, right):          # bottom row
                grid[bottom][c] = shell[idx]; idx += 1

            for r in range(bottom, top, -1):       # right column
                grid[r][right] = shell[idx]; idx += 1

            for c in range(right, left, -1):       # top row
                grid[top][c] = shell[idx]; idx += 1

            top, bottom, left, right = top + 1, bottom - 1, left + 1, right - 1

        return grid
```

**Complexity**

| | |
|---|---|
| **Time** | `O(m·n)` |
| **Space** | `O(min(m, n))` – the temporary shell array |

---

### 2.3  C++ (O(m·n) time, O(m·n) extra space)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> rotateGrid(vector<vector<int>>& grid, int k) {
        int m = grid.size(), n = grid[0].size();
        int top = 0, bottom = m - 1, left = 0, right = n - 1;

        while (top < bottom && left < right) {
            // 1. Extract shell
            vector<int> shell;
            for (int r = top; r < bottom; ++r)          // left column
                shell.push_back(grid[r][left]);

            for (int c = left; c < right; ++c)          // bottom row
                shell.push_back(grid[bottom][c]);

            for (int r = bottom; r > top; --r)          // right column
                shell.push_back(grid[r][right]);

            for (int c = right; c > left; --c)          // top row
                shell.push_back(grid[top][c]);

            // 2. Rotate left by k % shell.size()
            int shift = k % shell.size();
            if (shift) {
                rotate(shell.begin(), shell.begin() + shift, shell.end());
            }

            // 3. Write back
            int idx = 0;
            for (int r = top; r < bottom; ++r)
                grid[r][left] = shell[idx++];

            for (int c = left; c < right; ++c)
                grid[bottom][c] = shell[idx++];

            for (int r = bottom; r > top; --r)
                grid[r][right] = shell[idx++];

            for (int c = right; c > left; --c)
                grid[top][c] = shell[idx++];

            ++top; --bottom; ++left; --right;
        }
        return grid;
    }
};
```

**Complexity**

| | |
|---|---|
| **Time** | `O(m·n)` |
| **Space** | `O(min(m,n))` – the shell array |

> **Why we do `k % shell.size()`?**  
> Rotating a shell of length `L` by `L` or `2·L` positions is the identity.
> Using the modulo eliminates needless work when `k` is huge.

---

## 3.  “Good” vs. “Bad” Code: What Interviewers Look For

| | |
|---|---|
| **Good** | Clear separation of **shell extraction**, **rotation**, **write‑back**. |
| | Uses `k % shellLen` right after extraction. |
| | Shrinks the boundaries in a single line (`++top; --bottom; ...`). |
| | Comments explain *why* not *how* (e.g., “anti‑clockwise rotation by left shift”). |
| **Bad** | Recomputes shell length multiple times; does not use modulo; writes back without using the rotated array directly. |
| | Nested loops with redundant bounds checks. |
| | No comments or explanation of reverse rotation. |

---

## 4.  “Good” vs. “Bad” Code – A Mini‑Guide

| Feature | Good | Bad |
|---------|------|-----|
| **Clarity** | `extractShell()`, `rotateArray()`, `writeBack()` helpers | Everything tangled in a single loop |
| **Complexity awareness** | `k %= shellLen` to avoid useless iterations | Directly iterating `k` times even when `k > shellLen` |
| **Boundary shrinking** | Single line shrink (`++top; --bottom; ...`) | Multiple variables changed in separate lines, making bugs easy |
| **Documentation** | Comments explaining each step | No comments, just code |
| **Testing** | Handles empty / 1‑cell matrices (edge case) | Only tests for the typical case |

---

## 5.  Why This Matters for Interviews

1. **Time‑Complexity is the first signal** – recruiters ask for the best possible complexity.  
   `O(m·n)` is the minimal time; anything slower will instantly be rejected.
2. **Space‑Complexity shows awareness** – using `O(min(m,n))` instead of `O(m·n)` reveals that you know the shell length is bounded by the smaller dimension.
3. **Modulo trick for huge `k`** – demonstrates knowledge that `k` can overflow and that rotations are cyclical.
4. **Readable, self‑contained code** – interviewers value code that they can read, test, and explain in under a minute.

---

## 6.  “Good” vs. “Bad” Code – A Final Checklist

- [ ] **Shell extraction** done once per shell.  
- [ ] **Rotation** uses `k % shellLen` immediately.  
- [ ] **Write‑back** follows the same order as extraction.  
- [ ] **Variables** for rectangle boundaries are updated atomically.  
- [ ] **Comments** explain *why* we do each step.  
- [ ] **No hard‑coded limits** – the solution scales with input size.

---

## 7.  TL;DR for Interviewers

> *You peel a shell, rotate its 1‑D representation by `k % length`, and write it back. Repeat until all shells are done. Implement this in O(m·n) time and O(min(m,n)) space.*

---

## 8.  Takeaway

> The presented solutions are **perfect for technical interviews**:
> * They solve the problem within constraints.  
> * They showcase algorithmic insight (peeling shells, reverse‑rotation trick).  
> * They are written in **Java, Python, and C++**, covering the majority of interview stacks.

Good luck with your interview prep, and remember: a clean, well‑commented solution is far more impressive than a fast, unreadable one. Happy coding! 🚀

---

### Frequently Asked Questions

* **Can we solve this with O(1) extra space?**  
  Yes – you can rotate the shell in place with four‑way swapping. It’s a bit more involved but still O(m·n) time.
* **Why left rotation by `r` works for anti‑clockwise movement?**  
  Because we collected the shell in *clockwise* order, a left rotation by `r` places the element that was `r` steps ahead onto the current position, which equals an anti‑clockwise rotation in the 2‑D matrix.
* **Do we need to handle odd dimensions?**  
  The problem guarantees even dimensions, so the inner rectangle always shrinks evenly.

--- 

Happy interview‑preparing! If you have any questions or want a deeper dive into the reverse‑rotation trick, just let me know.