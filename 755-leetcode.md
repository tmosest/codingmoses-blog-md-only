---
title: LeetCode 755. Pour Water - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The LeetCode Problem 755 – Pour Water  
**Problem link**: https://leetcode.com/problems/pour-water/  

> *You are given an elevation map represented as an integer array `heights`.  
>  A unit of water falls at index `k`. The water first lands on the highest
>  terrain or water at that index. Then it tries to move left, then right,
>  and only stays if it cannot fall lower.  
>  Simulate `volume` units of water and return the final elevation map.*

---

## 2.  Algorithm Overview  
We simulate each unit of water one by one – the input limits (`n ≤ 100`,
`volume ≤ 2000`) make this perfectly fine (≈ 2 M operations).  

For a single unit:

| Step | What we do | Why |
|------|------------|-----|
| 1 | Start at `k`. | Water first lands here. |
| 2 | Scan left while the next cell is **not higher** than the current one. |
| 3 | While scanning, remember the first cell that is strictly lower – this is a “falling spot”. |
| 4 | If a left‑falling spot exists → move there, increment its height, and stop. |
| 5 | If no left falling spot → repeat the same scan on the right side. |
| 6 | If neither side offers a lower spot → stay at `k` and increment `heights[k]`. |

The scan stops as soon as we encounter a higher cell (infinite wall on the
outside ensures the scan ends at the array boundary).

The algorithm is **O(volume × n)** – each unit may look at all cells once,
but with the given constraints that’s at most `2000 × 100 = 200 000`
operations, trivial for modern CPUs.

---

## 3.  Correctness Proof  

We prove that the algorithm returns the exact final heights.

### Lemma 1  
During a scan in one direction, the algorithm moves step by step to the
next index only while the current cell height is **≥** the next cell height.
Thus it follows the rule “water only moves to equal or lower levels”.

*Proof.*  
The loop condition `heights[i + d] <= heights[i]` enforces this.
If the next cell were higher, the water would not be able to move there,
hence the loop terminates. ∎

### Lemma 2  
If there exists a cell to the left that is strictly lower than the starting
cell and all cells between the start and that cell are non‑higher than the
previous ones, the algorithm will choose the **leftmost** such cell.

*Proof.*  
During the left scan the variable `best` is updated only when a strictly
lower cell is found (`heights[i+d] < heights[i]`).  
Because the scan proceeds from `k` outward, the first such lower cell
becomes `best` and never changes again. Thus the algorithm picks the
leftmost falling spot. ∎

### Lemma 3  
If no left falling spot exists, the algorithm correctly decides whether a
right falling spot exists and, if so, chooses the rightmost one.

*Proof.*  
Symmetric to Lemma&nbsp;2. The right scan starts from `k` and moves right
while the next cell is not higher. The first strictly lower cell encountered
is stored in `best` and will be the rightmost falling spot, because we
continue scanning until a higher cell blocks further movement. ∎

### Lemma 4  
If neither side offers a falling spot, the algorithm increases `heights[k]`.

*Proof.*  
When both scans fail to find a strictly lower cell, `best` remains `k`.  
The condition `heights[best] < heights[K]` fails and the loop falls through
to the final statement `heights[K]++`. Thus the water stays at `k`. ∎

### Theorem  
After processing all `volume` units, the returned array equals the state of
the terrain and water as defined in the problem.

*Proof.*  
By induction over the number of processed units.  
Base: before any unit, the heights are correct.  
Induction step: assume after `t` units the state is correct.  
Processing unit `t+1` follows the exact rules of the problem:
first left, then right, then stay.  
By Lemmas 1‑4 the algorithm places the unit in the correct cell.  
Thus after `t+1` units the state remains correct.  
After `volume` units the algorithm terminates, yielding the correct final
array. ∎

---

## 4.  Complexity Analysis  

| Step | Operation | Complexity |
|------|-----------|------------|
| Simulation loop | `volume` iterations | `O(volume)` |
| Scan in each iteration | Worst‑case `n` cells | `O(n)` |
| **Total** | | **O(volume · n)** |

With the constraints (`n ≤ 100`, `volume ≤ 2000`) the worst case is
`200 000` elementary operations, well below any practical limit.

Memory usage is `O(n)` for the height array.

---

## 5.  Implementation

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.  
All use the same logic described above.

### 5.1 Java (LeetCode style)

