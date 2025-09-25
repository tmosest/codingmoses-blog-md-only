---
title: LeetCode 954. Array of Doubled Pairs - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 954. Array of Doubled Pairs – “The Good, the Bad, and the Ugly”

> **Goal:**  
> Given an even‑length integer array `arr`, determine whether it can be reordered so that every element `x` is followed by its double `2x`.  
> Formally we need a permutation of `arr` such that  
> `arr[2*i+1] == 2 * arr[2*i]` for all `0 <= i < arr.length/2`.

---

### Why this problem matters for an interview

- **Core concepts:** hash maps, greedy, sorting, handling negative numbers, edge cases (zeros).
- **Common interview pattern:** “Can we pair every element with its double / half?”
- **Job‑ready skillset:** algorithm design, complexity analysis, clean implementation in multiple languages.

---

## 1. The Good

| Aspect | What’s great | Why it matters |
|--------|--------------|----------------|
| **Simple idea** | Use frequency counts + absolute‑value ordering | Handles positives, negatives, and zeros uniformly |
| **Time Complexity** | `O(n log n)` (dominated by the sort) | Fast enough for `n ≤ 3·10⁴` |
| **Space Complexity** | `O(n)` | Acceptable for the constraints |
| **Deterministic** | Always returns the correct answer | No back‑tracking or exponential blow‑up |
| **Reusable** | Same pattern appears in “Split Array with Same Average”, “Dividing Two Groups” | Good to show you recognize algorithmic patterns |

---

## 2. The Bad

| Issue | Why it can trip you up | How to avoid |
|-------|------------------------|--------------|
| **Zero handling** | `0` can only pair with another `0`; the algorithm must ensure the count of zeros is even | Count `0`s separately or rely on the frequency map logic |
| **Negative numbers** | If you process large negatives first, you might consume their half prematurely | Sort by *absolute* value so that the smaller magnitude element is processed first |
| **Large integers** | `2 * x` can overflow 32‑bit when `x = 10⁵` | Use 64‑bit (`long`/`long long`) for the multiplication check or cast to a larger type |
| **Map iteration order** | In some languages the map is unordered, which can lead to inconsistent results | Iterate over a sorted list of keys instead of the map itself |

---

## 3. The Ugly

| Pitfall | What goes wrong | Fix |
|---------|-----------------|-----|
| **Mutable map during iteration** | Decreasing the count of a key while iterating can skip elements | Reduce counts *after* you’ve verified a pair exists |
| **Using a list of keys only** | If you skip an element that still has remaining count, you’ll miss future pairings | Loop over the array (or the sorted list) and check the current frequency before proceeding |
| **Int vs Long overflow** | `int` * 2 can exceed `int` max value | Promote to `long` when computing `2 * x` |
| **Missing half‑pairs for odd counts** | e.g. `[4, 4, 8]` – two 4s but only one 8 | The algorithm will detect insufficient partners when it tries to pair the second 4 |

---

## 4. Full Solutions

Below are clean, production‑ready implementations in **Java, Python, and C++**.  
All use the same high‑level strategy:

1. Count the frequency of every integer.  
2. Sort the numbers by their absolute value.  
3. For each number, try to pair it with its double, decrementing counts as you go.

### 4.1 Java

```java
import java.util.*;

class Solution {
    public boolean canReorderDoubled(int[] arr) {
        // 1. Frequency map
        Map<Integer, Integer> freq = new HashMap<>();
        for (int x : arr) {
            freq.put(x, freq.getOrDefault(x, 0) + 1);
        }

        // 2. Sort by absolute value
        List<Integer> sorted = new ArrayList<>(freq.keySet());
        sorted.sort(Comparator.comparingInt(Math::abs));

        // 3. Greedy pairing
        for (int x : sorted) {
            int countX = freq.getOrDefault(x, 0);
            if (countX == 0) continue;              // already used

            int doubleX = x * 2;                    // safe: x <= 1e5, so double <= 2e5
            int countDouble = freq.getOrDefault(doubleX, 0);

            if (countDouble < countX) return false; // not enough partners

            // consume the pairs
            freq.put(x, 0);
            freq.put(doubleX, countDouble - countX);
        }

        return true;
    }
}
```

