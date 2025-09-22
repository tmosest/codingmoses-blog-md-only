---
title: LeetCode 1301. Number of Paths with Max Score - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 📌 LeetCode 1301 – **Number of Paths with Max Score**  
**Difficulty:** Hard | **Tags:** DP, BFS, Grid, Modulo, Top‑Down

---

### 1️⃣ Problem Summary  

You are given an `n × n` board (`2 ≤ n ≤ 100`).  
Each cell contains one of the following characters:

| Character | Meaning |
|-----------|---------|
| `S` | Start (bottom‑right corner) |
| `E` | End (top‑left corner) |
| `1`–`9` | Numeric value that can be collected |
| `X` | Obstacle – cannot be entered |

You may move **up**, **left**, or **up‑left** (diagonally) only if the target cell is not an obstacle.  
Your goal is to:

1. **Maximize** the sum of digits collected along a path from `S` to `E`.
2. Count how many distinct paths achieve this maximum score.  
   The count must be returned modulo `1 000 000 007`.

If no path exists, return `[0, 0]`.

---

### 2️⃣ High‑Level Solution Idea  

Because every allowed move decreases the row and/or column index, the graph is **acyclic** – you never go back to a cell you have already visited.  
This property lets us use a simple **dynamic‑programming** approach that processes the board **from the start (bottom‑right)** towards the goal (top‑left).

For every cell `(i, j)` we store two values:

| `maxScore[i][j]` | Largest sum that can be achieved when we *arrive* at `(i, j)`. |
|------------------|---------------------------------------------------------------|
| `ways[i][j]`     | Number of ways to obtain that `maxScore[i][j]`.               |

We initialise the start cell with score `0` and `1` way, then iterate all cells in reverse row‑by‑row order.  
For each cell we propagate its score to the three possible “next” cells (up, left, up‑left).  
When propagating we

* **Replace** the destination cell’s score if the new score is larger.
* **Add** to the destination cell’s ways if the new score equals the current maximum.

Because every transition is monotone (only “upwards”), we never need to revisit a cell after it has been processed.

---

### 3️⃣ Algorithm (Pseudo‑Code)

```
MOD = 1_000_000_007
n = board.length
maxScore[n][n]  = -∞
ways[n][n]      = 0

// Start at bottom‑right
maxScore[n-1][n-1] = 0
ways[n-1][n-1]     = 1

for i from n-1 down to 0:
    for j from n-1 down to 0:
        if maxScore[i][j] == -∞: continue   // unreachable
        for each (di, dj) in {( -1, 0), (0, -1), (-1, -1)}:
            ni = i + di;  nj = j + dj
            if ni < 0 or nj < 0: continue
            if board[ni][nj] == 'X': continue
            add = 0
            if board[ni][nj] is digit:
                add = int(board[ni][nj])
            newScore = maxScore[i][j] + add
            if newScore > maxScore[ni][nj]:
                maxScore[ni][nj] = newScore
                ways[ni][nj] = ways[i][j]
            else if newScore == maxScore[ni][nj]:
                ways[ni][nj] = (ways[ni][nj] + ways[i][j]) % MOD

answer = [maxScore[0][0], ways[0][0]]
if answer[0] == -∞: answer = [0, 0]
return answer
```

**Complexity**

| Step | Time | Space |
|------|------|-------|
| DP | `O(n²)` | `O(n²)` |

With `n ≤ 100` this easily satisfies the limits.

---

### 4️⃣ Reference Implementations  

Below are clean, idiomatic solutions for **Java, Python, and C++**.

---

#### 4.1 Java 17  

