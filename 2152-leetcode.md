---
title: LeetCode 2152. Minimum Number of Lines to Cover Points - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 “Minimum Number of Lines to Cover Points” – LeetCode 2152  
## 📚 Problem Overview  
| Item | Detail |
|------|--------|
| **LeetCode ID** | 2152 |
| **Difficulty** | Medium |
| **Title** | Minimum Number of Lines to Cover Points |
| **Core Idea** | Find the smallest set of straight lines that cover all given points on an XY‑plane. |
| **Constraints** | `1 ≤ points.length ≤ 10`, `points[i] = [xi, yi]`, all points unique, `-100 ≤ xi, yi ≤ 100`. |

> **Goal**: Return the minimum number of straight lines needed to cover every point.

---

## ✅ Why You Should Master This Problem

* **Interview staple** – Many tech companies ask this to test combinatorial optimization, recursion, bit‑masking, and DP skills.
* **Language versatility** – Solutions exist in **Java, Python, and C++**. Demonstrating proficiency across languages showcases flexibility to hiring managers.
* **Algorithm depth** – You’ll learn how to trade off brute‑force vs. memoization, and how to manage state with bit masks.

---

## 🧠 High‑Level Strategies

| Approach | Complexity | When to Use |
|----------|------------|-------------|
| **Recursive + Brute‑Force** | `O(n^3)` (worst case) | Small `n` (≤ 10) – simple to code |
| **DFS + Bitmask DP** | `O(n^3 * 2^n)` | Balanced trade‑off for `n` ≤ 10 |
| **Memoized Recursion** | Same as DP, but often easier to understand | When you want to avoid explicit DP tables |

> **Best Choice**: For `n ≤ 10`, the DFS + bitmask DP is efficient and easy to implement.  
> **Worst‑Case**: 10 points → 1024 states → perfectly fine.

---

## 📦 Code Implementations

Below you’ll find fully‑commented solutions in **Java, Python, and C++**.  
Each code snippet includes:

* A small test harness (optional `main` method / `if __name__ == "__main__":`).
* Clear function signatures matching LeetCode style.
* Inline comments explaining each step.

---

### 1️⃣ Java (Bitmask + DFS)

```java
import java.util.*;

class Solution {
    // Target mask: all points covered
    private int targetMask;
    // Memoization map: key -> minimal lines
    private Map<Integer, Integer> memo;

    public int minimumLines(int[][] points) {
        int n = points.length;
        targetMask = (1 << n) - 1;
        memo = new HashMap<>();
        return dfs(0, points);
    }

    // DFS over bitmask state
    private int dfs(int mask, int[][] points) {
        if (mask == targetMask) return 0;          // all points covered
        if (memo.containsKey(mask)) return memo.get(mask);

        // Find first uncovered point
        int first = 0;
        while ((mask & (1 << first)) != 0) first++;

        int best = Integer.MAX_VALUE;

        // Try drawing a line that passes through 'first' and some other point
        for (int i = 0; i < points.length; i++) {
            if (i == first || (mask & (1 << i)) != 0) continue;
            int newMask = mask | (1 << first) | (1 << i);

            double slope = slope(points[first], points[i]);

            // Include all points on this line
            for (int j = 0; j < points.length; j++) {
                if (j == first || j == i) continue;
                if ((mask & (1 << j)) != 0) continue;
                if (Double.compare(slope, slope(points[first], points[j])) == 0)
                    newMask |= (1 << j);
            }
            best = Math.min(best, 1 + dfs(newMask, points));
        }

        // Edge case: all remaining points are collinear with 'first'
        if (best == Integer.MAX_VALUE) best = 1;

        memo.put(mask, best);
        return best;
    }

    private double slope(int[] a, int[] b) {
        // Avoid division by zero: vertical line -> Infinity
        if (a[0] == b[0]) return Double.POSITIVE_INFINITY;
        return (double)(b[1] - a[1]) / (b[0] - a[0]);
    }

    // ---------- Test harness ----------
    public static void main(String[] args) {
        Solution sol = new Solution();
        int[][] points1 = {{0,1},{2,3},{4,5},{4,3}};
        int[][] points2 = {{0,2},{-2,-2},{1,4}};
        System.out.println("Example 1: " + sol.minimumLines(points1)); // 2
        System.out.println("Example 2: " + sol.minimumLines(points2)); // 1
    }
}
```

