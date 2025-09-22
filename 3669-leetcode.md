---
title: LeetCode 3669. Balanced K-Factor Decomposition - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Balanced K‑Factor Decomposition – The Good, The Bad, and The Ugly  
**A LeetCode‑style interview walkthrough (Java | Python | C++)**

> *If you’re preparing for a software‑engineering interview, mastering this problem can boost your odds of landing a job. It’s a “medium” problem on LeetCode, but the techniques you learn are useful for a wide range of interview questions.*

---

### 1. Problem Recap

> **Balanced K‑Factor Decomposition**  
> Given two integers `n` and `k`, split `n` into exactly `k` positive integers whose product equals `n`.  
> Return *any* split that **minimises the difference between the largest and smallest number** in the split.

*Examples*  

| n | k | optimal split | min‑max diff |
|---|---|---------------|--------------|
| 100 | 2 | `[10, 10]` | 0 |
| 44 | 3 | `[2, 2, 11]` | 9 |

**Constraints**

* `4 ≤ n ≤ 10⁵`  
* `2 ≤ k ≤ 5`  
* `k` < total number of positive divisors of `n`

Because `k` is tiny, a backtracking solution is perfectly viable.

---

### 2. The Good – Why a Simple Backtracking Works

| Advantage | Why it matters in an interview |
|-----------|--------------------------------|
| **Intuitive** | Recursion mirrors the definition: “pick a factor, recurse on the remaining product.” |
| **Minimal Boilerplate** | No fancy data structures, just lists/arrays. |
| **Deterministic Result** | By forcing a non‑decreasing order (`start` parameter), we avoid duplicate factorizations. |
| **Pruning** | If the current partial product exceeds `n`, the branch dies immediately. |
| **Readability** | Clean, easy to explain to the interviewer. |

**Algorithm Overview**

1. **Recursive function** `dfs(remaining, k, start, current)`  
   * `remaining` – what still needs to be factored  
   * `k` – how many numbers still need to be chosen  
   * `start` – smallest allowed divisor (enforces non‑decreasing order)  
   * `current` – list of numbers chosen so far  
2. **Base case** – `k == 0`. If `remaining == 1`, we have a full factorisation. Compute its max‑min diff and keep it if it improves on the best so far.  
3. **Loop** – iterate `i` from `start` to `remaining` inclusive.  
   * If `remaining % i == 0`, add `i` to `current` and recurse with `remaining / i`, `k-1`, `i`.  
   * Backtrack after the recursive call.  

Because `k ≤ 5`, the depth of recursion is tiny, and the number of recursive calls is comfortably below the limits even for `n = 10⁵`.

---

### 3. The Bad – Potential Pitfalls

| Pitfall | What it looks like | Fix |
|---------|--------------------|-----|
| **Duplicate factorizations** | Visiting `[2, 11, 2]` and `[2, 2, 11]` separately | Use `start` to enforce sorted order |
| **High runtime for large `n`** | Enumerating all numbers 1…n in each recursion | Pre‑compute divisors or loop only up to `sqrt(remaining)` |
| **Overflow or wrong type** | Using `int` for product when `n` is large | Stick to `int` for product (≤10⁵) but be careful in languages with 32‑bit overflow |
| **Not handling `k` > number of divisors** | Returning an empty array | Problem guarantees a solution, but defensively check |
| **Ignoring the “minimise difference” objective** | Returning the first valid split | Track best diff and update only if improvement |

---

### 4. The Ugly – Edge‑Case Traps & Optimisations

#### a) Non‑Prime, Highly Composite `n`

When `n` has many small factors (e.g. `n = 2⁸ = 256`), the recursion tree can balloon.  
**Optimization:**  
* Instead of looping to `remaining`, generate the *divisors* of `remaining` once, store them in a list, and iterate that list.  
* Since `k ≤ 5`, the extra overhead of divisor generation is negligible but reduces the constant factor dramatically.

#### b) Large `k` in a Theoretical Sense

If the constraints were relaxed (say `k ≤ 10`), memoisation would become essential.  
**Memoisation Idea**  
```
key = (remaining, k, start)
store best difference found for this state
```
However, for the current problem this is unnecessary.

#### c) Stack Overflow

Recursion depth is at most 5, so this is safe in Java, Python, and C++.  
If you had a larger `k`, iterative DFS or BFS would be safer.

---

### 5. Full Solutions

Below are clean, production‑ready implementations in **Java, Python, and C++**.  
All three use the same backtracking strategy described above.

---

#### 5.1 Java

