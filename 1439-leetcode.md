---
title: LeetCode 1439. Find the Kth Smallest Sum of a Matrix With Sorted Rows - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Problem

> **LeetCode 1439 – Find the Kth Smallest Sum of a Matrix With Sorted Rows**  
> **Difficulty**: Hard  

You are given an `m × n` matrix `mat` whose rows are sorted in non‑decreasing order.  
From each row you must pick **exactly one** element, producing an array of length `m`.  
Among all possible arrays, you need the **kth smallest sum** of its elements.

```
Input
mat = [[1,3,11],
       [2,4,6]]
k   = 5

Output
7
```

The first five smallest sums are  
`[1,2] → 3`, `[1,4] → 5`, `[3,2] → 5`, `[3,4] → 7`, `[1,6] → 7`.  
The 5th sum is `7`.

`1 ≤ m,n ≤ 40`, `1 ≤ k ≤ min(200, m*n)`, `1 ≤ mat[i][j] ≤ 5000`.



--------------------------------------------------------------------

## 2.  The Idea – “Keep the Best k Sums”

The number of all possible sums can be astronomically large (`n^m`).  
Because **k is tiny (≤ 200)** we can avoid enumerating everything.

**Key Observation**  
When you have processed the first `i` rows, you only need the *k smallest* partial sums.  
Any sum that is not among those k can never become part of a final k‑th smallest sum –  
it would already be larger than at least k other partial sums.

So we iterate row by row, maintaining a *max‑heap of size at most k* that always
stores the k smallest sums seen so far.  
When a new row arrives, we combine every current partial sum with each element of
that row and push the new sums into a new heap, discarding the largest ones so the
size never exceeds k.

The algorithm is:

```
heap ← [0]                     // one sum before any rows are processed
for each row r in mat:
    newHeap ← empty max‑heap
    for each sum s in heap:
        for each element v in first min(k, n) elements of r:
            newHeap.push(s + v)
            if newHeap.size > k:
                newHeap.pop()  // remove the largest
    heap ← newHeap

return heap.top()             // the k‑th smallest sum
```

Because each row has at most `k` relevant columns (`k` ≤ 200), the inner loops are
very small.  
Time complexity: **O(m · n · k · log k)**  
Space complexity: **O(k)**

The same idea can be implemented with a *min‑heap* if you first generate all
candidate sums and pop the smallest k times, but the max‑heap keeps the memory
footprint low and gives the answer in a single pass.

--------------------------------------------------------------------

## 3.  Code – 3 Languages

### 3.1 Java

```java
import java.util.*;

public class Solution {
    public int kthSmallest(int[][] mat, int k) {
        int C = Math.min(mat[0].length, k);

        // max‑heap: largest element at the top
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        maxHeap.add(0);                      // starting sum

        for (int[] row : mat) {
            PriorityQueue<Integer> next = new PriorityQueue<>(Collections.reverseOrder());

            for (int prev : maxHeap) {
                for (int c = 0; c < C; ++c) {
                    next.add(prev + row[c]);
                    if (next.size() > k) next.poll();   // keep only k smallest
                }
            }
            maxHeap = next;
        }
        return maxHeap.peek();               // k‑th smallest sum
    }
}
```

### 3.2 Python