---

### 2️⃣ Python (Bitmask + DFS)

```python
from functools import lru_cache
from typing import List

class Solution:
    def minimumLines(self, points: List[List[int]]) -> int:
        n = len(points)
        target = (1 << n) - 1

        def slope(a, b):
            if a[0] == b[0]:
                return float('inf')          # vertical line
            return (b[1] - a[1]) / (b[0] - a[0])

        @lru_cache(None)
        def dfs(mask: int) -> int:
            if mask == target:
                return 0
            # Find first uncovered point
            first = 0
            while mask & (1 << first):
                first += 1

            best = float('inf')
            for i in range(n):
                if i == first or mask & (1 << i):
                    continue
                new_mask = mask | (1 << first) | (1 << i)
                sl = slope(points[first], points[i])

                for j in range(n):
                    if j in (first, i) or mask & (1 << j):
                        continue
                    if abs(sl - slope(points[first], points[j])) < 1e-9:
                        new_mask |= (1 << j)

                best = min(best, 1 + dfs(new_mask))

            return 1 if best == float('inf') else best

        return dfs(0)

# ---------- Test harness ----------
if __name__ == "__main__":
    sol = Solution()
    print(sol.minimumLines([[0,1],[2,3],[4,5],[4,3]]))  # 2
    print(sol.minimumLines([[0,2],[-2,-2],[1,4]]))      # 1
```

---

### 3️⃣ C++ (Bitmask + DFS)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumLines(vector<vector<int>>& points) {
        int n = points.size();
        int target = (1 << n) - 1;
        vector<int> memo(1 << n, -1);
        return dfs(0, points, target, memo);
    }

private:
    double slope(const vector<int>& a, const vector<int>& b) {
        if (a[0] == b[0]) return numeric_limits<double>::infinity(); // vertical
        return static_cast<double>(b[1] - a[1]) / (b[0] - a[0]);
    }

    int dfs(int mask, const vector<vector<int>>& pts, int target, vector<int>& memo) {
        if (mask == target) return 0;
        if (memo[mask] != -1) return memo[mask];

        // Find first uncovered point
        int first = 0;
        while (mask & (1 << first)) ++first;

        int best = INT_MAX;
        for (int i = 0; i < pts.size(); ++i) {
            if (i == first || (mask & (1 << i))) continue;
            int newMask = mask | (1 << first) | (1 << i);
            double sl = slope(pts[first], pts[i]);

            for (int j = 0; j < pts.size(); ++j) {
                if (j == first || j == i || (mask & (1 << j))) continue;
                if (abs(sl - slope(pts[first], pts[j])) < 1e-9)
                    newMask |= (1 << j);
            }
            best = min(best, 1 + dfs(newMask, pts, target, memo));
        }

        if (best == INT_MAX) best = 1; // All remaining points collinear with first
        memo[mask] = best;
        return best;
    }
};

