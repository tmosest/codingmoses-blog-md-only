---
title: LeetCode 1686. Stone Game VI - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – LeetCode 1686: Stone Game VI

> **Alice** and **Bob** take turns picking one stone from a pile of `n` stones.  
> Each stone *i* has two values:
> * `aliceValues[i]` – points Alice receives if she takes the stone
> * `bobValues[i]` – points Bob receives if he takes the stone  
> Both players play **optimally** and know each other’s value lists.  
> After all stones are taken, whoever has the larger total score wins  
> (`1` for Alice, `-1` for Bob, `0` for a tie).

Constraints  
```
1 ≤ n ≤ 10⁵
1 ≤ aliceValues[i], bobValues[i] ≤ 100
```

---

## 2.  Intuition – Why “Sort by a+b”?

Think of the *difference* between the two players’ final scores.  
If Alice takes stone `i` she gains `aliceValues[i]`.  
If Bob takes the same stone later he would have gained `bobValues[i]`.  

Instead of looking at the two values separately, observe that **the choice that maximizes the *difference* `alice - bob` is equivalent to maximizing the sum `alice + bob`**:

```
Alice takes stone i  →  +aliceValues[i] for Alice
Bob takes stone i     →  -bobValues[i] for Alice   (because Bob’s points are not Alice’s)

Difference impact = aliceValues[i] + bobValues[i]
```

Therefore, at every turn the player whose turn it is should grab the remaining stone with the largest **combined value** `alice + bob`.  
Once the stones are sorted by that key, players simply alternate picks:

* turn 0 (Alice) → take the first stone in the sorted order
* turn 1 (Bob)  → take the second stone
* … and so on.

Because each turn removes the *best* remaining stone for the current player, this greedy strategy is optimal.

---

## 3.  Algorithm

1. Create a list of `n` triples `(alice, bob, sum = alice + bob)`.
2. Sort the list in **descending** order of `sum`.
3. Simulate the game:  
   * For even indices (`0,2,4,…`) add `alice` to Alice’s score.  
   * For odd indices (`1,3,5,…`) add `bob` to Bob’s score.
4. Compare the two totals and return `1`, `-1`, or `0`.

Time complexity: **O(n log n)** (sorting dominates).  
Space complexity: **O(n)** for the auxiliary list.

---

## 4.  Reference Implementations  

### 4.1 Java

```java
import java.util.*;

public class Solution {
    private static class Stone {
        int alice, bob, sum;
        Stone(int alice, int bob) {
            this.alice = alice;
            this.bob = bob;
            this.sum = alice + bob;
        }
    }

    public int stoneGameVI(int[] aliceValues, int[] bobValues) {
        int n = aliceValues.length;
        Stone[] stones = new Stone[n];
        for (int i = 0; i < n; i++) {
            stones[i] = new Stone(aliceValues[i], bobValues[i]);
        }

        Arrays.sort(stones, (s1, s2) -> Integer.compare(s2.sum, s1.sum));

        long aliceScore = 0, bobScore = 0;
        for (int i = 0; i < n; i++) {
            if ((i & 1) == 0) {            // Alice's turn
                aliceScore += stones[i].alice;
            } else {                       // Bob's turn
                bobScore += stones[i].bob;
            }
        }

        return Long.compare(aliceScore, bobScore);
    }
}
```

> **Why `long`?**  
> Scores can be up to `n * 100` (`10⁷`), safely inside `int`, but using `long` removes any risk of overflow when the compiler optimizes.

---

### 4.2 Python

```python
class Solution:
    def stoneGameVI(self, aliceValues: List[int], bobValues: List[int]) -> int:
        # Build list of (sum, alice, bob)
        stones = sorted(
            [(a + b, a, b) for a, b in zip(aliceValues, bobValues)],
            reverse=True
        )

        alice, bob = 0, 0
        for i, (_, a, b) in enumerate(stones):
            if i % 2 == 0:
                alice += a
            else:
                bob += b

        return (alice > bob) - (alice < bob)   # 1 / -1 / 0
```

