---
title: LeetCode 1788. Maximize the Beauty of the Garden - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Restatement – “Maximize the Beauty of the Garden”  
**LeetCode #1788 – Hard**

You’re given an array `flowers` of length *n* (`2 ≤ n ≤ 10⁵`).  
`flowers[i]` is an integer in the range `[-10⁴, 10⁴]` and represents the beauty of the *i*‑th flower in a line.

A **valid garden** must satisfy

1. It contains at least two flowers.  
2. The first and the last flower in the remaining sequence have the **same beauty value**.

You may delete any number of flowers (including none) while keeping the relative order of the rest.  
The beauty of a garden is the sum of the beauty values of its remaining flowers.

**Goal** – after deletions, obtain a valid garden with the maximum possible beauty, and return that beauty.

> **Example 1**  
> Input: `[1,2,3,1,2]` → Output: `8`  
> One optimal garden: `[2,3,1,2]` (beauty = 2 + 3 + 1 + 2 = 8)

> **Example 3**  
> Input: `[-1,-2,0,-1]` → Output: `-2`  
> Optimal garden: `[-1,-1]` (beauty = -1 + (-1) = -2)

The LeetCode statement guarantees that at least one valid garden can be formed.



--------------------------------------------------------------------

## 2.  Key Observations

| Observation | Why it matters |
|-------------|----------------|
| **We only need the first and the last occurrence of each value.** | Any later occurrence can only give a smaller or equal sub‑sequence because we can delete the earlier ones. |
| **Between the first and last occurrence we should keep only non‑negative flowers.** | Negative flowers would lower the total beauty; we can delete them arbitrarily. |
| **The beauty contributed by the first and last flower is `2 × value`.** | Both ends must have the same value. |
| **The total sum of non‑negative flowers between indices `l` and `r` can be answered with a prefix sum.** | We can pre‑compute an array where `prefix[i]` = sum of `max(0, flowers[j])` for `j ≤ i`. Then the sum between `(l, r)` is `prefix[r‑1] – prefix[l]`. |

With these observations, the problem collapses to:

```
For every distinct flower value v:
    l = first occurrence of v
    r = last  occurrence of v   (l < r)
    candidate = 2*v + (prefix[r-1] - prefix[l])
Take the maximum candidate over all v
```

All steps can be done in linear time.



--------------------------------------------------------------------

## 3.  Algorithm (O(n) time, O(n) space)

1. **Scan once to build prefix sums of non‑negative values**  
   `posPrefix[i] = posPrefix[i-1] + max(0, flowers[i])`

2. **While scanning, record first and last indices for every value**  
   Use two hash‑maps (or an array indexed by value after offsetting).

3. **Compute the best beauty**  
   For each value `v` that appears at least twice, compute  
   `beauty = 2*v + posPrefix[last[v]-1] - posPrefix[first[v]]`  
   Update the global maximum.

4. **Return the maximum** – guaranteed to exist.

The algorithm is straightforward, but careful handling of indices (`last[v]-1` must be ≥ `first[v]`) and of negative values is essential.



--------------------------------------------------------------------

## 4.  Code

Below are clean, idiomatic implementations in **Java 17**, **Python 3.11**, and **C++17**.

### 4.1 Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int maximumBeauty(int[] flowers) {
        int n = flowers.length;

        // 1. Prefix sum of positive contributions
        int[] posPrefix = new int[n];
        for (int i = 0; i < n; i++) {
            int val = Math.max(0, flowers[i]);
            posPrefix[i] = val + (i == 0 ? 0 : posPrefix[i - 1]);
        }

        // 2. First and last indices for each value
        Map<Integer, Integer> first = new HashMap<>();
        Map<Integer, Integer> last  = new HashMap<>();

        for (int i = 0; i < n; i++) {
            int v = flowers[i];
            if (!first.containsKey(v)) first.put(v, i);
            last.put(v, i);                     // keep updating – the last one
        }

        // 3. Evaluate all candidate pairs
        int best = Integer.MIN_VALUE;
        for (int v : first.keySet()) {
            int l = first.get(v);
            int r = last.get(v);
            if (l == r) continue;              // need at least two flowers

            int inner = posPrefix[r - 1] - posPrefix[l];
            int candidate = 2 * v + inner;
            best = Math.max(best, candidate);
        }

        return best;
    }
}
```

### 4.2 Python

```python
class Solution:
    def maximumBeauty(self, flowers: List[int]) -> int:
        n = len(flowers)

        # Prefix sum of non‑negative values
        pos_pref = [0] * n
        for i, x in enumerate(flowers):
            pos_pref[i] = max(0, x) + (pos_pref[i-1] if i else 0)

        first, last = {}, {}
        for i, v in enumerate(flowers):
            first.setdefault(v, i)   # first occurrence
            last[v] = i              # keep updating – last occurrence

        best = float('-inf')
        for v in first:
            l, r = first[v], last[v]
            if l == r: continue      # need at least two
            inner = pos_pref[r-1] - pos_pref[l]
            best = max(best, 2*v + inner)

        return best
