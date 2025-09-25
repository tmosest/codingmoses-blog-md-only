---
title: LeetCode 573. Squirrel Simulation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 573. Squirrel Simulation – Fastest Java / Python / C++ Solutions  
> “**The good, the bad, and the ugly**” – a complete guide for interview‑ready candidates

---

### 📌 Problem Recap (LeetCode 573)

| Input | Meaning |
|-------|---------|
| `height, width` | Size of the garden (`height × width`). |
| `tree = [tr, tc]` | Location of the tree (the “home base”). |
| `squirrel = [sr, sc]` | Current position of the squirrel. |
| `nuts` | Array of `[nr, nc]` coordinates, one per nut. |

The squirrel can carry **one nut at a time** and moves 4‑directionally (up, down, left, right).  
We need the **minimum number of moves** for the squirrel to collect *all* nuts and put each one under the tree, one by one.

---

### ✅ The Optimal Insight – “Save the First Nut”

* Every nut eventually has to travel from the tree back to the tree: `tree → nut → tree`.  
  That is **two times** the Manhattan distance between the nut and the tree.

* The only thing we can alter is **where the squirrel starts** the first trip.  
  Instead of starting from the tree, the squirrel starts from its current position.

* So the total distance is:

```
total = Σ 2 * dist(tree, nut_i)        // every nut gets dropped
        - (dist(tree, nut_k) - dist(squirrel, nut_k))   // best first nut
```

* `dist(tree, nut_k) - dist(squirrel, nut_k)` is the *saving* if `nut_k` is taken first.  
  We just need the **maximum** saving across all nuts.

Thus the algorithm is:

1. Compute `total = Σ 2 * Manhattan(nut, tree)`  
2. Compute `maxSaving = max( dist(tree, nut) - dist(squirrel, nut) )`  
3. Answer = `total - maxSaving`

Complexity: `O(n)` time, `O(1)` extra space (`n` = number of nuts, ≤ 5 000).

---

## 🧑‍💻 Code Implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.

---

### Java (Java 17+)

```java
import java.util.*;

public class Solution {

    /** Manhattan distance between two points */
    private int dist(int[] a, int[] b) {
        return Math.abs(a[0] - b[0]) + Math.abs(a[1] - b[1]);
    }

    public int minDistance(int height, int width, int[] tree,
                           int[] squirrel, int[][] nuts) {

        long total = 0;          // use long to avoid overflow on large input
        int bestSaving = Integer.MIN_VALUE;

        for (int[] nut : nuts) {
            int dTree = dist(nut, tree);
            int dSquirrel = dist(nut, squirrel);

            total += 2L * dTree;
            int saving = dTree - dSquirrel;
            if (saving > bestSaving) bestSaving = saving;
        }
        return (int) (total - bestSaving);
    }

    /* ---------- Demo ---------- */
    public static void main(String[] args) {
        Solution sol = new Solution();
        int res = sol.minDistance(5, 7, new int[]{2, 2},
                new int[]{4, 4}, new int[][]{{3, 0}, {2, 5}});
        System.out.println(res);          // 12
    }
}
```

> **Why `long`?**  
> With up to 5 000 nuts and a grid size of 100, the raw sum can exceed `int` range.  
> Subtracting `bestSaving` brings it back into `int`.

---

### Python 3

```python
from typing import List

class Solution:
    @staticmethod
    def dist(a: List[int], b: List[int]) -> int:
        """Manhattan distance."""
        return abs(a[0] - b[0]) + abs(a[1] - b[1])

    def minDistance(
        self,
        height: int,
        width: int,
        tree: List[int],
        squirrel: List[int],
        nuts: List[List[int]],
    ) -> int:
        total = 0
        best_saving = float("-inf")

        for nut in nuts:
            d_tree = self.dist(nut, tree)
            d_squirrel = self.dist(nut, squirrel)

            total += 2 * d_tree
            best_saving = max(best_saving, d_tree - d_squirrel)

        return total - int(best_saving)

# Demo
if __name__ == "__main__":
    sol = Solution()
    print(sol.minDistance(
        5, 7,
        [2, 2],
        [4, 4],
        [[3, 0], [2, 5]]
    ))  # 12
```

> Python’s built‑in `int` automatically scales, so no overflow worries.

---

### C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
private:
    static int dist(const vector<int>& a, const vector<int>& b) {
        return abs(a[0] - b[0]) + abs(a[1] - b[1]);
    }
public:
    int minDistance(int height, int width,
                    vector<int> tree,
                    vector<int> squirrel,
                    vector<vector<int>> nuts) {

        long long total = 0;            // avoid overflow
        int bestSaving = INT_MIN;

        for (const auto& nut : nuts) {
            int dTree = dist(nut, tree);
            int dSquirrel = dist(nut, squirrel);

            total += 2LL * dTree;
            bestSaving = max(bestSaving, dTree - dSquirrel);
        }
        return static_cast<int>(total - bestSaving);
    }
};

