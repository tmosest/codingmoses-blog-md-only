---
title: LeetCode 2742. Painting the Walls - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎨 2742. Painting the Walls – A 3‑language Dynamic‑Programming Solution  
> **Java** | **Python** | **C++**  

Below you’ll find a **compact, 7‑line‑per‑language implementation** that solves the LeetCode Hard problem “Painting the Walls” in O(n²) time and O(n) space.  
After the code you’ll read a short blog‑style article that explains *why* the solution works, the pitfalls to avoid, and how you can use it to impress interviewers.

---

### ✅ Problem Recap

You’re given two arrays, `cost` and `time`, both of length `n`.  
* **Paid painter** – paints wall *i* in `time[i]` units and costs `cost[i]`.  
* **Free painter** – paints any wall in **1** unit of time for **free**, but can only work when the paid painter is busy.  

The goal: **Minimise the total money spent** to paint all `n` walls.

> *Example*  
> `cost = [1,2,3,2]`, `time = [1,2,3,2]` → answer `3` (pay for walls 0 and 1).

---

## 🧠 Core Idea – “Buy Time Like a Knapsack”

If you pay for wall `i`, you effectively get `time[i]` units of paid‑painter time **plus** the free‑painter time that will run concurrently.  
So by paying for a wall you can finish **`time[i] + 1`** walls altogether.

Thus the problem reduces to a classic 0‑1 knapsack:

| Item | Value (how many walls you cover) | Cost |
|------|----------------------------------|------|
| wall `i` | `time[i] + 1` | `cost[i]` |

We want the minimum total cost that covers **at least** `n` walls.  

---

## 🔧 Algorithm – Bottom‑Up DP

Let  

`dp[t] =` minimal cost to finish *exactly* `t` walls (`0 ≤ t ≤ n`).  
Initialize: `dp[0] = 0`, all others `= INF`.  

For each wall `i` (in any order)  
```
for t from n down to 1:
    dp[t] = min(dp[t],
                dp[max(t - time[i] - 1, 0)] + cost[i])
```
We iterate `t` **downwards** to avoid re‑using the same wall multiple times.

Finally, `dp[n]` is the answer (minimal cost to finish *at least* `n` walls).

**Complexity**

* Time: `O(n²)` – the double loop over `n` walls and `n` time slots.  
* Space: `O(n)` – the DP array.

With `n ≤ 500`, this easily fits into limits.

---

## 📦 Code (7 lines per language)

### Java

```java
import java.util.*;

class Solution {
    public int paintWalls(int[] cost, int[] time) {
        int n = cost.length, INF = 1_000_000_000;
        int[] dp = new int[n + 1];
        Arrays.fill(dp, INF); dp[0] = 0;
        for (int i = 0; i < n; ++i)
            for (int j = n; j > 0; --j)
                dp[j] = Math.min(dp[j], dp[Math.max(j - time[i] - 1, 0)] + cost[i]);
        return dp[n];
    }
}
```

### Python

```python
class Solution:
    def paintWalls(self, cost, time):
        n, INF = len(cost), 10**9
        dp = [0] + [INF] * n
        for c, t in zip(cost, time):
            for j in range(n, 0, -1):
                dp[j] = min(dp[j], dp[max(j - t - 1, 0)] + c)
        return dp[n]
```

### C++

```cpp
class Solution {
public:
    int paintWalls(vector<int>& cost, vector<int>& time) {
        int n = cost.size(), INF = 1e9;
        vector<int> dp(n + 1, INF); dp[0] = 0;
        for (int i = 0; i < n; ++i)
            for (int j = n; j > 0; --j)
                dp[j] = min(dp[j], dp[max(j - time[i] - 1, 0)] + cost[i]);
        return dp[n];
    }
};
```

---

## 📖 Blog Article – “Painting the Walls: The Good, The Bad, and The Ugly”

### Title  
**Painting the Walls – The Good, The Bad, and The Ugly of a Hard LeetCode Problem (DP + Knapsack)**  

### Meta Description  
Discover a clean DP‑knapsack solution to LeetCode 2742 “Painting the Walls”. Learn the pitfalls, optimization tricks, and how to ace this hard interview problem in Java, Python, or C++.

---

### 1. The Challenge in a Nutshell  
The problem forces you to combine **two painters**: a paid one who is slow but cheap, and a free one who is fast but can only act when the paid one is busy. The goal is to pay as little as possible while still finishing all walls.

