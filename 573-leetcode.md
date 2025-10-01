---
title: LeetCode 573. Squirrel Simulation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 LeetCode 573 – **Squirrel Simulation**  
**Difficulty** – Medium  
**Problem link** – https://leetcode.com/problems/squirrel-simulation/

> You’re given a garden grid (height × width), the coordinates of a tree, the squirrel’s starting point, and a list of nuts.  
> The squirrel can carry **one nut at a time** and moves only in four directions (up/down/left/right).  
> For each nut the squirrel must:  
> 1️⃣ Go to the nut,  
> 2️⃣ Carry it back to the tree,  
> 3️⃣ Drop it, and  
> 4️⃣ Repeat until all nuts are at the tree.  
> Return the **minimum number of moves** needed to collect all nuts.

> **Constraints**  
> * 1 ≤ height, width ≤ 100  
> * 1 ≤ nuts.length ≤ 5000  
> * All coordinates are within the grid

---

## 🧠 The Key Insight

The total distance depends only on **which nut the squirrel picks up first**.  

*If you pick nuts in any order*:

```
total = dist(squirrel, firstNut)          // first leg
        + dist(firstNut, tree)            // first drop
        + Σ[all nuts] dist(nut, tree)    // subsequent round‑trips
```

All subsequent legs start from the tree, so they are simply *two* times the distance from a nut to the tree (to go out and back).  
Thus we can pre‑compute:

```
base = Σ (2 * dist(nut, tree))          // all nuts round‑trip
```

To turn the first leg into a “shortcut” we replace the round‑trip for the chosen first nut with:

```
dist(squirrel, nut) + dist(nut, tree)
```

So the improvement over `base` is

```
improvement(nut) = dist(nut, tree) - dist(squirrel, nut)
```

We pick the nut that maximizes this improvement.  
The final answer is `base – maxImprovement`.

That’s the whole algorithm – **O(n)** time and **O(1)** extra space.



---

## 📦 Code Implementations

Below you’ll find clean, well‑commented solutions in **Java**, **Python**, and **C++**.  
All three use the same optimal strategy described above.

---

### Java (Java 17+)

```java
import java.util.*;

public class Solution {
    // Main function required by LeetCode
    public int minDistance(int height, int width, int[] tree, int[] squirrel, int[][] nuts) {
        int total = 0;           // Σ 2 * dist(nut, tree)
        int bestImprovement = Integer.MIN_VALUE;

        for (int[] nut : nuts) {
            int distNutToTree = manhattan(nut, tree);
            total += 2 * distNutToTree;

            int distNutToSquirrel = manhattan(nut, squirrel);
            int improvement = distNutToTree - distNutToSquirrel;
            if (improvement > bestImprovement) {
                bestImprovement = improvement;
            }
        }

        // Remove the “bad” round‑trip of the best first nut
        return total - bestImprovement;
    }

    // Helper: Manhattan distance between two points
    private int manhattan(int[] a, int[] b) {
        return Math.abs(a[0] - b[0]) + Math.abs(a[1] - b[1]);
    }
}
```

> **Why this works**  
> * `total` accumulates the cost of taking each nut from the tree and back.  
> * `bestImprovement` holds the maximum saving obtained by making a nut the first pick.  
> * Subtracting that saving yields the minimal distance.

---

### Python 3

```python
class Solution:
    def minDistance(self, height: int, width: int,
                    tree: List[int], squirrel: List[int],
                    nuts: List[List[int]]) -> int:
        total = 0          # Σ 2 * dist(nut, tree)
        best_improve = float('-inf')

        for nut in nuts:
            d_nut_tree = self._dist(nut, tree)
            total += 2 * d_nut_tree

            d_nut_squirrel = self._dist(nut, squirrel)
            improve = d_nut_tree - d_nut_squirrel
            best_improve = max(best_improve, improve)

        return total - best_improve

    @staticmethod
    def _dist(a: List[int], b: List[int]) -> int:
        """Manhattan distance."""
        return abs(a[0] - b[0]) + abs(a[1] - b[1])
```

---

### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minDistance(int height, int width,
                    vector<int>& tree,
                    vector<int>& squirrel,
                    vector<vector<int>>& nuts) {
        long long total = 0;              // Use long long to avoid overflow
        long long bestImprove = LLONG_MIN;

        for (auto& nut : nuts) {
            long long dNutTree = manhattan(nut, tree);
            total += 2 * dNutTree;

            long long dNutSquirrel = manhattan(nut, squirrel);
            long long improve = dNutTree - dNutSquirrel;
            bestImprove = max(bestImprove, improve);
        }

        return static_cast<int>(total - bestImprove);
    }

private:
    static long long manhattan(const vector<int>& a, const vector<int>& b) {
        return llabs(a[0] - b[0]) + llabs(a[1] - b[1]);
    }
};
```

---

## 📚 Blog Post – “The Good, The Bad, and the Ugly of LeetCode 573”

### 1️⃣ The Good: Simplicity & Optimality  
- **Linear time** (`O(n)`): Even with 5 000 nuts the solution runs in milliseconds.  
- **Mathematical elegance**: The entire problem collapses to a single Manhattan‑distance formula.  
- **Space‑efficient**: No DP table or graph traversal needed – just two integer accumulators.

### 2️⃣ The Bad: Misleading Intuition  
Many readers first think you need to perform a *full* traveling salesman search over nuts.  
- **Why that’s wrong**: The squirrel always returns to the tree after each nut.  
- **Key takeaway**: The only variable factor is the first nut. Once that’s fixed, the rest are fixed costs.

### 3️⃣ The Ugly: Edge Cases & Overflow  
- **Large coordinates** (height/width = 100) and up to 5 000 nuts still fit comfortably in a 32‑bit integer, but beware of multiplication (`2 * dist`).  
- **All nuts on the same spot**: `bestImprovement` may be negative – we still need to subtract it correctly.  
- **Coding pitfalls**: Forgetting to use `long long` (or `long`) in C++ can lead to subtle overflow bugs when the grid is large.

---

## 🎯 SEO‑Optimized Summary

- **Title**: “LeetCode 573 – Squirrel Simulation Explained (Java/Python/C++)”  
- **Meta description**: “Master LeetCode 573 with a clear O(n) solution. Read our Java, Python, and C++ code, plus a blog that breaks down the good, bad, and ugly of this squirrel problem.”  
- **Keywords**: *LeetCode 573, Squirrel Simulation, interview coding, optimal solution, Manhattan distance, algorithm design, job interview*, *software engineering interview*, *technical blog*.

---

## 🚀 Final Takeaway

- The **core trick** is to realize that all round‑trips except the first are fixed.  
- Pick the first nut that gives the maximum “savings” over the normal round‑trip.  
- Implement the formula in your language of choice and you’ll get a **fast, concise, and interview‑ready answer**.

Happy coding – and may the job offers keep coming! 🌟