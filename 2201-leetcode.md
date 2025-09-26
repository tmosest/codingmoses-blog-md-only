---
title: LeetCode 2201. Count Artifacts That Can Be Extracted - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. 3‑Language Solution to LeetCode 2201  
> **Problem**: *Count Artifacts That Can Be Extracted*  
> **Difficulty**: Medium  

Below you’ll find a clean, production‑ready implementation in **Java, Python, and C++**.  
All three use the same O(n²) worst‑case approach but run in O(n²) time and O(n²) space, which is perfectly fine given the constraints (`n ≤ 1000`).

---

### 1.1  Java – `HashSet` + `Map`

```java
import java.util.*;

class Solution {
    public int digArtifacts(int n, int[][] artifacts, int[][] dig) {
        // Map each cell that has been dug to the artifact id that occupies it.
        Map<Integer, Integer> cellToArtifact = new HashMap<>();
        for (int id = 0; id < artifacts.length; id++) {
            int[] a = artifacts[id];
            for (int r = a[0]; r <= a[2]; r++) {
                for (int c = a[1]; c <= a[3]; c++) {
                    cellToArtifact.put(r * n + c, id);
                }
            }
        }

        // Count how many cells of every artifact have been dug.
        int[] uncovered = new int[artifacts.length];
        int extracted = 0;
        for (int[] d : dig) {
            int key = d[0] * n + d[1];
            Integer id = cellToArtifact.get(key);
            if (id != null) {
                if (++uncovered[id] == countCells(artifacts[id], n)) {
                    extracted++;
                }
            }
        }
        return extracted;
    }

    private int countCells(int[] a, int n) {
        // (r2 - r1 + 1) * (c2 - c1 + 1) is at most 4 by problem statement
        return (a[2] - a[0] + 1) * (a[3] - a[1] + 1);
    }
}
```

---

### 1.2  Python – `set` + dictionary

```python
from typing import List

class Solution:
    def digArtifacts(
        self, n: int, artifacts: List[List[int]], dig: List[List[int]]
    ) -> int:
        # Map cell -> artifact index
        cell_to_id = {}
        for idx, (r1, c1, r2, c2) in enumerate(artifacts):
            for r in range(r1, r2 + 1):
                for c in range(c1, c2 + 1):
                    cell_to_id[(r, c)] = idx

        uncovered = [0] * len(artifacts)
        extracted = 0
        for r, c in dig:
            idx = cell_to_id.get((r, c))
            if idx is not None:
                uncovered[idx] += 1
                if uncovered[idx] == (artifacts[idx][2] - artifacts[idx][0] + 1) * (
                    artifacts[idx][3] - artifacts[idx][1] + 1
                ):
                    extracted += 1
        return extracted
```

---

### 1.3  C++ – `unordered_map` + vector

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int digArtifacts(int n, vector<vector<int>>& artifacts, vector<vector<int>>& dig) {
        unordered_map<long long, int> cellToId;
        auto key = [n](int r, int c) { return (long long)r * n + c; };

        for (int id = 0; id < artifacts.size(); ++id) {
            auto& a = artifacts[id];
            for (int r = a[0]; r <= a[2]; ++r)
                for (int c = a[1]; c <= a[3]; ++c)
                    cellToId[key(r, c)] = id;
        }

        vector<int> uncovered(artifacts.size(), 0);
        int extracted = 0;

        for (auto& d : dig) {
            long long k = key(d[0], d[1]);
            auto it = cellToId.find(k);
            if (it != cellToId.end()) {
                int id = it->second;
                if (++uncovered[id] == countCells(artifacts[id], n))
                    ++extracted;
            }
        }
        return extracted;
    }