```java
import java.util.*;

public class Solution {

    private List<Integer> best = new ArrayList<>();
    private int bestDiff = Integer.MAX_VALUE;

    private void dfs(int remaining, int k, int start, List<Integer> curr) {
        if (k == 0) {
            if (remaining == 1) {                     // full factorisation
                int mx = Collections.max(curr);
                int mn = Collections.min(curr);
                int diff = mx - mn;
                if (diff < bestDiff) {                // better solution
                    bestDiff = diff;
                    best = new ArrayList<>(curr);
                }
            }
            return;
        }

        // only divisors matter; we can stop at remaining
        for (int i = start; i <= remaining; i++) {
            if (remaining % i == 0) {
                curr.add(i);
                dfs(remaining / i, k - 1, i, curr);
                curr.remove(curr.size() - 1); // backtrack
            }
        }
    }

    public int[] minDifference(int n, int k) {
        dfs(n, k, 1, new ArrayList<>());
        int[] res = new int[best.size()];
        for (int i = 0; i < best.size(); i++) res[i] = best.get(i);
        return res;
    }

    // Example usage
    public static void main(String[] args) {
        System.out.println(Arrays.toString(new Solution().minDifference(100, 2)));
        System.out.println(Arrays.toString(new Solution().minDifference(44, 3)));
    }
}
```

**Time Complexity** –  
In the worst case the recursion explores all factorizations of `n` into `k` parts.  
With `k ≤ 5` this is far below `O(n)` for the given limits.  
**Space Complexity** – O(k) for recursion stack + O(k) for `best`.

---

#### 5.2 Python

```python
from typing import List

class Solution:
    def __init__(self):
        self.best = []
        self.best_diff = float('inf')

    def dfs(self, remaining: int, k: int, start: int, curr: List[int]) -> None:
        if k == 0:
            if remaining == 1:
                diff = max(curr) - min(curr)
                if diff < self.best_diff:
                    self.best_diff = diff
                    self.best = curr.copy()
            return

        for i in range(start, remaining + 1):
            if remaining % i == 0:
                curr.append(i)
                self.dfs(remaining // i, k - 1, i, curr)
                curr.pop()     # backtrack

    def minDifference(self, n: int, k: int) -> List[int]:
        self.dfs(n, k, 1, [])
        return self.best


# Example usage
if __name__ == "__main__":
    sol = Solution()
    print(sol.minDifference(100, 2))   # [10, 10]
    print(sol.minDifference(44, 3))    # [2, 2, 11]
```

---

#### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> best;
    int bestDiff = INT_MAX;

    void dfs(int remaining, int k, int start, vector<int>& curr) {
        if (k == 0) {
            if (remaining == 1) {
                int mx = *max_element(curr.begin(), curr.end());
                int mn = *min_element(curr.begin(), curr.end());
                int diff = mx - mn;
                if (diff < bestDiff) {
                    bestDiff = diff;
                    best = curr;
                }
            }
            return;
        }

        for (int i = start; i <= remaining; ++i) {
            if (remaining % i == 0) {
                curr.push_back(i);
                dfs(remaining / i, k - 1, i, curr);
                curr.pop_back();           // backtrack
            }
        }
    }

    vector<int> minDifference(int n, int k) {
        vector<int> curr;
        dfs(n, k, 1, curr);
        return best;
    }
};

int main() {
    Solution s;
    auto r1 = s.minDifference(100, 2);
    auto r2 = s.minDifference(44, 3);
    for (int x : r1) cout << x << ' '; cout << '\n';
    for (int x : r2) cout << x << ' '; cout << '\n';
}
```

---

### 6. How to Explain It in an Interview

> **Start**: “Let’s think of `n` as a product of `k` numbers. If we pick a factor `f`, we’re left with `n/f`, still to be factored into `k-1` numbers.”  
> **Proceed**: “We’ll use a depth‑first search. To avoid duplicates, we only pick divisors in non‑decreasing order.”  
> **Show**: “When we reach `k = 0`, we compute the max‑min diff. We keep the best split.”  

Keep the focus on **why the recursion works** (definition‑driven), **how we prune** (only valid divisors), and **how we meet the objective** (tracking `bestDiff`).  

---

### 7. Takeaways & Broader Applications

* **Backtracking** is a staple for “partition / subset” interview problems.  
* Enforcing a sorted order (`start`) is a generic trick to eliminate duplicates in combinatorial DFS.  
* When a problem adds an optimisation criterion (minimise/maximise something), simply “record the best so far” during DFS.  
* For larger inputs, the same skeleton can be enhanced with **divisor pre‑computation** or **memoisation**.

---

## 🎯 Quick Checklist Before You Submit

- [ ] Did you enforce non‑decreasing order to avoid duplicates?  
- [ ] Did you compute and update the best max‑min difference?  
- [ ] Did you use backtracking (push/pop) to keep memory low?  
- [ ] Did you test your solution on the examples and edge‑cases?  

---

### Final Words

> A tiny‑parameter backtracking solution is a powerful interview tool.  
> It teaches you recursion, pruning, and optimisation—all while solving a real LeetCode problem.  

**Good luck**—and remember: *explain your approach, watch for duplicates, and keep it simple.*  

--- 

#### References

* LeetCode Problem: **Balanced K‑Factor Decomposition**  
* Tags: `Backtracking`, `Recursion`, `Divide and Conquer`

--- 

*If you found this article helpful, share it with your peers and keep exploring the “Balanced K‑Factor Decomposition” problem. Your interview prep just got a little more robust!*