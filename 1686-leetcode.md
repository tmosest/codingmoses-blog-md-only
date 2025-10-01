---
title: LeetCode 1686. Stone Game VI - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 1686. Stone Game VI – Solutions in **Java**, **Python**, and **C++**

> **Problem** –  
> Alice and Bob play a game with `n` stones.  
> Each stone `i` has a value `aliceValues[i]` for Alice and `bobValues[i]` for Bob.  
> Alice starts, then they alternate turns. On a turn a player picks an unused stone, earns their value for that stone, and the stone disappears.  
> Both play optimally.  
> Return **1** if Alice wins, **-1** if Bob wins, and **0** for a draw.

---

### 1️⃣ Optimal Idea

If we treat a stone as giving Alice `+a` points *and* Bob `-b` points (i.e. the *difference* if Alice takes it), the total effect on the game‑score difference is `a + b`.  
Therefore:

1. **Sort stones by** `aliceValues[i] + bobValues[i]` **descending** – this is the greedy order that guarantees the best possible outcome for the current player.
2. Alice takes the 1st, 3rd, 5th … stones; Bob takes the 2nd, 4th, 6th … stones.
3. Sum Alice’s points and Bob’s points separately, then compare.

This runs in `O(n log n)` time and `O(1)` extra memory (besides the array for sorting).

---

## ⚙️ Code Implementations

### Java

```java
import java.util.Arrays;
import java.util.Comparator;

public class Solution {
    /** 
     * Stone Game VI – Greedy sorting by total value.
     * @param aliceValues Alice's values for each stone
     * @param bobValues   Bob's values for each stone
     * @return  1 if Alice wins, -1 if Bob wins, 0 for draw
     */
    public int stoneGameVI(int[] aliceValues, int[] bobValues) {
        int n = aliceValues.length;
        // Pair of (total, aliceScore, bobScore)
        int[][] pair = new int[n][3];
        for (int i = 0; i < n; i++) {
            pair[i][0] = aliceValues[i] + bobValues[i]; // total value
            pair[i][1] = aliceValues[i];
            pair[i][2] = bobValues[i];
        }

        // Sort by total value descending
        Arrays.sort(pair, Comparator.comparingInt(a -> -a[0]));

        long aliceScore = 0, bobScore = 0;
        for (int i = 0; i < n; i++) {
            if ((i & 1) == 0) {          // Alice's turn (0‑based)
                aliceScore += pair[i][1];
            } else {
                bobScore += pair[i][2];
            }
        }
        return Long.compare(aliceScore, bobScore);   // 1, 0, -1
    }
}
```

---

### Python

```python
class Solution:
    def stoneGameVI(self, aliceValues: list[int], bobValues: list[int]) -> int:
        """
        Sort stones by the sum of their values and simulate alternating turns.
        Returns 1 if Alice wins, -1 if Bob wins, 0 for a draw.
        """
        # Create a list of tuples: (total_value, alice_value, bob_value)
        stones = [(a + b, a, b) for a, b in zip(aliceValues, bobValues)]
        stones.sort(reverse=True, key=lambda x: x[0])   # sort by total descending

        alice, bob = 0, 0
        for i, (_, a_val, b_val) in enumerate(stones):
            if i % 2 == 0:            # Alice's turn
                alice += a_val
            else:                     # Bob's turn
                bob += b_val

        return (alice > bob) - (alice < bob)   # 1, 0, or -1
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int stoneGameVI(vector<int>& aliceValues, vector<int>& bobValues) {
        int n = aliceValues.size();
        // Vector of tuples: (total, alice, bob)
        vector<tuple<int,int,int>> stones;
        stones.reserve(n);
        for (int i = 0; i < n; ++i)
            stones.emplace_back(aliceValues[i] + bobValues[i], aliceValues[i], bobValues[i]);

        sort(stones.begin(), stones.end(),
             [](const auto& x, const auto& y) { return get<0>(x) > get<0>(y); });

        long long alice = 0, bob = 0;
        for (int i = 0; i < n; ++i) {
            if (i % 2 == 0)          // Alice's turn
                alice += get<1>(stones[i]);
            else
                bob += get<2>(stones[i]);
        }
        return (alice > bob) - (alice < bob); // 1, 0, -1
    }
};
```

---

## 📝 Blog Article – “Stone Game VI: The Good, the Bad, and the Ugly”

> **SEO Keywords**: *Stone Game VI, LeetCode 1686, optimal strategy, greedy sorting, algorithmic interview, coding challenge, job interview, algorithm analysis, greedy algorithm, competitive programming, dynamic programming, game theory*

---

### Introduction

