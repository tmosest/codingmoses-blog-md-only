---
title: LeetCode 3143. Maximum Points Inside the Square - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 1.  Problem Overview – “Maximum Points Inside the Square”

| LeetCode ID | Difficulty | Tags |
|-------------|------------|------|
| **3143** | Medium | Array, Greedy, Sorting, HashMap |

**Goal**  
You are given `points[i] = [x, y]` (distinct coordinates) and a string `s` where `s[i]` is the tag of point `i`.  
A **valid square** is:

* centered at the origin `(0, 0)`
* edges parallel to the axes
* **no two points inside the square share the same tag**

Return the maximum number of points that can be covered by any valid square.

The side length of the square may be zero.

---

## 🎯 2.  Why the Greedy “min‑vs‑second‑min” Trick Works

| Observation | Reason |
|-------------|--------|
| The square is defined by the *maximum* absolute coordinate among the included points (let `R = max(|x|, |y|)`). | The square’s half‑side length must be at least `R`; everything inside satisfies `|x|≤R` and `|y|≤R`. |
| If a tag occurs twice with distances `d1 ≤ d2`, we can never include both points in the same square because the square must be large enough for the farther point (`d2`). | Therefore the *second smallest* distance for each tag determines the first “conflict” that would invalidate a square. |
| For all tags where the *smallest* distance is strictly smaller than the *second smallest* distance, we can safely include the point with the smallest distance in our final square. | The square’s radius can be set to the largest such minimal distance – all chosen points fit, and no tag is duplicated. |

Thus the answer is the number of tags for which  
`minDistance[tag] < secondMinDistance[tag]`.

The algorithm is **O(n)** time and **O(m)** space (`m` = number of distinct tags).

---

## 🧠 3.  Algorithm (Pseudo)

```
minDist   = empty map  (tag → first smallest distance)
secondMin = +∞
count = 0

for each point (x, y) with tag t:
    d = max(|x|, |y|)          // distance from origin to the point
    if t not in minDist:
        minDist[t] = d
    else if d < minDist[t]:
        secondMin = min(secondMin, minDist[t])
        minDist[t] = d
    else:
        secondMin = min(secondMin, d)

for each tag in minDist:
    if minDist[tag] < secondMin:
        count += 1

return count
```

---

## 📄 4.  Code

Below are clean, idiomatic solutions for **Java**, **Python**, and **C++**.

---

### 4.1 Java (Java 17)

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int maxPointsInsideSquare(int[][] points, String s) {
        Map<Character, Integer> minDist = new HashMap<>();
        int secondMin = Integer.MAX_VALUE;
        int count = 0;

        for (int i = 0; i < points.length; i++) {
            int d = Math.max(Math.abs(points[i][0]), Math.abs(points[i][1]));
            char tag = s.charAt(i);

            if (!minDist.containsKey(tag)) {
                minDist.put(tag, d);
            } else if (d < minDist.get(tag)) {
                secondMin = Math.min(secondMin, minDist.get(tag));
                minDist.put(tag, d);
            } else {
                secondMin = Math.min(secondMin, d);
            }
        }

        for (int d : minDist.values()) {
            if (d < secondMin) {
                count++;
            }
        }
        return count;
    }

    public static void main(String[] args) {
        int[][] pts = {{2, 2}, {-1, -2}};
        String tags = "ab";
        System.out.println(new Solution().maxPointsInsideSquare(pts, tags)); // 2
    }
}
```

---

### 4.2 Python (Python 3.10+)

```python
from typing import List, Dict

class Solution:
    def maxPointsInsideSquare(self, points: List[List[int]], s: str) -> int:
        min_dist: Dict[str, int] = {}
        second_min = float('inf')

        for (x, y), tag in zip(points, s):
            d = max(abs(x), abs(y))
            if tag not in min_dist:
                min_dist[tag] = d
            elif d < min_dist[tag]:
                second_min = min(second_min, min_dist[tag])
                min_dist[tag] = d
            else:
                second_min = min(second_min, d)

        return sum(1 for d in min_dist.values() if d < second_min)
```

---

### 4.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxPointsInsideSquare(vector<vector<int>>& points, string s) {
        unordered_map<char, int> minDist;
        int secondMin = INT_MAX;
        int count = 0;

        for (size_t i = 0; i < points.size(); ++i) {
            int d = max(abs(points[i][0]), abs(points[i][1]));
            char tag = s[i];

            if (!minDist.count(tag)) {
                minDist[tag] = d;
            } else if (d < minDist[tag]) {
                secondMin = min(secondMin, minDist[tag]);
                minDist[tag] = d;
            } else {
                secondMin = min(secondMin, d);
            }
        }

        for (auto &kv : minDist) {
            if (kv.second < secondMin) ++count;
        }
        return count;
    }
};
```

