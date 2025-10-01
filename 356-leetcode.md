---
title: LeetCode 356. Line Reflection - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The 356. Line Reflection problem – 3‑way solution + a SEO‑friendly blog post

> **LeetCode #356 – Line Reflection**  
> **Difficulty**: Medium  
> **Target language**: Java, Python, C++  
> **Goal**: Determine whether a vertical line (parallel to the *y*‑axis) exists that perfectly reflects a set of 2‑D points onto itself.

Below you’ll find:

|  | **Java** | **Python** | **C++** |
|---|---|---|---|
| **Complexity** | O(n) time, O(n) space | O(n) time, O(n) space | O(n) time, O(n) space |
| **Key idea** | Use the “center” = (min + max)/2 | Same as Java | Same as Java |
| **Why it’s fast** | Constant‑time hash look‑ups | Constant‑time hash look‑ups | Constant‑time hash look‑ups |
| **Edge‑cases** | Duplicate points, negative coordinates, integer overflow | Same | Same |

> 📌 *If you’re preparing for coding interviews, this is one of the “must‑know” geometry + hash‑set questions.*  

---

## 2.  Problem Statement (paraphrased)

Given `n` points `points[i] = [xi, yi]` (duplicates allowed), determine if a vertical line `x = c` exists such that reflecting every point across that line produces a point that also exists in the original set. In other words:

> For every `(x, y)` there must be a point `(2c – x, y)` in the set.

---

## 3.  Approach – “Good, Bad, Ugly”

| **Good** | **Bad** | **Ugly** |
|---|---|---|
| **O(n) solution** using a single pass to find `minX` and `maxX`, then a hash‑set to verify reflected pairs. | A naive O(n²) pairwise comparison quickly becomes impractical for 10⁴ points. | Attempting to store coordinates as `double` for the line can introduce rounding errors; integer arithmetic is safer. |
| **No sorting** – avoids `O(n log n)` overhead. | Relying on sorting would be fine but is an unnecessary complication for this problem. | Using a custom struct for the hash key without proper `hash`/`equals` (Java) or `operator==`/`hash` (C++) leads to subtle bugs. |
| **Clear math** – center = (min + max)/2, integer‑only. | Forgetting that `min + max` may overflow a 32‑bit `int`. | Using `float`/`double` for the center but still comparing integer coordinates can cause subtle mismatches. |

---

## 4.  Code

Below are clean, commented implementations in **Java**, **Python**, and **C++**.

---

### 4.1 Java – HashSet of Strings (easy, safe)

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public boolean isReflected(int[][] points) {
        // 1. Find min and max X
        int minX = Integer.MAX_VALUE;
        int maxX = Integer.MIN_VALUE;
        for (int[] p : points) {
            minX = Math.min(minX, p[0]);
            maxX = Math.max(maxX, p[0]);
        }

        // 2. The line must be at x = (minX + maxX) / 2
        // Use a *double* to avoid integer division truncation
        double mid = (minX + (double) maxX) / 2.0;

        // 3. Put all points into a set as a unique key "x#y"
        Set<String> set = new HashSet<>();
        for (int[] p : points) {
            set.add(p[0] + "#" + p[1]);
        }

        // 4. For each point, the reflected point must exist
        for (int[] p : points) {
            double reflectedX = 2 * mid - p[0];
            // reflectedX must be an integer for a valid reflection
            if (Math.abs(reflectedX - Math.round(reflectedX)) > 1e-9) {
                return false;
            }
            String key = ((long) Math.round(reflectedX)) + "#" + p[1];
            if (!set.contains(key)) {
                return false;
            }
        }
        return true;
    }
}
```

**Why this works**

* `mid` is computed as a `double` but we immediately check that the reflected X is an integer.
* Using `"x#y"` guarantees unique string keys even when `x` or `y` is negative.
* The algorithm is `O(n)` time and `O(n)` space.

---

### 4.2 Python – Tuple in a set

```python
class Solution:
    def isReflected(self, points: List[List[int]]) -> bool:
        # 1. min and max X
        min_x = min(p[0] for p in points)
        max_x = max(p[0] for p in points)

        # 2. Center of the line
        mid = (min_x + max_x) / 2

        # 3. Put points into a set of tuples
        point_set = {tuple(p) for p in points}

        # 4. Verify each point has its mirror
        for x, y in points:
            reflected_x = 2 * mid - x
            # the reflected point must be integral
            if int(reflected_x) != reflected_x:
                return False
            if (int(reflected_x), y) not in point_set:
                return False
        return True
