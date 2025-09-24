---
title: LeetCode 1183. Maximum Number of Ones - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🏆 LeetCode 1183 – *Maximum Number of Ones*  
**The Good, The Bad, and The Ugly – A Complete Interview‑Ready Guide**

---

### 🚀 Why This Problem Rocks (and Why It Should Be on Your Interview List)

- **Real‑world relevance** – the constraint “any sub‑matrix can have at most X ones” is a classic pattern‑restriction problem that shows up in image processing, memory layout, and even in scheduling problems.
- **Algorithmic variety** – you can solve it with a simple greedy, a DP, or a mathematical insight. Interviewers love to hear why you chose one approach over another.
- **Clear “aha!” moment** – a quick observation (pick the most frequent offsets) turns a 100‑line DP into a 30‑line solution.
- **SEO Gold** – every tech blog or job‑seeking article that mentions *LeetCode 1183*, *Maximum Number of Ones*, *greedy algorithm*, *C++ interview*, *Python interview*, *Java interview* will rank high.

---

## 📖 Problem Statement (LeetCode 1183)

> Given a matrix `M` of dimensions `width × height` containing only `0` or `1`, we are told that **any** `sideLength × sideLength` sub‑matrix of `M` contains **at most** `maxOnes` ones.  
> **Return the maximum total number of ones** that `M` can contain.

**Constraints**

| Parameter | Range |
|-----------|-------|
| `width`, `height` | 1 … 100 |
| `sideLength` | 1 … min(`width`, `height`) |
| `maxOnes` | 0 … `sideLength × sideLength` |

**Examples**

```
Input  : width=3, height=3, sideLength=2, maxOnes=1
Output : 4
Matrix:
1 0 1
0 0 0
1 0 1

Input  : width=3, height=3, sideLength=2, maxOnes=2
Output : 6
Matrix:
1 0 1
1 0 1
1 0 1
```

---

## 🧠 The “Good” – The Greedy Insight

**Key observation**

Every `sideLength × sideLength` window contains exactly **one** cell from each of the `sideLength²` residue classes `(i mod sideLength, j mod sideLength)`.

- Think of the grid as being tiled with a `sideLength × sideLength` pattern that repeats every `sideLength` rows **and** columns.
- Each residue class is the set of positions that line up with the same spot inside that pattern.
- If we decide to put a `1` in a residue class, **every** window that contains that class will get one more `1`.

Thus:

1. Count how many cells belong to each residue class.  
   `count[r][c] = number of grid cells with (row mod sideLength = r, col mod sideLength = c)`
2. Pick the **`maxOnes` residue classes with the largest counts** – putting `1` in those classes maximizes total ones while guaranteeing that any window contains at most `maxOnes` ones.
3. The answer is the sum of the counts of the chosen classes.

Why is this optimal?  
Because we’re choosing the classes that contribute the most cells. If we replaced any chosen class with a non‑chosen one, the total would strictly decrease. And the constraint on each window is already satisfied because each window can pick at most one from each class, so at most `maxOnes` ones.

---

## 🚫 The “Bad” – A Pitfall to Avoid

- **Over‑thinking the DP** – One might try a 4‑dimensional DP to remember the number of ones in the last `sideLength` rows/columns. That’s unnecessary and would blow up time/memory.
- **Assuming we can pick any `maxOnes` cells arbitrarily** – If you just pick the most frequent cells without respecting residue classes, overlapping windows may violate the constraint.
- **Ignoring the modulo pattern** – Not realizing that the residue classes are the only thing that matters for the window constraint will lead to wrong solutions.

---

## 💥 The “Ugly” – When Your Code Goes Wrong

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| `IndexOutOfBoundsException` in Java or `IndexError` in Python | Wrong modulo indexing (off‑by‑one) | Use `row % sideLength` and `col % sideLength` consistently. |
| Wrong answer for `maxOnes == sideLength*sideLength` | Forgetting that you can pick all cells | Sum all counts (the whole matrix). |
| Time limit exceeded for 100×100 grid | Sorting all `sideLength²` classes when `maxOnes` is small | Use a min‑heap of size `maxOnes` to keep only the largest counts. |
| Wrong answer for rectangular grids (`width != height`) | Assuming square grid only | Count cells per class by iterating over all cells; no assumption on dimensions. |

---

## 📦 Code Solutions

Below are clean, interview‑ready implementations in **Java**, **Python**, and **C++**.

### 1️⃣ Java

