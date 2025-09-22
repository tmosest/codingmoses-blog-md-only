---
title: LeetCode 658. Find K Closest Elements - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 658. Find K Closest Elements – From Theory to Production‑Ready Code  

**Keywords:** LeetCode 658, Find K Closest Elements, two‑pointer algorithm, binary search, priority queue, Java, Python, C++, algorithm interview, O(n) solution, interview preparation, coding interview

---

### 1️⃣ Problem Recap  

> **Given** a *sorted* integer array `arr`, and two integers `k` and `x`,  
> **Return** the `k` integers in `arr` that are closest to `x`.  
>  
> The returned list must also be sorted in ascending order.  
>  
> If two numbers are equally close to `x`, the smaller number wins.

| Example | Input | Output |
|---------|-------|--------|
| 1 | `arr = [1,2,3,4,5]`, `k = 4`, `x = 3` | `[1,2,3,4]` |
| 2 | `arr = [1,1,2,3,4,5]`, `k = 4`, `x = -1` | `[1,1,2,3]` |

**Constraints**

```
1 ≤ k ≤ arr.length ≤ 10⁴
arr is sorted in ascending order
-10⁴ ≤ arr[i], x ≤ 10⁴
```

---

### 2️⃣ Algorithmic Choices – The “Good, The Bad, The Ugly”

| Approach | Good | Bad | Ugly |
|----------|------|-----|------|
| **Two‑pointer sliding window** | O(n) time, O(1) extra space – simple to understand and code | Needs careful handling of tie‑breaking logic (abs‑differences) | Rarely used in interviews; might be overlooked compared to binary‑search tricks |
| **Binary‑search with sliding window** | O(log n) time, O(1) space – optimal for large arrays | More complex to implement correctly; off‑by‑one bugs common | Some interviewers love this “trick” but they can be picky about the comparison formula |
| **Priority queue (max‑heap of size k)** | Easy to reason about “keep the k best so far” | O(n log k) time, O(k) space – slower on large inputs | Heap operations can become a bottleneck; not the most optimal for sorted arrays |
| **Brute force + sort** | Conceptually simple | O(n log n) time, not acceptable for 10⁴ size | Too slow for typical interview constraints |

> **Bottom line:** For a sorted array, the two‑pointer sliding window is the *sweet spot* – linear time, constant space, and it naturally respects the ordering requirement.

---

### 3️⃣ Two‑Pointer Sliding Window – The “Good” Code

#### 3.1 Concept

We maintain two indices, `left` and `right`, that delimit a window of candidates.  
While the window size is greater than `k`, we drop the *farther* end of the window:

```
if |arr[left] - x| > |arr[right] - x|   →  left++   // left end is farther
else                                    →  right--  // right end is farther or equal
```

When `right - left + 1 == k`, the window contains exactly the answer.

#### 3.2 Complexity

- **Time:** O(n) – each element is examined at most once.
- **Space:** O(1) – only two indices and a result list.

---

## 📌 Java Implementation

```java
import java.util.*;

public class Solution {
    /**
     * Finds the k integers in a sorted array that are closest to x.
     *
     * @param arr the sorted array
     * @param k   number of elements to return
     * @param x   target value
     * @return a list containing the k closest elements in ascending order
     */
    public List<Integer> findClosestElements(int[] arr, int k, int x) {
        int left = 0;
        int right = arr.length - 1;

        // Shrink the window until only k elements remain
        while (right - left + 1 > k) {
            if (Math.abs(arr[left] - x) > Math.abs(arr[right] - x)) {
                left++;               // left is farther – drop it
            } else {
                right--;              // right is farther (or tie with smaller left) – drop it
            }
        }

        // Copy the window into a List<Integer>
        List<Integer> result = new ArrayList<>(k);
        for (int i = left; i <= right; i++) {
            result.add(arr[i]);
        }
        return result;
    }
}
```

> **Why the `Math.abs` comparison?**  
> The problem’s tie‑break rule (`|a-x| == |b-x|` → `a < b`) is already satisfied by keeping the *smaller* index on the left.  
> If the distances are equal, the `else` branch keeps the left side (the smaller number), exactly matching the requirement.

---

## 🐍 Python Implementation

```python
from typing import List

class Solution:
    def findClosestElements(self, arr: List[int], k: int, x: int) -> List[int]:
        left, right = 0, len(arr) - 1

        while right - left + 1 > k:
            if abs(arr[left] - x) > abs(arr[right] - x):
                left += 1
            else:
                right -= 1

        return arr[left:right+1]
```

> The Python version is almost identical – the slice `arr[left:right+1]` gives the sorted sub‑list directly.

---

## 🧩 C++ Implementation

```cpp
#include <vector>
#include <cmath>

class Solution {
public:
    std::vector<int> findClosestElements(std::vector<int>& arr, int k, int x) {
        int left = 0;
        int right = arr.size() - 1;

        while (right - left + 1 > k) {
            if (std::abs(arr[left] - x) > std::abs(arr[right] - x))
                ++left;           // drop left
            else
                --right;          // drop right
        }

        return std::vector<int>(arr.begin() + left, arr.begin() + right + 1);
    }
};
```

> Using `std::vector`’s iterator constructor makes the result copying concise.

---

## 4️⃣ Why This Solution Wins in Interviews

1. **Clear, concise logic** – No nested loops or complex data structures.
2. **Optimal time** – O(n) beats the O(n log n) sorting baseline.
3. **Space‑friendly** – Constant extra memory; fits the interview’s “optimal solution” expectations.
4. **Handles tie‑breaking implicitly** – No extra `if (a < b)` checks, just the natural order of the array.

> Interviewers love a solution that *does* something interesting *and* stays simple. The two‑pointer method tick‑les both boxes.

---

## 5️⃣ SEO‑Optimized Blog Meta

- **Title:** LeetCode 658 – “Find K Closest Elements” Algorithm Explained (Java/Python/C++)
- **Meta Description:** Master LeetCode 658 with a fast O(n) two‑pointer solution in Java, Python, and C++. Learn the good, bad, and ugly of algorithmic approaches.
- **Keywords:** LeetCode 658, Find K Closest Elements, two‑pointer algorithm, binary search, interview prep, coding interview, Java, Python, C++, algorithm explanation, O(n) solution.

---

### 6️⃣ Final Thoughts – The “Ugly” Side

Even though the two‑pointer approach is perfect for sorted input, it **won’t generalise** to unsorted arrays.  
If the input were unsorted, you’d need a max‑heap of size `k` (priority queue), which introduces `O(n log k)` time.  
Thus, always confirm that the array is sorted before choosing the sliding‑window trick.

---

### 🚀 Takeaway

For *sorted* arrays, **the two‑pointer sliding window** is your go‑to algorithm for LeetCode 658.  
It satisfies the constraints, respects the tie‑break rule, and has a clear interview‑friendly proof of correctness.

Good luck on your next interview – now you can walk away with a *clean* implementation and a blog post that will be discovered by other interviewees searching for “LeetCode 658 solutions”.