```java
import java.util.*;

public class Solution {
    private static final int MOD = 1_000_000_007;

    public int[] pathsWithMaxScore(List<String> board) {
        int n = board.size();
        char[][] grid = new char[n][n];
        for (int i = 0; i < n; i++)
            grid[i] = board.get(i).toCharArray();

        int[][] maxScore = new int[n][n];
        int[][] ways     = new int[n][n];
        for (int[] row : maxScore) Arrays.fill(row, Integer.MIN_VALUE);

        // start cell (bottom‑right)
        maxScore[n - 1][n - 1] = 0;
        ways[n - 1][n - 1]     = 1;

        int[] dx = {-1, 0, -1};
        int[] dy = {0, -1, -1};

        for (int i = n - 1; i >= 0; i--) {
            for (int j = n - 1; j >= 0; j--) {
                if (maxScore[i][j] == Integer.MIN_VALUE) continue; // unreachable
                for (int dir = 0; dir < 3; dir++) {
                    int ni = i + dx[dir];
                    int nj = j + dy[dir];
                    if (ni < 0 || nj < 0) continue;
                    char ch = grid[ni][nj];
                    if (ch == 'X') continue;

                    int add = (ch >= '1' && ch <= '9') ? ch - '0' : 0;
                    int newScore = maxScore[i][j] + add;

                    if (newScore > maxScore[ni][nj]) {
                        maxScore[ni][nj] = newScore;
                        ways[ni][nj]     = ways[i][j];
                    } else if (newScore == maxScore[ni][nj]) {
                        ways[ni][nj] = (ways[ni][nj] + ways[i][j]) % MOD;
                    }
                }
            }
        }

        int finalScore = maxScore[0][0];
        int finalWays  = ways[0][0];
        if (finalScore == Integer.MIN_VALUE) return new int[]{0, 0};
        return new int[]{finalScore, finalWays};
    }
}
```

---

#### 4.2 Python 3.10+  

```python
from typing import List

MOD = 1_000_000_007

class Solution:
    def pathsWithMaxScore(self, board: List[str]) -> List[int]:
        n = len(board)
        grid = [list(row) for row in board]

        max_score = [[-10**9] * n for _ in range(n)]
        ways     = [[0] * n for _ in range(n)]

        max_score[n-1][n-1] = 0
        ways[n-1][n-1] = 1

        dirs = [(-1, 0), (0, -1), (-1, -1)]

        for i in range(n-1, -1, -1):
            for j in range(n-1, -1, -1):
                if max_score[i][j] == -10**9:
                    continue
                for di, dj in dirs:
                    ni, nj = i + di, j + dj
                    if ni < 0 or nj < 0:
                        continue
                    ch = grid[ni][nj]
                    if ch == 'X':
                        continue
                    add = int(ch) if ch.isdigit() else 0
                    new = max_score[i][j] + add

                    if new > max_score[ni][nj]:
                        max_score[ni][nj] = new
                        ways[ni][nj] = ways[i][j]
                    elif new == max_score[ni][nj]:
                        ways[ni][nj] = (ways[ni][nj] + ways[i][j]) % MOD

        if max_score[0][0] == -10**9:
            return [0, 0]
        return [max_score[0][0], ways[0][0]]
```

---

