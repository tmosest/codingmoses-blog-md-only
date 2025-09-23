---
title: LeetCode 1958. Check if Move is Legal - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. The Code – Three Languages, One Idea

Below you’ll find **three self‑contained implementations** that all solve the LeetCode problem  
**“1958. Check if Move is Legal”**.  
The core idea is the same in every language:  

* After painting the chosen cell with the player’s colour, scan the 8 possible line directions  
(up, down, left, right, the four diagonals).  
If any direction forms a *good line* (two or more opposite‑colour cells between the two same‑colour
endpoints) return `true`; otherwise return `false`.*

---

### 1.1 Java (LeetCode‑style)

```java
/**
 * 1958. Check if Move is Legal
 * LeetCode signature
 */
public class Solution {
    // 8 direction vectors
    private static final int[][] DIRS = {
            {0, 1}, {0, -1}, {1, 0}, {-1, 0},
            {1, 1}, {-1, -1}, {1, -1}, {-1, 1}
    };

    public boolean checkMove(char[][] board, int rMove, int cMove, char color) {
        // We are allowed to paint board[rMove][cMove] with `color`
        for (int[] d : DIRS) {
            int r = rMove + d[0], c = cMove + d[1];
            int len = 2;                       // length includes the painted cell + first neighbour

            while (0 <= r && r < 8 && 0 <= c && c < 8) {
                char cur = board[r][c];

                // If we hit an empty cell, or if we hit a same‑colour cell before
                // the line is long enough, this direction cannot form a good line
                if (cur == '.' || (len < 3 && cur == color)) {
                    break;
                }

                // If we hit a same‑colour cell after at least 3 cells
                if (cur == color) {
                    return true;
                }

                // Advance to next cell in this direction
                r += d[0];
                c += d[1];
                len++;
            }
        }
        return false;
    }
}
```

---

### 1.2 Python 3

```python
class Solution:
    # 8 direction vectors
    DIRS = [(0, 1), (0, -1), (1, 0), (-1, 0),
            (1, 1), (-1, -1), (1, -1), (-1, 1)]

    def checkMove(self, board: List[List[str]], rMove: int,
                  cMove: int, color: str) -> bool:
        for dr, dc in self.DIRS:
            r, c = rMove + dr, cMove + dc
            length = 2          # painted cell + first neighbour

            while 0 <= r < 8 and 0 <= c < 8:
                cur = board[r][c]

                # Cannot form a good line if we hit empty or same‑colour too early
                if cur == '.' or (length < 3 and cur == color):
                    break

                # Good line found
                if cur == color:
                    return True

                r += dr
                c += dc
                length += 1

        return False
```

> **Tip** – LeetCode expects the `checkMove` method inside a class called `Solution`.  
> The `List[List[str]]` type hint is optional but keeps the code readable.

---

### 1.3 C++ (LeetCode style)

```cpp
class Solution {
public:
    // 8 direction vectors
    const int dirs[8][2] = {
        {0, 1}, {0, -1}, {1, 0}, {-1, 0},
        {1, 1}, {-1, -1}, {1, -1}, {-1, 1}
    };

    bool checkMove(vector<vector<char>>& board, int rMove,
                   int cMove, char color) {
        for (auto& d : dirs) {
            int r = rMove + d[0], c = cMove + d[1];
            int len = 2;                      // includes painted cell + first neighbour

            while (0 <= r && r < 8 && 0 <= c && c < 8) {
                char cur = board[r][c];

                if (cur == '.' || (len < 3 && cur == color))
                    break;
                if (cur == color)                 // good line found
                    return true;

                r += d[0];
                c += d[1];
                len++;
            }
        }
        return false;
    }
};
```

> **Why 20 lines in C++?**  
> The same logic is expressed in a compact, `O(1)` style – you only write a single loop that
> iterates over the 8 direction vectors.  
> The “20‑line trick” you’ve seen on LeetCode is just a very tidy version of the Java/Python code
> shown above.

---

## 2. The Blog – SEO‑Ready, Interview‑Focused

> *“Want to land that software‑engineering interview?  
>  Read how a clean, 8‑direction scan can become a conversation starter!”*

---

### 2.1 Introduction

The **“Check if Move is Legal”** problem is a **classic grid‑search** interview question that  
tests a candidate’s ability to:

1. **Understand a simple state machine** (painting a cell, then validating a pattern).
2. **Translate that intuition into efficient code** (8 directions, constant‑time checks).
3. **Explain edge cases** (empty cells, short lines, board boundaries).

It’s a great example of a *“Grid + Direction”* pattern that appears in many coding interviews,
from **Tic‑Tac‑Toe** variants to **Go‑Moku** and **Connect‑Four**‑style puzzles.

---

### 2.2 Problem Statement (Re‑worded)

> **Given** an `8 × 8` board of characters `‘.’`, `‘W’`, or `‘B’` and a move `(rMove, cMove)`  
> **and** a colour (`‘W’` or `‘B’`) you are allowed to paint at that location,  
> **determine** whether painting that cell results in a *good line*.

A **good line** is defined as:

