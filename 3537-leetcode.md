---
title: LeetCode 3537. Fill a Special Grid - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 3537 – *Fill a Special Grid*  
> **Keywords:** *Fill a Special Grid*, *LeetCode 3537*, *Java solution*, *Python solution*, *C++ solution*, *divide‑and‑conquer*, *job interview*, *recursion*

---

### TL;DR

| Language | Time | Space |
|----------|------|-------|
| **Java** | O(4ⁿ) (i.e. O((2ⁿ)²)) | O(4ⁿ) |
| **Python** | O(4ⁿ) | O(4ⁿ) |
| **C++** | O(4ⁿ) | O(4ⁿ) |

> The key insight: a 2ⁿ×2ⁿ grid can be recursively divided into four 2ⁿ⁻¹×2ⁿ⁻¹ sub‑grids.  
>  Place numbers in the order **top‑right → bottom‑right → bottom‑left → top‑left**.  
>  This guarantees the required “<” relationships between quadrants.

---

## 1️⃣ Problem Recap

> **Fill a Special Grid**  
> You’re given an integer `n`. Build a `2ⁿ × 2ⁿ` matrix filled with the integers `0 … 2²ⁿ – 1` such that:
> 1. Every element in the **top‑right** quadrant is smaller than any element in the **bottom‑right** quadrant.  
> 2. Every element in the **bottom‑right** quadrant is smaller than any element in the **bottom‑left** quadrant.  
> 3. Every element in the **bottom‑left** quadrant is smaller than any element in the **top‑left** quadrant.  
> 4. Each quadrant itself is a “special” grid (recursive condition).  
> 5. Any `1×1` grid is special.

> Return the completed matrix.

> **Examples**

| `n` | Result | Reasoning |
|-----|--------|-----------|
| 0 | `[[0]]` | Only one element |
| 1 | `[[3,0],[2,1]]` | Order of quadrants: TR=0 < BR=1 < BL=2 < TL=3 |
| 2 | `[[15,12,3,0],[14,13,2,1],[11,8,7,4],[10,9,6,5]]` | Recursive construction |

---

## 2️⃣ Why This Problem Rocks for Interviews

| ✅ Good | ❌ Bad | ⚠️ Ugly |
|--------|--------|---------|
| **Recursion + Divide‑and‑Conquer** – core CS skill | **Hard to reason about** – you must understand how quadrant ordering works | **Off‑by‑one / indexing mistakes** – easy to trip on row/col splits |
| **Clean, deterministic output** – no randomness | **Edge case n=0** – trivial but must still return a matrix | **Large `n`** (≤10) → 4ⁿ up to 1 048 576 – memory & recursion depth can be a concern |
| **No external libraries** – pure algorithm | | |

> Mastering this problem demonstrates:  
> * Recursive thinking  
> * Array manipulation  
> * Time/Space analysis  
> * Attention to detail (index handling)

---

## 3️⃣ High‑Level Idea

1. **Base case** – If the sub‑grid is `1×1`, place the current counter value and increment it.  
2. **Recursive step** –  
   * Divide current sub‑grid into 4 equal quadrants.  
   * Fill the quadrants in the order **top‑right → bottom‑right → bottom‑left → top‑left**.  
   * Because we always increment the counter after filling a quadrant, numbers in earlier quadrants are guaranteed to be smaller than numbers in later quadrants, satisfying the 3 ordering conditions.  
3. Recurse until every cell is filled.

The recursion depth is `n` (≤10), safe in all languages.  
The counter starts at `0` and ends at `2²ⁿ – 1`.

---

## 4️⃣ Implementation

### 4.1 Java

```java
import java.util.Arrays;

public class Solution {
    private int counter = 0;     // global counter

    public int[][] specialGrid(int n) {
        int size = 1 << n;            // 2^n
        int[][] grid = new int[size][size];
        fill(grid, 0, size - 1, 0, size - 1);
        return grid;
    }

    /**
     * Recursively fill sub‑grid defined by row/col ranges.
     */
    private void fill(int[][] grid, int sr, int er, int sc, int ec) {
        // Base case: 1x1 cell
        if (sr == er && sc == ec) {
            grid[sr][sc] = counter++;
            return;
        }

        int midRow = (sr + er) / 2;
        int midCol = (sc + ec) / 2;

        // Order: top‑right, bottom‑right, bottom‑left, top‑left
        fill(grid, sr, midRow, midCol + 1, ec);          // top‑right
        fill(grid, midRow + 1, er, midCol + 1, ec);      // bottom‑right
        fill(grid, midRow + 1, er, sc, midCol);          // bottom‑left
        fill(grid, sr, midRow, sc, midCol);              // top‑left
    }

    // Demo main (optional)
    public static void main(String[] args) {
        Solution s = new Solution();
        int[][] g = s.specialGrid(2);
        for (int[] row : g) System.out.println(Arrays.toString(row));
    }
}
```

