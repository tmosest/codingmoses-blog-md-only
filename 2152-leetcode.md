---
title: LeetCode 2152. Minimum Number of Lines to Cover Points - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ LeetCode 2152 – “Minimum Number of Lines to Cover Points”

> **Problem**  
> You are given an array `points` where `points[i] = [xᵢ, yᵢ]` represents a point on an X‑Y plane.  
> Add straight lines so that every point is covered by **at least one line**.  
> Return the *minimum* number of lines needed.

> **Constraints**  
> * 1 ≤ points.length ≤ 10  
> * All coordinates are between –100 and 100 and are unique

---

### TL;DR – The Solution

Because the input size is tiny (≤ 10), we can solve the problem with a classic **bitmask DP**:

1. **Represent the set of already‑covered points** by a bitmask `mask`.  
2. Pick the first uncovered point `i`.  
3. Try every other point `j` to form a line `(i, j)` – all points that are collinear with this line can be added to the mask.  
4. Recurse on the new mask and add `1` for the line we just placed.  
5. Use memoization (`Map<Integer, Integer>`) to avoid recomputing the same masks.  
6. The answer is the minimum over all possible first lines.

Complexity:  
`O(2ⁿ · n²)` time, `O(2ⁿ)` space – easily fast for `n ≤ 10`.

Below are full, ready‑to‑copy solutions in **Java, Python, and C++**.

---

## 🧑‍💻 Code

### Java (Java 17)

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {

    // pair of reduced (dx,dy) that uniquely defines a slope
    private static class Slope {
        final int dx, dy;

        Slope(int dx, int dy) {
            // normalise sign so that dy >= 0; if dy==0, dx>0
            if (dy < 0 || (dy == 0 && dx < 0)) {
                dx = -dx; dy = -dy;
            }
            int g = gcd(Math.abs(dx), Math.abs(dy));
            this.dx = dx / g;
            this.dy = dy / g;
        }

        @Override
        public boolean equals(Object o) {
            if (!(o instanceof Slope)) return false;
            Slope other = (Slope) o;
            return this.dx == other.dx && this.dy == other.dy;
        }

        @Override
        public int hashCode() {
            return 31 * dx + dy;
        }
    }

    private static int gcd(int a, int b) {
        while (b != 0) {
            int t = a % b;
            a = b; b = t;
        }
        return a;
    }

    public int minimumLines(int[][] points) {
        int n = points.length;
        int targetMask = (1 << n) - 1;                 // all points covered
        Map<Integer, Integer> memo = new HashMap<>();   // mask -> min lines

        return dfs(0, points, targetMask, memo);
    }

    private int dfs(int mask, int[][] points, int targetMask,
                    Map<Integer, Integer> memo) {

        if (mask == targetMask) return 0;     // all covered

        if (memo.containsKey(mask)) return memo.get(mask);

        // find first uncovered point
        int first = 0;
        while ((mask & (1 << first)) != 0) first++;

        int best = Integer.MAX_VALUE;

        // try placing a line that goes through 'first' and some other point
        for (int j = 0; j < points.length; j++) {
            if (j == first || (mask & (1 << j)) != 0) continue;

            int newMask = mask | (1 << first) | (1 << j);
            Slope slope = new Slope(points[j][0] - points[first][0],
                                    points[j][1] - points[first][1]);

            // add all points collinear with this slope
            for (int k = 0; k < points.length; k++) {
                if (k == first || k == j) continue;
                if ((mask & (1 << k)) != 0) continue;
                Slope s2 = new Slope(points[k][0] - points[first][0],
                                     points[k][1] - points[first][1]);
                if (slope.equals(s2)) newMask |= 1 << k;
            }

            best = Math.min(best, 1 + dfs(newMask, points, targetMask, memo));
        }

        // also the option of covering 'first' alone (degenerate line)
        best = Math.min(best, 1 + dfs(mask | (1 << first), points, targetMask, memo));

        memo.put(mask, best);
        return best;
    }
}
```

### Python (Python 3.11)

```python
from typing import List, Tuple, Dict
import math
import sys
sys.setrecursionlimit(1 << 25)

class Solution:
    def _slope(self, p1: Tuple[int, int], p2: Tuple[int, int]) -> Tuple[int, int]:
        """Return a normalized (dx, dy) pair that uniquely defines a slope."""
        dx, dy = p2[0] - p1[0], p2[1] - p1[1]
        if dx == 0:
            return (0, 1)          # vertical line
        if dy == 0:
            return (1, 0)          # horizontal line
        g = math.gcd(abs(dx), abs(dy))
        dx //= g
        dy //= g
        # keep dy positive for a canonical representation
        if dy < 0:
            dx, dy = -dx, -dy
        return (dx, dy)

    def minimumLines(self, points: List[List[int]]) -> int:
        n = len(points)
        points = [tuple(p) for p in points]
        target = (1 << n) - 1
        memo: Dict[int, int] = {}

        def dfs(mask: int) -> int:
            if mask == target:
                return 0
            if mask in memo:
                return memo[mask]

            # first uncovered point
            first = next(i for i in range(n) if not mask & (1 << i))
            best = float('inf')

            # try every other point to form a line
            for j in range(n):
                if j == first or mask & (1 << j):
                    continue
                new_mask = mask | (1 << first) | (1 << j)
                slope = self._slope(points[first], points[j])

                for k in range(n):
                    if k == first or k == j or mask & (1 << k):
                        continue
                    if self._slope(points[first], points[k]) == slope:
                        new_mask |= 1 << k

                best = min(best, 1 + dfs(new_mask))

            # option: cover the first point alone
            best = min(best, 1 + dfs(mask | (1 << first)))

            memo[mask] = best
            return best

        return dfs(0)
