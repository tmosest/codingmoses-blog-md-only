---
title: LeetCode 464. Can I Win - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 “Can I Win?” – LeetCode 464  
**Java | Python | C++** – Recursive Bit‑Mask DP + Memoization  
**Blog post** – The good, the bad, the ugly (SEO‑friendly, job‑search ready)

---

### 1️⃣ Problem Recap (LeetCode 464)

> Two players play a game where they **take turns choosing a distinct integer** from `1 … maxChoosableInteger`.  
> The chosen number is added to a running total.  
> The first player to make the total **≥ desiredTotal** wins.  
> Both play optimally.  
> Return `true` if the first player can force a win, otherwise `false`.

Constraints  
```
1 ≤ maxChoosableInteger ≤ 20
0 ≤ desiredTotal ≤ 300
```

---

## 2️⃣ Why Brute‑Force Fails

With `maxChoosableInteger = 20`, there are **2²⁰ ≈ 1 048 576** possible states – still huge if we try a naive recursion without memoization.  
Moreover, a simple DFS would recompute the same “remaining numbers + current total” combinations over and over.

---

## 3️⃣ Optimal Solution – Bitmask + Memoization

| Idea | Why it works |
|------|--------------|
| **Represent the set of already‑chosen numbers with a bitmask** (`int mask`). | Each bit `i` (0‑based) corresponds to whether number `i+1` is already taken. With 20 numbers, a 32‑bit integer suffices. |
| **Recursive win/lose check** | For a state `(mask, remainingTotal)`, try every unused number `k`. If choosing `k` reaches or exceeds `remainingTotal`, the current player wins immediately. Otherwise we recurse on the opponent’s turn: `!canWin(mask|bit, remainingTotal - k)`. If *any* move leads to a win, the current player can force a win. |
| **Memoization (hash map)** | Store the result for every `mask`. Since `remainingTotal` can be derived from the mask (sum of all chosen numbers), we only need to key the map by `mask`. |
| **Pruning** | 1) If `desiredTotal <= 0`, the first player already won. <br>2) If the sum of all numbers `< desiredTotal`, no one can ever win → return `false`. <br>3) If any single number `>= desiredTotal`, the first player can pick it and win instantly. |

### 3.1 Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Recursion + Memo | **O(2ⁿ · n)** (n = `maxChoosableInteger`) | **O(2ⁿ)** (for memo table) |

With `n ≤ 20`, this is fast enough (`≈ 20 million` operations worst‑case).

---

## 4️⃣ Implementation

Below are clean, language‑specific implementations that you can paste into LeetCode or run locally.

---

### 4.1 Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    private Map<Integer, Boolean> memo = new HashMap<>();
    private int maxChoosableInteger;
    private int desiredTotal;

    public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
        this.maxChoosableInteger = maxChoosableInteger;
        this.desiredTotal = desiredTotal;

        // Quick wins / losses
        if (desiredTotal <= 0) return true;
        int sumAll = (maxChoosableInteger * (maxChoosableInteger + 1)) / 2;
        if (sumAll < desiredTotal) return false;
        for (int i = maxChoosableInteger; i >= 1; i--) {
            if (i >= desiredTotal) return true;
        }

        return canWin(0, desiredTotal);
    }

    private boolean canWin(int mask, int remaining) {
        if (memo.containsKey(mask)) return memo.get(mask);

        // Try every unused number
        for (int num = 1; num <= maxChoosableInteger; num++) {
            int bit = 1 << (num - 1);
            if ((mask & bit) != 0) continue; // already taken

            // If we can take it and win immediately
            if (num >= remaining) {
                memo.put(mask, true);
                return true;
            }

            // Otherwise, let the opponent play. If opponent loses, we win.
            if (!canWin(mask | bit, remaining - num)) {
                memo.put(mask, true);
                return true;
            }
        }

        memo.put(mask, false);
        return false;
    }
}
```

---

### 4.2 Python

```python
class Solution:
    def canIWin(self, maxChoosableInteger: int, desiredTotal: int) -> bool:
        self.max = maxChoosableInteger
        self.total = desiredTotal
        self.memo = {}

        if desiredTotal <= 0:
            return True

        if sum(range(1, maxChoosableInteger + 1)) < desiredTotal:
            return False

        for i in range(maxChoosableInteger, 0, -1):
            if i >= desiredTotal:
                return True

        return self._dfs(0, desiredTotal)

    def _dfs(self, mask: int, remaining: int) -> bool:
        if mask in self.memo:
            return self.memo[mask]

        for num in range(1, self.max + 1):
            bit = 1 << (num - 1)
            if mask & bit:
                continue

            if num >= remaining:
                self.memo[mask] = True
                return True

            # If opponent cannot win after we choose 'num', we win
            if not self._dfs(mask | bit, remaining - num):
                self.memo[mask] = True
                return True

        self.memo[mask] = False
        return False
