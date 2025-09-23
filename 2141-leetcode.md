---
title: LeetCode 2141. Maximum Running Time of N Computers - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Solution Overview – LeetCode 2141: Maximum Running Time of N Computers  

**Problem statement**  
You have `n` computers and an array `batteries`.  
`batteries[i]` is the number of minutes a single battery can power a computer.  
At any integer minute you may swap batteries between computers (swapping takes no time).  
Your goal is to run all `n` computers **simultaneously** for as many minutes as possible.  
Return that maximum number of minutes.

> **Constraints**  
> * `1 ≤ n ≤ batteries.length ≤ 10^5`  
> * `1 ≤ batteries[i] ≤ 10^9`  

The key insight:  
During any interval of length `T`, *each* battery can contribute at most `T` minutes of power, even if it could run longer.  
Thus, the total contribution of all batteries to a `T`‑minute run is at most `min(battery, T)` for each battery.

We’ll present two popular approaches, both with a time complexity of `O(m log m)` where `m = batteries.length`.  
The **sort‑and‑trim** method is easier to understand and implement, while the **binary search** method is a little more “algorithmic” and shows a different angle.  

Below you’ll find ready‑to‑copy implementations in **Java**, **Python**, and **C++**.

---

## ✅ 1️⃣ Sort‑and‑Trim Solution (O(m log m))

The idea is to keep all batteries except the ones that would be “wasted” if they are too powerful compared to the current average runtime.

1. **Sort** the battery array.
2. Compute the total energy `sum`.
3. Starting from the largest battery, keep removing it from consideration while it is larger than the current feasible average `sum / (n - k)`.  
   *When a battery is removed, we reduce `sum` and `n` by one.*
4. After trimming, the answer is simply `sum / n` (integer division – everything fits perfectly now).

### Why It Works

- If the strongest battery has more power than the average (`sum / n`), it would be used alone for that average time and the rest of the batteries would be idle.  
  Removing it and recomputing the average gives a tighter bound.
- We keep doing this until the strongest remaining battery can share the load with the rest of the batteries without wasting any energy.  
  At that point every battery can be used for exactly `sum / n` minutes, which is optimal.

---

## 🧪 2️⃣ Binary Search Solution (O(m log sum))

Alternative but equally fast approach:

1. Compute the total energy `total`.
2. Set `low = 0`, `high = total / n`. (No run can exceed this upper bound.)
3. While `low ≤ high`:  
   * Check if a run of `mid` minutes is possible.  
   * If yes, store it and search higher; otherwise search lower.
4. To **check** a candidate `mid`:  
   * Each battery contributes `min(battery, mid)` minutes.  
   * Sum all contributions; if the sum is at least `n * mid`, the run is feasible.

Both approaches are `O(m log m)` but the binary‑search version uses only a single pass over the array per iteration.

---

## 📄 3️⃣ Code Implementations

Below are clean, commented solutions in three languages.  
All use 64‑bit arithmetic (`long` / `long long`) because the total sum can exceed 32 bits.

### Java

```java
import java.util.Arrays;

public class Solution {
    /**
     * Return the maximum number of minutes all `n` computers can run simultaneously.
     * @param n number of computers
     * @param batteries array of battery runtimes
     * @return maximum simultaneous runtime (minutes)
     */
    public long maxRunTime(int n, int[] batteries) {
        Arrays.sort(batteries);                     // O(m log m)

        long sum = 0;
        for (int b : batteries) sum += b;           // total energy

        int idx = batteries.length - 1;             // start from largest
        while (idx >= 0 && batteries[idx] > sum / n) {
            sum -= batteries[idx];                  // remove too powerful battery
            idx--;                                  // shrink the usable array
            n--;                                    // one fewer computer
        }

        return sum / n;                              // final answer
    }
}
```

### Python

```python
def maxRunTime(n: int, batteries: list[int]) -> int:
    """
    Return the maximum number of minutes all `n` computers can run simultaneously.
    """
    batteries.sort()                              # O(m log m)

    total = sum(batteries)
    i = len(batteries) - 1                         # index of largest battery

    # Trim batteries that would be wasted
    while i >= 0 and batteries[i] > total // n:
        total -= batteries[i]
        i -= 1
        n -= 1

    return total // n
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maxRunTime(int n, vector<int>& batteries) {
        sort(batteries.begin(), batteries.end());   // O(m log m)

        long long sum = 0;
        for (int b : batteries) sum += b;           // total energy

        int idx = batteries.size() - 1;
        while (idx >= 0 && batteries[idx] > sum / n) {
            sum -= batteries[idx];                  // remove too powerful
            idx--;
            n--;
        }
        return sum / n;
    }
};
```

---

## 📚 4️⃣ Blog Article – “The Good, the Bad, and the Ugly of LeetCode 2141”

> **Title**: *Mastering LeetCode 2141 – Maximum Running Time of N Computers*  
> **Meta‑Description**: Discover the optimal solution, pitfalls, and interview‑ready insights for LeetCode 2141. Read the full guide in Java, Python, and C++.

