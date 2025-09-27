---
title: LeetCode 1861. Rotating the Box - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣ Java, Python & C++ Solutions for **LeetCode 1861 – Rotating the Box**

Below you’ll find clean, production‑ready implementations for the three most popular interview languages.  
All solutions follow the same idea:

1. **Apply gravity** – for every row, slide every stone (`#`) as far right as it can go while respecting obstacles (`*`).  
2. **Rotate** – transpose the matrix and flip it horizontally (or use the classic “rotate 90° clockwise” algorithm).

The time complexity is **O(m × n)** and the space complexity is **O(1)** extra (the output matrix is the required return value).

---

### 📄 Java (LeetCode‑style)

```java
import java.util.Arrays;

public class Solution {

    public char[][] rotateTheBox(char[][] box) {
        int m = box.length;          // original rows
        int n = box[0].length;       // original cols

        /* 1️⃣ Apply gravity – each row independently */
        for (int i = 0; i < m; i++) {
            int empty = n - 1;   // farthest right empty spot
            for (int j = n - 1; j >= 0; j--) {
                if (box[i][j] == '*') {          // obstacle
                    empty = j - 1;                // next free spot is left of it
                } else if (box[i][j] == '#') {    // stone
                    if (j != empty) {
                        box[i][empty] = '#';
                        box[i][j] = '.';
                    }
                    empty--;                       // next stone must land left
                }
            }
        }

        /* 2️⃣ Rotate 90° clockwise */
        char[][] rotated = new char[n][m];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                rotated[j][m - 1 - i] = box[i][j];
            }
        }
        return rotated;
    }

    /* Simple test harness – remove for LeetCode submission */
    public static void main(String[] args) {
        Solution s = new Solution();
        char[][] input = {
            {'#', '.', '#'},
        };
        char[][] out = s.rotateTheBox(input);
        for (char[] row : out) System.out.println(Arrays.toString(row));
    }
}
```

---

### 🐍 Python (Python 3.9+)

```python
class Solution:
    def rotateTheBox(self, box: list[list[str]]) -> list[list[str]]:
        m, n = len(box), len(box[0])

        # 1️⃣ Apply gravity – each row independently
        for i in range(m):
            empty = n - 1
            for j in range(n - 1, -1, -1):
                if box[i][j] == '*':
                    empty = j - 1
                elif box[i][j] == '#':
                    if j != empty:
                        box[i][empty] = '#'
                        box[i][j] = '.'
                    empty -= 1

        # 2️⃣ Rotate 90° clockwise
        return [[box[i][j] for i in range(m)][::-1] for j in range(n)]

# Example
if __name__ == "__main__":
    s = Solution()
    grid = [["#", ".", "#"]]
    print(s.rotateTheBox(grid))
```

---

### 🏗️ C++ (C++17)

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<vector<char>> rotateTheBox(vector<vector<char>>& box) {
        int m = box.size();
        int n = box[0].size();

        /* 1️⃣ Apply gravity – each row independently */
        for (int i = 0; i < m; ++i) {
            int empty = n - 1;
            for (int j = n - 1; j >= 0; --j) {
                if (box[i][j] == '*') {
                    empty = j - 1;          // obstacle blocks the way
                } else if (box[i][j] == '#') {
                    if (j != empty) {
                        box[i][empty] = '#';
                        box[i][j] = '.';
                    }
                    --empty;                // next stone must land left
                }
            }
        }

        /* 2️⃣ Rotate 90° clockwise */
        vector<vector<char>> rotated(n, vector<char>(m));
        for (int i = 0; i < m; ++i)
            for (int j = 0; j < n; ++j)
                rotated[j][m - 1 - i] = box[i][j];

        return rotated;
    }
};
```

---

## 2️⃣ Blog Article – “Rotating the Box: The Good, The Bad, and The Ugly”

> **Title:** Rotating the Box (LeetCode 1861) – A Deep Dive, Full Solution & Interview Tips  
> **Meta‑Description:** Master LeetCode 1861 “Rotating the Box” with step‑by‑step walkthrough, Java/Python/C++ code, complexity analysis, edge‑case handling, and interview‑friendly explanations. Boost your coding interview score!  
> **Keywords:** rotating the box, leetcode 1861, matrix rotation, gravity simulation, interview prep, coding interview, algorithmic problem, Java solution, Python solution, C++ solution

---

### 1️⃣ Problem Overview

On LeetCode you’re given a 2‑D grid that represents a **side‑view** of a rectangular box.  
Cells can be:

| Character | Meaning |
|-----------|---------|
| `#` | stone |
| `*` | stationary obstacle |
| `.` | empty space |

The box is rotated **90° clockwise**.  
Stones fall straight down under gravity, stopping on an obstacle, another stone, or the bottom of the box.  
Obstacles never move, and the stones keep their horizontal position during the rotation.

Your task is to return the grid after the rotation.

> **Why does this matter?**  
> It tests your ability to manipulate 2‑D arrays, understand gravity simulation, and perform matrix rotation—all common themes in coding interviews.

---

### 2️⃣ The Good: What Works

