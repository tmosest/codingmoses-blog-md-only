---
title: LeetCode 2126. Destroying Asteroids - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# Destroying Asteroids – 2126. LeetCode – A Job‑Ready Interview Problem

**Keywords:** LeetCode 2126, Destroying Asteroids, Greedy algorithm, Sorting, Frequency array, Java, Python, C++, Interview preparation, Software engineering interview, Data structures, Algorithm design

---

## 1. Problem Overview

You’re given an integer `mass` – the initial mass of a planet – and an array `asteroids` where `asteroids[i]` is the mass of the *i*‑th asteroid.  
The planet may collide with the asteroids in **any order**.  
* If the planet’s current mass is **≥** the asteroid’s mass, the asteroid is destroyed and its mass is added to the planet.  
* If the planet’s mass is **<** the asteroid’s mass, the planet is destroyed.

**Return** `true` if it is possible to destroy *all* asteroids, otherwise `false`.

**Constraints**

| Variable | Range |
|----------|-------|
| `mass` | 1 … 10<sup>5</sup> |
| `asteroids.length` | 1 … 10<sup>5</sup> |
| `asteroids[i]` | 1 … 10<sup>5</sup> |

---

## 2. Intuition

To grow as fast as possible, you should always absorb the *smallest* asteroid that the planet can currently defeat.  
If you absorb a larger asteroid first, you might miss the chance to grow enough to take on a slightly larger one later.  

Thus, the optimal strategy is:

1. **Sort** the asteroid masses in ascending order.  
2. Iterate through the sorted list, adding each asteroid’s mass to the planet’s mass whenever possible.  
3. If at any point the planet cannot absorb the current asteroid, you can never absorb it later (because all remaining asteroids are equal or larger).  

This greedy strategy guarantees that if a solution exists, you’ll find it.

---

## 3. Algorithm (Greedy + Sorting)

```
sort(asteroids)           // ascending
for each a in asteroids:
    if mass >= a:
        mass += a
    else:
        return false
return true
```

**Why it works**

- The planet’s mass only increases when it successfully absorbs an asteroid.  
- The smallest remaining asteroid is the easiest to absorb; if you can’t absorb it, you can’t absorb any larger one.  
- Absorbing the smallest first can only increase the planet’s mass, never decrease it, so the greedy choice is safe.

---

## 4. Complexity Analysis

| Metric | Time | Space |
|--------|------|-------|
| Sorting | **O(n log n)** (where *n* = `asteroids.length`) | **O(1)** – in‑place sorting (Java `Arrays.sort`, Python `list.sort`, C++ `sort`) |
| Iteration | **O(n)** | **O(1)** |
| **Total** | **O(n log n)** | **O(1)** |

> **Note:**  
> If the asteroid masses are bounded by a small constant (≤ 10<sup>5</sup>), a *counting sort* (`O(n + maxMass)`) can reduce the time to linear, but the classic sort is simpler and still fast enough for the given limits.

---

## 5. Edge Cases

| Case | Reasoning |
|------|-----------|
| **Mass already ≥ all asteroids** | The loop will absorb all; return `true`. |
| **A single asteroid heavier than the mass** | Immediately `false`. |
| **Multiple identical heavy asteroids** | Once you fail on one, you’ll fail on all identical ones. |
| **Mass or asteroid size up to 10<sup>5</sup>** | Use `long` (Java) or `long long` (C++) to avoid overflow when summing many asteroids. |

---

## 6. Code Implementations

Below are clean, interview‑ready implementations in **Java**, **Python**, and **C++**. Each follows the greedy + sorting strategy and includes comments for clarity.

### 6.1 Java

```java
import java.util.Arrays;

public class Solution {
    /**
     * Determines whether the planet can destroy all asteroids.
     *
     * @param mass      initial planet mass
     * @param asteroids array of asteroid masses
     * @return true if all asteroids can be destroyed, false otherwise
     */
    public boolean asteroidsDestroyed(int mass, int[] asteroids) {
        // Sort in ascending order
        Arrays.sort(asteroids);

        long currentMass = mass;           // Use long to avoid overflow

        for (int a : asteroids) {
            if (currentMass >= a) {
                currentMass += a;
            } else {
                return false;             // Cannot absorb this asteroid
            }
        }
        return true;                       // All asteroids destroyed
    }
}
```

### 6.2 Python

```python
from typing import List

class Solution:
    def asteroidsDestroyed(self, mass: int, asteroids: List[int]) -> bool:
        """
        Greedy solution: absorb the smallest asteroid you can.
        """
        asteroids.sort()                 # Ascending
        current = mass

        for a in asteroids:
            if current >= a:
                current += a
            else:
                return False
        return True
```

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool asteroidsDestroyed(int mass, vector<int>& asteroids) {
        sort(asteroids.begin(), asteroids.end());   // Ascending
        long long cur = mass;                       // 64‑bit to avoid overflow

        for (int a : asteroids) {
            if (cur >= a) cur += a;
            else return false;
        }
        return true;
    }
};
```

> **Tip for interviews:**  
> Explain that you use `long long` (C++), `long` (Java) or Python’s native integers to handle the cumulative mass safely.

---

## 7. The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic simplicity** | Greedy + sort is intuitive and easy to explain. | Requires sorting, which is *O(n log n)*. | Misunderstanding the greedy choice can lead to wrong answers. |
| **Time complexity** | Fast enough for 10<sup>5</sup> elements. | Sorting might be considered overkill if you know the data is already sorted. | Using an inefficient data structure (e.g., priority queue with repeated deletions) can add unnecessary overhead. |
| **Space usage** | In‑place sorting → O(1) extra. | Sorting modifies input array – may be undesirable if the array must stay unchanged. | Not using a counting sort when `maxMass` is small; you waste time sorting a huge array. |
| **Edge handling** | Long type handles overflow; early exit on failure. | None. | Forgetting to cast to `long` can cause subtle bugs when the planet mass grows beyond 2<sup>31</sup>−1. |
| **Code clarity** | Clean, self‑documenting. | Requires a brief explanation of why greedy works. | Over‑commenting can clutter the solution. |

**Takeaway:** The greedy + sorting solution is the “good” way. If you’re interviewing at a tech company that values *speed* and *simplicity*, explain the intuition, show the code, and you’ll win the interview.  

---

## 8. SEO‑Optimized Closing

*If you’re looking for a job in software engineering, mastering interview problems like **Destroying Asteroids** demonstrates your ability to apply greedy algorithms and reason about optimal strategies. Practicing such LeetCode problems boosts your problem‑solving skills, makes your resume stand out, and shows hiring managers that you can write clean, efficient code in **Java**, **Python**, or **C++**.*

> **Ready to conquer the next coding interview?**  
> - Practice greedy problems: “Surrounded Regions,” “Maximum Subarray,” “Stone Game.”  
> - Build a personal project that uses sorting & greedy logic (e.g., a simple game AI).  
> - Keep a GitHub repo with annotated solutions – recruiters love seeing real code.  

Happy coding, and good luck on your next interview!

---