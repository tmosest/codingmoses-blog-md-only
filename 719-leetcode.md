---
title: LeetCode 719. Find K-th Smallest Pair Distance - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. 3‑Language Solution – “Find K‑th Smallest Pair Distance”

Below you’ll find ready‑to‑paste, fully‑working code for **Java**, **Python**, and **C++** that solves LeetCode 719 – *Find K‑th Smallest Pair Distance*.

All three implementations use the same idea:

* **Sort the array** – distances are easier to count when the numbers are ordered.
* **Binary search on the answer** – the minimal distance we’re looking for lies between `0` and `max(nums)-min(nums)`.
* **Two‑pointer (sliding window) counting** – for a candidate distance `mid` we count how many pairs have distance ≤ `mid` in linear time.

The code is heavily commented so you can drop it straight into your favourite editor.

---

### Java

```java
import java.util.Arrays;

public class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        // 1️⃣  Sort the array
        Arrays.sort(nums);
        int n = nums.length;

        // 2️⃣  Binary search on the distance
        int low = 0;                                 // minimal possible distance
        int high = nums[n - 1] - nums[0];             // maximal possible distance

        while (low < high) {
            int mid = low + (high - low) / 2;        // avoid overflow

            // Count pairs with distance <= mid
            long cnt = countPairs(nums, mid);

            if (cnt < k) {
                low = mid + 1;                      // need larger distances
            } else {
                high = mid;                          // mid might be the answer
            }
        }
        return low;                                 // low == high
    }

    // Two‑pointer sliding window that returns the number of pairs
    // whose absolute difference is <= maxDist.
    private long countPairs(int[] nums, int maxDist) {
        long count = 0;
        int left = 0;
        for (int right = 0; right < nums.length; right++) {
            while (nums[right] - nums[left] > maxDist) {
                left++;          // shrink window until difference <= maxDist
            }
            count += right - left; // all indices between left and right-1 form valid pairs with right
        }
        return count;
    }
}
```

---

### Python 3

```python
from bisect import bisect_right
from typing import List

class Solution:
    def smallestDistancePair(self, nums: List[int], k: int) -> int:
        # 1️⃣  Sort the array
        nums.sort()
        n = len(nums)

        # 2️⃣  Binary search on the distance
        low, high = 0, nums[-1] - nums[0]

        while low < high:
            mid = (low + high) // 2

            # Count pairs with distance <= mid
            cnt = self._count_pairs(nums, mid)

            if cnt < k:
                low = mid + 1   # need larger distances
            else:
                high = mid       # mid might be the answer

        return low

    @staticmethod
    def _count_pairs(nums: List[int], max_dist: int) -> int:
        """Return number of pairs (i, j) with |nums[i]-nums[j]| <= max_dist."""
        count = 0
        left = 0
        for right in range(len(nums)):
            # shrink window until the distance is not larger than max_dist
            while nums[right] - nums[left] > max_dist:
                left += 1
            count += right - left          # all indices in [left, right-1] pair with right
        return count
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int smallestDistancePair(vector<int>& nums, int k) {
        // 1️⃣  Sort the array
        sort(nums.begin(), nums.end());
        int n = nums.size();

        // 2️⃣  Binary search on the distance
        int low = 0;
        int high = nums.back() - nums.front();  // maximum possible distance

        while (low < high) {
            int mid = low + (high - low) / 2;

            long long cnt = countPairs(nums, mid);

            if (cnt < k) {
                low = mid + 1;                   // need larger distances
            } else {
                high = mid;                       // mid might be the answer
            }
        }
        return low;  // low == high
    }

private:
    // Two‑pointer sliding window – returns number of pairs with distance <= maxDist.
    long long countPairs(const vector<int>& nums, int maxDist) {
        long long count = 0;
        int left = 0;
        for (int right = 0; right < (int)nums.size(); ++right) {
            while (nums[right] - nums[left] > maxDist) {
                ++left;                          // shrink window
            }
            count += right - left;               // all indices between left and right-1 form valid pairs
        }
        return count;
    }
};
```

---

## 2. Blog Post – SEO‑Optimised “The Good, the Bad, and the Ugly of LeetCode 719”

> **Title:**  
> *The Good, the Bad, and the Ugly of Finding the K‑th Smallest Pair Distance (LeetCode 719)*  

> **Meta‑Description:**  
> A deep dive into LeetCode 719 – find the kth smallest pair distance. Learn the efficient binary‑search solution, pitfalls to avoid, and get ready for your next coding interview.

> **Keywords:**  
> Leetcode 719, kth smallest pair distance, binary search, two pointers, algorithm, coding interview, interview preparation, coding challenge, optimal solution.

