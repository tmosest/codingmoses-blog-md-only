---
title: LeetCode 464. Can I Win - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 LeetCode 464 – “Can I Win”  
**Problem** | **Difficulty** | **Tags**  
--- | --- | ---  
[LeetCode 464 – Can I Win](https://leetcode.com/problems/can-i-win/) | Medium | `Dynamic Programming`, `Bitmask`, `Game Theory`, `Backtracking`

---

### 🎯 Problem Recap

Two players take turns picking a number from `1 … maxChoosableInteger`.  
Numbers **cannot be reused** – once a number is chosen it’s gone.  
They add the chosen number to a running total.  
The player who first makes the total **≥ desiredTotal** wins.

Return `true` if the first player can force a win, otherwise `false`.  
Both players play optimally.

**Constraints**  

```
1 ≤ maxChoosableInteger ≤ 20
0 ≤ desiredTotal ≤ 300
```

Because `maxChoosableInteger ≤ 20` we can encode the state of “which numbers are still available” with a 20‑bit mask – perfect for a DP‑memoization solution.

---

## 🔍 High‑Level Idea

1. **Early exit**  
   * If `desiredTotal <= 0` → first player wins immediately.  
   * If the sum of all numbers < `desiredTotal` → impossible to reach the target → return `false`.

2. **Recursive Backtracking + Memoization**  
   * Represent the set of available numbers with a bitmask `mask`.  
   * For each number `i` that is still free (bit `i` is 0):  
     * If picking `i` wins immediately (`i >= remainingTotal`), return `true`.  
     * Otherwise, simulate picking `i`, subtract it from the remaining total, and **recursively** ask if the *next* player can win with the new mask.  
     * If the *next* player **cannot** win, the current player can win – return `true`.  
   * If no choice leads to a win, memoize and return `false`.

3. **Complexity**  
   * **State space**: at most `2^maxChoosableInteger` masks (`≤ 1,048,576`).  
   * **Time**: `O(2^n * n)` in the worst case (each mask explores all remaining numbers).  
   * **Space**: `O(2^n)` for the memo table + recursion stack (`≤ 20` depth).

---

## 📄 Code Implementations

Below are clean, self‑documenting solutions in **Java**, **Python**, and **C++**.  
Each uses the same recursive + memoization pattern.

---

### Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    // Memoization map: mask -> canFirstPlayerWin
    private Map<Integer, Boolean> memo = new HashMap<>();

    public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
        // Quick sanity checks
        if (desiredTotal <= 0) return true;
        int maxSum = (maxChoosableInteger * (maxChoosableInteger + 1)) / 2;
        if (maxSum < desiredTotal) return false;

        return canWin(0, desiredTotal, maxChoosableInteger);
    }

    /**
     * @param mask          Bitmask of numbers already chosen.
     * @param remaining     How much still needed to reach desiredTotal.
     * @param maxChoosable  The maximum integer that can be chosen.
     * @return true if the current player can force a win.
     */
    private boolean canWin(int mask, int remaining, int maxChoosable) {
        // Memo lookup
        if (memo.containsKey(mask)) return memo.get(mask);

        for (int num = 1; num <= maxChoosable; num++) {
            int bit = 1 << (num - 1);
            if ((mask & bit) != 0) continue;          // already used

            // If we can win immediately by picking this number
            if (num >= remaining) {
                memo.put(mask, true);
                return true;
            }

            // Try picking this number and let opponent play
            int newMask = mask | bit;
            boolean opponentWins = canWin(newMask, remaining - num, maxChoosable);

            // If opponent loses, current player wins
            if (!opponentWins) {
                memo.put(mask, true);
                return true;
            }
        }

        // No winning move found
        memo.put(mask, false);
        return false;
    }
}
```

---

### Python

```python
class Solution:
    def __init__(self):
        self.memo = {}

    def canIWin(self, maxChoosableInteger: int, desiredTotal: int) -> bool:
        # Edge cases
        if desiredTotal <= 0:
            return True
        if (maxChoosableInteger * (maxChoosableInteger + 1)) // 2 < desiredTotal:
            return False

        return self._dfs(0, desiredTotal, maxChoosableInteger)

    def _dfs(self, mask: int, remaining: int, max_num: int) -> bool:
        # Memo lookup
        if mask in self.memo:
            return self.memo[mask]

        for num in range(1, max_num + 1):
            bit = 1 << (num - 1)
            if mask & bit:
                continue          # already chosen

            # Win immediately
            if num >= remaining:
                self.memo[mask] = True
                return True

            # Let opponent play next
            if not self._dfs(mask | bit, remaining - num, max_num):
                self.memo[mask] = True
                return True

        # No winning move
        self.memo[mask] = False
        return False
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    unordered_map<int, bool> memo;

    bool canIWin(int maxChoosableInteger, int desiredTotal) {
        // Early exits
        if (desiredTotal <= 0) return true;
        int maxSum = (maxChoosableInteger * (maxChoosableInteger + 1)) / 2;
        if (maxSum < desiredTotal) return false;

        return dfs(0, desiredTotal, maxChoosableInteger);
    }

