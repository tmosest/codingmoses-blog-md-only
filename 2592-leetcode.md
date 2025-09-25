---
title: LeetCode 2592. Maximize Greatness of an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2592. **Maximize Greatness of an Array** –  
### A Complete, Interview‑Ready Guide  
*(Java | Python | C++)*

---

### TL;DR  
- **Goal:** Rearrange `nums` to maximize the number of indices where the new value is *strictly larger* than the original value.  
- **Greedy / Two‑Pointer trick:** Sort `nums` ascending, then use two indices (`i` – “candidate” element, `j` – “current” position) to pick the smallest element that beats `nums[j]`.  
- **Time complexity:** `O(n log n)` (for the sort).  
- **Space complexity:** `O(1)` auxiliary (if we sort in‑place) or `O(n)` if we use a copy.  

---

## 1. Problem Recap

> **Definition of Greatness**  
> For a permutation `perm` of the original array `nums`, the *greatness* is the count of indices `i` where `perm[i] > nums[i]`.

> **Task**  
> Return the maximum possible greatness after any permutation of `nums`.

---

## 2. Intuition & Strategy

1. **Sort the array**  
   Sorting gives us the smallest available numbers at the front, which is perfect for a greedy approach: we want the smallest number that can still beat a particular element.

2. **Two pointers**  
   - `i` – scans through the sorted array (the “candidate” values).  
   - `j` – scans the original array (the “target” indices).  
   We try to pair the smallest candidate that is *greater* than the current target.  

3. **Why greedy works**  
   - If we have a candidate `c` that beats `nums[j]`, using it here cannot make the answer worse later because any larger candidate could also beat `nums[j]`.  
   - Using the smallest possible candidate leaves larger numbers available for later, maximizing future opportunities.

---

## 3. Algorithm (Pseudocode)

```
sort(nums)                     // ascending
i = 0                          // index of candidate
count = 0

for j in 0 .. nums.length-1:   // j is the target index
    while i < nums.length and nums[i] <= nums[j]:
        i += 1                  // skip all numbers that cannot beat nums[j]
    if i == nums.length:
        break                   // no more candidates left
    count += 1                  // nums[i] beats nums[j]
    i += 1                      // move to next candidate

return count
```

---

## 4. Complexity Analysis

| Operation | Cost |
|-----------|------|
| Sorting | `O(n log n)` |
| Two‑pointer scan | `O(n)` |
| Total time | **`O(n log n)`** |
| Extra space | **`O(1)`** (in‑place sort) or `O(n)` if a copy is needed |

The constraints (`n ≤ 10⁵`) are comfortably handled by this approach.

---

## 5. Code Implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.

---

### 5.1 Java

```java
import java.util.Arrays;

public class Solution {
    public int maximizeGreatness(int[] nums) {
        // Sort the array to enable the two‑pointer greedy strategy
        Arrays.sort(nums);
        int n = nums.length;
        int i = 0;   // candidate pointer
        int count = 0;

        // Traverse original indices
        for (int j = 0; j < n; j++) {
            // Advance i until we find a number that beats nums[j]
            while (i < n && nums[i] <= nums[j]) {
                i++;
            }
            if (i == n) break;      // no more candidates
            count++;                // nums[i] > nums[j]
            i++;                    // use this candidate
        }
        return count;
    }

    // Quick test harness
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.maximizeGreatness(new int[]{1,3,5,2,1,3,1})); // 4
        System.out.println(sol.maximizeGreatness(new int[]{1,2,3,4}));      // 3
    }
}
```

---

### 5.2 Python

```python
class Solution:
    def maximizeGreatness(self, nums: list[int]) -> int:
        nums.sort()                 # in‑place sort
        n = len(nums)
        i = 0                       # candidate index
        count = 0

        for j in range(n):
            while i < n and nums[i] <= nums[j]:
                i += 1
            if i == n:              # no more candidates
                break
            count += 1
            i += 1

        return count

# Quick test
if __name__ == "__main__":
    sol = Solution()
    print(sol.maximizeGreatness([1, 3, 5, 2, 1, 3, 1]))  # 4
    print(sol.maximizeGreatness([1, 2, 3, 4]))           # 3
```

