---
title: LeetCode 1406. Stone Game III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Stone Game III – Optimal DP Solution (Java / Python / C++)

Below are **clean, production‑ready implementations** for LeetCode problem **1406. Stone Game III**.  
All three solutions run in **O(n)** time and **O(1)** auxiliary space (the constant 3‑length DP array).

```java
// Java – LeetCode compatible
class Solution {
    public String stoneGameIII(int[] stoneValue) {
        int n = stoneValue.length;
        // dp[i % 3] stores the best net advantage (Alice – Bob) from index i
        int[] dp = new int[3];              // automatically 0-initialised

        for (int i = n - 1; i >= 0; i--) {
            int take1 = stoneValue[i] - dp[(i + 1) % 3];

            int take2 = Integer.MIN_VALUE;
            if (i + 1 < n) take2 = stoneValue[i] + stoneValue[i + 1] - dp[(i + 2) % 3];

            int take3 = Integer.MIN_VALUE;
            if (i + 2 < n) take3 = stoneValue[i] + stoneValue[i + 1] + stoneValue[i + 2] - dp[(i + 3) % 3];

            dp[i % 3] = Math.max(Math.max(take1, take2), take3);
        }

        int diff = dp[0];                   // Alice – Bob
        if (diff > 0)  return "Alice";
        if (diff < 0)  return "Bob";
        return "Tie";
    }
}
```

```python
# Python – LeetCode compatible
class Solution:
    def stoneGameIII(self, stoneValue: List[int]) -> str:
        n = len(stoneValue)
        dp = [0, 0, 0]                      # dp[i % 3] = best advantage from i

        for i in range(n - 1, -1, -1):
            take1 = stoneValue[i] - dp[(i + 1) % 3]

            take2 = float('-inf')
            if i + 1 < n:
                take2 = stoneValue[i] + stoneValue[i + 1] - dp[(i + 2) % 3]

            take3 = float('-inf')
            if i + 2 < n:
                take3 = stoneValue[i] + stoneValue[i + 1] + stoneValue[i + 2] - dp[(i + 3) % 3]

            dp[i % 3] = max(take1, take2, take3)

        diff = dp[0]                         # Alice – Bob
        if diff > 0:   return "Alice"
        if diff < 0:   return "Bob"
        return "Tie"
```

```cpp
// C++ – LeetCode compatible
class Solution {
public:
    string stoneGameIII(vector<int>& stoneValue) {
        int n = stoneValue.size();
        int dp[3] = {0, 0, 0};                 // dp[i % 3] = best advantage from i

        for (int i = n - 1; i >= 0; --i) {
            int take1 = stoneValue[i] - dp[(i + 1) % 3];

            int take2 = INT_MIN;
            if (i + 1 < n)
                take2 = stoneValue[i] + stoneValue[i + 1] - dp[(i + 2) % 3];

            int take3 = INT_MIN;
            if (i + 2 < n)
                take3 = stoneValue[i] + stoneValue[i + 1] + stoneValue[i + 2] - dp[(i + 3) % 3];

            dp[i % 3] = max({take1, take2, take3});
        }

        int diff = dp[0];                     // Alice – Bob
        if (diff > 0)  return "Alice";
        if (diff < 0)  return "Bob";
        return "Tie";
    }
};
```

---

# 📖 Blog Article: The Good, the Bad, and the Ugly of Stone Game III

> **Meta‑Description:**  
> Master LeetCode’s Stone Game III with a clear, optimal DP solution in Java, Python, and C++. Learn the good, bad, and ugly of this classic game‑theory problem and boost your interview readiness.

---

## 1. Introduction

Stone Game III is a **classic two‑player turn‑based puzzle** that appears on LeetCode as problem 1406. The game forces players to make decisions in a sequence of piles, and the winner is the one with the higher total stone value after all piles are taken.

If you’ve ever prepared for an interview, you’re probably familiar with similar “optimal‑strategy” questions: *What is the best move you can make, assuming the opponent is also playing optimally?*  
Stone Game III is one of those questions that can be solved elegantly with **dynamic programming** (DP). In this article, we’ll walk through:

- Why the problem is a **game‑theory DP** in disguise
- The **bottom‑up DP** solution that uses only a 3‑element array (`O(1)` space)
- Edge‑case pitfalls (negative stone values, odd lengths, etc.)
- The “good”, “bad”, and “ugly” parts of the solution
- How to use this knowledge to impress interviewers

---

## 2. Problem Recap (in your own words)

- You’re given an array `stoneValue` of length **n** (`1 ≤ n ≤ 5·10⁴`).  
- Alice starts; Bob follows; each turn, a player can take **1, 2, or 3** stones from the leftmost remaining pile.  
- Scores are accumulated per player as the sum of the values of stones taken.  
- Both play **optimally**.  
- Return `"Alice"`, `"Bob"`, or `"Tie"` depending on who ends up with the higher score.

---

## 3. Intuition: From Game Theory to DP

### 3.1. The “Net Advantage” Concept

Instead of tracking each player’s individual score, it’s simpler to track the **difference** (Alice’s score – Bob’s score).  
If we denote:

```
bestAdvantage(i) = maximum (AliceScore - BobScore) achievable
                   if the game starts at index i
```

then the answer is:

```
if bestAdvantage(0) > 0 -> Alice wins
if bestAdvantage(0) < 0 -> Bob  wins
else                       Tie
```

### 3.2. Recursive Relation

From index `i` a player can take:

1. **One stone** – the opponent starts at `i+1`.  
   The resulting advantage:  
   `stoneValue[i] - bestAdvantage(i+1)`

2. **Two stones** – if `i+1 < n`  
   `stoneValue[i] + stoneValue[i+1] - bestAdvantage(i+2)`

