---
title: LeetCode 719. Find K-th Smallest Pair Distance - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Code – 719. Find K‑th Smallest Pair Distance  

Below are clean, production‑ready implementations in **Java**, **Python** and **C++** that solve LeetCode 719 in **O(n log M)** time (where *M* is the maximum possible distance) and **O(1)** extra space (apart from the input array).

> **Why this approach?**  
> *Sort* the array once (`O(n log n)`).  
> Then perform a binary search on the distance value (`0 … max‑min`).  
> For each candidate distance *d* we count how many pairs have distance `≤ d` with a two‑pointer sliding window – this is linear in *n*.  
> Because the count is monotone with *d*, binary search gives us the exact k‑th smallest distance.

---

### Java

```java
import java.util.Arrays;

public class Solution {
    public int smallestDistancePair(int[] nums, int k) {
        Arrays.sort(nums);                     // O(n log n)
        int left  = 0;
        int right = nums[nums.length - 1] - nums[0];   // maximal possible distance

        while (left < right) {
            int mid = left + (right - left) / 2;
            if (countPairs(nums, mid) < k) {   // not enough pairs ≤ mid
                left = mid + 1;
            } else {
                right = mid;                   // enough pairs, try smaller distance
            }
        }
        return left;   // left == right
    }

    // Count pairs with distance <= maxDist
    private int countPairs(int[] nums, int maxDist) {
        int count = 0;
        int left  = 0;
        for (int right = 0; right < nums.length; right++) {
            while (nums[right] - nums[left] > maxDist) {
                left++;
            }
            count += right - left;   // all indices in [left, right-1] form valid pairs with right
        }
        return count;
    }
}
```

---

### Python

```python
class Solution:
    def smallestDistancePair(self, nums: list[int], k: int) -> int:
        nums.sort()
        left, right = 0, nums[-1] - nums[0]

        while left < right:
            mid = (left + right) // 2
            if self._count_pairs(nums, mid) < k:
                left = mid + 1
            else:
                right = mid
        return left

    def _count_pairs(self, nums: list[int], max_dist: int) -> int:
        count = 0
        left = 0
        for right in range(len(nums)):
            while nums[right] - nums[left] > max_dist:
                left += 1
            count += right - left
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
        sort(nums.begin(), nums.end());            // O(n log n)
        int left  = 0;
        int right = nums.back() - nums.front();    // maximal possible distance

        while (left < right) {
            int mid = left + (right - left) / 2;
            if (countPairs(nums, mid) < k) {        // not enough pairs <= mid
                left = mid + 1;
            } else {
                right = mid;                       // enough pairs, shrink distance
            }
        }
        return left;   // left == right
    }

private:
    // Count pairs with distance <= maxDist
    int countPairs(const vector<int>& nums, int maxDist) {
        int count = 0, left = 0;
        for (int right = 0; right < static_cast<int>(nums.size()); ++right) {
            while (nums[right] - nums[left] > maxDist) {
                ++left;
            }
            count += right - left;   // all indices in [left, right-1] form valid pairs
        }
        return count;
    }
};
```

---

## 2.  The Blog – “Cracking LeetCode 719: A Binary‑Search Masterclass”

> **Keywords:** LeetCode 719, k‑th smallest pair distance, binary search, two‑pointer sliding window, interview algorithm, software engineering interview, coding interview, algorithm interview, senior dev interview, job interview coding.

---

### Introduction

LeetCode 719 – *Find K‑th Smallest Pair Distance* – is a classic interview puzzle that sits at the intersection of sorting, binary search and sliding‑window techniques. Mastering it not only earns you a **0.98 %** upvote on LeetCode but also demonstrates your ability to turn a combinatorial counting problem into an efficient algorithm – a skill every senior software engineer should own.

---

### Problem Recap

Given an integer array `nums` (size ≤ 10 000) and an integer `k` (1 ≤ k ≤ n(n‑1)/2), find the k‑th smallest absolute difference between any two distinct indices *i* < *j*.

> *Example*  
> `nums = [1, 3, 1]`, `k = 1` → answer is `0` (the pair (1, 1)).

---

### 1️⃣  Good – The “Gold Standard” Algorithm

1. **Sort** the array (`O(n log n)`).  
2. **Binary search** the answer:
   - *Low* = 0, *High* = `max(nums) - min(nums)`.  
   - For each midpoint `mid`, **count** how many pairs have distance `≤ mid`.  
   - If count < k → we need a larger distance → `low = mid+1`.  
   - Else → enough pairs → `high = mid`.  
3. When `low == high`, that value is the k‑th smallest distance.