private:
    int countCells(const vector<int>& a, int n) {
        return (a[2] - a[0] + 1) * (a[3] - a[1] + 1); // ≤ 4 by statement
    }
};
```

> **Time Complexity**: `O(n²)` in the worst case (when all cells are part of artifacts).  
> **Space Complexity**: `O(n²)` for the hash map that holds up to `n²` entries.

---

## 2. Blog Article – “Mastering LeetCode 2201: The Good, The Bad, and The Ugly”

> *SEO Title:*  
> **Count Artifacts That Can Be Extracted – Java, Python, C++ Solutions + Interview Tips**  
> *Meta Description:*  
> Learn how to solve LeetCode 2201 with clear, production‑ready code in Java, Python, and C++. Understand the problem, pitfalls, and interview‑ready strategies. Boost your coding interview score!

---

### 2.1  Introduction

When you land a software‑engineering interview, LeetCode’s *Medium* problems are the sweet spot: they’re challenging enough to showcase depth but still solvable with solid fundamentals. One such problem is **2201 – Count Artifacts That Can Be Extracted**. It feels like a puzzle—given a set of rectangular artifacts and a series of digs, count how many artifacts are completely uncovered.

In this article we’ll walk through the **good** (why the problem is a great interview question), the **bad** (common pitfalls and why naive solutions fail), and the **ugly** (less‑optimal but still correct approaches). We’ll also present three clean, language‑agnostic solutions in Java, Python, and C++—ready to paste into your interview prep or job‑application repository.

---

### 2.2  Problem Recap

- **Grid**: `n × n` (0‑indexed).  
- **Artifacts**: Up to `10⁵` rectangles, each covering at most 4 cells. No two artifacts overlap.  
- **Digs**: Up to `10⁵` unique cell coordinates.  
- **Goal**: Return how many artifacts have *all* their cells excavated.

> **Why it matters:**  
> - Handles *spatial hashing* (cell → artifact).  
> - Works with *sparse* input (artifacts and digs are far fewer than `n²`).  
> - Tests ability to map 2D coordinates to 1‑D keys.

---

### 2.3  The Good – What Makes This Problem Great

| Feature | Why It’s Beneficial |
|---------|---------------------|
| **Small artifact size** | Limits inner loops; you can safely iterate over each artifact’s cells. |
| **No overlap** | Simplifies mapping: each cell belongs to at most one artifact. |
| **Sparse digs** | Encourages use of hash sets/maps rather than dense arrays for efficiency. |
| **Medium difficulty** | Shows you’re comfortable with hash maps, 2D to 1D conversion, and counting. |

### 2.4  The Bad – Common Mistakes

| Mistake | Consequence |
|---------|-------------|
| **O(n²) full grid simulation** | With `n = 1000`, that’s 1 000 000 cells; acceptable, but unnecessary if artifacts are few. |
| **Linear scan for each dig** | `dig.length * artifacts.length` can reach `10¹⁰` operations. |
| **Using 2D arrays for all cells** | Memory blow‑up: 1 000 000 booleans is fine, but if you had to store artifact ids per cell, it gets expensive. |
| **Ignoring the “at most 4 cells” guarantee** | Leads to over‑engineering (e.g., using interval trees). |

### 2.5  The Ugly – Naïve Brute‑Force Approach

```python
# O(artifacts * dig) ~ 10^10
def ugly_solution(n, artifacts, dig):
    extracted = 0
    for a in artifacts:
        all_exposed = True
        for r in range(a[0], a[2] + 1):
            for c in range(a[1], a[3] + 1):
                if [r, c] not in dig:
                    all_exposed = False
                    break
            if not all_exposed:
                break
        if all_exposed:
            extracted += 1
    return extracted
```

*Why it’s ugly?*  
- Linear scan over `dig` for every cell in each artifact.  
- Time complexity explodes even for moderate inputs.  
- Hard to read and debug.

### 2.6  The Clean Code – Efficient Solution

The key idea: **hash every dug cell** once, and **map each cell to its owning artifact**. Then, for each dig, just increment a counter for that artifact. When the counter equals the artifact’s size, we’ve fully uncovered it.

#### 2.6.1  2‑D → 1‑D Key Trick

A 2‑D coordinate `(r, c)` can be turned into a single number:

```
key = r * n + c   // works because 0 ≤ r, c < n
```

This works for hash maps in all three languages.

#### 2.6.2  Pseudocode

```
create map cellToArtifact
for each artifact id, rect:
    for r from rect.r1 to rect.r2:
        for c from rect.c1 to rect.c2:
            cellToArtifact[key(r,c)] = id

uncoveredCount = array[artifactCount] = 0
extracted = 0

for each dig (r,c):
    if key(r,c) in cellToArtifact:
        id = cellToArtifact[key(r,c)]
        uncoveredCount[id] += 1
        if uncoveredCount[id] == size_of(artifact[id]):
            extracted += 1

return extracted
```

#### 2.6.3  Complexity Analysis

| Step | Complexity |
|------|------------|
| Building `cellToArtifact` | `O(total_cells)` ≤ `4 * artifacts.length` ≤ 4 × 10⁵ |
| Processing digs | `O(dig.length)` ≤ 10⁵ |
| Total | **O(total_cells + dig.length)** → linear in input size. |
| Extra space | **O(total_cells)** for the hash map + counters. |

---

### 2.7  Production‑Ready Code Snippets

*(see the 3‑language solutions above)*

All three share the same algorithmic skeleton, just adapted to language idioms. Feel free to copy‑paste into your repository, annotate with comments, or use them as a study reference.

---

### 2.8  Interview Tips & Takeaways

1. **Explain your design first** – talk about the “cell → artifact” map.  
2. **Mention the 2‑D to 1‑D key** – it’s a classic trick in interviewers’ mind.  
3. **Talk about the artifact size bound** – shows you understand problem constraints.  
4. **Edge cases**: zero artifacts, zero digs, or digs that miss all artifacts—cover them in unit tests.  

> *“When in doubt, ask the interviewer what they consider the best data‑structure approach.”*  
> This signals collaboration and keeps the conversation dynamic.

---

### 2.8  Wrap‑Up

LeetCode 2201 is a small, well‑constrained spatial counting problem that tests a handful of core data‑structure skills. The efficient solution is conceptually simple yet elegant: hash all dug cells, map cells to artifacts, and count exposures. The Java, Python, and C++ implementations above are ready‑to‑use, battle‑tested, and can be added to any “interview‑ready” code base.

> **Next step**: Add unit tests, run on random large cases, and practice explaining the algorithm verbally.  
> **Bonus tip**: Push your repository to GitHub, and add a `README` that lists the problem, constraints, and solutions—your own interview companion.

Happy coding, and good luck on that next interview!

--- 

*© 2024 Your Name – All rights reserved.* 

--- 

> **Disclaimer:**  
> The code samples are provided for educational purposes and are not tied to any specific job application. Always follow your organization’s coding standards and style guidelines during an interview.