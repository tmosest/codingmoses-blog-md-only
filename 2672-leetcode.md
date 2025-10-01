---
title: LeetCode 2672. Number of Adjacent Elements With the Same Color - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every cell we keep the current colour (`0` means “uncoloured”).  
After each query we have to report how many adjacent pairs of cells
have the same non‑zero colour.

```
      pair (i , i+1) is counted if
            colour[i] == colour[i+1]  and  colour[i] != 0
```

Only the two neighbours of the changed cell can influence the answer,
therefore we can update the count in constant time for each query.



--------------------------------------------------------------------

#### Algorithm
```
cnt = 0                          # number of counted pairs
colour[0 … n-1] = 0              # current colours

for each query (pos, new_col):
        old = colour[pos]

        # 1) remove contributions of the old colour
        if old != 0:
            if pos > 0   and colour[pos-1] == old:   cnt -= 1
            if pos+1 < n and colour[pos+1] == old:   cnt -= 1

        # 2) write the new colour
        colour[pos] = new_col

        # 3) add contributions of the new colour
        if pos > 0   and colour[pos-1] == new_col:   cnt += 1
        if pos+1 < n and colour[pos+1] == new_col:   cnt += 1

        output cnt
```

The array `colour` is updated after each query, so the next query works
with the current state.



--------------------------------------------------------------------

#### Correctness Proof  

We prove that the algorithm outputs the correct number of adjacent
pairs after each query.

---

##### Lemma 1  
Before a query is processed, `cnt` equals the number of adjacent pairs
with the same non‑zero colour in the current array `colour`.

**Proof.**

*Initialization:*  
Initially all cells are `0`.  
No pair satisfies the condition, hence `cnt = 0`.  
The lemma holds.

*Preservation:*  
Assume the lemma holds before a query.
During step 1 the algorithm subtracts `1` for each neighbour that has
the same colour as the old value of the changed cell.  
Exactly those pairs disappear because the cell will be recoloured.
During step 3 the algorithm adds `1` for each neighbour that has the
same colour as the new value.  
Exactly those new pairs appear after recolouring.

No other pairs are affected by the query.
Thus after the query `cnt` equals the new number of counted pairs. ∎



##### Lemma 2  
After executing a query, the algorithm outputs the true number of
adjacent pairs with the same colour.

**Proof.**

Immediately after step 3 the variable `cnt` contains, by Lemma 1,
the correct number of adjacent pairs for the updated array.
The algorithm outputs this value. ∎



##### Theorem  
For every query the algorithm prints the correct answer.

**Proof.**

By induction over all queries.  
Base case: before the first query Lemma 1 holds.  
Induction step:  
Assume the algorithm prints the correct value for the first `k-1`
queries.  
For query `k` Lemma 2 shows that the value printed is correct.
Therefore the algorithm is correct for all queries. ∎



--------------------------------------------------------------------

#### Complexity Analysis

For each query only a constant number of array accesses and
arithmetical operations are performed.

```
Time   :  O(m)   (m = number of queries)
Memory :  O(n)   (array of colours)
```

--------------------------------------------------------------------

#### Reference Implementation (Python 3)

```python
import sys

def solve() -> None:
    data = sys.stdin.buffer.read().split()
    if not data:
        return
    it = iter(data)
    n = int(next(it))
    m = int(next(it))

    colours = [0] * n          # current colours
    res = []
    cnt = 0                    # current number of equal adjacent pairs

    for _ in range(m):
        pos = int(next(it))
        new_col = int(next(it))
        old_col = colours[pos]

        # remove pairs created by the old colour
        if old_col != 0:
            if pos > 0 and colours[pos - 1] == old_col:
                cnt -= 1
            if pos + 1 < n and colours[pos + 1] == old_col:
                cnt -= 1

        # write new colour
        colours[pos] = new_col

        # add pairs created by the new colour
        if pos > 0 and colours[pos - 1] == new_col:
            cnt += 1
        if pos + 1 < n and colours[pos + 1] == new_col:
            cnt += 1

        res.append(cnt)

    sys.stdout.write("\n".join(map(str, res)))

if __name__ == "__main__":
    solve()
```

The program follows exactly the algorithm proven correct above and
conforms to the required `solve()` signature.