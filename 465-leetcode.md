---
title: LeetCode 465. Optimal Account Balancing - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 465. Optimal Account Balancing – A Complete Guide  
*(Java / Python / C++ solutions + a job‑interview‑ready blog post)*  

> **Keywords:** LeetCode 465, Optimal Account Balancing, debt settlement, backtracking, bitmask DP, min transfers, Java, Python, C++, interview prep  

---

## 1. Problem Recap  

You’re given a list of money transfers between people:

```text
transactions[i] = [from_i, to_i, amount_i]
```

- `from_i` gives `amount_i` dollars to `to_i`.  
- All IDs are 0‑based, up to 12 unique people.  
- `1 ≤ transactions.length ≤ 8`, so the input is tiny.

**Goal**:  
Return the *minimum* number of additional transactions needed to settle every debt (i.e., make all balances zero).

---

## 2. Why Is It Hard?

* **Exponential combinations:**  
  With up to 8 edges the number of possible settlement paths grows exponentially.

* **State compression needed:**  
  A naïve BFS/DP over all possible balances is impossible (10^12 states).  

* **Backtracking + pruning** is the standard trick – but you need a good insight to prune aggressively.

---

## 3. The Core Insight  

After aggregating all transactions, every person ends up with a net balance:

```text
balance[person] = total_received – total_sent
```

Only people with non‑zero balances matter.  
If there are `k` such people (`k ≤ 12`), we can represent the whole problem by a vector of length `k`.

The goal is to zero‑out this vector using the fewest pairwise transfers.  
**Key Observation**:  
When we settle a pair `(i, j)`, we simply adjust `balance[i]` and `balance[j]`.  
Thus we can think of the problem as **combining debts until all are cancelled**.

---

## 4. Two Popular Approaches

| Approach | Idea | Complexity | When to use |
|----------|------|------------|-------------|
| **Backtracking with pruning** | Recursively pick the first non‑zero balance, settle it with every other opposite‑sign balance, and backtrack. | `O(k!)` worst‑case, but *very* fast in practice (`k ≤ 8`). | Standard interview answer, easy to implement. |
| **Bitmask DP (subset‑sum)** | Count the maximum number of people that can be grouped into a *zero‑sum* subset; answer = `k - maxZeroSumGroupCount`. | `O(2^k * k)` ~ 256 × 8 = 2 k ops for `k ≤ 8`. | Great for demonstrating clever DP; slightly harder to explain. |

Both give the same answer. The blog below focuses on the **backtracking** version because it’s the most interview‑friendly, but we also show the DP solution in Java, Python, and C++ for completeness.

---

## 5. The Backtracking Algorithm (Python)

```python
from collections import defaultdict
from typing import List

class Solution:
    def minTransfers(self, transactions: List[List[int]]) -> int:
        # 1. Aggregate balances
        balances = defaultdict(int)
        for frm, to, amt in transactions:
            balances[frm] += amt
            balances[to] -= amt

        # 2. Keep only non‑zero balances
        debt = [b for b in balances.values() if b != 0]
        if not debt:               # already settled
            return 0

        # 3. Recursive helper
        def dfs(start: int) -> int:
            # Skip settled people
            while start < len(debt) and debt[start] == 0:
                start += 1
            if start == len(debt):          # all settled
                return 0

            min_tx = float('inf')
            for i in range(start + 1, len(debt)):
                # Only settle opposite sign balances
                if debt[start] * debt[i] < 0:
                    # settle `start` with `i`
                    debt[i] += debt[start]
                    min_tx = min(min_tx, 1 + dfs(start + 1))
                    # backtrack
                    debt[i] -= debt[start]
                    # Prune: if we found a perfect match, break early
                    if debt[i] + debt[start] == 0:
                        break
            return min_tx

        return dfs(0)
```

### How It Works

1. **Balance Compression** – All people with zero net balance are ignored.  
2. **DFS + Pruning** –  
   * `start` is the first unsettled index.  
   * Try to settle `debt[start]` with every later `debt[i]` of opposite sign.  
   * After each settlement, recurse on the next unsettled index.  
   * The recursion depth never exceeds `k`.  
3. **Backtracking** – Restores `debt[i]` after each recursive call.  
4. **Early Exit** – If the settlement makes `debt[i]` exactly zero, no further splits are possible, so break early.

---

## 6. The Bitmask DP Approach (Java)

```java
import java.util.*;

class Solution {
    public int minTransfers(int[][] transactions) {
        // 1. Compute net balances
        Map<Integer, Integer> net = new HashMap<>();
        for (int[] t : transactions) {
            net.put(t[0], net.getOrDefault(t[0], 0) + t[2]);
            net.put(t[1], net.getOrDefault(t[1], 0) - t[2]);
        }

        // 2. Filter zeros
        List<Integer> debt = new ArrayList<>();
        for (int b : net.values())
            if (b != 0) debt.add(b);

        int n = debt.size();
        if (n == 0) return 0;

        int totalMask = (1 << n) - 1;
        int[] dp = new int[1 << n];
        Arrays.fill(dp, -1);
        dp[0] = 0;                 // empty set -> 0 people in zero‑sum groups

        // 3. DP over subsets
        for (int mask = 1; mask <= totalMask; mask++) {
            int sum = 0;
            for (int i = 0; i < n; i++)
                if ((mask & (1 << i)) != 0) sum += debt.get(i);

            // if subset balances to zero, it forms a group
            if (sum == 0) {
                dp[mask] = Integer.bitCount(mask); // all members in this group
                continue;
            }

            // try removing one element to reuse already computed subsets
            dp[mask] = 0;
            for (int i = 0; i < n; i++) {
                if ((mask & (1 << i)) != 0)
                    dp[mask] = Math.max(dp[mask], dp[mask ^ (1 << i)]);
            }
        }

        // 4. Result: k - maxZeroGroupCount
        return n - dp[totalMask];
    }
}
```