```

**Key points**

* Python’s `tuple` is hashable, making `point_set` a natural choice.
* `int(reflected_x) != reflected_x` is a concise way to ensure the reflected X is an integer.
* The code is easy to read and runs in linear time.

---

### 4.3 C++ – `unordered_set` with `pair_hash`

```cpp
#include <bits/stdc++.h>
using namespace std;

struct PairHash {
    size_t operator()(const pair<long long, long long>& p) const noexcept {
        return hash<long long>()((p.first << 32) ^ p.second);
    }
};

class Solution {
public:
    bool isReflected(vector<vector<int>>& points) {
        long long minX = LLONG_MAX, maxX = LLONG_MIN;
        for (auto& p : points) {
            minX = min(minX, (long long)p[0]);
            maxX = max(maxX, (long long)p[0]);
        }

        long double mid = (minX + maxX) / 2.0L;

        unordered_set<pair<long long, long long>, PairHash> s;
        for (auto& p : points)
            s.insert({p[0], p[1]});

        for (auto& p : points) {
            long double reflected = 2 * mid - p[0];
            if (floor(reflected) != ceil(reflected)) return false; // not integer
            auto key = make_pair((long long)llround(reflected), (long long)p[1]);
            if (!s.count(key)) return false;
        }
        return true;
    }
};
```

**Highlights**

* Uses `long long` to avoid overflow when adding `minX` and `maxX`.
* A custom hash `PairHash` guarantees efficient unordered set look‑ups.
* `long double` provides enough precision for the calculation, and `llround` is used to get the integer reflected X.

---

## 5.  Edge Cases & Common Pitfalls

| **Edge Case** | **What to watch out for** | **Solution** |
|---|---|---|
| Duplicate points | The set still contains the point; no special handling needed. | Using a set automatically deduplicates. |
| Negative coordinates | String concatenation or tuple hashing handles negatives fine. | Just use the same key scheme. |
| Very large coordinates | `int` addition may overflow in Java/C++. | Promote to `long`/`long long` or `long double`. |
| Non‑integer mid | Reflecting a point across a non‑integer center will produce a non‑integer reflected X. | Check `reflectedX == round(reflectedX)` before lookup. |
| Floating point precision | `double`/`float` rounding can mis‑detect a valid reflection. | Perform the check with an epsilon (`1e-9`) or use integer arithmetic entirely (mid expressed as rational). |

---

## 6.  Complexity Analysis

| **Operation** | **Time** | **Space** |
|---|---|---|
| Compute min/max X | O(n) | O(1) |
| Build set of points | O(n) | O(n) |
| Verify reflections | O(n) | O(1) (besides the set) |
| **Total** | **O(n)** | **O(n)** |

The algorithm easily meets the constraints (`n ≤ 10⁴`). Even on the edge of `int` range, the use of 64‑bit integers in C++/Java prevents overflow.

---

## 7.  Why This Blog Helps Your Job Hunt

* **Keywords** – “Line Reflection LeetCode 356”, “Java O(n) solution”, “Python interview problem”, “C++ hash set geometry”, “job interview coding questions”.
* **Structure** – Clear sections with code, complexity, edge‑case discussion – a format recruiters love.
* **SEO** – Use of header tags, bullet points, and keyword‑rich text ensures higher search rankings.
* **Demonstrated skillset** – Shows deep understanding of hash sets, integer arithmetic, and algorithmic thinking.

If you want to impress hiring managers, showcase this solution in your portfolio or GitHub, and be ready to explain the “good, bad, ugly” discussion in a technical interview.

---

## 8.  Final Thoughts

The Line Reflection problem is a great test of **geometric insight + hash‑based verification**. The linear solution is elegant: compute the midpoint of the extreme X values, then simply check that every point’s mirror exists. Avoid pitfalls like overflow or floating‑point drift, and you’ll have a robust, interview‑ready answer.

Happy coding, and good luck landing that dream job! 🚀

---

### 9.  Reference Links

* LeetCode problem page: https://leetcode.com/problems/line-reflection/
* Java hash‑set solution (simple): https://leetcode.com/problems/line-reflection/solutions/82970/simple-java-hashset-solution-by-juanren-aihc/
* Python solution (tuple set): https://leetcode.com/problems/line-reflection/solutions/4183052/easy-to-understand-solution-with-hashmap-zz7t/
* C++ solution (unordered_set with pair hash): https://leetcode.com/problems/line-reflection/solutions/3331741/solution-by-deleted_user-0b3o/

---