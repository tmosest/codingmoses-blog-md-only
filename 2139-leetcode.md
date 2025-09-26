---
title: LeetCode 2139. Minimum Moves to Reach Target Score - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Minimum Moves to Reach Target Score – A Complete Guide (Java + Python + C++)

> **SEO‑Optimized Title**: *LeetCode 2139 – Minimum Moves to Reach Target Score – Java, Python, C++ Solutions, Greedy Algorithm, Time Complexity, Interview Prep*

---

## 1️⃣ Problem Overview

LeetCode #2139 – **Minimum Moves to Reach Target Score** (Medium)

> You start at the integer `1`.  
> In one move you can:
> 1. `x = x + 1` (increment) – unlimited uses  
> 2. `x = 2 * x` (double) – at most `maxDoubles` times  
>  
> **Goal:** reach `target` with the minimum number of moves.

> **Constraints**  
> `1 ≤ target ≤ 10⁹`  
> `0 ≤ maxDoubles ≤ 100`

---

## 2️⃣ Intuition: Work Backwards

The most efficient strategy is to think *from the target back to 1*.

* Why?  
  * Doubling is *powerful* only when the current number is *small*.  
  * In reverse, “doubling” becomes “halving” (the most cost‑effective operation).  
  * You want to use a double as *late* as possible and as many times as you can.

**Greedy rule (reverse simulation)**  

| Condition | Action | Moves added |
|-----------|--------|-------------|
| `target` is **odd** | Decrement (`target–=1`) then halve (`target/=2`) | 2 |
| `target` is **even** | Halve (`target/=2`) | 1 |
| No `maxDoubles` left | Subtract `target‑1` (only increments remain) | `target‑1` |

The loop stops when `target` becomes `1` or we run out of double opportunities.

---

## 3️⃣ Algorithm

```text
moves = 0
while target > 1 and maxDoubles > 0:
    if target is odd:
        moves += 2      # decrement + halve
        target -= 1
    else:
        moves += 1      # only halve
    target //= 2
    maxDoubles -= 1

# remaining increments if target > 1
moves += target - 1
return moves
```

* **Time Complexity**: `O(log target)` – each loop halves `target`.  
* **Space Complexity**: `O(1)` – constant auxiliary space.

---

## 4️⃣ Why It Works (The “Good”)

* **Optimality** – In the reverse direction the halving operation is always the best you can do with a double.  
* **Simplicity** – The loop is short, no recursion, no DP table.  
* **Scalability** – Works for the largest `target` (`10⁹`) because it only iterates `≈30` times.

---

## 5️⃣ Potential Pitfalls (The “Bad”)

| Issue | Fix |
|-------|-----|
| Forgetting to decrement `maxDoubles` after a halve | `maxDoubles -= 1` in every loop iteration |
| Mixing up “increment” vs “decrement” in reverse simulation | Remember: a forward increment becomes a backward decrement |
| Edge case `maxDoubles == 0` | The loop will never run, you’ll jump straight to `target‑1` |

---

## 6️⃣ “Ugly” Code Anti‑Patterns

1. **Recursive DFS with memoization** – overkill, high memory.  
2. **Brute‑force BFS** – exponential state space (`10⁹` nodes).  
3. **Unnecessary big‑integer handling** – Python’s int is fine, but avoid 64‑bit overflow in C++ if you use `int` and shift negative values.

---

## 7️⃣ Reference Implementations

### 7.1 Java

```java
public class Solution {
    public int minMoves(int target, int maxDoubles) {
        int moves = 0;
        while (target > 1 && maxDoubles > 0) {
            if ((target & 1) == 1) {      // odd
                moves += 2;               // decrement + halve
                target -= 1;
            } else {                      // even
                moves += 1;               // only halve
            }
            target >>= 1;                 // divide by 2
            maxDoubles--;
        }
        return moves + (target - 1);      // remaining increments
    }
}
```

### 7.2 Python

```python
class Solution:
    def minMoves(self, target: int, maxDoubles: int) -> int:
        moves = 0
        while target > 1 and maxDoubles > 0:
            if target % 2:               # odd
                moves += 2               # decrement + halve
                target -= 1
            else:                        # even
                moves += 1
            target //= 2
            maxDoubles -= 1
        return moves + (target - 1)
```

### 7.3 C++

```cpp
class Solution {
public:
    int minMoves(int target, int maxDoubles) {
        int moves = 0;
        while (target > 1 && maxDoubles > 0) {
            if (target & 1) {            // odd
                moves += 2;              // decrement + halve
                target -= 1;
            } else {                     // even
                moves += 1;
            }
            target >>= 1;                // divide by 2
            --maxDoubles;
        }
        return moves + (target - 1);
    }
};
```

---

## 8️⃣ Complexity Table

| Language | Time | Space |
|----------|------|-------|
| Java | `O(log target)` | `O(1)` |
| Python | `O(log target)` | `O(1)` |
| C++ | `O(log target)` | `O(1)` |

---

## 9️⃣ FAQ

| Question | Answer |
|----------|--------|
| **Can I use the greedy approach when `maxDoubles` is large?** | Yes, because each loop iteration consumes one double, guaranteeing optimal use. |
| **What if `target` is already 1?** | The function returns `0` – no moves needed. |
| **Is `int` safe for C++?** | Yes, because `target ≤ 10⁹` and we never overflow the 32‑bit range during halving. |
| **What if `maxDoubles` is 0?** | We skip the loop and return `target‑1` (all increments). |

---

## 🔗 Bonus: Running the Code Locally

```bash
# Python
python3 - <<'PY'
from solution import Solution
print(Solution().minMoves(19, 2))   # 7
print(Solution().minMoves(5, 0))    # 4
PY
```

```bash
# Java
javac Solution.java
java Solution
```

```bash
# C++
g++ -std=c++17 solution.cpp -o sol
./sol
```

---

## 🏁 Conclusion

The **reverse greedy** approach gives an optimal, clean, and fast solution for LeetCode 2139.  
Its key take‑away for your job interviews:

1. **Think backwards** – often reduces the problem size drastically.  
2. **Greedy with a proof of optimality** – halving is always the best double‑use strategy.  
3. **Keep the code lean** – no unnecessary data structures, O(1) space.  

Share this post with your peers, and feel free to tweak the code to fit your coding style. Good luck crushing the interview!

--- 

> **SEO Tags:** LeetCode 2139, Minimum Moves to Reach Target Score, Java solution, Python solution, C++ solution, greedy algorithm, interview question, coding interview prep, algorithm analysis, job interview coding.