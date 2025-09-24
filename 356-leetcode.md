---
title: LeetCode 356. Line Reflection - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – LeetCode 356: **Line Reflection**

> **Given** `n` points on a 2‑D plane, determine whether a vertical line
> (parallel to the `y`‑axis) exists that reflects the entire point set
> onto itself.

> **Duplicates are allowed.**

The key observation is that if such a line exists, it must pass through
the midpoint of the *smallest* and *largest* `x`‑coordinate among all
points.  
Once that candidate line is found, every point must have its mirror
point in the original set.

---

## 2.  Why Brute‑Force Fails

A naïve \(O(n^2)\) solution would try every point as a possible
reflection partner.  
With \(n \le 10^4\) this approach is far too slow (≈ 100 million
comparisons).  
Interviewers expect an \(O(n)\) or \(O(n \log n)\) solution that uses a
hash‑based data structure to look up mirror points in constant time.

---

## 3.  Optimal \(O(n)\) Solution – The “Min + Max” Trick

1. **Find the extremes**  
   * `minX` = minimum `x` in the list  
   * `maxX` = maximum `x` in the list  

   The candidate reflection line is at  
   \[
   c = \frac{minX + maxX}{2}
   \]
   (note that `c` can be fractional).

2. **Store all points in a hash set**  
   Use a tuple `(x, y)` as the key (Java: `String`, Python: `tuple`,
   C++: custom hash).

3. **Verify each point**  
   For every `(x, y)` compute its mirrored counterpart  
   \[
   (x_{\text{mirrored}}, y) = (minX + maxX - x, \; y)
   \]  
   If the mirrored point isn’t in the set → **return false**.

4. **All points matched** → **return true**.

The algorithm runs in \(O(n)\) time and uses \(O(n)\) extra memory.

---

## 4.  Code – Three Languages

> ⚙️  **Tip:**  
> All three implementations use the same idea; the only differences are
> language‑specific data‑structure choices.

### 4.1 Java

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public boolean isReflected(int[][] points) {
        if (points == null || points.length == 0) return true;

        int minX = Integer.MAX_VALUE;
        int maxX = Integer.MIN_VALUE;

        // First pass: find min and max x
        for (int[] p : points) {
            minX = Math.min(minX, p[0]);
            maxX = Math.max(maxX, p[0]);
        }

        // Build a hash set of all points
        Set<String> set = new HashSet<>();
        for (int[] p : points) {
            set.add(p[0] + "," + p[1]);          // encode as string
        }

        int sum = minX + maxX;                   // 2 * c

        // Verify all points have a mirror counterpart
        for (int[] p : points) {
            int mirrorX = sum - p[0];
            String mirror = mirrorX + "," + p[1];
            if (!set.contains(mirror)) {
                return false;
            }
        }
        return true;
    }
}
```

### 4.2 Python

```python
class Solution:
    def isReflected(self, points: List[List[int]]) -> bool:
        if not points:
            return True

        min_x = min(p[0] for p in points)
        max_x = max(p[0] for p in points)
        s = min_x + max_x          # 2 * c

        point_set = {tuple(p) for p in points}   # hashable tuples

        for x, y in points:
            if (s - x, y) not in point_set:
                return False
        return True
```

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isReflected(vector<vector<int>>& points) {
        if (points.empty()) return true;

        int minX = INT_MAX, maxX = INT_MIN;
        for (auto& p : points) {
            minX = min(minX, p[0]);
            maxX = max(maxX, p[0]);
        }
        long long sum = 1LL * minX + maxX;   // avoid overflow

        unordered_set<long long> hs;
        auto encode = [](int x, int y) {
            return (static_cast<long long>(x) << 32) ^ (y & 0xffffffffLL);
        };

        for (auto& p : points)
            hs.insert(encode(p[0], p[1]));

        for (auto& p : points) {
            long long mirrorX = sum - p[0];
            if (!hs.count(encode(static_cast<int>(mirrorX), p[1])))
                return false;
        }
        return true;
    }
};
```

> ⚠️  **Why the 64‑bit encode in C++?**  
> It packs two 32‑bit integers into one 64‑bit key so that the unordered
> set can be used without a custom hash object.

---

## 5.  Edge Cases & Testing

| # | Input | Expected | Reason |
|---|-------|----------|--------|
| 1 | `[[1,1],[-1,1]]` | `true` | Line `x=0` works |
| 2 | `[[1,1],[-1,-1]]` | `false` | No vertical line reflects |
| 3 | `[[0,0],[0,0],[0,0]]` | `true` | All points identical |
| 4 | `[[2,3],[4,3]]` | `true` | Line `x=3` |
| 5 | `[[1,2],[3,4],[5,2]]` | `false` | Asymmetry |