---

### 1. Problem Recap

> **LeetCode 719 – Find K‑th Smallest Pair Distance**  
> Input: an integer array `nums` (length up to 10 000) and an integer `k`.  
> Output: the `k`‑th smallest value of `|nums[i] – nums[j]|` for all `i < j`.  
> Constraints guarantee that distances can range from `0` to `1 000 000`.

---

### 2. Brute Force vs. Optimal

| Approach | Complexity | Feasibility |
|----------|------------|-------------|
| **Brute force** – generate all `n*(n-1)/2` pairs, sort them, return the `k`‑th element | `O(n² log n)` | **Not** feasible for `n = 10 000` (≈ 50 M pairs). |
| **Two‑pointer + binary search** – linear counting inside a binary search | `O(n log(max‑value))` | **Yes**, runs comfortably in 1 s on LeetCode. |

The challenge is to avoid the quadratic blow‑up.

---

### 3. The “Good” – Why Binary Search on Answer Works

1. **Monotonicity**  
   If we ask “How many pairs have distance ≤ d?” the answer is a monotone non‑decreasing function of `d`.  
   *If you can answer this counting query quickly, you can binary‑search the exact `d` that gives exactly `k` pairs.*

2. **Bounded Search Space**  
   After sorting, the minimal possible distance is `0` (identical elements) and the maximal is `max(nums) - min(nums)`.  
   That gives a small, well‑defined interval to binary‑search over.

3. **Logarithmic Steps**  
   The binary search requires at most `⌈log₂(10⁶)⌉ ≈ 20` iterations – each of which can be handled in linear time.

---

### 4. The “Bad” – Common Pitfalls

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| **Counting with `int` overflow** | `n` up to 10 000 ⇒ ~50 M pairs, which may overflow 32‑bit `int`. | Use `long` (Java) or `long long` (C++) / Python’s arbitrary‑precision `int`. |
| **Wrong binary‑search bounds** | If `high` is set to `max(nums)-min(nums)+1`, the loop may never terminate. | Set `high = max(nums)-min(nums)` and loop `while(low < high)`. |
| **Two‑pointer off‑by‑one** | Mis‑counting the number of valid pairs for a window. | After shrinking the window, add `right-left` (not `right-left+1`). |
| **Not sorting first** | Counting with unsorted data gives wrong results. | **Always** sort before counting. |

---

### 5. The “Ugly” – Edge Cases & Hidden Tricks

1. **Duplicate Numbers**  
   If `nums` contains many duplicates, the answer can be `0`.  
   Two‑pointer logic still works: the window never shrinks because `nums[right] - nums[left]` stays `0`.

2. **Large `k` (nearly all pairs)**  
   When `k` equals `n*(n-1)/2`, the answer is simply `max(nums)-min(nums)`.  
   The binary search will converge correctly, but the count routine must still handle the full range.

3. **Tight Memory Constraints (C++ on LeetCode)**  
   Avoid `vector<vector<int>>` or heavy recursion; the two‑pointer method uses only a few indices.

4. **Python’s GIL vs. C++ speed**  
   Even though Python is slower, the `O(n log M)` algorithm still passes because `n=10⁴`.  
   Using `list.sort()` (Timsort) and a manual loop keeps it fast.

---

### 6. Code Walk‑through (Java / Python / C++)

Below is a quick “walk‑through” that explains the three major blocks in each language.

| Section | Java | Python | C++ |
|---------|------|--------|-----|
| **Sorting** | `Arrays.sort(nums);` | `nums.sort()` | `sort(nums.begin(), nums.end());` |
| **Binary search** | `int low = 0, high = nums[n-1]-nums[0]; while(low<high){…}` | `low, high = 0, nums[-1]-nums[0]` | `int low=0, high=nums.back()-nums.front(); while(low<high){…}` |
| **Count pairs** | `long countPairs(int[] nums, int maxDist)` – two pointers | `def _count_pairs(self, nums, max_dist): …` | `long long countPairs(const vector<int>& nums, int maxDist)` – two pointers |
| **Return** | `return low;` | `return low` | `return low;` |

All three pieces of code are 100 % compliant with LeetCode’s signature and will compile/run on any standard environment.

---

## 2. Blog Post – *The Good, the Bad, and the Ugly of LeetCode 719*

> **Target Audience:** Coding interviewers, data‑structure enthusiasts, and anyone looking to ace LeetCode 719.  
> **Primary Keywords:** LeetCode 719, kth smallest pair distance, binary search, two pointers, coding interview, algorithm, optimal solution, interview preparation.

---

### 1. Problem Statement

