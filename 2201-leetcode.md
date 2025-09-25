---
title: LeetCode 2201. Count Artifacts That Can Be Extracted - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap  

**LeetCode 2201 – Count Artifacts That Can Be Extracted**  
- We have an `n × n` grid (0‑indexed).  
- `artifacts[i] = [r1, c1, r2, c2]` describes a rectangle of cells containing an artifact.  
- Each artifact covers **at most 4 cells** and *no two artifacts overlap*.  
- `dig[j] = [r, c]` lists cells that will be excavated – every cell in this list is unique.  
- An artifact can be extracted **iff every cell it occupies has been dug**.  
- Return the number of extractable artifacts.

**Constraints**  
```
1 ≤ n ≤ 1000
1 ≤ artifacts.length, dig.length ≤ min(n², 10⁵)
```

---

## 2.  Brute‑Force Idea  

For every artifact iterate over its cells and check whether the cell was dug.  
If all cells are dug → increase answer.

Because every artifact covers at most 4 cells, the inner loop is tiny, so the
overall complexity is `O(artifacts.length × 4)`, which easily satisfies the limits.

We only need an efficient way to answer “has this cell been dug?”  
The simplest is a 2‑D boolean array (`visit[n][n]`) – O(1) lookup, O(n²) space.  
With the constraints `n ≤ 1000`, the array costs at most 1 000 000 booleans (~1 MB).

---

## 3.  Three Implementations  

### 3.1  Java – Clear & Easy  

```java
import java.util.*;

public class Solution {
    public int digArtifacts(int n, int[][] artifacts, int[][] dig) {
        // mark all dug cells
        boolean[][] dug = new boolean[n][n];
        for (int[] d : dig) {
            dug[d[0]][d[1]] = true;
        }

        int count = 0;
        // for each artifact test all its cells
        for (int[] a : artifacts) {
            boolean fullyDug = true;
            for (int r = a[0]; r <= a[2] && fullyDug; r++) {
                for (int c = a[1]; c <= a[3]; c++) {
                    if (!dug[r][c]) {          // a single undug cell stops us
                        fullyDug = false;
                        break;
                    }
                }
            }
            if (fullyDug) count++;
        }
        return count;
    }
}
```

**Why it’s good** –  
* Very readable.  
* Uses only O(n²) memory (1 MB for n=1000).  
* Time complexity `O(artifacts.length)` – each artifact checks ≤4 cells.

---

### 3.2  Python – Compact & Pythonic  

```python
from typing import List

class Solution:
    def digArtifacts(
        self, n: int, artifacts: List[List[int]], dig: List[List[int]]
    ) -> int:
        # set of dug coordinates for O(1) lookup
        dug = {tuple(p) for p in dig}

        extracted = 0
        for r1, c1, r2, c2 in artifacts:
            # generate all cells of this artifact
            cells = {(r, c) for r in range(r1, r2 + 1) for c in range(c1, c2 + 1)}
            if cells <= dug:          # set containment check
                extracted += 1
        return extracted
```

**Why it’s good** –  
* Uses a `set` of tuples for constant‑time lookups.  
* Avoids allocating a full 2‑D array – memory‑friendly.  
* Set‑containment (`cells <= dug`) is fast because each `cells` set has ≤4 elements.

---