/* ---------- Demo ---------- */
int main() {
    Solution sol;
    cout << sol.minDistance(5, 7,
                            {2, 2},
                            {4, 4},
                            {{3, 0}, {2, 5}}) << endl;  // 12
    return 0;
}
```

> `long long` protects against overflow just like Java’s `long`.

---

## 📚 Blog Article – “The Good, the Bad, and the Ugly” of the Squirrel Simulation

---

### Introduction  

If you’re a **frontend or backend engineer** eyeing a data‑structures interview, the **Squirrel Simulation** (LeetCode 573) is a gem. It tests:

1. **Manhattan distance** understanding  
2. **Greedy algorithm** intuition  
3. **Time‑space optimization** in a real‑world grid

In this article we’ll:

- Break the problem into *the good, the bad, and the ugly*  
- Provide production‑ready code (Java, Python, C++)  
- Show how to explain it in a job interview  
- Sprinkle SEO keywords to help recruiters find you

---

### The Good – A Clean, O(n) Solution  

| Feature | Why it’s great |
|---------|----------------|
| **Linear time** | `O(n)` is optimal; no DP or graph search needed. |
| **Constant space** | Only two integers (`total`, `bestSaving`). |
| **Mathematically elegant** | One‑liner after the insight: *pick the nut that gives the biggest saving*. |
| **Works for all constraints** | Handles up to 5 000 nuts, 100 × 100 grid with no overflow (if you use 64‑bit ints). |

> *Key takeaway*: When a problem asks for “minimal moves” on a grid, **Manhattan distance** often gives a direct formula. Don’t waste time over‑engineering.

---

### The Bad – The Hidden Pitfalls  

| Pitfall | How to avoid |
|---------|--------------|
| **Overflow** | In Java/C++ use `long`/`long long`. In Python it’s implicit. |
| **Mis‑reading “one nut at a time”** | It’s not a TSP (Traveling Salesman Problem). You never need to plan a long route; each nut is a *local* trip. |
| **Ignoring the first‑nut advantage** | Without subtracting the maximum saving you’ll always double‑count the path from the tree to the first nut. |

If you forget any of these, you’ll get a wrong answer on 30‑70% of test cases.

---

### The Ugly – Common Interview Traps  

1. **Brute‑force TSP** – trying to compute all permutations (factorial complexity) – clearly infeasible.  
2. **Using BFS for each nut** – unnecessary; Manhattan distance is a *closed‑form* answer on a grid with only orthogonal moves.  
3. **Wrong data type** – using `int` for sums that can exceed 2 147 483 647 on the edge cases.  
4. **Mis‑interpreting “at most one nut at a time”** – leads to thinking you can carry multiple nuts in one go, which would change the formula.

A good interviewee will mention **why those pitfalls are wrong** and pivot straight to the greedy solution.

---

### How to Talk About It in an Interview  

> **Problem Recap** – “We have a grid, a squirrel, nuts, and a tree. We want the shortest path that collects every nut and drops it in the tree one by one.”

> **Observation** – “Every nut must travel from the tree to itself and back. That’s 2 × Manhattan distance, regardless of the order.”

> **Optimization** – “The only decision is which nut the squirrel picks first. If the squirrel starts at its current location, it saves `dist(tree, nut) – dist(squirrel, nut)`. Pick the nut with the largest saving.”

> **Complexity** – “O(n) time, O(1) extra space. Works for up to 5 000 nuts.”

> **Edge Cases** – “We guard against overflow using 64‑bit integers. Even if the grid is 100×100, the maximum sum is below 1 000 000, but with 5 000 nuts the product can exceed 32‑bit.”

> **Wrap‑up** – “Thus the answer is `sum(2 * d_tree[n]) – max(d_tree[n] - d_squirrel[n])`.”

---

### SEO‑Optimized Conclusion

If you’re looking for a **Java LeetCode interview solution**, this article covers everything from the optimal algorithm to production‑ready code. Whether you’re a Python developer, a C++ engineer, or a Java enthusiast, the key ideas remain the same. The Squirrel Simulation is a great showcase of **greedy algorithm** mastery, and explaining it well can impress hiring managers during a technical interview.

**Key Search Tags:**  
- *Squirrel Simulation LeetCode*  
- *minDistance solution*  
- *Manhattan distance grid*  
- *greedy algorithm interview*  
- *O(n) time complexity*  
- *Java Python C++ interview code*  

---

### Final Takeaway

- **The Good**: Linear, constant‑space greedy solution.  
- **The Bad**: Common overflow or mis‑interpretation mistakes.  
- **The Ugly**: Brute‑force TSP, BFS, wrong data types.

Master these points, bring the code snippets, and you’ll not only solve the problem but also demonstrate clear thinking and interview‑ready communication. Good luck, and may the squirrels be ever in your favor! 🐿️💡