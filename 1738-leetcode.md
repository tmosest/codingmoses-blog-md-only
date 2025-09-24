---
title: LeetCode 1738. Find Kth Largest XOR Coordinate Value - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 1738. Find Kth Largest XOR Coordinate Value  
### The Good, The Bad, and The Ugly – A Deep‑Dive Guide for Interviews  
*(Java, Python & C++ implementations + an SEO‑friendly blog article)*  

---

### TL;DR  
- **Problem**: For a given `m × n` matrix, compute the XOR of all elements in the sub‑matrix `(0,0)` → `(i,j)` for every coordinate `(i,j)`. Return the **k‑th largest** of those XOR values.  
- **Key insight**: The XOR of a prefix can be computed in O(1) using a **prefix‑XOR table** (similar to 2‑D prefix sums).  
- **Two popular solution patterns**  
  1. **Min‑heap** – O(m n log k) time, O(k) space  
  2. **QuickSelect** – O(m n) expected time, O(m n) space (flattened array)  
- **Why it matters**: LeetCode 1738 is a favorite in coding interviews. Mastering it shows you can blend DP, data‑structures, and selection algorithms.

---

## 📚 Problem Restatement

```text
Input:  matrix   – int[m][n], 1 ≤ m, n ≤ 1000, 0 ≤ matrix[i][j] ≤ 1 000 000
        k        – 1‑indexed, 1 ≤ k ≤ m·n

Output: int – the k‑th largest XOR‑coordinate value
```

> **Definition** –  
> `value(i, j) = matrix[0][0] ^ matrix[0][1] ^ … ^ matrix[i][j]`  
> (XOR of all elements in the rectangle from the top‑left corner to `(i, j)`).

---

## 🚀 1. Algorithmic Blueprint

| Step | Action | Why |
|------|--------|-----|
| 1 | **Build a 2‑D prefix‑XOR table** `pref` of size `(m+1) × (n+1)` | `pref[i][j]` holds XOR of sub‑matrix `(0,0)` → `(i-1,j-1)`. Recurrence:  
  `pref[i][j] = matrix[i-1][j-1] ^ pref[i-1][j] ^ pref[i][j-1] ^ pref[i-1][j-1]` |
| 2 | **Flatten** all non‑zero prefix values into a 1‑D array `arr` | Allows use of heap or QuickSelect. |
| 3 | **Select k‑th largest** either by:
| 3a | **Min‑heap** (size k) – keep top k values | `O(m n log k)` time, `O(k)` extra space |
| 3b | **QuickSelect** – partition around a pivot until the k‑th largest lands | Expected `O(m n)` time, `O(m n)` space (for `arr`) |

Both approaches are linear in the size of the matrix after the prefix step.  

---

## 🧩 2. Code Implementations

Below are clean, production‑ready implementations in **Java, Python, and C++**.

### 2.1 Java – Two Approaches

```java
import java.util.PriorityQueue;
import java.util.Random;

public class Solution {

    /* ---------- 1️⃣  Prefix XOR + Min-Heap ---------- */
    public int kthLargestValueHeap(int[][] matrix, int k) {
        int m = matrix.length, n = matrix[0].length;
        int[][] pref = new int[m + 1][n + 1];
        PriorityQueue<Integer> minHeap = new PriorityQueue<>(k);

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                pref[i][j] = matrix[i - 1][j - 1]
                            ^ pref[i - 1][j]
                            ^ pref[i][j - 1]
                            ^ pref[i - 1][j - 1];
                minHeap.offer(pref[i][j]);
                if (minHeap.size() > k) minHeap.poll();
            }
        }
        return minHeap.peek();
    }

    /* ---------- 2️⃣  Prefix XOR + QuickSelect ---------- */
    public int kthLargestValue(int[][] matrix, int k) {
        int m = matrix.length, n = matrix[0].length;
        int[][] pref = new int[m + 1][n + 1];
        int[] arr = new int[m * n];
        int idx = 0;

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                pref[i][j] = matrix[i - 1][j - 1]
                        ^ pref[i - 1][j]
                        ^ pref[i][j - 1]
                        ^ pref[i - 1][j - 1];
                arr[idx++] = pref[i][j];
            }
        }

        int target = arr.length - k;          // kth largest = (len-k)th smallest
        quickSelect(arr, 0, arr.length - 1, target);
        return arr[target];
    }

    private void quickSelect(int[] a, int left, int right, int k) {
        if (left >= right) return;
        int pivot = medianOfThree(a, left, right);
        int i = left, j = right;
        while (i <= j) {
            while (a[i] < pivot) i++;
            while (a[j] > pivot) j--;
            if (i <= j) swap(a, i++, j--);
        }
        if (k <= j) quickSelect(a, left, j, k);
        else        quickSelect(a, i, right, k);
    }

    private int medianOfThree(int[] a, int l, int r) {
        int m = (l + r) >>> 1;
        int aL = a[l], aM = a[m], aR = a[r];
        if (aL > aM) { int tmp = aL; aL = aM; aM = tmp; }
        if (aM > aR) { int tmp = aM; aM = aR; aR = tmp; }
        if (aL > aM) { int tmp = aL; aL = aM; aM = tmp; }
        return aM;
    }

    private void swap(int[] a, int i, int j) {
        int tmp = a[i]; a[i] = a[j]; a[j] = tmp;
    }
}
```

