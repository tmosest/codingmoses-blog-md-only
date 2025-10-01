---
title: LeetCode 2174. Remove All Ones With Row and Column Flips II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap  

**LeetCode 2174 – Remove All Ones With Row and Column Flips II**  

> *You are given an `m × n` binary matrix `grid`.  
> In one operation you may choose a cell `(i, j)` with `grid[i][j] == 1` and set every cell in row `i` **and** every cell in column `j` to `0`.  
> Return the minimum number of operations required to turn every entry of `grid` into `0`.*

The grid is tiny: `1 ≤ m, n ≤ 15` and **`m × n ≤ 15`**.  
This makes a *bit‑mask* representation perfect – the whole matrix fits into a single integer.  
The classic solution is a **Breadth‑First Search** over all reachable bit‑mask states, using clever bit‑operations to delete an entire row or column in O(1).  

Below you’ll find concise, production‑ready code for **Java, Python, and C++** – all derived from the same BFS‑bitmask idea.  
After the code, a short, SEO‑friendly blog article explains the intuition, pitfalls, and what recruiters want to see.

---

## 2.  Reference Implementation

### 2.1  Java

```java
import java.util.*;

class Solution {
    // Pre‑computed column masks for every possible width (1 … 15).
    // columnMask[w][c] = mask that clears column c in a w‑wide grid.
    private static final int[][] COLUMN_MASK = new int[16][];

    static {
        for (int w = 1; w <= 15; w++) {
            int[] mask = new int[w];
            for (int c = 0; c < w; c++) {
                int m = 0;
                for (int r = 0; r < 15 / w + 1; r++) {     // maximum rows
                    m |= 1 << (r * w + c);
                }
                mask[c] = m;
            }
            COLUMN_MASK[w] = mask;
        }
    }

    public int removeOnes(int[][] grid) {
        int rows = grid.length, cols = grid[0].length;
        int state = 0;
        int rowMask = (1 << cols) - 1;                     // mask of a single row
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (grid[r][c] == 1) {
                    state |= 1 << (r * cols + c);
                }
            }
        }
        if (state == 0) return 0;

        Queue<Integer> q = new ArrayDeque<>();
        q.offer(state);
        int steps = 1;

        while (true) {
            int size = q.size();
            for (int s = 0; s < size; s++) {
                int cur = q.poll();
                for (int pos = 0; pos < rows * cols; pos++) {
                    if ((cur & (1 << pos)) != 0) {
                        int r = pos / cols, c = pos % cols;
                        int next = cur & ~((rowMask << (r * cols)) | COLUMN_MASK[cols][c]);
                        if (next == 0) return steps;
                        q.offer(next);
                    }
                }
            }
            steps++;
        }
    }
}
```

> **Why it works** –  
> `state` holds the entire matrix in binary form.  
> For every set bit we “flip” its row (`rowMask << (r*cols)`) and its column (`COLUMN_MASK[cols][c]`).  
> BFS guarantees the first time we reach `0` is the minimal number of flips.

---

### 2.2  Python 3

```python
from collections import deque
from typing import List

class Solution:
    # Pre‑compute column masks for each width (1‑15)
    _COL_MASK = [[] for _ in range(16)]
    for w in range(1, 16):
        for c in range(w):
            mask = 0
            r = 0
            while r * w + c < 15:        # maximum cells we ever need
                mask |= 1 << (r * w + c)
                r += 1
            _COL_MASK[w].append(mask)

    def removeOnes(self, grid: List[List[int]]) -> int:
        rows, cols = len(grid), len(grid[0])
        state = 0
        row_mask = (1 << cols) - 1
        for r in range(rows):
            for c in range(cols):
                if grid[r][c]:
                    state |= 1 << (r * cols + c)

        if state == 0:
            return 0

        q = deque([state])
        step = 1
        while True:
            for _ in range(len(q)):
                cur = q.popleft()
                for pos in range(rows * cols):
                    if cur & (1 << pos):
                        r, c = divmod(pos, cols)
                        next_state = cur & ~((row_mask << (r * cols)) | self._COL_MASK[cols][c])
                        if next_state == 0:
                            return step
                        q.append(next_state)
            step += 1
```

> **Tip** – Python’s integer type is unbounded, so we can freely shift bits beyond 32/64 bits without overflow.

---

### 2.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // column masks: colMask[cols][c] clears column c
    static const int COL_MASK[16][15];
    int removeOnes(vector<vector<int>>& grid) {
        int rows = grid.size(), cols = grid[0].size();
        int state = 0;
        int rowMask = (1 << cols) - 1;                     // all bits of a row
        for (int r = 0; r < rows; ++r)
            for (int c = 0; c < cols; ++c)
                if (grid[r][c]) state |= 1 << (r * cols + c);

        if (!state) return 0;

        queue<int> q;
        q.push(state);
        int steps = 1;

        while (true) {
            int sz = q.size();
            for (int i = 0; i < sz; ++i) {
                int cur = q.front(); q.pop();
                for (int pos = 0; pos < rows * cols; ++pos) {
                    if (cur & (1 << pos)) {
                        int r = pos / cols, c = pos % cols;
                        int nxt = cur & ~((rowMask << (r * cols)) | COL_MASK[cols][c]);
                        if (!nxt) return steps;
                        q.push(nxt);
                    }
                }
            }
            ++steps;
        }
    }
};

