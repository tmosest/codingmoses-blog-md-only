---
title: LeetCode 3647. Maximum Weight in Two Bags - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution Code

Below are three **ready‑to‑run** solutions – one each in Java, Python, and C++ – that solve LeetCode 3647 “Maximum Weight in Two Bags”.  
All three use the same dynamic‑programming idea: treat the two bags as two independent knapsack dimensions and iterate over the items once.

| Language | Complexity | Notes |
|----------|------------|-------|
| Java | `O(n · w1 · w2)` time, `O(w1 · w2)` space | Iterative DP, no recursion |
| Python | `O(n · w1 · w2)` time, `O(w1 · w2)` space | Same DP, uses a list of lists |
| C++ | `O(n · w1 · w2)` time, `O(w1 · w2)` space | Uses `vector<vector<int>>` |

---

### 1.1 Java

```java
// File: Solution.java
import java.util.*;

public class Solution {
    public int maxWeight(int[] weights, int w1, int w2) {
        // DP table: dp[a][b] = max weight that can be carried
        // with capacity a in bag1 and capacity b in bag2
        int[][] dp = new int[w1 + 1][w2 + 1];

        for (int weight : weights) {
            // iterate backwards to avoid re‑using the same item
            for (int a = w1; a >= 0; a--) {
                for (int b = w2; b >= 0; b--) {
                    int cur = dp[a][b];
                    if (a >= weight) {
                        cur = Math.max(cur, dp[a - weight][b] + weight);
                    }
                    if (b >= weight) {
                        cur = Math.max(cur, dp[a][b - weight] + weight);
                    }
                    dp[a][b] = cur;
                }
            }
        }
        return dp[w1][w2];
    }

    // ----------  Demo Main (optional) ----------
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.maxWeight(new int[]{1, 4, 3, 2}, 5, 4)); // 9
        System.out.println(s.maxWeight(new int[]{3, 6, 4, 8}, 9, 7)); // 15
        System.out.println(s.maxWeight(new int[]{5, 7}, 2, 3));        // 0
    }
}
```

---

### 1.2 Python

```python
# File: solution.py
from typing import List

class Solution:
    def maxWeight(self, weights: List[int], w1: int, w2: int) -> int:
        # dp[a][b] = max weight achievable with capacities a, b
        dp = [[0] * (w2 + 1) for _ in range(w1 + 1)]

        for weight in weights:
            for a in range(w1, -1, -1):
                for b in range(w2, -1, -1):
                    cur = dp[a][b]
                    if a >= weight:
                        cur = max(cur, dp[a - weight][b] + weight)
                    if b >= weight:
                        cur = max(cur, dp[a][b - weight] + weight)
                    dp[a][b] = cur
        return dp[w1][w2]

# ---------- Demo ----------
if __name__ == "__main__":
    s = Solution()
    print(s.maxWeight([1, 4, 3, 2], 5, 4))  # 9
    print(s.maxWeight([3, 6, 4, 8], 9, 7))  # 15
    print(s.maxWeight([5, 7], 2, 3))         # 0
```

---

### 1.3 C++

```cpp
// File: solution.cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxWeight(vector<int>& weights, int w1, int w2) {
        vector<vector<int>> dp(w1 + 1, vector<int>(w2 + 1, 0));

        for (int weight : weights) {
            for (int a = w1; a >= 0; --a) {
                for (int b = w2; b >= 0; --b) {
                    int cur = dp[a][b];
                    if (a >= weight)
                        cur = max(cur, dp[a - weight][b] + weight);
                    if (b >= weight)
                        cur = max(cur, dp[a][b - weight] + weight);
                    dp[a][b] = cur;
                }
            }
        }
        return dp[w1][w2];
    }
};

// ---------- Demo ----------
int main() {
    Solution s;
    cout << s.maxWeight({1, 4, 3, 2}, 5, 4) << endl; // 9
    cout << s.maxWeight({3, 6, 4, 8}, 9, 7) << endl; // 15
    cout << s.maxWeight({5, 7}, 2, 3) << endl;        // 0
    return 0;
}
```

---

## 2.  Blog Article – “Maximum Weight in Two Bags: The Good, The Bad, & The Ugly”

> *“If you can solve a problem that looks like a 0‑1 Knapsack, you’re basically a programmer.”* – *LeetCode Community*

