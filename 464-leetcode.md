---
title: LeetCode 464. Can I Win - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 464 – “Can I Win”

| Language | Code |
|----------|------|
| **Java** | [See below](#java-code) |
| **Python** | [See below](#python-code) |
| **C++** | [See below](#cpp-code) |

---

## 2.  The Problem (Short & Sweet)

Two players take turns picking a number from **1 … maxChoosableInteger** *without replacement*.  
They keep a running total.  
The first player who makes the total **≥ desiredTotal** wins.

> **Return `true` if the first player can force a win, otherwise `false`.**

**Constraints**

* 1 ≤ maxChoosableInteger ≤ 20  
* 0 ≤ desiredTotal ≤ 300

The small value of `maxChoosableInteger` (≤20) hints that a *bit‑mask DP* is possible.

---

## 3.  Quick Edge‑Case “Cuts”

| Case | Why it can be decided immediately |
|------|------------------------------------|
| `desiredTotal <= 0` | The first player already wins. |
| Sum of all numbers < `desiredTotal` | Even if the first player takes all numbers, the target can’t be reached → loss. |
| `maxChoosableInteger * (maxChoosableInteger + 1) / 2 < desiredTotal` | Same as above. |

These checks prune a large amount of impossible search space.

---

## 4.  Core Idea – Bit‑mask DP

* Each number 1…N can be represented by a bit in an integer (or `long`/`unsigned long long` for 20 bits).  
* **State**: `mask` – which numbers are *still available*.  
* **Memo**: `Map<Integer, Boolean>` – whether the *current player* can force a win from that state.  
* **Recurrence**:  
  * For every number `i` that is still available (`mask & (1 << i) != 0`):  
    * If `i+1 >= desiredTotal` → current player wins immediately.  
    * Else recursively check if the *opponent* loses from the new mask `mask ^ (1 << i)`.  
  * If *any* move leads to the opponent losing → current player can win (`true`).  
  * If *all* moves lead to opponent win → current player loses (`false`).

Because there are only \(2^{20} = 1,048,576\) masks, this solution runs comfortably in time and memory.

---

## 5.  Code

### Java

```java
// Java 17
import java.util.HashMap;
import java.util.Map;

public class Solution {
    private final Map<Integer, Boolean> memo = new HashMap<>();
    private int desiredTotal;

    public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
        // Quick cuts
        if (desiredTotal <= 0) return true;
        int sum = maxChoosableInteger * (maxChoosableInteger + 1) / 2;
        if (sum < desiredTotal) return false;

        this.desiredTotal = desiredTotal;
        // All numbers are available initially -> mask with all bits set
        int allNumbersMask = (1 << maxChoosableInteger) - 1;
        return canWin(allNumbersMask);
    }

    private boolean canWin(int mask) {
        if (memo.containsKey(mask)) return memo.get(mask);

        // Try each available number
        for (int i = 0; i < 20; i++) {   // 20 is max limit
            if ((mask & (1 << i)) != 0) { // number i+1 is available
                int chosen = i + 1;
                // If we can reach or exceed desiredTotal, we win instantly
                if (chosen >= desiredTotal) {
                    memo.put(mask, true);
                    return true;
                }

                // Recurse: after we pick i+1, the opponent plays on the remaining mask
                int nextMask = mask ^ (1 << i);
                if (!canWin(nextMask)) {   // opponent loses => we win
                    memo.put(mask, true);
                    return true;
                }
            }
        }

        // No winning move found
        memo.put(mask, false);
        return false;
    }
}
```

### Python

```python
# Python 3.10+

from functools import lru_cache

class Solution:
    def canIWin(self, maxChoosableInteger: int, desiredTotal: int) -> bool:
        if desiredTotal <= 0:
            return True

        total_sum = maxChoosableInteger * (maxChoosableInteger + 1) // 2
        if total_sum < desiredTotal:
            return False

        @lru_cache(maxsize=None)
        def dfs(mask: int) -> bool:
            # Try each still‑available number
            for i in range(maxChoosableInteger):
                if mask & (1 << i):
                    chosen = i + 1
                    if chosen >= desiredTotal:
                        return True
                    if not dfs(mask ^ (1 << i)):
                        return True
            return False

        full_mask = (1 << maxChoosableInteger) - 1
        return dfs(full_mask)
```

### C++

```cpp
// C++17
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool canIWin(int maxChoosableInteger, int desiredTotal) {
        if (desiredTotal <= 0) return true;
        int totalSum = maxChoosableInteger * (maxChoosableInteger + 1) / 2;
        if (totalSum < desiredTotal) return false;

        vector<int> memo(1 << maxChoosableInteger, -1);
        function<bool(int)> dfs = [&](int mask) -> bool {
            if (memo[mask] != -1) return memo[mask] == 1;
            for (int i = 0; i < maxChoosableInteger; ++i) {
                if (mask & (1 << i)) {          // number i+1 is available
                    int chosen = i + 1;
                    if (chosen >= desiredTotal) {
                        memo[mask] = 1;
                        return true;
                    }
                    if (!dfs(mask ^ (1 << i))) {
                        memo[mask] = 1;
                        return true;
                    }
                }
            }
            memo[mask] = 0;
            return false;
        };

        int fullMask = (1 << maxChoosableInteger) - 1;
        return dfs(fullMask);
    }
};
```

All three solutions share the same *bit‑mask DP* core and run in **O(2ⁿ · n)** time with **O(2ⁿ)** memory, which is fine for n ≤ 20.

---

## 6.  Blog Article – “LeetCode 464: Can I Win – The Good, the Bad, the Ugly”

> **Keywords**: LeetCode 464, Can I Win, bitmask DP, dynamic programming, interview questions, algorithm interview, Java, Python, C++ solution, job interview preparation

---

### 1. Introduction

If you’ve ever stared at the “Can I Win” problem on LeetCode, you know it’s a deceptively simple question with a very tricky solution. The game is an off‑beat version of the classic “100 game” but with **no replacement** – each integer can only be chosen once. The challenge: can the first player force a win, assuming both players play perfectly?

In this article we dissect the problem, explore the “good, the bad, and the ugly” of typical solutions, and present clean, production‑ready code in Java, Python, and C++ that you can drop straight into your interview stack.

---

### 2. Problem Recap

> **Game Rules**  
> * Two players alternate turns.  
> * Each turn a player chooses an unused integer between 1 and `maxChoosableInteger`.  
> * The chosen number is added to a running total.  
> * The first player to bring the total **≥ desiredTotal** wins.

> **Goal**  
> Return `true` if the first player can force a win, otherwise `false`.

> **Constraints**  
> * `1 ≤ maxChoosableInteger ≤ 20`  
> * `0 ≤ desiredTotal ≤ 300`

The constraints are the key: 20 numbers mean there are at most 2²⁰ ≈ 1 million distinct states – a perfect fit for a bit‑mask DP.

---

### 3. The Good – A Beautifully Small Search Space

**Why the solution works**

* *Each number can only be used once* → the game state can be represented by a **bitmask**.  
  *Bit i* tells whether the number `i+1` is still available.  
* The total number of states is `2ⁿ`, with `n ≤ 20`.  
  This is tiny enough for memoization, even in languages like Python where recursion overhead is a bit higher.

**The Recurrence**

```text
canWin(mask) =
    for each i such that bit i of mask is set:
        if i+1 >= desiredTotal: return true
        if !canWin(mask ^ (1<<i)): return true
    return false
```

The algorithm simply tries every legal move. If any move lets the current player force a win, we return `true`. Otherwise the player loses.

**Why it's “good”**

* **Time**: O(2ⁿ · n) – with n = 20, this is < 20 million operations.  
* **Memory**: O(2ⁿ) – about 1 MB for a `bool` array.  
* **Readability**: The logic maps almost literally onto the problem description.  

---

### 4. The Bad – Brute‑Force and Why It Fails

A naive solution would simulate every possible sequence of moves:

```python
def brute(n, target, used, total):
    if total >= target: return True
    for i in range(1, n+1):
        if i not in used:
            used.add(i)
            if not brute(n, target, used, total + i): return True
            used.remove(i)
    return False
```

* **Time Explosion**: This explores `n!` game trees – for n = 20, far beyond reasonable limits.  
* **Stack Overhead**: Deep recursion leads to stack overflow in many languages.  
* **Inefficiency**: The same state is revisited millions of times.

Thus, the “bad” approach is a useful teaching example but is unusable in a production interview.

---

### 5. The Ugly – Subtle Bugs & Edge‑Case Pitfalls

Even with the correct DP skeleton, some details can trip you up:

1. **Desired Total of 0 or Negative**  
   *The first player is already a winner.* Forgetting this check will let the algorithm run unnecessarily.

2. **Sum of All Numbers < Desired Total**  
   *Even if you take everything, you still can’t win.* A quick sum check prevents exploring impossible states.

3. **Bit‑Mask Size**  
   *Using a 32‑bit int is fine for n = 20, but be careful if the constraints change.* Using `long` or `unsigned long long` is safer for larger n.

4. **Memoization Key**  
   *Make sure the key is the mask itself, not a string or tuple.* In Python, use `@lru_cache` on an integer; in C++, use a vector or unordered_map indexed by the mask.

5. **Stack Overflow**  
   *Recursive depth can reach 20, which is safe, but in other languages (e.g., JavaScript) you might hit the recursion limit.* Consider an explicit stack if needed.

---

### 6. Real‑World Interview Tips

1. **Start with the Edge Checks** – Show the interviewer you’re thinking about pruning before brute force.  
2. **Explain the Bit‑Mask** – Even if you’re coding in Python, talk through how you would use bits to encode state.  
3. **Time/Space Complexity** – Mention the O(2ⁿ) factor and why it’s acceptable for n = 20.  
4. **Discuss Alternatives** – Briefly mention minimax with alpha‑beta pruning; but show that bit‑mask DP is cleaner here.  
5. **Show Up‑to‑Date Code** – Provide clean, tested snippets in the language of your choice.  

---

### 7. Takeaway

“Can I Win” is a classic interview problem that showcases:

* **Game theory** (minimax)  
* **Bit‑mask DP** (state compression)  
* **Practical optimization** (early exits)

A solid solution demonstrates an understanding of combinatorial search, dynamic programming, and careful handling of edge cases. By mastering this problem, you’ll be ready to tackle any interview question that asks “Can I win?” – literally or figuratively.

Happy coding, and good luck with your next job interview! 🚀

---

## 8. TL;DR – The One‑Page Summary

```text
Edge Cases:
  - desiredTotal <= 0 → true
  - sum(1..n) < desiredTotal → false

State:
  mask ∈ [0, 2ⁿ-1]  (bit i = 1 if number i+1 is still available)

DP:
  canWin(mask) = any move i:
      if i+1 >= desiredTotal: return true
      if !canWin(mask ^ (1<<i)): return true
  else return false

Complexity:
  Time  : O(2ⁿ · n)  (n <= 20 → < 20M ops)
  Memory: O(2ⁿ)     (≈ 1 MB)
```

All three code samples above implement this logic in Java, Python, and C++.