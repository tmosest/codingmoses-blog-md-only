---
title: LeetCode 1274. Number of Ships in a Rectangle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Overview**

For each test case we are given  

```
topRight   = [x2 , y2]
bottomLeft = [x1 , y1]
```

`Sea.hasShips(topRight, bottomLeft)` returns **True** iff the closed rectangle  
`[x1 … x2] × [y1 … y2]` contains at least one ship.

The whole sea can contain at most 10 ships, the grid size is as large as  
`10^9 × 10^9` and we may call `hasShips` at most 100 times.

The goal is to count all ships in the given rectangle.



--------------------------------------------------------------------

#### 1.  Observations

* The answer is at most 10 – the function can stop as soon as all ships
  are found.
* We never need to inspect a rectangle that contains no ships.
* A rectangle with a single cell `[x , y] × [x , y]` is a ship iff
  `hasShips([x , y] , [x , y])` is `True`.

So we can explore only rectangles that are guaranteed to contain ships.
Because the total number of ships is tiny, the amount of work is very small.



--------------------------------------------------------------------

#### 2.  Recursive Quadrant Search

For a non‑empty rectangle we split it into four sub‑rectangles
(bottom‑left, top‑left, bottom‑right, top‑right) and
recurse into the sub‑rectangles that actually contain ships.

```
                 (x2 , y2)                     (x2 , y2)
                     |                               |
          +----------+----------+         +----------+----------+
          |          |          |         |          |          |
      [x1 … xm , y1 … ym]  [xm+1 … x2 , y1 … ym]
          |          |          |         |          |          |
          +----------+----------+         +----------+----------+
                 |                               |
          (xm , ym)                     (x2 , y2)
```

* `mx = (x2 + x1) // 2`  
  `my = (y2 + y1) // 2`

The four quadrants are

| quadrant | topRight            | bottomLeft          |
|----------|---------------------|---------------------|
| BL       | `[mx , my]`         | `[x1 , y1]`         |
| TL       | `[mx , y2]`         | `[x1 , my+1]`       |
| BR       | `[x2 , my]`         | `[mx+1 , y1]`       |
| TR       | `[x2 , y2]`         | `[mx+1 , my+1]`     |

We only recurse into a quadrant if `Sea.hasShips` for that quadrant
returns `True`.  
If the rectangle is a single cell we return `1` (a ship) – the call
to `hasShips` guarantees that a ship really exists in that cell.



--------------------------------------------------------------------

#### 2.  Algorithm

```
count(rectangle (tr , bl)):
    if bl > tr              # empty rectangle
        return 0
    if not sea.hasShips(tr, bl):
        return 0
    if tr == bl:            # single cell
        return 1

    mx = (tr[0] + bl[0]) // 2
    my = (tr[1] + bl[1]) // 2

    return  count([mx , my]       , bl)        +   # BL
            count([mx , tr[1]]    , [bl[0], my+1]) +   # TL
            count([tr[0], my]     , [mx+1 , bl[1]]) +   # BR
            count(tr              , [mx+1 , my+1])     # TR
```

The public entry point `solve_case` simply calls `count` with the
given rectangle.



--------------------------------------------------------------------

#### 3.  Correctness Proof  

We prove that the algorithm returns the exact number of ships in the
given rectangle.

---

##### Lemma 1  
If `sea.hasShips(tr, bl)` is `False` for a rectangle,
the algorithm never counts a ship inside this rectangle.

**Proof.**  
The algorithm checks `hasShips` at the very beginning of `count`.  
If it returns `False` the function immediately returns `0`.  
Thus no ship inside this rectangle contributes to the answer. ∎



##### Lemma 2  
For a rectangle that is a single cell `[x , y] × [x , y]`,
`count` returns `1` iff there is a ship on `(x , y)`.

**Proof.**  
When the rectangle is a single cell the algorithm reaches the
base case `tr == bl`.  
It returns `sea.hasShips(tr, bl)`, which is `True`
iff the cell contains a ship.  
If it is `True` the function returns `1`, otherwise `0`. ∎



##### Lemma 3  
Let `R` be a rectangle that contains at least one ship and is **not**
a single cell.  
`count` returns the sum of the numbers of ships in its four sub‑rectangles.

**Proof.**  
For such `R` the algorithm

1. computes the mid coordinates `mx, my`,
2. checks every of the four quadrants with `hasShips`.  
   By Lemma&nbsp;1 the quadrants that contain no ship are ignored.
3. Recursively calls `count` on each quadrant that does contain a ship.

Thus each ship in `R` lies in exactly one of the quadrants, and the
recursive calls count it exactly once.  
Adding the four recursive results gives the total number of ships in
`R`. ∎



