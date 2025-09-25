---
title: LeetCode 2111. Minimum Operations to Make the Array K-Increasing - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Overview

**LeetCode #2111 – Minimum Operations to Make the Array K‑Increasing**  
*Difficulty: Hard | Public*

> Given a 0‑indexed array `arr` of positive integers and an integer `k`, an array is **K‑increasing** if  
> `arr[i‑k] ≤ arr[i]` for every `k ≤ i < n`.  
> In one operation you can replace any element `arr[i]` by *any* positive integer.  
> **Return the minimum number of operations required to make the array K‑increasing.**

| Example | Input | Output | Explanation |
|---------|-------|--------|-------------|
| 1 | `arr = [5,4,3,2,1]`, `k = 1` | `4` | Must become non‑decreasing → change 4 elements |
| 2 | `arr = [4,1,5,2,6,2]`, `k = 2` | `0` | Already K‑increasing |
| 3 | `arr = [4,1,5,2,6,2]`, `k = 3` | `2` | Change positions 3 and 5 |

`1 ≤ arr.length ≤ 10^5` – the solution must be **O(n log n)**.

---

## 2.  High‑Level Idea – “LIS in Disguise”

1. **Break the array into `k` independent subsequences**  
   For every start offset `s (0 … k‑1)` take the elements `arr[s], arr[s+k], arr[s+2k] …`.  
   Each of those subsequences must be **non‑decreasing** in the final array because of the
   definition of K‑increasing.

2. **Keep the longest non‑decreasing subsequence (LNDS) inside each subsequence**  
   The elements that belong to the LNDS can stay unchanged; everything else must be
   updated.  
   For a subsequence of length `m` with LNDS length `ℓ` we need `m – ℓ` operations.

3. **Sum the required operations over the `k` subsequences**  
   The answer is  
   ```
   total_ops = Σ( subsequence_length – LNDS_length )
   ```

4. **Compute LNDS in O(m log m) time**  
   The classical patience‑sorting / binary‑search LIS algorithm works for
   *non‑decreasing* sequences if we use **upper_bound** (the first element
   > current value) instead of lower_bound.  
   Complexity per subsequence: `O(m log m)`.  
   Summed over all `k` subsequences: `O(n log n)`.

---

## 3.  Why This Works

- The K‑increasing constraint splits the array into independent “tracks”.
  Each track behaves like a classic “make array non‑decreasing with minimum updates”.
- For a single track the optimal strategy is exactly: *keep a longest non‑decreasing subsequence*.
  Any other element must be changed, because it breaks the non‑decreasing property.
- The patience‑sorting algorithm finds the LNDS length in `O(m log m)` while only using
  a single vector of “tails” – no DP table needed.

---

## 4.  Complexity Analysis

| Step | Cost | Reason |
|------|------|--------|
| Split into `k` subsequences | `O(n)` | Simple indexing |
| Compute LNDS per subsequence | `O(m log m)` | Patience sorting |
| Sum over all subsequences | `O(n)` | Adding integers |
| **Total** | **`O(n log n)`** | `n = arr.length` |
| **Extra space** | `O(n)` (worst case all tails stored at once) | Storing one tail vector per track |

---

## 5.  Reference Implementations

Below you’ll find a clean, self‑contained implementation for **Java**, **Python** and **C++**.
All three versions use the same patience‑sorting LNDS routine.

> **NOTE**:  
> * In **C++** and **Java** we need a custom `upperBound` because the standard library
> functions work with *strict* comparisons.  
> * In **Python** we can use `bisect_right` which is equivalent to `upper_bound`.

### 5.1  Java (Java 17)

```java
import java.util.*;

public class Solution {
    // ----- Main API ---------------------------------------------------------
    public int kIncreasing(int[] arr, int k) {
        int n = arr.length;
        long answer = 0;              // use long to avoid overflow when n=1e5

        // Process each of the k independent tracks
        for (int start = 0; start < k; start++) {
            List<Integer> track = new ArrayList<>();
            for (int idx = start; idx < n; idx += k) {
                track.add(arr[idx]);
            }
            int m = track.size();
            int lnnds = longestNonDecreasingSubseq(track);
            answer += (m - lnnds);
        }
        return (int) answer;
    }

    // ----- LNDS using patience sorting (upper_bound) -----------------------
    private int longestNonDecreasingSubseq(List<Integer> track) {
        // tails[i] = smallest possible last element of an LNDS of length i+1
        int[] tails = new int[track.size()];
        int size = 0;               // current number of tails

        for (int value : track) {
            int pos = upperBound(tails, 0, size, value);
            tails[pos] = value;
            if (pos == size) size++;   // a longer subsequence found
        }
        return size;
    }

    // Equivalent to C++ std::upper_bound: first element > target
    private int upperBound(int[] arr, int left, int right, int target) {
        int lo = left, hi = right;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (arr[mid] <= target) {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        return lo;
    }
}
```

