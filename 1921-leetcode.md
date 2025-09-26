---
title: LeetCode 1921. Eliminate Maximum Number of Monsters - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🗡️  Eliminate Maximum Number of Monsters  
> **A Deep‑Dive on a Classic LeetCode Problem (Time‑O(n), Space‑O(n))**

---

## Problem Restatement

| | |
|---|---|
| **Given** | `dist[i]` – distance of monster *i* from the target. <br> `speed[i]` – constant speed of monster *i*. |
| **Rule** | In every turn we can eliminate **exactly one** monster. <br> A monster that arrives at time `t` (integer number of turns) before we get a chance to fire at it will win. |
| **Goal** | Find the maximum number of monsters we can eliminate before any monster reaches the target. |

> **Output** – the number of monsters we can eliminate.

---

## 1.  Intuition – “When Does a Monster Arrive?”

A monster needs `ceil(dist / speed)` turns to reach the target.  
Why “ceil”?

* `dist = 4`, `speed = 3` → it takes 1.33 turns, so it arrives on turn **2**.  
* `dist = 2`, `speed = 2` → it reaches on turn **1**.

Thus **arrival time** is:

```text
arrival = ceil( dist / speed )
```

If `arrival` is larger than the number of monsters we have, it simply means that the monster will arrive *after* we are already finished eliminating everyone – we can safely ignore it.

---

## 2.  Classic Solution – Priority Queue

The most common solution (and the one you see most often on LeetCode) is to:

1. **Compute the arrival time** of each monster.
2. **Sort** all arrival times.
3. Walk through them: as soon as we hit a monster whose arrival time `≤ current turn` we stop – that turn is the answer.

```
sort(arrivalTimes)
for (turn = 0; turn < n; ++turn) {
    if (arrivalTimes[turn] <= turn) return turn;
}
return n;
```

*Time*: `O(n log n)` (because of the sort)  
*Space*: `O(n)` (for the array of arrival times)

While this is fast enough for most test cases, we can do better – **O(n) time**.

---

## 3.  The Bucket / “Counting Sort” Trick – O(n) Time

The key observation:  
*For a monster to survive until turn `t`, its arrival time must be ≤ `t`.*

Instead of sorting, we **bucket** monsters by their arrival time.

```
n          = dist.length
bucket[i]  = number of monsters that arrive on turn i   (0‑indexed)
```

We only need buckets up to `n‑1`. If a monster’s arrival time is ≥ `n`, it will arrive after we are already done eliminating everyone – it never causes trouble.

**Step 1 – Build the buckets**

```text
for each monster i
    arrival = ceil(dist[i] / speed[i])
    if arrival < n
        bucket[arrival]++
```

**Step 2 – Scan the buckets**

We walk through the turns in order.  
At turn `i` we have already eliminated `eliminated` monsters.  
If **more monsters are waiting at this turn than the turns we have survived**, i.e.

```
eliminated + bucket[i] > i
```

then some monster will reach the target before we can fire – we stop and return `i`.  
Otherwise we add the monsters of this turn to `eliminated` and continue.

If we finish the scan without returning, we can eliminate all monsters – answer is `n`.

**Full Algorithm (Python‑style)**

```python
def eliminateMaximum(dist, speed):
    n = len(dist)
    bucket = [0] * n

    for d, s in zip(dist, speed):
        arrival = math.ceil(d / s)
        if arrival < n:
            bucket[arrival] += 1

    eliminated = 0
    for i in range(n):
        if eliminated + bucket[i] > i:
            return i
        eliminated += bucket[i]

    return n
```

**Why it works**

* At turn `i` we have exactly `i+1` opportunities to fire (turn 0, turn 1, …, turn i).
* If at any turn `i` the number of monsters that have already arrived (`eliminated + bucket[i]`) exceeds the number of opportunities (`i+1`), some monster wins – we cannot go any further.
* If we reach the end of the buckets, we have had enough turns to fire once at every monster’s arrival time, so all monsters are eliminated.

