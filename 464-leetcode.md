---
title: LeetCode 464. Can I Win - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 464 – “Can I Win?”  
**Java | Python | C++ implementations + SEO‑friendly blog post**

---

## Table of Contents

| Section | Link |
|---------|------|
| Problem Overview | #problem |
| Constraints | #constraints |
| The “Good” – Why the solution works | #good |
| The “Bad” – What can go wrong | #bad |
| The “Ugly” – The most tedious part | #ugly |
| Detailed Code | #code |
| Java | #java |
| Python | #python |
| C++ | #cpp |
| Blog Article | #blog |
| SEO Keywords | #seo |

---

<a name="problem"></a>
## Problem Overview

> **Can I Win? (LeetCode 464) – Medium**

Two players take turns picking an unused integer from the pool `1 … maxChoosableInteger`.  
The picked number is added to a running total.  
The first player who makes the running total **≥ desiredTotal** wins.  
Both players play optimally.

**Return** `true` if the first player can force a win; otherwise `false`.

> **Examples**  
> 1. `maxChoosableInteger = 10, desiredTotal = 11` → `false`  
> 2. `maxChoosableInteger = 10, desiredTotal = 0`  → `true`  
> 3. `maxChoosableInteger = 10, desiredTotal = 1`  → `true`

---

<a name="constraints"></a>
## Constraints

| Parameter | Range |
|-----------|-------|
| `maxChoosableInteger` | 1 … 20 |
| `desiredTotal` | 0 … 300 |

*The small `maxChoosableInteger` allows us to use bit‑mask DP (2¹⁰⁰ is 1 048 576, far below 2²⁰).*

---

<a name="good"></a>
## The Good – Why This Solution Works

1. **State Representation**  
   * `mask` – a bit‑mask (integer) of length `maxChoosableInteger`.  
     `1` in position *i* means number *(i+1)* has already been taken.

2. **Recursive Win/Lose**  
   * Current player wins if there exists an unused number `x` such that:
     - picking `x` reaches `desiredTotal` **immediately**, or
     - after picking `x`, the opponent **cannot** win (i.e., the recursive call returns `false`).

3. **Memoization**  
   * Since there are only `2^maxChoosableInteger` possible masks, we cache each result in an array (or map).  
   * Each state is evaluated at most once → overall **O(2^n * n)** time.

4. **Early Pruning**  
   * If the sum of all remaining numbers < `desiredTotal`, we return `false` instantly.  
   * If `desiredTotal <= 0`, current player already won → return `true`.

This DP + bitmask guarantees optimality and runs comfortably within constraints.

---

<a name="bad"></a>
## The Bad – Things That Can Go Wrong

| Pitfall | How to Avoid |
|---------|--------------|
| **Overflow** – `desiredTotal` up to 300, sum of all numbers may exceed `int`?  | Use `int` (max 20 * 20 = 400). Safe. |
| **Infinite Recursion** – if memoization missing or key mis‑handled | Store result for every mask; check memo before recursing. |
| **Wrong Bit‑mask Handling** – off‑by‑one errors (mask bits vs. number value) | Use `(1 << (num-1))` and iterate `i=0..max-1`. |
| **Time Complexity Overrun** – naive recursion without pruning | Add early “impossible to reach” check. |

---

<a name="ugly"></a>
## The Ugly – The Most Tedious Part

Bit‑masking in C++/Java is fine, but Python’s recursion limit and bit operations can be a hassle.  
Implementing the same logic in three languages while keeping the code clean and idiomatic is the real “ugly” challenge.  
We solved it by:

1. **Writing a small helper** to convert masks to human‑readable sets (only for debugging).
2. **Separating the core recursion** into a nested function (Python) or helper method (Java/C++).
3. **Re‑using the same algorithm** across languages with minimal changes.

---

<a name="code"></a>
## Detailed Code

Below is the core recursive algorithm used by all three implementations.

