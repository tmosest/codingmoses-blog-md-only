---
title: LeetCode 2528. Maximize the Minimum Powered City - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 2528 – **Maximize the Minimum Powered City**

The task is to add `k` new power‑stations (all having the same range `r`) to a line of cities so that the *minimum* power of any city becomes as large as possible.

The constraints are large (`n` up to `10⁵`, `k` up to `10⁹`) – we need an algorithm that runs in `O(n log answer)` and uses only `O(n)` additional memory.



--------------------------------------------------------------------

## 2.  Algorithm Overview

| Step | What we do | Why it works |
|------|------------|--------------|
| **1.  Binary search on the answer** | Search the integer `mid` that could be the minimum power. | If we can achieve a minimum power of `mid` with at most `k` new stations, any smaller value is also achievable; if we cannot, no larger value is possible. |
| **2.  Greedy placement** | While scanning the cities left‑to‑right, if city `i` is missing power, place the missing stations as far right as possible (`pos = min(n-1, i+r)`). | Placing as far right as possible gives the station the longest “future reach”, so it can help the largest possible number of subsequent cities. |
| **3.  Sliding‑window bookkeeping** | Maintain a running sum `cur` of power stations that currently cover city `i` (existing + added).  Use an auxiliary array `add[]` where `add[x]` is the net change in `cur` that starts at city `x`.  When a new station is placed at `pos`, we do `add[pos] += need` and `add[pos+r+1] -= need` so that the station stops influencing after its range ends. | This gives us `O(1)` update per city and keeps the window sliding in linear time. |

The check procedure is therefore linear, and we binary‑search over the answer.



--------------------------------------------------------------------

## 3.  Correctness Proof

We prove that the algorithm returns the maximum possible minimum power.

---

### Lemma 1  
During the check for a fixed target `mid`, the greedy rule “place missing stations at the farthest city that can still cover the current city” never decreases the set of cities that can be powered in the future.

**Proof.**  
Let city `i` currently have `p < mid` power.  
Any city `j` that can be powered by a new station at `pos` satisfies  
`pos ∈ [i, i+r]`.  
If we place the new stations at a *left* position `pos' < pos`, the new stations still cover city `i` and also cover all cities in `[pos', pos'+r]`.  
Since `pos` is the *rightmost* position that still covers `i`, the interval `[pos', pos'+r]` is a subset of `[pos, pos+r]`.  
Therefore any city that can be powered by a station at `pos'` can also be powered by a station at `pos`.  
Thus choosing `pos` cannot reduce the set of future cities that can be powered. ∎



### Lemma 2  
For a fixed `mid`, the greedy placement uses the minimum possible number of additional stations among all feasible placements.

**Proof.**  
Consider the moment city `i` is processed.  
Because all earlier cities are already satisfied (by induction), any feasible placement that satisfies city `i` must add at least `need = mid - cur` stations that cover `i`.  
The greedy algorithm adds exactly `need` stations.  
Because of Lemma&nbsp;1 those stations are placed as far right as possible, so they do not make any previously satisfied city become unsatisfied.  
Thus no placement can use fewer stations than the greedy one. ∎



### Lemma 3  
If the greedy check fails (needs more than `k` stations), then no placement can achieve the target `mid`.

**Proof.**  
Assume there existed a placement that achieves `mid` using at most `k` stations.  
Consider the cities in order.  
Whenever the greedy algorithm decides to add stations at city `i`, any other placement must add **at least** the same number of stations at a position that also covers `i` (Lemma&nbsp;2).  
Therefore the other placement cannot use fewer stations than the greedy one for any prefix of cities.  
If the greedy one exceeds `k`, so must every other placement. ∎



### Theorem  
The binary search combined with the greedy check returns the maximum attainable minimum power.

**Proof.**  
Let `opt` be the optimal minimum power.  
For every `mid ≤ opt`, the greedy check succeeds (by Lemma 3), so the binary search will move the lower bound up to `mid`.  
For every `mid > opt`, the greedy check fails, so the binary search moves the upper bound down.  
Thus the search converges to `opt`. ∎



--------------------------------------------------------------------

## 4.  Complexity Analysis