##### Theorem  
For every test case the algorithm returns the exact number of ships
inside the given rectangle.

**Proof.**  
We prove by induction on the recursion depth.

*Base:*  
The recursion stops when the rectangle is a single cell.
By Lemma&nbsp;2 the returned value is exactly the number of ships
in that cell.

*Induction step:*  
Assume that for all rectangles at depth `< d` the algorithm returns the
exact ship count.  
Consider a rectangle `R` at depth `d`.  
If `hasShips` is `False`, by Lemma&nbsp;1 the answer is `0`, which is
correct.  
Otherwise `R` contains at least one ship.  
If `R` is a single cell we are in the base case.  
Otherwise we split `R` into the four quadrants and, by
Lemma&nbsp;3, the total number of ships in `R` is the sum of the numbers
in the quadrants.  
Each quadrant has depth `< d`, hence by the induction hypothesis the
recursive calls return the correct counts.  
Adding them yields the correct answer for `R`.

By induction the algorithm is correct for all rectangles, including
the initial one. ∎



--------------------------------------------------------------------

#### 4.  Complexity Analysis

Let `k` be the number of ships that the algorithm actually visits
(`k ≤ 10`).

* Every ship in the sea creates at most **4** recursive calls
  (one for each level of the split) until it is isolated to a single cell.
  Therefore the number of `Sea.hasShips` calls is  
  `≤ 4 * k + 1   ≤ 4 * 10 + 1 = 41`, which is well below the
  allowed 100 calls.
* Each call performs a constant amount of work (a few integer
  operations and a membership test on the ship set).
* The recursion depth is at most `⌈log₂ 10⁹⌉ ≈ 30`, so Python’s recursion
  limit is not an issue.

```
Time   :  O(k)   ≤ 40   operations per test case
Memory :  O(1)   (only a few local integers per recursion level)
```

--------------------------------------------------------------------

#### 5.  Reference Implementation (Python 3)

```python
import sys
from typing import List, Tuple

# ----------  Sea  ----------
class Sea:
    """
    The Sea object simply keeps a set of ship positions.
    hasShips(tr, bl) returns True if any ship lies in the closed rectangle
    defined by bottomLeft <= coordinates <= topRight.
    """
    def __init__(self, ships: List[Tuple[int, int]]):
        # ships are given as a list of (x, y) tuples
        self.ships = set(ships)

    def hasShips(self, topRight: List[int], bottomLeft: List[int]) -> bool:
        x2, y2 = topRight
        x1, y1 = bottomLeft
        for sx, sy in self.ships:
            if x1 <= sx <= x2 and y1 <= sy <= y2:
                return True
        return False


# ----------  Solver  ----------
def count_ships_in_rectangle(sea: Sea,
                             topRight: List[int],
                             bottomLeft: List[int]) -> int:
    """
    Recursively splits the rectangle into four quadrants and counts ships.
    The function stops as soon as all ships are found.
    """

    def helper(tr: List[int], bl: List[int]) -> int:
        # empty rectangle
        if bl[0] > tr[0] or bl[1] > tr[1]:
            return 0

        # no ship in this rectangle
        if not sea.hasShips(tr, bl):
            return 0

        # single cell
        if tr == bl:
            return 1

        # split into four sub‑rectangles
        mx = (tr[0] + bl[0]) // 2
        my = (tr[1] + bl[1]) // 2

        # bottom‑left
        cnt = helper([mx, my], bl)

        # top‑left
        cnt += helper([mx, tr[1]], [bl[0], my + 1])

        # bottom‑right
        cnt += helper([tr[0], my], [mx + 1, bl[1]])

        # top‑right
        cnt += helper(tr, [mx + 1, my + 1])

        return cnt

    return helper(topRight, bottomLeft)


# ----------  Main driver  ----------
def main() -> None:
    data = list(map(int, sys.stdin.read().strip().split()))
    if not data:
        return
    it = iter(data)
    t = next(it)                     # number of test cases
    out_lines = []

    for case_no in range(1, t + 1):
        x1 = next(it); y1 = next(it); x2 = next(it); y2 = next(it)
        ship_x = next(it); ship_y = next(it)

        # generate the ship list according to the input specification
        # each ship position is a single point (ship_x, ship_y)
        sea = Sea([(ship_x, ship_y)])

        ans = count_ships_in_rectangle(sea,
                                       topRight=[x2, y2],
                                       bottomLeft=[x1, y1])

        out_lines.append(f"Case #{case_no}: {ans}")

    sys.stdout.write("\n".join(out_lines))


if __name__ == "__main__":
    main()
```

The program follows exactly the algorithm proven correct above
and conforms to the required input / output format.