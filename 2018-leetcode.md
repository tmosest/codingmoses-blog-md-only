---
title: LeetCode 2018. Check if Word Can Be Placed In Crossword - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ 1.  LeetCode 2018 – *Check if Word Can Be Placed In Crossword*  
### 📌  Problem Summary  
Given an **m × n** crossword board with  

| Character | Meaning |
|-----------|---------|
| `#` | blocked cell (cannot be used) |
| `' '` | empty cell (any letter may be placed) |
| `'a'–'z'` | already solved letter |

and a word `word`, determine if the word can be placed **horizontally** (left‑to‑right or right‑to‑left) or **vertically** (top‑to‑bottom or bottom‑to‑top) following these rules:

1. The word may not overlap a `#`.
2. Each letter must match the board letter or be placed on an empty space.
3. The cell immediately **before** and **after** the word in the direction of placement must be a `#` or outside the board – i.e. the word cannot be adjacent to any other letter or empty cell.

Return `true` if such a placement exists, otherwise `false`.

### 🧩  High‑level Idea  
1. Iterate over every cell that is **not** a `#`.  
2. For each cell, try the four possible directions.  
3. A direction is valid if:
   * The “boundary cells” (one step before/after the word) satisfy rule 3.  
   * All letters of `word` fit inside the board, match existing letters or empty cells, and never run into a `#` or the board edge before the word finishes.  

The algorithm is *O(m · n · |word|)* – fast enough for LeetCode and perfect for interview discussions.

Below you’ll find clean, fully‑commented implementations in **Java**, **Python** and **C++**. All three use the same helper `canPlace` that accepts a direction vector `(dx, dy)`.

---

## 📚 2.  Java Implementation  
```java
// File: Solution.java
// LeetCode 2018 – Check if Word Can Be Placed In Crossword
// Java 17 (works on Java 8+)

public class Solution {

    // four cardinal directions (dx, dy)
    private static final int[][] DIRS = {
            {0, 1},   // right
            {0, -1},  // left
            {1, 0},   // down
            {-1, 0}   // up
    };

    public boolean placeWordInCrossword(char[][] board, String word) {
        int m = board.length, n = board[0].length;

        for (int r = 0; r < m; ++r) {
            for (int c = 0; c < n; ++c) {
                if (board[r][c] == '#') continue;           // blocked
                for (int[] d : DIRS) {
                    if (canPlace(board, word, r, c, d[0], d[1])) {
                        return true;
                    }
                }
            }
        }
        return false;
    }

    /** Helper that checks if the word can be placed starting at (r,c)
     *  and moving in direction (dx,dy). */
    private boolean canPlace(char[][] board, String word,
                             int r, int c, int dx, int dy) {
        int m = board.length, n = board[0].length;
        int len = word.length();

        // 1️⃣  Check the cell just before the word (rule 3)
        int beforeR = r - dx, beforeC = c - dy;
        if (inBounds(beforeR, beforeC, m, n) && board[beforeR][beforeC] != '#')
            return false;

        // 2️⃣  Check the cell just after the word
        int afterR = r + dx * len, afterC = c + dy * len;
        if (inBounds(afterR, afterC, m, n) && board[afterR][afterC] != '#')
            return false;

        // 3️⃣  Iterate over the word letters
        for (int i = 0; i < len; ++i) {
            int nr = r + dx * i;
            int nc = c + dy * i;

            // out of bounds → word longer than the free space
            if (!inBounds(nr, nc, m, n))
                return false;

            char boardChar = board[nr][nc];
            if (boardChar != ' ' && boardChar != word.charAt(i))
                return false;          // mismatch
        }
        return true;
    }

    private boolean inBounds(int r, int c, int m, int n) {
        return r >= 0 && r < m && c >= 0 && c < n;
    }
}
```

**Complexity**  
*Time*: `O(m · n · |word|)` – each cell is examined in 4 directions.  
*Space*: `O(1)` – only a few local variables.

---

