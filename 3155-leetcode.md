---
title: LeetCode 3155. Maximum Number of Upgradable Servers - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ 3155. Maximum Number of Upgradable Servers – Full‑Stack Solution  
> **Problem type:** Medium – Binary Search  
> **Languages:** Java 17 | Python 3 | C++17  
> **LeetCode Link:** https://leetcode.com/problems/maximum-number-of-upgradable-servers/

---

### 1. Problem Recap

You have `n` data centers.  
For data center `i` you know:

| array | meaning |
|-------|---------|
| `count[i]` | # of servers you own |
| `upgrade[i]` | money needed to upgrade **one** server |
| `sell[i]` | money you get when selling **one** server |
| `money[i]` | money you already have |

You may sell any number of servers (`0 … count[i]`) **inside that data center only**.  
After selling, you can upgrade as many of the remaining servers as your money allows.

**Goal** – For every data center output the *maximum* number of servers that can be upgraded.



---

### 2. Observations & Intuition

* If we decide to upgrade `x` servers, we must keep at least `x` servers unsold.  
  Hence `x ≤ count[i] - s`, where `s` = # sold.

* The total money after selling `s` servers is `money[i] + s * sell[i]`.  
  To upgrade `x` servers we need `x * upgrade[i]` money.

* We must find the largest `x` such that there exists an `s` satisfying **both**  
  `x ≤ count[i] - s` **and** `money[i] + s * sell[i] ≥ x * upgrade[i]`.

The key is that for a *fixed* `x` the smallest `s` that satisfies the money constraint is  

```
s_min = ceil( (x * upgrade[i] - money[i]) / sell[i] )
```

If `s_min` is negative we can set it to `0`.  
The constraint `x ≤ count[i] - s` turns into `s ≤ count[i] - x`.

So for a given `x` we just need to check:

```
max(0, ceil((x*upgrade - money)/sell)) ≤ count - x
```

If the inequality holds, `x` servers are upgradeable.  
Otherwise, they’re not.

Since the answer is an integer between `0` and `count[i]`, we can binary‑search over `x`.  
Each check is O(1). The overall complexity per data center is `O(log count[i])`,  
and for all data centers `O(n log maxCount)` – easily fast enough for `n, count ≤ 10⁵`.

---

### 3. Algorithm (Pseudo)

```
for each data center i
    lo = 0
    hi = count[i]
    while lo < hi
        mid = (lo + hi + 1) / 2   // upper mid to avoid infinite loop
        // compute required sell amount
        required = max(0, ceil((mid * upgrade[i] - money[i]) / sell[i]))
        if required <= count[i] - mid
            lo = mid              // mid is achievable
        else
            hi = mid - 1
    answer[i] = lo
```

`ceil(a / b)` in integer arithmetic: `(a + b - 1) // b` (with `a ≥ 0`).  
All intermediate products are up to `10¹⁰`, so `long` (Java / C++) or Python `int` is safe.

---

### 4. Correctness Proof

We prove that the algorithm returns the maximal upgradeable servers for each data center.

**Lemma 1**  
For any integer `x` (0 ≤ x ≤ count),  
`x` servers can be upgraded iff  

```
max(0, ceil((x*upgrade - money)/sell)) ≤ count - x
```

*Proof.*  
- Necessity:  
  Let `s` be the actual number of sold servers.  
  `x` upgrade requires `x ≤ count - s` ⇒ `s ≤ count - x`.  
  Money after selling: `money + s*sell ≥ x*upgrade` ⇒  
  `s ≥ ceil((x*upgrade - money)/sell)`.  
  Thus the smallest possible `s` that satisfies the money condition is  
  `s_min = max(0, ceil((x*upgrade - money)/sell))`.  
  For a feasible solution we must have `s_min ≤ s ≤ count - x`.  
  Hence `s_min ≤ count - x`.  

- Sufficiency:  
  If `s_min ≤ count - x` choose `s = s_min`.  
  Then `s ≤ count - x` ⇒ we keep at least `x` servers.  
  And by definition of `s_min` we have enough money:  
  `money + s_min*sell ≥ x*upgrade`.  
  Thus `x` servers can be upgraded. ∎



**Lemma 2**  
For each data center the binary search in the algorithm returns the largest `x`
satisfying Lemma 1.

*Proof.*  
The predicate `P(x)` defined as “`x` is upgradeable” is monotonic:
if `x` servers are upgradeable then any `y < x` are upgradeable as well
(by selling the same or more servers).  
Binary search on a monotone predicate always returns the maximum `x`
with `P(x)` true.  
The algorithm uses the upper mid formula `(lo+hi+1)/2`, so it converges to the
greatest feasible `x`. ∎



**Theorem**  
For every data center the algorithm outputs the maximum possible number of
upgraded servers.

*Proof.*  
By Lemma 1 the predicate used in the binary search is exactly the feasibility
condition.  
By Lemma 2 the binary search yields the largest `x` that satisfies this
condition, which by definition is the maximum upgradeable servers. ∎



