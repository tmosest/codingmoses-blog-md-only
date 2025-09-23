---
title: LeetCode 3279. Maximum Total Area Occupied by Pistons - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Solving **“Maximum Total Area Occupied by Pistons”** (LeetCode 3279)

Below you’ll find:

| Language | Code | Complexity |
|----------|------|------------|
| **Java** | <details><summary>Click to expand</summary>```java
import java.util.*;

public class Solution {
    public long maxArea(int height, int[] positions, String directions) {
        int n = positions.length;
        long[] diff = new long[2 * height + 2];   // difference array (index 0 unused)
        long currentSum = 0;                      // sum of positions at time 0
        for (int i = 0; i < n; ++i) currentSum += positions[i];

        for (int i = 0; i < n; ++i) {
            int pos = positions[i];
            char d = directions.charAt(i);
            if (d == 'U') {                       // moving up first
                diff[1] += 1;
                diff[height - pos + 1] += -2;
                diff[2 * height - pos + 1] += 2;
            } else {                              // moving down first
                diff[1] += -1;
                diff[pos + 1] += 2;
                diff[pos + 1 + height] += -2;
            }
        }

        long best = currentSum;
        long slope = 0;   // derivative of total area wrt time
        for (int t = 1; t <= 2 * height; ++t) {
            slope += diff[t];
            currentSum += slope;   // area after t seconds
            if (currentSum > best) best = currentSum;
        }
        return best;
    }
}
```</details> | **Time** `O(n + height)`<br>**Space** `O(height)` |
| **Python** | <details><summary>Click to expand</summary>```python
import sys
from typing import List

def max_area(height: int, positions: List[int], directions: str) -> int:
    n = len(positions)
    diff = [0] * (2 * height + 2)  # 1‑based index
    cur = sum(positions)

    for pos, d in zip(positions, directions):
        if d == 'U':
            diff[1] += 1
            diff[height - pos + 1] += -2
            diff[2 * height - pos + 1] += 2
        else:  # 'D'
            diff[1] += -1
            diff[pos + 1] += 2
            diff[pos + 1 + height] += -2

    best = cur
    slope = 0
    for t in range(1, 2 * height + 1):
        slope += diff[t]
        cur += slope
        if cur > best:
            best = cur
    return best

# ---- sample usage ----
if __name__ == "__main__":
    h = 5
    pos = [2, 5]
    dirs = "UD"
    print(max_area(h, pos, dirs))  # → 7
````</details> | **Time** `O(n + height)`<br>**Space** `O(height)` |
| **C++** | <details><summary>Click to expand</summary>```cpp
#include <bits/stdc++.h>
using namespace std;

long long maxArea(int height, const vector<int>& positions, const string& directions) {
    int n = positions.size();
    vector<long long> diff(2 * height + 2, 0);   // 1‑based index
    long long cur = 0;
    for (int p : positions) cur += p;

    for (int i = 0; i < n; ++i) {
        int pos = positions[i];
        char d = directions[i];
        if (d == 'U') {          // moving up first
            diff[1] += 1;
            diff[height - pos + 1] += -2;
            diff[2 * height - pos + 1] += 2;
        } else {                 // moving down first
            diff[1] += -1;
            diff[pos + 1] += 2;
            diff[pos + 1 + height] += -2;
        }
    }

    long long best = cur;
    long long slope = 0;
    for (int t = 1; t <= 2 * height; ++t) {
        slope += diff[t];
        cur += slope;
        best = max(best, cur);
    }
    return best;
}

