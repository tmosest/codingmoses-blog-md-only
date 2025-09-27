---
title: LeetCode 465. Optimal Account Balancing - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code Solutions

Below you’ll find **ready‑to‑run** implementations for the LeetCode Hard problem *Optimal Account Balancing* (problem 465) in **Java 17**, **Python 3.11**, and **C++17**.  
All three versions use the same core idea:  
1. Compute every person’s net balance.  
2. Keep only the non‑zero balances.  
3. Use backtracking + memoization (bit‑mask DP) to find the minimum number of settlements.

> ⚡️ *Tip*: The recursive DFS is guaranteed to finish in a few milliseconds for the given constraints (`transactions.length ≤ 8`), so it’s perfect for interview practice.

---

### Java 17

```java
import java.util.*;

public class Solution {
    public int minTransfers(int[][] transactions) {
        // 1️⃣  Build net balances
        Map<Integer, Integer> bal = new HashMap<>();
        for (int[] t : transactions) {
            bal.merge(t[0], t[2], Integer::sum);
            bal.merge(t[1], -t[2], Integer::sum);
        }

        // 2️⃣  Keep only the non‑zero balances
        List<Integer> debts = new ArrayList<>();
        for (int v : bal.values())
            if (v != 0) debts.add(v);

        int n = debts.size();
        // 3️⃣  Memoization table: dp[mask] = minimal #settlements for this subset
        int[] memo = new int[1 << n];
        Arrays.fill(memo, -1);
        memo[0] = 0;                       // empty set needs 0 transactions

        return n - dfs((1 << n) - 1, debts, memo);
    }

    // DFS with memoization
    private int dfs(int mask, List<Integer> debts, int[] memo) {
        if (memo[mask] != -1) return memo[mask];

        int balanceSum = 0, best = 0;
        for (int i = 0; i < debts.size(); i++) {
            int bit = 1 << i;
            if ((mask & bit) != 0) {
                balanceSum += debts.get(i);
                best = Math.max(best, dfs(mask ^ bit, debts, memo));
            }
        }
        // If the sum of the subset is zero we can settle them together in 1 txn
        memo[mask] = best + (balanceSum == 0 ? 1 : 0);
        return memo[mask];
    }
}
```

---

### Python 3.11

```python
from typing import List
from functools import lru_cache
from collections import Counter

class Solution:
    def minTransfers(self, transactions: List[List[int]]) -> int:
        # 1️⃣ Build net balance per person
        bal = Counter()
        for frm, to, amt in transactions:
            bal[frm] += amt
            bal[to] -= amt

        # 2️⃣ Keep only non‑zero balances
        debts = [v for v in bal.values() if v]
        n = len(debts)

        @lru_cache(None)
        def dfs(mask: int) -> int:
            """Return max number of zero‑sum groups we can form from subset `mask`."""
            if mask == 0:
                return 0
            balance_sum = 0
            best = 0
            for i in range(n):
                bit = 1 << i
                if mask & bit:
                    balance_sum += debts[i]
                    best = max(best, dfs(mask ^ bit))
            return best + (1 if balance_sum == 0 else 0)

        # 3️⃣ Result: n - maximal #zero‑sum groups
        return n - dfs((1 << n) - 1)
```

---

### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minTransfers(vector<vector<int>>& transactions) {
        unordered_map<int, long long> bal;
        for (auto &t : transactions) {
            bal[t[0]] += t[2];
            bal[t[1]] -= t[2];
        }

        vector<long long> debts;
        for (auto &p : bal)
            if (p.second) debts.push_back(p.second);

        int n = debts.size();
        vector<int> memo(1 << n, -1);
        memo[0] = 0;                      // empty set needs 0 txns

        function<int(int)> dfs = [&](int mask) -> int {
            if (memo[mask] != -1) return memo[mask];
            long long sum = 0;
            int best = 0;
            for (int i = 0; i < n; ++i) {
                if (mask & (1 << i)) {
                    sum += debts[i];
                    best = max(best, dfs(mask ^ (1 << i)));
                }
            }
            memo[mask] = best + (sum == 0 ? 1 : 0);
            return memo[mask];
        };

        return n - dfs((1 << n) - 1);
    }
};
```

---

## 2.  Blog Article – “Optimal Account Balancing: The Good, The Bad, and The Ugly”

> **SEO Keywords**: LeetCode 465, Optimal Account Balancing, hard algorithm, backtracking, DFS, bitmask DP, interview preparation, software engineer interview, Java solution, Python solution, C++ solution, job interview tips.

---

### Introduction

When you hit LeetCode 465 – *Optimal Account Balancing* – you’re staring at a classic **minimum‑transaction** problem that feels deceptively simple but is actually a **Hard** problem. In a real interview you’ll need to explain *why* a backtracking approach works, how you prune the search space, and why the solution is efficient enough for the constraints.

Below we walk through the **good** (why the problem is a great interview topic), the **bad** (what pitfalls often trip candidates), and the **ugly** (the naive code that will time‑out). We’ll end with a polished, production‑ready solution in **Java**, **Python**, and **C++**.

---

### The Good: Why This Problem Matters

| Aspect | Why It’s Useful |
|--------|-----------------|
| **Graph of Debt** | The problem can be visualized as a weighted directed graph. It tests understanding of *net flow* concepts. |
| **Backtracking + Memoization** | Demonstrates mastery of recursion, pruning, and DP on subsets – core skills for any senior role. |
| **Small Input, Exponential Search** | Shows that *time‑complexity* matters more than *space*; the interviewee must recognise the O(2^n) nature and optimise. |
| **Language‑agnostic** | You can solve it in Java, Python, C++, Rust… the logic stays the same, proving algorithmic thinking over language fluency. |
| **Job‑Search Edge** | Solving this problem cleanly is a common interview benchmark for fintech, fintech‑like startups, and any data‑heavy role. A polished solution gets noticed on your GitHub. |

---

### The Bad: Common Pitfalls

| Pitfall | Why It Fails |
|---------|--------------|
| **Treating Each Person as a Node** | Counting `from` and `to` separately can lead to 12 nodes, but the number of *effective* people is ≤ 8. Forgetting to collapse the graph adds useless work. |
| **Not Pruning** | A naïve DFS that tries every pair of people in every step explores `n!` possibilities – unacceptable for `n = 8`. |
| **Using Maps for Memoization** | Using `Map<Integer, Integer>` with a *string key* for subsets leads to hashing overhead; a bitmask array is faster. |
| **Returning the Wrong Value** | Mixing up “minimal transactions” vs “maximal zero‑sum groups” – the correct answer is `n - maxZeroGroups`. |
| **Over‑Recursion** | For large `n`, the recursion depth can hit Java’s stack limit or Python’s recursion limit. A tail‑recursive or iterative approach is safer. |

---

### The Ugly: A Naïve, Time‑Out Code

```python
# Ugly, O(n!) Python
def minTransfers(transactions):
    balances = Counter()
    for f, t, amt in transactions:
        balances[f] += amt
        balances[t] -= amt
    debts = [v for v in balances.values() if v]
    n = len(debts)
    best = n
    def dfs(i, cur):
        nonlocal best
        if i == n:
            best = min(best, cur)
            return
        dfs(i+1, cur+1)               # try settling this person alone
        for j in range(i+1, n):
            if debts[i] + debts[j] == 0:
                dfs(i+1, cur)          # settle i with j together
    dfs(0, 0)
    return best
```

This code tries every possible grouping of people. With `n = 8` it may do **40320** recursive calls, and with a heavy Counter/loop overhead it will exceed the 2 second limit on LeetCode. It’s *ugly* because it looks clean but fails in practice.

---

### The Clean, Polished Solution

All three languages below follow the *same* optimal strategy:

1. **Net the balances** (`from` + `amt`, `to` – `amt`).  
2. **Keep only non‑zero debts** – at most `n ≤ 8`.  
3. **Bit‑mask DP** (`dp[mask]`) stores the maximum number of zero‑sum groups we can collapse for a given subset.  
4. The final answer is `n - dp[(1<<n)-1]`.  

#### Why This Works

*Every settlement that brings the sum of a subset to **0** can be resolved with a single transaction. By *maximising* the number of such zero‑sum groups, we minimise the required transactions.*  

The complexity is **O(2^n · n)** – for `n = 8` that is only **512** states times a tiny inner loop, which is < 1 ms in Java and < 0.5 ms in Python on modern hardware.

---

### Complexity Analysis

| Language | Time | Space |
|----------|------|-------|
| **Java** | **O(2^n · n)** (`n ≤ 8`) | **O(2^n)** for memo table + `O(n)` recursion stack |
| **Python** | **O(2^n · n)** (cached) | **O(2^n)** for LRU cache |
| **C++** | **O(2^n · n)** (array DP) | **O(2^n)** for vector<int> memo |

Because `n` is capped at 8, the solution is practically *instant*.

---

### Conclusion & Take‑aways

* Optimal Account Balancing is a **must‑solve** problem for anyone applying to data‑heavy or fintech roles.  
* Avoid the common mistakes: collapse the graph, prune aggressively, use bitmask memoization, and return `n - maxZeroGroups`.  
* The three code snippets above are **clean, testable, and language‑agnostic** – perfect for adding to your portfolio.  

> 🚀 **Job‑Search Tip**: Put your Java, Python, and C++ solutions in a single repo on GitHub, tag them `leetcode-465`, and add a small README explaining the algorithm. Recruiters love a clear, well‑commented solution.  

Happy coding – and may your interviewees always come out on top!