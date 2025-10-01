---
title: LeetCode 464. Can I Win - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 464 – *Can I Win*  
### Java | Python | C++ – All three solutions + a complete blog post

> **TL;DR** –  
> *If the sum of all available numbers is less than `desiredTotal`, the first player can never win.  
> If any single number is already ≥ `desiredTotal`, the first player wins immediately.  
> Otherwise, we use a depth‑first search with memoization (`bitmask DP`) to explore the game tree.  
> The search terminates as soon as one move forces the opponent into a losing state.*

---

## 1.  The Code

Below you’ll find **three** independent, self‑contained solutions that you can drop straight into your IDE and run.  
All three use the same algorithmic idea (DFS + memoization) but are written in the language you prefer.

---

### 1.1 Java

```java
import java.util.*;

public class Solution {

    // Bitmask key -> true if current player can force a win
    private Map<Integer, Boolean> memo = new HashMap<>();

    public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
        // Quick exits
        if (desiredTotal <= 0) return true;                // 0 or negative → already won
        int sumAll = (maxChoosableInteger * (maxChoosableInteger + 1)) / 2;
        if (sumAll < desiredTotal) return false;           // impossible to reach target
        if (maxChoosableInteger >= desiredTotal) return true; // can win in the first move

        return dfs(0, 0, maxChoosableInteger, desiredTotal);
    }

    /**
     * @param usedMask   Bitmask of already chosen numbers
     * @param currentSum Current running total
     * @param max        Maximum number that can be chosen
     * @param target     Desired total
     */
    private boolean dfs(int usedMask, int currentSum, int max, int target) {
        // If we already computed this state, return cached result
        if (memo.containsKey(usedMask)) return memo.get(usedMask);

        // Try every available number
        for (int i = 1; i <= max; i++) {
            int bit = 1 << (i - 1);
            if ((usedMask & bit) != 0) continue;        // number already used

            // If picking this number wins immediately
            if (currentSum + i >= target) {
                memo.put(usedMask, true);
                return true;
            }

            // Recurse: after picking i, opponent plays next
            if (!dfs(usedMask | bit, currentSum + i, max, target)) {
                // If opponent loses from this state, current player wins
                memo.put(usedMask, true);
                return true;
            }
        }

        // No winning move found → current player loses
        memo.put(usedMask, false);
        return false;
    }

    /* ---------  Main for quick testing  --------- */
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.canIWin(10, 11)); // false
        System.out.println(s.canIWin(10, 0));  // true
        System.out.println(s.canIWin(10, 1));  // true
    }
}
```

---

### 1.2 Python

```python
from functools import lru_cache

class Solution:
    def canIWin(self, maxChoosableInteger: int, desiredTotal: int) -> bool:
        # Quick exits
        if desiredTotal <= 0:
            return True
        if sum(range(1, maxChoosableInteger + 1)) < desiredTotal:
            return False
        if maxChoosableInteger >= desiredTotal:
            return True

        @lru_cache(None)
        def dfs(used_mask: int, current_sum: int) -> bool:
            # Try every available number
            for i in range(1, maxChoosableInteger + 1):
                bit = 1 << (i - 1)
                if used_mask & bit:
                    continue  # already taken

                # Immediate win?
                if current_sum + i >= desiredTotal:
                    return True

                # If opponent loses after this move → we win
                if not dfs(used_mask | bit, current_sum + i):
                    return True

            # No winning move
            return False

        return dfs(0, 0)

# ------------- quick test -------------
if __name__ == "__main__":
    sol = Solution()
    print(sol.canIWin(10, 11))  # False
    print(sol.canIWin(10, 0))   # True
    print(sol.canIWin(10, 1))   # True
```

---

### 1.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // DP cache: bitmask -> can win?
    unordered_map<int, bool> memo;

    bool canIWin(int maxChoosableInteger, int desiredTotal) {
        // Quick exits
        if (desiredTotal <= 0) return true;
        int sumAll = (maxChoosableInteger * (maxChoosableInteger + 1)) / 2;
        if (sumAll < desiredTotal) return false;
        if (maxChoosableInteger >= desiredTotal) return true;

        return dfs(0, 0, maxChoosableInteger, desiredTotal);
    }

private:
    bool dfs(int usedMask, int currentSum, int max, int target) {
        if (memo.count(usedMask)) return memo[usedMask];

        for (int i = 1; i <= max; ++i) {
            int bit = 1 << (i - 1);
            if (usedMask & bit) continue;   // already chosen

            if (currentSum + i >= target) {
                memo[usedMask] = true;
                return true;
            }

            if (!dfs(usedMask | bit, currentSum + i, max, target)) {
                memo[usedMask] = true;
                return true;
            }
        }

        memo[usedMask] = false;
        return false;
    }
};