If you’re prepping for coding interviews, *Stone Game VI* (LeetCode 1686) is a classic that tests not only your coding skills but also your ability to reason about optimal play in a two‑player game. The problem is deceptively simple: pick the “right” stone each turn so that your final score is the highest. But the solution hinges on a clever transformation that turns the game into a greedy sorting problem.

Below, we dissect the **good**, **bad**, and **ugly** aspects of this problem, explain why the greedy solution works, and show you how to ace it in an interview.

---

### The Good – Why It’s a Great Interview Question

| ✅ Feature | Why It Matters |
|-----------|----------------|
| **Small Input** | `n ≤ 10⁵` keeps the solution within time limits while challenging your algorithmic thinking. |
| **Clear Optimality** | The greedy order `a + b` can be proven optimal; it gives you a clean, linear‑time strategy after sorting. |
| **Game‑Theory Insight** | It forces candidates to model the problem as a two‑player zero‑sum game, a valuable skill for many algorithmic contests. |
| **Code‑Ready** | The solution is just sorting + a loop, which makes it an excellent “write‑and‑run” exercise during interviews. |

### The Bad – Where It Can Trip You Up

1. **Misunderstanding the Objective** – Some candidates think “pick the stone with the highest `a`” or “highest `b`”; that’s wrong.
2. **Ignoring Bob’s Perspective** – Failing to consider that Bob’s score *negatively* affects Alice’s advantage can lead to a suboptimal heuristic.
3. **Sorting Mistakes** – Sorting on `a + b` is essential; sorting on `a` or `b` alone breaks the strategy.

### The Ugly – Common Pitfalls and Edge Cases

| 🚨 Pitfall | Consequence | Fix |
|------------|-------------|-----|
| **Using `int` overflow** | Scores can reach `n * 100`, still fits in 32‑bit, but be cautious if the constraints change. | Use `long`/`long long` for safety. |
| **Stability of Sort** | Not a problem; only the relative order of equal sums matters, but any order is fine. | No action needed. |
| **Large `n` with O(n²)** | Time limit exceeded. | Always implement `O(n log n)` sorting; avoid nested loops. |
| **Wrong turn assignment** | Swapping Alice/Bob turns flips the outcome. | Use `i % 2 == 0` for Alice, else Bob (or vice‑versa). |

---

### The Core Insight – From Game Theory to Greedy

Let’s formalize the trick:

- If Alice takes stone `i`, she gains `a[i]` points.
- If Bob takes stone `i`, he gains `b[i]` points.
- Suppose we **pretend** that Alice takes the stone and we **subtract** Bob’s potential gain from Alice’s score: the *difference* becomes `a[i] + b[i]`.

When players alternate turns, Alice wants to maximize the **difference** between her score and Bob’s. Therefore, on each turn, the optimal move is to pick the remaining stone with the **largest `a + b`**.

Once all stones are sorted in this way, the game becomes deterministic: Alice takes every odd‑index stone, Bob every even‑index stone. The final comparison of their sums gives the winner.

---

### Complexity Analysis

| Operation | Complexity |
|-----------|------------|
| Build pairs | `O(n)` |
| Sort by `a+b` | `O(n log n)` |
| Simulate turns | `O(n)` |
| **Total** | **`O(n log n)` time, `O(1)` extra space** |

---

### How to Nail It in an Interview

1. **State the Intuition** – “We’re looking at the difference Alice can enforce; each stone’s value to the difference is `a+b`.”
2. **Show the Greedy Proof** – Briefly argue why picking the largest `a+b` is always optimal (you can mention the standard minimax argument or refer to the official editorial).
3. **Implement Efficiently** – Use an array of tuples or custom structs. Sort once, then loop.
4. **Handle Edge Cases** – Discuss the possibility of a draw and how the comparison works.

---

### Final Thoughts

Stone Game VI elegantly demonstrates how a two‑player game can be reduced to a simple greedy algorithm when the right transformation is spotted. Mastering this problem gives you:

- Confidence in game‑theory reasoning.
- A solid, interview‑friendly solution.
- Experience applying *difference‑based* thinking in other contexts (e.g., “Pick‑the‑Maximum‑Profit” problems).

So the next time you encounter Stone Game VI, remember the **good** (great interview style), avoid the **bad** (objective misinterpretation), and steer clear of the **ugly** (common bugs). You’ll be ready to present a clean, optimal solution that impresses any interviewer.

Happy coding—and good luck landing that dream job! 🚀

--- 

**Disclaimer:** The editorial proof is available on LeetCode; if you need a deeper mathematical justification, review the minimax proof that the greedy order satisfies the optimality condition. 

--- 

**Happy Interviewing!**