> **Why upperBound?**  
> For a non‑decreasing LNDS we replace the *first element that is greater* than the
> current value; if all existing tails are ≤ value we append a new tail.

### 5.2  Python (Python 3)

```python
import bisect
from typing import List

class Solution:
    def kIncreasing(self, arr: List[int], k: int) -> int:
        total_ops = 0
        n = len(arr)

        # Process each of the k independent tracks
        for start in range(k):
            track = [arr[start + i * k] for i in range((n - start + k - 1) // k)]
            lnnds = self._lnnds_length(track)
            total_ops += len(track) - lnnds

        return total_ops

    # Patience sorting variant for non‑decreasing subsequence
    def _lnnds_length(self, seq: List[int]) -> int:
        tails = []                         # tails[i] = smallest last value of LNDS of len i+1
        for v in seq:
            # upper_bound: first element > v
            idx = bisect.bisect_right(tails, v)
            if idx == len(tails):
                tails.append(v)
            else:
                tails[idx] = v
        return len(tails)
```

### 5.3  C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int kIncreasing(vector<int>& arr, int k) {
        int n = arr.size();
        long long ans = 0;

        // For every starting offset build the corresponding track
        for (int start = 0; start < k; ++start) {
            vector<int> track;
            for (int i = start; i < n; i += k) track.push_back(arr[i]);

            int m = track.size();
            int lnnds = longestNonDecreasing(track);
            ans += m - lnnds;                // operations for this track
        }
        return static_cast<int>(ans);
    }

private:
    // Patience sorting for non‑decreasing LNDS
    int longestNonDecreasing(const vector<int>& v) {
        vector<int> tails;                  // tails[i] = minimal last value of LNDS of len i+1
        for (int x : v) {
            // upper_bound : first element > x
            auto it = upper_bound(tails.begin(), tails.end(), x);
            if (it == tails.end())
                tails.push_back(x);
            else
                *it = x;
        }
        return static_cast<int>(tails.size());
    }
};
```

All three implementations share the same logic and run in `O(n log n)` time and `O(n)` auxiliary space.

---

## 5.  “Good – Bad – Ugly” Quick Take

| Aspect | What it is | Why it matters |
|--------|------------|----------------|
| **Good** | *Patience‑sorting LNDS* – linear‑time splitting + `O(log n)` updates per element. | Gives an optimal, proven‑optimal solution in one sweep. |
| **Bad** | Handling duplicates (`upper_bound` vs `lower_bound`) can be a source of bugs. | A wrong bound leads to an *over‑optimistic* LNDS length and wrong answer. |
| **Ugly** | The problem statement’s wording (“replace by *any* positive integer”) can mislead into thinking we must keep original values. | In fact we can *replace* with *any* value; we only care about relative ordering. |

> **Interview Tip**  
> In a technical interview, ask the interviewer to confirm the track‑decomposition
> idea – it saves time and shows you understand the problem structure.

---

## 6.  Blog Article – “LeetCode 2111: From K‑Increasing to LIS in Disguise”

> *SEO keywords: “LeetCode 2111”, “Minimum Operations to Make the Array K‑Increasing”, “K‑increasing array”, “Longest Non‑Decreasing Subsequence”, “Job interview algorithm”, “LIS in disguise”, “K‑track dynamic programming”.*

---

### 🎯  Master LeetCode 2111 – K‑Increasing Arrays (and Nail Your Next Tech Interview)

If you’re preparing for a software‑engineering interview, **LeetCode 2111** is a *must‑knock* because it combines two classic topics:

1. **K‑increasing array constraint** – a subtle split into independent tracks.
2. **Longest Non‑Decreasing Subsequence (LNDS)** – a classic O(n log n) algorithm.

Below you’ll find a step‑by‑step solution, reference code in Java/Python/C++, and a quick “good‑bad‑ugly” cheat‑sheet that you can flash in an interview.

---

#### 📌  What Is a K‑Increasing Array?

> `arr[i‑k] ≤ arr[i]` for every `k ≤ i < n`.

Think of `k` parallel rails.  Every element must be ≥ the element that sits `k` positions before it.

---

#### 🚀  The “LIS in Disguise” Strategy

1. **Split the array into `k` tracks**  
   `track_s = arr[s], arr[s+k], arr[s+2k], …`

2. **For each track keep the longest non‑decreasing subsequence (LNDS)**  
   *All other elements must be updated.*

3. **Sum updates across tracks** – that’s the answer.

The key to an *optimal* solution is the **patience‑sorting (binary‑search) LNDS algorithm**.  
For each element we maintain a “tail” array and use **upper_bound** to keep the array sorted.

---

#### ⚡  Why This is a Great Interview Question

- **Shows algorithmic depth** – splitting into tracks is non‑obvious.  
- **Demonstrates mastery of LIS** – a staple in many interview sets.  
- **Highlights careful handling of equal values** – subtle use of `upper_bound`.  
- **Time complexity** – you need to prove `O(n log n)`.

---

#### 📦  Reference Implementations

```java
// Java 17 – K‑Increasing via LNDS on each k‑track
public class Solution {
    public int kIncreasing(int[] arr, int k) {
        int n = arr.length, ans = 0;
        for (int start = 0; start < k; start++) {
            List<Integer> track = new ArrayList<>();
            for (int i = start; i < n; i += k) track.add(arr[i]);

            int len = track.size();
            int lnnds = lengthOfLNDS(track);   // patience sorting
            ans += len - lnnds;                // elements to change
        }
        return ans;
    }

