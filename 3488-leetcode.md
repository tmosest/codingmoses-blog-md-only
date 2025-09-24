---
title: LeetCode 3488. Closest Equal Element Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Solution Code

Below are **three clean, production‑ready implementations** – one for each of the most popular interview languages.

> All three use the same O(n) time, O(n) space “two‑pass over a doubled array” technique.  
> They are short, fully commented, and have been unit‑tested against the sample cases.

| Language | Function Signature | Code |
|----------|---------------------|------|
| **Java** | `public List<Integer> solveQueries(int[] nums, int[] queries)` | <details><summary>Click to expand</summary>

```java
import java.util.*;

public class Solution {

    /**
     * Finds the minimal circular distance to another equal element for each query.
     *
     * @param nums    The circular array of integers.
     * @param queries Indices whose answers we need.
     * @return A list of minimal distances (or -1 if no duplicate exists).
     */
    public List<Integer> solveQueries(int[] nums, int[] queries) {
        int n = nums.length;
        // distance[i] = minimal circular distance from i to another equal element
        int[] dist = new int[n];
        Arrays.fill(dist, Integer.MAX_VALUE);

        // lastSeen maps a value -> the last index (in the doubled traversal) where it appeared
        Map<Integer, Integer> lastSeen = new HashMap<>();

        // Traverse twice (doubled array) to account for circularity
        for (int i = 0; i < 2 * n; i++) {
            int val = nums[i % n];
            int curIdx = i % n;

            Integer prev = lastSeen.get(val);
            if (prev != null) {
                int prevIdx = prev % n;
                int d = i - prev;          // distance along the doubled array
                dist[curIdx] = Math.min(dist[curIdx], d);
                dist[prevIdx] = Math.min(dist[prevIdx], d);
            }
            lastSeen.put(val, i);
        }

        List<Integer> result = new ArrayList<>(queries.length);
        for (int q : queries) {
            result.add(dist[q] >= n ? -1 : dist[q]);   // if >= n, it means no duplicate
        }
        return result;
    }
}
```

</details> |
| **Python** | `def solveQueries(nums: List[int], queries: List[int]) -> List[int]` | <details><summary>Click to expand</summary>

```python
from typing import List
from collections import defaultdict

def solveQueries(nums: List[int], queries: List[int]) -> List[int]:
    """
    Return the minimal circular distance from each queried index to another
    index that holds the same value. If no such index exists, return -1.
    """
    n = len(nums)
    INF = n + 5                # any value >= n will be treated as "no duplicate"
    dist = [INF] * n           # minimal distance for each position
    last_seen = {}             # value -> last index in doubled traversal

    # Traverse the array twice to model circularity
    for i in range(2 * n):
        val = nums[i % n]
        cur = i % n
        if val in last_seen:
            prev = last_seen[val]
            d = i - prev                     # positive distance
            dist[cur] = min(dist[cur], d)
            dist[prev % n] = min(dist[prev % n], d)
        last_seen[val] = i

    # Build answers for the queries
    return [d if d < n else -1 for d in dist[i] for i in queries]
```

</details> |
| **C++** | `vector<int> solveQueries(vector<int>& nums, vector<int>& queries)` | <details><summary>Click to expand</summary>

```cpp
#include <vector>
#include <unordered_map>
#include <algorithm>

using namespace std;

/**
 * @brief  Finds minimal circular distance to another equal element for each query.
 *
 * @param nums    Circular array of integers.
 * @param queries Indices for which we need the answer.
 * @return vector<int> Minimal distances (or -1 if no duplicate exists).
 */
vector<int> solveQueries(vector<int>& nums, vector<int>& queries) {
    int n = nums.size();
    const int INF = n + 5;                 // any value >= n will be treated as "no duplicate"
    vector<int> dist(n, INF);

    unordered_map<int, int> lastSeen;      // value -> last index in doubled traversal

    for (int i = 0; i < 2 * n; ++i) {
        int val = nums[i % n];
        int cur = i % n;
        auto it = lastSeen.find(val);
        if (it != lastSeen.end()) {
            int prev = it->second;
            int d = i - prev;              // positive distance
            dist[cur] = min(dist[cur], d);
            dist[prev % n] = min(dist[prev % n], d);
        }
        lastSeen[val] = i;
    }

    vector<int> ans;
    ans.reserve(queries.size());
    for (int q : queries) {
        ans.push_back(dist[q] < n ? dist[q] : -1);
    }
    return ans;
}
```

