---
title: LeetCode 3222. Find the Winning Player in Coin Game - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Solution Overview

| Language | Time | Space | Code |
|----------|------|-------|------|
| **Java** | `O(1)` | `O(1)` | ```java<br>public class Solution {<br>    public String winningPlayer(int x, int y) {<br>        int turns = Math.min(x, y / 4);<br>        return (turns % 2 == 1) ? "Alice" : "Bob";<br>    }<br>}<br>``` |
| **Python** | `O(1)` | `O(1)` | ```python<br>class Solution:<br>    def winningPlayer(self, x: int, y: int) -> str:<br>        turns = min(x, y // 4)<br>        return "Alice" if turns % 2 == 1 else "Bob"<br>``` |
| **C++** | `O(1)` | `O(1)` | ```cpp<br>#include <bits/stdc++.h><br>using namespace std;<br><br>class Solution {<br>public:<br>    string winningPlayer(int x, int y) {<br>        int turns = min(x, y / 4);<br>        return (turns % 2) ? "Alice" : "Bob";<br>    }<br>};<br>``` |

> **Why it works**  
> The only way to reach a total value of 115 is **1 coin of 75 + 4 coins of 10**.  
> Therefore each turn consumes exactly one 75‑coin and four 10‑coins.  
> The maximum number of turns is limited by whichever resource runs out first:  
> `turns = min(x, y/4)`.  
> If `turns` is odd, Alice makes the last valid move and wins; if even, Bob wins.

---

## 2. Iterative / Recursive Variants

### Iterative (C++/Java/Python)

```cpp
int turns = 0;
while (x > 0 && y >= 4) {
    x--;  y -= 4;  turns++;
}
return turns % 2 ? "Alice" : "Bob";
```

### Recursive (Java)

```java
String helper(int x, int y) {
    if (x == 0 || y < 4) return "Bob";
    return helper(x - 1, y - 4);   // same player, parity changes automatically
}
```

> The recursive version is just a tidy way of saying “repeat the same operation until the game ends”.  
> It’s less efficient because of function‑call overhead, but still runs in `O(min(x, y/4))` time and `O(1)` space (after tail‑call optimization).

---

## 3. Corner Cases & Constraints

| Case | Reason | Result |
|------|--------|--------|
| `x = 0` or `y < 4` | No valid move exists | Bob wins (Alice cannot move) |
| `x = 1, y = 4` | Exactly one turn | Alice wins |
| `x = 100, y = 100` | Many turns | Determined by parity of `min(100, 25) = 25` → Alice |

*Constraints:* `1 ≤ x, y ≤ 100`.  
With these bounds, the simple O(1) formula is already optimal; no simulation is necessary.

---

## 4. Blog Article – “The Good, The Bad, and The Ugly of the Coin Game”

### 4.1 Introduction (SEO‑friendly)

> **LeetCode #3222 – Find the Winning Player in Coin Game**  
> A deceptively simple puzzle that tests your understanding of game theory, parity, and greedy reasoning.  
> In this post we’ll walk through the intuitive “good” solution, highlight the “bad” pitfalls that lead to timeouts, and warn about the “ugly” pitfalls of over‑engineering the answer.  
> We’ll provide Java, Python, and C++ implementations, explain the math behind the O(1) solution, and give you a concise cheat‑sheet you can copy‑paste into an interview.

---

### 4.2 Problem Restatement (SEO)

> Two players, Alice and Bob, play alternately.  
> Each turn they must take coins that total 115 in value.  
> Coins are of two denominations: **75** and **10**.  
> The game ends when a player cannot make a legal move; that player loses.  
> Determine the winner assuming optimal play.

---

### 4.3 Intuition: The “Good” Part

1. **Identify the only valid combination**  
   `75 + 4×10 = 115`.  
   No other combination of 75‑ and 10‑coins can sum to 115.  
   **Good** – reduces the game to a single move type.

2. **Count the limiting resource**  
   Each turn consumes **1** 75‑coin and **4** 10‑coins.  
   The game can last at most  
   ```text
   turns = min(x, y/4)
   ```
   because the resource that runs out first stops the game.

3. **Parity decides the winner**  
   - If `turns` is **odd**, Alice (first player) makes the last valid move → **Alice wins**.  
   - If `turns` is **even**, Bob makes the last move → **Bob wins**.  
   This is the classic “take‑turns‑until‑no‑moves‑left” game theory result.

4. **O(1) solution**  
   ```java
   int turns = Math.min(x, y / 4);
   return (turns % 2 == 1) ? "Alice" : "Bob";
   ```