#### 4.3 C++ 17  

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    const int MOD = 1'000'000'007;

    vector<int> pathsWithMaxScore(vector<string>& board) {
        int n = board.size();
        vector<vector<int>> score(n, vector<int>(n, INT_MIN));
        vector<vector<int>> ways(n, vector<int>(n, 0));

        // bottom‑right cell
        score[n-1][n-1] = 0;
        ways[n-1][n-1]  = 1;

        const int dx[3] = {-1, 0, -1};
        const int dy[3] = { 0,-1,-1};

        for (int i = n-1; i >= 0; --i) {
            for (int j = n-1; j >= 0; --j) {
                if (score[i][j] == INT_MIN) continue;          // unreachable
                for (int dir = 0; dir < 3; ++dir) {
                    int ni = i + dx[dir], nj = j + dy[dir];
                    if (ni < 0 || nj < 0) continue;
                    char ch = board[ni][nj];
                    if (ch == 'X') continue;

                    int add = (ch >= '1' && ch <= '9') ? ch - '0' : 0;
                    int newScore = score[i][j] + add;

                    if (newScore > score[ni][nj]) {
                        score[ni][nj] = newScore;
                        ways[ni][nj] = ways[i][j];
                    } else if (newScore == score[ni][nj]) {
                        ways[ni][nj] = (ways[ni][nj] + ways[i][j]) % MOD;
                    }
                }
            }
        }

        if (score[0][0] == INT_MIN) return {0, 0};
        return {score[0][0], ways[0][0]};
    }
};
```

---

### 5️⃣ “Good‑and‑Bad” Insight – The **Good‑Path** vs. **Bad‑Path**

| | Good Path | Bad Path |
|---|---|---|
| **Complexity** | `O(n²)` | Requires top‑down recursion + memoisation → still `O(n²)` but heavier |
| **Implementation Effort** | 30 min | 30 min | 30 min |
| **Memory Footprint** | `O(n²)` | `O(n²)` | `O(n²)` |
| **Readability** | Very high | Very high | Very high |
| **Why it works** | Graph is a DAG – no cycles → linear DP | Same reasoning | Same reasoning |

> **Bottom‑Line:** A single pass DP is the “good path” that wins on time‑and‑memory.

---

### 6️⃣ The Good‑Path vs. The Bad‑Path (Common Mistakes)

| Mistake | Why It Happens | How to Fix |
|---------|----------------|------------|
| **1️⃣ Adding the start cell’s value** | `S` is *not* a digit – it shouldn’t be counted. | Treat `S` as score `0` only. |
| **2️⃣ Forgetting to skip obstacles** | Path may “step” onto an `X` cell and crash the program. | Explicit `if (ch == 'X') continue;` |
| **3️⃣ Using `int` for scores on a 32‑bit board** | The maximum possible score is `9 × (n²‑2)` – still fits in `int`, but when you use `Integer.MIN_VALUE` as “unreachable” you must be careful not to overflow. | Use a sentinel (`-INF` or `-10⁹`) that is far below any real score. |
| **4️⃣ Modulo on every addition** | Only `ways` needs modulo. Scores do not. | Keep scores as `int`/`long`; apply `% MOD` only on `ways`. |
| **5️⃣ Not checking reachability** | Returning `[maxScore[0][0], ways[0][0]]` when the goal is unreachable yields a garbage score. | After the DP loop, if `maxScore[0][0]` is still `-INF`, return `[0, 0]`. |

---

### 7️⃣ Testing Your Own Board  

```python
# Python – quick sanity test
if __name__ == "__main__":
    sol = Solution()
    board = [
        "S123",
        "X1X1",
        "2X2E",
        "121X"
    ]
    print(sol.pathsWithMaxScore(board))   # ➜ [max_score, ways]
```

Feel free to tweak the board and watch how the algorithm behaves.

---

## 🎯 Why This Matters for Your Interview Prep

| ✅ | Benefit |
|----|---------|
| **Handles Cycles Gracefully** | The board is a DAG, so a single DP sweep is enough. |
| **Clear State Definition** | `maxScore` + `ways` is a textbook DP pair. |
| **Modular Counting** | The modulo trick is a staple in grid‑DP problems. |
| **Easy to Translate** | The same pattern works in *any* language (Java, Python, C++). |

---

## 🔍 Search‑Friendly Summary  

* LeetCode 1301 – Number of Paths with Max Score  
* Acyclic grid DP – `O(n²)` time, `O(n²)` space  
* Two‑dimensional state: `maxScore` & `ways` (mod `1e9+7`)  
* Reference code: **Java, Python, C++**  

---

### 📚 Further Reading  

| Topic | Link |
|-------|------|
| Grid DP Patterns | https://leetcode.com/explore/learn/card/advanced-dynamic-programming/ |
| Modular Arithmetic in DP | https://cp-algorithms.com/algebra/modular-arithmetic.html |
| Top‑down vs. Bottom‑up DP | https://www.geeksforgeeks.org/dynamic-programming/ |

Happy coding, and may your scores always be *maximum*! 🚀