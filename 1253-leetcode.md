---
title: LeetCode 1253. Reconstruct a 2-Row Binary Matrix - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Restatement  
**Reconstruct a 2‑Row Binary Matrix**

Given

* `upper` – the required sum of the first (0‑th) row  
* `lower` – the required sum of the second (1‑st) row  
* `colsum[]` – an array of length *n* where  
  * `colsum[i]` can be `0`, `1` or `2`  
  * it represents the total number of `1`s that must appear in column *i* of the final 2‑row matrix

Return any 2‑row binary matrix `mat` that satisfies all of the above, or an empty matrix if it is impossible.



--------------------------------------------------------------------

## 2.  Intuition & Greedy Idea  

* **`colsum[i] == 2`**  
  Both rows must contain a `1`.  This consumes one `1` from `upper` **and** one from `lower`.

* **`colsum[i] == 0`**  
  Both rows contain a `0`.  No quota is used.

* **`colsum[i] == 1`**  
  One of the two rows must contain a `1`.  Whichever row still has remaining quota is chosen greedily – it does not matter which row receives the `1` as long as the total quotas are respected.

The greedy choice is safe because the only columns that **force** a placement are those with `2`.  
After all such columns are fixed, the remaining `1`‑columns are “free” – any allocation that uses exactly the remaining `upper` and `lower` counts is valid.

--------------------------------------------------------------------

## 3.  Algorithm  

```
1. Let m = colsum.length
2. If sum(colsum) != upper + lower → impossible → return []

3. Count the number of 2’s in colsum, let it be twos
   If twos > upper or twos > lower → impossible → return []

4. Create result matrix res[2][m] initialised with 0

5. First pass: for every i with colsum[i] == 2
        res[0][i] = res[1][i] = 1
        upper-- ; lower--

6. Second pass: for every i with colsum[i] == 1
        if upper > 0:
            res[0][i] = 1 ; upper--
        else:
            res[1][i] = 1 ; lower--

7. Return res
```

--------------------------------------------------------------------

## 4.  Correctness Proof  

We prove that the algorithm returns a valid matrix if one exists, and returns `[]` otherwise.

---

### Lemma 1  
If `sum(colsum) != upper + lower`, no valid matrix exists.

**Proof.**  
The total number of `1`s in the matrix equals  
`sum(colsum)` (by definition of column sums) and also equals  
`upper + lower` (by definition of row sums).  
If they differ, the two equalities cannot simultaneously hold. ∎



### Lemma 2  
If the number of `2`‑columns (`twos`) exceeds either `upper` or `lower`, no valid matrix exists.

**Proof.**  
Every `2`‑column requires a `1` in *both* rows.  
Thus each such column consumes one unit from the quota of *both* rows.  
If `twos` is larger than the quota of a row, that row would need more than `upper` (or `lower`) `1`s, impossible. ∎



### Lemma 3  
After the first pass, the remaining values of `upper` and `lower` equal the exact number of `1`s that still have to be placed in the corresponding rows.

**Proof.**  
Every `2`‑column has been filled with two `1`s, one per row, and `upper` and `lower` were decremented accordingly.  
No other `1`s have been placed yet, so the new values represent the remaining quota. ∎



### Lemma 4  
The second pass places exactly the remaining `1`‑columns in a way that satisfies the remaining quotas.

**Proof.**  
Each `1`‑column needs a single `1`.  
The algorithm places it in the upper row while `upper > 0`.  
If `upper` is already exhausted, it must be placed in the lower row, and by Lemma 3 `lower > 0` holds at that moment.  
Thus every `1`‑column is assigned to a row that still has available quota, and after the pass both `upper` and `lower` become `0`. ∎



### Theorem  
The algorithm returns a matrix that satisfies all constraints iff such a matrix exists.

**Proof.**  

*If the algorithm returns a matrix*:  
By Lemma 1 and 2 it never returns a matrix when the constraints are unsatisfiable.  
When it proceeds, Lemmas 3 and 4 guarantee that after the two passes every column sum is respected and both row sums equal the original `upper` and `lower`.  
Hence the matrix is valid.

*If a valid matrix exists*:  
All necessary conditions of Lemmas 1 and 2 must hold, otherwise no matrix could exist.  
Thus the algorithm never rejects a feasible instance early.  
With the conditions satisfied, the greedy assignment in the second pass is always possible (Lemma 4), so the algorithm produces a matrix. ∎



