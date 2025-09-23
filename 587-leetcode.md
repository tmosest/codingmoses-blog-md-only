---
title: LeetCode 587. Erect the Fence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 587. Erect the Fence – Three‑Way Solution & a SEO‑Optimized Blog Post

> **TL;DR**  
> *Implement a convex‑hull (Monotonic‑Chain) that keeps collinear points on the boundary.*  
> *We provide Java, Python, and C++ code, and a full‑blown blog article that explains the “good, the bad and the ugly” of this problem, packed with interview‑friendly SEO hooks.*

---

## 1. Problem Recap (LeetCode 587)

> **Goal** – Given an array of tree coordinates, return *every* tree that lies on the perimeter of the minimum‑length fence that encloses all trees.  
> **Special rule** – If the fence is a straight line (all trees collinear) return all trees.

Constraints:  
- 1 ≤ `trees.length` ≤ 3000  
- 0 ≤ `xi, yi` ≤ 100  
- All coordinates are unique.

---

## 2. High‑Level Idea

The fence that covers all points with the smallest perimeter is the **convex hull** of the point set.  
The only nuance is that *collinear points that lie on the hull’s edges must also be returned*.  
This is a classic variation of the convex‑hull problem.

We’ll use the **Monotonic Chain (Andrew’s) algorithm** – O(N log N) time, O(N) space – because it is short, fast, and easy to write in all three languages.

---

## 3. Algorithm Details

1. **Sort** all points lexicographically by x, then by y.
2. **Build the lower hull**:
   * Walk through sorted points.  
   * While the last two points of the hull together with the current point make a **right turn** (`cross <= 0` for *keeping* collinear points) – pop the last point.
   * Push the current point.
3. **Build the upper hull** in the same way, but walk through the sorted points in reverse.
4. **Combine** lower and upper hulls (drop the last point of each to avoid duplication).
5. **Remove duplicates** (especially when all points are collinear – both hulls will contain the same set) – use a `HashSet` (Java), `set` of tuples (Python), or `unordered_set` with a custom hash (C++).

**Cross Product**  
For points `A(x1,y1)`, `B(x2,y2)`, `C(x3,y3)`:
```
cross = (x2-x1)*(y3-y1) - (y2-y1)*(x3-x1)
```
* `> 0` → left turn  
* `< 0` → right turn  
* `== 0` → collinear

---

## 4. Code

### 4.1 Java (Java 17+)

```java
import java.util.*;

public class Solution {

    // Cross product of AB and AC
    private long cross(int[] A, int[] B, int[] C) {
        return (long)(B[0] - A[0]) * (C[1] - A[1]) -
               (long)(B[1] - A[1]) * (C[0] - A[0]);
    }

    public int[][] outerTrees(int[][] trees) {
        if (trees == null || trees.length == 0) return new int[0][0];
        Arrays.sort(trees, (p, q) -> {
            if (p[0] != q[0]) return Integer.compare(p[0], q[0]);
            return Integer.compare(p[1], q[1]);
        });

        List<int[]> lower = new ArrayList<>();
        for (int[] pt : trees) {
            while (lower.size() >= 2 &&
                   cross(lower.get(lower.size() - 2),
                         lower.get(lower.size() - 1), pt) < 0) {
                lower.remove(lower.size() - 1);
            }
            lower.add(pt);
        }

        List<int[]> upper = new ArrayList<>();
        for (int i = trees.length - 1; i >= 0; --i) {
            int[] pt = trees[i];
            while (upper.size() >= 2 &&
                   cross(upper.get(upper.size() - 2),
                         upper.get(upper.size() - 1), pt) < 0) {
                upper.remove(upper.size() - 1);
            }
            upper.add(pt);
        }

        // Merge lower and upper, avoid duplicate endpoints
        Set<List<Integer>> set = new HashSet<>();
        for (int[] p : lower) set.add(Arrays.asList(p[0], p[1]));
        for (int[] p : upper) set.add(Arrays.asList(p[0], p[1]));

        int[][] res = new int[set.size()][2];
        int i = 0;
        for (List<Integer> lst : set) {
            res[i][0] = lst.get(0);
            res[i][1] = lst.get(1);
            i++;
        }
        return res;
    }
}
```

> **Why use `List<Integer>` in the set?**  
> Primitive arrays can’t be hashed properly in Java; wrapping into a `List` gives us a stable `equals`/`hashCode`.

---

### 4.2 Python 3