## 🐍 2.  Python Implementation (Python 3)  
```python
# File: solution.py
# LeetCode 2018 – Check if Word Can Be Placed In Crossword
# Python 3

class Solution:
    # Direction vectors: right, left, down, up
    DIRS = [(0, 1), (0, -1), (1, 0), (-1, 0)]

    def placeWordInCrossword(self, board: [ [str] ], word: str) -> bool:
        m, n = len(board), len(board[0])

        for r in range(m):
            for c in range(n):
                if board[r][c] == '#':
                    continue
                for dr, dc in self.DIRS:
                    if self.can_place(board, word, r, c, dr, dc, m, n):
                        return True
        return False

    def can_place(self, board, word, r, c, dr, dc, m, n) -> bool:
        # cell before the word
        br, bc = r - dr, c - dc
        if 0 <= br < m and 0 <= bc < n and board[br][bc] != '#':
            return False

        # cell after the word
        ar, ac = r + dr * len(word), c + dc * len(word)
        if 0 <= ar < m and 0 <= ac < n and board[ar][ac] != '#':
            return False

        # check each letter
        for i, ch in enumerate(word):
            nr, nc = r + dr * i, c + dc * i
            if not (0 <= nr < m and 0 <= nc < n):
                return False
            cell = board[nr][nc]
            if cell != ' ' and cell != ch:
                return False
        return True
```

**Complexity** – same as Java: `O(m·n·|word|)` time, `O(1)` space.

---

## C++17 Implementation  
```cpp
// File: solution.cpp
// LeetCode 2018 – Check if Word Can Be Placed In Crossword
// C++17

class Solution {
public:
    // direction vectors: right, left, down, up
    const vector<pair<int,int>> dirs = {{0,1},{0,-1},{1,0},{-1,0}};

    bool placeWordInCrossword(vector<vector<char>>& board, string word) {
        int m = board.size(), n = board[0].size();

        for (int r = 0; r < m; ++r) {
            for (int c = 0; c < n; ++c) {
                if (board[r][c] == '#') continue;
                for (auto [dr, dc] : dirs) {
                    if (canPlace(board, word, r, c, dr, dc))
                        return true;
                }
            }
        }
        return false;
    }

private:
    bool inBounds(int r, int c, int m, int n) {
        return r >= 0 && r < m && c >= 0 && c < n;
    }

    bool canPlace(const vector<vector<char>>& board, const string& word,
                  int r, int c, int dr, int dc) {
        int m = board.size(), n = board[0].size(), L = word.size();

        // cell before the word
        int br = r - dr, bc = c - dc;
        if (inBounds(br, bc, m, n) && board[br][bc] != '#')
            return false;

        // cell after the word
        int ar = r + dr * L, ac = c + dc * L;
        if (inBounds(ar, ac, m, n) && board[ar][ac] != '#')
            return false;

        // check each letter
        for (int i = 0; i < L; ++i) {
            int nr = r + dr * i, nc = c + dc * i;
            if (!inBounds(nr, nc, m, n)) return false;
            char bc = board[nr][nc];
            if (bc != ' ' && bc != word[i]) return false;
        }
        return true;
    }
};
```

---

## 📖 2.  SEO‑Optimized Blog Post  
### The Good, The Bad, & The Ugly of Placing a Word in a Crossword (LeetCode 2018)

> **Keywords**: *LeetCode 2018*, *crossword puzzle algorithm*, *Java interview problem*, *Python interview*, *C++ interview coding*, *job interview preparation*, *algorithmic thinking*, *string manipulation*, *backtracking*, *DFS*, *coding interview tips*  

---

### 1️⃣  Introduction  
In many coding interviews, interviewers love to ask you to solve *grid‑based* problems – they’re great for testing your **algorithmic thinking**, **attention to detail**, and **time‑space optimisation**.  
**LeetCode 2018** – *Check if Word Can Be Placed In Crossword* is a perfect interview staple. It forces you to juggle:

* String matching
* Boundary‑condition checks
* Directional traversal

Below we dissect the *good*, *bad*, and *ugly* parts of this problem, show a clean solution, and give you interview‑ready talking points.

---

### 2️⃣  The “Good” – Why This Problem Is Interview‑Friendly  

| Aspect | Why It Helps |
|--------|--------------|
| **Simplicity of Input** | One board and one word → no recursion, no global state. |
| **Deterministic Traversal** | Four fixed directions. Candidates can reason about O(1) direction logic. |
| **Boundary Checks** | Teaches careful handling of *edge cases* (board edges, preceding/following cells). |
| **Clear Constraints** | `O(m · n · |word|)` is fast; you can show you’ve considered complexity. |
| **Multiple Languages** | You can comfortably translate the solution into Java, Python, or C++, demonstrating language flexibility. |

Because of these qualities, LeetCode 2018 is often used as a *warm‑up* question. Solving it gives interviewers confidence that you can handle **grid navigation** before moving to more complex dynamic programming or BFS/DFS problems.

---

### 3️⃣  The “Bad” – Common Pitfalls You Should Avoid  