---

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maximizeGreatness(vector<int>& nums) {
        sort(nums.begin(), nums.end());          // ascending
        int n = nums.size();
        int i = 0;                               // candidate pointer
        int count = 0;

        for (int j = 0; j < n; ++j) {
            while (i < n && nums[i] <= nums[j]) ++i;
            if (i == n) break;                   // no more candidates
            ++count;                             // nums[i] > nums[j]
            ++i;
        }
        return count;
    }
};

// Quick test
int main() {
    Solution sol;
    cout << sol.maximizeGreatness({1,3,5,2,1,3,1}) << endl; // 4
    cout << sol.maximizeGreatness({1,2,3,4}) << endl;       // 3
    return 0;
}
```

---

## 6. Blog Article – “The Good, The Bad, and The Ugly of LeetCode’s Maximize Greatness Problem”

### 6.1 Why This Problem is a Great Interview Show‑stopper

- **Conceptual depth:** It tests *greedy* reasoning, *two‑pointer* tricks, and an understanding that sorting can unlock hidden structure.
- **Language‑agnostic:** Your solution works in Java, Python, C++, Go, Rust – the same idea, just different syntax.
- **Scalable:** With `n = 10⁵`, a naïve `O(n²)` approach would time‑out, so interviewers appreciate an `O(n log n)` solution.

### 6.2 The Good

| Aspect | Why it’s good |
|--------|---------------|
| **Simplicity** | After sorting, the logic reduces to a single linear pass. |
| **Deterministic** | No randomness or heuristic. |
| **Deterministic time** | `O(n log n)` is acceptable even for large inputs. |
| **Broad applicability** | The greedy technique is reusable (e.g., “Permuting Two Arrays” problem). |

### 6.3 The Bad

| Issue | Mitigation |
|-------|------------|
| **Sorting cost** | For extremely large arrays (`n > 10⁶`), you might need a counting sort or bucket sort, but not needed here. |
| **In‑place mutation** | Some interviewers ask to preserve the original array; you can make a copy before sorting. |
| **Edge cases** | All equal elements → answer `0`. Empty array (not in constraints) → handle gracefully. |

### 6.4 The Ugly

- **Misunderstanding “greater than”**: Some candidates accidentally use “≥” instead of “>”, causing off‑by‑one errors.  
- **Pointer overrun**: Forgetting to check `i < n` inside the inner `while` can lead to an `ArrayIndexOutOfBoundsException` in Java or segmentation fault in C++.  
- **Sorting side‑effects**: If the interview requires the original order for later use, remember to copy the array first.

### 6.5 SEO‑Friendly Takeaway

If you’re searching for *“LeetCode 2592 solution”* or *“maximize greatness of an array interview”*, this guide is your one‑stop shop:

- **Title** – *LeetCode 2592 – Maximize Greatness of an Array – Java, Python, C++ Solutions*  
- **Meta description** – “Learn how to solve LeetCode 2592 with efficient Java, Python, and C++ code. Understand the greedy two‑pointer approach, edge cases, and interview tips.”  
- **Keywords** – LeetCode, Maximize Greatness, array permutation, two pointers, greedy algorithm, coding interview, algorithmic challenge, Java solution, Python solution, C++ solution, job interview prep.

---

## 7. Final Words – How to Nail the Interview

1. **Explain the greedy idea** – “Sort the array so we can always pick the smallest number that beats the current target.”  
2. **Mention time/space** – “We sort in `O(n log n)` and then run a linear scan.”  
3. **Cover edge cases** – “All elements equal → 0; array size 1 → 0.”  
4. **Show the code** – Provide the clean, commented solution in the language of your choice.  
5. **Highlight reusability** – “The same pattern solves ‘Permuting Two Arrays’ and many “beat‑the‑target” problems.”

With this approach, you’ll turn a seemingly complex LeetCode problem into a showcase of clean algorithm design and solid coding skills—exactly what recruiters want. Good luck!