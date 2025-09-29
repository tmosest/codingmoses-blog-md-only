---
title: LeetCode 719. Find K-th Smallest Pair Distance - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Find K‑th Smallest Pair Distance – LeetCode 719  
*Hard* | `public int smallestDistancePair(int[] nums, int k)`  

> **Goal** – Return the `k`‑th smallest absolute difference between any two elements in `nums`.  
> **Input** – `nums` (length 2 ≤ n ≤ 10⁴, 0 ≤ nums[i] ≤ 10⁶), `k` (1 ≤ k ≤ n(n‑1)/2).  
> **Output** – The `k`‑th smallest pair distance.  

The following sections cover:

1.  Problem overview  
2.  The optimal algorithm (binary search over distance + sliding window)  
3.  Implementation in **Java**, **Python 3**, **C++**  
4.  Edge cases & common pitfalls  
5.  Good, Bad, Ugly – a quick code‑review checklist  
6.  SEO‑friendly take‑away for job‑hunters  

---

## 1. Problem Overview  

The brute‑force way would generate all \(\binom{n}{2}\) pair distances, sort them, and return the \(k\)‑th element.  
*Time* – \(O(n^2 \log n)\) → far too slow for \(n=10⁴\).  
*Space* – \(O(n^2)\) for storing all distances → impossible.  

We need a **\(O(n \log M)\)** solution, where \(M\) is the maximum possible distance (\(\max(nums)-\min(nums)\)).  
The trick is to **search on the answer** (the distance) and count how many pairs have a distance ≤ mid.  
If that count is < k, we need a larger distance; otherwise we can try a smaller one.  

---

## 2. Optimal Approach – Binary Search + Two‑Pointer  

1. **Sort** the array – \(O(n \log n)\).  
2. Binary search on the distance `d` in the range `[0, max‑min]`.  
3. For a candidate `mid`, count pairs `(i, j)` with `nums[j] - nums[i] ≤ mid` using a sliding window (two‑pointer).  
   * `left` starts at 0.  
   * For each `right` from 0 to n‑1, increment `left` while the window width exceeds `mid`.  
   * All indices between `left` and `right-1` form valid pairs with `right`. Add `right-left` to the count.  
   * This is linear, \(O(n)\).  
4. Adjust binary search bounds based on the count.  
5. Return the smallest distance whose count ≥ k.  

**Why it works**  
- The array is sorted, so any valid pair with distance ≤ `mid` is contiguous in the sorted order.  
- The sliding window counts each pair exactly once.  
- Binary search guarantees we converge to the minimal distance that satisfies the condition.

---

## 3. Code Implementations  

### 3.1 Java  

```java
import java.util.Arrays;

public class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);                       // 1. sort
        int low = 0;                             // minimal possible distance
        int high = nums[nums.length - 1] - nums[0]; // maximal possible distance

        while (low < high) {
            int mid = low + (high - low) / 2;
            long count = countPairs(nums, mid);  // 2. count pairs <= mid

            if (count < k) {                     // need larger distance
                low = mid + 1;
            } else {                             // mid is sufficient
                high = mid;
            }
        }
        return low;                              // minimal distance with count >= k
    }

    // two‑pointer linear count (uses long to avoid overflow)
    private long countPairs(int[] nums, int limit) {
        long cnt = 0;
        int left = 0;
        for (int right = 0; right < nums.length; right++) {
            while (nums[right] - nums[left] > limit) {
                left++;
            }
            cnt += right - left;                 // pairs (left … right-1, right)
        }
        return cnt;
    }
}
```

> **Why 64‑bit?**  
> The number of pairs can be up to \(\frac{10^4 \times 9999}{2} \approx 5\times10^7\), still fits in `int`.  
> Using `long` is a defensive choice when extending to larger `n`.

---

### 3.2 Python 3  

```python
class Solution:
    def smallestDistancePair(self, nums: list[int], k: int) -> int:
        nums.sort()                               # 1. sort
        low, high = 0, nums[-1] - nums[0]          # search range

        while low < high:
            mid = (low + high) // 2
            if self.count_pairs(nums, mid) < k:     # need bigger distance
                low = mid + 1
            else:                                 # mid works
                high = mid
        return low

    def count_pairs(self, nums: list[int], limit: int) -> int:
        """Linear two‑pointer count of pairs with distance <= limit."""
        left, cnt = 0, 0
        for right, val in enumerate(nums):
            while val - nums[left] > limit:
                left += 1
            cnt += right - left
        return cnt
```

---

### 3.3 C++ (C++17)  

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int smallestDistancePair(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end());                 // 1. sort
        int low = 0;
        int high = nums.back() - nums.front();           // 2. search bounds

        while (low < high) {
            int mid = low + (high - low) / 2;
            long long cnt = countPairs(nums, mid);      // 3. count pairs <= mid

            if (cnt < k) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return low;                                    // 4. answer
    }