private:
    bool dfs(int mask, int remaining, int maxNum) {
        if (memo.count(mask)) return memo[mask];

        for (int num = 1; num <= maxNum; ++num) {
            int bit = 1 << (num - 1);
            if (mask & bit) continue;          // already used

            if (num >= remaining) {            // win immediately
                memo[mask] = true;
                return true;
            }

            // Opponent's turn
            if (!dfs(mask | bit, remaining - num, maxNum)) {
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

## 📚 Blog Article – “Can I Win? Mastering LeetCode 464 with DP & Bitmask”

> **SEO Keywords**: LeetCode 464, Can I Win, dynamic programming, bitmask, game theory, coding interview, algorithm interview, Java, Python, C++.

---

### 1️⃣ The Problem in a Nutshell

> *“Can I Win”* is a classic impartial game problem. Two players pick a unique number each turn, adding it to a running total. The first to reach or exceed the desired total wins. The challenge? Determine whether the first player can force a win given both play optimally.

It’s a perfect example of *game theory* combined with *dynamic programming* and *bitmask* techniques—skills that interviewers love.

---

### 2️⃣ Why This Problem Rocks (Good)

- **Small Input Size** – `maxChoosableInteger ≤ 20` lets us explore all game states with a 20‑bit mask.  
- **Deterministic** – No randomness; we can reason about the optimal path.  
- **Clear DP** – Each state can be memoized: “does the current player win from this set of remaining numbers?”  
- **Extensible** – The same pattern applies to other “take‑away” games (e.g., Nim variants).

---

### 3️⃣ The Pitfalls (Bad)

1. **Infinite Recursion** – Without memoization you’ll blow the stack.  
2. **State Explosion** – Naïve enumeration (e.g., list all permutations) is exponential and impossible for `maxChoosableInteger = 20`.  
3. **Off‑by‑One Errors** – Off‑by‑one in bit positions (`1 << (num-1)`) is a common bug.  
4. **Edge Cases** – Forgetting the `desiredTotal <= 0` shortcut or the sum‑check leads to wrong answers on trivial tests.

---

### 4️⃣ The “Ugly” – A Quick Debug Checklist

| Issue | Symptoms | Fix |
|-------|----------|-----|
| Wrong memo key | Same mask reused incorrectly | Use `unordered_map<int, bool>` or `Map<Integer, Boolean>` |
| TLE on worst case | Too many recursive calls | Add early exit: if `num >= remaining` return `true` immediately |
| Wrong bit shift | Off‑by‑one bit | Verify with small examples (e.g., max=3, pick=1 -> bit=1) |
| Stack overflow | Recursion depth > 1000 | Depth is ≤ 20, but safe‑guard with iterative DP if needed |

---

### 5️⃣ Step‑by‑Step Walkthrough (The Algorithm)

1. **Base Conditions**  
   * `desiredTotal <= 0` → first player wins instantly.  
   * Sum of `1 … maxChoosableInteger < desiredTotal` → impossible, return `false`.

2. **Recursive Helper**  
   * Parameter `mask` encodes *used* numbers.  
   * Loop over all `num` from `1` to `maxChoosableInteger`.  
   * Skip if already used (`mask & bit`).  
   * **Immediate Win**: if `num >= remaining` → current player wins.  
   * **Opponent’s Turn**: `if !canWin(newMask, remaining - num)` → we win.

3. **Memoize & Return** – Store the result for each mask to avoid recomputation.

---

### 6️⃣ Complexity Analysis

- **Time**: `O(2^n * n)` (worst case), where `n = maxChoosableInteger`.  
  *Reason*: There are `2^n` masks; for each we may try up to `n` numbers.  
- **Space**: `O(2^n)` for memo table + `O(n)` recursion depth.  

With `n ≤ 20`, this is comfortably fast for all LeetCode tests.

---

### 7️⃣ “What if I want a faster solution?”  

For this specific problem, the DP + bitmask solution is already optimal in practice.  
If you encounter larger `maxChoosableInteger`, you’d need a different approach (e.g., Sprague–Grundy numbers or combinatorial analysis), but those exceed the constraints of LeetCode 464.

---

### 8️⃣ Interview‑Ready Tips

| Tip | Why It Matters |
|-----|----------------|
| **Explain early exits** | Shows you care about performance and edge cases. |
| **Draw the mask** | Visualizing the 20‑bit state helps interviewers see your thought process. |
| **Talk about recursion vs iteration** | Interviewers love seeing you consider stack safety. |
| **Mention memoization map** | Highlights your knowledge of caching to prevent repeated work. |
| **Test with small numbers** | Demonstrates debugging skill and understanding of the algorithm. |

---

### 9️⃣ Final Thoughts

“Can I Win” is a textbook problem that blends **game theory** with **dynamic programming** and **bit manipulation**. Mastering it not only gives you a winning solution for LeetCode but also equips you with a pattern you’ll see in many interview puzzles.

- **Good**: Small state space, deterministic, clean DP.  
- **Bad**: Easy to slip on bit indexing, missing base cases.  
- **Ugly**: Recursive TLE if you forget memoization.

Use the code snippets above (Java, Python, C++) as a ready‑to‑paste reference. Then, practice explaining the algorithm out loud—your ability to articulate the reasoning is just as valuable as the code itself.

Happy coding, and may your first player always win! 🚀

--- 

**Ready to ace your next interview?**  
Keep practicing, read editorial solutions, and share your own “Can I Win” analysis on your blog or GitHub. Recruiters love seeing clean, explainable solutions—especially when you demonstrate both the *why* and the *how* behind your code.