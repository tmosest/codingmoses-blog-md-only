---
title: LeetCode 2111. Minimum Operations to Make the Array K-Increasing - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2111. Minimum Operations to Make the Array K‑Increasing  
### The Good, the Bad, and the Ugly  
*An SEO‑friendly guide that will help you ace the interview and get hired.*

---

### Table of Contents  

| Section | What you’ll learn |
|---------|-------------------|
| 🎯 Problem Overview | The formal statement and constraints |
| 🏁 Naïve & Brute‑Force | Why a simple DP fails |
| ⚡️ Optimal Solution | `LIS in disguise` – O(n log n) |
| 📚 Code | Java, Python, and C++ implementations |
| 🔍 Edge Cases | Common pitfalls and how to avoid them |
| 📈 Complexity | Time & space |
| 📖 Summary | Take‑aways for your next interview |

> **SEO Keywords**: *LeetCode 2111*, *Minimum Operations to Make the Array K‑Increasing*, *LIS in disguise*, *Longest Non‑Decreasing Subsequence*, *k‑increasing array*, *Java Python C++ solution*, *dynamic programming*, *binary search*, *O(n log n)*

---

## 1. Problem Overview

You’re given a 0‑indexed array `arr` of `n` positive integers and a positive integer `k`.

> **Definition**  
> The array is *k‑increasing* if for every `i` with `k ≤ i < n` we have  
> `arr[i‑k] ≤ arr[i]`.

> **Operation**  
> In one operation you may choose any index `i` and change `arr[i]` into any positive integer.

> **Goal**  
> Return the *minimum* number of operations required to transform the given array into a k‑increasing array.

### Constraints

```
1 ≤ arr.length ≤ 10^5
1 ≤ arr[i] ≤ 10^5
1 ≤ k ≤ arr.length
```

---

## 2. The Naïve Idea (and Why It Fails)

A first instinct is to treat the whole array as a single sequence and try to make it non‑decreasing.  
For `k = 1` this reduces to the classic “minimum changes to make an array non‑decreasing”, which is equivalent to `n – LIS(arr)`.  

But for general `k` you might think of:

```
for each i from 0 to k-1:
    consider subarray arr[i], arr[i+k], arr[i+2k], ...
    fix that subarray
```

If you apply a quadratic DP (O(m²) where *m* is the subarray length) for each subarray, the total cost becomes O(k * (n/k)²) = O(n²/k), which blows up for `n = 10^5`.

So the brute force is **O(n²)** in the worst case—impossible to pass.

---

## 3. The Optimal Insight: *LIS in Disguise*

### Observation

Each index `i` belongs to exactly one of `k` independent “k‑separated” subarrays:

```
S0 = arr[0], arr[k], arr[2k], ...
S1 = arr[1], arr[1+k], arr[1+2k], ...
...
Sk‑1 = arr[k-1], arr[2k-1], arr[3k-1], ...
```

The condition `arr[i‑k] ≤ arr[i]` is *exactly* the same as demanding that every subarray `Sx` be **non‑decreasing**.

### What does “minimum operations” mean for a single subarray?

If you keep some elements unchanged, the rest must be replaced.  
The best you can do is to keep a **Longest Non‑Decreasing Subsequence (LNDS)** of that subarray and change all the other elements.

Hence, for subarray `S`:

```
operations(S) = |S| – LNDS(S)
```

The answer for the whole array is the sum over all `k` subarrays:

```
answer = Σ (|Sx| – LNDS(Sx))  =  n – Σ LNDS(Sx)
```

So we need a fast algorithm to compute LNDS for many short sequences.

---

## 4. Computing LNDS in O(m log m)

The classic O(m log m) algorithm for the Longest Increasing Subsequence (LIS) also works for **non‑decreasing** subsequences if you change the binary‑search condition to *“greater‑than”*.

Algorithm sketch for a sequence `seq`:

```
ends = []                     # smallest possible last value for a subsequence of length i+1
for v in seq:
    pos = first index where ends[pos] > v   (bisect_right for non‑decreasing)
    if pos == len(ends):   ends.append(v)
    else:                 ends[pos] = v
return len(ends)
```

Because we only *replace* an element that is larger than `v`, the subsequence represented by `ends` stays sorted and keeps the optimal length.

---

## 5. Final Algorithm (Pseudocode)

```
1. Build an array ends[0 … k-1] – each entry is an empty vector.
2. For each subarray Sx (x = 0 … k-1):
        m = 0
        for each element v in Sx:
            pos = first index in ends[x] with ends[x][pos] > v   (bisect_right)
            if pos == m:          ends[x].append(v)      // extend LNDS
            else:                 ends[x][pos] = v
        // after the loop, m = LNDS(Sx)
        operations += |Sx| - m
3. Return operations
```

The inner binary search runs in `O(log m)`.  
Each element participates in **exactly one** subarray, so the total runtime is

```
O( Σ ( |Sx| log |Sx| ) )  =  O( n log (n/k) )  =  O( n log n )
```

The memory usage is `O(k)` for the `k` vectors plus `O(n)` for the array itself.

---

## 5. Reference Implementations

Below are three implementations that run in `O(n log n)` and satisfy the LeetCode time limits.  
Feel free to copy‑paste, test, and tweak.

### 5.1 Java (using `ArrayList` and `Collections.binarySearch`)

