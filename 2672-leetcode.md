---
title: LeetCode 2672. Number of Adjacent Elements With the Same Color - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Problem recap**

You have an array `colors[0…n‑1]` (initially all zeros).  
For each query `[i, c]` you paint position `i` with colour `c`.  
After every query you must output the number of *adjacent pairs* of cells that have the **same non‑zero colour**.  
For example, after painting positions `[0,1,2]` with colours `[1,1,2]` the answer is `1` (pair `(0,1)`).

**Key observation**

When you repaint position `i` only the two neighbours of `i` can change the pair count:

```
      i-1   i   i+1
```

All other cells keep their relative colours.  
So for each query we can update the global pair counter by:

1. **Removing** the pairs that existed with the *old* colour of `i`.
2. **Adding** the pairs that will exist with the *new* colour `c`.

That is all that is needed – we never need to scan the whole array.

--------------------------------------------------------------------

### Algorithm
```
pair_cnt = 0                     # number of adjacent equal pairs
colors = [0] * n                 # current colours (0 means unpainted)

for (idx, new_colour) in queries:
    old_colour = colors[idx]

    # 1. Remove pairs that involve the old colour
    if old_colour != 0:
        if idx > 0   and colors[idx-1] == old_colour: pair_cnt -= 1
        if idx+1 < n and colors[idx+1] == old_colour: pair_cnt -= 1

    # 2. Paint with new colour
    colors[idx] = new_colour

    # 3. Add pairs that involve the new colour
    if idx > 0   and colors[idx-1] == new_colour: pair_cnt += 1
    if idx+1 < n and colors[idx+1] == new_colour: pair_cnt += 1

    record pair_cnt as answer for this query
```

--------------------------------------------------------------------

### Correctness proof  

We prove that the algorithm outputs the correct number of equal‑colour adjacent
pairs after each query.

---

#### Lemma 1  
Before processing a query, `pair_cnt` equals the number of equal‑colour adjacent
pairs in the current array.

*Proof.*  
Initially all cells are unpainted, no pairs exist and `pair_cnt = 0`.  
When a query is processed, the algorithm updates `pair_cnt` exactly as described
in steps 1–3 (removing old pairs, adding new ones).  
Thus after the update, `pair_cnt` reflects the current array. ∎



#### Lemma 2  
During step 1 of a query the algorithm decreases `pair_cnt` **once** for each
pair that disappears because the old colour of position `i` is replaced.

*Proof.*  
Only two pairs can involve the old colour of position `i`:
- with the left neighbour (`i-1`);
- with the right neighbour (`i+1`).

If either neighbour had the same non‑zero colour as the old one, that pair
really existed and must be removed; the algorithm checks each neighbour
separately and subtracts 1 per existing pair.  
No other pair can be destroyed by repainting `i`. ∎



#### Lemma 3  
During step 3 of a query the algorithm increases `pair_cnt` **once** for each
new pair created by painting position `i` with the new colour.

*Proof.*  
After the repaint, the only possible new pairs are with the left neighbour and
with the right neighbour.  
If either neighbour has the same non‑zero colour as the new one, a new pair
appears; the algorithm adds 1 for each such neighbour.  
No other pair is created because all other cells keep their relative colours. ∎



#### Theorem  
After processing a query, the value stored in the answer array is exactly the
number of adjacent pairs of equal, non‑zero colour in the array.

*Proof.*  
Consider a query on position `i`.  
Let `P_old` be the set of pairs involving `i` before repaint,
`P_new` be the set after repaint, and `P_other` be all other pairs that do not involve `i`.

By Lemma 2 the algorithm subtracts `|P_old|` from `pair_cnt`;  
by Lemma 3 it adds `|P_new|`.  
`pair_cnt` was `|P_old| + |P_other|` before the query (Lemma 1).  
After the updates it becomes `|P_other| + |P_new|`, which is precisely the
current number of pairs.  
The value stored in the answer array is therefore correct. ∎



--------------------------------------------------------------------

### Complexity analysis

- **Time:**  
  Each query performs O(1) work (constant number of comparisons).  
  For `m` queries, total time is **O(m)**.

- **Memory:**  
  We keep the colour array of size `n` plus the result list of size `m`.  
  Space complexity is **O(n + m)** (the array dominates).

--------------------------------------------------------------------

### Reference implementation (Python 3)

```python
from typing import List

class Solution:
    def colorTheArray(self, n: int, queries: List[List[int]]) -> List[int]:
        colors = [0] * n          # current colours, 0 means unpainted
        res = []
        pair_cnt = 0

        for idx, new_colour in queries:
            old_colour = colors[idx]

            # remove old pairs that involve position idx
            if old_colour != 0:
                if idx > 0 and colors[idx - 1] == old_colour:
                    pair_cnt -= 1
                if idx + 1 < n and colors[idx + 1] == old_colour:
                    pair_cnt -= 1

            # paint with the new colour
            colors[idx] = new_colour

            # add new pairs that involve position idx
            if idx > 0 and colors[idx - 1] == new_colour:
                pair_cnt += 1
            if idx + 1 < n and colors[idx + 1] == new_colour:
                pair_cnt += 1

            res.append(pair_cnt)

        return res
```

The same logic can be written in Java, C++ or any language – just translate
the constant‑time neighbour checks.

--------------------------------------------------------------------

**Enjoy coding!**