private:
    // Linear two‑pointer count
    long long countPairs(const vector<int>& nums, int limit) {
        long long cnt = 0;
        int left = 0;
        for (int right = 0; right < (int)nums.size(); ++right) {
            while (nums[right] - nums[left] > limit) {
                ++left;
            }
            cnt += right - left;  // all i in [left, right-1] form a valid pair with right
        }
        return cnt;
    }
};
```

> All three solutions have the same time/space complexity:  
> **Time**: \(O(n \log n + n \log M)\) ≈ `O(n log M)`  
> **Space**: \(O(1)\) besides the input array (the sort is in‑place).  

---

## 4. Edge Cases & Common Pitfalls  

| Scenario | What to check | Why it matters |
|----------|---------------|----------------|
| Duplicate numbers | `low` starts at 0; countPairs will return many zero‑distance pairs. | The algorithm still finds the smallest non‑negative distance. |
| Very large `k` (close to \(n(n-1)/2\)) | Binary search ends with `low == high == max-min`. | Ensure no off‑by‑one: `count < k` → `low = mid + 1`. |
| Array already sorted | Sorting is mandatory; otherwise the sliding window logic fails. | Remember to call `Arrays.sort` / `sort(nums)` before binary search. |
| Overflow in pair count | Use `long` / `long long` for the count. | `n(n-1)/2` can exceed `int` when n = 10⁴. |
| Negative numbers? | Problem guarantees non‑negative, but if you extend to negatives, `nums[right] - nums[left]` still works because array is sorted. | No change needed. |

---

## 5. Good, Bad, Ugly – Quick Code‑Review Checklist  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Sorting** | Uses built‑in efficient sort (`Arrays.sort` / `sort`) | Forgetting to sort breaks the algorithm | Over‑sorting a huge data‑stream (e.g., using `Collections.sort` on a list of objects) |
| **Binary search bounds** | Correct `low = 0`, `high = max‑min` | Using `high = max(nums)` instead of `max‑min` inflates the search space | Off‑by‑one: `int mid = (low + high)/2` without preventing overflow |
| **Count function** | Linear two‑pointer, no extra memory | Mis‑incrementing `left` or forgetting `right-left` | Counting pairs twice or missing pairs (e.g., using `right-left+1`) |
| **Return value** | `low` after loop – minimal distance with count ≥ k | Returning `high` incorrectly | Returning `mid` inside the loop (stops too early) |
| **Type safety** | Use `long`/`long long` for pair count | Assuming `int` is enough → integer overflow | Unnecessary `unsigned` conversions (in C++ code) |

---

## 6. Alternatives & Why We Chose This One  

| Approach | Complexity | Pros | Cons |
|----------|------------|------|------|
| **K‑th order statistic + min‑heap** | \(O(n^2 \log k)\) | Works for small `n` | Still quadratic |
| **FFT + frequency array** | \(O(M \log M)\) | Can handle very large `n` if values are bounded | Requires convolution libraries, memory heavy |
| **Binary search + binary search per element** | \(O(n \log n \log M)\) | Simpler to code for interviewers | Slightly slower than linear count |

For LeetCode 719 the binary‑search‑over‑answer method is the *de facto* solution.

---

## 7. SEO‑Friendly Take‑Away – What Hiring Managers Love  

- **Keyword**: *K‑th Smallest Pair Distance LeetCode 719*  
- **Meta‑Description** (≈155 chars)  
  > “Hard LeetCode 719 solution – binary search on distance + two‑pointer sliding window. O(n log M) code in Java, Python, C++. Interview prep and job‑hunting guide.”  
- **Headers**:  
  - `# Find K-th Smallest Pair Distance – LeetCode 719`  
  - `## Optimal Algorithm: Binary Search + Sliding Window`  
  - `## Java / Python / C++ Code`  
  - `## Good, Bad, Ugly – Interview Code Checklist`  

> **Why it matters** – Recruiters search for “LeetCode 719” when looking for candidates who can solve hard array‑processing problems.  
> Include the above keywords in your blog title, first paragraph, and meta‑description to rank higher in job‑search queries.  

---

### 🎯 Final Words  

- The **binary search over answer** pattern is a powerful tool for “find‑kth‑smallest” problems.  
- Keep your **count** function linear; the sliding window is both *fast* and *clean*.  
- Don’t forget to use **`long`** for the pair count.  
- The algorithm runs comfortably for the maximum constraints (10⁴ elements).  

Drop this post into your portfolio, tweak the comments for your style, and hit “Publish”! Good luck on the interview floor.