---

### 5. Complexity Analysis

| Operation | Cost |
|-----------|------|
| Per data center binary search | `O(log count[i])` |
| All data centers | `O(n log maxCount)` |
| Memory | `O(1)` extra per data center, `O(n)` for the answer array |

With `n, count ≤ 10⁵`, this easily fits within the limits.

---

### 6. Reference Implementations

> **Tip:** Use `long` (Java / C++) or `int` (Python) for intermediate products to avoid overflow.

---

#### 6.1 Java 17

```java
import java.util.*;

public class Solution {
    public int[] maxUpgrades(int[] count, int[] upgrade, int[] sell, int[] money) {
        int n = count.length;
        int[] ans = new int[n];
        for (int i = 0; i < n; i++) {
            int lo = 0, hi = count[i];
            while (lo < hi) {
                int mid = (lo + hi + 1) >>> 1; // upper mid
                long need = (long) mid * upgrade[i] - money[i];
                long requiredSell = 0;
                if (need > 0) {
                    requiredSell = (need + sell[i] - 1) / sell[i];
                }
                if (requiredSell <= (long) count[i] - mid) {
                    lo = mid;
                } else {
                    hi = mid - 1;
                }
            }
            ans[i] = lo;
        }
        return ans;
    }
}
```

---

#### 6.2 Python 3

```python
class Solution:
    def maxUpgrades(self, count, upgrade, sell, money) -> list[int]:
        ans = []
        for cnt, up, sl, mo in zip(count, upgrade, sell, money):
            lo, hi = 0, cnt
            while lo < hi:
                mid = (lo + hi + 1) // 2
                need = mid * up - mo
                required_sell = 0 if need <= 0 else (need + sl - 1) // sl
                if required_sell <= cnt - mid:
                    lo = mid
                else:
                    hi = mid - 1
            ans.append(lo)
        return ans
```

---

#### 6.3 C++17

```cpp
class Solution {
public:
    vector<int> maxUpgrades(vector<int>& count,
                            vector<int>& upgrade,
                            vector<int>& sell,
                            vector<int>& money) {
        int n = count.size();
        vector<int> ans(n);
        for (int i = 0; i < n; ++i) {
            int lo = 0, hi = count[i];
            while (lo < hi) {
                int mid = (lo + hi + 1) / 2;   // upper mid
                long long need = 1LL * mid * upgrade[i] - money[i];
                long long requiredSell = 0;
                if (need > 0) {
                    requiredSell = (need + sell[i] - 1) / sell[i];
                }
                if (requiredSell <= 1LL * count[i] - mid) {
                    lo = mid;
                } else {
                    hi = mid - 1;
                }
            }
            ans[i] = lo;
        }
        return ans;
    }
};
```

---

### 7. Edge Cases & Pitfalls

| Edge case | What to watch for | Fix |
|-----------|-------------------|-----|
| `sell[i]` very small, but `money[i]` already enough | Division by zero? | `sell[i] >= 1` by constraints, but guard with `if (need <= 0)` |
| Very large products (up to 10¹⁰) | 32‑bit overflow | Use 64‑bit (`long` / `long long`) |
| `count[i]` = 0 | Binary search still works (0..0) | No special handling needed |
| All servers are already affordable to upgrade | Should return `count[i]` | Binary search ends at `hi` = `count[i]` |

---

### 8. Testing Strategy

1. **Sample tests** – Provided in the problem statement.  
2. **Edge tests**  
   - `count = [0]` → answer `[0]`  
   - `money` large enough to upgrade all → answer `count`  
   - `sell` = 1, `upgrade` = 1, `money` = 0, `count` = 10 → answer `0` (need to sell at least `10` to upgrade `0`?)  
3. **Randomized tests** – Generate random values (within constraints) and verify with brute force (small `count`).  
4. **Stress tests** – Max input size (`n = 10⁵`, each `count = 10⁵`) to ensure time/memory limits are respected.

---

### 9. Summary

* **Problem:** Maximize upgraded servers per data center by selling some servers.  
* **Key idea:** Binary search over the number of upgraded servers and a simple feasibility check using integer arithmetic.  
* **Complexity:** `O(n log maxCount)` time, `O(1)` extra space.  
* **Result:** Ready‑to‑copy solutions in Java, Python, and C++.

---

### 10. SEO‑Optimized Takeaway

If you’re preparing for a **software engineer interview** and want to showcase your mastery of **binary search** and **algorithmic reasoning**, this problem is a perfect showcase. Use the code snippets above in your portfolio, highlight the **time/space analysis**, and be ready to explain the **mathematical proof**—all of which demonstrate your depth of understanding.

Good luck landing that dream job! 🚀

--- 

> **Keywords**: Maximum Number of Upgradable Servers, LeetCode 3155, Binary Search, Java solution, Python solution, C++ solution, interview question, algorithm analysis, coding interview, software engineer, job interview, programming challenge, data center optimization.