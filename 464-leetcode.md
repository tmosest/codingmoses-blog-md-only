---
title: LeetCode 464. Can I Win - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Leetcode 464 – *Can I Win* – 3‑Language Solution + SEO‑Optimized Blog

> **Goal:**  
> Determine whether the first player can force a win in the “Can I Win” game.  
>  
> **Why it matters:**  
> This problem blends game theory, memoization, and bit‑mask DP—perfect for demonstrating depth in a software‑engineering interview.  

---

### 📌 Problem Statement (Leetcode 464)

Two players take turns picking a **unique** integer from `1 … maxChoosableInteger`.  
The picked number is added to a running total.  
The first player to reach or exceed `desiredTotal` **wins**.  
Both players play optimally.  

**Return** `true` if the first player can force a win, otherwise `false`.

**Constraints**

| Variable | Range |
|----------|-------|
| `maxChoosableInteger` | 1 … 20 |
| `desiredTotal` | 0 … 300 |

---

## ✅ 1. Key Observations

| # | Observation | Why it matters |
|---|-------------|----------------|
| 1 | **Immediate win** – if any number ≥ `desiredTotal`, first player picks it and wins. | Shortcut for `O(1)` checks. |
| 2 | **Unreachable total** – sum of `1 … maxChoosableInteger` < `desiredTotal` → impossible for anyone to win. | Quick `false` return. |
| 3 | **State representation** – a bitmask can encode which numbers are still available (up to 20 bits). | Enables memoization over at most 2²⁰ ≈ 1 048 576 states. |
| 4 | **Recurrence** – current player wins if they can pick a number that forces the opponent into a losing state. | Classic minimax + DP. |

---

## 🧠 2. Algorithm Overview – Bitmask DP

1. **Base Cases** – handle the two shortcuts (points 1 & 2 above).  
2. **Memoization** – array/dictionary indexed by the bitmask; `-1` = uncomputed, `0` = lose, `1` = win.  
3. **DFS** –  
   * Iterate over all unused numbers (`bit = 1 << i`).  
   * If picking `i+1` ≥ `desiredTotal`, current player wins.  
   * Else, recursively call the function with the updated mask and reduced `desiredTotal - (i+1)`.  
   * If any recursive call returns `false` (opponent loses), set current state to `true`.  
4. **Return** the result for the initial empty mask.

---

## ⚙️ 3. Code Implementations

> *All three implementations follow the same logic, adapted to language idioms.*

### 3.1 Java (Java 17+)

```java
import java.util.Arrays;

public class CanIWin {
    private int maxChoosableInteger;
    private int[] memo; // -1: uncomputed, 0: lose, 1: win

    public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
        this.maxChoosableInteger = maxChoosableInteger;

        // Quick checks
        if (desiredTotal <= 0) return true;
        int maxSum = maxChoosableInteger * (maxChoosableInteger + 1) / 2;
        if (maxSum < desiredTotal) return false;
        if (maxChoosableInteger >= desiredTotal) return true;

        memo = new int[1 << maxChoosableInteger];
        Arrays.fill(memo, -1);
        return dfs(0, desiredTotal);
    }

    private boolean dfs(int mask, int remaining) {
        if (memo[mask] != -1) return memo[mask] == 1;

        for (int i = 0; i < maxChoosableInteger; i++) {
            int bit = 1 << i;
            if ((mask & bit) != 0) continue;          // already chosen

            if (i + 1 >= remaining) {                 // instant win
                memo[mask] = 1;
                return true;
            }

            if (!dfs(mask | bit, remaining - (i + 1))) { // opponent loses
                memo[mask] = 1;
                return true;
            }
        }

        memo[mask] = 0;
        return false;
    }
}
```

### 3.2 Python 3 (Python 3.9+)

```python
class Solution:
    def canIWin(self, maxChoosableInteger: int, desiredTotal: int) -> bool:
        # Quick pre‑checks
        if desiredTotal <= 0:
            return True
        max_sum = maxChoosableInteger * (maxChoosableInteger + 1) // 2
        if max_sum < desiredTotal:
            return False
        if maxChoosableInteger >= desiredTotal:
            return True

        memo = [-1] * (1 << maxChoosableInteger)

        def dfs(mask: int, remaining: int) -> bool:
            if memo[mask] != -1:
                return memo[mask] == 1

            for i in range(maxChoosableInteger):
                bit = 1 << i
                if mask & bit:
                    continue

                if i + 1 >= remaining:
                    memo[mask] = 1
                    return True

                if not dfs(mask | bit, remaining - (i + 1)):
                    memo[mask] = 1
                    return True

            memo[mask] = 0
            return False

        return dfs(0, desiredTotal)
```

