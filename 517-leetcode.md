---
title: LeetCode 517. Super Washing Machines - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Super Washing Machines – LeetCode 517  
**A deep dive into a “Hard” interview problem – Java, Python, and C++ solutions, pitfalls, and SEO‑friendly blog content**

---

## Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Key Insights & Edge Cases](#key-insights--edge-cases)  
3. [Algorithm in a Nutshell](#algorithm-in-a-nutshell)  
4. [Complexity Analysis](#complexity-analysis)  
5. [Implementation Details](#implementation-details)  
   - Java  
   - Python  
   - C++  
6. [Common Mistakes & “Ugly” Pitfalls](#common-mistakes--ugly-pitfalls)  
7. [Alternative Approaches & Variations](#alternative-approaches--variations)  
8. [Why This Problem Rocks in Interviews](#why-this-problem-rocks-in-interviews)  
9. [Wrap‑Up & Take‑away](#wrap-up--take-away)  

---

## Problem Overview <a name="problem-overview"></a>

> **LeetCode 517 – Super Washing Machines**  
> You have `n` super washing machines on a line.  
> Each machine contains some number of dresses (`machines[i]`).  
> **Move rule**: In one move, you can pick any `m` machines (1 ≤ m ≤ n) and each selected machine passes *one* dress to one of its adjacent machines simultaneously.  
>  
> **Goal**: Make every machine contain the same number of dresses using the minimum number of moves.  
> If it is impossible, return `-1`.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[1,0,5]` | `3` | After 3 moves, each machine has 2 dresses. |
| `[0,3,0]` | `2` | After 2 moves, each machine has 1 dress. |
| `[0,2,0]` | `-1` | Impossible – total dresses (2) is not divisible by 3. |

**Constraints**

- `1 <= n <= 10⁴`
- `0 <= machines[i] <= 10⁵`

---

## Key Insights & Edge Cases <a name="key-insights--edge-cases"></a>

1. **Feasibility Check**  
   - If the total number of dresses isn’t divisible by `n`, equal distribution is impossible → return `-1`.

2. **Convert to “Differences”**  
   - Compute the target dresses per machine: `avg = total / n`.  
   - Replace each `machines[i]` with `diff = machines[i] - avg`.  
   - A positive `diff` means that machine has *extra* dresses to send.  
   - A negative `diff` means it needs dresses from the left side.

3. **Cumulative Flow**  
   - As we sweep from left to right, keep a running sum `cur`.  
   - `cur` represents the net number of dresses that must flow **across** the current boundary (between machine `i` and `i+1`).  
   - The minimum moves needed up to index `i` is `max(|cur|, diff)`.

4. **Final Answer**  
   - The answer is the maximum of all `max(|cur|, diff)` values seen during the sweep.

5. **Edge Cases**  
   - `n == 1` → answer is always `0`.  
   - All machines already equal → answer `0`.  
   - Large numbers → use 64‑bit integers to avoid overflow in Java/C++.

---

## Algorithm in a Nutshell <a name="algorithm-in-a-nutshell"></a>

```text
1. total = sum(machines)
2. if total % n != 0: return -1
3. avg   = total / n
4. cur   = 0            // cumulative difference
5. ans   = 0            // maximum moves seen
6. for each machine m in machines:
        diff = m - avg
        cur += diff
        ans = max(ans, abs(cur), diff)
7. return ans
```

*Why `abs(cur)`?*  
`cur` is the number of dresses that must cross the boundary *to the right* (if positive) or *to the left* (if negative).  
The absolute value is the total number of dresses that must cross that boundary, i.e., the minimum moves needed there.

*Why `diff`?*  
If a machine has a large positive `diff`, it must send out that many dresses immediately, even if the cumulative sum is small.

---

## Complexity Analysis <a name="complexity-analysis"></a>

| Operation | Time | Space |
|-----------|------|-------|
| Sum & sweep | `O(n)` | `O(1)` |
| Overall | `O(n)` | `O(1)` |

The algorithm is linear and uses constant auxiliary memory, satisfying the constraints even for `n = 10⁴`.

---

## Implementation Details <a name="implementation-details"></a>

### Java

```java
class Solution {
    public int findMinMoves(int[] machines) {
        int n = machines.length;
        long total = 0;
        for (int m : machines) total += m;

        if (total % n != 0) return -1;

        int avg = (int) (total / n);
        long cur = 0;   // cumulative difference
        int ans = 0;

        for (int m : machines) {
            int diff = m - avg;
            cur += diff;
            int need = Math.max((int) Math.abs(cur), diff);
            ans = Math.max(ans, need);
        }
        return ans;
    }
}
```

> **Why `long`?**  
> `total` can be up to `10⁹`, so using `long` prevents overflow when summing.

---

### Python

```python
class Solution:
    def findMinMoves(self, machines: List[int]) -> int:
        n = len(machines)
        total = sum(machines)

        if total % n != 0:
            return -1

        avg = total // n
        cur = 0
        ans = 0

        for m in machines:
            diff = m - avg
            cur += diff
            need = max(abs(cur), diff)
            ans = max(ans, need)

        return ans
```

---

### C++

```cpp
class Solution {
public:
    int findMinMoves(vector<int>& machines) {
        int n = machines.size();
        long long total = 0;
        for (int m : machines) total += m;

        if (total % n != 0) return -1;

        int avg = static_cast<int>(total / n);
        long long cur = 0;    // cumulative difference
        int ans = 0;

        for (int m : machines) {
            int diff = m - avg;
            cur += diff;
            int need = std::max(static_cast<int>(std::abs(cur)), diff);
            ans = std::max(ans, need);
        }
        return ans;
    }
};
```

---

## Common Mistakes & “Ugly” Pitfalls <a name="common-mistakes--ugly-pitfalls"></a>

| Mistake | Why it fails | Fix |
|---------|--------------|-----|
| **Ignoring the feasibility check** | Returning an arbitrary number when total % n != 0 | Add early `if` guard. |
| **Using `int` for the total sum** | Overflow for large inputs | Use 64‑bit (`long` / `long long`). |
| **Taking `max(cur, diff)` without abs** | Negative `cur` not considered → underestimates moves | Use `abs(cur)` in the max. |
| **Assuming moves = `max(cur)` only** | Misses cases where a machine has a large surplus that must be shipped out immediately | Include `diff` in the max. |
| **Re‑using the original `machines` array to store differences** | Confuses future iterations and makes debugging hard | Create a separate variable or recompute on the fly. |

---

## Alternative Approaches & Variations <a name="alternative-approaches--variations"></a>

1. **Prefix Sum Approach**  
   - Compute prefix sums of differences.  
   - The answer is `max(maxPrefix, abs(minPrefix))`.  
   - Equivalent to the sweep method but expressed differently.

2. **Simulation (inefficient)**  
   - Simulate each move step by step.  
   - Works only for very small `n` (e.g., interview practice) but has `O(n * moves)` runtime → **not recommended** for production.

3. **Two‑Pointer / Queue**  
   - Keep a queue of indices that need dresses.  
   - Pop from left, push to right.  
   - Again linear but more code‑heavy.

4. **Dynamic Programming on Boundaries**  
   - Treat each boundary as a DP state: how many dresses cross it.  
   - DP transition identical to the cumulative flow logic.

---

## Why This Problem Rocks in Interviews <a name="why-this-problem-rocks-in-interviews"></a>

- **Shows mathematical thinking** – converting to differences is a classic *balance* trick.
- **Linear time & constant space** – ideal for interview constraints.
- **Edge‑case heavy** – forces candidates to think about feasibility, overflow, and negative values.
- **Extremely common interview topic** – often asked in tech‑company onsite rounds (Google, Facebook, Amazon, Microsoft).

> *Interview‑style question*:  
> “You’re given `machines` and a move rule. How would you compute the minimum number of moves without simulating each step?”  
> The interviewer expects the *cumulative flow* reasoning.

---

## Wrap‑Up & Take‑away <a name="wrap-up--take-away"></a>

- The “Super Washing Machines” problem boils down to a **cumulative flow of differences**.  
- The minimal number of moves equals the maximum of two quantities at every boundary:  
  1. The absolute cumulative difference that must cross the boundary.  
  2. The net surplus/deficit of the current machine itself.
- A single linear sweep gives the answer in `O(n)` time and `O(1)` extra space.  
- Avoid common pitfalls (feasibility check, 64‑bit sums, `abs(cur)`).  

**For recruiters:**  
Show the candidate how the algorithm runs on the provided examples, highlight the feasibility guard, and ask them to reason about edge cases.

**For interviewees:**  
Practice the sweep on paper; once you’re comfortable with the “diff / cur” trick, you’ll ace this problem in any language.

---

### SEO‑Friendly Meta Description  
> Master LeetCode 517 – Super Washing Machines. Learn Java, Python, and C++ solutions, algorithm insights, edge‑case handling, and common pitfalls in this comprehensive guide.

---

Happy coding – and good luck on your next coding interview!