    private int lengthOfLNDS(List<Integer> seq) {
        int[] tails = new int[seq.size()];
        int m = 0;                                 // current size
        for (int v : seq) {
            int pos = upperBound(tails, 0, m, v);
            tails[pos] = v;
            if (pos == m) m++;
        }
        return m;
    }

    private int upperBound(int[] a, int l, int r, int x) {
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (a[mid] <= x) l = mid + 1;
            else r = mid;
        }
        return l;
    }
}
```

```python
# Python 3 – Using bisect_right for upper_bound
from bisect import bisect_right

class Solution:
    def kIncreasing(self, arr, k):
        n, ans = len(arr), 0
        for start in range(k):
            track = arr[start::k]
            lnnds = self.lnnds(track)
            ans += len(track) - lnnds
        return ans

    def lnnds(self, seq):
        tails = []
        for v in seq:
            i = bisect_right(tails, v)
            if i == len(tails): tails.append(v)
            else: tails[i] = v
        return len(tails)
```

```cpp
// C++17 – K‑Increasing using upper_bound on each k‑track
class Solution {
public:
    int kIncreasing(vector<int>& arr, int k) {
        long long ans = 0;
        for (int s = 0; s < k; ++s) {
            vector<int> track;
            for (int i = s; i < arr.size(); i += k) track.push_back(arr[i]);

            int lnnds = longestNonDecreasing(track); // patience sorting
            ans += (int)track.size() - lnnds;
        }
        return (int)ans;
    }

private:
    int longestNonDecreasing(const vector<int>& v) {
        vector<int> tails;
        for (int x : v) {
            auto it = upper_bound(tails.begin(), tails.end(), x);
            if (it == tails.end()) tails.push_back(x);
            else *it = x;
        }
        return tails.size();
    }
};
```

All three solutions run in `O(n log n)` time, the *golden standard* for LIS‑based problems.

---

#### 📌  Good‑Bad‑Ugly Cheat‑Sheet

- **Good** – Linear track splitting + `O(log n)` tail updates = *optimal* solution.
- **Bad** – Choosing the wrong bound (`lower_bound` instead of `upper_bound`) → incorrect LNDS.
- **Ugly** – Thinking the problem requires *keeping original values*; you can replace with *any* integer, only relative ordering matters.

---

### 🚀  Takeaway

*LeetCode 2111 is more than a math‑puzzle; it’s a showcase of deep algorithmic reasoning and efficient implementation.*

- **Use the reference code** to test your own solutions.  
- **Ask your interviewer**: “Do you confirm the `k`‑track decomposition?” – a quick sanity check.  
- **Remember**: the subtlety is in the *bound* you use for equal values.

Good luck nailing the question – you’ll be a step closer to landing that software‑engineering role!

--- 

> **Stay tuned** for more “From LeetCode to Job” deep dives. Drop your favorite LeetCode problems in the comments!  

--- 

That’s it. You’ve got the theory, the code, and a quick cheat‑sheet ready for the interview. Happy coding!