```

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maximumBeauty(vector<int>& flowers) {
        int n = flowers.size();

        // Prefix sum of positive contributions
        vector<int> posPref(n);
        for (int i = 0; i < n; ++i) {
            posPref[i] = max(0, flowers[i]) + (i ? posPref[i-1] : 0);
        }

        unordered_map<int, int> first, last;
        for (int i = 0; i < n; ++i) {
            int v = flowers[i];
            if (!first.count(v)) first[v] = i; // first occurrence
            last[v] = i;                        // last occurrence
        }

        int best = INT_MIN;
        for (auto &[v, l] : first) {
            int r = last[v];
            if (l == r) continue;              // need at least two flowers
            int inner = posPref[r-1] - posPref[l];
            best = max(best, 2*v + inner);
        }
        return best;
    }
};
```

All three implementations run in **O(n)** time, use **O(n)** extra space, and correctly handle negative beauty values.



--------------------------------------------------------------------

## 5.  The Good, The Bad, and The Ugly

| Category | What Works | What’s Tricky | How to Avoid Pain |
|----------|------------|---------------|-------------------|
| **The Good** | • One‑pass solution. <br>• Only simple arithmetic and hash‑maps. <br>• Handles huge inputs (`n = 10⁵`) comfortably. | – | – |
| **The Bad** | • Negative values force careful handling – you cannot just sum the subarray. <br>• Forgetting that `last[v]-1` must be ≥ `first[v]` can lead to `IndexOutOfBounds`. | Keep the prefix sum of **non‑negative** numbers; never subtract a negative. | Pre‑write unit tests for edge cases: all negative, all positive, single value repeated many times. |
| **The Ugly** | • Some naive solutions try to keep track of all pairs or use DP, blowing up to O(n²). <br>• The “beauty” calculation involves a double count (`2*v`) – easy to forget. | Avoid over‑engineering; stick to the two‑map+prefix‑sum idea. | Keep the code short and comment the three key steps; this also helps interviewer confidence. |

> **Takeaway** – The trick is to realize that we only care about the *first* and *last* occurrence of each value, and we can drop every negative between them. Once you see that, the whole problem reduces to a single scan.



--------------------------------------------------------------------

## 6.  SEO‑Optimized Blog Title & Meta Description

**Title:**  
*“Master LeetCode 1788 – Maximize the Beauty of the Garden (Hard) – Java, Python, C++ Solutions & Interview Tips”*

**Meta Description:**  
“Learn the O(n) algorithm to solve LeetCode 1788, ‘Maximize the Beauty of the Garden’. See clean Java, Python, and C++ code, plus interview insights – the good, the bad, and the ugly. Boost your coding interview prep and land your next tech job.”

> *Why it works:* The title and description include high‑search keywords (“LeetCode 1788”, “Maximize the Beauty of the Garden”, “O(n) algorithm”, “Java/Python/C++”), directly matching what recruiters and interviewers search for.



--------------------------------------------------------------------

## 7.  Interview‑Friendly Discussion

| Question | Suggested Answer |
|----------|------------------|
| *Why do we only look at the first and last occurrence of each value?* | Any later occurrence can be paired with an earlier one to give a larger or equal subsequence. Keeping the earliest start and latest end maximizes the number of flowers we can keep. |
| *How do you handle negative beauties?* | We can delete any flower, so we skip all negative flowers between the chosen endpoints. The prefix sum stores only the sum of `max(0, value)` so the inner contribution is always non‑negative. |
| *What if a value appears only once?* | That value cannot serve as both ends of a garden; we ignore such values in the final pass. |
| *What’s the time complexity?* | Linear, O(n). Two hash‑maps and a single prefix array are built in one pass. |
| *Can this be done in O(1) space?* | Not without extra constraints. We need to remember indices for each value, which requires O(n) storage in the worst case. |

Being able to answer these succinctly demonstrates a deep understanding of the problem and its constraints.



--------------------------------------------------------------------

## 8.  Final Thoughts

LeetCode 1788 is an excellent test of *problem reduction* skills. The solution we discussed:

1. **Identifies the crucial pair (first & last occurrence).**  
2. **Uses a prefix sum of only non‑negative flowers.**  
3. **Runs in linear time, perfect for production‑grade interview answers.**

With the code snippets and the interview framing above, you’re ready to impress on the next coding challenge or technical interview. Happy coding—and best of luck landing that dream tech role!