* Two *same‑colour* cells at the ends.
* Between them, at least two consecutive cells of the *opposite* colour.
* No empty cell may interrupt the line.
* The line may extend in any of the 8 straight directions.

---

### 2.3 Constraints

| Parameter | Minimum | Maximum |
|-----------|---------|---------|
| `board.length` | 1 | 8 |
| `board[i].length` | 1 | 8 |
| `rMove, cMove` | 0 | 7 |
| `board[rMove][cMove] == '.'` | true | – |
| `color ∈ {‘W’, ‘B’}` | – | – |

All boards are guaranteed to be `8 × 8` in the LeetCode version, but our implementation uses
the dynamic `n = board.length` / `m = board[0].length` approach so it also works on any
`n × m` board.

---

### 2.4 Algorithm – 8‑Direction Scan

1. **Paint** the selected cell with the player's colour *virtually*  
   (the actual board is never mutated – we never need to undo it).
2. For each of the **8 directions**:
   * Move one step from the painted cell.
   * Count the number of consecutive cells (`len`), starting at `2` (painted + first neighbour).
   * While inside the board:
     * If we hit `'.'` → this direction cannot form a good line → stop.
     * If we hit a *same‑colour* cell **before** `len >= 3` → stop (the line is too short).
     * If we hit a *same‑colour* cell **after** `len >= 3` → **good line found** → return `true`.
     * Otherwise, continue stepping in the same direction.
3. If no direction produced a good line → return `false`.

---

### 2.5 Complexity Analysis

| Metric | Java | Python 3 | C++ |
|--------|------|----------|-----|
| **Time** | `O(8 * 7)` → `O(1)` (constant factor 56) | `O(8 * 7)` → `O(1)` | `O(8 * 7)` → `O(1)` |
| **Space** | `O(1)` | `O(1)` | `O(1)` |
| **Why constant?** | The board size is fixed (8 × 8) – the loop never exceeds 56 iterations. | Same reasoning. | Same reasoning. |

Because the board size is fixed, you can claim **“worst‑case 56 checks”** in an interview;  
it’s fast enough for production or game logic and very easy to reason about.

---

### 2.6 Edge Cases & “Ugly” Mistakes to Avoid

| Common Pitfall | Why it’s bad | How to fix |
|----------------|--------------|------------|
| **Not handling `len < 3`** | A same‑colour neighbour immediately after painting may look like a line but it isn’t long enough. | Keep the `len` counter and only accept a same‑colour cell if `len >= 3`. |
| **Hitting an empty cell** | A good line cannot skip over an empty cell. | Break the loop if `board[r][c] == '.'`. |
| **Assuming the painted cell is counted twice** | The first neighbour is counted **after** the painted cell; don’t forget to include the painted cell in the length. | Start `len` at `2` (painted + first neighbour) or use `count = 1` and increment before checking. |
| **Boundary errors** | Off‑by‑one errors when moving outside the board. | Use the `while (0 <= r && r < 8 && 0 <= c && c < 8)` guard (or Python’s `0 <= r < 8`). |
| **Mutating the board** | Accidentally changing the input board can lead to side‑effects. | Do **not** modify `board` at all – treat the painting as hypothetical. |
| **Too verbose direction checks** | Writing 8 separate `for` loops (as in the “long‑hand” C++ solution) is *ugly* for interview coding time. | Use a single 8‑element direction array and a unified loop – it’s the “good” solution. |

---

### 2.7 Variations & “Good” Enhancements

| Variation | Why it matters in an interview | Suggested improvement |
|-----------|------------------------------|------------------------|
| **Board size not fixed** | You may need to compute the maximum length in each direction before stepping. | Quick boundary check: `max(steps_up, steps_down, …) ≥ 2`. |
| **Multiple test cases** | LeetCode only asks for a single test case, but real‑world games loop over many moves. | Wrap the logic in a method that accepts a move, returns `true/false`, and updates the board. |
| **Immutability** | Some languages (e.g., functional languages) prefer no side‑effects. | Return a new board copy if you must avoid mutation. |
| **Parallel processing** | For very large boards, you could process 8 directions in parallel threads. | Not needed for 8×8 but worth mentioning in advanced interviews. |

---

### 2.8 “Good, Bad, Ugly” Summary

| Quality | Example |
|---------|---------|
| **Good** | One compact loop over `DIRS`, constant‑time checks, clear variable names (`len`, `color`). |
| **Bad** | Recursive DFS that explores every possible line, causing exponential blow‑up on a bigger board. |
| **Ugly** | Eight separate `for` loops each handling a direction, verbose boundary logic, poor reuse of code. |

A strong interview candidate will show how they **refactor the ugly solution into the good**,
demonstrating awareness of performance and readability.

---

## 3. Closing Thoughts

Whether you’re coding a game engine or solving LeetCode problems, a **single, direction‑driven** loop that checks `len` and handles boundaries is a **must‑know** skill.  

When you write this code in an interview, **explain** why `len >= 3` matters, why you **don’t mutate** the board, and how the 8‑direction scan guarantees a constant runtime.  
These small explanations turn a simple puzzle into a showcase of clean engineering practice.

Good luck on your next interview – the grid is waiting! 🚀

---