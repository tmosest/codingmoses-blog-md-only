---
title: LeetCode 464. Can I Win - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 464 – **Can I Win?**  
*From LeetCode’s “100 game” to a full‑blown strategy puzzle*

> **Short version:**  
> Given a maximum choosable integer `n` (1 … 20) and a `desiredTotal` (0 … 300), can the first player force a win if each number may be used **once**?  
> The game ends when the running total reaches or exceeds the desired total. Both players play optimally.

---

## Why this problem matters for your job hunt

* **Dynamic programming + bitmasking** – a classic interview combo that tests recursion, memoisation and space optimisation.  
* **Problem‑solving mindset** – you must recognise trivial win/loss cases, prune impossible branches, and craft a clean recursive structure.  
* **Time & space trade‑offs** – understanding that 2¹⁰ is 1 024 and 2²⁰ is about one million is a quick mental check that a bitmask solution will run in time.  

If you can explain this solution in an interview, you’ll demonstrate mastery of recursion, optimisation, and code clarity – all of which recruiters love.

---

## The challenge

You have two integers:

| Variable | Meaning |
| -------- | ------- |
| `maxChoosableInteger` | The largest number you can pick (the set is {1,…,n}). |
| `desiredTotal` | The total you must reach or exceed to win. |

**Rules**

1. Players alternate turns.  
2. On a turn you pick a *unused* number from the pool and add it to the running total.  
3. The first player whose pick brings the total to `≥ desiredTotal` wins.  
4. If all numbers are used and the total never reaches the goal, the game is a draw (but this never happens under the constraints).

**Goal** – return `true` if the first player can guarantee a win.

---

## 1.  The “good” – A clean, optimal solution

We’ll use **recursive back‑tracking** with **memoisation**.  
Because `maxChoosableInteger ≤ 20`, we can represent the set of used numbers with a 20‑bit integer (`mask`). Each bit `i` (`0‑based`) indicates whether number `i+1` has been chosen.

### Why memoisation matters

Without caching, the same state (`mask`, `currentTotal`) is recomputed many times – the recursion tree explodes exponentially.  
Caching reduces the problem to at most `2ⁿ` states.

### Early‑exit pruning

| Situation | Result | Why it’s safe |
|-----------|--------|---------------|
| `desiredTotal <= 0` | `true` | The first player wins immediately (no move needed). |
| `sum(1..n) < desiredTotal` | `false` | Even if all numbers are used, the total is insufficient. |
| The current player picks a number `k` that makes the total ≥ `desiredTotal` | `true` | He wins instantly. |

These simple checks cut the search space dramatically.

### Recursive idea

```text
CanPlayerWin(mask, total):
    if mask is cached: return cached result

    for each unused number k:
        if total + k >= desiredTotal:
            cache true, return true          // win this turn
        if CanPlayerWin(mask | (1<<k), total + k) == false:
            // opponent loses from this state → current player wins
            cache true, return true

    cache false, return false                // all moves lead to opponent win
```

The function returns *true* if the current player can force a win.

---

## 2.  The “bad” – A common pitfall

**Using an array of booleans for memoisation without an “unknown” state**  
If you initialise the DP array with `false`, you’ll treat “not yet computed” as a real “losing state”, causing the algorithm to return incorrect answers.  

Solution: use a tri‑state (unknown, win, lose) or an `Integer` array where `null` represents “unknown”.

---

## 3.  The “ugly” – Over‑engineering

- **Dynamic programming with a huge 2D table** (mask × total).  
  With `desiredTotal` up to 300, this becomes a 1 024 × 301 ≈ 300 k cells array – still okay, but unnecessary because the recursion only needs the current total to be compared against the goal; the exact value is irrelevant beyond that comparison.  

- **Bitwise hacking for performance** (e.g., manually counting set bits, pre‑computing all masks).  
  It makes the code unreadable and offers negligible runtime benefit for `n ≤ 20`.

Stick to the simple recursive memoised solution – it’s fast, clear, and passes all tests.

---

## 4.  Code – one implementation per language

Below you’ll find the same algorithm in **Java**, **Python**, and **C++**.  
All use a single integer `mask` and an array / dict / vector for memoisation.

---

### Java 17

```java
import java.util.Arrays;

public class CanIWin {
    private int maxChoosable;
    private int desiredTotal;
    private int[] memo;          // -1 = unknown, 0 = lose, 1 = win

    public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
        this.maxChoosable = maxChoosableInteger;
        this.desiredTotal = desiredTotal;

        // Trivial cases
        if (desiredTotal <= 0) return true;
        int maxSum = maxChoosable * (maxChoosable + 1) / 2;
        if (maxSum < desiredTotal) return false;

        memo = new int[1 << maxChoosable];
        Arrays.fill(memo, -1);

        return canWin(0, 0);
    }

    private boolean canWin(int mask, int total) {
        if (memo[mask] != -1) return memo[mask] == 1;

        for (int i = 0; i < maxChoosable; i++) {
            int bit = 1 << i;
            if ((mask & bit) != 0) continue;          // already used

            int val = i + 1;                           // numbers are 1‑based
            if (total + val >= desiredTotal) {
                memo[mask] = 1;                       // win immediately
                return true;
            }

            // If opponent cannot win after this move → we win
            if (!canWin(mask | bit, total + val)) {
                memo[mask] = 1;
                return true;
            }
        }

        memo[mask] = 0; // lose
        return false;
    }

    public static void main(String[] args) {
        CanIWin solver = new CanIWin();
        System.out.println(solver.canIWin(10, 11)); // false
        System.out.println(solver.canIWin(10, 0));  // true
        System.out.println(solver.canIWin(10, 1));  // true
    }
}
```

