---
title: LeetCode 2087. Minimum Cost Homecoming of a Robot in a Grid - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 2087 – Minimum Cost Homecoming of a Robot in a Grid  
### One‑liner greedy solution (O(1) space, O(|Δrow|+|Δcol|) time)

```java
// Java 17
class Solution {
    public int minCost(int[] startPos, int[] homePos,
                       int[] rowCosts, int[] colCosts) {
        int r = 0;
        // vertical moves
        if (startPos[0] < homePos[0]) {
            for (int i = startPos[0] + 1; i <= homePos[0]; ++i) r += rowCosts[i];
        } else if (startPos[0] > homePos[0]) {
            for (int i = startPos[0] - 1; i >= homePos[0]; --i) r += rowCosts[i];
        }

        int c = 0;
        // horizontal moves
        if (startPos[1] < homePos[1]) {
            for (int i = startPos[1] + 1; i <= homePos[1]; ++i) c += colCosts[i];
        } else if (startPos[1] > homePos[1]) {
            for (int i = startPos[1] - 1; i >= homePos[1]; --i) c += colCosts[i];
        }

        return r + c;
    }
}
```

```python
# Python 3.10
class Solution:
    def minCost(self, startPos, homePos, rowCosts, colCosts):
        r = 0
        # vertical cost
        if startPos[0] < homePos[0]:
            r += sum(rowCosts[startPos[0] + 1 : homePos[0] + 1])
        elif startPos[0] > homePos[0]:
            r += sum(rowCosts[homePos[0] : startPos[0]])

        c = 0
        # horizontal cost
        if startPos[1] < homePos[1]:
            c += sum(colCosts[startPos[1] + 1 : homePos[1] + 1])
        elif startPos[1] > homePos[1]:
            c += sum(colCosts[homePos[1] : startPos[1]])

        return r + c
```

```cpp
// C++17
class Solution {
public:
    int minCost(vector<int>& startPos, vector<int>& homePos,
                vector<int>& rowCosts, vector<int>& colCosts) {
        long long cost = 0;

        // vertical moves
        if (startPos[0] < homePos[0]) {
            for (int i = startPos[0] + 1; i <= homePos[0]; ++i) cost += rowCosts[i];
        } else if (startPos[0] > homePos[0]) {
            for (int i = startPos[0] - 1; i >= homePos[0]; --i) cost += rowCosts[i];
        }

        // horizontal moves
        if (startPos[1] < homePos[1]) {
            for (int i = startPos[1] + 1; i <= homePos[1]; ++i) cost += colCosts[i];
        } else if (startPos[1] > homePos[1]) {
            for (int i = startPos[1] - 1; i >= homePos[1]; --i) cost += colCosts[i];
        }

        return static_cast<int>(cost);
    }
};
```

> **Why this works** –  
> Every valid path must visit *every* row between `startRow` and `homeRow` and every column between `startCol` and `homeCol`.  
> Moving in any order can never avoid paying the cost of these rows/columns, so the total cost is independent of the specific path shape. The greedy algorithm simply accumulates the mandatory row‑ and column‑costs.

---

## 2.  Blog Article  
*(SEO‑optimized, interview‑friendly, “Good, Bad & Ugly” breakdown)*

### Title
> **LeetCode 2087 – Minimum Cost Homecoming: A Simple Greedy Solution in Java, Python & C++ (Interview‑Ready)**

### Meta description
> Learn the O(1) space solution for LeetCode 2087. Understand why a greedy sum of row/column costs is optimal, discover edge cases, and get ready for coding‑interview questions.

### Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Intuitive “Axis‑Aligned” Solution](#intuitive-solution)  
3. [Correctness Proof & Complexity](#correctness)  
4. [Common Pitfalls – “The Bad”](#bad)  
5. [Edge Cases – “The Ugly”](#ugly)  
6. [Alternative Approaches (BFS / Dijkstra)](#alternatives)  
7. [Sample Test Cases & Unit Tests](#tests)  
8. [Take‑away for Interview Success](#takeaway)  

---

### Problem Overview

> **LeetCode 2087 – Minimum Cost Homecoming of a Robot in a Grid**  
> A robot starts at `startPos = [r1, c1]` in a 2‑D grid and must reach `homePos = [r2, c2]`.  
> It can only move **up/down** (change row) or **left/right** (change column).  
> **Each movement cost** depends solely on the row or column *entered*:
> - Moving into a new row `i` costs `rowCosts[i]`.
> - Moving into a new column `j` costs `colCosts[j]`.  
> `rowCosts` has length `m`, `colCosts` has length `n`.  
> Find the *minimum* total cost for the robot to reach its home.

Constraints:  
- `1 ≤ m, n ≤ 10⁵`  
- All costs are non‑negative integers.

---

### Intuitive Greedy Solution

1. **Vertical moves** – walk from `r1` to `r2`.  
   *If `r2 > r1`* → sum `rowCosts[r1+1 … r2]`.  
   *If `r2 < r1`* → sum `rowCosts[r1-1 … r2]`.
2. **Horizontal moves** – walk from `c1` to `c2`.  
   *If `c2 > c1`* → sum `colCosts[c1+1 … c2]`.  
   *If `c2 < c1`* → sum `colCosts[c1-1 … c2]`.
3. Return the sum of the two partial sums.

**Why no path‑finding is required**  
Because the robot can only move orthogonally, every path must cross each intermediate row **and** each intermediate column at least once. The order in which you cross them does not change the total set of rows/columns visited, and hence not the total cost. Therefore, the minimal cost equals the sum of the mandatory rows and columns, which is exactly what the algorithm does.

---

### Correctness Proof

Let `R = {min(r1, r2)+1 … max(r1, r2)}` be the set of rows that must be entered (each time we step from one row to the next).  
Similarly let `C = {min(c1, c2)+1 … max(c1, c2)}` be the set of columns that must be entered.

*Lemma 1* – Any valid path must contain every row in `R` and every column in `C`.

*Proof.*  
The robot starts at row `r1`. The only way to change its row index is to move up or down one step at a time. To reach row `r2`, the path must step from `r1` to `r1±1`, then to `r1±2`, … until `r2`. Thus each intermediate row in `R` is visited. The same reasoning applies horizontally for `C`. ∎

*Lemma 2* – The cost incurred by a path equals  
`Σ_{i∈R} rowCosts[i] + Σ_{j∈C} colCosts[j]`.

*Proof.*  
Each time the robot enters a new row `i` (either by moving up or down), it pays `rowCosts[i]` exactly once. By Lemma 1 all rows in `R` are entered, so the total vertical cost is the sum over `R`. Analogously for columns. No other costs exist. ∎

*Theorem* – The greedy algorithm returns the minimum possible cost.

*Proof.*  
Any path’s cost is exactly the expression in Lemma 2. Since the algorithm computes that exact expression, its result is the cost of *any* path, hence also the minimal cost. ∎

---

### Common Pitfalls – “The Bad”

| Pitfall | What to avoid | Why it fails |
|---------|----------------|--------------|
| **Assuming a shortest‑distance path** | Thinking Manhattan distance matters | All paths have the same cost; distance is irrelevant |
| **Off‑by‑one in loops** | Using inclusive bounds incorrectly | Skipping the first or last row/column cost |
| **Using 64‑bit overflow** | Adding large costs into an `int` | Cost can reach `10⁵ * 10⁹` → use `long long`/`long` |
| **Treating the robot as a graph problem** | Running BFS/Dijkstra unnecessarily | Adds O(mn) time & memory, overkill for this problem |
| **Ignoring negative costs** | If a cost array contains negatives, greedy still works because all costs are summed | No path can avoid a negative row/column; greedy still optimal |

---

### Edge Cases – “The Ugly”

| Case | What to check | Why it matters |
|------|----------------|----------------|
| Start equals home | `startPos == homePos` | Result must be `0`. The algorithm handles this automatically (no loops). |
| Large grid (10⁵ rows or columns) | Performance | Loop length is only `|Δrow| + |Δcol|`, at most `2·10⁵` iterations – fine in < 1 ms. |
| Cost arrays with zeros | Zero‑cost rows/cols | Sum correctly handles zeros; greedy still valid. |
| Non‑continuous cost arrays (if the problem allowed gaps) | Not applicable here | Problem guarantees a contiguous grid. |
| 32‑bit integer overflow | Costs up to 10⁹ | Use `long long` / `long` during accumulation, cast to `int` at the end. |

---

### Alternative Solutions (Why They’re Unnecessary)

| Algorithm | When it’s useful | Why it’s overkill here |
|-----------|------------------|------------------------|
| **Breadth‑First Search** | Uniform edge weights, small grid | Each edge weight is variable; BFS would have to keep track of cost per node → O(mn) time & memory. |
| **Dijkstra** | Variable positive weights | Works, but the greedy solution is simpler and faster. |
| **Dynamic Programming** | Path‑dependent costs | Not needed because the cost structure is row/column‑only. |

---

### Sample Unit Tests

```java
@Test
void test() {
    var s = new Solution();
    assertEquals(12, s.minCost(
            new int[]{1,1}, new int[]{4,3},
            new int[]{1,3,5,2}, new int[]{2,4,6,1}));
}
```

```python
def test():
    sol = Solution()
    assert sol.minCost([1,1],[4,3],[1,3,5,2],[2,4,6,1]) == 12
```

```cpp
void test() {
    Solution sol;
    assert(sol.minCost({1,1},{4,3},{1,3,5,2},{2,4,6,1}) == 12);
}
```

---

### Take‑away for the Interview

1. **Identify invariants** – The robot must cross all rows/cols between the two points.  
2. **Avoid unnecessary graph traversal** – The problem’s cost function collapses to a simple sum.  
3. **Beware of off‑by‑one errors** – Remember that the cost of a row/col is paid *when you enter it*, not when you leave it.  
4. **Use 64‑bit arithmetic** – Cost can exceed 2³¹‑1 on the upper bounds.  
5. **Explain the greedy proof** – Interviewers love when you can articulate *why* the simplest algorithm is correct.

---

## 2.  Blog Post – “LeetCode 2087: The Robot Homecoming Puzzle (Java/Python/C++ Solutions)”

> **Keywords** – *LeetCode 2087*, *minimum cost homecoming robot*, *grid algorithm*, *Java solution*, *Python solution*, *C++ solution*, *coding interview*, *algorithm interview questions*, *job interview algorithm*.

---

### 🚀  Mastering LeetCode 2087 – The Robot Homecoming Puzzle  

**Meta description**:  
"Step-by-step LeetCode 2087 solution – calculate minimum cost for a robot to return home in a grid. Java, Python, C++ code examples, proof of correctness, edge‑case guide for interviews."

---

#### 1️⃣  What Makes This Puzzle Interesting?

- A **robot** can only move **orthogonally**.
- Each **row** and **column** carries a *distinct* cost.
- You’re asked for the **minimum** cost, not the shortest path.

It sounds like a classic path‑finding problem, but the trick lies in the cost model.

---

#### 2️⃣  Good – The Elegant Greedy Trick  

- **Step 1**: Sum all *mandatory* row‑costs between `r1` and `r2`.  
- **Step 2**: Sum all mandatory column‑costs between `c1` and `c2`.  
- **Result**: Add the two partial sums.

Why this works: every possible walk crosses the same set of rows and columns; the order doesn't matter.  
**Time**: O(|Δrow| + |Δcol|)  
**Space**: O(1)

---

#### 3️⃣  Bad – The Over‑Engineered Approach  

- People try BFS or Dijkstra, assuming variable edge weights need a priority queue.  
- These algorithms use O(mn) memory and are slower than a simple loop.  
- Intersections and off‑by‑ones become a nightmare.

---

#### 4️⃣  Ugly – Edge Cases That Bite  

- **Start equals home** → zero cost.  
- **Large grids** → 10⁵ rows or columns → ensure loops don’t exceed that bound.  
- **Zero or negative costs** → still summed correctly.  
- **Overflow** → use 64‑bit arithmetic in Java (`long`), C++ (`long long`), or Python’s arbitrary ints.

---

#### 5️⃣  Proof in Plain English  

1. *Invariant*: To move from row `r1` to row `r2`, every intermediate row must be visited.  
2. *Cost attribution*: The robot pays `rowCosts[i]` once for each row `i` it enters.  
3. *Summation*: The total cost equals the sum over all visited rows and columns.  
4. Since the greedy algorithm computes exactly that sum, it is optimal.

Explain this clearly in interviews – the “why” is half the job.

---

#### 6️⃣  Unit Test Recipes  

| Language | Example |
|----------|---------|
| **Java** | `assertEquals(12, s.minCost(new int[]{1,1}, new int[]{4,3}, new int[]{1,3,5,2}, new int[]{2,4,6,1}));` |
| **Python** | `assert sol.minCost([1,1],[4,3],[1,3,5,2],[2,4,6,1]) == 12` |
| **C++** | `assert(sol.minCost({1,1},{4,3},{1,3,5,2},{2,4,6,1}) == 12);` |

---

#### 7️⃣  Interview Boost – What Recruiters Want to Hear  

- **Simplicity**: The solution is just a sum.  
- **Proof**: Demonstrate the invariants and why no other algorithm is needed.  
- **Edge‑case awareness**: Show you considered the “ugly” scenarios.  
- **Language proficiency**: Provide clean, idiomatic code for Java, Python, and C++.  

---  

### 🎯 Final Thought

LeetCode 2087 is a beautiful example of how **problem constraints can turn a seemingly complex path‑finding question into a one‑liner greedy sum**. Master it, explain the logic, and you’ll impress any interview panel. Happy coding!  

---  

**Enjoy the code, ace the interview, and keep solving!**  

---


*End of article.*