| Step | Time | Memory |
|------|------|--------|
| Binary search (log answer) | `O(log answer)` | `O(1)` |
| Check per `mid` | `O(n)` | `O(n)` (the auxiliary array `add[]`) |
| Total | **`O(n log answer)`** | **`O(n)`** |

`answer` ≤ `sum(stations) + k` ≤ `10⁵·10⁵ + 10⁹` ≈ `10¹⁰`, so `log answer` ≤ 34.



--------------------------------------------------------------------

## 5.  Reference Implementations

All three implementations are identical in logic – only syntax differs.

> **NOTE** – All use `long` / `long long` for sums and the number of added stations to avoid overflow.  
> The helper function `can(mid)` implements the greedy check.

### 5.1  Java 17

```java
import java.util.*;

public class Solution {
    public long maxPower(int[] stations, int r, int k) {
        int n = stations.length;

        // upper bound: sum of all stations + k
        long sum = 0;
        for (int s : stations) sum += s;
        long left = 0, right = sum + k;

        while (left <= right) {
            long mid = (left + right) >>> 1;   // (left+right)/2 without overflow
            if (can(stations, r, n, mid, k)) {
                left = mid + 1;                // mid is achievable → try bigger
            } else {
                right = mid - 1;               // mid impossible → try smaller
            }
        }
        return right;   // last successful mid
    }

    private boolean can(int[] stations, int r, int n, long target, long k) {
        long[] add = new long[n + 1];     // diff array, n+1 to avoid bounds checks
        long cur = 0;                    // current power covering city i
        long used = 0;                   // stations we have already added

        for (int i = 0; i < n; i++) {
            cur += add[i] + stations[i];

            if (cur < target) {
                long need = target - cur;
                used += need;
                if (used > k) return false;          // cannot stay within budget

                cur += need;                         // add the power now

                int pos = Math.min(n - 1, i + r);    // farthest city that still covers i
                add[pos] += need;                    // start affecting at pos
                if (pos + r + 1 < n) {
                    add[pos + r + 1] -= need;        // stop affecting after the range
                }
            }
        }
        return true;
    }
}
```

### 5.2  Python 3.10

```python
class Solution:
    def maxPower(self, stations: List[int], r: int, k: int) -> int:
        n = len(stations)

        total = sum(stations)
        lo, hi = 0, total + k          # 64‑bit integers are natural in Python

        def can(mid: int) -> bool:
            add = [0] * (n + 1)        # diff array
            cur = 0                    # power currently covering this city
            used = 0

            for i in range(n):
                cur += add[i] + stations[i]
                if cur < mid:
                    need = mid - cur
                    used += need
                    if used > k:       # budget exceeded
                        return False
                    cur += need
                    pos = min(n - 1, i + r)
                    add[pos] += need
                    if pos + r + 1 < n:
                        add[pos + r + 1] -= need
            return True

        while lo <= hi:
            mid = (lo + hi) // 2
            if can(mid):
                lo = mid + 1
            else:
                hi = mid - 1
        return hi
```

### 5.3  C++17

```cpp
class Solution {
public:
    long long maxPower(vector<int>& stations, int r, long long k) {
        int n = stations.size();

        long long sum = 0;
        for (int s : stations) sum += s;
        long long lo = 0, hi = sum + k;          // 64‑bit

        auto can = [&](long long target) -> bool {
            vector<long long> add(n + 1, 0);     // diff array
            long long cur = 0, used = 0;

            for (int i = 0; i < n; ++i) {
                cur += add[i] + stations[i];
                if (cur < target) {
                    long long need = target - cur;
                    used += need;
                    if (used > k) return false;
                    cur += need;
                    int pos = min(n - 1, i + r);
                    add[pos] += need;            // starts influencing at pos
                    if (pos + r + 1 < n)
                        add[pos + r + 1] -= need; // stops after its range
                }
            }
            return true;
        };

        while (lo <= hi) {
            long long mid = (lo + hi) >> 1;
            if (can(mid)) lo = mid + 1;
            else          hi = mid - 1;
        }
        return hi;
    }
};
```

All three solutions run in the same `O(n log answer)` time and use only an `O(n)` auxiliary array.



--------------------------------------------------------------------

## 6.  Blog Article – “From LeetCode 2528 to a Job‑Interview‑Ready Solution”

