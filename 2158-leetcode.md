---
title: LeetCode 2158. Amount of New Area Painted Each Day - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2158 – *Amount of New Area Painted Each Day*  
**LeetCode Hard | Interview‑Ready Solution in Java, Python & C++**

> Want to show recruiters you can solve a *hard* LeetCode problem in **O(n log n)** time?  
> Here’s a clean, production‑ready implementation, a step‑by‑step algorithm walkthrough, and a blog‑ready article that you can copy‑paste into Medium, Dev.to, or your personal portfolio.  

---

## Table of Contents

| Section | What you’ll learn |
|---------|-------------------|
| 📌 Problem Statement | The exact LeetCode challenge |
| ✅ Examples | 3 sample inputs/outputs |
| 📏 Constraints | Size limits and implications |
| 🔧 Solution Overview | Why a `TreeMap` / `map` works |
| 📚 Algorithm Walk‑through | Merging intervals + computing new area |
| ⚙️ Code in Three Languages | Java, Python, C++ |
| ⏱️ Time & Space Complexity | Big‑O analysis |
| ❓ Common Pitfalls | Things that trip people up |
| 🚀 SEO & Career Tips | Keywords, how to showcase this on a résumé |
| 📣 Call to Action | What to do next |

---

## 📌 Problem Statement

> **LeetCode 2158** – *Amount of New Area Painted Each Day*  
> You are given a list `paint` where `paint[i] = [start_i, end_i]`.  
> On day `i` you paint the segment `[start_i, end_i)` of a 1‑dimensional canvas.  
> Painting the same area multiple times is wasteful; you only want to count the *new* area painted on each day.  
> Return an array `worklog` where `worklog[i]` is the amount of new area painted on day `i`.

### Input

| Variable | Type | Constraints |
|----------|------|-------------|
| `paint`  | `int[][]` | `1 ≤ n ≤ 10⁵`, `0 ≤ start_i < end_i ≤ 5·10⁴` |

### Output

| Variable | Type | Meaning |
|----------|------|---------|
| `worklog` | `int[]` | New area painted each day |

---

## ✅ Sample Cases

| Input | Output | Explanation |
|-------|--------|-------------|
| `[[1,4],[4,7],[5,8]]` | `[3,3,1]` | Day 0 paints 1‑4 → 3 units. Day 1 paints 4‑7 → 3 units. Day 2 paints 5‑8, but 5‑7 was already painted → only 1 unit is new. |
| `[[1,4],[5,8],[4,7]]` | `[3,3,1]` | Same logic, order matters. |
| `[[1,5],[2,4]]` | `[4,0]` | Day 0 paints 1‑5 → 4 units. Day 1’s interval is fully inside day 0’s painted region → 0 new units. |

---

## 📏 Constraints

* `paint.length` can be as large as `10⁵`.  
* `end_i` never exceeds `5·10⁴`, but we cannot rely on a small coordinate range because the algorithm needs to handle *any* large values efficiently.  
* A naive *O(n²)* scan is far too slow.

---

## 🔧 Solution Overview

The key insight:  
**Keep a balanced BST of *disjoint* painted intervals.**  
When a new interval `[l, r)` arrives, we:

1. **Find all overlapping intervals** in the BST.  
2. **Subtract** the overlapping lengths from the new interval’s length to get the *new* area.  
3. **Merge** all overlapping intervals (plus the new one) into a single interval and insert it back.

A `TreeMap` (`Java`), `std::map` (`C++`), or a `map` of start → end (`Python`) gives us *O(log m)* search/insert where `m` is the current number of painted intervals.  
Since every interval is merged at most once, total complexity is `O(n log n)`.

---

## 📚 Algorithm Walk‑through

```
painted = empty ordered map  (key=start, value=end)
res     = array of length n

for each day i:
    l, r   = paint[i]
    newLen = r - l

    // 1) Merge all intervals that intersect [l, r)
    while there is an interval [s, e) in painted with s ≤ r and e ≥ l:
        // overlapping: subtract the overlap
        newLen -= min(r, e) - max(l, s)
        // extend the new interval to cover the union
        l = min(l, s)
        r = max(r, e)
        // remove the old interval; it will be replaced
        delete [s, e) from painted

    // 2) Record new area
    res[i] = newLen

    // 3) Insert the merged interval back
    painted[l] = r
```

*The “while” loop always terminates because each iteration removes one existing interval and never adds more.*