--------------------------------------------------------------------

## 5.  Complexity Analysis  

*Let `n = colsum.length`.*

* Time:  
  * Sum & count: `O(n)`  
  * Two passes over the array: `O(n)`  
  * **Total** `O(n)`.

* Space:  
  * Result matrix `2 × n`: `O(n)`.  
  * Auxiliary variables: `O(1)`.

--------------------------------------------------------------------

## 6.  Reference Implementations  

Below are clean, idiomatic implementations in **Java**, **Python**, and **C++**.

---

### 6.1 Java (Java 17)

```java
import java.util.*;

public class Solution {
    public List<List<Integer>> reconstructMatrix(int upper, int lower, int[] colsum) {
        int n = colsum.length;
        int total = 0;
        int twos = 0;
        for (int v : colsum) {
            total += v;
            if (v == 2) twos++;
        }
        if (total != upper + lower || twos > upper || twos > lower) {
            return new ArrayList<>();           // empty matrix
        }

        int[][] res = new int[2][n];

        // First pass – columns with 2
        for (int i = 0; i < n; i++) {
            if (colsum[i] == 2) {
                res[0][i] = 1;
                res[1][i] = 1;
                upper--;
                lower--;
            }
        }

        // Second pass – columns with 1
        for (int i = 0; i < n; i++) {
            if (colsum[i] == 1) {
                if (upper > 0) {
                    res[0][i] = 1;
                    upper--;
                } else {
                    res[1][i] = 1;
                    lower--;
                }
            }
        }

        List<List<Integer>> answer = new ArrayList<>();
        answer.add(toList(res[0]));
        answer.add(toList(res[1]));
        return answer;
    }

    private List<Integer> toList(int[] arr) {
        List<Integer> list = new ArrayList<>(arr.length);
        for (int v : arr) list.add(v);
        return list;
    }
}
```

---

### 6.2 Python 3

```python
from typing import List

class Solution:
    def reconstructMatrix(self, upper: int, lower: int,
                          colsum: List[int]) -> List[List[int]]:

        n = len(colsum)
        total = sum(colsum)
        twos = colsum.count(2)

        if total != upper + lower or twos > upper or twos > lower:
            return []

        # initialise the 2‑row matrix with zeros
        res = [[0] * n for _ in range(2)]

        # first pass – columns with 2
        for i, v in enumerate(colsum):
            if v == 2:
                res[0][i] = 1
                res[1][i] = 1
                upper -= 1
                lower -= 1

        # second pass – columns with 1
        for i, v in enumerate(colsum):
            if v == 1:
                if upper > 0:
                    res[0][i] = 1
                    upper -= 1
                else:
                    res[1][i] = 1
                    lower -= 1

        return res
```

---

