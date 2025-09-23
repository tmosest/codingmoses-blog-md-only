---
title: LeetCode 3641. Longest Semi-Repeating Subarray - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 LeetCode 3641 – Longest Semi‑Repeating Subarray  
### A “Good‑Bad‑Ugly” Deep‑Dive + SEO‑Optimized Interview Guide  

> **Keywords** – *Longest Semi‑Repeating Subarray, LeetCode 3641, Java solution, Python solution, C++ solution, sliding window, two pointers, algorithm interview, job interview, interview prep, coding interview, data‑structures, array, hashmap, space‑time complexity.*

---

### 1. Problem Statement

> **Input**  
> * `nums` – an integer array of length `n` (`1 ≤ n ≤ 10⁵`, `1 ≤ nums[i] ≤ 10⁵`)  
> * `k` – a non‑negative integer (`0 ≤ k ≤ n`)  

> **Definition** – A *semi‑repeating* subarray is a contiguous subarray in which **at most `k` distinct elements appear more than once** (i.e., repeat at least twice).  

> **Task** – Return the length of the longest semi‑repeating subarray.

---

### 2. Intuition & Core Idea

The problem is a classic *sliding window* / *two‑pointer* question:

1. **Window invariants**  
   * Expand the right pointer `r` while counting occurrences.  
   * Track how many elements have reached a **second occurrence** – this is the number of “repeating” elements.  

2. **When the invariant breaks**  
   * If the number of repeating elements exceeds `k`, slide the left pointer `l` rightwards, decrementing counts and possibly decreasing the repeating count.  

3. **Record the maximum window length**  
   * At every step, `maxLen = max(maxLen, r - l + 1)`.

Because each element enters and leaves the window exactly once, the algorithm is **O(n)** time and **O(n)** auxiliary space (hash map / array for counts).

---

### 3. Corner Cases & “The Ugly”

| Case | Why it matters | What to watch out for |
|------|----------------|-----------------------|
| `k = 0` | No element may repeat | When the second occurrence of any value is seen, immediately shrink the window. |
| All elements equal | The whole array is valid if `k ≥ 1` | The repeating counter will grow to 1 and stay 1. |
| `k` large (`≥` number of distinct values) | Entire array is always valid | The window never shrinks; just return `n`. |
| `nums` contains large values (up to 10⁵) | Fixed‑size array vs `HashMap` trade‑off | A plain array of size `max(nums)+1` (≈ 10⁵+1) is safe and faster in Java/Python; C++ can use `unordered_map`. |
| Negative numbers (not in constraints) | Would break index‑based arrays | If they existed, we must use a hash map. |

---

### 4. Reference Solution (Java)

```java
class Solution {
    public int longestSubarray(int[] nums, int k) {
        int n = nums.length;
        int maxLen = 0;
        int[] cnt = new int[100_010];          // counts, 1‑based values
        int repeats = 0;                       // number of elements that appear twice

        for (int l = 0, r = 0; r < n; ++r) {
            int val = nums[r];
            cnt[val]++;
            if (cnt[val] == 2) repeats++;      // new repeating element

            while (repeats > k) {              // shrink window
                int leftVal = nums[l];
                if (cnt[leftVal] == 2) repeats--; // losing a repeat
                cnt[leftVal]--;
                l++;
            }

            maxLen = Math.max(maxLen, r - l + 1);
        }
        return maxLen;
    }
}
```

> **Complexity** – Time `O(n)`, Space `O(max(nums))` (≈ 10⁵).  

> **Why it’s good** – Constant‑time operations, single pass, minimal overhead.  
> **Potential pitfall** – Hard‑coded array size must match the problem’s constraints; otherwise use a `HashMap`.

---

### 5. Python Implementation

```python
def longestSubarray(nums: list[int], k: int) -> int:
    from collections import defaultdict
    cnt = defaultdict(int)
    repeats = 0          # number of values that appear >= 2
    l = 0
    best = 0

    for r, val in enumerate(nums):
        cnt[val] += 1
        if cnt[val] == 2:
            repeats += 1

        while repeats > k:
            left = nums[l]
            if cnt[left] == 2:
                repeats -= 1
            cnt[left] -= 1
            l += 1

        best = max(best, r - l + 1)
    return best
```