### Table of Contents
1. [Problem Overview](#problem-overview)
2. [Naïve Approaches](#naïve-approaches)
3. [Dynamic Programming – The Sweet Spot](#dynamic-programming-the-sweet-spot)
4. [The Good, The Bad, & The Ugly](#the-good-the-bad-the-ugly)
5. [Time & Space Complexity](#time-space-complexity)
6. [Why This Matters in Interviews](#why-this-matters-in-interviews)
7. [Final Takeaway](#final-takeaway)

---

### Problem Overview <a name="problem-overview"></a>

> **Maximum Weight in Two Bags**  
> *LeetCode 3647 – Medium*  

You’re given an array `weights` of positive integers and two integers `w1`, `w2` representing the capacity limits of two bags. Each item can go into **at most one** bag (or not be used). The goal is to **maximise the total weight** that ends up in both bags.

*Constraints*  
- `1 ≤ weights.length ≤ 100`  
- `1 ≤ weights[i] ≤ 100`  
- `1 ≤ w1, w2 ≤ 300`

Because the capacities are relatively small (≤ 300), a dynamic‑programming solution with `O(w1 · w2)` state space is perfectly feasible.

---

### Naïve Approaches <a name="naïve-approaches"></a>

1. **Brute‑Force Backtracking**  
   Enumerate every assignment of each item to bag 1, bag 2, or “not used.”  
   *Complexity*: `3^n` – completely infeasible even for `n = 20`.

2. **Recursive Top‑Down with Memoization**  
   A typical 0‑1 knapsack recursion with an extra dimension for the second bag.  
   *Pros*: Easy to understand.  
   *Cons*: 3‑dimensional memoization array can become bulky; risk of stack overflow for deep recursion.

Both naïve methods quickly hit the wall in real interview scenarios – they’re too slow and hard to explain in under a minute.

---

### Dynamic Programming – The Sweet Spot <a name="dynamic-programming-the-sweet-spot"></a>

#### 1.  Two‑Dimensional Knapsack

The core idea: treat the two capacities as a *grid* of states.  
`dp[a][b]` = maximum total weight that can be achieved with *at most* `a` capacity in bag 1 and `b` capacity in bag 2.

#### 2.  State Transition

For each weight `x`:

```
for a from w1 downto 0
    for b from w2 downto 0
        cur = dp[a][b]
        if a >= x: cur = max(cur, dp[a - x][b] + x)
        if b >= x: cur = max(cur, dp[a][b - x] + x)
        dp[a][b] = cur
```

*Why reverse loops?*  
We update from high to low capacities so that each item is used at most once. The reversed order guarantees that we never read a state that already includes the current item.

#### 3.  Final Answer

`dp[w1][w2]` holds the optimal total weight.

---

### The Good, The Bad, & The Ugly <a name="the-good-the-bad-the-ugly"></a>

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Concept** | Classic 0‑1 knapsack extension – straightforward to explain. | Requires *two* dimensions; can be confusing for beginners. | A naïve 3‑dimensional DP is often the “ugly” solution people try first and get stuck. |
| **Implementation** | Compact nested loops – easy to read and debug. | Memory footprint `O(w1 · w2)` can be large if capacities are near 300. | Recursion with a 3‑D array may overflow the stack or hit Java’s `StackOverflowError`. |
| **Time** | `O(n · w1 · w2)` – fast enough for given limits. | If you accidentally loop from `0` up instead of down, you’ll count items multiple times. | Backtracking (`3^n`) is infeasible beyond very small test cases. |
| **Space** | 2D array only – no extra recursion stack. | For extremely large `w1` or `w2` you’d run out of memory. | Top‑down memoization can use a 3‑D map which is less cache friendly. |
| **Interview Impact** | Shows mastery of DP and space optimisation. | Demonstrates careful attention to boundary conditions. | Shows ability to recognise and avoid exponential blow‑up. |

---

### Time & Space Complexity <a name="time-space-complexity"></a>

| Approach | Time | Space |
|----------|------|-------|
| Brute‑Force | `O(3^n)` | `O(1)` |
| Top‑Down DP | `O(n · w1 · w2)` | `O(n · w1 · w2)` |
| Bottom‑Up 2‑D DP | **`O(n · w1 · w2)`** | **`O(w1 · w2)`** |

For the given constraints (`n ≤ 100`, `w1,w2 ≤ 300`) the bottom‑up DP uses at most `301 × 301 ≈ 90 601` integers – well within any modern stack.

---

### Why This Matters in Interviews <a name="why-this-matters-in-interviews"></a>

- **Showcase DP Mastery**: Most interviewers love to see you solve a variation of the Knapsack problem. It proves you can handle combinatorial optimisation.
- **Explain Reverse Loops**: A subtle but essential part of the transition; missing it can lead to wrong answers. It tests your ability to think about “used” vs “unused” states.
- **Space Optimisation**: Reducing from 3‑D to 2‑D demonstrates you think about runtime memory and cache locality.
- **Avoid Stack Traps**: The recursive solution often causes stack overflows. If you recognise this and switch to an iterative version, you’ll impress.
- **Time‑Sensitive**: You’ll finish within the interview window (≈ 30 minutes) and still have time for clarifying questions.

---

### Final Takeaway <a name="final-takeaway"></a>

> “If you can solve this with a 2‑D DP, you’re ready to tackle *any* combinatorial packing problem.”  

The *Maximum Weight in Two Bags* problem is a “safe‑harbor” in interview stacks: it is complex enough to test your algorithmic depth but still simple enough to be explained in less than a minute.  
Use the **bottom‑up 2‑D DP** approach – it’s the “good” solution that avoids the “bad” pitfalls of a 3‑D memo table and sidesteps the “ugly” exponential backtracking.  

Happy coding! 🚀

---

### SEO & Keywords

- LeetCode 3647
- Maximum Weight in Two Bags solution
- Dynamic Programming interview questions
- 0‑1 Knapsack two bags
- Medium LeetCode problems
- Interview DP space optimisation
- Two‑dimensional knapsack
- Programming interview best practices

*(Feel free to copy/paste the code snippets above, run them in your IDE, and tweak the demo main to test more edge cases.)*

---

> **Want more “the good, the bad & the ugly” articles?** Subscribe to my newsletter and get the *most common interview pitfalls* solved, *step by step*.  

---

**Author**: *[Your Name]* – Full‑stack engineer, open‑source contributor, and LeetCode evangelist.  
LinkedIn: <https://linkedin.com/in/yourprofile>  
GitHub: <https://github.com/yourgithub>  
Blog: <https://yourblog.com>  

---


---

> *If you’re using this article for a personal website, remember to credit LeetCode and the problem’s official description.*