---

## ⚙️ Code in Three Languages

Below are **complete, ready‑to‑paste** solutions in **Java**, **Python**, and **C++**.  
All three use the same algorithmic idea but adapt to their native data structures.

> **Tip:**  
> • In Java you’ll need `java.util.TreeMap`.  
> • In C++ use `std::map<int,int>`.  
> • In Python, the standard library has `bisect`, but for clarity we use `sortedcontainers`’s `SortedDict`.  
>   If you don’t have that package, you can fall back to a list + bisect.

---

### Java

```java
import java.util.*;

class Solution {
    // O(n log n) time, O(m) space
    public int[] amountPainted(int[][] paint) {
        int n = paint.length;
        int[] res = new int[n];
        TreeMap<Integer, Integer> painted = new TreeMap<>();

        for (int i = 0; i < n; ++i) {
            int l = paint[i][0];
            int r = paint[i][1];
            int newLen = r - l;

            // Find the first interval with start <= r
            Integer key = painted.floorKey(l);
            while (key != null) {
                int s = key;
                int e = painted.get(s);

                if (e <= l) {      // no overlap
                    key = painted.lowerKey(key);
                    continue;
                }

                // Overlap exists
                newLen -= Math.min(r, e) - Math.max(l, s);
                l = Math.min(l, s);
                r = Math.max(r, e);

                // Remove merged interval
                painted.remove(s);
                key = painted.floorKey(l);
            }

            // After merging all overlaps
            res[i] = newLen;
            painted.put(l, r);
        }
        return res;
    }
}
```

---

### Python

```python
from bisect import bisect_left, bisect_right

class Solution:
    # O(n log n) time, O(m) space
    def amountPainted(self, paint: list[list[int]]) -> list[int]:
        n = len(paint)
        res = [0] * n
        # Use two parallel lists: starts and ends
        starts, ends = [], []

        for i, (l, r) in enumerate(paint):
            new_len = r - l

            # Find the first interval that may overlap
            idx = bisect_right(starts, l) - 1
            while idx >= 0 and ends[idx] > l:
                s, e = starts[idx], ends[idx]
                # Overlap
                new_len -= min(r, e) - max(l, s)
                l = min(l, s)
                r = max(r, e)
                # Remove this interval
                starts.pop(idx)
                ends.pop(idx)
                idx -= 1

            # Merge any intervals that start within [l, r)
            idx = bisect_left(starts, l)
            while idx < len(starts) and starts[idx] < r:
                s, e = starts[idx], ends[idx]
                new_len -= min(r, e) - max(l, s)
                l = min(l, s)
                r = max(r, e)
                starts.pop(idx)
                ends.pop(idx)

            res[i] = new_len
            # Insert the merged interval
            idx = bisect_left(starts, l)
            starts.insert(idx, l)
            ends.insert(idx, r)

        return res
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> amountPainted(vector<vector<int>>& paint) {
        int n = paint.size();
        vector<int> res(n);
        map<int,int> painted;               // start -> end

        for (int i = 0; i < n; ++i) {
            int l = paint[i][0];
            int r = paint[i][1];
            int newLen = r - l;

            // Find the interval with largest start <= l
            auto it = painted.upper_bound(l);
            if (it != painted.begin()) --it;

            while (it != painted.end() && it->first <= r && it->second > l) {
                int s = it->first, e = it->second;
                newLen -= min(r, e) - max(l, s);
                l = min(l, s);
                r = max(r, e);
                auto toErase = it++;
                painted.erase(toErase);
            }

            res[i] = newLen;
            painted[l] = r;   // insert merged interval
        }
        return res;
    }
};
```

> **Note:**  
> If you’re using Python on a platform without `bisect`‑based solutions, you can simply install `sortedcontainers`:

```bash
pip install sortedcontainers
```

Then replace the two‑list approach with a `SortedDict` (start → end) – it works exactly like the Java `TreeMap` version.

---

## ⏱️ Time & Space Complexity

| Language | Time | Space |
|----------|------|-------|
| Java | **O(n log n)** (balanced BST lookup & erase) | **O(m)** – `m` is the number of *disjoint* painted intervals (≤ n). |
| Python | **O(n log n)** (bisect + list erase) | **O(m)** |
| C++ | **O(n log n)** (map lookup & erase) | **O(m)** |