Run a full test harness that covers duplicate points, negative coordinates,
and the extreme bounds (`-10^8` to `10^8`).

---

## 6.  Complexity Analysis

| Aspect | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | \(O(n)\) | \(O(n)\) | \(O(n)\) |
| **Space** | \(O(n)\) | \(O(n)\) | \(O(n)\) |
| **Explanation** | Single pass to collect min/max, one set, one pass to check | Same | Same |

All languages use a single hash set lookup per point, giving linear
performance even for the maximum constraints.

---

## 7.  “Good, Bad, & Ugly” – What Interviewers Want

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic Idea** | 2‑pointer / min‑max trick → \(O(n)\) | Brute‑force \(O(n^2)\) | Using sorting + binary search without hashing, still \(O(n \log n)\) but unnecessary |
| **Data Structures** | HashSet (or unordered_set) | List or array with linear search | Custom hash collisions without handling |
| **Code Clarity** | Clear variable names, short helper `encode` | Inline lambda for `encode` | Missing comments, magic numbers |
| **Edge‑Case Handling** | Duplicates, negative values, single point | None | Overflows, floating‑point inaccuracies |
| **Readability** | Comments, concise | None | Obscure logic, no documentation |
| **Testing** | Unit tests, boundary checks | No tests | Test harness missing |

> **Tip:** Always explain your choice of data structure in an interview.  
> “I chose a hash set because I need constant‑time look‑ups; this keeps
> the solution linear.” This demonstrates both technical skill and
> communication ability.

---

## 8.  SEO‑Optimized Blog Article

### Title  
**“LeetCode 356 – Line Reflection: Master the Vertical Symmetry Problem in Java, Python & C++”**

### Meta Description  
Discover a clean \(O(n)\) solution to LeetCode 356 “Line Reflection”. Read the step‑by‑step guide, code snippets in Java, Python, and C++, and interview‑ready tips.

### Keywords  
LeetCode 356, line reflection, vertical line symmetry, hash set solution, O(n) algorithm, coding interview, Java, Python, C++, data structures, job interview tips

### Headings & Content

| Hn | Heading | Summary |
|----|---------|---------|
| H1 | LeetCode 356 – Line Reflection | Problem statement and why it matters |
| H2 | The Brute‑Force Pitfall | Why \(O(n^2)\) is a dead end |
| H2 | The Min‑Max Trick | How the reflection line is determined |
| H2 | The Hash Set Magic | Constant‑time mirror lookup |
| H2 | Code Walkthrough – Java | Full implementation with comments |
| H2 | Code Walkthrough – Python | Full implementation with comments |
| H2 | Code Walkthrough – C++ | Full implementation with comments |
| H2 | Edge Cases & Test Cases | What to test for, sample tests |
| H2 | Complexity Analysis | Big‑O, memory, and practical impact |
| H2 | Interview Tips – Good, Bad & Ugly | What recruiters look for |
| H3 | Bonus: Alternative Sorting Solution | If hashing is disallowed |
| H2 | Final Thoughts | How mastering this problem boosts your résumé |

### Sample Excerpt

> **H2: The Min‑Max Trick**  
> In a vertical reflection, every pair of mirrored points lies on opposite sides of a common line. The only line that can satisfy this for all points is the one that passes through the midpoint of the leftmost and rightmost points. Hence, the candidate line is simply
> \[
> c = \frac{minX + maxX}{2}.
> \]
> This observation reduces the problem to a single pass over the data.

### Call‑to‑Action

> **Want to ace your next coding interview?**  
> Practice this problem and share your own solutions on GitHub. Tag your posts with `#LeetCode356 #CodingInterview` so recruiters can spot your expertise.

---

## 9.  Closing Advice – Getting the Job

1. **Show the Full Pipeline** – From problem statement → algorithm → code → tests. Recruiters love clear problem‑solving flow.
2. **Talk About Complexity** – Always mention time/space trade‑offs.
3. **Highlight Edge‑Case Handling** – Duplicates, negatives, extreme bounds are interview test‑cases.
4. **Link to Your Portfolio** – Post the solution on GitHub and reference it in your résumé.
5. **Discuss Interview‑Friendly Points** – Use hash sets, explain why you chose them, and how you’d adapt if constraints changed.

By mastering **LeetCode 356** and presenting the solution with clean, language‑specific code, you demonstrate algorithmic thinking, data‑structure expertise, and the communication skills recruiters seek in a software engineer. Good luck! 🚀