### 3.3  C++ – Performance‑oriented  

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int digArtifacts(int n, vector<vector<int>>& artifacts,
                     vector<vector<int>>& dig) {
        // map each dug cell to a boolean flag
        vector<vector<bool>> dug(n, vector<bool>(n, false));
        for (auto &p : dig) dug[p[0]][p[1]] = true;

        int res = 0;
        for (auto &a : artifacts) {
            bool ok = true;
            for (int r = a[0]; r <= a[2] && ok; ++r)
                for (int c = a[1]; c <= a[3]; ++c)
                    if (!dug[r][c]) { ok = false; break; }
            if (ok) ++res;
        }
        return res;
    }
};
```

**Why it’s good** –  
* Uses `vector<vector<bool>>` – compact and cache friendly.  
* Loops are simple, no extra data structures, so it runs in < 1 ms for the limits.  

---

## 4.  Algorithmic Analysis  

| Implementation | Time | Space | Notes |
|----------------|------|-------|-------|
| Java | `O(artifacts.length)` | `O(n²)` | 1 MB max |
| Python | `O(artifacts.length)` | `O(dig.length)` | Set lookup, memory‑efficient |
| C++ | `O(artifacts.length)` | `O(n²)` | Fastest for large inputs |

All three run comfortably within the LeetCode limits because each artifact covers at most 4 cells.

---

## 5.  The Good, The Bad, The Ugly  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Correctness** | Simple exhaustive check; no corner‑case bugs. | None – straightforward. | None. |
| **Readability** | Java code is self‑explanatory. | Python’s set‑comprehension is elegant but might be unfamiliar to beginners. | C++ code is concise but uses nested loops that can hide errors if boundaries slip. |
| **Memory Footprint** | Java/C++ allocate `n²` booleans; 1 MB for n=1000 – fine. | Python uses a set of dug cells – O(dig.length), smaller when dig is sparse. | None. |
| **Performance** | All are linear in number of artifacts. | Python may be slightly slower for very large inputs but still within limits. | None. |
| **Extensibility** | Easy to switch to a different data structure. | None. | None. |

---

## 6.  “What If” Improvements  

1. **Sparse Grid** – If `n` were 10⁵, we’d drop the 2‑D array in favor of a hash‑set for dug cells (Python or C++ `unordered_set`).  
2. **Pre‑compute Cell Count** – For every artifact, store how many cells it covers. Then decrement a counter for each dug cell belonging to that artifact. When counter hits zero, the artifact is extracted. This avoids re‑checking the entire rectangle each time.  
3. **Parallelism** – The artifact checks are embarrassingly parallel; in a production setting we could distribute them across threads or use SIMD.

---

## 7.  SEO‑Optimized Blog Article  

### **Title**  
“Cracking LeetCode 2201: Count Artifacts That Can Be Extracted – Java, Python & C++ Solutions for Your Next Interview”

### **Meta Description**  
Learn how to solve LeetCode’s “Count Artifacts That Can Be Extracted” in Java, Python, and C++. Read the algorithmic walkthrough, time‑space analysis, and interview‑friendly tips.

### **Keywords**  
LeetCode, Count Artifacts, LeetCode 2201, coding interview, Java, Python, C++, algorithm, time complexity, space complexity, data structures, interview prep

---

### **Article Body**  

**Introduction**  
LeetCode 2201 is a classic medium‑level problem that tests your ability to work with 2‑D grids, sets, and simple loops. The statement is deceptively simple: “Given a grid, some rectangular artifacts, and a list of dug cells, count how many artifacts can be fully extracted.” Yet the constraints (artifacts covering at most four cells, no overlaps, `n` up to 1000) force you to think about an optimal approach.  

Below is a complete walkthrough of the problem, a clean linear solution, and three implementations in Java, Python, and C++. After the code, we discuss pitfalls, potential improvements, and why this solution shines in a technical interview.

---

#### 1. Problem Restatement  
- `n × n` grid, 0‑indexed.  
- `artifacts[i] = [r1, c1, r2, c2]` – rectangle that may contain up to 4 cells.  
- `dig[j] = [r, c]` – unique list of cells to excavate.  
- Artifact can be extracted if **every** cell inside its rectangle is dug.  
- Return number of extractable artifacts.

---

#### 2. Intuition & Algorithm  

Because each artifact touches at most four cells, a direct brute‑force check is cheap. The key is to answer “is cell (r,c) dug?” in O(1).  
* **Option 1** – 2‑D boolean array (`dug[r][c]`).  
* **Option 2** – Hash‑set of tuples or pairs.  

Both are constant‑time lookups. We then iterate over each artifact, examine its cells, and increment the counter if all cells are dug.

---

#### 3. Complexity  

* **Time** – `O(#artifacts)` (≤ 10⁵).  
* **Space** – `O(n²)` for the 2‑D array (≈ 1 MB for n=1000) or `O(#dig)` for a hash‑set.  

Both comfortably fit within the problem limits.

---

#### 4. Code Snippets  

##### 4.1 Java (Easy to Understand)

```java
public class Solution {
    public int digArtifacts(int n, int[][] artifacts, int[][] dig) {
        boolean[][] dug = new boolean[n][n];
        for (int[] d : dig) dug[d[0]][d[1]] = true;

        int count = 0;
        for (int[] a : artifacts) {
            boolean ok = true;
            for (int r = a[0]; r <= a[2] && ok; r++) {
                for (int c = a[1]; c <= a[3]; c++) {
                    if (!dug[r][c]) { ok = false; break; }
                }
            }
            if (ok) count++;
        }
        return count;
    }
}
```

##### 4.2 Python (Pythonic Set)

```python
class Solution:
    def digArtifacts(self, n, artifacts, dig):
        dug = {tuple(p) for p in dig}
        extracted = 0
        for r1, c1, r2, c2 in artifacts:
            cells = {(r, c) for r in range(r1, r2+1) for c in range(c1, c2+1)}
            if cells <= dug:
                extracted += 1
        return extracted
```

##### 4.3 C++ (Fast & Cache‑Friendly)

```cpp
class Solution {
public:
    int digArtifacts(int n, vector<vector<int>>& artifacts,
                     vector<vector<int>>& dig) {
        vector<vector<bool>> dug(n, vector<bool>(n, false));
        for (auto &p : dig) dug[p[0]][p[1]] = true;

        int res = 0;
        for (auto &a : artifacts) {
            bool ok = true;
            for (int r = a[0]; r <= a[2] && ok; ++r)
                for (int c = a[1]; c <= a[3]; ++c)
                    if (!dug[r][c]) { ok = false; break; }
            if (ok) ++res;
        }
        return res;
    }
};
```

---

#### 5. Interview‑Friendly Takeaways  

1. **Explain your data‑structure choice** – 2‑D array vs. set – and how it gives O(1) lookups.  
2. **Edge cases** – Always confirm the inclusive bounds (`<=`).  
3. **Time/Space justification** – Show the linear time and low memory usage.  
4. **Optimizations** – Mention the counter‑decrement trick as a follow‑up improvement.

---

#### 6. Common Pitfalls  

| Pitfall | Fix |
|---------|-----|
| Off‑by‑one in rectangle bounds | Use `<=` with `r2+1` or `c2+1` in Python. |
| Wrong memory layout in C++ | Prefer `vector<vector<bool>>` over raw `bool**` for safety. |
| Forgetting to break after the first undug cell | Add `ok = false; break;` to avoid unnecessary work. |

---

#### 7. Final Thoughts  

LeetCode 2201’s challenge is more about recognizing that a small grid cell count per artifact allows a simple linear algorithm, not about complex data structures. The solutions above are **readable**, **fast**, and **memory‑efficient** – exactly what interviewers look for.  

Give these implementations a try on the platform, tweak them for sparse grids, or extend the counter‑decrement trick for an even leaner solution. Good luck, and happy coding!

---

**Conclusion**  
You’ve now seen a full, interview‑ready solution to “Count Artifacts That Can Be Extracted.” Pick the language you’re most comfortable with, run through the edge cases, and you’ll ace this problem in your next technical interview. Happy hacking!

---

### **End of Article**  

--- 

This article, combined with the three clean code snippets, not only solves LeetCode 2201 but also positions you as a strong candidate for your next coding interview. Use it as a reference, adapt it to your language of choice, and bring confidence to the interview stage.  

--- 

## 8.  Wrap‑Up  

We’ve dissected LeetCode 2201 from the ground up, delivered three production‑grade solutions, and reflected on their strengths. Whether you’re writing clean Java, writing compact Python, or optimizing in C++, the core idea remains: mark dug cells in O(1), then linearly verify each artifact. This pattern is a staple in grid‑based interview problems and a perfect showcase of algorithmic thinking.  

Happy coding—and good luck on your next interview!