### Explanation

* `dp[mask]` = **maximum number of people** in `mask` that can be partitioned into zero‑sum subsets.  
* For every subset, if the sum is zero, the whole subset is a zero‑sum group.  
* Otherwise, we can drop any single element and take the best of the remaining subsets – this is the classic subset DP recurrence.  
* The answer is `k - maxZeroGroupCount` because each zero‑sum group needs `size-1` transactions, so total transactions = `k - (#groups)`.

---

## 7. C++ Implementation (Backtracking)

```cpp
#include <vector>
#include <unordered_map>
#include <algorithm>

class Solution {
public:
    int minTransfers(std::vector<std::vector<int>>& transactions) {
        std::unordered_map<int, int> balance;
        for (auto &t : transactions) {
            balance[t[0]] += t[2];
            balance[t[1]] -= t[2];
        }

        std::vector<int> debt;
        for (auto &kv : balance)
            if (kv.second != 0) debt.push_back(kv.second);

        if (debt.empty()) return 0;
        return dfs(debt, 0);
    }

private:
    int dfs(std::vector<int>& debt, int start) {
        int n = debt.size();
        while (start < n && debt[start] == 0) ++start;
        if (start == n) return 0;          // all settled

        int best = INT_MAX;
        for (int i = start + 1; i < n; ++i) {
            if (debt[start] * debt[i] < 0) {
                debt[i] += debt[start];
                best = std::min(best, 1 + dfs(debt, start + 1));
                debt[i] -= debt[start];   // backtrack

                if (debt[i] + debt[start] == 0) break; // perfect match
            }
        }
        return best;
    }
};
```

---

## 8. Complexity Analysis  

| Step | Operation | Cost |
|------|----------|------|
| Balance aggregation | `O(m)` (`m ≤ 8`) | trivial |
| Build debt vector | `O(p)` (`p ≤ 12`) | trivial |
| DFS (Python/C++) | Worst‑case `O(k!)` but with pruning far less | Practical < 1 ms for `k ≤ 8` |
| Bitmask DP (Java) | `O(2^k * k)` | ≤ 2048 operations for `k = 8` |

**Space**  
* DFS: `O(k)` recursion stack + `O(k)` debt list.  
* DP: `O(2^k)` array (256 ints when `k = 8`).

---

## 8. Edge Cases & Test Harness

| # | Input | Expected |
|---|-------|----------|
| 1 | `[[0,1,10],[2,0,5],[1,2,5]]` | `1` |
| 2 | `[[0,1,10],[1,2,5],[2,0,5]]` | `2` |
| 3 | `[[0,1,1],[0,2,1],[0,3,1],[1,4,1],[2,4,1],[3,4,1]]` | `3` |
| 4 | `[]` | `0` |
| 5 | `[[0,1,1],[1,0,1]]` | `0` (already balanced) |

**Quick Test Script (Python)**

```python
def test():
    sol = Solution()
    assert sol.minTransfers([[0,1,10],[2,0,5],[1,2,5]]) == 1
    assert sol.minTransfers([[0,1,10],[1,2,5],[2,0,5]]) == 2
    assert sol.minTransfers([]) == 0
    print("All tests passed!")

test()
```

---

## 9. Interview Tips  

1. **Explain the *balance compression* first.**  
   Show you understand that only non‑zero nets matter.

2. **Show the recursive thought process.**  
   “Pick the first person who still owes money and try to offset it with each creditor/ debtor.”  

3. **Highlight pruning tricks.**  
   * Skip settled indices.  
   * Only pair opposite signs.  
   * Break early if a perfect cancellation is found.

4. **Mention the DP trick if asked for a *more optimal* solution.**  
   “We can view the problem as partitioning the debt vector into zero‑sum groups; the number of groups determines the transaction count.”

5. **Time‑space complexity**  
   *Backtracking* – factorial but practically linear because `k ≤ 8`.  
   *Bitmask DP* – `O(2^k * k)` – a nice showcase of state compression.

6. **Real‑world analogy**  
   Think of the problem as a *knapsack* of money balances: we’re trying to fill a “zero‑balance” bucket.

7. **Ask clarifying questions**  
   *“Do people IDs repeat across transactions?”* – helps you justify the `Map` aggregation.  
   *“Can I use recursion?”* – ensures the interviewer is comfortable with your chosen approach.

---

## 10. Summary  

* **Optimal Account Balancing** is a small‑input, exponential‑state problem that’s a favourite for interviewers because it tests your ability to think in terms of *state compression* and *backtracking*.
* The **DFS with pruning** solution is short, easy to explain, and runs in a few microseconds for the given constraints.
* The **bitmask DP** version demonstrates clever use of subset DP and can impress interviewers who appreciate dynamic‑programming tricks.
* All three languages (Java, Python, C++) are provided; pick the one you’re most comfortable with, or keep all three handy for a multi‑language interview.

Good luck, and remember: *the key is to zero‑out balances, not to simulate every possible transaction.* Happy coding!