> **Why two methods?**  
> - `kthLargestValueHeap` is simple, deterministic, and uses only `O(k)` extra memory.  
> - `kthLargestValue` is faster in practice for large `k` because QuickSelect has linear expected time.

### 2.2 Python – Min‑Heap + QuickSelect

```python
import heapq
import random
from typing import List

class Solution:
    # ---------- 1️⃣  Prefix XOR + Min-Heap ----------
    def kthLargestValueHeap(self, matrix: List[List[int]], k: int) -> int:
        m, n = len(matrix), len(matrix[0])
        pref = [[0]*(n+1) for _ in range(m+1)]
        min_heap = []

        for i in range(1, m+1):
            for j in range(1, n+1):
                pref[i][j] = (matrix[i-1][j-1] ^ pref[i-1][j] ^
                              pref[i][j-1] ^ pref[i-1][j-1])
                heapq.heappush(min_heap, pref[i][j])
                if len(min_heap) > k:
                    heapq.heappop(min_heap)
        return min_heap[0]

    # ---------- 2️⃣  Prefix XOR + QuickSelect ----------
    def kthLargestValue(self, matrix: List[List[int]], k: int) -> int:
        m, n = len(matrix), len(matrix[0])
        pref = [[0]*(n+1) for _ in range(m+1)]
        arr = []

        for i in range(1, m+1):
            for j in range(1, n+1):
                pref[i][j] = (matrix[i-1][j-1] ^ pref[i-1][j] ^
                              pref[i][j-1] ^ pref[i-1][j-1])
                arr.append(pref[i][j])

        target = len(arr) - k                # kth largest -> (len-k)th smallest
        self.quick_select(arr, 0, len(arr)-1, target)
        return arr[target]

    def quick_select(self, a: List[int], left: int, right: int, k: int):
        if left >= right:
            return
        pivot = self.median_of_three(a, left, right)
        i, j = left, right
        while i <= j:
            while a[i] < pivot:
                i += 1
            while a[j] > pivot:
                j -= 1
            if i <= j:
                a[i], a[j] = a[j], a[i]
                i += 1
                j -= 1
        if k <= j:
            self.quick_select(a, left, j, k)
        else:
            self.quick_select(a, i, right, k)

    def median_of_three(self, a, l, r):
        m = (l + r) >> 1
        trio = sorted([a[l], a[m], a[r]])
        return trio[1]
```

> **Note** – Python’s built‑in `heapq` is a **min‑heap**. QuickSelect is implemented manually because Python has no native `nth_element`.

