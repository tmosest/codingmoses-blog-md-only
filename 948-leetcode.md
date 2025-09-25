---
title: LeetCode 948. Bag of Tokens - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Bag of Tokens – Optimal Greedy Solution  

**Problem**  
You start with `power = P` and `score = 0`.  
You have a list `tokens` where `tokens[i]` is the “power cost” of token *i*.  
You may play any token at most once, in any order, in one of two ways

| action | requirement | effect |
|--------|-------------|--------|
| face‑up | `power ≥ tokens[i]` | `power -= tokens[i]`, `score += 1` |
| face‑down | `score ≥ 1` | `power += tokens[i]`, `score -= 1` |

Return the maximum score you can reach.

--------------------------------------------------------------------

### Why a greedy two‑pointer strategy works

* **Spend the cheapest token to gain a point**  
  When you have enough power to play a token face‑up, it is always best to use the *smallest* available token.  Any larger token would waste more power for the same +1 point.

* **Use a point to gain the most power**  
  When you cannot play any token face‑up, you can convert a point into power.  It is optimal to use the *largest* remaining token for this, because it gives the maximum possible power for a single -1 point.

If we repeatedly apply these two rules, we will

1. grow the score as long as power allows (using the cheapest tokens);
2. if we run out of power but still have points, we recover power with the most expensive token (losing a point);
3. stop when neither move is possible.

During the process we keep track of the maximum score seen – that is the answer.

--------------------------------------------------------------------

### Algorithm (two‑pointer greedy)

```
sort(tokens)                     # O(n log n)
l = 0                            # leftmost unused token
r = len(tokens)-1                # rightmost unused token
score = 0
maxScore = 0

while l <= r:
    if tokens[l] <= power:       # can play face‑up
        power -= tokens[l]
        l += 1
        score += 1
        maxScore = max(maxScore, score)
    elif score > 0:              # need to play face‑down
        power += tokens[r]
        r -= 1
        score -= 1
    else:                        # cannot play any more
        break

return maxScore
```

--------------------------------------------------------------------

### Correctness Proof

We prove that the algorithm returns the maximum achievable score.

---

#### Lemma 1  
If a token can be played face‑up, using the *smallest* remaining token is never worse than using any other token.

**Proof.**  
Playing any token face‑up consumes its value from power and gives +1 point.  
Let `x ≤ y` be two remaining tokens.  
After playing `x` we have at least as much power left as after playing `y` (`P - x ≥ P - y`).  
Both actions give the same +1 point, so using `x` can only leave us with more power, which can only help in future moves. ∎



#### Lemma 2  
If we must play a token face‑down (i.e. we have no power for any face‑up play but at least one point), using the *largest* remaining token is never worse.

**Proof.**  
Playing a token face‑down gives `+tokens[i]` power at the cost of `-1` point.  
Let `x ≤ y` be two remaining tokens.  
After playing `y` we have at least as much power as after playing `x` (`P + y ≥ P + x`).  
Both actions lose exactly one point, so using `y` can only leave us with more power, which can only help later. ∎



#### Lemma 3  
During the execution of the algorithm, at every step the set of tokens that have **not** been used is a suffix of the sorted array.

**Proof.**  
Initially no tokens are used.  
The algorithm only removes tokens in two ways:

* `tokens[l]` is removed by moving `l` forward.  
* `tokens[r]` is removed by moving `r` backward.

Thus the removed tokens are always contiguous from the beginning or from the end, leaving an untouched suffix `tokens[l … r]`. ∎



#### Lemma 4  
The algorithm never misses an optimal sequence of moves.

**Proof.**  
Assume an optimal sequence `S` that yields the maximum score.  
Consider the first step where `S` differs from the greedy algorithm.

*If `S` plays a face‑up token while greedy does not*:  
Greedy either has enough power (so it would also play) or it does not.  
If greedy cannot play any face‑up token, then the token in `S` must be *more expensive* than `tokens[l]` (by Lemma 1).  
Replacing that token in `S` with the cheaper `tokens[l]` cannot reduce the score and only increases remaining power, contradicting optimality.

*If `S` plays a face‑down token while greedy does not*:  
Greedy only fails to play face‑down when `score == 0`.  
Any face‑down move reduces the score to negative, which is never useful for reaching a higher final score, so an optimal solution would never play a face‑down token in that situation.

Hence, at every step the greedy choice is at least as good as any other, and no optimal solution can achieve a higher score than the greedy algorithm. ∎



#### Theorem  
`maxScore` returned by the algorithm equals the maximum possible score.

**Proof.**  
By Lemma 4 the greedy algorithm can be transformed into an optimal sequence that yields the same or higher score.  
The algorithm records the largest score seen during the transformation, so `maxScore` is at least the optimal score.  
Conversely, the algorithm never exceeds the true optimum because each step is consistent with the problem rules.  
Therefore, the algorithm’s output equals the optimum. ∎



--------------------------------------------------------------------

### Complexity Analysis

*Sorting* – `O(n log n)` time, `O(n)` space (Python’s sort uses Timsort).  
*Greedy loop* – each token is processed at most once, `O(n)` time, `O(1)` additional space.

Total: **Time `O(n log n)`, Space `O(1)`** (ignoring input storage).

--------------------------------------------------------------------

### Reference Implementation (Python 3)

```python
from typing import List

class Solution:
    def bagOfTokensScore(self, tokens: List[int], power: int) -> int:
        tokens.sort()                # ascending order
        l, r = 0, len(tokens) - 1    # two pointers
        score, best = 0, 0

        while l <= r:
            if tokens[l] <= power:          # play face‑up
                power -= tokens[l]
                l += 1
                score += 1
                best = max(best, score)
            elif score > 0:                 # play face‑down
                power += tokens[r]
                r -= 1
                score -= 1
            else:                           # no moves left
                break

        return best
```

The same logic can be written in C++/Java with identical two‑pointer structure; the code above follows the algorithm verbatim and contains explanatory comments.

--------------------------------------------------------------------

**Result:**  
The solution is optimal, simple to implement, and runs in the minimal possible time given the need to sort the input.