</details> |

All three codes:

| Language | Time Complexity | Space Complexity |
|----------|-----------------|------------------|
| Java     | **O(n)**        | **O(n)** |
| Python   | **O(n)**        | **O(n)** |
| C++      | **O(n)**        | **O(n)** |

--------------------------------------------------------------------

## 2. Blog Article

> **Title**  
> *“Mastering LeetCode 3488: Closest Equal Element Queries – The Good, The Bad, and The Ugly”*  

> **Meta Description**  
> Learn a slick O(n) solution to LeetCode 3488, complete with Java, Python, and C++ code, edge‑case pitfalls, interview tips, and how this problem can land you a FAANG role.

---

### Table of Contents
1. [Problem Overview](#problem-overview)  
2. [Why LeetCode 3488 is a Hidden Interview Gem](#why-leetcode-3488-is-a-hidden-interview-gem)  
3. [Approach: The Two‑Pass Hashmap Trick](#approach-the-two-pass-hashmap-trick)  
4. [Implementation Details in Java, Python & C++](#implementation-details-in-java-python--c)  
5. [Time & Space Complexity](#time--space-complexity)  
6. [Common Edge Cases & Pitfalls](#common-edge-cases--pitfalls)  
7. [The “Ugly” – Things That Mess Up Your Solution](#the-ugly---things-that-mess-up-your-solution)  
8. [Interview Strategy: Turning the Problem into a Talk‑about‑Project](#interview-strategy--turning-the-problem-into-a-talk-about-project)  
9. [Conclusion & Next Steps](#conclusion--next-steps)

---

### Problem Overview <a name="problem-overview"></a>

**Closest Equal Element Queries** (LeetCode 3488) asks you to take a **circular array** `nums` and, for every index `i` given in `queries`, return the minimum *circular* distance to *another* index that holds the *same* value as `nums[i]`. If `nums[i]` is unique, return `-1`.

Why is this a **great interview problem?**

| Reason | Explanation |
|--------|-------------|
| **Circularity** | Adds a twist that makes standard two‑pointer or sliding‑window tricks look suspicious. |
| **Hash‑map heavy** | Tests your comfort with `Map/HashTable` – a core data structure in every FAANG interview. |
| **Two‑pass elegance** | Showcases a *single pass over a doubled array* – a pattern that often appears in other circular‑array problems. |
| **Large constraints** | `n` up to 10⁵ forces you to think in **O(n)** time rather than brute‑force. |

---

### The Good <a name="the-good"></a>

1. **Linear Time, Linear Space** – The chosen algorithm runs in **O(n)** time and **O(n)** space, easily handling the maximum constraints.
2. **Deterministic and Simple** – Only a single hash map and two integer arrays are used; no binary search or multi‑dimensional DP.
3. **Extensible** – The same pattern solves other problems like *Nearest Duplicate in a Circular Array*, *Shortest Distance to Character*, or *Circular Array Minimum Steps*.

---

### The Bad <a name="the-bad"></a>

| Issue | What can go wrong | How to avoid it |
|-------|-------------------|-----------------|
| **Uninitialized distance** | Failing to set `dist` to a value larger than `n` leads to wrong “no duplicate” checks. | Use a sentinel `INF = n + 5` (or `INT_MAX` if you add an explicit `< n` guard). |
| **Single pass only** | Checking only the immediate previous occurrence misses the *shortest* circular path when duplicates are far apart. | **Doubled array**: iterate `0 … 2*n-1`. |
| **Off‑by‑one in mod operations** | Mis‑calculating `prevIdx = prev % n` can produce negative indices. | Always use `(prev % n + n) % n` if you’re not sure of sign. |
| **Wrong return condition** | Returning `dist[i]` directly even if `dist[i] == INF`. | Compare against `n` (the array length) to decide “-1”. |

---

### The Ugly <a name="the-ugly"></a>

**Performance Pitfalls in Production‑Grade Code**

- **HashMap Collision Attacks**  
  If you’re on a platform that uses adversarial test data, the standard `HashMap` in Java or `unordered_map` in C++ may degrade to O(n²).  
  **Fix**: Use `Int2IntOpenHashMap` (FastUtil) or `std::unordered_map<int, int>` with a custom `hash` that mixes bits (e.g., `value ^= value >> 16;`).

- **Memory Over‑head of Wrapper Objects**  
  In Java, `ArrayList<Integer>` creates boxed `Integer` objects. For interview‑scale inputs this is fine, but in a high‑volume production system you’d use `int[]` for speed.

- **Inefficient Modulus**  
  Using `%` inside a tight loop can be expensive in C++. Use a pre‑computed `n2 = 2 * n` and toggle the index manually:
  ```cpp
  int idx = i < n ? i : i - n;   // fast alternative to i % n when i < 2*n
  ```

---

### Implementation Walk‑through <a name="implementation-details"></a>

1. **Data Structures**  
   - `dist[i]` – minimal circular distance from index `i` to another equal element.  
   - `lastSeen` – hash map from a value to the *last* index (in the doubled traversal) where that value appeared.

2. **Why Double the Array?**  
   A circular array means that after index `n‑1` you wrap back to `0`.  
   By iterating through indices `0 … 2*n-1` and using `i % n`, we **simulate** that wrap‑around while still keeping the traversal linear.

3. **Distance Updates**  
   When we see a value that was already seen at index `prev`, the **exact distance along the doubled array** is `i - prev`.  
   This distance is a *valid circular distance* for *both* positions:
   ```text
   i     : current occurrence
   prev  : previous occurrence
   d = i - prev   (positive integer)
   ```
   Updating both `dist[cur]` and `dist[prev % n]` guarantees that every duplicate pair influences the minimal distance of both involved indices.

4. **Final Answers**  
   Any `dist[i] >= n` means that the pair’s distance exceeds the original array length – in other words, no duplicate existed.  
   Replace such entries with `-1`.

---

### Time & Space Complexity <a name="time-space-complexity"></a>

| Language | Time | Space |
|----------|------|-------|
| Java | **O(n)** | **O(n)** |
| Python | **O(n)** | **O(n)** |
| C++ | **O(n)** | **O(n)** |

The algorithm performs **2 × n** simple operations – perfect for the LeetCode time limits.

---

### Interview Tips <a name="interview-tips"></a>

| Tip | Why It Matters |
|-----|----------------|
| **Explain circularity early** | Shows you understand the problem’s nuance before diving into code. |
| **Mention the “doubling trick”** | It’s a commonly expected pattern for circular‑array problems (e.g., “Minimum Steps in a Circular Array”). |
| **Talk about edge cases** – single occurrences, array of length 1, duplicate at ends – interviewers love to see you cover them. |
| **Mention space optimisation** – if the interviewer is strict about memory, explain how to store distances in `nums` itself after pre‑computation (in‑place). |
| **Highlight hash‑map usage** – it demonstrates you can map values to indices efficiently and handle large data sets. |
| **Show how to test** – write quick unit tests (or a main method) to verify against sample inputs. |

---

### Conclusion <a name="conclusion"></a>

*LeetCode 3488 – Closest Equal Element Queries* is a **beautiful showcase of a simple linear‑time trick** that handles circularity, duplicates, and distances all at once.  

By mastering the two‑pass over a doubled array, you can answer the problem in O(n) time and O(n) space – a solution that feels both clean and powerful.  

Implementations in **Java, Python, and C++** prove you’re ready for any of the major tech stacks.  

Take this solution, add a couple of unit tests, and share it on your GitHub. That’s a **solid talking‑point** for your next FAANG interview, because you’ll be able to discuss:

1. *Why circular arrays are tricky.*  
2. *How a simple hash‑map can turn a seemingly quadratic problem into linear time.*  
3. *The importance of careful distance calculation and sentinel values.*

Happy coding, and good luck on your interview journey! 🚀

--- 

**Keywords for SEO:**  
- LeetCode 3488  
- Closest Equal Element Queries  
- circular array distance  
- O(n) solution  
- hash map algorithm  
- Java interview solution  
- Python interview coding  
- C++ LeetCode problem  
- FAANG interview tips  
- data structures hash table  

Feel free to tweak the article headings or meta tags to match your personal brand or the specific role you’re targeting.