---

### 1. Introduction

LeetCode 2141 asks you to maximize the simultaneous run time of a set of computers using a pool of batteries.  
It’s a classic *resource‑allocation* problem that appears frequently in technical interviews.  
Why? Because it forces you to think about **capacity planning**, **greedy trimming**, and **binary search** – all skills that are highly valuable to data‑engineering and backend roles.

### 2. Problem Recap

You have:
- `n` computers.
- `m = batteries.length` batteries.
- `batteries[i]` = runtime of battery `i` (minutes).

You can swap batteries between computers any time (swapping is instant).  
Goal: find the maximum number of minutes all `n` computers can run **simultaneously**.

### 3. Intuition & Key Observations

| Observation | Why it matters |
|-------------|----------------|
| **O(1) Swap** | Allows you to re‑balance instantly; the only real constraint is battery capacity. |
| **Battery can’t exceed T** | During a `T`‑minute interval, a battery contributes at most `T` minutes. |
| **Feasible Total** | A run of length `T` is feasible iff `∑ min(battery, T) ≥ n * T`. |

The last observation gives us a **decision function** – a perfect candidate for binary search.

### 4. The Good – Sort‑and‑Trim

#### What It Does

1. Sort batteries ascending.
2. Keep trimming the strongest battery if it would otherwise be “idle”.
3. The remaining batteries can all be used for exactly `sum / n` minutes.

#### Why It’s Good

- **Simple to explain** – perfect for an interview.
- **Fast** – one `O(m log m)` sort + a single linear scan.
- **Deterministic** – no random factors; always the same answer for the same input.

#### Code‑Snippet (Java)

```java
Arrays.sort(batteries);
while (batteries[idx] > sum / n) { … }
```

### 5. The Bad – Binary Search Pitfalls

Binary search is elegant but subtle:

- **Wrong upper bound** → infinite loop.  
  Always use `high = total / n`, not `total` itself.
- **Integer division** – `mid = (low + high) / 2` may overflow; use `low + (high - low) / 2`.
- **Feasibility function** – you must sum `min(battery, mid)`; forgetting to cap at `mid` will give wrong results.

#### Interview‑Ready Tip  
If you’re asked to justify the binary search, highlight that `mid` is a **guess** for the target runtime, and the feasibility test is essentially a *knapsack* with bounded weights.

### 6. The Ugly – Common Traps & Edge Cases

| Trap | Example | Fix |
|------|---------|-----|
| **32‑bit overflow** | `sum` of 10^5 batteries each 10^9 = 10^14 | Use `long` / `long long`. |
| **Empty Battery List** | `m < n` | Problem guarantees `m ≥ n`; still, guard against division by zero. |
| **All Batteries Equal** | `batteries = [5,5,5]` | After sort, trimming loop doesn’t run; answer is `15/3 = 5`. |
| **Very Strong Battery** | `[1000,1,1]`, `n=2` | Trim 1000 → `sum=2`, `n=2`, answer `1`. |

### 7. Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Sort‑Trim | `O(m log m)` | `O(1)` extra |
| Binary Search | `O(m log (total / n))` | `O(1)` extra |

With `m ≤ 10^5`, both are well within LeetCode’s limits.

### 8. Interview Tips

- **Explain the decision function** before coding; interviewers love seeing your thought process.
- **Show edge case handling** – e.g., when all batteries are equal, or one battery is astronomically larger.
- **Mention alternatives** – e.g., priority queue, DP – to demonstrate breadth of knowledge, even if you settle on sort‑trim for brevity.
- **Time your solution** on a whiteboard – ensure you don’t exceed the allotted 30‑minute window.

### 9. Conclusion

LeetCode 2141 is a neat illustration of **greedy trimming + arithmetic reasoning**.  
By mastering this problem, you’ll showcase strong algorithmic thinking, a solid grasp of integer arithmetic, and the ability to optimize for both *time* and *space*—exactly the skills recruiters look for in backend, data‑engineering, and full‑stack roles.

---

## 🔑 5️⃣ Why This Blog Helps You Land Your Next Job

- **Interview‑ready**: The article walks through the full reasoning pipeline.
- **Multi‑language**: Java, Python, C++ snippets demonstrate cross‑platform proficiency.
- **SEO‑friendly**: Keywords such as *Maximum Running Time of N Computers*, *LeetCode 2141*, *binary search*, *coding interview*, *Java*, *Python*, *C++* ensure visibility on job‑search engines.
- **Clear structure**: Headings, tables, and code blocks make the article easy to skim—a big plus for recruiters scanning quickly.

---

### 📌 Takeaway

> **Sort‑and‑Trim** gives you a clean, optimal solution that’s easy to explain in an interview.  
> **Binary Search** offers a more algorithmic perspective and proves the same result with a different lens.  
> Whichever you present, be ready to discuss edge cases and justify the feasibility check—those are the “good” interview moments that leave a lasting impression.  

Good luck, and happy coding!