> **Search Keywords**: *LeetCode 2528*, *Maximize the Minimum Powered City*, *binary search sliding window*, *Java interview*, *Python interview*, *C++ interview*, *job interview*, *software engineer*, *data‑structures*.

---

### 6.1  Problem Recap (TL;DR)

> Given `n` cities in a line, each with an initial number of power‑stations `stations[i]`.  
> A station has a coverage range `r` (i.e., it powers every city from `pos-r` to `pos+r`).  
> We can build `k` new stations of the same type.  
> **Goal:** add the new stations so that the **minimum** power among all cities is as large as possible.  
> Return that maximum attainable minimum power.

---

### 6.2  What Makes the Problem Hard?

1. **Huge `k` (≤ 10⁹).**  
   We cannot iterate over all possibilities; we must use 64‑bit arithmetic everywhere.

2. **Large `n` (≤ 10⁵).**  
   Any `O(n²)` approach will time out.  
   We need a linear or `n log n` solution.

3. **Global objective (min of all cities).**  
   The optimum is not achieved by locally maximizing each city; we must think globally.



### 6.3  Insight 1 – Binary Search on the Answer

If we can achieve a minimum power of `mid` using at most `k` new stations, then any smaller target is also achievable.  
Conversely, if we cannot achieve `mid`, no larger target is possible.

*Why binary search?*  
It transforms a global feasibility check into a monotone predicate:  
`P(mid)` = “can we achieve minimum power ≥ mid?”.

---

### 6.4  Insight 2 – Greedy Placement Is Optimal

When we encounter a city that lacks power, we **must** add some stations that cover it.  
Which city should host those new stations?  
*Greedy Rule:* put them as far right as possible (`pos = min(n-1, i+r)`).

Why does this work?

* The station at the rightmost feasible position has the longest *future reach* – it can help the greatest number of cities to its right.
* The placement never hurts already satisfied cities (they are all left of `pos`).

Mathematically, the greedy strategy is optimal (uses the fewest new stations) – see the correctness proof above.



### 6.5  Insight 3 – O(1) Window Update with a Diff Array

While scanning from left to right we maintain a running sum `cur` of power stations that currently influence the city being processed.

```
existing stations  → constant
new stations       → added by greedy, leave the window after r steps
```

We store the *influence change* that starts at each city in an auxiliary array `add[]`:

*When we put `need` new stations at `pos`*  
`add[pos]      += need`   (they start influencing at `pos`)  
`add[pos+r+1] -= need`   (they stop after their range)

During the scan we do `cur += add[i] + stations[i]`.  
All operations are `O(1)` per city → overall `O(n)`.



### 6.6  Complete Pseudocode

```
function can(mid):
    cur  = 0
    used = 0
    add  = [0] * (n+1)          // diff array

    for i in 0 … n-1:
        cur += add[i]           // apply diff that starts here
        cur += stations[i]

        if cur < mid:
            need = mid - cur
            used += need
            if used > k: return False

            cur += need
            pos = min(n-1, i+r)
            add[pos]     += need
            add[pos+r+1] -= need   // will be applied when range ends

    return True
```

Binary search on `mid` calls `can(mid)` until the answer stabilises.



### 6.7  Code Snippets

| Language | Key Points |
|----------|------------|
| **Java** | `long` for sums, `>>> 1` for midpoint to avoid overflow. |
| **Python** | Use `list` of zeros; Python’s ints are unbounded, but we still use `long`‑like logic. |
| **C++** | `long long` everywhere, `vector<long long>` for `add`. |

> See the reference implementations in the previous section for full, copy‑paste‑ready code.



--------------------------------------------------------------------

## 7.  Common Pitfalls & “What Not To Do”

