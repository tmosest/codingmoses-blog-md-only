---
title: LeetCode 3189. Minimum Moves to Get a Peaceful Board - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 LeetCode 3189 – Minimum Moves to Get a Peaceful Board  
**Language‑agnostic solution + Java / Python / C++ implementations**  
**SEO‑optimized blog article to help you land that next interview**  

---

### 1️⃣ Problem Recap  

You’re given an `n × n` chessboard and `n` rooks that are already on the board.  
Each rook is represented by its coordinates `[xᵢ, yᵢ]`.  
In one move a rook can be moved **one cell** either vertically or horizontally (no diagonal moves).  

**Goal** – Move the rooks until the board is *peaceful*: exactly one rook in every row and every column.  
Return the **minimum number of moves** required.  

> **Constraints**  
> * `1 ≤ n ≤ 500`  
> * `0 ≤ xᵢ, yᵢ < n`  
> * No two rooks share the same cell initially  

---

### 2️⃣ Intuition – Why Sorting Works  

Think about the final peaceful board: the rook in row `i` must end up somewhere in **column `i`** (after a permutation).  
The order of the rooks in the final board matters *only* for the distance they travel **in that dimension**.

- **Rows** – The rook that starts the *k‑th* farthest south (largest `x`) will inevitably be forced to the bottom of the board, the next one above it, and so on.  
  This is exactly what a stable sort does.  
  After sorting by `x`, the rook that ends up in the *i‑th* sorted position will have to move to row `i`.  
  The minimum number of vertical steps for that rook is `|xᵢ – i|`.

- **Columns** – The same logic holds for the horizontal direction.  
  After sorting by `y`, the rook that ends up in the *i‑th* sorted position will have to move to column `i`.  
  The minimum number of horizontal steps is `|yᵢ – i|`.

Because vertical and horizontal moves are independent (the board is a grid), the **total** minimal moves is just the sum of both contributions.

> **Why is this optimal?**  
> The sorting strategy is a classic *greedy* argument:  
> 1. **Row phase** – Place the rook that is currently the farthest south into the last row, the next farthest into the second‑last row, …  
> 2. **Column phase** – Do the same with the columns.  
> This produces a *matching* between initial rows and target rows (and columns) that minimizes the sum of absolute differences – a well‑known property of the 1‑dimensional “earth‑mover” (transport) problem.  
> Hence, no other arrangement can yield fewer moves.

---

### 3️⃣ Edge‑Case Checklist  

| Edge case | Why it matters | How our algorithm copes |
|-----------|----------------|------------------------|
| Rook already in the correct row but wrong column | Column phase must still move it | Sorting by `y` still yields `|yᵢ – i| = 0` for the correct target column |
| Rook starts at the corner `(0,0)` | Might need many moves in both dimensions | Each dimension is handled independently |
| `n = 1` | Zero moves | Sorting returns an empty loop → 0 |

---

### 4️⃣ Complexity  

| Step | Operation | Time | Space |
|------|-----------|------|-------|
| Sort by rows | `O(n log n)` | `O(n log n)` | `O(1)` (in‑place) |
| Sum vertical moves | `O(n)` | `O(1)` | `O(1)` |
| Sort by columns | `O(n log n)` | `O(n log n)` | `O(1)` |
| Sum horizontal moves | `O(n)` | `O(1)` | `O(1)` |
| **Total** | **`O(n log n)`** | **`O(1)`** |

With `n ≤ 500`, this easily fits into the limits.

---

### 5️⃣ Code Implementation  

> **Note:**  
> *All three snippets are function‑only (no I/O).  
> *They can be pasted straight into the LeetCode “Run Code” pane.*  

---

#### 5.1️⃣ Java  

```java
import java.util.Arrays;

class Solution {
    public int minMoves(int[][] rooks) {
        int n = rooks.length;

        // ----- Row phase -----
        Arrays.sort(rooks, (a, b) -> Integer.compare(a[0], b[0]));
        int rowMoves = 0;
        for (int i = 0; i < n; ++i) {
            rowMoves += Math.abs(rooks[i][0] - i);
        }

        // ----- Column phase -----
        Arrays.sort(rooks, (a, b) -> Integer.compare(a[1], b[1]));
        int colMoves = 0;
        for (int i = 0; i < n; ++i) {
            colMoves += Math.abs(rooks[i][1] - i);
        }

        return rowMoves + colMoves;
    }
}
```

---

#### 5.2️⃣ Python 3  

```python
from typing import List

class Solution:
    def minMoves(self, rooks: List[List[int]]) -> int:
        n = len(rooks)

        # Row phase
        rooks.sort(key=lambda r: r[0])
        row_moves = sum(abs(r[0] - i) for i, r in enumerate(rooks))

        # Column phase
        rooks.sort(key=lambda r: r[1])
        col_moves = sum(abs(r[1] - i) for i, r in enumerate(rooks))

        return row_moves + col_moves
```

---