### 6.3 C++17

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<vector<int>> reconstructMatrix(int upper, int lower, vector<int>& colsum) {
        int n = colsum.size();
        int total = 0, twos = 0;
        for (int v : colsum) {
            total += v;
            if (v == 2) ++twos;
        }
        if (total != upper + lower || twos > upper || twos > lower)
            return {};                          // empty matrix

        vector<vector<int>> res(2, vector<int>(n, 0));

        // columns that must contain two 1's
        for (int i = 0; i < n; ++i)
            if (colsum[i] == 2) {
                res[0][i] = res[1][i] = 1;
                --upper;  --lower;
            }

        // columns that contain exactly one 1
        for (int i = 0; i < n; ++i)
            if (colsum[i] == 1) {
                if (upper > 0) {
                    res[0][i] = 1;
                    --upper;
                } else {
                    res[1][i] = 1;
                    --lower;
                }
            }

        return res;
    }
};
```

--------------------------------------------------------------------

## 7.  SEO‑Optimised Blog Post – “How to Nail a Data‑Structures Interview *and* Land Your Next Tech Job”  

> **Keywords** – *reconstruct a 2‑row binary matrix*, *LeetCode 1739*, *job interview algorithm*, *greedy algorithm*, *Java Python C++ coding interview*, *software engineer interview tips*, *data structures interview questions*, *algorithmic thinking*, *tech hiring trends*  

---

### 7.1 Title & Meta‑Description  

| Element | Content |
|---------|---------|
| **Title** | Reconstruct a 2‑Row Binary Matrix – LeetCode 1739 (Java, Python, C++) |
| **Meta‑Description** | Master the LeetCode “Reconstruct a 2‑Row Binary Matrix” problem. Step‑by‑step greedy algorithm, proof, complexity, and clean Java, Python, C++ solutions. Perfect for software‑engineer interview prep. |

---

### 7.2 Introduction  

> *Data‑structure and algorithm questions are the cornerstone of every software‑engineering interview. One of the most frequently asked patterns involves reconstructing a matrix from row and column sums – a deceptively simple yet surprisingly tricky problem. In this article, we walk through the “Reconstruct a 2‑Row Binary Matrix” LeetCode challenge (ID 1739) and provide production‑ready solutions in Java, Python and C++.*

---

### 7.3 Why This Problem Matters in Interviews  

1. **Demonstrates Understanding of Basic Counting** – You must keep track of both row and column sums simultaneously.  
2. **Highlights Greedy Decision‑Making** – Choosing where to place a `1` in “free” columns is a classic greedy pitfall; solving it correctly shows algorithmic maturity.  
3. **Shows Edge‑Case Rigor** – Early rejection of impossible inputs tests your ability to spot necessary conditions before doing heavy work.  
4. **Scales Across Languages** – A solid solution in three popular languages proves language‑agnostic thinking – a valuable asset for full‑stack or systems positions.

---

### 7.4 Step‑by‑Step Algorithm (in Plain English)

1. **Validate Feasibility**  
   * Total number of `1`s must equal the sum of the two row quotas.  
   * Every column with `2` forces a placement; its count cannot exceed either quota.

2. **Fill Forced Columns** (`colsum[i] == 2`) – both rows get `1`, and we reduce both quotas.

3. **Allocate “Free” Columns** (`colsum[i] == 1`) – greedily put the `1` in the row that still needs one.  After this pass both quotas reach zero.

4. **Return the Matrix** or an empty list if any check failed.

---

### 7.5 Code Overview  

| Language | Key Implementation Details |
|----------|----------------------------|
| **Java** | Uses `List<List<Integer>>` to match LeetCode’s return type; helper `toList()` for readability. |
| **Python** | List‑comprehensions for concise matrix creation; early return on impossibility. |
| **C++** | 2‑dimensional `vector<vector<int>>`; early exit with `{}`. |

---

### 7.6 Complexity & Performance  

* **Time** – `O(n)` where *n* is the number of columns.  
* **Space** – `O(n)` for the resulting matrix.  

These tight bounds are optimal; no algorithm can be asymptotically better because we must inspect every column at least once.

---

### 7.7 Edge‑Case Checklist  

| Scenario | Expected Result |
|----------|-----------------|
| `sum(colsum) != upper + lower` | `[]` |
| Too many `2` columns | `[]` |
| All columns are `0` | matrix of all zeros |
| All columns are `2` and `upper == lower == n/2` | matrix with all ones |

---

### 7.8 Final Takeaway  

*The greedy algorithm above is **provably correct** and runs in linear time.  
Implementing it in multiple languages demonstrates both algorithmic thinking and language‑specific craftsmanship – exactly the kind of skill set recruiters look for.*

---

## 8.  How This Blog Boosts Your Tech Career  

| Benefit | How the Article Helps |
|---------|-----------------------|
| **Keyword Visibility** | Keywords such as “Reconstruct a 2‑Row Binary Matrix”, “LeetCode 1739 solution”, “Java Python C++ interview” appear naturally throughout the post, making it discoverable when recruiters or hiring managers search for common interview problems. |
| **Showcases Problem‑Solving Skills** | The article walks through proof, complexity, and edge‑case handling—signals to recruiters that you can reason rigorously about algorithms. |
| **Demonstrates Multilingual Expertise** | By providing clear, idiomatic solutions in three major languages, you illustrate the ability to adapt to a company’s tech stack. |
| **Adds Value to Your Portfolio** | You can embed the code snippets in your GitHub README or personal website, and the explanation serves as a quick reference for future interviews. |
| **Engages Recruiters with Structured Content** | The use of tables, bullet lists, and code blocks matches the format recruiters appreciate when skimming candidate blogs or technical profiles. |

**Take Action:**  
*Post this article on Medium, Dev.to, or your personal blog.*  
*Add a link to your GitHub repository that contains the full solution files.*  
*Use it as a talking point in upcoming interviews—explain the intuition, walk through the proof, and showcase the clean implementations.*

Happy coding—and happy interviewing! 🚀