**Why it works**  
The number of pairs with distance ≤ d is monotone in d: as you increase d, you never lose pairs. Binary search exploits this monotonicity to hone in on the exact threshold where the count just reaches *k*.

---

### 2️⃣  Bad – Why Simpler Brute‑Force Fails

A naïve O(n²) double loop works for small inputs, but with *n* up to 10 000, it can do ~50 million iterations – still acceptable in theory but **O(n²)** is a *deadly* performance pitfall for production‑grade code or interview settings. It also doesn’t scale if you change constraints.

---

### 3️⃣  Ugly – Common Pitfalls and Missteps

| Mistake | Symptom | Fix |
|---------|---------|-----|
| **Using `int mid = (left + right) / 2;`** | Integer overflow on large ranges | `int mid = left + (right - left) / 2;` |
| **Counting pairs incorrectly** | Wrong result for duplicate numbers | Use two‑pointer *while* loop to keep `nums[right] - nums[left] ≤ maxDist`. |
| **Missing array sort** | Wrong answer for unsorted input | `Arrays.sort(nums);` (Java/Python) / `sort(nums.begin(), nums.end());` (C++) |
| **Using `right = mid;` without `mid`** | Infinite loop when `left < right` but `mid == left` | Keep `right = mid;` only when count ≥ k, otherwise `left = mid + 1`. |
| **Ignoring 64‑bit count** | Overflow when `n` ≈ 10 000 (pairs ≈ 50 million) | Use `long` for `count` if you suspect overflow; however `int` suffices for the given constraints. |

---

### 4️⃣  Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Sort | **O(n log n)** | **O(1)** |
| Binary search iterations | **O(log M)** (`M` ≤ 10⁶) | — |
| Counting pairs per iteration | **O(n)** | **O(1)** |
| **Total** | **O(n log M)** | **O(1)** |

Because `n` is at most 10 000 and `M` is bounded by 10⁶, the algorithm runs comfortably under 100 ms on modern judges and in interview coding platforms.

---

### 5️⃣  Quick Test Cases

```python
assert Solution().smallestDistancePair([1,3,1], 1) == 0
assert Solution().smallestDistancePair([1,6,1], 3) == 5
assert Solution().smallestDistancePair([1,2,3,4], 2) == 1
```

Feel free to paste the Java/Python/C++ snippets into your IDE and run against LeetCode's test harness.

---

### 6️⃣  Take‑Away Checklist for Interviews

- **State the idea**: “Sort + binary search over distances + two‑pointer counting”.
- **Explain monotonicity**: Count of pairs ≤ d never decreases as d increases.
- **Mention edge cases**: duplicates, negative numbers, large `k`.
- **Time/space**: `O(n log M)` time, `O(1)` space.
- **Pitfalls**: overflow in mid‑point calculation, wrong pointer increments, missing `sort`.

---

## 2.  The Blog – “Cracking LeetCode 719: A Binary‑Search Masterclass”

> **Target audience**: CS graduates, senior‑level candidates, software engineers preparing for *coding interview* or *algorithm interview* rounds.

---

### Title
**“LeetCode 719: Find K‑th Smallest Pair Distance – Binary Search Mastery for Your Next Interview”**

### Meta Description
“Learn how to solve LeetCode 719 in O(n log M) time. Detailed Java, Python, and C++ solutions, step‑by‑step guide, pitfalls, and interview‑ready explanations. Master binary search + two‑pointer techniques to ace your coding interview.”

### Keywords
- LeetCode 719
- k‑th smallest pair distance
- binary search algorithm
- two‑pointer sliding window
- interview coding problem
- software engineering interview
- algorithm interview
- job interview coding
- senior dev interview
- coding interview prep

---

### Introduction  

Interviewers love problems that force you to blend classic data‑structures tricks (sorting) with algorithmic insights (binary search). LeetCode 719 is a textbook example: “find the k‑th smallest absolute difference between any two numbers in an array.” It sounds simple, but naive solutions drown in O(n²) time. Mastering the binary‑search‑on‑answer technique not only earns you a spot on your interview board but also showcases the depth of your problem‑solving toolbox.

---

### Problem Statement (Formal)  

> **Given** an integer array `nums` (size `n` ≤ 10 000) and an integer `k` (`1 ≤ k ≤ n(n‑1)/2`).  
> **Return** the k‑th smallest value of `|nums[i] – nums[j]|` for all `0 ≤ i < j < n`.

---

### 1️⃣  Intuition & Core Idea  

1. **Sort** the array → distances become monotone along the index axis.  
2. The smallest distance is `0`, the largest is `max(nums) – min(nums)`.  
3. **Binary search** the distance value `d`.  
4. For each `d`, count how many pairs have distance `≤ d`.  
5. If count `< k` → `d` is too small → increase low.  
   If count `≥ k` → `d` is large enough → tighten high.  