---

### Python 3.10+

```python
from functools import lru_cache
from typing import Tuple

class Solution:
    def canIWin(self, maxChoosableInteger: int, desiredTotal: int) -> bool:
        if desiredTotal <= 0:
            return True

        max_sum = maxChoosableInteger * (maxChoosableInteger + 1) // 2
        if max_sum < desiredTotal:
            return False

        @lru_cache(maxsize=None)
        def dfs(mask: int, total: int) -> bool:
            # iterate over all unused numbers
            for i in range(maxChoosableInteger):
                bit = 1 << i
                if mask & bit:
                    continue

                val = i + 1
                if total + val >= desiredTotal:
                    return True

                # if opponent cannot win after our move → we win
                if not dfs(mask | bit, total + val):
                    return True
            return False

        return dfs(0, 0)


# quick tests
if __name__ == "__main__":
    sol = Solution()
    print(sol.canIWin(10, 11))  # False
    print(sol.canIWin(10, 0))   # True
    print(sol.canIWin(10, 1))   # True
```

---

### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool canIWin(int maxChoosableInteger, int desiredTotal) {
        if (desiredTotal <= 0) return true;

        int maxSum = maxChoosableInteger * (maxChoosableInteger + 1) / 2;
        if (maxSum < desiredTotal) return false;

        vector<int> memo(1 << maxChoosableInteger, -1); // -1 unknown, 0 lose, 1 win
        function<bool(int, int)> dfs = [&](int mask, int total) -> bool {
            if (memo[mask] != -1) return memo[mask] == 1;

            for (int i = 0; i < maxChoosableInteger; ++i) {
                int bit = 1 << i;
                if (mask & bit) continue; // already used

                int val = i + 1;
                if (total + val >= desiredTotal) {
                    memo[mask] = 1;
                    return true;
                }
                if (!dfs(mask | bit, total + val)) {
                    memo[mask] = 1;
                    return true;
                }
            }
            memo[mask] = 0;
            return false;
        };

        return dfs(0, 0);
    }
};

int main() {
    Solution s;
    cout << boolalpha;
    cout << s.canIWin(10, 11) << endl; // false
    cout << s.canIWin(10, 0)  << endl; // true
    cout << s.canIWin(10, 1)  << endl; // true
}
```

---

## 5.  Complexity analysis

| Operation | Time | Space |
|-----------|------|-------|
| Recursive DP | **O(2ⁿ × n)** in the worst case (each state explores up to `n` moves). With `n ≤ 20` this is ≈ 20 million operations – easily fast enough. |
| Memoisation array | **O(2ⁿ)** for storage of the `mask` states. For `n = 20` it’s just 1 048 576 integers (~4 MB). |
| Additional pruning checks | **O(1)**. |

---

## 6.  How to present this solution in an interview

1. **Explain the game rules and constraints.**  
   Mention the pool is *without replacement* and `n ≤ 20` allows a bitmask.
2. **Show the trivial cases first** (desiredTotal ≤ 0, total sum < desiredTotal).  
   This demonstrates you’re looking for corner cases.
3. **Describe the recursion** – choose a number, update total, flip turn.  
   Emphasise that if *any* move forces the opponent into a losing state, the current player wins.
4. **Introduce memoisation** – map each `mask` to the result.  
   Clarify that we don’t need to store the running total because we only compare it to `desiredTotal` on the fly.
5. **Give the complexity** and why it’s acceptable for the limits.
6. **Write clean code** (or sketch it) – readability beats micro‑optimisation.

---

## 7.  Final take‑aways

| Good | Bad | Ugly |
|------|-----|------|
| **Good** – Clear recursion + memoisation, early‑exit pruning, uses a 20‑bit mask. | **Bad** – Failing to differentiate *unknown* from *losing* state in the DP array. | **Ugly** – Over‑engineering with huge tables or unnecessary bitwise tricks. |

By mastering this problem you’ll showcase:

* **Algorithmic thinking** – mapping a game to a state space.  
* **Data structure choice** – using bitmasks to encode subsets.  
* **Optimisation** – pruning impossible branches.  

All of which are interview staples for roles that involve problem solving, coding, and systems design.

Happy coding, and good luck landing that job! 🚀