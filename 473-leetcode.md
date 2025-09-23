---
title: LeetCode 473. Matchsticks to Square - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 473 – **Matchsticks to Square**  
### The Good, The Bad, and The Ugly  
> **Want to land a tech job?**  
> Master this problem, show a clean solution, and walk into your interview with confidence.  

---

## 1. Problem Overview  

> **Given** an integer array `matchsticks`, where `matchsticks[i]` is the length of the *i*-th matchstick.  
> **Goal**: Use *all* matchsticks to build a **square** (no breaking, no extra sticks).  
> **Return** `true` if possible, otherwise `false`.

### Constraints

| | |
|---|---|
|`1 <= matchsticks.length <= 15`|`1 <= matchsticks[i] <= 10⁸`|

---

## 2. The “Good”

* **Intuitive**: Treat it like a partition‑into‑four‑equal‑sums problem.  
* **Backtracking**: Easy to understand, minimal code.  
* **Bitmask DP**: Exploits the small input size (≤15) → `2^15 = 32768` states.  

---

## 3. The “Bad”

* **Naïve recursion** quickly explodes with 15 sticks (worst‑case `4^15`).  
* **Sorting** is not strictly required but often used for pruning.  
* **Large numbers** (≤10⁸) can cause overflow if not handled with `long`.  

---

## 4. The “Ugly”

* **Tight constraints** force you to handle many edge cases:
  * Total length not divisible by 4 → immediate `false`.  
  * A single stick longer than the target side → impossible.  
* **Bit‑mask tricks** can be hard to read for candidates unfamiliar with them.  

---

## 5. Optimal Approach: DFS + Bitmask + Memoization  

1. **Compute total perimeter** and the **target side** (`total / 4`).  
2. **Early exits**  
   * If `total % 4 != 0` → `false`.  
   * If any stick > target side → `false`.  
3. **DFS** over the sticks, trying to build four sides.  
4. Use a **bitmask** to represent which sticks have been used.  
5. **Memoize** `(mask, sideIndex)` → result to avoid recomputation.  

> **Why memoization?**  
> Without it, the algorithm may revisit identical states many times, especially with duplicate lengths.

---

## 6. Code Implementations  

Below are clean, well‑commented solutions for **Java**, **Python**, and **C++**.

---

### 6.1 Java

```java
import java.util.*;

class Solution {
    private int[] sticks;          // matchstick lengths
    private int sideLen;           // target side length
    private int n;                 // number of sticks
    private Map<Long, Boolean> memo; // key = (mask << 3) | sideIndex

    public boolean makesquare(int[] matchsticks) {
        if (matchsticks == null || matchsticks.length == 0) return false;
        this.n = matchsticks.length;
        this.sticks = Arrays.copyOf(matchsticks, n);

        long total = 0;
        for (int v : sticks) total += v;
        if (total % 4 != 0) return false;

        this.sideLen = (int) (total / 4);

        // early pruning: a stick longer than the side can't fit
        for (int v : sticks) {
            if (v > sideLen) return false;
        }

        // sort descending to place large sticks first (better pruning)
        Arrays.sort(this.sticks);
        reverse(this.sticks);

        this.memo = new HashMap<>();
        return dfs(0, 0, 0);
    }

    /** DFS with mask and current side */
    private boolean dfs(int mask, int sideIdx, int curLen) {
        long key = ((long)mask << 3) | sideIdx;
        if (memo.containsKey(key)) return memo.get(key);

        // All 4 sides finished → success
        if (sideIdx == 3) return true;

        for (int i = 0; i < n; i++) {
            if ((mask & (1 << i)) != 0) continue; // already used
            int len = sticks[i];
            if (curLen + len > sideLen) continue;  // would overflow current side

            int newMask = mask | (1 << i);
            int newCurLen = curLen + len;
            int newSideIdx = (newCurLen == sideLen) ? sideIdx + 1 : sideIdx;
            if (dfs(newMask, newSideIdx, newCurLen == sideLen ? 0 : newCurLen)) {
                memo.put(key, true);
                return true;
            }
        }

        memo.put(key, false);
        return false;
    }

    private void reverse(int[] a) {
        int l = 0, r = a.length - 1;
        while (l < r) {
            int tmp = a[l]; a[l] = a[r]; a[r] = tmp;
            l++; r--;
        }
    }
}
```

---

### 6.2 Python

```python
from functools import lru_cache
from typing import List

class Solution:
    def makesquare(self, matchsticks: List[int]) -> bool:
        if not matchsticks:
            return False

        total = sum(matchsticks)
        if total % 4 != 0:
            return False

        side = total // 4
        if max(matchsticks) > side:
            return False

        # Sort descending to place large sticks first
        matchsticks.sort(reverse=True)
        n = len(matchsticks)

        @lru_cache(None)
        def dfs(mask: int, side_idx: int, cur_len: int) -> bool:
            if side_idx == 3:                # 4th side automatically fits
                return True
            for i in range(n):
                if mask & (1 << i):
                    continue
                if cur_len + matchsticks[i] > side:
                    continue
                new_mask = mask | (1 << i)
                new_cur = cur_len + matchsticks[i]
                new_side = side_idx + 1 if new_cur == side else side_idx
                new_cur = 0 if new_cur == side else new_cur
                if dfs(new_mask, new_side, new_cur):
                    return True
            return False

        return dfs(0, 0, 0)
```

