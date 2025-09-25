---
title: LeetCode 3609. Minimum Moves to Reach Target in Grid - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 3609 – Minimum Moves to Reach Target in Grid  
*LeetCode – Hard* | **Java | Python | C++** | **Interview‑Ready Solution**  

---

### TL;DR  
*Reversing the moves* is the key.  
Starting from the target `(tx, ty)`, repeatedly **subtract** the smaller coordinate from the larger one.  
When the larger coordinate is *at least twice* the smaller one, it must have been *doubled* in the forward direction – so we **divide by 2**.  
If a division would produce a fraction, the target is impossible.  
When we finally hit the start point `(sx, sy)` we have the answer.  
Complexity: **O(log max(tx, ty))** time, **O(1)** space.

---

## 1️⃣ Problem Statement

> Given four integers `sx, sy, tx, ty` (with `0 ≤ sx ≤ tx ≤ 10⁹` and `0 ≤ sy ≤ ty ≤ 10⁹`), you start at `(sx, sy)` on an infinite grid.  
> At any point `(x, y)` let `m = max(x, y)`. You can **add `m`** to either coordinate:
>  * `(x + m, y)` or  
>  * `(x, y + m)`
>  
> Find the **minimum** number of moves required to reach `(tx, ty)`.  
> Return `-1` if it is impossible.

Examples  
| sx | sy | tx | ty | Output | Explanation |
|---|---|---|---|---|---|
| 1 | 2 | 5 | 4 | 2 | `+2` on y → `(1,4)` → `+4` on x → `(5,4)` |
| 0 | 1 | 2 | 3 | 3 | `+1` on x → `(1,1)` → `+1` on x → `(2,1)` → `+2` on y → `(2,3)` |
| 1 | 1 | 2 | 2 | -1 | Impossible, see notes |

---

## 2️⃣ Intuition

A naïve breadth‑first search would blow up (the grid is infinite).  
Instead, think *backwards*.  

If we are at `(x, y)` and we know the last move that produced it, we can deduce what the previous position was:

| last move | previous | condition |
|-----------|----------|-----------|
| `x += max(prev)` | `x` was the larger coordinate, so it was **doubled** → `prev = x / 2` |
| `y += max(prev)` | `y` was the larger coordinate, so it was **doubled** → `prev = y / 2` |
| `x += max(prev)` | `x` was the smaller coordinate, so it was **added** → `prev = x - y` |
| `y += max(prev)` | `y` was the smaller coordinate, so it was **added** → `prev = y - x` |

The reverse operation is always *deterministic* once we know which case holds.

**Why division by 2?**  
If the larger coordinate is at least twice the smaller one, the only way it could have been increased in a single step is by adding itself (doubling).  
Otherwise the increase came from the smaller coordinate, so we subtract.

---

## 3️⃣ Algorithm (Reversed BFS)

```
moves = 0
while tx ≥ sx and ty ≥ sy:
    if tx == sx and ty == sy: return moves
    if tx > ty:
        if tx ≥ 2 * ty:
            if tx is odd: return -1
            tx //= 2
        else:
            tx -= ty
    else if ty > tx:
        if ty ≥ 2 * tx:
            if ty is odd: return -1
            ty //= 2
        else:
            ty -= tx
    else:   # tx == ty
        # cannot reduce further unless one of the start coordinates is 0
        if sx == tx and sy == ty: return moves
        else: return -1
    moves += 1
return -1
```

*Why the `>=` checks?*  
If either coordinate of the target becomes smaller than the corresponding start coordinate, we can never reach the target from the start because moves only increase coordinates.

---

## 4️⃣ Edge‑Case Checklist