```

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    unordered_map<int, bool> memo;
    int maxChoosable, desiredTotal;

    bool canIWin(int maxChoosableInteger, int desiredTotal) {
        maxChoosable = maxChoosableInteger;
        desiredTotal = desiredTotal;

        if (desiredTotal <= 0) return true;

        int sumAll = (maxChoosableInteger * (maxChoosableInteger + 1)) / 2;
        if (sumAll < desiredTotal) return false;

        for (int i = maxChoosableInteger; i >= 1; --i)
            if (i >= desiredTotal) return true;

        return dfs(0, desiredTotal);
    }

private:
    bool dfs(int mask, int remaining) {
        if (memo.count(mask)) return memo[mask];

        for (int num = 1; num <= maxChoosable; ++num) {
            int bit = 1 << (num - 1);
            if (mask & bit) continue;           // already used

            if (num >= remaining) {
                memo[mask] = true;
                return true;
            }

            if (!dfs(mask | bit, remaining - num)) {
                memo[mask] = true;
                return true;
            }
        }
        memo[mask] = false;
        return false;
    }
};
```

---

## 5️⃣ Blog Post – “The Good, the Bad, and the Ugly”  
*(SEO‑Optimized, Interview‑Ready)*

> **Keywords**: LeetCode 464, Can I Win, bitmask DP, recursion, dynamic programming, Java, Python, C++, coding interview, algorithm design, job interview, software engineer

---

### 5.1 Introduction

The “Can I Win” problem is a classic **turn‑based game theory** challenge that tests your ability to think recursively and to optimize state space. It’s frequently asked in **software engineering interviews** (especially for Google, Microsoft, Amazon, etc.).  
In this article, we’ll walk through the **good** approach, the **bad** pitfalls you should avoid, and the **ugly** edge cases that can trip you up.

---

### 5.2 The Good – Clean Bitmask DP

1. **State Compression** – A bitmask of length `maxChoosableInteger` uniquely identifies which numbers are already chosen.  
2. **Recursive Win/Lose** – Try every unused number; if any leads to a forced win, return `true`.  
3. **Memoization** – Cache results for each mask to avoid exponential blow‑up.  
4. **Pruning** – Quick checks for impossible totals or immediate wins reduce recursion depth dramatically.

The final solution runs comfortably within LeetCode’s limits (20 MB memory, < 2 s time) and is readable in less than 70 lines per language.

---

### 5.3 The Bad – Over‑engineering & Common Mistakes

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| Using **HashSet** of remaining numbers instead of a bitmask | 2ⁿ × n memory, slower lookups | Replace with integer mask |
| Not memoizing results | TLE on larger inputs | Add `unordered_map<int, bool>` (C++), `Map<Integer, Boolean>` (Java), or dict (Python) |
| Forgetting to handle `desiredTotal <= 0` | Wrong answer on edge tests | Early return `true` |
| Not pruning impossible totals (`sum < desiredTotal`) | Extra recursive calls | Compute sum once and return `false` early |

---

### 5.4 The Ugly – Edge Cases & Debugging

1. **`desiredTotal` is 0** – First player automatically wins (empty move).  
2. **`maxChoosableInteger` is 1** – Game reduces to a single number.  
3. **`desiredTotal` equals the sum of all numbers** – Must pick all numbers to reach the total; first player wins iff parity of moves allows.  
4. **Large `desiredTotal` (e.g., 300)** – Sum of all numbers (≤ 210 for n=20) may be less than desiredTotal → impossible → return `false`.

**Debugging Tip**: Add a counter for memo hits vs. misses. If you see many misses, your mask generation or memo key might be wrong.

---

### 5.5 Interview Talk‑Track

When discussing this problem with an interviewer:

1. **Clarify assumptions** – Do numbers have to be unique? (Yes, per problem).  
2. **State your approach** – “I’ll use bitmask DP because `maxChoosableInteger` ≤ 20.”  
3. **Explain recursion** – “At each state, the current player picks a number; if that number >= remaining total, they win. Otherwise, we recursively ask the opponent if they can win.”  
4. **Mention pruning** – “If the total of all remaining numbers < desiredTotal, the game is unwinnable.”  
5. **Complexity** – “O(2ⁿ·n) time, O(2ⁿ) space.”  
6. **Edge cases** – “Handle `desiredTotal <= 0`, and early wins when any single number is large enough.”

---

### 5.6 Final Takeaway

> **Key Insight**: A combinatorial game with limited unique choices is a textbook problem for **bitmask DP**.  
> Mastering this pattern not only wins LeetCode contests but also demonstrates your grasp of recursion, memoization, and state compression—exact skills hiring managers love.

---

## 6️⃣ References & Further Reading

1. LeetCode Problem: <https://leetcode.com/problems/can-i-win/>
2. Bitmask DP tutorials:  
   - *GeeksforGeeks – Bitmask DP*  
   - *TopCoder – Bitmask DP*  
3. Interview prep: *Cracking the Coding Interview – Game Theory Problems*

Happy coding! 🚀