**Complexity**

| | |
|---|---|
| **Time** | `O(n)` – one pass to bucket, one pass to scan. |
| **Space** | `O(n)` – the bucket array. |

---

## 4.  Reference Implementations

Below you will find the same algorithm written in **Java 17**, **C++17** and **Python 3**.  
All three are 1‑to‑1 translations of the logic described above and are ready to paste into your editor.

---

### 4.1 Java 17

```java
import java.util.*;

public class Solution {
    public int eliminateMaximum(int[] dist, int[] speed) {
        int n = dist.length;
        int[] bucket = new int[n];

        // Build buckets
        for (int i = 0; i < n; i++) {
            int arrival = (int) Math.ceil((double) dist[i] / speed[i]);
            if (arrival < n) {
                bucket[arrival]++;
            }
        }

        // Scan buckets
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

---

### 4.2 C++17

```cpp
#include <vector>
#include <cmath>

class Solution {
public:
    int eliminateMaximum(std::vector<int>& dist, std::vector<int>& speed) {
        int n = dist.size();
        std::vector<int> bucket(n, 0);

        // Build buckets
        for (int i = 0; i < n; ++i) {
            int arrival = static_cast<int>(std::ceil(static_cast<double>(dist[i]) / speed[i]));
            if (arrival < n) {
                ++bucket[arrival];
            }
        }

        // Scan buckets
        int eliminated = 0;
        for (int i = 0; i < n; ++i) {
            if (eliminated + bucket[i] > i) {
                return i;
            }
            eliminated += bucket[i];
        }
        return n;
    }
};
```

---

### 4.3 Python 3

```python
import math
from typing import List

class Solution:
    def eliminateMaximum(self, dist: List[int], speed: List[int]) -> int:
        n = len(dist)
        bucket = [0] * n

        # Build buckets
        for d, s in zip(dist, speed):
            arrival = math.ceil(d / s)
            if arrival < n:
                bucket[arrival] += 1

        # Scan buckets
        eliminated = 0
        for i in range(n):
            if eliminated + bucket[i] > i:
                return i
            eliminated += bucket[i]

        return n
```

---

## 5.  “Good – Bad – Ugly” Perspective

| | |
|---|---|
| **Good** | *O(n)* time, *O(n)* space. Handles all edge cases. Very clear logic (bucket & scan). |
| **Bad** | The bucket array can be large (`n` up to 200 000). If many monsters arrive after `n` turns, they are simply ignored – but we still allocate the full array. |
| **Ugly** | A naïve priority‑queue solution is simple but `O(n log n)`. If you only need the answer for a single query, the bucket trick may feel a bit “magical” and harder to justify to a skeptical interviewer. |

---

## 6.  What Interviewers Like

1. **Explain your thinking** – start with “I’ll compute arrival times” and show you’ll sort them.
2. **Show you can optimize** – ask if we can do better than `O(n log n)`. Bring up the bucket idea, and explain the “≤ turn” invariant.
3. **Handle edge cases** – e.g., `dist[i] == 0`, `speed[i] == 1`, or all monsters arrive after `n`.  
4. **Time‑Space trade‑off** – you can mention that if memory is at a premium, you can keep the sorted list instead of buckets.

---

## 6.  Final Takeaway

The “bucket & scan” solution is a classic example of *sorting‑by‑counting* when the keys (arrival times) are bounded by `n`.  
It beats the standard priority‑queue approach by a clear factor of *log n* and still keeps the code readable.

When you’re asked to solve this problem in an interview:

1. **Show the straightforward priority‑queue solution** (makes it clear you understand the basic mechanics).
2. **Ask** if there is a more efficient approach.
3. **Present the bucket trick** – explain the key invariant (`eliminated + bucket[i] > i`) and why ignoring arrival times ≥ `n` is safe.
4. **Wrap up with the complexity** and a quick sanity‑check on the constraints.

Good luck, and may your shots always hit the right target! 🚀