// Example usage
int main() {
    int h = 5;
    vector<int> pos = {2, 5};
    string dirs = "UD";
    cout << maxArea(h, pos, dirs) << '\n';   // prints 7
}
```</details> | **Time** `O(n + height)`<br>**Space** `O(height)` |

> **Tip** – All three solutions share the same logic:
> 1. Transform each piston’s motion into a *difference array* of slope changes.
> 2. Accumulate slopes to get the area after each second.
> 3. Track the maximum.

---

## 🎯 Why This Problem is a Gold‑Mine for Interviews

- **Simulation + Optimization**: Shows you can turn a naive O(n · t) simulation into O(n + h) by clever math.
- **Prefix‑Sum / Difference Array**: A classic interview trick. Master it, and you can crack many problems.
- **Time‑Complexity Analysis**: Handles `height` up to 10⁶ and `n` up to 10⁵ – you’ll need to think about memory and linear passes.
- **Edge‑Case Awareness**: Pistons at `0` or `height`, single piston, all moving in the same direction.

---

## 🔎 The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Concept** | Straightforward physics‑to‑math transformation | Might confuse candidates unfamiliar with difference arrays | Mistaking “maximum area at *any* time” for “maximum area *after* all pistons stop” |
| **Implementation** | One‑pass O(n + h) algorithm | Requires 64‑bit integers to avoid overflow | Off‑by‑one in array indices can lead to subtle bugs |
| **Performance** | Works fast for 10⁶ height | Still O(h), which may be large on slow machines | Trying to brute‑force simulation would TLE |

**Pro Tip**: During an interview, articulate your plan *before* writing code: "We’ll use a difference array because each piston’s contribution changes only twice per period."

---

## 🛠️ Step‑by‑Step Walkthrough (Java Edition)

1. **Base Sum** – `cur = Σ positions[i]` (area at time 0).
2. **Difference Array** – `diff[1]` holds the change in *slope* (the rate of area change per second) at each moment.
3. **For each piston**:
   - If moving up (`U`):
     ```
     diff[1] += 1;                           // start increasing
     diff[height - pos + 1] += -2;           // hit upper bound → start decreasing
     diff[2 * height - pos + 1] += 2;        // hit lower bound → start increasing again
     ```
   - If moving down (`D`):
     ```
     diff[1] += -1;                          // start decreasing
     diff[pos + 1] += 2;                     // hit lower bound → start increasing
     diff[pos + 1 + height] += -2;           // hit upper bound → start decreasing
     ```
4. **Sweep** over `t = 1 … 2*height`:
   - `slope += diff[t]` – updates how fast the area changes at this second.
   - `cur += slope` – area after this second.
   - Keep `best = max(best, cur)`.
5. **Return** `best`.

> The array size `2*height+2` is enough because a piston can at most change direction twice in a full period (up → down → up).

---

## 📊 Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Build diff | `O(n)` | `O(height)` |
| Sweep | `O(height)` | – |
| **Total** | **`O(n + height)`** | **`O(height)`** |

With `height ≤ 10⁶`, the diff array is about 16 MB (using `long`), which comfortably fits in memory.

---

## 📚 Blog Article – “Maximum Total Area Occupied by Pistons”  
**(SEO‑optimized, interview‑ready, good‑bad‑ugly guide)**

> **Keywords**: LeetCode 3279, piston simulation, difference array, algorithm interview, O(n + h), Java Python C++ solutions, interview strategy

---

### 1. Introduction

You’ve probably seen simulation problems before: “Given a set of objects moving on a line, compute something over time.”  
LeetCode 3279, **Maximum Total Area Occupied by Pistons**, takes this to the next level: each piston bounces back and forth between the walls of a cylinder, and you need to find the maximum total area under all pistons at any moment.

> **Why is this a great interview question?**  
> * It tests simulation skills, but forces you to think about **periodicity** and **efficient data structures**.  
> * The optimal solution is surprisingly simple once you recognize the underlying math.

---

### 2. Problem Recap

- **Input**  
  - `height` – upper limit of the pistons’ motion.  
  - `positions[i]` – current height of piston `i` (also the area under it).  
  - `directions[i]` – ‘U’ (upwards) or ‘D’ (downwards).

- **Goal** – Return the **maximum** of  
  \[
  \text{total area}(t) = \sum_{i=0}^{n-1} \text{height of piston }i\text{ after }t\text{ seconds}
  \]
  for *any* integer `t ≥ 0`.  
  The pistons never stop; they keep reflecting forever.

- **Constraints**  
  - `1 ≤ n ≤ 10⁵`  
  - `1 ≤ height ≤ 10⁶`  
  - `0 ≤ positions[i] ≤ height`

---

### 3. Naïve Approach (Why It Fails)

You could simulate second by second, updating each piston’s position and checking the sum.  
But:

- Each piston needs `O(height)` steps for a full cycle.  
- With 10⁵ pistons and a height of 10⁶, that’s **10¹¹ operations** – way beyond time limits.

---

### 3. Key Insight – Periodicity + Slope Changes

Each piston’s height over time is a *triangular wave*:

- If it starts at `p` and moves up, the area increases at rate `+1` per second until it hits the top (`height`).  
- At the top it immediately reverses direction → the slope changes to `-1`.  
- After another `height - p` seconds it hits the bottom, slope flips again to `+1`.  
- The pattern repeats every `2*height` seconds.

**Observation**:  
A piston’s *slope* (rate of area change) changes only at **three distinct moments** in a full period.  
We can encode all these changes in a **difference array** – a classic technique for range updates and point queries in O(1) time each.

---

### 4. Difference Array Construction

| Direction | Updates (1‑based indices) | Reason |
|-----------|---------------------------|--------|
| `U` | `diff[1] += 1` <br> `diff[height - p + 1] += -2` <br> `diff[2*height - p + 1] += 2` | Start up, hit top, hit bottom |
| `D` | `diff[1] += -1` <br> `diff[p + 1] += 2` <br> `diff[p + 1 + height] += -2` | Start down, hit bottom, hit top |

The **first entry `diff[1]`** sets the initial slope change for all pistons simultaneously.  
Adding or subtracting `2` at the later indices reflects the instantaneous reversal of motion.

> **Why 2 × height?**  
> A piston can change direction at most twice per cycle (up → down → up).  
> So we need an array long enough to capture both events, hence `2*height + 2`.

---

### 5. Sweeping Through Time

Once the difference array is built:

1. `cur = Σ positions[i]` – area at time 0.
2. `slope = 0`
3. For every second `t = 1 … 2*height`:
   - `slope += diff[t]` (updates how fast area changes at this instant).
   - `cur += slope` (new total area after `t` seconds).
   - `best = max(best, cur)`.

Because the slope is a *piecewise constant* function, we never have to simulate the pistons themselves – we just update aggregate quantities.

---

### 6. Implementation in One Line (Java, Python, C++)

The code snippets above illustrate how compact the final solution can be once the math is understood.  
All languages follow the same pattern:  
- **Build a diff array in O(n)**  
- **Sweep in O(height)**  

---

### 7. Interview‑Ready Strategy

1. **Clarify the question**: “We’re asked for the maximum total area *at any moment*, not after all pistons stop.”
2. **Sketch the approach**:  
   - *Explain* that each piston’s motion is periodic with period `2*height`.  
   - *Mention* that a piston only changes its rate of area change twice per period.  
   - *Introduce* a difference array to capture slope changes.
3. **Write clean code** – keep indices 1‑based to avoid confusion.
4. **Test edge cases** on paper:  
   - Pistons already at 0 or height.  
   - Single piston moving in one direction.  
   - All pistons moving together.
5. **Explain complexity** – show that the solution scales with `height` and `n`.

> **Why this matters**: Demonstrating a clear plan and explaining the logic signals mastery to interviewers, even if you’re not 100 % confident in the code.

---

### 8. Common Pitfalls (and How to Avoid Them)

| Pitfall | Fix |
|---------|-----|
| **Integer overflow** – sum can exceed 32‑bit | Use `long`/`int64_t`. |
| **Off‑by‑one in indices** | Stick to 1‑based indices in the diff array. |
| **Misinterpreting “maximum”** | Clarify you’re taking the max over all times `t ≥ 0`. |
| **Not considering full period** | Sweep up to `2*height` to capture all direction changes. |

---

### 9. Takeaway

LeetCode 3279 is a shining example of how *math* and *data‑structures* can turn a simulation nightmare into a slick O(n + h) solution.  
Whether you’re coding in Java, Python, or C++, the core idea remains the same: transform motion into slope changes and sweep once.

> **Remember** – When interviewers present a “simulation” problem with big constraints, think: *Is there a hidden periodicity? Can I use prefix sums or difference arrays?* That’s usually the key to cracking it.

---

### 10. Call to Action

- **Practice** – Try variations: pistons on a circle, different boundary conditions, or “maximum after k seconds”.  
- **Share** – Drop your own code on GitHub or a blog; others can learn from your style.  
- **Interview** – Bring this problem to your next coding interview – it’s a win‑win for both you and the interviewer.

---

### 11. Closing Thought

In the world of coding interviews, **simulation problems are not the bottleneck** – they’re often the *doorway* to deeper algorithmic thinking.  
LeetCode 3279 shows that a well‑chosen data structure (the difference array) can save you **millions of operations** and impress any hiring manager.

> **Good luck on your next interview!**  
> Use the solutions above, explain your thought process clearly, and you’ll walk out of that room feeling like a true algorithmist.

---

Happy coding, and may your interview stack always contain the right *difference*! 🚀

--- 

> *This article is tailored for interview‑savvy developers and tech hiring managers. Feel free to adapt the code and explanations to your teaching or hiring style.*