### 4.2 Python

```python
def specialGrid(n: int) -> list[list[int]]:
    size = 1 << n                     # 2**n
    grid = [[0] * size for _ in range(size)]
    counter = 0

    def fill(sr, er, sc, ec):
        nonlocal counter
        if sr == er and sc == ec:
            grid[sr][sc] = counter
            counter += 1
            return
        mid_r, mid_c = (sr + er) // 2, (sc + ec) // 2
        # Order: top-right, bottom-right, bottom-left, top-left
        fill(sr, mid_r, mid_c + 1, ec)      # top-right
        fill(mid_r + 1, er, mid_c + 1, ec)  # bottom-right
        fill(mid_r + 1, er, sc, mid_c)      # bottom-left
        fill(sr, mid_r, sc, mid_c)          # top-left

    fill(0, size - 1, 0, size - 1)
    return grid
```

### 4.3 C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<vector<int>> specialGrid(int n) {
        int size = 1 << n;                    // 2^n
        vector<vector<int>> grid(size, vector<int>(size));
        int counter = 0;
        fill(grid, 0, size - 1, 0, size - 1, counter);
        return grid;
    }

private:
    void fill(vector<vector<int>>& g, int sr, int er, int sc, int ec, int& cnt) {
        if (sr == er && sc == ec) {
            g[sr][sc] = cnt++;
            return;
        }
        int midR = (sr + er) / 2;
        int midC = (sc + ec) / 2;
        // Order: top-right, bottom-right, bottom-left, top-left
        fill(g, sr, midR, midC + 1, ec, cnt);        // top-right
        fill(g, midR + 1, er, midC + 1, ec, cnt);    // bottom-right
        fill(g, midR + 1, er, sc, midC, cnt);        // bottom-left
        fill(g, sr, midR, sc, midC, cnt);            // top-left
    }
};
```

---

## 5️⃣ Complexity Analysis

| Language | Time | Space |
|----------|------|-------|
| Java / Python / C++ | **O(4ⁿ)** (each cell visited once) | **O(4ⁿ)** (the grid itself) |

> `4ⁿ = (2ⁿ)²` – the total number of cells in the grid.  
> Recursion depth is `n` (≤10), negligible relative to the grid size.

---

## 6️⃣ Edge Cases & Gotchas

| Problem | Pitfall | Fix |
|---------|---------|-----|
| `n = 0` | Returning a `0 × 0` array | Handle base case explicitly (`size = 1 << 0 = 1`). |
| Index calculation | Off‑by‑one when splitting rows/cols | Use integer division `mid = (start + end) / 2`. |
| Counter overflow | 2²ⁿ may exceed `int` for large `n` | `n ≤ 10`, `int` suffices; for larger values use `long`. |
| Stack overflow | Recursive depth > 1000 | Not a problem here (`n ≤ 10`), but use iterative approach for higher constraints. |

---

## 7️⃣ Alternative Approaches

| Approach | When to Use | Pros | Cons |
|----------|-------------|------|------|
| **Bit‑mask / XOR trick** | Some participants discovered a pattern where the value equals the bitwise XOR of row and column indices | O(1) assignment per cell | Hard to justify; not obvious in interview |
| **Iterative (no recursion)** | Avoid recursion depth concerns | Same time/space | More code complexity |
| **Bottom‑up DP** | Fill the grid level‑by‑level | No recursion, easier to debug | Slightly more memory due to temporary arrays |

---

## 8️⃣ Final Thoughts

- **Recursive divide‑and‑conquer** is the canonical solution.  
- The quadrant order **TR → BR → BL → TL** is the secret that enforces the three ordering constraints.  
- The algorithm is *deterministic* and *fully deterministic*, making it an excellent interview demo of **clarity + correctness**.

---

## 🎯 How This Boosts Your Interview Resume

- **Shows mastery of recursion** – a staple in many algorithm interviews.  
- **Demonstrates clean code** – minimal helper functions, good naming.  
- **Highlights problem‑solving** – you turn a complex “ordering” condition into a simple traversal order.  
- **SEO advantage** – when recruiters search “Fill a Special Grid Java”, your GitHub or blog post will appear.

---

### 🔗 Further Reading

- [LeetCode 3537 – Fill a Special Grid (official problem)](https://leetcode.com/problems/fill-a-special-grid/)
- [Divide‑and‑Conquer Patterns](https://www.geeksforgeeks.org/divide-and-conquer-algorithms/)
- [Recursion vs. Iteration](https://www.programiz.com/dsa/recursion-vs-iteration)

---

### 🎬 Wrap‑up

> **“Fill a Special Grid”** is more than a coding challenge—it's a micro‑lesson in recursive mindset, array slicing, and meticulous indexing.  
> With the solutions above, you’re ready to ace the question and impress hiring managers with your algorithmic flair.  

Happy coding and good luck! 🚀

---