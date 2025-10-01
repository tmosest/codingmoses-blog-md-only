---
title: LeetCode 1921. Eliminate Maximum Number of Monsters - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Eliminate Maximum Number of Monsters  
**A clean, O(n) solution + ready‑to‑copy code in Java, Kotlin, Python & Swift**

> *Why it matters*: The classic “heap‑based” solution is O(n log n) and is easy to miss when you’re in a hurry.  
> The bucket‑based approach works in linear time and uses only an extra array of size *n*.  
> In this post you’ll see the algorithm, the implementation in four languages, the complexity analysis and a brief “What could go wrong?” section that explains the common pitfalls.

---

### 1. Problem restated

You’re given two integer arrays of equal length *n*:

| i | 0 | 1 | … | n‑1 |
|---|---|---|---|-----|
| **dist[i]** | distance of monster *i* from the target |
| **speed[i]**| speed of monster *i* (distance units per turn) |

Every turn you may destroy *exactly one* monster that is still alive.  
If at any point a monster reaches the target *before* you get a chance to destroy it, the game ends.  
Return the maximum number of monsters you can destroy before the first monster reaches the target.

---

### 2. Intuition

1. **When does each monster arrive?**  
   - If a monster needs *t* turns to reach the target, then *t* = ⌈dist / speed⌉ (round up).  
   - A monster that would arrive after *n* turns is “out of the bucket” – we will never hit it before destroying all *n* monsters.

2. **Buckets of arrivals**  
   - For every monster, increment a counter `bucket[t]` (where `t < n`).  
   - `bucket[t]` tells us how many monsters arrive exactly in turn *t*.

3. **Walk through the turns**  
   - We have one “shot” per turn.  
   - At turn *i* (0‑based), we can destroy at most one monster from the cumulative arrivals up to *i*.  
   - If the total monsters that should have been destroyed **plus** the arrivals at turn *i* already exceed *i*, the game ends at turn *i*.

4. **If we never break early**  
   - We successfully destroyed all monsters before any could reach the target → return *n*.

---

### 3. Algorithm (O(n) time, O(n) extra space)

```
n        = dist.length
bucket[] = new int[n]   // bucket[t] = monsters that arrive in turn t

for each monster i:
    arrival = ceil(dist[i] / speed[i])
    if arrival < n:
        bucket[arrival]++

eliminated = 0
for i = 0 … n-1:
    if eliminated + bucket[i] > i:
        return i            // cannot destroy all monsters up to this turn
    eliminated += bucket[i]

return n                    // all monsters can be destroyed
```

The “ceil” can be computed with integer arithmetic:

```
arrival = (dist[i] + speed[i] - 1) / speed[i]
```

---

### 4. Complexity

|                | Time | Space |
|----------------|------|-------|
| **Whole algo** | O(n) | O(n) (the bucket array) |

No extra log‑factor, no priority queue.

---

### 5. Code

> **Tip:** The same logic works for Java, Kotlin, Python and Swift – just adjust syntax and imports.

#### 5.1 Java

```java
import java.util.*;

public class Solution {
    public int eliminateMaximum(int[] dist, int[] speed) {
        int n = dist.length;
        int[] bucket = new int[n];

        for (int i = 0; i < n; i++) {
            int arrival = (dist[i] + speed[i] - 1) / speed[i];  // ceil
            if (arrival < n) {
                bucket[arrival]++;
            }
        }

        int eliminated = 0;
        for (int i = 0; i < n; i++) {
            if (eliminated + bucket[i] > i) {
                return i;
            }
            eliminated += bucket[i];
        }
        return n;
    }
}
```

#### 5.2 Kotlin

```kotlin
class Solution {
    fun eliminateMaximum(dist: IntArray, speed: IntArray): Int {
        val n = dist.size
        val bucket = IntArray(n)

        for (i in 0 until n) {
            val arrival = (dist[i] + speed[i] - 1) / speed[i]   // ceil
            if (arrival < n) bucket[arrival]++
        }

        var eliminated = 0
        for (i in 0 until n) {
            if (eliminated + bucket[i] > i) return i
            eliminated += bucket[i]
        }
        return n
    }
}
```

#### 5.3 Python

```python
import math
from typing import List

class Solution:
    def eliminateMaximum(self, dist: List[int], speed: List[int]) -> int:
        n = len(dist)
        bucket = [0] * n

        for d, s in zip(dist, speed):
            arrival = (d + s - 1) // s   # ceil
            if arrival < n:
                bucket[arrival] += 1

        eliminated = 0
        for i in range(n):
            if eliminated + bucket[i] > i:
                return i
            eliminated += bucket[i]
        return n
```

#### 5.4 Swift

```swift
import Foundation

class Solution {
    func eliminateMaximum(_ dist: [Int], _ speed: [Int]) -> Int {
        let n = dist.count
        var bucket = Array(repeating: 0, count: n)

        for i in 0..<n {
            let arrival = (dist[i] + speed[i] - 1) / speed[i]   // ceil
            if arrival < n {
                bucket[arrival] += 1
            }
        }

        var eliminated = 0
        for i in 0..<n {
            if eliminated + bucket[i] > i {
                return i
            }
            eliminated += bucket[i]
        }
        return n
    }
}
```

---

### 6. Common pitfalls & how to avoid them

| Pitfall | Why it happens | Fix |
|---------|----------------|-----|
| Using `dist[i] / speed[i]` (integer division) | Misses the fractional turn, so a monster that arrives in 1.3 turns is considered to arrive in 1 turn, letting you survive one extra turn | Use `ceil` or `(dist + speed - 1) / speed` |
| Forgetting `arrival < n` guard | Bucket index out of bounds for monsters that arrive after the last turn | Only count arrivals that can happen before you have fired all *n* shots |
| Not handling 0‑based vs 1‑based turns | Comparing cumulative monsters to `i+1` instead of `i` | Keep all indices 0‑based throughout the algorithm |
| Mis‑counting shots | Destroying more than one monster per turn is impossible | The loop logic `if eliminated + bucket[i] > i` guarantees that at most one shot per turn is considered |

---

### 7. Take‑away

*The bucket‑based solution is the most efficient way to solve “Eliminate Maximum Number of Monsters” when you want linear time.  
It’s simple, easy to understand, and free of priority‑queue overhead.*  

If you ever run into a TLE on the original O(n log n) heap solution, switch to this linear approach – it passes all the hard test cases with flying colors.

Happy coding! 🚀  
Feel free to drop a comment or star the repository if you found this useful.