At first glance you might try a greedy strategy (always pick the cheapest wall, or the fastest), but that fails spectacularly. The real magic comes from **seeing time as a resource to be bought**.

### 2. Why the Knapsack Perspective Works  
When you pay for wall *i*, you get:

* `time[i]` units of paid‑painter work, **and**
* The free painter can keep working during that time, effectively giving you `1` extra wall (the one the free painter finishes while the paid painter is busy).

So you get to paint **`time[i] + 1`** walls for the price of `cost[i]`.  
If you think of “walls painted” as the value and money as the cost, you’re in perfect 0‑1 knapsack land: choose a subset of items (walls) whose total value meets or exceeds `n`.

### 3. The Dynamic‑Programming Blueprint  
Define `dp[t]` as the cheapest cost to paint *exactly* `t` walls. The update rule:

```
dp[t] = min( dp[t],              // skip this wall
             dp[t - (time[i]+1)] + cost[i] )   // buy this wall
```

We guard against negative indices by clipping at 0. Because we scan `t` from high to low, each wall is considered only once.

**The “good”**: The DP is only **one array** – no need for 2D structures or recursive memoisation.

**The “bad”**: A naïve implementation that scans `t` from low to high re‑uses the same wall and over‑pays. Remember to reverse the loop!

**The “ugly”**: Forgetting that we need to cover *at least* `n` walls can lead to a subtle bug: if you store only “exactly `t`” walls, you might return a sub‑optimal solution that covers fewer than `n` walls. Using `max(t - time[i] - 1, 0)` ensures we never undershoot.

### 4. Common Pitfalls & How to Avoid Them  

| Mistake | Fix |
|---------|-----|
| **Iterating `t` upwards** | Use a *reverse* loop (`for j = n; j > 0; --j`) |
| **Wrong “value” calculation** | Add `+1` to `time[i]` – that’s the free painter’s contribution |
| **Large `INF` causing overflow** | Pick a safe `INF` (e.g., `1e9`) and avoid adding beyond it |
| **Thinking “exactly `t` walls” is needed** | We can finish *more* than `n` walls – the DP already handles it with `max(...)` |

### 5. Edge‑Case Cheat Sheet  

| Edge | Why it matters | What to do |
|------|----------------|------------|
| `time[i] == 0` | You still get 1 wall, so buying it is always a good idea if it’s cheap | The DP handles it automatically |
| All `cost[i]` huge | The answer will be the sum of all costs – DP still runs fast |
| `time[i] == n-1` | Paying for one wall can finish *all* walls – quick shortcut if you spot it early |

### 6. Interview‑Ready Tips  

1. **Explain the “buy time” analogy first** – shows you can think resource‑wise.  
2. **Write the DP recurrence on a whiteboard** – the `max(t - time[i] - 1, 0)` line is the heart of the solution.  
3. **Show the complexity** – O(n²) is acceptable for `n ≤ 500`.  
4. **Mention the alternative recursive DP** – helpful for languages that favour recursion, but it’s slower to write in an interview.

### 7. Final Thoughts – The Ugly Side of Hard Problems  

Hard problems often hide a surprisingly simple pattern. The ugly part is that many candidates try exponential backtracking or elaborate heuristics, only to run out of time. The beauty of the DP‑knapsack approach is that it is:

* **Concise** – 7 lines per language.  
* **Correct** – proven by the recurrence.  
* **Extensible** – you can tweak it for “cover at least `k` walls” or “add another painter”.

When you solve this problem, you demonstrate mastery of **resource‑allocation DP** – a skill that interviewers love.

---

### 📈 SEO Checklist

| ✅ | Element |
|----|---------|
| Keywords | `painting the walls leetcode`, `dynamic programming`, `knapsack`, `interview`, `coding interview`, `problem solution`, `Java`, `Python`, `C++` |
| Title | Contains the problem ID and solution type |
| Meta Description | 150‑170 characters, includes main keywords |
| Headings | H1, H2, H3 used for structure |
| Code snippets | Language‑specific blocks for copy‑paste |
| Practical advice | Clear, bullet‑pointed tips for interviewers |

---

### 🎯 Wrap‑Up  

* The **knapsack view** turns a confusing two‑painter story into a simple 0‑1 DP.  
* A *reverse* update loop guarantees each wall is used once.  
* The 3‑language code above is ready for copy‑paste in your next interview or competitive‑programming contest.

Good luck – and may your walls be ever painted!