---

### 4.4 “Bad” Mistakes That Lead to Timeouts

| Mistake | Why it’s bad | Remedy |
|---------|--------------|--------|
| **Simulating every turn** (while‑loop or recursion) | With `x=100, y=100` you’ll do ~25 iterations; still fine, but LeetCode’s hidden test harness can use larger inputs in custom tests. | Use the O(1) arithmetic formula instead of loops. |
| **Using division on integers incorrectly** (`y / 4.0`) | Forces a floating‑point operation and can introduce bugs. | Use integer division (`y / 4` or `y // 4`) – guaranteed to truncate to floor. |
| **Ignoring the “no‑moves‑left” base case** | Many people write `min(x, y/4)` blindly and forget that if `y < 4` or `x == 0` Alice can’t move. | Add an explicit guard: if `turns == 0` return `"Bob"`. |
| **Premature optimization** | Over‑optimizing with bit‑manipulation or custom classes when the built‑in `min()` is already fast. | Keep it simple – readability matters in interviews. |

---

### 4.5 “Ugly” Pitfalls: Over‑Engineering

| Over‑engineering | Consequence |
|-----------------|-------------|
| **Complex recursion with memoisation** | Adds O(n²) memory; unnecessary because the state space is just `(x, y)` pairs. |
| **State‑space DP (`dp[x][y]`)** | 10 k entries at worst; still O(n²) time, far slower than O(1). |
| **Custom stack or queue simulation** | Adds overhead and complicates the code you need to explain on the spot. |
| **Using `BigInteger` or arbitrary precision types** | For `x, y ≤ 100` this is overkill; increases run‑time and code size. |

---

### 4.6 Complexity Recap

| Approach | Time | Space |
|----------|------|-------|
| **O(1) arithmetic** | `O(1)` | `O(1)` |
| **Iterative simulation** | `O(min(x, y/4))` | `O(1)` |
| **Recursive** | `O(min(x, y/4))` | `O(1)` (tail‑call) |

> *Tip for interviewers:* When asked for the fastest solution, mention the O(1) formula and justify it with the parity argument. That shows you understand the underlying theory, not just the implementation.

---

### 4.7 Practical Interview Cheat‑Sheet

```java
// Java 17+
class Solution {
    public String winningPlayer(int x, int y) {
        int turns = Math.min(x, y / 4);
        return (turns % 2 == 1) ? "Alice" : "Bob";
    }
}
```

```python
# Python 3
class Solution:
    def winningPlayer(self, x: int, y: int) -> str:
        turns = min(x, y // 4)
        return "Alice" if turns % 2 == 1 else "Bob"
```

```cpp
// C++ 17
class Solution {
public:
    string winningPlayer(int x, int y) {
        int turns = min(x, y / 4);
        return (turns % 2) ? "Alice" : "Bob";
    }
};
```

> Copy‑paste the snippet that matches the language you’re most comfortable with.  

---

### 4.8 Why This Problem Is a Good Interview Question

1. **Game Theory 101** – shows you can reason about first‑player advantage and parity.  
2. **O(1) Mindset** – many candidates write loops or DP; pointing out the constant‑time shortcut impresses interviewers.  
3. **Clear Communication** – the solution can be explained in 3 sentences; great for “walk‑through” interview style.  
4. **Multilingual** – having Java, Python, and C++ versions ready demonstrates versatility in tech stacks.

---

### 4.9 SEO Keywords & Meta Tags

- **Meta Title:** LeetCode Coin Game – O(1) Winning Player Solution (Java/Python/C++)
- **Meta Description:** Solve LeetCode #3222 in constant time. Learn the game‑theory intuition, iterative and recursive variants, and a copy‑paste cheat‑sheet for Java, Python, and C++.
- **Keywords:** leetcode coin game, find winning player, optimal play, parity game, O(1) solution, interview coding problem, Java Python C++ solutions, job interview coding, algorithm cheat sheet.

> *Tip:* If you’re preparing for a coding interview, remember to talk about “why” before you show the code. Recruiters love candidates who explain their reasoning.

---

### 4.10 Final Thoughts

- **Good:** One‑line O(1) solution that uses parity.  
- **Bad:** Trying to simulate every move or build a huge DP table.  
- **Ugly:** Over‑engineering with unnecessary data structures or misreading the constraints.

With the code snippets above and the quick‑reference cheat‑sheet, you’ll be ready to ace this LeetCode problem—and impress your next hiring manager. Happy coding!