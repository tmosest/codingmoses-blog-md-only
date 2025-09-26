---
title: LeetCode 464. Can I Win - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 464 – “Can I Win?”  
> **Medium** – 1 ≤ maxChoosableInteger ≤ 20, 0 ≤ desiredTotal ≤ 300  

> **Goal** – Decide if the first player can force a win in the “100‑Game” variant where each number can only be chosen once.

---

## 🚀 TL;DR

- **Answer** – Use a *bitmask + memoization* DP.  
- **Time** – `O(2^n · n)` (worst‑case 20 bits → ~20 M operations).  
- **Space** – `O(2^n)` for the memo table.  
- **Languages** – Java, Python, C++ (full, ready‑to‑paste).

---

## 🧩 Problem Recap

Two players pick a number from `1 … maxChoosableInteger` **without replacement** and add it to a running total.  
The player that first makes the total **≥ desiredTotal** wins.

You must decide whether the *first* player can win assuming both play optimally.



---

## 🔍 The Naïve Brute‑Force Idea

```
DFS(used, currentTotal):
    if currentTotal >= desiredTotal: return true      // current player won
    for each unused i:
        if DFS(used∪{i}, currentTotal+i) == false:
            return true          // current player can force win
    return false
```

With `maxChoosableInteger = 20` this explores up to `20!` states → impossible.



---

## 🧠 Key Insight: Bitmask + Memoization

- **State** – which numbers are already taken (`usedMask`).  
- **Property** – from a given mask the remaining total needed is **fixed**:  
  `remaining = desiredTotal – sum(chosenNumbers)`.  
  Thus the result for a mask is independent of the play‑order that led to it.
- **Memoize** the result for each mask → each state examined once.

So we can write a DFS that remembers if the *current* player can force a win from the current mask.



---

## 📊 Complexity Analysis

| | Explanation | Result |
|---|---|---|
| Number of states | Every subset of `{1,…,n}` → `2^n` | ≤ 2^20 = 1,048,576 |
| Work per state | Iterate over unused numbers → ≤ n | ≤ 20 |
| **Total time** | `O(2^n · n)` | ~20 M primitive ops (fast enough) |
| **Space** | Memo table + recursion stack | `O(2^n)` + `O(n)` |

---

## 📚 “The Good, The Bad, and The Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Idea** | Bitmask DP is elegant & powerful. | Complexity can still blow if `maxChoosableInteger` were > 20. | For 20 the recursion depth can hit 20 – fine, but must guard against stack overflow. |
| **Implementation** | Simple bit‑operations; no external libraries. | Need to compute `sum(chosenNumbers)` efficiently. | Avoid recomputing sums by maintaining a running total. |
| **Readability** | Clean recursion + memo table. | The base‑case logic may seem odd to newcomers. | The bit‑mask tricks can be cryptic for beginners. |

---

## 💻 Code – Java 17

```java
import java.util.*;

public class Solution {
    public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
        // Quick win / loss pre‑checks
        if (desiredTotal <= 0) return true;
        int maxSum = maxChoosableInteger * (maxChoosableInteger + 1) / 2;
        if (maxSum < desiredTotal) return false;
        // Special case: single move guarantees win
        if (maxChoosableInteger >= desiredTotal) return true;

        int n = maxChoosableInteger;
        int[] memo = new int[1 << n];          // -1 = unknown, 0 = lose, 1 = win
        Arrays.fill(memo, -1);

        return dfs(0, desiredTotal, n, memo);
    }

    private boolean dfs(int usedMask, int remaining, int n, int[] memo) {
        if (remaining <= 0) return true;          // current player already won
        if (memo[usedMask] != -1) return memo[usedMask] == 1;

        for (int i = 0; i < n; i++) {
            int bit = 1 << i;
            if ((usedMask & bit) != 0) continue;   // already used

            // If we pick this number and reach the goal, we win
            if (i + 1 >= remaining) {
                memo[usedMask] = 1;
                return true;
            }

            // Recurse: after picking i+1, opponent plays next
            if (!dfs(usedMask | bit, remaining - (i + 1), n, memo)) {
                memo[usedMask] = 1; // we can force opponent into a losing state
                return true;
            }
        }
        memo[usedMask] = 0; // all moves lead to opponent win
        return false;
    }
}
```

