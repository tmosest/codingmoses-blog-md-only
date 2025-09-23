---
title: LeetCode 2312. Selling Pieces of Wood - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 2312 – Selling Pieces of Wood  
**LeetCode Hard – Dynamic Programming**  

| Language | Time | Space | Notes |
|----------|------|-------|-------|
| **Java** | `O(m · n · (m + n))` | `O(m · n)` | 64‑bit `long` |
| **Python** | `O(m · n · (m + n))` | `O(m · n)` | Use `list` of `list` |
| **C++** | `O(m · n · (m + n))` | `O(m · n)` | 64‑bit `long long` |

> The same bottom‑up DP works for every language – just make sure to use a 64‑bit type because the answer can reach `10^6 · 200 · 200 ≈ 4×10^10`.

---

## 🧩 Problem Recap (Short Version)

You have a rectangular board of size `m × n`.  
A set of **price offers** is given, each offer `[h, w, price]` means you can sell a piece of size `h × w` for `price` dollars.  
You can cut the board any number of times **horizontally or vertically across the whole width/height**.  
You may sell any number of pieces (including zero) of the shapes you own.

> **Goal:** maximize the total money earned from the `m × n` board.

> **No rotation allowed** – a `1×4` cannot become `4×1`.

---

## 💡 Intuition

The only thing that matters is how the board is split.  
If we know the optimal value for a **smaller** rectangle, we can combine two sub‑rectangles to form a bigger one.

This is the classic “maximum‑value‑cut” problem → **Dynamic Programming (DP)**.

### Recurrence

Let `dp[h][w]` be the maximum money from a piece of size `h × w`.  
Base values come from the given offers; other cells start at `0`.

```
dp[h][w] = max( dp[h][w],  // no cut
                dp[i][w] + dp[h-i][w]  for i = 1 .. h/2 )   // horizontal cuts
                dp[h][j] + dp[h][w-j]  for j = 1 .. w/2 )   // vertical cuts
```

Why only `h/2` and `w/2`?  
Because a cut at `i` and at `h-i` produce the same pair of sub‑rectangles – we just consider one direction.

### Why bottom‑up?

We compute all `dp` values in increasing order of `h` and `w`.  
When we process `dp[h][w]`, all smaller rectangles are already optimal, so we can safely combine them.

---

## 🏗️ Code

Below you’ll find the exact same algorithm implemented in **Java**, **Python**, and **C++**.  
Feel free to copy‑paste into your editor and run against the LeetCode test harness.

---

### Java

```java
import java.util.*;

public class Solution {
    public long sellingWood(int m, int n, int[][] prices) {
        // dp[h][w] – maximum money for a h x w piece
        long[][] dp = new long[m + 1][n + 1];

        // base cases: set offered prices
        for (int[] p : prices) {
            int h = p[0], w = p[1];
            dp[h][w] = p[2];
        }

        // bottom‑up DP
        for (int h = 1; h <= m; ++h) {
            for (int w = 1; w <= n; ++w) {
                // horizontal cuts
                for (int i = 1; i <= h / 2; ++i) {
                    dp[h][w] = Math.max(dp[h][w],
                        dp[i][w] + dp[h - i][w]);
                }
                // vertical cuts
                for (int j = 1; j <= w / 2; ++j) {
                    dp[h][w] = Math.max(dp[h][w],
                        dp[h][j] + dp[h][w - j]);
                }
            }
        }
        return dp[m][n];
    }
}
```

---

### Python

```python
class Solution:
    def sellingWood(self, m: int, n: int, prices: List[List[int]]) -> int:
        # dp[h][w] – maximum money for a h x w piece
        dp = [[0] * (n + 1) for _ in range(m + 1)]

        for h, w, p in prices:
            dp[h][w] = p

        for h in range(1, m + 1):
            for w in range(1, n + 1):
                # horizontal cuts
                for i in range(1, h // 2 + 1):
                    dp[h][w] = max(dp[h][w], dp[i][w] + dp[h - i][w])
                # vertical cuts
                for j in range(1, w // 2 + 1):
                    dp[h][w] = max(dp[h][w], dp[h][j] + dp[h][w - j])

        return dp[m][n]
```

---

### C++