| Mistake | Fix |
|---------|-----|
| **Over‑Complicating with Recursion** | The problem is *iterative* – recursion can introduce extra stack space and hidden bugs. |
| **Ignoring Boundary Cells** | Rule 3 (cell before/after the word) is easy to overlook; this leads to false positives. |
| **Treating Empty Cells as ‘#’** | A stray space (`' '`) in the board should be treated as *free*; many candidates mistakenly reject it. |
| **Hard‑coding Directions** | Writing four separate loops instead of a single direction array increases verbosity and invites copy‑paste errors. |

Interviewers often look for **clean code** that can be explained in under 5 minutes. By focusing on these pitfalls, you can anticipate the most common interview questions.

---

### 4️⃣  The “Ugly” – What Makes It Tricky to Explain

1. **The “Before/After” Check**  
   *It’s a single‑line condition, but the logic is subtle.*  
   You must reason: *“Does the cell one step before the first letter of the word block placement? And what about the cell just after the last letter?”*  
   If you skip this, you’ll get accepted answers that actually break rule 3.

2. **Directional Indexing**  
   Using `dx` and `dy` for directions can be confusing at first.  
   Many candidates write separate `right()`, `left()`, `up()`, `down()` methods—code duplication ensues.

3. **Off‑By‑One Errors**  
   Calculating the “after” cell as `r + dx * len(word)` is easy to mis‑compute by +1 or -1.  
   This small arithmetic mistake often leads to “wrong answer” feedback.

4. **Grid Boundary Checks**  
   Remember that the board is a *matrix of chars*; `board[r][c]` is a character, not a string.  
   In Java, `board[r][c] == '#` is *character* comparison; in Python it’s `board[r][c] == '#`.  
   Mixing these can produce subtle bugs.

---

### 5️⃣  Clean Interview‑Ready Solution (Summarised)

*Define the 4 cardinal direction vectors once.*  
*For each cell that’s not `#`, iterate over these directions.*  
*`canPlace` does three checks in order:  
1. Cell before the word,  
2. Cell after the word,  
3. Each letter of the word matches or is a space.*

```text
for each cell (r,c) not blocked
   for each direction (dx,dy)
      if boundary cells ok AND all letters fit
          → return true
return false
```

**Talking point**: *“I avoided recursion by iterating over the word length; that keeps memory usage constant.”*  

---

### 6️⃣  Interview Pointers

| Question | Suggested Response |
|----------|--------------------|
| *Why iterate over all cells?* | “Because the word can start from *any* free cell; we need to test all possibilities.” |
| *How do you check for boundary conflicts?* | “We look one step before the start and one step after the end; if those cells exist, they must be ‘#’.” |
| *What if the word is longer than the available space?* | “The loop will reach out of bounds; we immediately reject that direction.” |
| *Could you use BFS or DFS?* | “We could, but that’s unnecessary overhead; the problem is linear in direction, so a simple loop is optimal.” |
| *What’s the complexity?* | “O(m · n · |word|) time, constant extra space.” |

---

### 7️⃣  Takeaway for Job Interviews  

1. **Explain the grid** – “It’s a 2D character array; we treat `' '` as empty, `'#'` as blocked.”  
2. **Show direction abstraction** – “I use a fixed array of `(dx,dy)` vectors to avoid code duplication.”  
3. **Highlight boundary handling** – “Checking cells before/after the word is critical; a missing check will lead to wrong answers.”  
4. **Emphasise time‑space** – “The solution is linear in the board size times the word length, and uses only constant extra memory.”  

If you can walk through these points confidently, you’ll showcase your grasp of *grid traversal*, *string matching*, and *clean code practices*—exactly what hiring managers look for in coding interview candidates.

---

### 8️⃣  Conclusion  
LeetCode 2018 is a *tiny* but powerful window into your problem‑solving style. With the solutions above and the interview talking points from this post, you’re ready to impress recruiters on Java, Python or C++—and to ace any *crossword‑grid* question that comes your way.

Happy coding, and good luck on your next job interview! 🚀

---


--- 

### 🎉  Bonus: Quick Live‑Coding Checklist  
1. **Read Input** – board dimensions, word length.  
2. **Loop over cells** – skip `#`.  
3. **Loop over 4 directions** – call helper.  
4. **Helper**  
   * Check cell *before* the word.  
   * Check cell *after* the word.  
   * Iterate letters, match or accept space.  
5. **Return** true if any direction works.  
6. **Return** false otherwise.  

That’s all there is to it. ✨

--- 

Happy interviewing!