| Scenario | Result |
|----------|--------|
| `(sx, sy) == (tx, ty)` | `0` |
| `sx == 0 && sy == 0` and target not `(0,0)` | `-1` (cannot create a non‑zero coordinate when both are zero) |
| Larger coordinate odd when we need to divide by 2 | `-1` |
| `tx == ty` but not equal to `(sx, sy)` | `-1` (can't reduce an equal pair unless one start coordinate is 0) |
| Any coordinate becomes < start coordinate | `-1` |

---

## 5️⃣ Implementations

Below are clean, production‑ready implementations for **Java**, **Python**, and **C++**.  
All run in `O(log max(tx, ty))` time and use only constant extra space.

### 5.1 Java

```java
class Solution {
    public int minMoves(int sx, int sy, int tx, int ty) {
        if (sx == tx && sy == ty) return 0;
        if (sx > tx || sy > ty) return -1;

        int moves = 0;
        while (tx >= sx && ty >= sy) {
            if (tx == sx && ty == sy) return moves;

            if (tx > ty) {
                if (tx >= 2 * ty) {
                    if ((tx & 1) == 1) return -1;  // odd
                    tx >>= 1;                     // divide by 2
                } else {
                    tx -= ty;                      // subtract smaller
                }
            } else if (ty > tx) {
                if (ty >= 2 * tx) {
                    if ((ty & 1) == 1) return -1;
                    ty >>= 1;
                } else {
                    ty -= tx;
                }
            } else { // tx == ty
                return -1; // equal pair cannot be reduced
            }
            moves++;
        }
        return -1;
    }
}
```

### 5.2 Python

```python
class Solution:
    def minMoves(self, sx: int, sy: int, tx: int, ty: int) -> int:
        if sx == tx and sy == ty:
            return 0
        if sx > tx or sy > ty:
            return -1

        moves = 0
        while tx >= sx and ty >= sy:
            if tx == sx and ty == sy:
                return moves

            if tx > ty:
                if tx >= 2 * ty:
                    if tx % 2:   # odd
                        return -1
                    tx //= 2
                else:
                    tx -= ty
            elif ty > tx:
                if ty >= 2 * tx:
                    if ty % 2:
                        return -1
                    ty //= 2
                else:
                    ty -= tx
            else:  # tx == ty
                return -1
            moves += 1
        return -1
```

### 5.3 C++

```cpp
class Solution {
public:
    int minMoves(int sx, int sy, int tx, int ty) {
        if (sx == tx && sy == ty) return 0;
        if (sx > tx || sy > ty) return -1;

        int moves = 0;
        while (tx >= sx && ty >= sy) {
            if (tx == sx && ty == sy) return moves;

            if (tx > ty) {
                if (tx >= 2 * ty) {
                    if (tx & 1) return -1;   // odd
                    tx >>= 1;                // divide by 2
                } else {
                    tx -= ty;
                }
            } else if (ty > tx) {
                if (ty >= 2 * tx) {
                    if (ty & 1) return -1;
                    ty >>= 1;
                } else {
                    ty -= tx;
                }
            } else { // tx == ty
                return -1;
            }
            ++moves;
        }
        return -1;
    }
};
```

---

## 6️⃣ Complexity Analysis

| Metric | Value |
|--------|-------|
| **Time** | `O(log max(tx, ty))` – each loop cuts the larger coordinate in half or subtracts it, both logarithmic reductions |
| **Space** | `O(1)` – only a handful of integer variables |

For the worst case `(0,0) → (10⁹,10⁹)`, the loop runs at most ~30 times.

---

## 7️⃣ Good / Bad / Ugly

| Category | What to Emphasise / Avoid |
|----------|--------------------------|
| **Good** | • Reverse deterministic logic eliminates the need for BFS.  <br>• Works for 10⁹ bounds without overflow (use 64‑bit integers if you want extra safety). |
| **Bad** | • Forgetting the `>= 2 * min` condition can let the algorithm choose the wrong operation, returning an incorrect answer.  <br>• Ignoring the “odd when dividing” check yields wrong results for targets like `(3,4)`. |
| **Ugly** | • Implementing a forward BFS or DFS is a recipe for time‑outs and memory errors.  <br>• Using floating‑point division in the reverse step (e.g., `tx / 2.0`) introduces rounding errors; stay with integer arithmetic. |

---

## 8️⃣ FAQ

| Question | Answer |
|----------|--------|
| *Why is division always safe when `larger ≥ 2 * smaller`?* | The only way the larger coordinate could have grown to its current value in one forward step is by adding itself (doubling). If it were the smaller coordinate, the growth would have been less than twice the smaller value. |
| *What if both coordinates are equal at some point?* | The only way to reach an equal pair from the start is if one of the start coordinates was zero. Otherwise we can’t reduce the pair further, so the path is impossible. |
| *Can I still use BFS on this problem?* | You can, but you’d need to store the visited set up to 10⁹ steps – infeasible. The reverse algorithm is the interview‑friendly approach. |
| *Will this algorithm work for negative coordinates?* | The problem guarantees non‑negative coordinates, but if you want a generic solution you’d need to handle signs carefully. |

---

## 9️⃣ Conclusion

*Reversing the move* transforms an infinite‑grid problem into a logarithmic‑time greedy algorithm.  
The core idea—**“divide when you see a big jump; otherwise subtract”**—works in all three languages and is simple enough to explain in 5 minutes during an interview.

Use this solution as a **code‑review staple** or drop it into your personal LeetCode library.  

---

### 📌 SEO Tags & Keywords

- Minimum Moves to Reach Target in Grid  
- LeetCode 3609 – Hard  
- Grid movement algorithm  
- Reverse BFS solution  
- Java, Python, C++ implementations  
- Interview prep, coding interview, data structures  

---

Happy coding, and may your future interviewers be dazzled by your **reverse thinking**! 🎉