```cpp
class Solution {
public:
    long long sellingWood(int m, int n, vector<vector<int>>& prices) {
        // dp[h][w] – maximum money for a h x w piece
        vector<vector<long long>> dp(m + 1, vector<long long>(n + 1, 0));

        // base cases
        for (auto& p : prices) {
            int h = p[0], w = p[1];
            dp[h][w] = p[2];
        }

        // bottom‑up DP
        for (int h = 1; h <= m; ++h) {
            for (int w = 1; w <= n; ++w) {
                // horizontal cuts
                for (int i = 1; i <= h / 2; ++i)
                    dp[h][w] = max(dp[h][w], dp[i][w] + dp[h - i][w]);

                // vertical cuts
                for (int j = 1; j <= w / 2; ++j)
                    dp[h][w] = max(dp[h][w], dp[h][j] + dp[h][w - j]);
            }
        }
        return dp[m][n];
    }
};
```

---

## 📊 Complexity

| Operation | Complexity |
|-----------|------------|
| Base‑case fill | `O(#prices)` |
| Main DP loops | `O(m · n · (m + n))` |
| Memory | `O(m · n)` |

**Why is it `O(m · n · (m + n))`?**  
For each of the `m · n` cells we try up to `h/2 + w/2 ≤ (m + n)/2` cuts.

---

## ⚠️ Edge Cases & Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Using `int` for `dp` | Max answer ≈ 4 × 10¹⁰ → overflow. Use `long`/`long long`. |
| Forgetting to set base cases | Uninitialized cells stay `0`, which can be sub‑optimal if all offers are negative‑like. |
| Cutting beyond half | Double‑count cuts → slower. Only iterate to `h/2` and `w/2`. |
| Off‑by‑one indices | LeetCode board is 1‑based in the recurrence; allocate `m+1` and `n+1`. |
| Not handling `h/2` for odd `h` | Use integer division (`h // 2` in Python, `h / 2` in Java/C++). |

---

## 🧩 Alternative Strategies

| Strategy | Pros | Cons |
|----------|------|------|
| **Top‑down + Memoization** | Simple recursion, fewer loops | Recursion depth up to 200 (ok in most languages) but can hit Python recursion limit |
| **Branch & Bound / DFS** | Handles *very* sparse price sets efficiently | Still `O(mn(m+n))` worst‑case; more code |
| **Greedy** | Fast | Only works for special price sets (e.g., only 1×1). |
| **Bitmask DP** | Works when `m, n ≤ 10` | Exponential, not suitable for 200 |

For interviews, the **bottom‑up DP** is the safest answer because it guarantees optimal sub‑solutions without the risk of stack overflow.

---

## 📚 Blog Post: “Selling Pieces of Wood – The Good, the Bad & the Ugly”  

> **SEO Tags:**  
> * selling pieces of wood leetcode  
> * leetcode 2312 solution  
> * dynamic programming wood cutting  
> * interview dynamic programming  
> * Java Python C++ LeetCode solution  

---

# Selling Pieces of Wood: A Deep‑Dive into Dynamic Programming  
(The Good, the Bad & the Ugly – SEO‑Optimized)

---

## 1️⃣ Problem Snapshot  

> **LeetCode 2312 – Selling Pieces of Wood**  
> Cut a `m × n` board into smaller pieces to maximize earnings, respecting a set of price offers and non‑rotational cuts.

> **Why it matters**  
> This problem surfaces in every *algorithm interview* that tests your understanding of 2‑D DP, recursion, and optimization.

---

## 2️⃣ Good – Why It’s a Great Interview Problem  

| ✅ | Explanation |
|---|-------------|
| **Clear DP State** | The board is split into independent sub‑rectangles. |
| **Bottom‑Up Simplicity** | One triple‑nested loop; no recursion stack to manage. |
| **Language‑agnostic** | Same logic works in Java, Python, C++ – perfect for cross‑language coding interviews. |
| **Scales Well** | Handles maximum constraints (`m, n ≤ 200`) comfortably in 2 s on LeetCode. |
| **Real‑World Analogy** | Mirrors real‑world cutting‑stock problems (fabric, metal, etc.). |

> *Tip:* Use 64‑bit integers (`long`/`long long`) to avoid overflow.  

---

## 3️⃣ Bad – Things That Can Go Wrong  