```python
import heapq
from typing import List

class Solution:
    def kthSmallest(self, mat: List[List[int]], k: int) -> int:
        C = min(len(mat[0]), k)

        # max‑heap via negative numbers
        max_heap = [0]          # start with sum 0
        for row in mat:
            new_heap = []
            for s in max_heap:
                for v in row[:C]:
                    heapq.heappush(new_heap, -(s + v))
                    if len(new_heap) > k:
                        heapq.heappop(new_heap)   # drop largest
            max_heap = new_heap

        # top of the max‑heap (negative)
        return -max_heap[0]
```

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int kthSmallest(vector<vector<int>>& mat, int k) {
        int C = min((int)mat[0].size(), k);
        // max‑heap: priority_queue keeps largest on top
        priority_queue<int> maxHeap;
        maxHeap.push(0);                      // starting sum

        for (const auto& row : mat) {
            priority_queue<int> next;
            while (!maxHeap.empty()) {
                int s = maxHeap.top(); maxHeap.pop();
                for (int i = 0; i < C; ++i) {
                    next.push(s + row[i]);
                    if ((int)next.size() > k) next.pop(); // keep k smallest
                }
            }
            maxHeap.swap(next);
        }
        return maxHeap.top();                 // k‑th smallest sum
    }
};
```

All three solutions run in **O(m · n · k · log k)** time and use **O(k)** memory –
perfect for the constraints (`m, n ≤ 40`, `k ≤ 200`).

--------------------------------------------------------------------

## 4.  Blog Article – “The Good, The Bad, The Ugly of LeetCode Hards”

> **Title**: *The Good, The Bad, and The Ugly of LeetCode Hard Problems – A Job‑Ready Guide*  
> **Meta‑description**: Master LeetCode 1439, the “kth smallest sum” matrix puzzle, with Java, Python, and C++ solutions. Learn how to turn hard problems into interview wins.  
> **Keywords**: LeetCode Hard, 1439, kth smallest sum matrix, interview prep, job interview, algorithm design, max‑heap, min‑heap, job interview tips

---

### 🚀 Introduction

Interviewers love hard LeetCode problems. They are the *signature* of a good engineer.  
But do they truly indicate skill, or are they just “puzzles that make you sweat”?  
Let’s break it down with one of the most talked‑about hard questions: **LeetCode 1439 – Find the Kth Smallest Sum of a Matrix With Sorted Rows**.

> **Why it matters**  
> * 1439 appears in most “Hard” interview prep lists.  
> * It forces you to think about **space‑efficient** solutions, not just brute force.  
> * Mastering it signals you can handle **dynamic programming + heaps**—a gold‑mine for senior roles.

---

### 🔎 The Good – Turning a Hard Problem into an Interview Gold‑Mine

1. **Clear constraints**  
   *`k ≤ 200`* is a tiny number; the trick is to use it.  
   Interviewers love it when you spot that you can *prune* the search space.

2. **Reusable pattern**  
   The “keep the best k sums” strategy appears in  
   * [LeetCode 373 – Find K Pairs with Smallest Sums](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/)  
   * [LeetCode 373 – K Smallest Pairs](https://leetcode.com/problems/k-th-smallest-pair-distance/)  
   Practicing one hard problem teaches you a reusable pattern: **max‑heap of fixed size**.

3. **Language‑agnostic solution**  
   All three languages above solve the same problem in the same way.  
   That’s the perfect talking point: *“I can write clean, efficient Java, Python, and C++ code.”*

---

### ❌ The Bad – Common Pitfalls That Make You Lose

| Pitfall | Why it hurts | Fix |
|---------|--------------|-----|
| **Brute force enumeration** | Exponentially large space → Time‑outs | Use k‑pruning via heaps |
| **Ignoring “k ≤ 200”** | Combine all n elements → O(n²) per row | Process only the first `min(k, n)` elements |
| **Wrong heap type** | Max‑heap logic with min‑heap leads to O(k²) pop/push | Use max‑heap of size k, pop the largest when exceeding |
| **Neglecting edge cases** | Single‑row matrix, rows with `n < k` | Handle `m == 1` and `C = min(n, k)` |

When you fall into any of these traps, the interviewer will see a *lack of insight*.
A clean, memory‑efficient solution demonstrates algorithmic maturity.

---

### 😈 The Ugly – What Interviewers Look For (and How to Avoid It)

| Ugly Symptom | What Interviewers Notice | How to Fix |
|--------------|--------------------------|------------|
| **“I tried all combinations, it timed out.”** | Shows lack of *pruning* thinking | Spot the k‑pruning trick, use a heap |
| **“I used recursion and got stack overflow.”** | Over‑recursive DP without memoization | Use iterative heap merging |
| **“I returned the wrong element (min‑heap vs max‑heap).”** | Mis‑understanding of heap top | Keep a max‑heap of size k; return its top |

**Takeaway**: If you can *explain* why a max‑heap of size k works, you’re already ahead.

---

## 5.  Interview‑Ready Tips

1. **Start with constraints**  
   *“Because k ≤ 200, we can keep only the best k partial sums.”*  
   Constraints guide the solution path.

2. **Explain the “why” before the “how”**  
   Interviewers value insight.  
   “We keep a max‑heap because any sum not among the k smallest partial sums can’t be in the final answer.”

3. **Show alternative approaches**  
   Mention the min‑heap pairing method, binary search on the answer, etc.  
   “Here’s a second way using a min‑heap of pairs.”

4. **Time/Space trade‑off**  
   Discuss the O(m·n·k·log k) vs. O(m·n·k) approach, and why you chose the max‑heap.

5. **Write clean code**  
   *Descriptive variable names*, *inline comments*, *type annotations* (Python 3.9+).  
   Interviewers check readability just as much as correctness.

6. **Practice edge cases**  
   *Single row, single column, all equal values, k = m*n.*  
   Be ready to run quick tests on your own.

7. **Relate to real life**  
   “In production, you often need the k‑smallest values from sorted streams – this algorithm is the same.”

---

## 6.  Closing Words – Get the Job

*Hard problems like LeetCode 1439 are not just puzzles; they’re a **testing ground** for your design instincts.*  
When you can walk through:

* The **constraints** → “Because k is small…”
* The **core idea** → “Keep the best k sums”
* The **clean implementation** → “Java, Python, C++”

you’ll demonstrate the very qualities hiring managers want: **algorithmic thinking, attention to detail, and the ability to write production‑ready code**.

So next time you see a hard problem, remember:  
**Good** – you see a pattern.  
**Bad** – you waste time enumerating everything.  
**Ugly** – you mis‑apply data structures.  

Avoid the bad and ugly; practice the good, and you’ll be ready to ace that interview and land the job. Happy coding!