> *If you prefer `functools.lru_cache`, it also works, but the explicit array is faster for 20 bits.*

### 3.3 C++17

```cpp
class Solution {
public:
    bool canIWin(int maxChoosableInteger, int desiredTotal) {
        if (desiredTotal <= 0) return true;
        int maxSum = maxChoosableInteger * (maxChoosableInteger + 1) / 2;
        if (maxSum < desiredTotal) return false;
        if (maxChoosableInteger >= desiredTotal) return true;

        vector<int> memo(1 << maxChoosableInteger, -1);
        return dfs(0, desiredTotal, maxChoosableInteger, memo);
    }

private:
    bool dfs(int mask, int remaining, int maxChoosableInteger, vector<int>& memo) {
        if (memo[mask] != -1) return memo[mask] == 1;

        for (int i = 0; i < maxChoosableInteger; ++i) {
            int bit = 1 << i;
            if (mask & bit) continue; // already used

            if (i + 1 >= remaining) {
                memo[mask] = 1;
                return true;
            }

            if (!dfs(mask | bit, remaining - (i + 1), maxChoosableInteger, memo)) {
                memo[mask] = 1;
                return true;
            }
        }

        memo[mask] = 0;
        return false;
    }
};
```

---

## 📊 4. Complexity Analysis

| Aspect | Complexity |
|--------|------------|
| **Time** | `O(2^n * n)` in the worst case (`n = maxChoosableInteger`). For `n ≤ 20`, ≈ 20 million operations – acceptable. |
| **Space** | `O(2^n)` for memoization array + recursion stack (`O(n)`). |

---

## 🎯 5. The Good, The Bad, & The Ugly

| Category | What Worked | Pain Points | Fixes & Tips |
|----------|-------------|-------------|--------------|
| **Good** | - Bitmask DP keeps state compact.<br>- Pre‑check optimizations prune most cases.<br>- Code is concise and language‑agnostic. | | |
| **Bad** | - Recursive DFS can hit recursion limits in Python for deep trees (use iterative or set recursion limit).<br>- For `n = 20`, memory ~ 8 MB – fine but watch out for larger `n`. | - Use iterative stack or `@lru_cache(maxsize=None)` in Python.<br>- For Java, use `int[]` not `Integer[]`. |
| **Ugly** | - Some solutions incorrectly assume `desiredTotal` > sum of all numbers => return `false` without checking that first move may win. <br>- Mixing `int` and `long` can cause overflow when computing `maxSum`. | - Always use `long` for intermediate sum if `maxChoosableInteger` is near 20: `long maxSum = 1L * n * (n + 1) / 2;` |

---

## 💡 6. Interview‑Ready Tips

1. **Explain the Game Theory** – describe it as a two‑player perfect‑information game.  
2. **Justify Bitmask** – talk about the 20‑bit limit and why DP is feasible.  
3. **Show Complexity** – be ready to explain both time and space bounds.  
4. **Mention Edge Cases** – e.g., `desiredTotal = 0`, `maxChoosableInteger >= desiredTotal`.  
5. **Code Readability** – use clear variable names (`mask`, `remaining`) and comments.  
6. **Testing** – demonstrate sample tests, plus edge cases: `maxChoosableInteger = 1, desiredTotal = 1`, `maxChoosableInteger = 20, desiredTotal = 300`.  

---

## 📚 7. Final Thoughts

- **Game theory + DP** are staple topics for senior software roles.  
- Demonstrating *why* the bitmask works, *how* memoization avoids exponential blow‑up, and *how* you handle edge cases shows depth.  
- Providing **multi‑language solutions** showcases versatility—great for companies with polyglot stacks.  

> Ready to nail that interview? Keep the code clean, the explanation tight, and show confidence that you’ve mastered **Leetcode 464 – Can I Win**. Good luck! 🚀

---

### 📑 SEO Keywords

- Leetcode 464 Can I Win
- Java solution Leetcode 464
- Python solution Leetcode 464
- C++ solution Leetcode 464
- Bitmask DP
- Game Theory Interview
- Optimal play
- Software engineer interview prep
- Dynamic programming interview
- Coding interview algorithms

---