#### 5.3️⃣ C++ 17  

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minMoves(vector<vector<int>>& rooks) {
        int n = rooks.size();

        // Row phase
        sort(rooks.begin(), rooks.end(),
             [](const vector<int>& a, const vector<int>& b){ return a[0] < b[0]; });
        int rowMoves = 0;
        for (int i = 0; i < n; ++i)
            rowMoves += abs(rooks[i][0] - i);

        // Column phase
        sort(rooks.begin(), rooks.end(),
             [](const vector<int>& a, const vector<int>& b){ return a[1] < b[1]; });
        int colMoves = 0;
        for (int i = 0; i < n; ++i)
            colMoves += abs(rooks[i][1] - i);

        return rowMoves + colMoves;
    }
};
```

---

## 📝 Blog Article – SEO‑Ready, Interview‑Ready, Job‑Ready  

> **Title:**  
> *Mastering LeetCode 3189: Minimum Moves to Get a Peaceful Board – Algorithm, Code, and Interview Tips*  

> **Meta‑Description:**  
> *Learn the greedy sorting strategy for LeetCode 3189, see Java/Python/C++ code, and get interview‑ready insights to ace your next software‑engineering job.*  

---

### 📚 1. Problem Overview  

- **Definition of “peaceful”** – one rook per row & column.  
- **Goal** – *minimum* number of unit moves.  
- Constraints make brute‑force infeasible (`O(n!)` permutations).  

---

### 2.  💡 Key Insight: Sorting = Minimal Distance  

| Dimension | Operation | Why it gives the minimal cost |
|-----------|-----------|--------------------------------|
| Row | Sort rooks by `x` (row index) | After sorting, the rook that should end in row `i` is already the one that needs the fewest vertical moves. |
| Column | Sort rooks by `y` (column index) | Symmetric to rows; horizontal moves are independent of vertical moves. |

**Proof sketch**

1. *One‑dimensional transport problem*:  
   Given positions `p₁ … pₙ` and target positions `0 … n‑1`, the minimal total distance is `∑|pᵢ – sorted[i]|`.  
   This is a classic result: the optimal matching is obtained by sorting both lists.  

2. *Independence of dimensions*:  
   Each move changes exactly one coordinate.  
   Therefore, the vertical cost depends only on rows, the horizontal cost only on columns.  
   The total cost is the sum of both optimal 1‑D costs.  

---

### 3.  👌 What’s Good About This Approach  

| Aspect | Benefit |
|--------|---------|
| **Simplicity** | Two sorts + linear scans – easy to code, easy to explain. |
| **Deterministic** | No backtracking or DP – guarantees optimality. |
| **Fast** | `O(n log n)` time, `O(1)` auxiliary space – perfect for `n ≤ 500`. |
| **Adaptable** | Works for any number of rooks on a grid where moves are Manhattan‑based. |

---

### 4.  ⚠️ Common Pitfalls (What to Avoid)  

| Pitfall | Why it breaks the solution |
|---------|----------------------------|
| **Assuming the rook that starts farthest needs the most moves** | It’s the *relative* order that matters, not absolute distance. |
| **Mixing row and column sorting in one loop** | You must perform the row sort **first**, compute the cost, *then* sort by columns. |
| **Using `HashMap` to remember original indices** | Unnecessary overhead; sorting is cleaner and faster. |
| **Ignoring that the board size equals the number of rooks** | If `m ≠ n`, the problem changes (assignment problem). |

---

### 5.  🎨 The “Good‑Bad‑Ugly” Analysis  

| Stage | Good | Bad | Ugly |
|-------|------|-----|------|
| **Idea** | Greedy sorting is elegant. | Some candidates copy‑paste without understanding. | Using an NP‑hard assignment algorithm when not needed. |
| **Code** | Clean, 3‑line loops in each language. | Over‑commenting can make the code bulky. | Manual `abs` macros, forgetting `#include <bits/stdc++.h>` in C++. |
| **Explanation** | You can explain in 5 minutes. | Forgetting to mention independence of dimensions. | Trying to prove DP optimality where sorting suffices. |

---

### 6.  🚀 Interview Tips: How to Ace the Discussion  

1. **Start with a diagram** – draw a grid, place rooks, show the sorting process.  
2. **Explain the 1‑D transport proof** – you can reference the “Earth Mover’s Distance” or “matching on a line”.  
3. **Highlight independence** – vertical & horizontal moves are orthogonal.  
4. **Answer “what if” questions** – show you know when this method applies.  

> *Example Q:* “What if a rook could move diagonally?”  
> **A:** Then the Manhattan distance is no longer separable; you’d need a different strategy.

---

### 6.  🚀 Final Takeaway  

- Sorting by rows and columns, summing absolute differences, is the *canonical* solution for LeetCode 3189.  
- Implementations in Java, Python, and C++ are straightforward and within constraints.  
- Understanding the underlying transport problem ensures you’ll never need to resort to more complex algorithms.  
- Use the “Good‑Bad‑Ugly” analysis to impress interviewers: show you know the *why* behind the code.

---

### 🎯  Call‑to‑Action  

- **Practice** the idea on similar grid‑move problems (e.g., “assign points to target grid”).  
- **Teach** the concept to a friend – teaching reinforces mastery.  
- **Apply** the same logic to other LeetCode problems (e.g., 1‑D “Minimum Sum of Distances” or “Moving Stones”).  

Good luck on your next interview! 🚀

--- 

That’s it – you now have the algorithm, the code, and a polished article that showcases your deep understanding and interview readiness. Happy coding!