```java
import java.util.*;

class Solution {
    public int kIncreasing(int[] arr, int k) {
        int n = arr.length;
        int totalLnds = 0;

        // For each of the k separated subsequences
        for (int start = 0; start < k; start++) {
            List<Integer> subseq = new ArrayList<>();
            for (int idx = start; idx < n; idx += k) {
                subseq.add(arr[idx]);
            }
            totalLnds += lndsLength(subseq);
        }

        return n - totalLnds;            // n – Σ LNDS(Sx)
    }

    // Longest Non‑Decreasing Subsequence (LNDS) – O(m log m)
    private int lndsLength(List<Integer> seq) {
        List<Integer> tail = new ArrayList<>();   // tail[i] = minimal last value of LNDS of length i+1

        for (int v : seq) {
            int pos = Collections.binarySearch(tail, v, Comparator.naturalOrder());
            if (pos < 0) pos = -(pos + 1);        // first index where tail[pos] > v
            if (pos == tail.size())
                tail.add(v);                     // extend
            else
                tail.set(pos, v);                 // replace
        }
        return tail.size();
    }
}
```

### 5.2 Python (using `bisect`)

```python
from bisect import bisect_right
from typing import List

class Solution:
    def kIncreasing(self, arr: List[int], k: int) -> int:
        n = len(arr)
        total_lnds = 0

        for start in range(k):
            subseq = arr[start:n:k]
            total_lnds += self.lnds_length(subseq)

        return n - total_lnds

    def lnds_length(self, seq: List[int]) -> int:
        tail = []          # minimal possible last element of LNDS of each length
        for v in seq:
            pos = bisect_right(tail, v)     # find first tail[pos] > v
            if pos == len(tail):
                tail.append(v)
            else:
                tail[pos] = v
        return len(tail)
```

### 5.3 C++ (using `vector` and `upper_bound`)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int kIncreasing(vector<int>& arr, int k) {
        int n = arr.size();
        int totalLnds = 0;

        for (int start = 0; start < k; ++start) {
            vector<int> subseq;
            for (int idx = start; idx < n; idx += k)
                subseq.push_back(arr[idx]);

            totalLnds += lndsLength(subseq);
        }

        return n - totalLnds;   // n - Σ LNDS
    }

private:
    int lndsLength(const vector<int>& seq) {
        vector<int> tail;              // minimal last value for LNDS of each length
        for (int v : seq) {
            auto it = upper_bound(tail.begin(), tail.end(), v);  // first > v
            if (it == tail.end())
                tail.push_back(v);
            else
                *it = v;
        }
        return (int)tail.size();
    }
};
```

> **Why the C++ version uses `upper_bound`**  
> `upper_bound` finds the first element **greater** than `v`, which is perfect for *non‑decreasing* subsequences.  
> If we used `lower_bound` (first **not less**), we would mistakenly enforce strictly increasing subsequences.

---

## 5.4 Common Pitfalls (The Ugly)

| Mistake | Why it breaks | Fix |
|---------|----------------|-----|
| **Using LIS instead of LNDS** | Strictly increasing removes equal elements, giving a smaller subsequence | Replace `lower_bound` with `upper_bound` (or `bisect_right`) |
| **Treating the whole array as one sequence** | Violates the independence of the k‑separated subarrays | Split into `k` subarrays as shown |
| **Not handling empty subarray** | For `k = n` the subarray lengths are 1; `LNDS` should be 1 | The algorithm naturally handles it (`|S|=1, LNDS=1`) |
| **Using `bisect_left` instead of `bisect_right`** | Fails when there are repeated values, producing wrong LNDS length | Use `bisect_right` for non‑decreasing |
| **O(n log n) per subarray** | Rebuilding `ends` array for each subarray leads to O(nk log n) | Process each index only once, keep `ends` per subsequence |

---

## 6. Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n log n)` (each element part of one LNDS computation) | `O(n log n)` | `O(n log n)` |
| **Space** | `O(k)` auxiliary lists + input array | `O(k)` auxiliary lists + input array | `O(k)` auxiliary lists + input array |
| **Why it passes** | `n = 10^5`, `log n ≈ 17`, so about 1.7 M operations – comfortably under the 1‑second limit on LeetCode |

---

## 7. Quick Test Cases

```python
# Python version
sol = Solution()

assert sol.kIncreasing([1,2,3], 1) == 0          # already non‑decreasing
assert sol.kIncreasing([3,1,2,4,5], 2) == 1
assert sol.kIncreasing([5,4,3,2,1], 3) == 2
```

> Use a random array generator to sanity‑check both Python and C++ against the Java solution.

---

## 8. Take‑Aways for Your Next Interview

1. **Recognize independence** – the k‑separated subarrays are *independent* constraints.
2. **Reduce to a known problem** – LNDS is the exact sub‑problem.  
   The whole array transforms into “sum of (subarray length – LNDS)”.
3. **Leverage O(n log n)** LIS algorithm for each subarray.  
   It’s faster than DP and works for up to 10⁵ elements.
4. **Implement cleanly** – use `upper_bound` (C++), `bisect_right` (Python), or `Collections.binarySearch` (Java) for the non‑decreasing variant.
5. **Edge‑cases** – always test `k = 1`, `k = n`, and random large inputs.
6. **Explain your thought process** – interviewers love seeing the “why” behind the code.

---

### Final Thought

The beauty of LeetCode 2111 lies in turning a seemingly hard “array‑rearrangement” problem into a handful of classic LIS problems. Once you spot the *k‑separated* independence, the rest follows naturally.  

Show this trick in your next interview and you’ll impress the panel with both **algorithmic insight** and **clean code**—exactly what hiring managers are looking for.

Happy coding, and may your next offer be a “k‑increasing” one! 🚀

---