| ❌ | What can break your solution? |
|---|-------------------------------|
| **Overflow** | Price * 40 000 000 > 2³¹–1 → use 64‑bit. |
| **Off‑by‑One** | DP indices start at `1`; forgetting the `+1` in array size kills the algorithm. |
| **Full‑Range Cuts** | Trying `i = 1 .. h-1` doubles work; use `h/2` and `w/2`. |
| **Missing Base Prices** | Any shape without a price offer is implicitly `0`. If all offers are negative, you must still consider zero cuts. |
| **Recursion Stack** | Pure recursion may hit Python’s recursion limit (`1000`). |
| **Timeouts** | O(m·n·(m+n)) ≈ 16 M operations is fine in Java/Python; in C++ it's faster, but still watch for 200²×400 ≈ 16 000 000 loops. |

---

## 4️⃣ Ugly – The “Tricky” Parts  

| 😱 | Why it’s ugly |
|---|---------------|
| **Half‑Cut Optimisation** | Remember to only iterate up to `h/2` and `w/2`. Skipping this doubles work and can slow you down. |
| **Price Map vs. DP** | Some people store a price map and look it up during DP. That’s unnecessary; just write the price directly into `dp`. |
| **Choosing Cut Direction** | Horizontal cuts at `i` and `h-i` produce the same pair; you must avoid counting them twice. |
| **Space Trade‑off** | A 2‑D DP of `200 × 200` is only 40 000 cells – fine. But if you tried a 3‑D DP (height, width, leftover), it explodes. |
| **Testing on LeetCode** | The answer can be up to `4×10¹⁰`, so you must return `long`/`long long`. Many solutions mistakenly return `int` and get a wrong answer. |

---

## 5️⃣ Full Solution Walk‑through (Java + Python + C++)

*(Code snippets already shown above – you can copy them directly.)*

---

## 6️⃣ Complexity Analysis

| Measure | Java | Python | C++ |
|---------|------|--------|-----|
| **Time** | `O(m·n·(m+n))` | `O(m·n·(m+n))` | `O(m·n·(m+n))` |
| **Space** | `O(m·n)` | `O(m·n)` | `O(m·n)` |
| **Worst‑case ops** | ≈ 16 000 000 for 200×200 | same | same |

> **Why it’s fast enough** – 16 M integer operations run < 0.2 s in Java/C++ and < 0.5 s in Python on LeetCode’s servers.

---

## 7️⃣ Alternative Approaches

| Approach | When to use it | Pros | Cons |
|----------|---------------|------|------|
| **Top‑down memoization (DFS)** | Small number of offers; you want code brevity | Recursion + memo gives clean code | Python recursion limit; slower in practice. |
| **Bitmask DP** | Board size ≤ 10 | Works for all cut patterns | Exponential `2^h` state – not for 200. |
| **Greedy / DP on 1×1 only** | If all offers are 1×1 | O(1) | Wrong in general – misses higher‑value offers. |
| **Divide & Conquer + Memo** | For teaching recursion | Demonstrates recursion + memo | Same complexity but higher constant overhead. |

> *Bottom‑up DP is the canonical interview answer – it shows you understand sub‑problem ordering and can handle the large constraints.*

---

## 8️⃣ How to Turn This into a Job‑Winning Interview

1. **Show the DP intuition** – talk about `dp[h][w]` and why horizontal/vertical cuts only need half the range.
2. **Explain complexity** – interviewers love you when you’re explicit about `O(m·n·(m+n))`.
3. **Talk about edge cases** – e.g., a 2×2 might be cheaper than four 1×1s; always check the base offers.
4. **Mention 64‑bit safety** – “I’m using `long`/`long long` because the answer can exceed 32‑bit”.
5. **Optional** – discuss memoization vs bottom‑up and why bottom‑up is usually the safest in production interviews.
6. **Show the code in multiple languages** – demonstrates versatility.
7. **Wrap up** – “I can solve this problem in Java, Python, or C++ with the same optimal algorithm.”

> *Result:* You’ll land a coding interview where they ask you to explain or extend this solution.

---

## 🎓 Final Thoughts

* **Good:** Clean, deterministic DP; O(16 M) ops easily pass; no recursion pitfalls.
* **Bad:** Must remember half‑cut optimisation and 64‑bit integers.
* **Ugly:** Off‑by‑one errors, double‑counting cuts, and subtle language differences.

If you can convey all of this concisely in an interview, you’re showing that you’re not just a *coder* but an *algorithmist* who can think through states, time/space, and real‑world constraints.  

Happy coding! 🚀

---

*End of Blog Post*  

--- 

Enjoy the interview! If you’d like to dive deeper into DP or have questions about implementing the solution in a different language, feel free to drop a comment.