// ---------- quick test ----------
int main() {
    Solution s;
    cout << boolalpha;
    cout << s.canIWin(10, 11) << endl; // false
    cout << s.canIWin(10, 0) << endl;  // true
    cout << s.canIWin(10, 1) << endl;  // true
}
```

---

## 2.  Blog Post – “The Good, The Bad, and The Ugly of LeetCode 464”

> **Keywords**: *LeetCode 464*, *Can I Win*, *Bitmask DP*, *Game Theory*, *Java solution*, *Python solution*, *C++ solution*, *Interview Prep*, *Algorithm Analysis*

---

### 2.1 Introduction

If you’re preparing for a software‑engineering interview, you’ve probably seen the **“Can I Win”** problem on LeetCode.  
It’s deceptively simple: two players pick distinct numbers from `1 … N` without replacement, adding them to a running total.  
Whoever first pushes the total to `desiredTotal` wins.  

At first glance you might think “pick the biggest number first, that’s it!”  
But because each move removes a number from the pool, the game is a **two‑player perfect‑information game** – a classic scenario for *minimax* + *memoization*.

In this post, we’ll break down:

1. The mathematical shortcuts that let us bail early.
2. The **good** – why bitmask DP is elegant and fast.
3. The **bad** – pitfalls in the implementation (overflow, recursion depth, memory).
4. The **ugly** – real‑world tweaks to pass edge cases and improve readability.

We’ll finish with the code for **Java, Python, and C++** – copy‑paste ready for your interview toolkit.

---

### 2.2 Quick‑Win Math (The “Good” Starts Here)

| Condition | Reason | What we do |
|-----------|--------|------------|
| `desiredTotal <= 0` | You already won before any move | Return `true` immediately |
| Sum `1 + 2 + … + N` < `desiredTotal` | Impossible to reach the goal | Return `false` |
| `N >= desiredTotal` | You can pick that number right away | Return `true` |

These three checks turn the worst case from exponential to *O(1)* for many inputs.  
They also protect against overflow when `N = 20` (max sum is 210, well inside 32‑bit int).

---

### 2.3 The Core: Bitmask + Memoized DFS

> **Why Bitmask?**  
> Each integer from `1 … N` can be either “used” or “unused”.  
> With `N ≤ 20`, a 32‑bit integer can encode the whole state: bit *i* = 1 → number *i+1* already chosen.

#### DFS Logic

1. **Iterate over all unused numbers**.  
   For each `i`:
   * If `currentSum + i >= desiredTotal` → current player wins immediately.
   * Otherwise, recurse with `i` marked as used.
2. **Opponent’s turn** – if *any* recursive call returns `false` (opponent loses), then the current player wins.
3. If *all* moves lead to opponent victory, the current state is losing.

The recursion naturally implements the minimax principle:  
- Current player wants **any** winning move.  
- Opponent will always pick the best response.

#### Memoization

The state space is `2^N` (max `2^20 ≈ 1 048 576`).  
We store the result for each `usedMask` in a hash map / array.  
This reduces time from `O(N * 2^N)` to `O(2^N)`.

---

### 2.4 The “Bad” – Common Pitfalls

| Pitfall | How to Avoid |
|---------|--------------|
| **Stack overflow** (deep recursion) | For `N=20` recursion depth ≤ 20, safe in all languages. If you worry, convert to iterative DP. |
| **Int overflow** (sum of numbers > 2^31) | With `N=20` the max sum is 210 → no overflow. Still, cast to `long` if you extend constraints. |
| **Duplicate states** | Use *exact* bitmask representation – avoid off‑by‑one errors in bit positions. |
| **Missing base case** | Always check `desiredTotal <= currentSum` before recursion. |
| **Memory blow‑up** | `unordered_map<int, bool>` in C++ or `HashMap<Integer, Boolean>` in Java uses ~4 bytes per entry. For 1M entries it's ~4 MB – fine. |

---

### 2.5 The “Ugly” – Tweak & Clean Up

1. **Early pruning** – If the *largest* unused number alone is enough to win, we can skip recursion for that branch.
2. **Sorting numbers** – Trying larger numbers first often leads to a win earlier, improving average case.
3. **Iterative DP** – Some interviewers appreciate a bottom‑up bitmask DP implementation that avoids recursion.
4. **Clear variable names** – `mask`, `sum`, `max`, `target` vs `usedMask`, `currentSum`, `maxChoosableInteger`, `desiredTotal`.
5. **Type‑safe caching** – In Python, `@lru_cache(None)` automatically handles memoization; in C++ you can use a vector<bool> of size `1<<N`.

---

### 2.6 SEO‑Optimized Summary

- **Title**: *LeetCode 464 – “Can I Win” Solution in Java, Python & C++ | Bitmask DP Explained*  
- **Meta Description**: “Learn the fastest solution for LeetCode 464, a two‑player game theory problem. Read step‑by‑step Java, Python, and C++ code, plus why memoization matters.”  
- **Headings**: `# LeetCode 464 Can I Win`, `## Quick‑Win Math`, `## Bitmask DP`, `## Common Pitfalls`, `## Full Code`, `## Interview Tips`.  
- **Keywords**: *LeetCode 464*, *Can I Win*, *Game Theory*, *Bitmask DP*, *Java solution*, *Python solution*, *C++ solution*, *Interview Preparation*, *Algorithm Analysis*.

---

### 2.7 Take‑Away Checklist

| ✅ | Task |
|----|------|
| ✅ | Understand the math shortcuts (early exit). |
| ✅ | Implement DFS with bitmask state. |
| ✅ | Memoize results – avoid recomputation. |
| ✅ | Test with the given examples and edge cases (e.g., `N=20, desiredTotal=210`). |
| ✅ | Refactor for readability – clear variable names and comments. |
| ✅ | Prepare the code snippets for interview submission. |

Good luck! With these techniques, you’ll breeze through **Can I Win** and demonstrate mastery of game‑theory DP to any hiring manager.

---

### 2.8 Conclusion

LeetCode 464 is a classic demonstration of how *mathematical insight* + *state compression* can tame an exponential problem.  
The “good” is the elegance of a 20‑bit mask and a single hash map lookup per state.  
The “bad” is the small risk of recursion overflow or memory misuse—mostly avoidable with careful coding.  
And the “ugly” is the polishing work: ordering moves, adding comments, and ensuring your code is interview‑friendly.

Grab the **Java, Python, and C++** solutions above, run them locally, and feel confident that you can ace this problem on any interview board.

--- 

> *Happy coding, and may the winner always be you!*

---


--- 

**End of Blog Post**