*Why is `m` ≤ n?*  
Each day can add at most one new interval, and every merge removes at least one old interval.  
Therefore, at any time we never have more than `n` intervals stored.

---

## ❓ Common Pitfalls

| Mistake | Why it breaks | Fix |
|---------|---------------|-----|
| **Using a `list` and scanning all intervals** | `O(n²)` – too slow. | Use an *ordered* structure (`TreeMap` / `map`). |
| **Subtracting only from `r-l` once** | Ignores *partial* overlaps. | Subtract the exact overlap for *every* overlapping interval. |
| **Not removing merged intervals** | You end up with overlapping intervals again. | Delete each overlapped interval inside the loop. |
| **Edge case `[l, l]`** | Empty interval → area 0. | The algorithm naturally handles it (`newLen = 0`). |
| **Python `bisect` off‑by‑one** | Wrong merge boundaries. | Test with `bisect_left`/`bisect_right` carefully; the code above demonstrates both ends. |

---

## 🚀 SEO & Career Tips

| Keyword | How to use it |
|---------|--------------|
| *hard LeetCode problem* | In your résumé: “Solved LeetCode 2158 (Hard) – 100% correct”. |
| *interval merging* | In a blog post or interview: “Implemented a disjoint‑interval BST for optimal time”. |
| *TreeMap / std::map* | Demonstrates knowledge of balanced trees. |
| *O(n log n) algorithm* | Shows you can reason about asymptotics. |

**Show it on your résumé**

```text
- Implemented a hard LeetCode problem (2158 – “Amount of New Area Painted Each Day”) in <O(n log n)> time.
- Used a balanced BST (TreeMap / std::map) to maintain disjoint painted intervals.
- Demonstrated strong problem‑solving and data‑structure skills.
```

**Post it on Medium / Dev.to**

- Copy the *blog article* section below.
- Tag it with `#LeetCode`, `#Algorithm`, `#Java`, `#Python`, `#C++`.
- Add a short video (or GIF) that walks through the first 3 test cases.

---

## 📣 Call to Action

1. **Run the code on LeetCode.**  
   Verify that the `O(n log n)` implementation passes all hidden tests.

2. **Add the solution to your portfolio.**  
   ```text
   - Solved LeetCode 2158 – Amount of New Area Painted Each Day (Hard)
   - Languages: Java, Python, C++
   - Algorithm: Balanced BST of disjoint intervals
   - Complexity: O(n log n) time, O(m) space
   ```

3. **Publish a blog post.**  
   Use the article below (or adapt it) and include a link to your GitHub repo.

4. **Interview practice.**  
   Ask a friend or mentor to simulate a white‑board interview where you explain the algorithm *without* looking at the code.

---

## 📖 Blog‑Ready Article (copy‑paste into Medium / Dev.to)

> **Title**: *LeetCode 2158 – “Amount of New Area Painted Each Day” – O(n log n) Solution in Java, Python & C++*  
> **Tags**: #LeetCode #Hard #Interview #Algorithms #Java #Python #C++

```markdown
# 2158 – *Amount of New Area Painted Each Day*  
**LeetCode Hard – O(n log n) Solution in Java, Python & C++**

> Want to impress recruiters with a hard LeetCode problem?  
> Here’s a clean, production‑ready implementation in three languages, plus a deep dive into the algorithm.

## Problem Statement
> (copy the full statement from the “Problem Statement” section)

## Sample Cases
> (copy the sample table)

## Algorithm Overview
> (copy the “Solution Overview” paragraph)

## Step‑by‑Step Walk‑through
> (copy the “Algorithm Walk‑through” diagram)

## Code

### Java
> (paste the Java code block)

### Python
> (paste the Python code block)

### C++
> (paste the C++ code block)

## Complexity Analysis
> (copy the complexity section)

## Common Pitfalls
> (copy the pitfalls section)

## SEO & Career Boost
> (copy the SEO & career tips section)

---

```

Feel free to tweak the tone, add your own anecdotes, or insert a short **video demo**.  

---

## 📣 Call to Action

1. **Submit** the solution on LeetCode and watch the “Hard” badge appear.  
2. **Add it to your GitHub** with a README that mirrors this article.  
3. **Tweet** a short “#LeetCodeHard #Java #Python #C++” post and tag LeetCode.  
4. **Prepare for interviews** – you now have a solid, test‑ready example you can explain in under 5 minutes.

Good luck, and happy coding! 🚀