> *You are given an array of `n` integers `nums` (1 ≤ n ≤ 10⁴, 0 ≤ nums[i] ≤ 10⁶) and an integer `k`.  
> Find the k‑th smallest absolute difference among all unordered pairs `(i, j)` with `i < j`.*

The naive solution enumerates all pairs, sorts the distances, and picks the k‑th – **O(n² log n)**, far too slow for LeetCode’s limits.

---

### 2. The Optimal Insight: Binary Search on the Distance

1. **Why Binary Search?**  
   The answer (the minimal distance that yields at least `k` pairs) is an integer in a small interval: `[0, max(nums) - min(nums)]`.  
   The counting function `cnt(d)` (“how many pairs have distance ≤ d?”) is monotone, so we can binary‑search `d`.

2. **Counting in Linear Time** – Two‑pointer / Sliding Window  
   After sorting `nums`, the array is in non‑decreasing order.  
   For a fixed `d`, we can scan the array once, maintaining a window `[left, right]` where `nums[right] - nums[left] ≤ d`.  
   Every new `right` expands the window, and every time we shift `left`, we discard pairs whose distance becomes too large.  
   The number of pairs added at step `right` is `right - left`.  
   Total work per count: **O(n)**.

3. **Full Complexity** –  
   *Binary search iterations*: `⌈log₂(10⁶)⌉ ≈ 20`  
   *Counting per iteration*: `O(n)`  
   → **Overall**: **O(n log M)**, easily passes the 1 second limit.

---

### 3. The “Good” – Strengths of the Approach

| Strength | Detail |
|----------|--------|
| **Scalability** | Handles the full range of inputs without blowing up. |
| **Determinism** | Works for all corner cases: duplicates, large `k`, `k` near the total number of pairs. |
| **Memory Friendly** | Only a few indices; no extra big data structures. |

---

### 4. The “Bad” – Things That Can Go Wrong

| Issue | Typical Mistake | Quick Fix |
|-------|----------------|-----------|
| **Overflow** | Counting in `int` on Java/C++ | Use `long` / `long long` or Python’s big int. |
| **Incorrect Bounds** | Off‑by‑one in high bound | Set `high = max(nums)-min(nums)`; loop `while(low < high)`. |
| **Mis‑counting** | Two‑pointer off‑by‑one | After shrinking window, add `right-left`. |
| **Ignoring Sorting** | Unsorted array → wrong count | **Always** sort first. |

---

### 5. The “Ugly” – Hidden Quirks

| Quirk | Impact | Strategy |
|-------|--------|----------|
| **Zero Distance** | Many duplicates → answer `0` | Two‑pointer logic remains correct; window never shrinks. |
| **Maximum `k`** | `k = n*(n-1)/2` → answer `max(nums)-min(nums)` | Binary search still works; count function yields full range. |
| **Python Speed** | GIL & interpreted loops | Use Timsort for sorting; keep loops tight and avoid unnecessary overhead. |
| **C++ Memory** | Avoid extra containers | Only indices, `sort` + `while` loop. |

---

### 6. Pseudocode

```
sort(nums)
low = 0
high = max(nums) - min(nums)

while low < high:
    mid = (low + high) // 2
    if count_pairs(mid) < k:
        low = mid + 1
    else:
        high = mid

return low
```

**count_pairs(d)**  
```
count = 0
left = 0
for right in 0 .. n-1:
    while nums[right] - nums[left] > d:
        left += 1
    count += right - left
return count
```

---

### 7. Complexity Analysis

| Step | Cost |
|------|------|
| Sorting | `O(n log n)` |
| Each binary search iteration | `O(n)` |
| Number of iterations | `O(log(max‑value))` ≈ 20 |
| **Total** | `O(n log M)` ≈ `10⁴ × 20 = 2×10⁵` operations – comfortably within time limits. |

---

### 8. Final Thoughts

LeetCode 719 is a classic example where **“binary search on the answer”** shines.  
Master this pattern: sort → count → binary search.  
Remember to watch for integer overflows and off‑by‑one errors – those are the usual culprits that derail even a logically sound solution.

With the three ready‑to‑copy implementations (Java, Python, C++), you can confidently submit, analyze runtime, and discuss trade‑offs in an interview.

Happy coding! 🚀

---  

*End of Blog Post*  

---  

> **Feel free to adapt the post to your platform’s guidelines or integrate visual aids (e.g., diagrams of the sliding window).**  

---  

*---*  

> **Disclaimer:** The code and explanations are written with clarity and brevity in mind; further optimisations (e.g., using `nth_element` or heap approaches) exist but do not beat the binary‑search solution for LeetCode 719.  

---  

*End of Document*