### 2.3 C++ – Min‑Heap & QuickSelect

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // ---------- 1️⃣  Prefix XOR + Min-Heap ----------
    int kthLargestValueHeap(vector<vector<int>>& matrix, int k) {
        int m = matrix.size(), n = matrix[0].size();
        vector<vector<int>> pref(m+1, vector<int>(n+1, 0));
        priority_queue<int, vector<int>, greater<int>> minHeap;

        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                pref[i][j] = matrix[i-1][j-1]
                          ^ pref[i-1][j]
                          ^ pref[i][j-1]
                          ^ pref[i-1][j-1];
                minHeap.push(pref[i][j]);
                if ((int)minHeap.size() > k) minHeap.pop();
            }
        }
        return minHeap.top();
    }

    // ---------- 2️⃣  Prefix XOR + QuickSelect ----------
    int kthLargestValue(vector<vector<int>>& matrix, int k) {
        int m = matrix.size(), n = matrix[0].size();
        vector<vector<int>> pref(m+1, vector<int>(n+1, 0));
        vector<int> arr; arr.reserve(m*n);
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                pref[i][j] = matrix[i-1][j-1]
                        ^ pref[i-1][j]
                        ^ pref[i][j-1]
                        ^ pref[i-1][j-1];
                arr.push_back(pref[i][j]);
            }
        }
        int target = arr.size() - k;          // kth largest -> (size-k)th smallest
        nth_element(arr.begin(), arr.begin()+target, arr.end(),
                    [](int a, int b){ return a > b; });
        return arr[target];
    }
};
```

> **C++ `nth_element`**  
> The STL `nth_element` implements QuickSelect internally and runs in linear expected time.  
> We give it a *descending* comparison (`a > b`) so that the `k‑th` largest ends up at index `size-k`.

---

## 🏎️ Performance Summary

| Approach | Time | Extra Space | Practical Notes |
|----------|------|-------------|-----------------|
| Prefix + Min‑heap | **O(m n log k)** | O(k) | Deterministic, great for `k << m·n`. |
| Prefix + QuickSelect | **O(m n)** expected | O(m n) (array `arr`) | Faster for large `k`; uses `nth_element` / custom partition. |
| Prefix + Sort | **O(m n log (m n))** | O(m n) | Straightforward but slower; rarely used in interviews. |

> **Space consumption**  
> The 2‑D prefix table consumes `(m+1) × (n+1)` integers (~4 MB for a 1000×1000 matrix).  
> Flattening adds another `m·n` integers (~4 MB).  
> All in all, < 10 MB – perfectly safe for LeetCode’s 256 MB limit.

---

## 📌 3. The Good, The Bad, The Ugly

| Category | What’s *Good* | What’s *Bad* | What’s *Ugly* |
|----------|---------------|--------------|---------------|
| **Algorithmic elegance** | Combines DP + XOR + heap/QuickSelect in one pass. | None (fully deterministic). | None. |
| **Time complexity** | Heap: `O(mn log k)` → good for small k. QuickSelect: expected `O(mn)` → excellent for large k. | No, the naive `O((mn)^2)` method is dead. | For worst‑case QuickSelect (always picking worst pivot) → `O((mn)^2)`. Mitigated by random pivot or median‑of‑three. |
| **Space usage** | Min‑heap: `O(k)` → very memory‑friendly. | QuickSelect: needs a flattened array of `mn` ints → extra `O(mn)` memory. | When `k` is almost `m·n`, heap still keeps all values (k≈mn) → space cost grows to `O(mn)`. |
| **Coding complexity** | Heap implementation is short and clean. | QuickSelect requires careful partitioning and median‑of‑three. | C++ `nth_element` is trivial, but customizing comparator may confuse novices. |
| **Edge‑case safety** | Handles `k=1` (largest) and `k=m·n` (smallest) seamlessly. | QuickSelect must guard against empty sub‑arrays (always `pref[i][j]` > 0). | None. |
| **Readability** | Java’s `PriorityQueue` + loops is highly readable. | Python’s `heapq` is concise; QuickSelect can be a bit verbose. | C++ with STL `nth_element` is short but may hide the selection logic. |
| **Interview value** | Demonstrates DP (prefix sums) + data‑structures (heap). | Shows you can apply QuickSelect, a classic selection algorithm. | Highlights STL usage. |

---

## 💡 4. Why LeetCode 1738 Is Interview‑Gold

1. **It tests multi‑disciplinary thinking**  
   - Dynamic programming (prefix XOR)  
   - Data‑structure choice (heap vs. array)  
   - Algorithmic selection (QuickSelect)

2. **It has a clear “right answer”** – no randomness, just the deterministic k‑th largest value.

3. **It forces you to reason about space/time trade‑offs** – something interviewers love to hear.

4. **It’s a hidden gem in the “prefix sum” family** – many candidates forget the XOR variant.

---

## 📣 SEO‑Friendly Blog Post

> **Title**: *Finding the Kth Largest XOR Coordinate Value – LeetCode 1738 (Java, Python, C++)*  
> **Meta‑Description**: Solve LeetCode 1738 “Find Kth Largest XOR Coordinate Value” with fast Java, Python, and C++ code. Learn the prefix‑XOR trick, min‑heap vs. QuickSelect, and interview tips.  

---

### 1️⃣ Introduction

In coding interviews, LeetCode 1738 often appears as a “medium”‑level problem that can be solved in **O(m n)** time. It blends a **dynamic‑programming** prefix table with a classic **selection algorithm**. Mastering this problem shows you can juggle bitwise operations, two‑dimensional arrays, and efficient data‑structures.

---

### 2️⃣ Problem Summary

> For a matrix, compute the bitwise XOR of every prefix sub‑matrix and then return the **k‑th largest** of those XOR values.

---

### 3️⃣ Key Insight: Prefix‑XOR Table

| Step | Formula | Why it works |
|------|---------|--------------|
| `pref[i][j] = matrix[i‑1][j‑1] ^ pref[i‑1][j] ^ pref[i][j‑1] ^ pref[i‑1][j‑1]` | Uses inclusion‑exclusion with XOR (note: XOR is associative & commutative). | After filling the table, `pref[i][j]` holds the XOR of the rectangle `(1,1)` → `(i,j)`. No recomputation needed. |

The table takes one pass; every entry is computed in O(1). So we get all prefix XORs in a single `O(m n)` scan.

---

### 3️⃣ Two Fast Solutions

#### A) Prefix + Min‑heap

1. Build prefix table.  
2. Push each value onto a **min‑heap** (`PriorityQueue` in Java, `heapq` in Python).  
3. Keep heap size ≤ k; pop when it exceeds.  
4. Top of the heap is the answer.

**Pros**: Deterministic, very space‑efficient (`O(k)`).  
**Cons**: Slightly slower for large `k` because of the log factor.

#### B) Prefix + QuickSelect / `nth_element`

1. Build prefix table and push all values into an array.  
2. Compute `target = size - k`.  
3. Use `nth_element` (C++) or a custom QuickSelect (Java/Python) to partition the array so that the `k‑th` largest ends up at index `target`.  
4. Return that element.

**Pros**: Linear expected time, perfect for large `k`.  
**Cons**: Requires a custom partition; some candidates find it error‑prone.

---

### 4️⃣ Language‑Specific Code Snippets

- **Java** – `PriorityQueue` + loops; QuickSelect with median‑of‑three.  
- **Python** – `heapq` + loops; manual QuickSelect.  
- **C++** – STL `nth_element` with a descending comparator.  

(Insert code blocks from the sections above.)

---

### 5️⃣ Interview Tips

- **Explain your choice**: If `k` is small, justify the heap; if `k` is large, mention QuickSelect.  
- **Talk about XOR properties**: Associative, commutative, and that `x ^ x = 0`.  
- **Mention edge‑cases**: `k=1` and `k=m·n`.  
- **Show trade‑offs**: Memory vs. speed.

---

### 6️⃣ Complexity Table

(Include the performance table from earlier.)

---

### 7️⃣ Final Thoughts

LeetCode 1738 is not just another medium problem. It’s a textbook example of how to turn a seemingly hard “k‑th largest” challenge into an **O(m n)** solution. Whether you’re coding in Java, Python, or C++, the key lies in that simple prefix‑XOR formula and a smart choice of data‑structure.

Happy coding! 🚀

---

## 🔚 5. Next Steps

- Run the provided snippets on LeetCode.  
- Practice edge cases: single‑row, single‑column, k=1, k=m·n.  
- Try to *modify* the solution to return the **k‑th smallest** (just reverse comparison).  
- Discuss trade‑offs with a friend or mentor.

---

> **Happy Interview‑Preparing!**  
> Remember: a clear explanation is often more valuable than a fancy solution.  

--- 

*End of article.*