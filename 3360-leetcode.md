---
title: LeetCode 3360. Stone Removal Game - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📦 3360. Stone Removal Game – Code, Theory & Interview‑Ready Blog Post  
*(Java | Python | C++)*  

---

### TL;DR  
- **Problem**: Two players alternately remove stones from a pile.  
  *Alice* starts by removing **exactly 10** stones.  
  Every subsequent move removes **one stone less** than the opponent’s last move.  
  Whoever can’t move loses.  
- **Goal**: Return `true` if Alice wins for a given `n`, otherwise `false`.  
- **Observation**: The game is completely deterministic – just simulate the decreasing sequence `10, 9, 8, …`.  
- **Complexity**: `O(k)` where `k ≤ 10` (since the first move is 10).  
- **Implementation**: Straight‑forward loop in Java, Python, and C++.

> **Pro tip**: The pattern is “first move 10, then 9, …”.  
> After each subtraction, flip the player.  
> When you can’t subtract, the current player loses, so the *other* player wins.

---

## 1️⃣ Problem Statement

> **Stone Removal Game**  
> *Alice and Bob play a game with a pile of `n` stones.*  
> 1. Alice starts, removing **exactly 10** stones.  
> 2. On each subsequent turn a player removes **1 stone less** than the opponent’s last move.  
> 3. If a player cannot make a move (i.e., the remaining stones are less than the required removal), they lose.  
> **Return** `true` if Alice wins, otherwise `false`.

> **Constraints**: `1 ≤ n ≤ 50`  
> **Example**  
> `n = 12` → Alice takes 10 → 2 stones left → Bob needs 9 → can't → **Alice wins**.

---

## 2️⃣ Intuition & “Good, Bad, Ugly” Breakdown

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Simplicity** | O(1) loop with at most 10 iterations. | None. | — |
| **Deterministic** | No branching, no recursion. | None. | — |
| **Edge Cases** | Handles `n < 10` automatically. | None. | — |
| **Readability** | Clear variable names (`stonesToRemove`, `turn`). | None. | — |
| **Scalability** | Works for any upper bound because loop length ≤ 10. | If the first move changed to a variable `k`, still O(k). | — |

> **Why it’s “good”** – the greedy simulation is literally the rule set; no clever math tricks required.  
> **Why it’s “bad”** – some might try over‑engineering (dynamic programming, memoization) when a one‑liner loop suffices.  
> **Why it’s “ugly”** – the only trick is remembering to flip the player after each turn; a tiny slip will give the wrong answer.

---

## 3️⃣ Algorithm (Pseudocode)

```
turn = 0            // 0 → Alice, 1 → Bob
stonesToRemove = 10

while n >= stonesToRemove:
    n -= stonesToRemove
    stonesToRemove -= 1
    turn = 1 - turn   // switch player

return turn == 1      // if last turn was Bob → Alice wins
```

The loop stops when the current player cannot make the required move.  
`turn` indicates the *player who just moved*.  
If the loop ends because the *next* player cannot move, the winner is the one who just moved (`turn`).

---

## 4️⃣ Code Implementations

### Java

```java
class Solution {
    public boolean canAliceWin(int n) {
        int turn = 0;          // 0 = Alice, 1 = Bob
        int stonesToRemove = 10;

        while (n >= stonesToRemove) {
            n -= stonesToRemove;
            stonesToRemove--;
            turn = 1 - turn;  // switch player
        }
        return turn == 1;     // Bob just moved, Alice wins
    }
}
```

### Python

```python
class Solution:
    def canAliceWin(self, n: int) -> bool:
        turn = 0          # 0 = Alice, 1 = Bob
        stones_to_remove = 10

        while n >= stones_to_remove:
            n -= stones_to_remove
            stones_to_remove -= 1
            turn ^= 1     # flip 0/1

        return turn == 1
```

### C++

```cpp
class Solution {
public:
    bool canAliceWin(int n) {
        int turn = 0;          // 0 = Alice, 1 = Bob
        int stonesToRemove = 10;

        while (n >= stonesToRemove) {
            n -= stonesToRemove;
            --stonesToRemove;
            turn ^= 1;         // toggle 0/1
        }
        return turn == 1;      // Bob just moved → Alice wins
    }
};
```

All three solutions run in **O(1)** time and **O(1)** space.  
The loop executes at most 10 iterations (for `n = 50`), so the runtime is essentially constant.

---

## 5️⃣ Complexity Analysis

| Complexity | Reason |
|------------|--------|
| **Time**   | At most 10 iterations → **O(1)** |
| **Space**  | Constant auxiliary variables → **O(1)** |

Even with a higher upper bound on `n`, the loop would still run at most `min(10, n)` times because the removal count decreases by one each turn.

---

## 6️⃣ Testing & Edge Cases

| `n` | Expected | Explanation |
|-----|----------|-------------|
| 1   | `false`  | Alice cannot remove 10 stones. |
| 10  | `true`   | Alice takes all stones, Bob loses immediately. |
| 11  | `false`  | Alice takes 10 → 1 left → Bob needs 9 → loses. |
| 12  | `true`   | Example from statement. |
| 50  | `false`  | Full simulation: Alice (10), Bob (9), Alice (8), … until Bob cannot move. |

Run a quick unit test in your language of choice to verify the above cases.

---

## 7️⃣ Interview‑Ready Tips

| Tip | Why It Helps |
|-----|--------------|
| **Explain the rule** before coding. | Shows you understand the problem constraints. |
| **State the invariant**: “The remaining stones are `n`, next removal is `k`.” | Keeps the solution clean. |
| **Avoid DP**. | Interviewers appreciate the minimal‑work solution. |
| **Mention edge case** (`n < 10`). | Demonstrates thoroughness. |
| **Show the winner logic** (`turn == 1`). | Clarifies who actually wins when the loop ends. |

---

## 8️⃣ SEO‑Optimized Conclusion

If you’re prepping for coding interviews, mastering *LeetCode 3360 – Stone Removal Game* proves you can handle greedy simulations, simple loops, and edge‑case analysis—all essentials for technical hiring.  

**Key takeaways**  
- The game is deterministic; a straight‑forward loop suffices.  
- Complexity is O(1) with at most 10 iterations.  
- Implement cleanly in **Java, Python, and C++** for language‑agnostic confidence.  

Ready to ace your next interview? Show this solution to recruiters, add it to your GitHub, and mention it in your résumé under “Algorithms & Problem Solving”.  

---

### SEO Keywords

- LeetCode 3360
- Stone Removal Game
- Coding interview
- Java solution
- Python solution
- C++ solution
- Algorithm interview questions
- Game theory problem
- Interview prep
- Technical hiring tips

Happy coding—and may the job interview odds be in your favor!