1. **Two‑Pointer Gravity** – By scanning each row from right to left and maintaining the “next free spot” (`empty`) we can slide stones in **O(n)** per row.  
2. **Deterministic Rotation** – A single nested loop rotates the matrix without extra memory beyond the result grid.  
3. **Simplicity & Readability** – The solution uses only primitive operations; no fancy libraries or complex data structures.  
4. **Linear Complexity** – With `m × n ≤ 250 000`, the algorithm comfortably fits within LeetCode’s time limits.

---

### 3️⃣ The Bad: Edge Cases & Gotchas

| Issue | Explanation | Mitigation |
|-------|-------------|------------|
| **Obstacles adjacent to stones** | A stone right next to an obstacle should not pass it. | The two‑pointer logic sets `empty = j - 1` when encountering `*`, ensuring the stone lands left of it. |
| **Stones already at the rightmost column** | They must stay put. | If `j == empty`, the `if (j != empty)` guard skips unnecessary moves. |
| **Large grids** | Too many nested loops could hit Python’s recursion limit? | We avoid recursion; all loops are iterative. |
| **Non‑rectangular input** | LeetCode guarantees a rectangle, but a robust solution should guard against `box[i] == null`. | Optional defensive checks can be added. |

---

### 4️⃣ The Ugly: What to Watch Out For

1. **Confusing Indices** – Remember that after rotation, the original row becomes a column. The formula `rotated[j][m - 1 - i] = box[i][j]` can trip you up.  
2. **Mutable Input** – The algorithm mutates the original `box`. If you need the original for debugging, keep a copy.  
3. **Language‑Specific Pitfalls** –  
   * **Java**: Use `char[][]` (not `String[]`) to avoid immutable strings.  
   * **Python**: `list[list[str]]` is recommended to keep row mutability.  
   * **C++**: Avoid out‑of‑bounds with `vector<vector<char>>`.  

---

### 5️⃣ Code Walk‑through (Java Edition)

```java
// 1️⃣ Apply gravity on each row
for (int i = 0; i < m; i++) {
    int empty = n - 1;                       // rightmost free cell
    for (int j = n - 1; j >= 0; j--) {       // scan from right to left
        if (box[i][j] == '*') {              // obstacle – block further stones
            empty = j - 1;
        } else if (box[i][j] == '#') {        // stone
            if (j != empty) {                // move only if displaced
                box[i][empty] = '#';
                box[i][j] = '.';
            }
            empty--;                         // next stone must land left
        }
    }
}
```

*Why does this work?*  
Because stones can only move left in the **pre‑rotation** grid (which will become “down” after the rotation).  
The `empty` pointer always points to the nearest empty spot where a stone could settle.  
When we hit an obstacle, we shrink the reachable area by moving `empty` one cell left.

---

### 6️⃣ Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Gravity per row | **O(n)** | **O(1)** |
| All rows | **O(m × n)** | **O(1)** |
| Rotation | **O(m × n)** | **O(m × n)** (output matrix) |
| **Total** | **O(m × n)** | **O(m × n)** |

---

### 7️⃣ Quick Test (Python)

```python
s = Solution()
grid = [
    ["#", ".", "*", "."],
    ["#", "#", "*", "."]
]
print(s.rotateTheBox(grid))
# Expected:
# [['#', '.'],
#  ['#', '#'],
#  ['*', '*'],
#  ['.', '.']]
```

---

### 8️⃣ Interview Tips

- **Ask clarifying questions**: e.g., “Do stones maintain horizontal positions after rotation?”  
- **Explain your approach out loud**: walk through the gravity simulation and rotation separately.  
- **Highlight edge cases**: obstacles next to stones, stones at the boundary, empty grids.  
- **Time‑complexity matters**: reassure the interviewer you’re not using nested loops that iterate over the entire matrix more than twice.

---

### 9️⃣ SEO & Career Impact

- **Keywords**: *Rotating the Box, LeetCode 1861, matrix rotation, gravity simulation, interview prep, algorithmic problem, Java solution, Python solution, C++ solution*  
- **Meta Description**: *Master LeetCode 1861 “Rotating the Box” with step‑by‑step walkthrough, full solution in Java, Python, and C++, complexity analysis, and interview‑ready explanations.*  
- **Blog Post Hook**: “Want to ace the matrix‑manipulation question on your next coding interview? Read how to solve Rotating the Box in 30 seconds.”  

By publishing this article on a platform that ranks for “LeetCode 1861 solution,” you’ll attract hiring managers who are actively searching for candidates who can solve interview problems fast and cleanly. Use an engaging tone, code snippets, and an “interview‑ready” section to showcase your problem‑solving mindset—exactly what recruiters want.

---

### 🔟 Wrap‑Up

Rotating the Box may look intimidating at first, but once you split the problem into **gravity** and **rotation**, the solution is straightforward and elegant.  
With the provided code in your preferred language and the interview‑friendly explanation above, you’ll be ready to impress any recruiter tackling this classic coding interview challenge. Good luck!

--- 

*Happy coding!* 🚀
--- 

> *Author note: Feel free to adapt the article to your own voice or add your own “real‑world” examples. The more context you give, the higher the chance recruiters will see you as a valuable asset.*