> The expression `(alice > bob) - (alice < bob)` is a concise way to return `1`, `-1`, or `0`.

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int stoneGameVI(vector<int>& aliceValues, vector<int>& bobValues) {
        int n = aliceValues.size();
        vector<tuple<int,int,int>> stones;          // (sum, alice, bob)
        stones.reserve(n);
        for (int i = 0; i < n; ++i)
            stones.emplace_back(aliceValues[i] + bobValues[i],
                                 aliceValues[i], bobValues[i]);

        sort(stones.begin(), stones.end(),
             [](const auto& x, const auto& y) {
                 return get<0>(x) > get<0>(y);    // descending by sum
             });

        long long alice = 0, bob = 0;
        for (int i = 0; i < n; ++i) {
            if (i % 2 == 0)  // Alice's turn
                alice += get<1>(stones[i]);
            else            // Bob's turn
                bob += get<2>(stones[i]);
        }

        if (alice > bob) return 1;
        if (alice < bob) return -1;
        return 0;
    }
};
```

---

## 5.  Blog Article – “The Good, The Bad, and The Ugly of Stone Game VI”

> **Keywords:** Stone Game VI, LeetCode 1686, greedy algorithm, sorting, game theory, interview coding, algorithmic thinking, time complexity, space complexity, job interview, coding interview prep

---

### 5.1 Introduction

In coding interviews, problems that blend game theory with classic data‑structures are common. **Stone Game VI** is one such problem. While its statement may seem intimidating, the optimal solution is surprisingly simple: *sort by the sum of the two players’ values and play greedily*. This article walks through that insight, the pitfalls you might encounter, and why this approach is a great showcase for your interview portfolio.

---

### 5.2 The Good – What Works and Why

| What | Why It Works |
|------|--------------|
| **Sorting by `alice + bob`** | The combined value of a stone is the exact advantage it gives to the current player over the opponent. Picking the maximum `sum` maximizes the score differential at every turn. |
| **Alternating Turns** | After sorting, the game reduces to a deterministic sequence: Alice takes indices 0, 2, 4…; Bob takes 1, 3, 5…. No further decision making is required. |
| **O(n log n) Time** | Sorting dominates, but with `n ≤ 10⁵` the algorithm comfortably fits into the time limits on any platform. |
| **O(n) Extra Space** | We store a lightweight array of tuples (or structs). No heavy heap or recursion is needed. |
| **Clear Code** | The solution is a few lines long in any language, making it easy to explain on the spot. |

> **Takeaway:** When the problem reduces to a *global* optimal choice, a greedy strategy often beats more complex dynamic‑programming formulations.

---

### 5.3 The Bad – Common Mistakes & Edge Cases

| Mistake | Why It Breaks | Fix |
|---------|---------------|-----|
| **Sorting by `alice` or `bob` alone** | Ignores the opponent’s perspective. A stone that looks good for Alice may be terrible for Bob. | Sort by `alice + bob`. |
| **Using a min‑heap and always popping the smallest** | Inverts the objective; you’re giving away the best stones. | Use a max‑heap or sort descending. |
| **Assuming `int` is safe for scores** | While `int` can hold up to ~2 billion, it’s safer to use `long`/`long long` for sums to guard against future modifications. | Use 64‑bit integers for cumulative scores. |
| **Ignoring tie handling** | Returning a wrong value for a draw leads to a wrong verdict. | Explicitly compare scores: `scoreA > scoreB ? 1 : (scoreA < scoreB ? -1 : 0);` |
| **Off‑by‑one errors in turn simulation** | Mixing 0‑based vs 1‑based indices leads to swapped turns. | Use `(i & 1) == 0` for Alice’s turns if indices start at 0. |

> **Debugging Tip:** Print the sorted order and simulate the first few turns manually to confirm the alternation logic.

---

### 5.4 The Ugly – When Greedy Might Fail (and How to Spot It)

The greedy solution *always* works for Stone Game VI because the game is **zero‑sum** with perfect information and no hidden states. However, it’s easy to misinterpret the problem as a classic “pick the highest value” game (like Nim). If the stone values were dynamic (e.g., changing after each pick) or if the players had hidden information, a greedy strategy could be suboptimal.

**What to watch for:**

- **Dynamic stone values** – If picking a stone alters the `alice + bob` of others, you’d need a DP or minimax approach.
- **Incomplete information** – If one player doesn’t know the other’s values, you can’t compute the exact score differential.
- **Multiple moves per turn** – If a player can take more than one stone per turn, the alternation logic breaks.

When encountering such variations, start by enumerating a few states manually and checking if picking the locally best move can lead to a worse global outcome. This will quickly reveal whether a more sophisticated solution (DP, minimax, or game‑tree search) is required.

---

### 5.5 Why This Problem is a Must‑Have on Your Interview Playbook

1. **Demonstrates Game‑Theoretic Insight** – You’ll impress interviewers with your ability to see beyond the obvious and capture the opponent’s advantage.
2. **Language Agnosticism** – The same algorithm translates effortlessly into Java, Python, C++, Go, or even JavaScript. Show that you can code the same logic in the interview’s preferred language.
3. **Scales to Constraints** – Interviewers love solutions that handle edge constraints elegantly; our `O(n log n)` algorithm does just that.
4. **Easy to Communicate** – In 3–5 minutes you can outline the problem, the `a+b` trick, and the turn simulation, leaving the interviewee to focus on your reasoning rather than boilerplate coding.

> **Pro Tip:** In a live interview, explain the *score differential* idea first. Write down the combined value of a stone, and show that picking the largest `sum` maximizes the advantage. Then drop the code.

---

### 5.6 Closing Thoughts

Stone Game VI is a perfect showcase of *algorithmic elegance*. Its solution is:

1. **Conceptually neat** – One line sorting and a single pass.  
2. **Robust** – Handles all constraints, ties, and large sums.  
3. **Interview‑friendly** – Short, explainable, and language‑agnostic.

Mastering this problem equips you with a strong example of greedy thinking, game‑theoretic insight, and efficient coding practice—all essential skills for landing that coveted software‑engineering role.

---

### 5.7 Call to Action

- **Practice** the greedy approach in a sandbox (LeetCode, HackerRank, or your own compiler).
- **Share** your solution on GitHub with a concise README explaining the `alice + bob` trick.
- **Discuss** the pitfalls we highlighted during your next mock interview.

> A clean solution to Stone Game VI is a *code‑quality badge* that says: “I can solve complex problems with elegant, efficient algorithms.”

---

### 6.  FAQ

| Question | Answer |
|----------|--------|
| *Can I use a stable sort?* | Stability isn’t required, but it doesn’t hurt if you want deterministic output. |
| *What if `aliceValues[i] + bobValues[i]` is the same for multiple stones?* | Their relative order doesn’t matter, as all such stones yield the same score differential. |
| *Is there a linear‑time solution?* | With bounded values (≤100) you could use counting sort in **O(n + M)** where `M = 200`. However, the overhead of counting arrays can outweigh the benefit for `n = 10⁵`. |

---

## 6.  Final Thoughts

The “good, bad, and ugly” framework turns a seemingly tricky interview question into a learning moment. By understanding *why* sorting by the sum works, you avoid common pitfalls and present a clear, optimal solution that demonstrates strong algorithmic thinking.  

Happy coding—and may your next interview go as smoothly as a stone’s combined value! 🚀

--- 

*Prepared by: Your Friendly Algorithm Tutor*  
*Feel free to adapt the code snippets or article to suit your personal branding and portfolio.*