```java
import java.util.Arrays;
import java.util.Comparator;

class Solution {
    public int maximumNumberOfOnes(int width, int height, int sideLength, int maxOnes) {
        // Count cells for each residue class
        int[][] cnt = new int[sideLength][sideLength];
        for (int r = 0; r < height; r++) {
            for (int c = 0; c < width; c++) {
                cnt[r % sideLength][c % sideLength]++;
            }
        }

        // Flatten and sort counts descending
        int[] flat = new int[sideLength * sideLength];
        int idx = 0;
        for (int[] row : cnt) {
            for (int v : row) flat[idx++] = v;
        }
        Arrays.sort(flat);
        int n = flat.length;
        int ans = 0;
        // Sum the largest `maxOnes` counts
        for (int i = n - 1; i >= n - maxOnes; i--) {
            ans += flat[i];
        }
        return ans;
    }
}
```

> **Why this works** –  
> - `cnt` aggregates by residue classes.  
> - `flat` contains all class sizes.  
> - Sorting gives the largest sizes at the end; we sum the top `maxOnes`.

### 2️⃣ Python

```python
class Solution:
    def maximumNumberOfOnes(
        self,
        width: int,
        height: int,
        sideLength: int,
        maxOnes: int
    ) -> int:
        # Count per residue class
        cnt = [[0] * sideLength for _ in range(sideLength)]
        for r in range(height):
            for c in range(width):
                cnt[r % sideLength][c % sideLength] += 1

        # Flatten and get the largest maxOnes counts
        flat = [v for row in cnt for v in row]
        flat.sort(reverse=True)
        return sum(flat[:maxOnes])
```

> Python’s list comprehensions make the code concise and easy to read.

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maximumNumberOfOnes(int width, int height, int sideLength, int maxOnes) {
        vector<vector<int>> cnt(sideLength, vector<int>(sideLength, 0));

        for (int r = 0; r < height; ++r)
            for (int c = 0; c < width; ++c)
                ++cnt[r % sideLength][c % sideLength];

        vector<int> flat;
        for (auto &row : cnt)
            for (int v : row) flat.push_back(v);

        sort(flat.begin(), flat.end(), greater<int>());
        int ans = 0;
        for (int i = 0; i < maxOnes; ++i) ans += flat[i];
        return ans;
    }
};
```

> Uses `greater<int>()` to sort descending, exactly like the Java and Python versions.

---

## 📈 Complexity Analysis

| Operation | Java | Python | C++ |
|-----------|------|--------|-----|
| Counting cells | `O(width × height)` | `O(width × height)` | `O(width × height)` |
| Sorting `sideLength²` elements | `O(sideLength² log sideLength²)` | `O(sideLength² log sideLength²)` | `O(sideLength² log sideLength²)` |
| Space | `O(sideLength²)` | `O(sideLength²)` | `O(sideLength²)` |

With `width, height ≤ 100` and `sideLength ≤ 100`, the algorithm runs in well under a millisecond on modern hardware.

---

## 🎯 How to Talk About This in an Interview

1. **State the problem succinctly** – “We’re given a grid and a constraint on any `k×k` sub‑matrix. Find the maximum ones.”
2. **Identify the pattern** – “Any `k×k` window contains exactly one cell from each residue class modulo `k`.”
3. **Explain the greedy** – “We count cells per residue class and choose the top `maxOnes` classes; each window sees at most `maxOnes` ones.”
4. **Mention edge cases** – “When `maxOnes` equals `k²`, we can fill the whole grid.”
5. **Talk about complexity** – “O(nm + k² log k²) time, O(k²) space, trivial for the given constraints.”
6. **Optional** – “If `k` were huge, we could keep a min‑heap of size `maxOnes` to avoid full sorting.”

---

## 🛠️ Testing & Validation

```python
# quick sanity check
def brute(width, height, k, m):
    from itertools import product
    best = 0
    for grid in product([0,1], repeat=width*height):
        ok=True
        for r in range(height-k+1):
            for c in range(width-k+1):
                s=sum(grid[(r+i)*width+(c+j)] for i in range(k) for j in range(k))
                if s>m:
                    ok=False; break
            if not ok: break
        if ok:
            best=max(best,sum(grid))
    return best

print(brute(3,3,2,1))  # 4
print(brute(3,3,2,2))  # 6
```

The greedy algorithm matches the exhaustive brute force for all small grids (`width,height ≤ 4`). Feel free to embed such unit tests in your personal library.

---

## 📣 Takeaway

- **Residue classes modulo `k` are the only relevant dimension** – windows cannot differentiate beyond that.
- **Greedy is both simple and optimal** – you never need a complex DP.
- **Implementation is straightforward** – count, sort, sum.

Use this solution to **score** in coding contests and to **win** in technical interviews. Happy coding! 🚀

--- 

*Prepared by your friendly algorithm assistant.*