> **Complexity** – Time `O(n)`, Space `O(min(n, distinct))`.  

> **Why it’s good** – Pythonic `defaultdict`, clear variable names, and works with large inputs.  
> **Potential pitfall** – `defaultdict` overhead; if speed is critical, consider `Counter` or a plain dict with `get`.

---

### 6. C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

int longestSubarray(vector<int>& nums, int k) {
    unordered_map<int,int> cnt;
    int repeats = 0, best = 0;
    int l = 0;
    for (int r = 0; r < (int)nums.size(); ++r) {
        int val = nums[r];
        if (++cnt[val] == 2) repeats++;          // new repeat

        while (repeats > k) {
            int left = nums[l];
            if (cnt[left] == 2) repeats--;       // lose a repeat
            if (--cnt[left] == 0) cnt.erase(left);
            ++l;
        }
        best = max(best, r - l + 1);
    }
    return best;
}
```

> **Complexity** – Time `O(n)` on average, Space `O(min(n, distinct))`.  

> **Why it’s good** – `unordered_map` handles arbitrary values, minimal memory overhead.  
> **Potential pitfall** – Worst‑case `O(n^2)` if hash collisions are terrible; can mitigate by reserving bucket count or using a custom hash.

---

### 7. Testing & Edge‑Case Validation

```python
# Quick sanity check
assert longestSubarray([1,2,3,1,2,3,4], 2) == 6
assert longestSubarray([1,1,1,1,1], 4) == 5
assert longestSubarray([1,1,1,1,1], 0) == 1
assert longestSubarray([1,2,3,4,5], 0) == 5
assert longestSubarray([1,1,2,2,3,3,4], 1) == 5   # [2,2,3,3,4]
```

Run these on all three implementations. Consider large random tests (`n = 10⁵`) to ensure linear behavior.

---

### 8. SEO‑Optimized Blog Wrap‑Up

#### Title  
**“LeetCode 3641: Longest Semi‑Repeating Subarray – Java, Python & C++ Solutions + Interview Tips”**

#### Meta Description  
Learn how to solve LeetCode 3641 (Longest Semi‑Repeating Subarray) with optimal sliding‑window algorithms. Get Java, Python, and C++ code, edge‑case analysis, and interview‑ready insights.

#### Sub‑Headers  

- **Problem Recap & Constraints** – Quick refresher.  
- **Sliding Window Strategy** – The “good” algorithmic pattern.  
- **Java / Python / C++ Code Samples** – Copy‑paste ready.  
- **Edge Cases & Debugging Tips** – “The Ugly” pitfalls to avoid.  
- **Time & Space Analysis** – Why this solution beats O(n²) brute force.  
- **Interview Prep** – How to discuss this during coding interviews.  

#### Keywords Placement  
- *Longest Semi‑Repeating Subarray* (headline, intro, conclusion)  
- *LeetCode 3641* (problem number)  
- *Java solution*, *Python solution*, *C++ solution* (code sections)  
- *Sliding window*, *two pointers*, *hashmap* (algorithm)  
- *Interview question*, *coding interview*, *job interview* (career context)

#### Closing Call‑to‑Action  
> *“Want more LeetCode walkthroughs? Subscribe for weekly posts and interview success stories.”*

---

### 9. Final Thoughts – Good, Bad, Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithm** | O(n) sliding window – optimal | None if implemented correctly | None |
| **Code Simplicity** | Straightforward pointers, single pass | Need to manage counts & repeats carefully | Mis‑counting repeats leads to wrong answer |
| **Performance** | Fast for n = 10⁵ | Slight overhead for hash maps in Python | Hash collision in C++ unordered_map could degrade |
| **Readability** | Clear variable names (`l`, `r`, `repeats`) | Array index vs map choice may confuse | Forgetting to decrement `repeats` when shrinking |
| **Scalability** | Works with large numbers | Large `max(nums)` may push memory | In languages without built‑in hash map, custom hash required |

---

#### Ready to ace the interview?  
Drop the code into your IDE, test with edge cases, and be prepared to explain the sliding window logic on the fly. With the right narrative and confidence, you’ll showcase both technical skill and problem‑solving clarity—exactly what recruiters look for. 🚀

---