---
title: LeetCode 2162. Minimum Cost to Set Cooking Time - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 2162 – Minimum Cost to Set Cooking Time  
**Languages:** Java | Python | C++  
**Difficulty:** Medium  

Below you’ll find a clean, production‑ready solution in **Java, Python, and C++**.  
After the code you’ll read a **SEO‑optimized blog post** that explains the problem, the
trickiest parts, and how you can talk about it in an interview.

---

## ✅ Code – Java

```java
class Solution {
    public int minCostSetTime(int startAt, int moveCost, int pushCost, int targetSeconds) {
        int best = Integer.MAX_VALUE;

        // minute can never exceed 99 (max microwave time)
        for (int mins = 0; mins <= 99; mins++) {
            int secs = targetSeconds - mins * 60;
            if (secs < 0 || secs > 99) continue;          // out of bounds

            int val = mins * 100 + secs;                  // 4‑digit number the microwave expects
            String digits = Integer.toString(val);        // minimal representation (no leading zeros)

            int cost = pushCost * digits.length();        // every digit we push
            int last = startAt;

            for (char ch : digits.toCharArray()) {
                int cur = ch - '0';
                if (cur != last) cost += moveCost;       // we have to move
                last = cur;                              // finger now on this digit
            }

            best = Math.min(best, cost);
        }
        return best;
    }
}
```

---

## ✅ Code – Python

```python
class Solution:
    def minCostSetTime(
        self, startAt: int, moveCost: int, pushCost: int, targetSeconds: int
    ) -> int:
        best = float("inf")

        for mins in range(100):                # 0 … 99 minutes
            secs = targetSeconds - mins * 60
            if secs < 0 or secs > 99:
                continue

            val = mins * 100 + secs
            digits = str(val)                 # minimal digit sequence

            cost = len(digits) * pushCost
            last = startAt
            for d in digits:
                cur = int(d)
                if cur != last:
                    cost += moveCost
                last = cur

            best = min(best, cost)
        return best
```

---

## ✅ Code – C++

```cpp
class Solution {
public:
    int minCostSetTime(int startAt, int moveCost, int pushCost, int targetSeconds) {
        int best = INT_MAX;

        for (int mins = 0; mins <= 99; ++mins) {
            int secs = targetSeconds - mins * 60;
            if (secs < 0 || secs > 99) continue;

            int val = mins * 100 + secs;          // 4‑digit value
            string digits = to_string(val);       // minimal representation

            int cost = digits.size() * pushCost;
            int last = startAt;
            for (char ch : digits) {
                int cur = ch - '0';
                if (cur != last) cost += moveCost;
                last = cur;
            }
            best = min(best, cost);
        }
        return best;
    }
};
```

---

## 📚 Blog Post – “The Good, The Bad, and The Ugly of Minimum Cost to Set Cooking Time”

### 1. Introduction  
LeetCode 2162 (“Minimum Cost to Set Cooking Time”) is a deceptively simple problem that
really tests your understanding of *state* and *simulation*.  If you’ve ever tried to
enter a cooking time on a microwave that only accepts four digits, you know the
challenge: **different minute/second combinations can map to the same total time**.
In a technical interview, this problem is perfect because it forces you to think about
**brute force vs. clever pruning**.

> **Why it matters for your resume**  
> Solving this puzzle demonstrates that you can:  
> • Translate a real‑world constraint into clean code.  
> • Optimize a seemingly combinatorial search to O(1) or O(100).  
> • Reason about edge cases (max 99 minutes, seconds up to 99).  

### 2. Problem Statement (in a nutshell)  

- The microwave accepts **1–4 digits**.  
- Digits are left‑padded with zeros to reach four digits, then split into minutes
  (first two) and seconds (last two).  
- You’re given `startAt`, `moveCost`, `pushCost`, and a target in seconds.  
- **Goal**: minimize total cost to set the microwave to that target.

### 3. Constraints that shape the solution  

| Parameter | Value | Insight |
|-----------|-------|---------|
| `startAt` | 0‑9 | Finger starts on a single digit. |
| `moveCost`, `pushCost` | 1‑1e5 | Up to 100 000, but they only influence arithmetic. |
| `targetSeconds` | 1‑6039 | Max is 99 min + 39 sec → at most 99 minutes. |
| Digits pushed | 1‑4 | Leads to a *minimal* representation of `minutes*100+seconds`. |

The most important observation: **the only pairs (minutes, seconds) that can produce
`targetSeconds` are those where `seconds ∈ [0, 99]`.**  
Because we can only push up to four digits, we never need to consider minutes above 99.

### 4. The Core Idea – Brute‑Force with a Twist  

A naïve approach would try every 1‑4 digit combination and simulate the push/move
operations, which is unnecessary.  
Instead, we enumerate **possible minute values** (0…99).  
For each minute we deduce the seconds that satisfy the equation:

```
minutes * 60 + seconds = targetSeconds
```

If `seconds` falls in `[0, 99]`, we’ve found a *valid* combination.  
That reduces the search space to **≤ 100** possibilities – *constant time*.

### 4. Computing the Cost Efficiently  

The cost formula is straightforward:

```
total pushes = len(digits) * pushCost
moves = number of times the pressed digit differs from the previous one
        (also from the starting digit for the first push)
```

We can compute it in O(len(digits)) per combination (≤ 4).  
The overall runtime is therefore **O(100)**, which is effectively constant for interview
purposes.

#### Why we use the *minimal* string  
When `minutes*100 + seconds` is 100, the string is `"100"` (3 digits).  
Pushing `"0100"` (4 digits) would only add a push and possibly a move, never reducing
cost.  Thus, we always use the *shortest* digit sequence.

### 5. Common Pitfalls (the “Bad” side)

| Pitfall | Why it breaks |
|---------|---------------|
| **Forgetting to skip invalid seconds** (e.g., seconds > 59) | Leads to incorrect “time” or out‑of‑range push. |
| **Adding move cost for every digit** regardless of repetition | Over‑estimates cost; the finger stays on the same key. |
| **Treating leading zeros as mandatory** | Forces unnecessary pushes. |
| **Ignoring the 99‑minute cap** | May iterate beyond the microwave’s limits. |

### 6. Edge Cases & “The Ugly” – When the Problem Gets Trickier  

- **Target = 39 seconds** → minutes = 0, seconds = 39 → digits `"39"` (2 pushes).  
  The best strategy may involve pushing `"039"` (3 pushes) if `moveCost` < `pushCost`,
  but our algorithm always picks the minimal digits, so the extra push never helps.
- **`startAt` equals first digit** → first push incurs *no* move cost.  
  We need to check this specially to avoid adding an unnecessary move.
- **Repeated digits** – e.g., `"11"` or `"2222"` – no move cost between them.
  The `last` variable in the solution keeps track of the current finger position.

### 7. Alternative Approaches

1. **All 4‑digit combinations** (0000‑9999) and filter by `minutes*60+seconds`.  
   Complexity: O(10 000) – acceptable but overkill.
2. **Mathematical shortcut** – derive the exact number of moves without building
   the string.  
   Works but is error‑prone for interview explanation.
3. **Dynamic programming** – overkill; the search space is tiny.

> **Verdict:** The “brute‑force‑with‑pruning” (the solution above) is the sweet spot.

### 8. “The Good” – What you should highlight in an interview  

- **Simplicity**: O(100) loops, clear cost calculation.  
- **Correctness**: handles all edge cases by construction.  
- **Scalability**: O(1) time with respect to the input size (since the problem is bounded).

### 9. “The Bad” – Things to avoid  

- Writing a full simulation of the microwave’s state machine.  
- Ignoring the possibility of minutes > 9 leading to two‑digit minute representation.  
- Adding move cost for each digit without checking repetition.

### 10. “The Ugly” – A sub‑optimal implementation  

```cpp
// Bad: O(10000) brute force, no pruning, heavy string ops
for (int x = 1; x <= 9999; ++x) {
    int mins = x / 100;
    int secs = x % 100;
    if (mins * 60 + secs != targetSeconds) continue;
    // simulate each keypress with naive loops
}
```

This code passes but is unnecessary work and hard to reason about.

### 11. How to Present It in an Interview

1. **State the observation** – “Only minutes up to 99 and seconds up to 99 can produce
   the target.”
2. **Explain the enumeration** – “We iterate minutes 0…99, compute the required
   seconds, and discard impossible pairs.”
3. **Show the cost formula** – “Push cost is always `len*pushCost`.  
   Move cost is only when the pressed digit changes.”
4. **Mention complexity** – “O(100) time, O(1) memory.”
5. **Answer a common follow‑up** – “What if the microwave allowed 5 digits?”  
   → You’d just increase the loop bounds accordingly.

### 12. Final Thoughts – 🚀 How to Use This on Your Resume  

- **Technical Keywords**: *brute force*, *state machine*, *dynamic programming*, *optimisation*, *real‑world constraints*.
- **STAR Format**:  
  **S**ituation – Microwave accepts 1‑4 digits.  
  **T**ask – Minimise cost to reach a target time.  
  **A**ction – Enumerate valid minute/second pairs, compute minimal cost.  
  **R**esult – O(100) time, perfect for interview & resume.

> “I solved LeetCode 2162 by transforming a microwave’s quirky input format into
> a concise O(100) algorithm. The key was to only consider the minimal digit
> representation for each (minute, second) pair.”  

With this solution in hand and the interview‑friendly explanation above, you’ll be ready to
show off your problem‑solving chops in any technical interview or on your résumé. Happy coding!