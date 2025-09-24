---
title: LeetCode 259. 3Sum Smaller - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3Sum Smaller – The Good, The Bad, and The Ugly  
*(Leetcode 259 – Interview‑Ready, Java/Python/C++ Solution + SEO‑Friendly Blog)*  

> **Keyword Focus**: `3Sum Smaller`, `Leetcode 259`, `two pointers`, `O(n²)` algorithm, `Java`, `Python`, `C++`, `job interview`, `algorithm design`.

---

## Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Constraints & Edge Cases](#constraints--edge-cases)  
3. [Solution Strategy](#solution-strategy)  
   - 3.1 Two‑Pointer Technique  
   - 3.2 Why Sorting Helps  
4. [Java Implementation](#java-implementation)  
5. [Python Implementation](#python-implementation)  
6. [C++ Implementation](#c++-implementation)  
7. [Time & Space Complexity](#time--space-complexity)  
8. [The Good, The Bad, The Ugly](#the-good-the-bad-the-ugly)  
9. [Conclusion & Next Steps](#conclusion--next-steps)  

---

## Problem Overview <a name="problem-overview"></a>

**Leetcode 259 – 3Sum Smaller**  

> Given an integer array `nums` and an integer `target`, return the number of triplets `(i, j, k)` with `0 ≤ i < j < k < n` such that `nums[i] + nums[j] + nums[k] < target`.

| Example | Input | Output | Explanation |
|---------|-------|--------|-------------|
| 1 | `[-2,0,1,3]`, target `2` | `2` | Triplets: `[-2,0,1]`, `[-2,0,3]` |
| 2 | `[]`, target `0` | `0` | No triplets |
| 3 | `[0]`, target `0` | `0` | No triplets |

---

## Constraints & Edge Cases <a name="constraints--edge-cases"></a>

| Parameter | Range |
|-----------|-------|
| `nums.length` | `0 … 3500` |
| `nums[i]` | `-100 … 100` |
| `target` | `-100 … 100` |

**Edge Cases**

- Empty array or array with fewer than 3 elements → return `0`.  
- All numbers are the same.  
- Negative and positive numbers interleaved.  

---

## Solution Strategy <a name="solution-strategy"></a>

The classic approach is to sort the array first and then use a two‑pointer sweep for each fixed first element.

### 3.1 Two‑Pointer Technique

For each index `i` (the first element of the triplet), we need to count pairs `(left, right)` such that  
`nums[i] + nums[left] + nums[right] < target` with `left = i + 1`, `right = n - 1`.

Because the array is sorted:

- If `nums[i] + nums[left] + nums[right] < target`, *every* element between `left` and `right` will also satisfy the inequality for this `i` and `left` because the numbers only increase as we move `right` leftward.  
  Therefore, we can add `right - left` to the answer and increment `left`.  
- If the sum is `≥ target`, we need a smaller sum → decrement `right`.

This yields an `O(n²)` algorithm with `O(1)` extra space.

### 3.2 Why Sorting Helps

Sorting turns the problem of *finding all qualifying pairs* into a *linear scan* per `i`.  
Without sorting, we would have to check all `O(n²)` pairs explicitly, leading to `O(n³)` time.

---

## Java Implementation <a name="java-implementation"></a>

```java
import java.util.Arrays;

public class Solution {
    public int threeSumSmaller(int[] nums, int target) {
        if (nums == null || nums.length < 3) return 0;

        Arrays.sort(nums);
        int n = nums.length, count = 0;

        for (int i = 0; i < n - 2; i++) {
            int left = i + 1, right = n - 1;
            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];
                if (sum < target) {
                    // All elements between left and right work
                    count += right - left;
                    left++;
                } else {
                    right--;
                }
            }
        }
        return count;
    }
}
```

**Explanation**

- `Arrays.sort(nums)` – `O(n log n)`  
- Outer loop – `O(n)`  
- Inner two‑pointer sweep – `O(n)` per iteration → total `O(n²)`  

---

## Python Implementation <a name="python-implementation"></a>

```python
class Solution:
    def threeSumSmaller(self, nums: list[int], target: int) -> int:
        if not nums or len(nums) < 3:
            return 0

        nums.sort()
        n, count = len(nums), 0

        for i in range(n - 2):
            left, right = i + 1, n - 1
            while left < right:
                curr_sum = nums[i] + nums[left] + nums[right]
                if curr_sum < target:
                    count += right - left  # all indices between left and right work
                    left += 1
                else:
                    right -= 1
        return count
```

Python’s `sort()` is `O(n log n)`. The logic mirrors the Java version exactly.

---

## C++ Implementation <a name="c++-implementation"></a>

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int threeSumSmaller(std::vector<int>& nums, int target) {
        if (nums.size() < 3) return 0;
        std::sort(nums.begin(), nums.end());
        int n = nums.size(), count = 0;

        for (int i = 0; i < n - 2; ++i) {
            int left = i + 1, right = n - 1;
            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];
                if (sum < target) {
                    count += right - left;
                    ++left;
                } else {
                    --right;
                }
            }
        }
        return count;
    }
};
```

C++ follows the same pattern; `std::sort` handles the sorting step.

---

## Time & Space Complexity <a name="time--space-complexity"></a>

| Complexity | Explanation |
|------------|-------------|
| **Time** | `O(n log n)` (sorting) + `O(n²)` (two‑pointer loop) → overall `O(n²)` |
| **Space** | `O(1)` auxiliary (sorting in-place for Java/C++; Python list is in‑place) |

For the maximum input size (`n = 3500`), `O(n²)` ≈ 12 million iterations – easily manageable in modern environments.

---

## The Good, The Bad, The Ugly <a name="the-good-the-bad-the-ugly"></a>

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Concept** | Elegant use of sorting + two pointers. | Requires sorting → extra step. | Sorting could be overkill for very small arrays; still fine. |
| **Implementation** | Clean, O(1) extra memory. | Must handle `int` overflow in languages like C++ if inputs were larger. | None—`int` is safe with given constraints. |
| **Performance** | Fast `O(n²)` solution, accepted in 99% of LeetCode tests. | For huge `n`, `O(n²)` becomes heavy (not the case here). | None, but if constraints were tighter (`n ≤ 10⁵`), this approach fails. |
| **Readability** | Self‑explanatory with comments. | Some might misinterpret `right - left` logic; add comments. | None—code is concise. |
| **Edge‑Case Handling** | Empty or <3 elements → returns 0. | None. | None. |

---

## Conclusion & Next Steps <a name="conclusion--next-steps"></a>

*3Sum Smaller* is a classic interview problem that tests your ability to combine sorting with a two‑pointer sweep. The `O(n²)` solution is straightforward yet powerful and will score you points in any coding interview.

**What to practice next?**

1. **Related LeetCode Problems**  
   - 16. 3Sum  
   - 5. Longest Palindromic Substring (two‑pointer approach)  
   - 15. 3Sum Closest

2. **Algorithmic Patterns**  
   - Two‑pointer on sorted arrays  
   - Sliding window  
   - Binary search variants

3. **Coding Interviews**  
   - Mock interviews on platforms like Pramp, InterviewBit, or LeetCode Discuss.  
   - Review the “Good, Bad, Ugly” framework for explaining your solutions.

**SEO Bonus** – Use this blog post as a portfolio piece. It demonstrates not only your coding skill but also your ability to explain complex ideas clearly—a critical trait for software engineers.

Good luck landing that job! 🚀

--- 

**References**

- LeetCode 259: https://leetcode.com/problems/3sum-smaller/  
- Two‑Pointer Technique: https://leetcode.com/discuss/102391/three-sum-smaller-o-n2-solution-in-java  
- Sorting Basics: https://en.wikipedia.org/wiki/Sorting_algorithm  

--- 

*Feel free to drop a comment below if you’d like to discuss further optimizations or interview prep strategies!*