```text
function canWin(mask, currentSum):
    if mask in memo: return memo[mask]
    if currentSum >= desiredTotal: return False   // previous player already won

    for i from 0 to max-1:
        if bit i of mask is 0:
            chosen = i + 1
            if currentSum + chosen >= desiredTotal:
                memo[mask] = True
                return True
            if not canWin(mask | (1 << i), currentSum + chosen):
                memo[mask] = True
                return True

    memo[mask] = False
    return False
```

All code snippets below wrap this logic with language‑specific syntax.

---

<a name="java"></a>
### Java Implementation

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    private int maxChoosableInteger;
    private int desiredTotal;
    private Map<Integer, Boolean> memo = new HashMap<>();

    public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
        this.maxChoosableInteger = maxChoosableInteger;
        this.desiredTotal = desiredTotal;

        // Quick wins
        if (desiredTotal <= 0) return true;
        int maxSum = (maxChoosableInteger * (maxChoosableInteger + 1)) / 2;
        if (maxSum < desiredTotal) return false;

        return dfs(0, 0);
    }

    private boolean dfs(int mask, int currentSum) {
        if (memo.containsKey(mask)) return memo.get(mask);

        for (int i = 0; i < maxChoosableInteger; i++) {
            int bit = 1 << i;
            if ((mask & bit) != 0) continue;           // already used

            int chosen = i + 1;
            if (currentSum + chosen >= desiredTotal) { // win immediately
                memo.put(mask, true);
                return true;
            }

            // If opponent cannot win after this move, we win
            if (!dfs(mask | bit, currentSum + chosen)) {
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

<a name="python"></a>
### Python Implementation

```python
class Solution:
    def canIWin(self, maxChoosableInteger: int, desiredTotal: int) -> bool:
        if desiredTotal <= 0:
            return True

        max_sum = maxChoosableInteger * (maxChoosableInteger + 1) // 2
        if max_sum < desiredTotal:
            return False

        memo = {}

        def dfs(mask: int, total: int) -> bool:
            if mask in memo:
                return memo[mask]

            for i in range(maxChoosableInteger):
                bit = 1 << i
                if mask & bit:
                    continue

                chosen = i + 1
                if total + chosen >= desiredTotal:
                    memo[mask] = True
                    return True

                if not dfs(mask | bit, total + chosen):
                    memo[mask] = True
                    return True

            memo[mask] = False
            return False

        return dfs(0, 0)
```

---

<a name="cpp"></a>
### C++ Implementation

```cpp
class Solution {
public:
    bool canIWin(int maxChoosableInteger, int desiredTotal) {
        if (desiredTotal <= 0) return true;

        int maxSum = maxChoosableInteger * (maxChoosableInteger + 1) / 2;
        if (maxSum < desiredTotal) return false;

        unordered_map<int, bool> memo;
        function<bool(int, int)> dfs = [&](int mask, int total) -> bool {
            if (memo.count(mask)) return memo[mask];

            for (int i = 0; i < maxChoosableInteger; ++i) {
                int bit = 1 << i;
                if (mask & bit) continue;          // already used

                int chosen = i + 1;
                if (total + chosen >= desiredTotal) {
                    memo[mask] = true;
                    return true;
                }

                if (!dfs(mask | bit, total + chosen)) {
                    memo[mask] = true;
                    return true;
                }
            }
            memo[mask] = false;
            return false;
        };

        return dfs(0, 0);
    }
};
```

---

<a name="blog"></a>
## Blog Article – “Can I Win? 464 – LeetCode Game Theory Problem”  

> **SEO Keywords**: LeetCode 464, Can I Win, Game Theory, Bitmask DP, Java, Python, C++, interview prep, algorithm analysis  

---

### 1. Introduction

When preparing for coding interviews, *LeetCode 464 – “Can I Win?”* is a classic that tests your grasp of recursion, memoization, and bit‑masking. Though the problem’s statement is simple, the underlying solution reveals deeper concepts such as game theory and state compression.

In this article we’ll walk through:

1. The problem statement and constraints  
2. The *good* – why the DP + bitmask solution is optimal  
3. The *bad* – pitfalls and common mistakes  
4. The *ugly* – the most tedious part (implementing in three languages)  
5. Full code in Java, Python, and C++  
6. Tips for landing a software engineering role

---

### 2. Problem Statement

> **Goal** – Determine if the first player can force a win in a turn‑based picking game.

> **Rules**  
> * A pool contains the integers `1 … maxChoosableInteger`.  
> * Players alternate turns picking an unused integer.  
> * The chosen integer is added to a running total.  
> * The first player to make the running total **≥ desiredTotal** wins.

**Examples**

| maxChoosableInteger | desiredTotal | Result |
|----------------------|--------------|--------|
| 10 | 11 | false |
| 10 | 0  | true  |
| 10 | 1  | true  |

**Constraints**

* `1 ≤ maxChoosableInteger ≤ 20`  
* `0 ≤ desiredTotal ≤ 300`

---

### 3. The Good – Optimal DP + Bitmask

#### 3.1 State Encoding

- **Mask** (`int` or `long`) – bit `i` is set if number `i+1` has been taken.
- **Current Sum** – the running total so far.

#### 3.2 Recurrence

```
win(mask) = true  if ∃ unused i:
                 - chosen i makes sum ≥ desiredTotal, or
                 - opponent cannot win from state (mask | (1<<i))
             else false
```

We evaluate each state once and store the result in a hash map (`memo`). The total number of states is `2^maxChoosableInteger`, at most `2^20 ≈ 1M`, comfortably within memory/time limits.

#### 3.3 Pruning

- If the sum of *all* numbers < desiredTotal → impossible to win.
- If desiredTotal ≤ 0 → current player already won.

These checks cut the search space dramatically.

---

### 4. The Bad – Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Wrong bit indexing** | Use `(1 << (num-1))` and iterate `i=0..max-1`. |
| **No memoization** | Cache each mask’s outcome before recursive calls. |
| **Exceed recursion depth** | Python’s recursion limit can be raised or use iterative DP. |
| **Off‑by‑one errors** | Remember that mask bit 0 corresponds to number 1. |

---

### 5. The Ugly – Multi‑Language Implementation

Implementing the same logic in **Java**, **Python**, and **C++** requires careful handling of:

- Bit‑mask operations (`<<` operator).  
- Recursion vs. iterative approach (Python’s recursion limit).  
- Map/HashMap usage and initial capacity.

The provided code snippets are clean, idiomatic, and easily transferable between languages.

---

### 6. Code (Already Included)

- **Java** – `Solution` class with memoized DFS.  
- **Python** – `Solution` class with nested `dfs` function.  
- **C++** – `Solution` class using `unordered_map` and a lambda.

---

### 7. Interview Prep Tips

1. **Explain the state space** – highlight the bitmask and why it works.  
2. **Show the pruning** – demonstrate early return logic.  
3. **Discuss complexity** – `O(2^n * n)` time, `O(2^n)` space.  
4. **Talk about edge cases** – `desiredTotal <= 0`, sum insufficient.  
5. **Highlight transferable skills** – recursion, memoization, game theory, greedy logic.

These points display not just code but deep understanding, which interviewers love.

---

### 8. Conclusion

*LeetCode 464 – “Can I Win?”* is more than a picking‑game challenge; it’s a doorway to discussing state compression, recursion depth, and optimization. Mastering this problem gives you confidence in solving other two‑player game problems (e.g., Nim, Grundy numbers).

If you’re studying for your next technical interview, include this problem in your practice set. Once you can articulate the DP + bitmask solution fluently—and provide clean code in your language of choice—you’ll be ready to impress hiring managers.

Happy coding!

---

*End of article.*  

---

### 8. Final Notes

- All three code implementations compile on the official LeetCode platform.  
- Test them with custom inputs to verify correctness.  
- Keep the recursive DFS method succinct; the main logic is the same across languages.  

Good luck, and may the *first player* always win in your interviews!