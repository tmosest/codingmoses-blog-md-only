---
title: LeetCode 1921. Eliminate Maximum Number of Monsters - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # How Many Monsters Can You Slay?  
**A fast, O(n) solution to Leetcode 2094 – “Eliminate Maximum Number of Monsters”**  

> _When a horde of monsters march toward your target, how many can you kill before they arrive?  
> Learn a bucket‑based trick that runs in linear time, uses only linear extra space, and works for Java, JavaScript, Python, and C++._

---

## 🔎 Problem Recap  

You’re defending a single point.  
* `dist[i]` – distance of the *i*‑th monster from the target  
* `speed[i]` – how many units the monster moves each minute  

You can kill **one** monster every minute, starting at minute 0.  
If a monster reaches the target **before** you fire, the game ends for that monster.  
Return the maximum number of monsters you can eliminate before any of them gets you.

---

## 📌 Key Insight

*For each monster, compute the **arrival minute** (ceil(distance/speed)).  
A monster that arrives after minute *n‑1* can never ruin your strategy, because you’ll have had *n* opportunities to shoot—one per minute.*  

With that in mind, we can bucket monsters by the minute they arrive (ignoring those whose bucket would be ≥ *n*).  
Then we walk through the buckets, keeping a running count of how many monsters we have already killed.  
If at minute *i* the number of monsters that have shown up so far (`killed + bucket[i]`) exceeds *i*, we cannot kill any more – the answer is *i*.  
Otherwise, we add `bucket[i]` to `killed` and continue.  
If we finish the loop, we survived all *n* monsters.

---

## 🗂 Algorithm

| Step | Action | Comments |
|------|--------|----------|
| 1 | Compute `arrival = ceil(dist[i] / speed[i])` | Use `ceil` because a monster arriving in the middle of a minute still counts as a full minute. |
| 2 | If `arrival < n` increment `bucket[arrival]` | `bucket[t]` = how many monsters arrive exactly at minute *t`. |
| 3 | For each minute *i* (0 … n‑1) | Check if `killed + bucket[i] > i`. If true → return *i*. |
| 4 | Add `bucket[i]` to `killed` | Continue. |
| 5 | Return `n` | All monsters survived without being shot. |

Complexities for every language: **Time O(n)**, **Space O(n)**.

---

## 📝 Code

Below are fully‑commented solutions in the four requested languages.

### Java
```java
import java.util.Arrays;

class Solution {
    public int eliminateMaximum(int[] dist, int[] speed) {
        int n = dist.length;
        int[] bucket = new int[n];          // bucket[i] = monsters arriving at minute i

        for (int i = 0; i < n; ++i) {
            int arrival = (int)Math.ceil((double)dist[i] / speed[i]);
            if (arrival < n) bucket[arrival]++;
        }

        int killed = 0;
        for (int i = 0; i < n; ++i) {
            if (killed + bucket[i] > i)   // a monster reaches before we can shoot it
                return i;
            killed += bucket[i];
        }
        return n;  // survived all monsters
    }
}
```

### JavaScript
```javascript
/**
 * @param {number[]} dist
 * @param {number[]} speed
 * @return {number}
 */
var eliminateMaximum = function(dist, speed) {
    const n = dist.length;
    const bucket = Array(n).fill(0);

    for (let i = 0; i < n; ++i) {
        const arrival = Math.ceil(dist[i] / speed[i]);
        if (arrival < n) bucket[arrival]++;
    }

    let killed = 0;
    for (let i = 0; i < n; ++i) {
        if (killed + bucket[i] > i) return i;
        killed += bucket[i];
    }
    return n;
};
```

### Python
```python
import math
from typing import List

class Solution:
    def eliminateMaximum(self, dist: List[int], speed: List[int]) -> int:
        n = len(dist)
        bucket = [0] * n

        for d, s in zip(dist, speed):
            arrival = math.ceil(d / s)
            if arrival < n:
                bucket[arrival] += 1

        killed = 0
        for i in range(n):
            if killed + bucket[i] > i:
                return i
            killed += bucket[i]
        return n
```

### C++
```cpp
class Solution {
public:
    int eliminateMaximum(vector<int>& dist, vector<int>& speed) {
        int n = dist.size();
        vector<int> bucket(n, 0);

        for (int i = 0; i < n; ++i) {
            int arrival = (dist[i] + speed[i] - 1) / speed[i]; // ceil
            if (arrival < n) bucket[arrival]++;
        }

        int killed = 0;
        for (int i = 0; i < n; ++i) {
            if (killed + bucket[i] > i) return i;
            killed += bucket[i];
        }
        return n;
    }
};
```

---

## 📊 Complexity Summary

| Language | Time | Space |
|----------|------|-------|
| Java | O(n) | O(n) |
| JavaScript | O(n) | O(n) |
| Python | O(n) | O(n) |
| C++ | O(n) | O(n) |

All implementations run in linear time with linear extra space for the bucket array.

---

## 🎯 Sample Test

| `dist` | `speed` | **Result** |
|--------|---------|------------|
| `[1, 2, 2, 3]` | `[1, 1, 1, 1]` | `2` |
| `[1, 4, 4]` | `[1, 1, 1]` | `3` |

**Explanation:**  
- In the first case, two monsters arrive at minute 1 and minute 2, but we can only kill one per minute, so we survive two kills.  
- In the second case, the earliest arriving monster needs 1 minute; the others need 4 and 4 minutes, which are beyond the number of shots we’ll get (3), so all three can be eliminated.

---

## 🔚 Wrap‑Up  

We turned a seemingly tricky “arrival‑time” problem into a clean bucket‑sorting trick that is both easy to understand and fast.  
The same pattern—compute arrival times, bucket them, walk the buckets—works for many “first‑arrival” Leetcode puzzles.

Give it a try, and feel free to tweak the bucket size or even drop the array if you’re clever about a priority queue. Happy coding!