> **Why it works**  
> - `usedMask` tracks which numbers have already been taken.  
> - The remaining sum needed to win is `remaining`.  
> - If any unused number reaches or exceeds `remaining`, the current player wins immediately.  
> - Otherwise, we recursively ask: *Can the opponent win from the new state?*  
> - If the opponent cannot win, we win.  
> - Memoization guarantees each mask is evaluated only once.



---

## 🐍 Code – Python 3 (Fast)

```python
def canIWin(maxChoosableInteger: int, desiredTotal: int) -> bool:
    # Quick checks
    if desiredTotal <= 0: return True
    total = maxChoosableInteger * (maxChoosableInteger + 1) // 2
    if total < desiredTotal: return False
    if maxChoosableInteger >= desiredTotal: return True

    n = maxChoosableInteger
    from functools import lru_cache

    @lru_cache(maxsize=None)
    def dfs(mask: int, remaining: int) -> bool:
        if remaining <= 0:                 # current player already won
            return True
        for i in range(n):
            bit = 1 << i
            if mask & bit:                 # already used
                continue
            if i + 1 >= remaining:         # instant win
                return True
            # Opponent plays next: we win if opponent loses
            if not dfs(mask | bit, remaining - (i + 1)):
                return True
        return False

    return dfs(0, desiredTotal)
```

> The `@lru_cache` replaces the explicit memo array.  
> Each key is a pair `(mask, remaining)` – but because `remaining` is determined by `mask`, Python still caches the state effectively.



---