```python
from typing import List

class Solution:
    def outerTrees(self, trees: List[List[int]]) -> List[List[int]]:
        if not trees:
            return []

        trees.sort()                     # lexicographic: x then y

        def cross(o, a, b):
            return (a[0]-o[0]) * (b[1]-o[1]) - (a[1]-o[1]) * (b[0]-o[0])

        lower = []
        for p in trees:
            while len(lower) >= 2 and cross(lower[-2], lower[-1], p) < 0:
                lower.pop()
            lower.append(p)

        upper = []
        for p in reversed(trees):
            while len(upper) >= 2 and cross(upper[-2], upper[-1], p) < 0:
                upper.pop()
            upper.append(p)

        # Combine and dedupe
        hull = set(tuple(pt) for pt in lower[:-1] + upper[:-1])
        return [list(pt) for pt in hull]
```

> **Note** – We drop the last point of each half‑hull to avoid duplication of the endpoints (`lower[-1] == upper[-1]`).

---

### 4.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long cross(const array<int,2>& A,
                    const array<int,2>& B,
                    const array<int,2>& C) {
        return 1LL*(B[0]-A[0])*(C[1]-A[1]) -
               1LL*(B[1]-A[1])*(C[0]-A[0]);
    }

    vector<vector<int>> outerTrees(vector<vector<int>>& trees) {
        if (trees.empty()) return {};

        sort(trees.begin(), trees.end(),
             [](const vector<int>& p, const vector<int>& q){
                 if (p[0] != q[0]) return p[0] < q[0];
                 return p[1] < q[1];
             });

        vector<array<int,2>> lower, upper;
        for (auto &p : trees) {
            array<int,2> pt{p[0], p[1]};
            while (lower.size() >= 2 &&
                   cross(lower[lower.size()-2], lower.back(), pt) < 0)
                lower.pop_back();
            lower.push_back(pt);
        }

        for (auto it = trees.rbegin(); it != trees.rend(); ++it) {
            array<int,2> pt{(*it)[0], (*it)[1]};
            while (upper.size() >= 2 &&
                   cross(upper[upper.size()-2], upper.back(), pt) < 0)
                upper.pop_back();
            upper.push_back(pt);
        }

        // Remove last element of each to avoid duplicates
        lower.pop_back();
        upper.pop_back();

        unordered_set<long long> seen;
        vector<vector<int>> res;

        auto add = [&](const array<int,2>& pt){
            long long key = 1LL*pt[0]*101 + pt[1]; // 0<=x,y<=100 -> unique
            if (seen.insert(key).second) {
                res.push_back({pt[0], pt[1]});
            }
        };

        for (auto &pt : lower) add(pt);
        for (auto &pt : upper) add(pt);

        return res;
    }
};
```

> **Key trick** – Compress each point into a single `long long` key (`x*101 + y`) because `x` and `y` are ≤ 100. This lets us use `unordered_set<long long>` for O(1) deduplication.

---

## 5. Blog Post – “The Good, The Bad, and The Ugly” of LeetCode 587

### Title
> **Erect the Fence (LeetCode 587) – The Complete Guide (Java / Python / C++)**  
> *Why convex hull is a must‑know for data‑structure interviews, plus a step‑by‑step tutorial.*

*(Add an engaging featured image of a convex hull in 2‑D, the LeetCode logo, and the code snippets highlighted.)*

---

### 1. Introduction

> “I’m interviewing for a software engineer position, but I can’t seem to get past the geometry questions.”  
> This sentence is as common as a broken pencil in a hiring panel.  
> The Erect the Fence problem is a *golden ticket* – it tests two concepts simultaneously:  
> - **Sorting** (to linearize the data)  
> - **Geometric invariants** (cross product, orientation).  

We’ll show you the **fastest, cleanest, and most interview‑friendly** solution in three languages.

---

### 2. Why Convex Hull? (The Good)

- **Universal concept** – Appears in computer graphics, robotics, GIS, even game AI.  
- **Linear‑time or near‑linear** – Andrew’s algorithm runs in O(N log N), beating the brute‑force O(N²) perimeter check.  
- **Rich test coverage** – Handles all corner cases (all points collinear, single point, convex vs. concave arrangements).

> **Interview hook** – “Describe a situation where you had to optimize the path of a robot vacuum that had to cover all rooms of a house.”  
> The recruiter expects you to mention “convex hull” almost immediately.

---

### 3. Implementation Walk‑through (The Good)

1. **Sort the points** – The first O(N log N) step.  
2. **Use a stack (or vector)** to maintain the hull as you iterate.  
3. **Cross product test** – A single line of code determines if a point should stay or be removed.  
4. **Keep collinear points** – Just change `cross < 0` to `cross <= 0`.  
5. **Deduplicate** – In Java, use a `Set<List<Integer>>`; in Python, `set(tuple(pt))`; in C++, a hashed integer key.  

*Why this algorithm beats the classic Graham Scan?*  
- Less code, fewer edge‑case bugs, easier to explain verbally.  

---

### 4. Common Pitfalls (The Bad)

| Pitfall | Why it’s bad | Fix |
|---------|--------------|-----|
| Using `< 0` for pop condition | Removes collinear points on the hull’s edges. | Use `<= 0` only if you *need* to keep them, otherwise `>= 0`. |
| Forgetting to dedupe when all points are collinear | Returns duplicate trees or misses some. | Use a `Set` or `unordered_set` after merging lower & upper hulls. |
| Using integer overflow in cross product | Coordinates ≤ 100, but in generic problems they might be 10⁹. | Cast to `long long` / `long` before multiplication. |
| Mixing up left/right turn logic | Flips the hull orientation, leading to incorrect shape. | Stick to the convention: `cross > 0` = left, `< 0` = right. |

**Tip** – Write a small `cross` helper with clear comments; a single typo will break the whole solution.

---

### 5. The “Ugly” – When Interviewers Push You Further

1. **All Points Collinear** – Your algorithm must handle this edge case gracefully.  
   *Solution:* After building both hulls, convert to a set; no need for separate logic.

2. **Large Input Range** – Some interviewers ask “What if `xi, yi` could be up to 10⁹?”  
   *Solution:* Use `long long` for cross product, avoid integer overflow, and change the deduplication key to a hash pair.

3. **Memory Constraints** – When memory is limited, you can build the hull in place and reuse the input array.  
   *Python/C++:* Append hull indices to a new list, then map back.  
   *Java:* Use an `int[][]` result and fill it while iterating.

4. **Runtime Limits** – The O(N log N) algorithm passes easily with N = 3000.  
   If you need to optimize further, you can pre‑sort by integer values and use counting sort (O(N)) because coordinates are small.

---

### 6. How to Explain It on a Whiteboard

1. **Draw** the sorted list of points horizontally.  
2. **Show** the lower hull being built – each pop represents a “corner” being smoothed out.  
3. **Mirror** for the upper hull.  
4. **Combine** – Visualize the “upper” as a flipped version of the lower.  
5. **Point out** why collinear points on edges stay – emphasize the cross‑product condition.

*Practice*: Try to explain this in 3 minutes – you’ll find you can do it naturally after you’ve written the code a few times.

---

### 7. Take‑away Cheat Sheet

| Language | Sorting | Cross Product | Hull Building | Dedupe |
|----------|---------|---------------|---------------|--------|
| Java | `Arrays.sort(trees, cmp)` | `long cross(int[],int[],int[])` | `ArrayList<int[]>` | `HashSet<List<Integer>>` |
| Python | `trees.sort()` | `def cross(o,a,b)` | `list` for halves | `set(tuple)` |
| C++ | `sort(begin,end,cmp)` | `long long cross(array)` | `vector<array<int,2>>` | `unordered_set<long long>` (key = x*101+y) |

> **Key:** Use the *same* cross‑product formula across languages. Keep the condition `cross < 0` for “right turn” and keep collinear points.

---

### 8. Final Words

> Geometry questions like Erect the Fence are *not* about being a mathematician; they’re about proving you can handle **sets of data in two dimensions**, apply a robust algorithm, and write clean, bug‑free code.  
> Master the convex hull once and you’ll win not just this problem, but a *whole class* of interview questions.

*Add a “Want more? Check out my complete repository of data‑structure interview solutions” link and an email or contact form.*

---

### 9. SEO Meta‑Data (for Recruiter Robots)

| Field | Value |
|-------|-------|
| Keywords | “LeetCode 587”, “Erect the Fence solution”, “convex hull interview”, “Java convex hull”, “Python geometry problem”, “C++ convex hull”, “data structure interview questions”, “algorithm interview preparation” |
| Description | “Discover the fastest Java/Python/C++ solution for LeetCode 587 – Erect the Fence. Learn convex hull fundamentals and get a full walkthrough with code.” |
| Title Tag | “Erect the Fence LeetCode 587 – Complete Java/Python/C++ Guide” |
| OG Image | `https://example.com/assets/convex-hull.png` |
| Twitter Card | `summary_large_image` |

---

## 6. How to Use This Blog in Your Portfolio

- **Pin it on LinkedIn** with the headline “Solved Erect the Fence in 4 languages – Convex Hull 101”.  
- **Embed** the code snippets on your GitHub README.  
- **Create a short video** (3 minutes) of you solving the problem live, then upload to YouTube with the same title – YouTube’s algorithm loves “how to solve” videos.  
- **Add a quiz** at the end of the article (“Given points X, Y, Z, which is on the hull?”) – encourages engagement.

---

## 7. Closing Thought

> Geometry may feel intimidating, but the underlying patterns are simple.  
> By mastering the Monotonic Chain algorithm once, you can confidently solve Erect the Fence, its variations, and a multitude of other interview geometry puzzles.

Happy coding, and may your fences always be *tight* and *efficient*! 🚀

--- 

**End of Post**