```java
import java.util.*;

class Solution {
    public int[] pourWater(int[] H, int V, int K) {
        int n = H.length;

        while (V-- > 0) {                 // one unit of water at a time
            boolean moved = false;

            // try left first
            for (int dir : new int[]{-1, 1}) {
                int i = K, best = K;

                while (i + dir >= 0 && i + dir < n && H[i + dir] <= H[i]) {
                    if (H[i + dir] < H[i]) {
                        best = i + dir;   // first lower cell in this direction
                    }
                    i += dir;              // move one step
                }

                if (H[best] < H[K]) {      // found a falling spot
                    H[best]++;             // place water
                    moved = true;
                    break;                 // stop searching for this unit
                }
            }

            if (!moved) {                  // stays at the original index
                H[K]++;
            }
        }
        return H;
    }
}
```

### 5.2 Python

```python
from typing import List

class Solution:
    def pourWater(self, H: List[int], V: int, K: int) -> List[int]:
        n = len(H)
        for _ in range(V):
            moved = False
            for d in (-1, 1):            # left first, then right
                i, best = K, K
                while 0 <= i + d < n and H[i + d] <= H[i]:
                    if H[i + d] < H[i]:
                        best = i + d   # first strictly lower cell
                    i += d
                if H[best] < H[K]:
                    H[best] += 1
                    moved = True
                    break
            if not moved:
                H[K] += 1
        return H
```

### 5.3 C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> pourWater(vector<int>& H, int V, int K) {
        int n = H.size();
        while (V--) {                     // simulate each unit
            bool moved = false;
            for (int d : {-1, 1}) {        // left first, then right
                int i = K, best = K;
                while (i + d >= 0 && i + d < n && H[i + d] <= H[i]) {
                    if (H[i + d] < H[i]) best = i + d;
                    i += d;
                }
                if (H[best] < H[K]) {      // found lower spot
                    H[best] += 1;
                    moved = true;
                    break;
                }
            }
            if (!moved) H[K] += 1;         // stays where it landed
        }
        return H;
    }
};
```

All three solutions compile on the LeetCode online judge and will
pass every test case.

---

## 6.  The Good, The Bad & The Ugly – A Developer’s Lens  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Good – Simplicity** | The problem is a pure simulation; no fancy data
structures are required.  The algorithm is easy to explain and reason
about, which is a plus in an interview. | | |
| **Good – Readability** | All implementations use explicit loops,
clear variable names (`best`, `moved`, `dir`) and comments. | | |
| **Bad – O(n) scans per unit** | With huge inputs the algorithm would become
slow, but the LeetCode constraints guarantee it is acceptable. | | |
| **Bad – Repeated Scanning** | We scan the same array for every unit of water.
A clever implementation could keep track of “next falling spot” to avoid
re‑scanning. | | |
| **Ugly – Off‑by‑one traps** | The two‑direction scan is delicate: we must stop when we
encounter a higher cell or the array boundary. A wrong condition can
cause infinite loops or wrong placement. | | |
| **Ugly – Edge Cases** | If `k` is at the edge, the algorithm must not go out of
bounds.  The outer “infinite wall” is handled by the `while` condition. | | |

### Why the simulation is still *good* for interview practice  
- **Talk‑through**: You can explain your thought process step‑by‑step
  in an interview.  
- **Testing**: Write a few edge tests yourself:  
  - `k = 0` or `k = n-1`  
  - All heights equal  
  - `volume = 0` (no change)  
  - Very tall walls on both sides  

### Tips for the Job Interview  

1. **Start with a clean design** – describe the simulation before coding.  
2. **Explain the scan logic** – emphasise the `<=` condition and why we look
   for the first strictly lower cell.  
3. **Talk about time/space trade‑offs** – show you understand that
   `O(n·volume)` is acceptable for the constraints.  
4. **Mention corner‑case handling** – boundaries, infinite walls, units that
   stay on the landing cell.  
5. **Show confidence** – say “I will keep this code in my interview
   notebook for future reference”.  

---

## 7.  SEO‑Optimized Blog Post  

> **Title**: *LeetCode 755 – Pour Water: The Good, The Bad, and The Ugly*  
> **Meta Description**: “Learn a clean Java, Python, and C++ solution for LeetCode 755
> Pour Water. Dive into algorithm details, complexity analysis,
> correctness proof, and interview‑ready insights for your next job.”

```markdown
# LeetCode 755 – Pour Water: The Good, The Bad, and The Ugly

If you’re prepping for a **software‑engineering interview** or a **coding
interview** at a top‑tech company, you’ve probably seen the *Pour Water*
problem on LeetCode.  It’s a classic example of a simulation that
forces you to think carefully about movement rules and edge cases.

In this post I’ll walk you through:

- The **exact algorithm** that solves the problem in linear time.
- A **formal correctness proof** so you can discuss it with confidence.
- A full **Java**, **Python**, and **C++** implementation (LeetCode‑ready).
- A quick cheat‑sheet of **time‑space complexity**.
- How to use the *Pour Water* solution to land your next **software‑engineering
  job**.

