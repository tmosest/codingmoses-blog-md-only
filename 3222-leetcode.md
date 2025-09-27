---
title: LeetCode 3222. Find the Winning Player in Coin Game - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem – 3222. Find the Winning Player in Coin Game  
**LeetCode : https://leetcode.com/problems/find-the-winning-player-in-coin-game/**  

> You’re given two positive integers `x` (number of 75‑value coins) and `y` (number of 10‑value coins).  
> Alice and Bob play a turn‑based game starting with Alice. In each turn a player **must** take coins that total **115**.  
> If a player cannot make a move they lose.  
> Return the name of the winner if both play optimally.

### 📌 Key Observations

| What a move looks like | Reason |
|------------------------|--------|
| `1 × 75 + 4 × 10 = 115` | 75 + 40 = 115. No other combination of 75‑ and 10‑coins sums to 115. |
| A turn consumes **one** 75‑coin **and** **four** 10‑coins | Must have both resources. |
| Number of possible turns = `min(x, y/4)` | The limiting resource runs out first. |

Once the number of turns `t` is known, the game is simply a normal Nim‑style play:

* If `t` is odd → **Alice** makes the last move → **Bob** loses → Alice wins.  
* If `t` is even → Bob makes the last move → Alice loses → Bob wins.

Thus the solution is just a constant‑time arithmetic formula.

---

## 🚀 Code Solutions

Below are ready‑to‑paste, fully‑working implementations in **Java, Python, and C++**.

> All three use the same O(1) time, O(1) space algorithm:  
> `return (Math.min(x, y/4) % 2 == 1) ? "Alice" : "Bob";`

---

### Java

```java
// File: Solution.java
class Solution {
    public String winningPlayer(int x, int y) {
        // Each turn uses 1 coin of 75 and 4 coins of 10
        int turns = Math.min(x, y / 4);
        return (turns % 2 == 1) ? "Alice" : "Bob";
    }
}
```

*Complexity* – **O(1)** time, **O(1)** space.  
*Test* – `new Solution().winningPlayer(2, 7)` → `"Alice"`.

---

### Python

```python
# File: solution.py
class Solution:
    def winningPlayer(self, x: int, y: int) -> str:
        turns = min(x, y // 4)            # integer division
        return "Alice" if turns % 2 else "Bob"
```

*Complexity* – **O(1)** time, **O(1)** space.  
*Test* – `Solution().winningPlayer(4, 11)` → `"Bob"`.

---

### C++

```cpp
// File: solution.cpp
#include <algorithm>
#include <string>

class Solution {
public:
    std::string winningPlayer(int x, int y) {
        int turns = std::min(x, y / 4);  // integer division
        return (turns % 2) ? "Alice" : "Bob";
    }
};
```

*Complexity* – **O(1)** time, **O(1)** space.  
*Test* – `Solution().winningPlayer(2, 7)` → `"Alice"`.

---

## 📄 Blog Article – “The Good, the Bad, and the Ugly of the Coin Game”

> **SEO‑Optimized Title**  
> *“Coin Game Winner Algorithm – O(1) Java, Python, C++ – LeetCode 3222 Deep Dive”*

---

### 1️⃣ The Good

* **Simplicity** – Once you realize that a move is forced to be `1×75 + 4×10`, the game collapses to a single arithmetic expression.
* **Time & Space** – O(1) is the sweet spot for interviewers. No loops, no recursion, no DP tables.
* **Cross‑Language Compatibility** – The same logic works in Java, Python, and C++. Perfect for interview prep where the language is a choice.
* **Clear Explanation** – The blog breaks down the reasoning step‑by‑step, which is ideal for recruiters reading your notes.

---

### 2️⃣ The Bad

* **Assumption of Optimal Play** – We skip simulation of all states. In more complex coin games the optimal strategy may not be obvious.  
* **Edge Cases with Small `y`** – If `y < 4`, no turn can happen. The algorithm naturally handles it (turns = 0 → Bob wins), but a novice might miss this nuance.  
* **LeetCode‑Specific** – The explanation is tightly coupled to the problem statement; you need to translate the logic when the coin values change.

---

### 3️⃣ The Ugly

* **Mis‑reading the Winning Condition** – Many candidates get tangled by the “last move wins/loses” wording. In this problem *the player who **cannot** move loses*, which flips the parity logic.
* **Hard‑to‑Debug Recursion** – Some solutions try a recursive simulation, causing stack overflow on large inputs.  
* **Over‑Engineering** – Building a full state‑search (DFS/BFS) when a single formula suffices is a classic interview trap.

---

### 4️⃣ Takeaways for Your Job Hunt

| Skill | How the Coin Game Demonstrates It |
|-------|----------------------------------|
| **Algorithmic Thinking** | Reduce a game to its core operation (`1x75 + 4x10`). |
| **Complexity Analysis** | Show O(1) vs O(n) trade‑offs. |
| **Code Clarity** | 2‑line solution in any language – brevity is valued. |
| **Problem Decomposition** | Identify resource constraints (`x` vs `y/4`). |
| **Interview Communication** | Explain reasoning aloud – “Here’s why I use `min(x, y/4)` …” |

---

### 📚 Further Reading & Resources

1. **LeetCode Solution Discussion** – https://leetcode.com/problems/find-the-winning-player-in-coin-game/solutions/  
2. **Game Theory Basics** – “Winning Ways for Your Mathematical Plays” (book).  
3. **Time‑Complexity Cheat Sheet** – https://www.topcoder.com/community/competitive-programming/tutorials/time-complexity/

---

### 💬 Final Thoughts

The Coin Game is a classic interview showcase. The O(1) solution in Java, Python, and C++ proves you can find the “golden path” in a problem and communicate it concisely. When recruiters glance at your blog post or your submission, they’ll spot:

* You **understood the core constraints**.  
* You can **optimize** immediately.  
* You can **implement** cleanly in the language you choose.

Keep the blog handy for future interviews; it’s a quick refresher and a résumé‑friendly highlight reel. 🚀

---