6. When low meets high, that value is the answer.

Because the count of valid pairs never decreases when you raise `d`, the binary search is guaranteed to converge to the exact k‑th smallest distance.

---

### 2️⃣  Counting Pairs in Linear Time  

The counting step is the heart of the algorithm. After sorting, use a two‑pointer sliding window:

```
left = 0
for right in [0 .. n-1]:
    while nums[right] - nums[left] > d:
        left += 1
    # Now all indices between left and right-1 form valid pairs with 'right'
    count += right - left
```

*Why does this work?*  
- For a fixed `right`, `left` is the first index such that the window `[left, right]` satisfies `nums[right] - nums[left] ≤ d`.  
- All indices between `left` and `right-1` create pairs with distance `≤ d`.  
- Moving `right` one step expands the window; `left` only moves forward, never backward.  
- Therefore the loop runs `O(n)`.

---

### 3️⃣  Full Algorithm (Pseudocode)

```
sort(nums)
low  = 0
high = max(nums) - min(nums)

while low < high:
    mid = low + (high - low) // 2
    if countPairs(mid) < k:
        low = mid + 1
    else:
        high = mid

return low
```

**countPairs(d)** returns the number of pairs `(i, j)` such that `nums[j] - nums[i] ≤ d`.

---

### 3️⃣  Edge Cases & Special Scenarios  

- **Duplicate values**: distance 0 will be counted multiple times. The algorithm handles this automatically because the sliding window will include identical values.  
- **Negative numbers**: absolute difference is handled by sorting; negatives do not affect the logic.  
- **Large `k`**: When `k` equals the total number of pairs, the answer is the largest distance – binary search still works because the count will match `k` only at the upper bound.  

---

### 4️⃣  Why Brute Force Fails (The O(n²) “Cave”)  

Trying every pair with a double loop is intuitive but brutal for `n = 10 000`. 50 million comparisons may pass under 1 second on LeetCode, but the solution is fragile:

- **Memory**: If you store all distances, you’ll blow the judge’s memory limits.  
- **Time**: Interview environments typically impose a 1‑minute window. O(n²) will be a red flag.  
- **Learning signal**: Interviewers often ask why you didn’t go for the more optimal O(n log M) route.

---

### 5️⃣  Pitfalls & “Ugly” Quirks  

- **Overflow**: Use `low + (high - low) / 2` instead of `(low + high) / 2`.  
- **Pointer mis‑advance**: Forgetting to move `left` when the window exceeds `d`.  
- **Off‑by‑one**: Be careful that `right` is always strictly larger than `left`.  
- **Large count**: If you modify constraints, switch `int` to `long` for safety.  

---

### 6️⃣  Full Code (Java / Python / C++)  

*Include the code snippets from the “Solution” section.*  

These snippets are production‑ready: they sort the array, use a safe mid‑point calculation, and count pairs efficiently.

---

### 7️⃣  What Interviewers Really Care About  

1. **Concept clarity**: Can you articulate “binary search on answer” before diving into code?  
2. **Complexity justification**: Show you understand why O(n log M) is optimal.  
3. **Edge handling**: Demonstrate awareness of duplicates, large `k`, negative numbers.  
4. **Testing**: Provide a few unit tests; be ready to explain why they cover important scenarios.  

---

### 8️⃣  Final Thoughts & Next Steps  

LeetCode 719 isn’t just another “medium” problem; it’s a lesson in turning a counting question into a logarithmic search. Once you nail this, you can apply the same pattern to other interview staples:

- “k‑th largest element in sorted order”  
- “smallest range covering elements from multiple lists”  
- “minimize maximum distance”  

Keep practicing: run the Java/Python/C++ versions locally, tweak the constraints, and watch how the algorithm scales. Add it to your *interview playbook* and show your future employers you’re not just a coder, but a **strategic problem solver**.

---

### Call to Action  

> *“If you found this deep dive helpful, drop a comment, share it on LinkedIn, and let’s keep the conversation going! Want more interview‑ready solutions? Subscribe to our weekly algorithm challenge newsletter.”*

---

### Closing  

Mastering LeetCode 719 with a binary‑search approach is a powerful signal to interviewers: you can see the underlying structure of a problem, abstract it into a search space, and engineer a solution that is both efficient and elegant. As you prep for your next **coding interview**, remember: the *right* algorithm is often a combination of classic tricks (sorting) and clever insights (binary search on answer). Get comfortable with both, and the next interview will feel like a walk in the park.

--- 

*Happy coding!*