// ---------- Test harness ----------
int main() {
    Solution s;
    vector<vector<int>> pts1 = {{0,1},{2,3},{4,5},{4,3}};
    vector<vector<int>> pts2 = {{0,2},{-2,-2},{1,4}};
    cout << s.minimumLines(pts1) << endl; // 2
    cout << s.minimumLines(pts2) << endl; // 1
    return 0;
}
```

---

## 📊 Complexity Breakdown

| Step | Time | Space |
|------|------|-------|
| **Slope calculation** | `O(1)` | – |
| **DFS state transitions** | `O(n^2)` per state | – |
| **Number of states** | `2^n` | ≤ 1024 |
| **Total** | `O(n^3 * 2^n)` | ≤ `10^5` operations, < 1 ms in practice |

> **Why `n^3`?**  
> 1. Pick the first uncovered point (`n`).  
> 2. Pair it with any other uncovered point (`n`).  
> 3. Scan the rest to collect collinear points (`n`).  

> **Space** – Memo table of size `2^n` (≤ 1024 integers).

---

## 🔄 Alternative: Pure Brute‑Force (Recursive)

If you prefer a **“no‑memo”** solution for maximum readability, the following Python skeleton works for `n ≤ 10`.  

```python
def brute_force(points):
    n = len(points)
    best = n  # upper bound

    def helper(uncovered):
        nonlocal best
        if not uncovered:            # all points covered
            return 0
        if len(uncovered) >= best:   # pruning
            return best

        first = uncovered[0]
        for second in uncovered[1:]:
            line_points = [p for p in uncovered if on_same_line(first, second, p)]
            new_uncovered = [p for p in uncovered if p not in line_points]
            best = min(best, 1 + helper(new_uncovered))
        return best

    return helper(points)
```

> **Drawback** – No memoization, but still < `10!` permutations.  

---

## 🎯 Take‑Away Checklist (Interview)

1. **State representation**  
   * Use a bit mask (`int mask`) – each bit represents whether a point is already covered.
2. **Recursive DFS**  
   * Pick the first uncovered point, pair it with another point, build a line, and recurse on the new mask.
3. **Memoization / DP**  
   * Cache results for each mask (`lru_cache` in Python, `unordered_map` or vector in Java/C++).
4. **Slope handling**  
   * Handle vertical lines with `Infinity` or `numeric_limits<double>::infinity()`.
   * Use a tolerance (`1e‑9`) when comparing floating‑point slopes.

5. **Edge cases**  
   * All remaining points collinear with the first uncovered point → use one line.

---

## 📈 Big‑O Summary (DFS + Bitmask)

| Metric | Value |
|--------|-------|
| **Time** | `O(n^3 * 2^n)` |
| **Space** | `O(2^n)` for memoization + recursion stack (`≤ 10` levels). |
| **Practical** | For `n = 10` → 1024 states, ≈ 5 × 10⁵ operations – runs in < 1 ms. |

---

## 🛠️ What to Show on Your Resume

| Skill | Sample Bullet Point |
|-------|---------------------|
| **Algorithm Design** | “Solved LeetCode 2152 using DFS with bit‑mask DP to minimize lines covering up to 10 points.” |
| **Complexity Analysis** | “Achieved `O(n³·2ⁿ)` time and `O(2ⁿ)` space; demonstrated full memoization.” |
| **Multilingual** | “Implemented solutions in **Java, Python, and C++**, showcasing cross‑language fluency.” |
| **Interview Success** | “Used this problem to land a role at XYZ Tech – interview feedback praised my clear state‑encoding strategy.” |

> **Tip**: When answering interview questions, start with the **problem‑transformation** (bitmask, DP, recursion), then explain the **state** and **transitions**. Show your complexity analysis to prove you understood the trade‑offs.

---

## 🎉 Bonus – Quick‑Start Playground

If you’d like to experiment locally, simply copy the chosen language snippet into a file and run it. The included `main`/`if __name__ == "__main__":` section will print the expected outputs:

```bash
$ javac Solution.java && java Solution
Example 1: 2
Example 2: 1

$ python3 solution.py
2
1

$ ./a.out
2
1
```

---

## 🎯 Final Take‑Away

* For LeetCode 2152, **DFS + Bitmask DP** is the sweet spot: elegant, fast, and language‑agnostic.  
* Master the **state‑encoding** (bit masks) and **memoization** patterns – they’re useful for a wide variety of interview problems (e.g., “Set Cover”, “Maximum Subsequence”, “Stickers to Spell Word”).  
* Practice explaining the algorithm, complexity, and edge cases—clear communication is half the interview win!

Good luck, and happy coding! 🚀✨

---

> **Meta Description** – “Solve LeetCode 2152: Minimum Number of Lines to Cover Points. Get the optimal Java, Python, and C++ solutions, algorithmic analysis, and interview tips to ace your next job interview.”