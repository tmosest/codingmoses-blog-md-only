---
title: LeetCode 3277. Maximum XOR Score Subarray Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap  

**Maximum XOR Score Subarray Queries (LeetCode #3277)**  

You are given an integer array `nums` (length ≤ 2000) and up to 10⁵ queries  
`queries[i] = [lᵢ, rᵢ]`.  
For each query you must report the **maximum XOR‑score** that can be obtained
by taking **any** sub‑array of `nums[lᵢ…rᵢ]`.

> **XOR‑score of an array** `a` is defined by repeatedly doing  
>  `a[i] ← a[i] XOR a[i+1]` for every valid `i` *simultaneously* and then
>  deleting the last element, until only one element remains.  
>  That single remaining element is the score.

Examples are provided in the statement; the key observation is that the
score of a sub‑array is *not* the XOR of all its elements, but it can be
computed efficiently with dynamic programming.

---

## 2.  Algorithm – Bottom‑up DP  

Let  

* `xor[i][j]`  – XOR‑score of the *exact* sub‑array `nums[i…j]`  
  (i.e. the score if we start with this sub‑array and **do not** consider
  any inner sub‑arrays).
* `best[i][j]` – **maximum** XOR‑score over **all** sub‑arrays inside
  `nums[i…j]`.

### Recurrence

```
xor[i][i] = nums[i]
best[i][i] = nums[i]

for len = 2 … n:
    for i = 0 … n-len:
        j = i + len - 1
        xor[i][j]   = xor[i][j-1] ^ xor[i+1][j]           // merge two halves
        best[i][j]  = max( best[i][j-1],                  // extend to the left
                          best[i+1][j],                   // extend to the right
                          xor[i][j] )                     // take the whole [i..j]
```

The first line of the inner loop merges the two *adjacent* halves that are
already known; the second line picks the best among:

1. best sub‑array that ends at `j-1` (`best[i][j-1]`);
2. best sub‑array that starts at `i+1` (`best[i+1][j]`);
3. the whole sub‑array `nums[i…j]` itself (`xor[i][j]`).

Once the tables are filled, answer each query in **O(1)** time by
`best[l][r]`.

### Why it works

The transformation rule is associative in the following sense:

```
score( [x, y, z] ) = (score([x, y])  XOR  score([y, z]))  (the last element
                           after the first reduction)
```

Therefore `xor[i][j] = xor[i][j-1] ^ xor[i+1][j]` is valid for all
`i < j`.  
The maximum inside a range can only come from one of the two sub‑ranges
plus the whole range itself – hence the `max()` recurrence.

---

## 3.  Correctness Proof  

We prove that the DP indeed returns the correct answer for every query.

### Lemma 1  
For any `i ≤ j`, `xor[i][j]` computed by the recurrence equals the XOR‑score
obtained if we *start* with the sub‑array `nums[i…j]` and never take a
shorter sub‑array inside it.

*Proof.*  
Base case `len = 1`: trivially the score of a single element is that
element.  
Induction step: assume the lemma holds for all lengths `< len`.  
For `nums[i…j]` the first reduction creates two numbers  

```
L = xor[i][j-1]   (score of nums[i…j-1])
R = xor[i+1][j]   (score of nums[i+1…j])
```

and the new element is `L XOR R`.  
By induction hypothesis `xor[i][j-1]` and `xor[i+1][j]` already hold the
correct scores of the two halves, so the recurrence
`xor[i][j] = xor[i][j-1] ^ xor[i+1][j]` gives the correct result. ∎



### Lemma 2  
For any `i ≤ j`, `best[i][j]` equals the maximum XOR‑score over **all**
sub‑arrays of `nums[i…j]`.

*Proof.*  
Base case `i=j`: only one sub‑array exists, so `best[i][i] = nums[i]`.  
Induction step: assume the lemma holds for all shorter lengths.  
Every sub‑array of `nums[i…j]` is either

1. inside `nums[i…j-1]`, or
2. inside `nums[i+1…j]`, or
3. the whole `nums[i…j]` itself.

By the induction hypothesis, the best scores of (1) and (2) are
`best[i][j-1]` and `best[i+1][j]` respectively, while (3) is `xor[i][j]`.  
The maximum of the three is therefore the maximum over **all** sub‑arrays,
hence the recurrence for `best[i][j]` is correct. ∎



### Theorem  
After filling the tables, for any query `[l, r]` the answer `best[l][r]`
is the maximum XOR‑score obtainable from a sub‑array of `nums[l…r]`.

*Proof.*  
Immediate from Lemma 2: `best[l][r]` is defined exactly as the maximum
over all sub‑arrays of that range. ∎



---

## 4.  Complexity Analysis  

* **Time**  
  * Building the two tables: `∑_{len=2}^{n} (n-len+1) = O(n²)`  
  * Answering `q` queries in O(1) each: `O(q)`  
  * **Total:** `O(n² + q)`  
  With `n ≤ 2000`, `n² = 4 000 000`, easily fast enough.

* **Space**  
  Two `n × n` integer tables → `2 * n² * 4 bytes ≈ 32 MB` in Java
  (less in C++/Python due to memory layout).  
  Acceptable under LeetCode’s 512 MB limit.

* **Practical notes**  
  * Use **short‑circuit** `for` loops to keep the constant factor low.  
  * In Java, prefer `int[][]` rather than `ArrayList` to avoid boxing.  
  * In Python, a list of lists works fine; `sys.setrecursionlimit` is
    *not* needed because we use an iterative DP.

---

## 5.  Reference Implementations  

> **All three versions use the same bottom‑up DP.  
>  You may copy‑paste the snippet that matches your favourite language.**

### 5.1 Java 17

```java
import java.util.*;

public class Solution {
    public int[] maximumSubarrayXor(int[] nums, int[][] queries) {
        int n = nums.length;
        int[][] xor  = new int[n][n];
        int[][] best = new int[n][n];

        // base cases: length 1
        for (int i = 0; i < n; i++) {
            xor[i][i]  = nums[i];
            best[i][i] = nums[i];
        }

        // fill by increasing length
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i + len <= n; i++) {
                int j = i + len - 1;
                xor[i][j]   = xor[i][j-1] ^ xor[i+1][j];
                best[i][j]  = Math.max(best[i][j-1],
                                       Math.max(best[i+1][j], xor[i][j]));
            }
        }

        int[] ans = new int[queries.length];
        for (int k = 0; k < queries.length; k++) {
            int l = queries[k][0];
            int r = queries[k][1];
            ans[k] = best[l][r];
        }
        return ans;
    }
}
```

### 5.2 Python 3

```python
def maximumSubarrayXor(nums, queries):
    n = len(nums)
    xor = [[0] * n for _ in range(n)]
    best = [[0] * n for _ in range(n)]

    # base case
    for i in range(n):
        xor[i][i] = nums[i]
        best[i][i] = nums[i]

    for length in range(2, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            xor[i][j] = xor[i][j-1] ^ xor[i+1][j]
            best[i][j] = max(best[i][j-1], best[i+1][j], xor[i][j])

    return [best[l][r] for l, r in queries]
```

### 5.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> maximumSubarrayXor(vector<int>& nums,
                                   vector<vector<int>>& queries) {
        int n = nums.size();
        vector<vector<int>> xorTbl(n, vector<int>(n));
        vector<vector<int>> best(n, vector<int>(n));

        // base case
        for (int i = 0; i < n; ++i) {
            xorTbl[i][i] = best[i][i] = nums[i];
        }

        // DP by increasing length
        for (int len = 2; len <= n; ++len) {
            for (int i = 0; i + len <= n; ++i) {
                int j = i + len - 1;
                xorTbl[i][j] = xorTbl[i][j-1] ^ xorTbl[i+1][j];
                best[i][j]   = max({best[i][j-1], best[i+1][j], xorTbl[i][j]});
            }
        }

        vector<int> ans;
        ans.reserve(queries.size());
        for (auto &q : queries) {
            ans.push_back(best[q[0]][q[1]]);
        }
        return ans;
    }
};
```

---

## 6.  Alternative (Memory‑Efficient) Approach  

If you’re tight on memory (e.g. n = 2000 is still fine, but you may want
to drop a 32 MB table), you can process queries *by length*:

1. Group query indices by `len = r-l`.
2. Keep a single array `cur` that stores the current maximum scores while
   we perform the XOR reductions in place on `nums`.
3. Whenever the current reduction step’s length matches a query’s length,
   answer all those queries with `cur[l]`.

This yields a **O(n²)** time and **O(n)** space solution
(see the “by‑length” editorial on LeetCode).

---

## 7.  Why this matters for job interviews

* **Data structure choice** – the two‑dimensional DP is a classic
  dynamic‑programming pattern; recruiters value a clean, cache‑friendly
  implementation.
* **Algorithmic thinking** – you should be able to explain the transformation
  rule and why the maximum inside a range must come from the two
  sub‑ranges plus the whole range.
* **Complexity reasoning** – interviewers love when you state the
  `O(n² + q)` complexity and discuss why it satisfies the constraints.
* **Edge cases** – remember to test single‑element ranges and the
  longest possible length.

---

## 7.  Final Checklist Before Submitting  

- [ ] Verify the environment: Java 17, Python 3.8+, C++17.  
- [ ] Test with the sample cases given in the problem.  
- [ ] Run a stress test: random `n` (≤ 2000) and many queries to ensure
  you stay within the time limit (≈ 0.2 s on LeetCode).  
- [ ] Check for integer overflow?  
  The XOR score never exceeds `2³²-1` because each element is an `int`.  

---

## 8.  Why This Solution is a Good Interview “Talk‑about”

* **Solid algorithmic concept** – dynamic programming over intervals.  
* **Proof‑based reasoning** – you can walk through the induction steps
  in an interview.  
* **Scalability** – time complexity is independent of the number of
  queries; only the pre‑processing costs matter.  
* **Hands‑on coding** – the reference implementation is short and
  clean, so you can code it in a minute if asked.

Good luck on your interview! 🚀

---

### 9.  Bonus – Blog‑style Summary  

> *If you want a quick read‑through, scroll down to “Blog Summary”.*

#### Blog Summary

> *“A single‑page, no‑frills explanation of the DP that solves the
>  ‘Maximum XOR Sub‑array’ problem on LeetCode, including correctness
>  proof, complexity, and code snippets in Java, Python, and C++.”*

---

## 10.  Glossary  

| Symbol | Meaning |
|--------|---------|
| `n` | length of `nums` |
| `xor[i][j]` | XOR‑score of `nums[i…j]` when you start with that range |
| `best[i][j]` | maximum XOR‑score among all sub‑arrays of `nums[i…j]` |
| `len` | current sub‑array length (used for DP) |
| `queries` | list of `[l, r]` pairs (0‑based indices) |

--- 

> **Enjoy coding!** If you run into timeouts, double‑check that you
>  compiled with `-O2` (C++), used `sys.setrecursionlimit` only if you
>  switched to a recursive version, and avoided unnecessary copying.