// Pre‑compute the masks once (static storage)
const int Solution::COL_MASK[16][15] = []{
    int arr[16][15] = {};
    for (int w = 1; w <= 15; ++w) {
        for (int c = 0; c < w; ++c) {
            int mask = 0;
            for (int r = 0; w * r + c < 15; ++r)
                mask |= 1 << (w * r + c);
            arr[w][c] = mask;
        }
    }
    return arr;
}();
```

> **C++ 17/20** – the static lambda initialises the masks at compile‑time, so no runtime overhead.

---

## 3.  Blog Article – “The Good, the Bad, and the Ugly of LeetCode 2174”

### 3.1  Headline (SEO‑friendly)

> **“LeetCode 2174 – Remove All Ones With Row and Column Flips II: BFS‑Bitmask Masterclass for Interview Success”**

### 3.2  Meta Description

> *Solve LeetCode 2174 with a BFS + bitmask approach in Java, Python, and C++. Learn the intuition, pitfalls, and how to ace the coding interview.*

### 3.3  Introduction (Hook)

> In a world where a **tiny 15‑cell matrix** can decide the fate of an interview, the “Remove All Ones” problem is a classic test of algorithmic flair.  
> Recruiters want you to *think fast*, *code cleanly*, and *optimize in one line*—exactly what the BFS‑bitmask trick delivers.  
> Below you’ll see the full solution in three languages, plus a quick guide to what makes this problem a shining example of interview skill.

---

### 3.4  The Good – Why the Problem is Great

1. **Compact State Space** – With *m × n ≤ 15*, a single 32‑bit integer encodes the whole board.  
2. **Exhaustive Search in 2ⁿ** – Breadth‑First Search guarantees the minimal number of flips, while bit‑operations keep each transition O(1).  
3. **Language‑agnostic** – The same idea works in Java, Python, and C++; a great talking point to demonstrate cross‑platform competence.  
4. **Real‑World Relevance** – The row/column wipe operation is a simplified model of *bit‑mask based pruning* used in network packet filtering, image processing, and game AI.

---

### 3.5  The Bad – Common Pitfalls

| Mistake | What Happens | How to Fix |
|---------|--------------|------------|
| **Using `int` on 15×15 grid (225 bits)** | Overflow, incorrect results. | Restrict to *rows × cols ≤ 15*; otherwise use `long long` or Python’s arbitrary‑precision ints. |
| **Redundant row/column clearing** | Generates duplicate states → longer queue. | Pre‑compute masks for each width; reuse them. |
| **Iterating over 225 positions in a 15‑cell mask** | Unnecessary checks, O(225 × BFS). | Loop only up to `rows*cols` positions. |
| **Not returning upon reaching zero** | Might run forever or miscount steps. | Return immediately when `nextState == 0`. |
| **Hard‑coding column masks** | Bug for width = 1 (mask 0). | Pre‑compute column masks per width; store in a static 2‑D array. |

### 3.6  The Ugly – Over‑engineering the Solution

> In interview settings, *simple is powerful*.  
> Adding extra data structures, recursion depth checks, or verbose comments can hide the elegant O(1) transition.  
> Recruiters will penalise **over‑engineering**: they value a *clear, single‑pass BFS* over a labyrinth of helper functions.

---

### 3.7  Intuition – What Recruiters Look For

- **Problem Understanding**: “I model the board as a bit‑mask; why is that helpful?”  
- **Algorithm Choice**: “BFS gives us the optimal sequence; why not DFS or greedy?”  
- **Optimization Insight**: “A single bit‑wise OR/AND deletes a whole row/column instantly.”  
- **Cross‑Language Consistency**: “I can express the same logic in Java, Python, or C++ without sacrificing speed.”

> These are the exact discussion points recruiters ask: *“How did you arrive at the solution?”* *“What’s the time complexity?”* *“Could you make it faster?”*

---

### 3.8  Wrap‑Up (Takeaway)

> LeetCode 2174 may look like a “cute little puzzle”, but mastering the BFS‑bitmask approach shows a candidate can compress a problem into a single line of code and still produce *optimal, test‑ready solutions* in multiple languages.  
> Practice this pattern, talk it through, and bring the same mindset to every interview.

### 3.9  Call to Action

> Ready to code? Grab the **Java, Python, and C++** snippets above, run them on your own LeetCode account, and be prepared to explain *why* you chose BFS‑bitmask in the next interview.

---

## 4.  Closing Notes

- **Time Complexity** – O(2ⁿ) where *n* = `rows × cols` (≤ 15).  
- **Space Complexity** – O(2ⁿ) for the queue, but in practice only a handful of states survive due to bit‑clearing.  
- **Run on LeetCode** – Paste the respective snippet into the editor; the test harness will output the minimal steps instantly.

Happy coding, and may your interviews be *as fast as a BFS‑bitmask!*

---



## 4.  Final Word

The BFS‑bitmask solution is a *compact, O(1) per transition* algorithm that recruiters love.  
Whether you write it in **Java, Python, or C++**, the key is to keep the state in a single integer, pre‑compute masks, and let BFS do the rest.  

Bring the idea, the code, and the story to your next interview – you’ll ace the problem and impress the hiring manager!