> **Tip:** Use `long` for the `doubleX` calculation if you expect values near `Integer.MAX_VALUE`.

---

### 4.2 Python

```python
from collections import Counter
from typing import List

class Solution:
    def canReorderDoubled(self, arr: List[int]) -> bool:
        freq = Counter(arr)

        # Process numbers by ascending absolute value
        for x in sorted(freq, key=abs):
            if freq[x] == 0:
                continue

            double_x = x * 2
            if freq[double_x] < freq[x]:
                return False

            freq[double_x] -= freq[x]
            freq[x] = 0

        return True
```

> **Why sorted by `abs`?**  
> Ensures that if `x` is the larger magnitude of a pair (`-4` and `-8`), we process `-4` first, so `-8` is still available.

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool canReorderDoubled(vector<int>& arr) {
        unordered_map<long long, int> freq;
        for (int x : arr) freq[x]++;

        // Sort by absolute value
        sort(arr.begin(), arr.end(), [](int a, int b) {
            return llabs(a) < llabs(b);
        });

        for (int x : arr) {
            if (freq[x] == 0) continue;                // already used
            long long doubleX = 2LL * x;               // use 64‑bit to avoid overflow
            if (freq[doubleX] < freq[x]) return false; // not enough partners

            freq[doubleX] -= freq[x];
            freq[x] = 0;
        }
        return true;
    }
};
```

---

## 5. Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n log n)` (sorting dominates) | `O(n log n)` | `O(n log n)` |
| **Space** | `O(n)` (hash map + sorted list) | `O(n)` | `O(n)` |

---

## 6. SEO‑Friendly Summary

- **Title:** “Array of Doubled Pairs – 954 | LeetCode Solution (Java, Python, C++)”
- **Meta‑Description:** “Learn the greedy approach to solve LeetCode 954 – Array of Doubled Pairs. See Java, Python, and C++ code examples, complexity analysis, and interview tips.”
- **Keywords:** LeetCode 954, Array of Doubled Pairs, hash map, greedy algorithm, interview preparation, coding interview, Java solution, Python solution, C++ solution
- **Header Structure:**
  - H1: Array of Doubled Pairs – 954
  - H2: Problem Statement
  - H2: Key Insights (Good / Bad / Ugly)
  - H2: Full Code (Java / Python / C++)
  - H2: Complexity Analysis
  - H2: Interview Tips

> **Why this helps you land a job?**  
> By mastering this classic “pair‑matching” problem and presenting a clean, multi‑language solution, you demonstrate:  
> * a strong grasp of data structures (hash maps, counters),  
> * ability to reason about edge cases (zeros, negatives, overflow),  
> * experience in writing clear, commented code that’s easy to review.  
> All of which are highly sought after by top tech recruiters.

---

## 7. Take‑away Checklist

- [ ] Count frequencies with a hash map / Counter.  
- [ ] Sort by absolute value, not by raw value.  
- [ ] Greedy pairing: consume counts in one pass.  
- [ ] Handle zero as a special case (even count).  
- [ ] Use 64‑bit arithmetic for `2 * x` if necessary.  
- [ ] Test on edge cases:  
  - `[0, 0]` → `true`  
  - `[1, 2, 2, 4]` → `true`  
  - `[1, 2, 4, 8]` → `false`  
  - `[-1, -2, -4, -8]` → `true`

---

### Final Thought

The “Array of Doubled Pairs” problem is a microcosm of many interview challenges: you must think in terms of *pairs*, respect *ordering constraints*, and be wary of pitfalls like **zeros** and **negative numbers**. Mastering this problem will give you confidence for a whole class of “pairing” questions that frequently appear on coding interviews.

Good luck, happy coding, and may your `canReorderDoubled` function always return `true` for the right inputs!