---
title: LeetCode 2453. Destroy Sequential Targets - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 **Destroy Sequential Targets – A Deep Dive (Java / Python / C++)**  

> **LeetCode #2453 – Destroy Sequential Targets**  
> **Difficulty:** Medium  
> **Keywords:** *hash map, modulo, frequency counting, greedy, optimization*  

---

## 1️⃣ Problem Recap  

You’re given an array `nums` of **positive integers** and a `space` value.  
If you “seed” the machine with any `nums[i]`, it can destroy every target that equals

```
seed + c * space   (c ≥ 0, integer)
```

Your goal: **destroy the maximum number of targets**.  
Return **the smallest seed value** that achieves this maximum.

---

## 2️⃣ Intuition – Why “Modulo” is the Key  

All numbers that are congruent modulo `space` can be destroyed by the **same seed**.  
E.g., with `space = 2` :

```
1, 3, 5, 7, …   → 1 mod 2
2, 4, 6, 8, …   → 0 mod 2
```

* The machine only cares about the remainder of each target when divided by `space`.  
* Therefore, the problem boils down to:
  * **Count how many times each remainder appears** in `nums`.  
  * Pick the remainder with the highest count (ties → smallest original value).

The “destroy” rule is *equivalence‑class* based, not a sequential range. That is the magic behind the modulo solution.

---

## 3️⃣ Algorithm (O(n) Time, O(k) Space)

1. **Count Frequencies**  
   Iterate once through `nums`, compute `r = nums[i] % space`, increment a hash‑map counter.

2. **Find the Optimal Remainder**  
   * `maxFreq` = maximum frequency found.  
   * `answer`  = minimal `nums[i]` among all numbers whose remainder’s count equals `maxFreq`.

3. Return `answer`.

Because `space` can be as large as \(10^9\) but the array length is only up to \(10^5\), the map will contain at most \(10^5\) entries – perfectly fine.

---

## 4️⃣ Implementation

### 4.1 Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int destroyTargets(int[] nums, int space) {
        // Step 1: frequency table of remainders
        Map<Integer, Integer> freq = new HashMap<>();
        int maxFreq = 0;
        for (int n : nums) {
            int r = n % space;
            int newFreq = freq.merge(r, 1, Integer::sum); // same as freq.getOrDefault(r,0)+1
            if (newFreq > maxFreq) maxFreq = newFreq;
        }

        // Step 2: find minimal seed with that frequency
        int answer = Integer.MAX_VALUE;
        for (int n : nums) {
            int r = n % space;
            if (freq.get(r) == maxFreq && n < answer) {
                answer = n;
            }
        }
        return answer;
    }
}
```

**Why `merge`?**  
`merge` both inserts and updates in one call. It’s cleaner than `put/getOrDefault`.

---

### 4.2 Python (3)

```python
from collections import Counter
from typing import List

class Solution:
    def destroyTargets(self, nums: List[int], space: int) -> int:
        # Frequency of each remainder
        freq = Counter([x % space for x in nums])
        max_freq = max(freq.values())

        # Minimal seed among numbers belonging to the most frequent remainder
        answer = min(
            (x for x in nums if freq[x % space] == max_freq),
            default=0
        )
        return answer
```

*`min()` with a generator keeps memory low.*  
If all numbers are zero‑length (impossible by constraints), `default` ensures safety.

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int destroyTargets(vector<int>& nums, int space) {
        unordered_map<int, int> freq;
        int maxFreq = 0;

        for (int n : nums) {
            int r = n % space;
            int f = ++freq[r];              // increment and store new value
            if (f > maxFreq) maxFreq = f;   // track maximum frequency
        }

        int answer = INT_MAX;
        for (int n : nums) {
            int r = n % space;
            if (freq[r] == maxFreq && n < answer)
                answer = n;
        }
        return answer;
    }
};
```

*C++17+ optional `unordered_map` default constructs to 0, so `++freq[r]` works out‑of‑the‑box.*

---

## 5️⃣ Complexity Analysis

| Approach | Time | Extra Space |
|----------|------|-------------|
| Java | `O(n)` (one pass + one pass) | `O(k)` where `k` = distinct remainders (≤ n) |
| Python | `O(n)` | `O(k)` |
| C++ | `O(n)` | `O(k)` |

All three solutions comfortably fit the limits (`n ≤ 10⁵`).

---

## 6️⃣ The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Concept** | Simple modulo counting – O(1) per element. | None – the logic is linear. | Mistaking it for “range” and doing an O(n²) brute force. |
| **Implementation** | Clean hash‑map usage. | Java’s `merge` might be unfamiliar to beginners. | C++ `unordered_map` can get a *hash collision* attack in rare cases (unimportant for LeetCode). |
| **Edge Cases** | Works with very large `space` (no array allocation). | None. | If `space` > max(nums), all remainders are unique – still correct, but may confuse newcomers. |
| **Memory** | Linear but small constant factor. | None. | Over‑allocating a `vector<int>` of size `space` would blow memory. |

**Bottom line:** The modulo‑frequency solution is the *correct* and *optimal* approach. Avoid pitfalls by sticking to a hash map and a single pass.

---

## 7️⃣ SEO‑Optimized Takeaway

- **Keywords:** “Destroy Sequential Targets”, “LeetCode 2453 solution”, “hash map modulo”, “Python O(n) LeetCode”, “C++ unordered_map”, “time complexity destroy targets”.
- **Meta‑description:** “Learn how to solve LeetCode 2453 – Destroy Sequential Targets – with a linear time, O(n) algorithm using modulo frequency counting. Full Java, Python, and C++ code examples, complexity analysis, and SEO‑friendly guide.”

---

## 8️⃣ Final Thoughts

- The problem’s essence is **equivalence classes modulo `space`**.  
- A single pass to count frequencies, followed by a second pass to pick the minimal seed, gives the optimal solution.  
- The same pattern (modulo → frequency → greedy tie‑break) reappears in many LeetCode problems: *Maximum Subarray with Mod*, *Longest Substring with K Distinct*, etc.

Happy coding! 🚀