| Pitfall | Explanation | Fix |
|---------|-------------|-----|
| **Using `int` for the answer** | `k` can be `10⁹` and each city may already have `10⁵` stations → sum ~ `10¹⁰`. | Always use `long` / `long long`. |
| **Omitting the `+ r + 1` in the diff update** | The station’s influence ends after `r` steps. Forgetting the `+1` means it keeps influencing one city too many, leading to over‑counting. | Add the `-need` at `pos + r + 1`. |
| **Binary search bounds off by one** | If we return `lo` after loop, we may give an unattainable target. | Return `hi` (last successful `mid`). |
| **Ignoring cities beyond the range of `add`** | When `pos + r + 1 == n`, we access `add[n]` which is outside the vector. | Allocate `add` with size `n+1` or guard with `if (pos + r + 1 < n)`. |
| **Not adding `stations[i]` after the diff** | The existing stations are always part of the power at that city. | Include `stations[i]` in the same `cur` update. |
| **Not resetting `cur` each test** | The running sum persists across binary search calls → wrong results. | Recreate `add` (or clear it) for every call of `can`. |



--------------------------------------------------------------------

## 8.  From Algorithm to Interview Success

1. **Explain the monotonicity**: why binary search works for feasibility.
2. **Describe the greedy invariant**: farthest placement → longest reach, never hurts left.
3. **Show the O(1) window trick**: diff array → sliding window update.
4. **Emphasise 64‑bit arithmetic**: point out that `k` is huge, `n` large → all sums must be `long`.
5. **Demonstrate your code**: run through a small test case mentally, stepping through `cur`, `add`, `used`.
6. **Discuss edge cases**:  
   * `r` = 0 → each city can only be powered by its own station.  
   * `k` = 0 → answer is simply `min(stations)`.  
   * All cities already have huge power → the answer is huge but still bounded by budget.

Being able to articulate these insights shows the interviewer that you can handle both *algorithmic* and *practical* constraints.



--------------------------------------------------------------------

### 7.1  Final Thought

LeetCode 2528 is a textbook example of how a seemingly simple “add‑up‑to‑k” problem hides deep algorithmic ideas.  
By turning the global objective into a binary search predicate, proving greedy optimality, and using a diff array for linear updates, we arrive at a solution that is both **efficient** and **clean**.  

Share this approach in your next interview, and watch the interviewers nod in approval!  

---



### 6.8  Article – “Did I Miss Something?”

> If you skimmed the article and see gaps, here’s a quick checklist:

1. **Did you mention 64‑bit arithmetic?**  
2. **Did you explain why greedy is optimal?**  
3. **Did you show how the diff array keeps the window O(1)?**  
4. **Did you list the most common mistakes?**  
5. **Did you use the keywords in the title?**

> If not, feel free to tweak the article accordingly.



---

**Congratulations!**  
You now understand LeetCode 2528 at a deep level, have production‑ready code in three languages, and can explain it like a seasoned engineer. Good luck in your next interview!



--------------------------------------------------------------------

### 7.  About This Write‑Up

> If you found this helpful, share it on GitHub, Stack Overflow, or a personal blog.  
> Tag it with **software‑engineering**, **interview‑prep**, **algorithms**, and **data‑structures**.  

---



---

### 8.  Summary of What You Learn

* Binary search turns a global feasibility check into a simple monotone predicate.  
* The greedy “place as far right as possible” rule is provably optimal for this problem.  
* A diff (difference) array gives an `O(1)` sliding‑window update, turning a potential `O(n²)` process into `O(n)`.  
* All implementations use 64‑bit arithmetic to stay within the budget constraints.  
* The same algorithm works in Java, Python, and C++, proving language‑agnostic elegance.



--------------------------------------------------------------------

**Happy coding!**



--------------------------------------------------------------------

### 9.  Quick FAQ

**Q:** Why is the answer always `hi` after the binary search loop?  
**A:** Because `lo` overshoots the last successful `mid`. The loop terminates when `lo > hi`; `hi` then holds the greatest feasible value.

**Q:** What if `k = 0`?  
**A:** The `can` function immediately checks if each city already satisfies `mid`; if `mid` is larger than the global minimum, it fails.

**Q:** Do we really need the `add` array size `n+1`?  
**A:** It’s a small safety margin; we could use size `n` with boundary checks, but the extra slot removes a conditional per iteration.



--------------------------------------------------------------------

### 10.  Take‑away

> *LeetCode 2528* shows that a clever combination of **binary search**, **greedy placement**, and **diff array updates** can turn a daunting global optimisation into an elegant, linear‑time algorithm.  
> Mastering these patterns will serve you well in interviews and real‑world projects alike.