3. **Three stones** – if `i+2 < n`  
   `stoneValue[i] + stoneValue[i+1] + stoneValue[i+2] - bestAdvantage(i+3)`

The player will pick the **maximum** among these three options because both play optimally.

### 3.3. Bottom‑Up DP

We compute `bestAdvantage` from right to left (`i = n-1 … 0`).  
Each step depends only on the next three states, so we can keep **only the last three values** instead of an entire `n`‑size array.  

> **Why 3 values?**  
> The recurrence uses `i+1`, `i+2`, and `i+3`.  
> A circular buffer of size 3 is enough.

This yields **O(n)** time and **O(1)** auxiliary space.

---

## 4. The Code: Three Languages

> Each implementation follows the same logic, just with language‑specific syntax and standard library calls.

*(Refer to the code blocks in the “Solutions” section above.)*

### 4.1. Why This is the “Good”

- **Simplicity**: Clear, one‑pass DP with only a few lines inside the loop.
- **Efficiency**: Handles the maximum input size (5·10⁴) comfortably.
- **Space‑Optimised**: Only 3 integers are stored, so memory usage is negligible.
- **Universally Accepted**: Works in Java, Python, and C++—great for coding interviews where language choice matters.

### 4.2. The “Bad”: Edge Cases & Misconceptions

| Pitfall | What can go wrong? | Fix |
|---------|-------------------|-----|
| **Negative values** | Assuming all stones are positive leads to wrong choices. | The DP recurrence already subtracts the opponent’s best advantage, so it naturally handles negatives. |
| **Using `int` overflow** | `stoneValue[i]` up to 1000, sum up to 3 000, but with many piles difference can reach ~5·10⁷ → still within 32‑bit. | Still safe in 32‑bit, but use 64‑bit if you’re paranoid. |
| **Wrong modulo index** | Off‑by‑one errors in `(i+1) % 3` vs `i % 3` can corrupt the buffer. | Stick to the pattern shown in the code: store result in `dp[i % 3]`. |
| **Mis‑reading the problem** | Confusing “take 1, 2, or 3” with “take at most 3” (same thing, but some solvers think you must always take 3). | Explicitly check boundaries: `if (i + 1 < n)` etc. |

### 4.3. The “Ugly”: Naïve Recursion & TLE

A typical beginner approach:

```python
def helper(i):
    if i >= n: return 0
    best = -inf
    sum = 0
    for k in range(1,4):
        if i + k <= n:
            sum += stoneValue[i + k - 1]
            best = max(best, sum - helper(i + k))
    return best
```

- **Time Complexity**: Exponential (`O(3^n)`) due to recomputation of the same states.
- **Stack Overflow**: For large `n`, recursion depth > 10⁴ blows the stack.
- **Result**: LeetCode reports **Time Limit Exceeded** (TLE) for even modest inputs.

The ugly part is the **inadequate memoisation**: if you only memoise the best outcome but not the difference, you lose the opponent’s perspective and end up with incorrect logic.

---

## 5. A Quick Proof of Correctness

**Claim:** The bottom‑up DP described above returns the exact optimal winner.

**Proof Sketch:**

1. **Base Cases**: For `i ≥ n`, `bestAdvantage(i) = 0` (no stones left). Correct.
2. **Inductive Step**: Assume we know `bestAdvantage(i+1)`, `bestAdvantage(i+2)`, `bestAdvantage(i+3)` correctly.  
   From index `i`, the player chooses among the 3 allowed moves.  
   By subtracting the opponent’s `bestAdvantage`, we compute the net advantage for the current player.  
   Taking the **maximum** ensures optimality against a rational opponent.  
   Hence, `bestAdvantage(i)` is correct.
3. **Termination**: We fill all indices down to 0, so `bestAdvantage(0)` is correct.  
4. **Winner Determination**: The sign of `bestAdvantage(0)` matches the higher score. ∎

---

## 6. How to Use This in an Interview

1. **Explain the “net advantage” idea first**: Show that you’re not just coding, you’re *understanding* the problem.
2. **Draw the recurrence on a whiteboard**: Even if you’re writing code later, the visual helps interviewers follow your reasoning.
3. **Show the 3‑element buffer trick**: Many interviewers love it because it demonstrates knowledge of **space optimisation**.
4. **Mention the edge‑case table**: A quick glance at the table reveals you’ve thought about pitfalls.
5. **Wrap up with “What if the rules changed?”**: E.g., if you could take up to 4 stones, you’d just increase the buffer size. This flexibility impresses.

---

## 7. Conclusion

Stone Game III may look like a simple game, but it’s a textbook example of **optimal‑strategy dynamic programming**. The key is to think in terms of *score difference*, write a clean recurrence, and implement a space‑optimised bottom‑up DP.

**Takeaway for interviews:**

- Talk about *net advantage* before coding.  
- Use a circular buffer for `O(1)` space.  
- Highlight that negatives are automatically handled.  
- Avoid the naive recursion; instead, explain why your DP is optimal.

Feel confident: you can tackle Stone Game III (and similar problems) with a concise, proven solution in any of the top languages. Good luck smashing that next coding interview! 🚀

---

## 8. References & Further Reading

- LeetCode problem 1406 – [Stone Game III](https://leetcode.com/problems/stone-game-iii/)
- “Competitive Programming 4” – Chapter on **Game Theory DP**  
- “Dynamic Programming – A Simple Introduction” – GeeksforGeeks  
- “Interview Warm‑Up: DP Problems” – Pramp’s free practice platform

---


--- 

> **Want more deep dives?**  
> Subscribe to our newsletter for weekly interview‑prep challenges, and let’s ace those hard DP questions together.