```

### C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // Normalised slope as pair<int,int>
    struct Slope {
        int dx, dy;
        Slope(int a, int b) {
            if (a == 0) { dx = 0; dy = 1; return; }
            if (b == 0) { dx = 1; dy = 0; return; }
            if (a < 0) a = -a, b = -b;          // keep dy >= 0
            int g = std::gcd(abs(a), abs(b));
            dx = a / g;
            dy = b / g;
            if (dy < 0) { dx = -dx; dy = -dy; } // canonical
        }
        bool operator==(const Slope& o) const {
            return dx == o.dx && dy == o.dy;
        }
    };

    int minimumLines(vector<vector<int>>& points) {
        int n = points.size();
        int targetMask = (1 << n) - 1;
        unordered_map<int,int> memo;
        return dfs(0, points, targetMask, memo);
    }

private:
    int dfs(int mask,
            const vector<vector<int>>& pts,
            int targetMask,
            unordered_map<int,int>& memo) {

        if (mask == targetMask) return 0;
        auto it = memo.find(mask);
        if (it != memo.end()) return it->second;

        // first uncovered point
        int first = 0;
        while (mask & (1 << first)) ++first;

        int best = INT_MAX;

        for (int j = 0; j < pts.size(); ++j) {
            if (j == first || (mask & (1 << j))) continue;
            int newMask = mask | (1 << first) | (1 << j);
            Slope s(pts[j][0] - pts[first][0],
                    pts[j][1] - pts[first][1]);

            for (int k = 0; k < pts.size(); ++k) {
                if (k == first || k == j || (mask & (1 << k))) continue;
                Slope t(pts[k][0] - pts[first][0],
                        pts[k][1] - pts[first][1]);
                if (s.dx == t.dx && s.dy == t.dy)
                    newMask |= 1 << k;
            }

            best = min(best, 1 + dfs(newMask, pts, targetMask, memo));
        }

        // line that covers only 'first' (degenerate case)
        best = min(best, 1 + dfs(mask | (1 << first), pts, targetMask, memo));

        memo[mask] = best;
        return best;
    }
};
```

---

## 📈 Complexity Analysis (Fixed)

|  | **Time** | **Space** |
|---|---|---|
| Bitmask DP | `O(2ⁿ · n²)`  (≈ 1 048 576 operations for `n = 10`) | `O(2ⁿ)`  (≈ 1 024 ints) |

*Why the `n²` factor?*  
When we form a line through point `i` and `j`, we need to check every other point `k` to see if it lies on that line – that’s an `O(n)` scan inside the `O(n)` loop, giving `O(n²)` for each mask.

---

## 🚀 How to Nail This Question in a Coding‑Interview

| Step | What recruiters want to see | How we prove it |
|------|-----------------------------|-----------------|
| **1. Brute‑Force** | Understand that the problem is NP‑hard in general, but the constraints give you a “small‑n” trick. | Mention that `n ≤ 10` → exhaustive search is fine. |
| **2. State Compression** | Map “covered points” → bitmask → O(2ⁿ) states. | Show the mask idea in code. |
| **3. Recurrence** | Pick the first uncovered point → generate all possible lines → mask‑update → recursion. | Walk through the recurrence in the blog post. |
| **4. Memoization** | Avoid recomputing the same mask. | Highlight `Map<Integer,Integer>` / `unordered_map`. |
| **5. Correctness** | Proof by induction over the number of uncovered points. | Provide a short inductive argument. |
| **6. Complexity** | `O(2ⁿ·n²)` time, `O(2ⁿ)` memory. | Show the table again. |
| **7. Edge Cases** | Degenerate lines (single point), vertical/horizontal lines, slope representation. | Use integer gcd normalisation to avoid floating‑point errors. |

> **Result** – You’ll land the “good‑for‑job” part of the interview: *You can write a clean DP, you know how to encode geometry without FP bugs, and you can justify your runtime.*

---

## 📚 Why This Blog Helps You Get a Job

| Benefit | Why it matters to recruiters |
|---------|-----------------------------|
| **LeetCode 2152** | Popular in data‑structure & graph rounds; demonstrates mastery of geometry & DP. |
| **Bitmask DP** | Shows you know state‑compression – a staple of many interview problems. |
| **Three Languages** | Highlights versatility – if you’re comfortable with Java, Python, or C++, you can adapt quickly to any tech stack. |
| **Clean Code + Comments** | Recruiters appreciate readability; you’ll be praised for production‑ready code. |
| **Time Complexity Discussion** | You’ll be ready to answer “why is this fast?” on the spot. |
| **Prepared for “Why this solution?”** | You can discuss trade‑offs (DP vs. brute‑force vs. greedy) – a good interview habit. |

---

## 🎯 Next Steps for Your Interview Prep

1. **Practice** – run the above solutions on all edge cases (`n=1`, all points vertical, etc.).  
2. **Explain your algorithm** – practice the 5‑minute “explain‑and‑draw‑on‑paper” version.  
3. **Ask a “What if n=1000?”** – show you understand the limits of this DP and when you’d need a greedy or heuristic approach.  
4. **Leverage this problem in your portfolio** – add a note that you solved it in **O(2ⁿ·n²)** and that you can adapt it to larger inputs if required.  
5. **Deploy** – commit the code to a public repo (GitHub, GitLab) and link it in your résumé / LinkedIn.  

> *If you’ve mastered a problem like this, you’re already showing the kind of algorithmic thinking that recruiters look for.*  

Happy coding! 🚀