---

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool makesquare(vector<int>& matchsticks) {
        int n = matchsticks.size();
        if (n == 0) return false;

        long long total = 0;
        for (int v : matchsticks) total += v;
        if (total % 4 != 0) return false;

        int side = total / 4;
        for (int v : matchsticks) if (v > side) return false;

        sort(matchsticks.begin(), matchsticks.end(), greater<int>()); // descending
        unordered_map<long long, bool> memo;

        function<bool(int, int, int)> dfs = [&](int mask, int sideIdx, int curLen) -> bool {
            long long key = ((long long)mask << 3) | sideIdx;
            if (memo.find(key) != memo.end()) return memo[key];
            if (sideIdx == 3) return true; // last side fits automatically

            for (int i = 0; i < n; ++i) {
                if (mask & (1 << i)) continue;          // already used
                int len = matchsticks[i];
                if (curLen + len > side) continue;      // overflow current side

                int newMask = mask | (1 << i);
                int newCur = curLen + len;
                int newSideIdx = (newCur == side) ? sideIdx + 1 : sideIdx;
                newCur = (newCur == side) ? 0 : newCur;
                if (dfs(newMask, newSideIdx, newCur)) {
                    memo[key] = true;
                    return true;
                }
            }
            memo[key] = false;
            return false;
        };

        return dfs(0, 0, 0);
    }
};
```

> **Why `((long long)mask << 3) | sideIdx`?**  
> `mask` needs 15 bits; we shift by 3 (since `0 <= sideIdx <= 3`) to keep a unique key.

---

## 7. Edge‑Case Checklist

| Condition | Why it matters |
|-----------|----------------|
| Empty array | Cannot form a square. |
| Total not divisible by 4 | Impossible – no equal‑length sides. |
| Stick > target side | Single stick too long → no fit. |
| Duplicate lengths | Memoization handles identical masks efficiently. |
| `n < 4` | Still possible if each side uses multiple sticks. |

---

## 8. Complexity Analysis  

| | Time | Space |
|---|------|-------|
|Brute‑Force DFS (no pruning) | `O(4^n)` | `O(n)` |
|DFS + Bitmask + Memoization | `O(2^n * n)` (≈ `O(2^15 * 15)`) | `O(2^n)` for memo table |
|C++ unordered‑map | `O(2^n)` average case, worst‑case `O(2^n * n)` |

*With `n <= 15`, the optimal solution runs in milliseconds on modern hardware.*

---

## 9. Common Pitfalls to Avoid

1. **Integer overflow**  
   * Use `long`/`long long` when summing stick lengths.  
2. **Wrong side index handling**  
   * Remember: after finishing side 3 we can return `true` because the remaining sticks must form the 4th side.  
3. **Missing pruning**  
   * Sorting descending and placing the largest sticks first dramatically cuts the search tree.  
4. **Incorrect memo key**  
   * Make sure mask and side index are combined uniquely (shift + OR).  
5. **Time‑outs on duplicate sticks**  
   * Without memoization, duplicate lengths lead to exponential blow‑up.

---

## 10. Interview‑Ready Tips  

| Tip | Why It Helps |
|-----|--------------|
| **Explain the problem in plain English** before coding. | Shows communication skills. |
| **Show the intuition** (partition into 4 equal sums). | Demonstrates problem‑solving mindset. |
| **Write a clean DFS first** (no memo), then add pruning. | Lets interviewers see your thought process. |
| **Discuss the memo key** clearly in your answer. | Avoids confusion about bit‑mask tricks. |
| **Mention time‑complexity** and why the constraints allow `2^15` states. | Highlights algorithmic awareness. |
| **Show edge‑case tests** (`[1,1,1,1,4]`, `[1,1,1,1,2,2,2,2]`). | Demonstrates thoroughness. |

> **Pro‑move:** After solving, ask the interviewer:  
> *“If we had 30 sticks, would this approach still hold? What changes?”*  
> It turns a simple question into a discussion about scalability and algorithmic trade‑offs.

---

## 11. Take‑Away: How to Nail This Problem

| ✅ Feature | ✅ Implementation |
|-----------|-------------------|
| **Readability** | Clear comments + helper functions |
| **Efficiency** | Bitmask + memoization |
| **Scalability** | Handles duplicates & large numbers |
| **Interview‑friendly** | Discuss early exits, pruning, and complexity |

---

## 12. Conclusion  

> **LeetCode 473** is a classic interview staple.  
> Master the backtracking‑with‑bitmask pattern, understand the edge cases, and be ready to explain the algorithm in plain language.  

> **SEO‑ready hook**:  
> *“LeetCode 473 Matchsticks to Square solution – DFS, Bitmask, Memoization – prepare for your next technical interview.”*

---

### 📄 Meta Description  
*Learn how to solve LeetCode 473 “Matchsticks to Square” in Java, Python, and C++ with a clear DFS + bitmask approach. Understand the good, bad, and ugly aspects, and walk into your interview with a polished solution.*

--- 

Happy coding, and good luck landing that dream role! 🚀