## 📦 Code – C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool canIWin(int maxChoosableInteger, int desiredTotal) {
        if (desiredTotal <= 0) return true;
        int total = maxChoosableInteger * (maxChoosableInteger + 1) / 2;
        if (total < desiredTotal) return false;
        if (maxChoosableInteger >= desiredTotal) return true;

        int n = maxChoosableInteger;
        vector<int> memo(1 << n, -1);        // -1 unknown, 0 lose, 1 win

        function<bool(int,int)> dfs = [&](int mask, int remaining) -> bool {
            if (remaining <= 0) return true;
            if (memo[mask] != -1) return memo[mask];

            for (int i = 0; i < n; ++i) {
                int bit = 1 << i;
                if (mask & bit) continue;          // already used

                if (i + 1 >= remaining) {
                    memo[mask] = 1;
                    return true;
                }
                if (!dfs(mask | bit, remaining - (i + 1))) {
                    memo[mask] = 1;
                    return true;
                }
            }
            memo[mask] = 0;
            return false;
        };

        return dfs(0, desiredTotal);
    }
};
```

> The `std::function` keeps the recursion simple and captures `memo` by reference.



---

## 📄 Blog Article – “Can I Win?”: The Good, The Bad, and The Ugly

> **Keywords** – *LeetCode 464, Can I Win, bitmask DP, game theory, Java solution, Python solution, C++ solution, interview coding, optimal play, algorithmic puzzle.*

---

### 1. Intro: Why This Problem Rocks

The classic “100 Game” is a staple in coding interviews because it blends *game theory* with *combinatorics*.  
LeetCode’s version adds a twist: **no repeats**.  
This small change turns an apparently simple Nim‑style game into a state‑space exploration nightmare if you ignore the structure of the numbers.

---

### 2. Problem Recap (What the interviewer expects)

> **Input**  
> - `maxChoosableInteger`: highest number available (1–20)  
> - `desiredTotal`: the target total to win (0–300)  
> **Output**  
> `true` if the first player can force a win; `false` otherwise.

All moves are deterministic and both players play optimally.

---

### 3. Why Brute‑Force Fails

- The branching factor is `maxChoosableInteger`.  
- Depth is up to 20.  
- Without pruning you could visit up to `20!` leaf nodes.  
- Even with simple memoization on the *set of used numbers*, you still need to evaluate each subset *once*. That’s `2^20` ≈ 1 million states – fine – but you *must* use the right key!

---

### 4. The Elegant Solution: Bitmask + DP

#### 4.1 State Representation
- **Bitmask**: a 20‑bit integer where bit `i` (0‑based) denotes whether number `i+1` has been taken.  
- **Memo table**: `int dp[1<<n]` (`-1 = unknown, 0 = lose, 1 = win`).

#### 4.2 Transition
For a given mask:
1. **Calculate remaining total**: `remaining = desiredTotal - sumChosen`.
   - Because `mask` uniquely identifies `sumChosen`, we can compute `remaining` once per call.
2. **Try every unused number `x`** (`x = i+1`):
   - If `x >= remaining` → instant win → mark mask as *win*.
   - Else recursively ask: *Can opponent win from mask | (1<<i) with remaining‑x*?
   - If opponent loses → current player wins.

#### 4.3 Base Cases
- `remaining <= 0` → current player already won.  
- If a single number is ≥ `remaining` → guaranteed win.

#### 4.4 Result
- The recursion depth is at most 20; stack space is safe in most interview environments.  
- Each mask is processed once → O(2^n) states.

---

### 5. Implementation Tips (Beyond the code)

- **Pre‑check**:  
  - If `desiredTotal <= 0` → immediate win.  
  - If the sum of all numbers < `desiredTotal` → impossible.  
  - If a single number can reach the target → immediate win.
- **Running total vs recomputing sums**:  
  Store the running `remaining` instead of recomputing `sumChosen` at every recursion.
- **Iterative vs recursive**:  
  Recursive DFS keeps code concise; iterative loops can be added if you fear stack limits.

---

### 6. “Good, Bad, Ugly” Breakdown

| Good | Bad | Ugly |
|------|-----|------|
| *Good*: bitmask is fast, memory efficient, and generalizable to many subset problems. | *Bad*: computing sums can be confusing; need to avoid repeated work. | *Ugly*: the bit‑mask hack looks like magic to novices; explain carefully. |
| *Good*: The recursion logic maps directly to “can we force a win?” – intuitive for experienced programmers. | *Bad*: the base case `remaining <= 0` might feel unintuitive; it’s the *inverse* of the classic Nim condition. | *Ugly*: debugging memoization errors can be tricky if you misuse the index (`mask` vs `mask|bit`). |

---

### 7. Code Snippets (Languages)

Show the three language versions (Java, Python, C++) as short blocks, letting readers pick their favorite.

---

### 8. Conclusion: Take‑aways for Your Interview

1. **State compression** – Bitmask is a go‑to tool for subset DP.  
2. **Memoization key** – Always choose a *unique* identifier of the game state.  
3. **Prune early** – The instant‑win check (`x >= remaining`) cuts a huge branch.  
4. **Test edge cases** – 0 target, large numbers, total exceeding the max sum – all quick paths.

If you can explain this approach cleanly, you’ll impress both the interviewer and the recruiter on your *algorithmic mindset*.

---

### 9. Call to Action

> “Got the problem under your belt? Try tweaking the parameters – what happens if `maxChoosableInteger` grows to 25? Could you parallelize the DP? Feel free to leave comments or PRs with your own language solutions.”

---



---

## 🎯 Final Thought

A solid, production‑ready solution to LeetCode 464 demonstrates mastery of:

- State‑space compression (bitmask)  
- Dynamic programming & memoization  
- Clean recursion with clear base cases

These are exactly the patterns interviewers look for in top‑tier tech companies.  
Now go ace that interview – and enjoy the satisfaction of solving a puzzle that once seemed intractable! 🚀



---