All three solutions run in **linear time** and use a single hash‑map for the tags.

---

## 🔍 5.  Edge‑Case Checklist

| Edge Case | Expected Behavior |
|-----------|-------------------|
| No points (`points.length == 0`) | Return `0`. |
| All tags unique | `secondMin` stays `∞`; answer = number of tags (`points.length`). |
| All points have the *same* tag | Only the nearest point can be taken; answer `1`. |
| Points with distance `0` | They are perfectly inside a zero‑size square; handled the same way. |

---

## ⚠️ 6.  Common Pitfalls (“The Bad & The Ugly”)

1. **Misinterpreting side length**  
   *Many candidates treat the side length as the *full* side rather than the half‑side (`R`).*  
   **Fix:** Work with `R = max(|x|, |y|)`; the actual square side is `2R`.

2. **Stopping at the first duplicate**  
   *A simple “sorted by R and break on duplicate” gives wrong answers when an early duplicate blocks two later unique tags.*  
   **Fix:** Use the min/second‑min trick described above.

3. **Overflow on distance**  
   *If coordinates can be `±10^9`, `max(abs(x),abs(y))` fits in `int`.  Avoid `long` unless you know the range extends beyond `2^31‑1`.*  
   **Fix:** Use `int` if the constraints are `|x|,|y| ≤ 10^9`.  Otherwise switch to `long`.

4. **Tag as string instead of char**  
   *`s[i]` is a single character, not a substring.*  
   **Fix:** Use `s.charAt(i)` (Java) / `s[i]` (Python) / `s[i]` (C++).

---

## 📦 7.  Test‑Driven Verification

```text
Input:
points = [[2,2], [-1,-2]]
s      = "ab"
Output: 2
```

```text
Input:
points = [[-1,-2],[3,0],[0,3]]
s      = "aaB"
Output: 1   // only the nearest 'a' can be taken
```

```text
Input:
points = [[0,0],[1,1],[2,2]]
s      = "abc"
Output: 3   // all tags unique, square side 4 fits all
```

Run the code in any language and verify the outputs.  

---

## 📚 8.  The Good, The Bad, & The Ugly of Interview Solutions

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Complexity** | O(n) greedy, easy to prove | O(n log n) sorting or binary search – heavier on explanation | O(n²) brute‑force, accepted only on toy data |
| **Readability** | Single pass, clear variable names | Nested loops, confusing temp maps | Hard‑to‑read, mixed data structures |
| **Robustness** | Handles zero‑size squares, negative coordinates | Fails when duplicate tags appear early but later points could be chosen | Works only if constraints guarantee no duplicate tags |

**Takeaway:**  
For a *software‑engineering* interview, you want the *cleanest, fastest, and easiest to explain* solution – the **min‑vs‑second‑min** greedy is exactly that.

---

## 🚀 9.  Interview‑Ready Tips

1. **Explain the distance metric (`R`) first.**  
   “Because the square is axis‑aligned and centered at the origin, any point inside must satisfy `|x|≤R` and `|y|≤R`. So the square is completely determined by the maximum of these two absolute values.”

2. **Show the conflict logic with a small example** (the `a(1), a(2), b(3)` counter‑example).  
   This demonstrates you truly understand why a tag cannot appear twice.

3. **Mention the two‑pass nature** (min → secondMin → count) and give the complexity up front.

4. **Optional bonus:**  
   If the interviewer asks for an *actual* square side length, simply output `2 * maxMinDist` where `maxMinDist` is the largest `minDist` that satisfies the condition.

---

## 🎉 10.  Summary

* The problem reduces to choosing one point per tag such that the **maximum** distance among the chosen points is minimized.
* The greedy “smallest vs second‑smallest” trick gives the optimum in linear time.
* Clean implementations exist in Java, Python, and C++ – all identical in logic.
* Be ready to explain the intuition and edge‑case handling – that’s what *interviewers* look for.

Good luck on your next coding interview – you’ve got the *golden* algorithm! 🍀

---  

**Keywords**: Leetcode, coding interview, maximum points inside square, Java interview, Python interview, C++ interview, greedy algorithm, time complexity, space complexity, software engineering job, interview prep.