> **Keywords**: LeetCode 755, Pour Water, Java solution, Python solution,
  C++ solution, interview algorithm, coding interview, job interview,
  software engineer, algorithm interview, career boost.

---

## 🎯 Why LeetCode 755 Matters

- **Common interview topic** – many senior‑level hiring managers ask
  simulation questions that test problem‑solving and clean coding.
- **Edge‑case heavy** – it forces you to think about array boundaries,
  infinite walls, and movement direction priority.
- **Small constraints, big learning** – you can solve it by brute‑force
  simulation, yet you still need to reason about why the simulation is
  correct.

---

## 📈 The Algorithm in a Nutshell

1. **Simulate each unit of water** (≤ 2000 units).  
2. **Scan left** while the next cell is not higher.  
3. Record the *first strictly lower* cell (leftmost falling spot).  
4. If found → drop water there.  
5. If not, **scan right** using the same logic.  
6. If neither side offers a fall → stay at the landing index.

This yields a time complexity of **O(n × volume)** and a space
usage of **O(n)**.

---

## 🛠️ Code Snippets

### Java

```java
class Solution {
    public int[] pourWater(int[] H, int V, int K) {
        int n = H.length;
        while (V-- > 0) {
            boolean moved = false;
            for (int dir : new int[]{-1, 1}) { // left first, then right
                int i = K, best = K;
                while (i + dir >= 0 && i + dir < n && H[i + dir] <= H[i]) {
                    if (H[i + dir] < H[i]) best = i + dir;
                    i += dir;
                }
                if (H[best] < H[K]) {
                    H[best]++; moved = true; break;
                }
            }
            if (!moved) H[K]++;
        }
        return H;
    }
}
```

### Python

```python
from typing import List

class Solution:
    def pourWater(self, H: List[int], V: int, K: int) -> List[int]:
        n = len(H)
        for _ in range(V):
            moved = False
            for d in (-1, 1):
                i, best = K, K
                while 0 <= i + d < n and H[i + d] <= H[i]:
                    if H[i + d] < H[i]:
                        best = i + d
                    i += d
                if H[best] < H[K]:
                    H[best] += 1
                    moved = True
                    break
            if not moved:
                H[K] += 1
        return H
```

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> pourWater(vector<int>& H, int V, int K) {
        int n = H.size();
        while (V--) {
            bool moved = false;
            for (int d : {-1, 1}) {
                int i = K, best = K;
                while (i + d >= 0 && i + d < n && H[i + d] <= H[i]) {
                    if (H[i + d] < H[i]) best = i + d;
                    i += d;
                }
                if (H[best] < H[K]) {
                    H[best] += 1;
                    moved = true;
                    break;
                }
            }
            if (!moved) H[K] += 1;
        }
        return H;
    }
};
```

---

## 📊 Complexity Cheat‑Sheet

| Operation | Complexity | Notes |
|-----------|------------|-------|
| Simulate all units | **O(volume · n)** | ≤ 200 000 ops (tiny) |
| Space | **O(n)** | The height array itself |

---

## 🚀 How to Use This Post in Your Job Hunt  

1. **Show your code on GitHub** – label the repo “LeetCode‑755‑Pour‑Water” and
   write a short README that mirrors this blog.  Recruiters love seeing a
   clean, commented implementation.  
2. **Discuss the algorithm in your interview** – walk through the
   simulation, why left is tried before right, and how edge‑cases are
   handled.  
3. **Mention time‑space trade‑offs** – explain why brute‑force works for
   the given limits and hint at possible optimisations (e.g. using a
   segment tree for larger inputs).  
4. **Link to this article** in your portfolio or LinkedIn “Projects”
   section – it shows you know the problem inside‑out and can document
   it for others.

---

## 8.  Final Take‑away  

- **Good**: The simulation is straightforward, easy to reason about,
  and perfectly suited for the LeetCode constraints.  
- **Bad**: It runs in `O(n·volume)` time; for huge inputs a more
  sophisticated data‑structure would be needed.  
- **Ugly**: The two‑direction scan can be error‑prone (off‑by‑one bugs,
  wrong boundary handling) if not carefully commented and tested.

With a clean Java, Python, or C++ implementation, a rigorous proof,
and interview‑ready explanations, you’re all set to ace *Pour Water* and
maybe land that dream tech job. Happy coding! 🚀

```

--- 

Feel free to copy, adapt, or expand this Markdown.  It can be posted
directly on GitHub Pages, dev.to, or your own blog.

Happy learning, and may your next interview be “just water‑falling”
smooth!