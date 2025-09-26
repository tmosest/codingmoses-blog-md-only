---
title: LeetCode 1846. Maximum Element After Decreasing and Rearranging - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Mastering LeetCode 1846  
**Maximum Element After Decreasing and Rearranging** – *The Good, the Bad, and the Ugly*  

---

### TL;DR  
* **Problem** – You can reorder an array and decrease any element to any smaller positive integer.  
* **Goal** – After all operations, the first element must be `1` and consecutive elements must differ by at most `1`. Return the **maximum possible value** in the array.  
* **Answer** – Sort, then walk once and increment a running maximum whenever you see a number larger than it.  
* **Complexity** – `O(n log n)` time, `O(1)` extra space (ignoring sorting overhead).  

---

## 1️⃣ Problem Restatement

```text
Input:  arr = [2,2,1,2,1]
Output: 2

Input:  arr = [100,1,1000]
Output: 3
```

You can **any number of times**:
1. **Decrease** a value to a smaller positive integer.
2. **Rearrange** the array in any order.

After all changes:
- `arr[0] == 1`
- `abs(arr[i] - arr[i-1]) ≤ 1` for all `i > 0`

Return the largest element that can exist in such a valid array.

> **Constraints**  
> `1 ≤ arr.length ≤ 10⁵`  
> `1 ≤ arr[i] ≤ 10⁹`

---

## 2️⃣ Intuition & “Good” – Why sorting + greedy works

| What we can do | What we cannot do |
|----------------|--------------------|
| Decrease any element | Increase any element |
| Reorder freely | We must keep the **first element** as `1` |

Because we cannot increase numbers, the “best” strategy is to **use the smallest values to seed the sequence**.  
Sort the array ascending: the smallest numbers come first, so we can safely assign them to the positions we need.  
Once the array is sorted, we only need to decide **how many consecutive integers** we can build starting from `1`.  

Let `maxVal` be the largest integer we have already “secured”.  
When we look at the next element `x` in the sorted array:
- If `x` > `maxVal`, we can *decrease* `x` to `maxVal + 1`.  
  → increase `maxVal` by `1`.  
- Otherwise `x` is not large enough to extend the chain; skip it.

This greedy rule guarantees the longest possible chain because:
- The sorted order ensures we always consider the **smallest possible value** that could extend the chain next.  
- Decreasing a larger number never hurts any earlier part of the chain – we are simply “saving” a bigger number for later.

---

## 3️⃣ “Bad” – Potential pitfalls you might encounter

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| **Starting `maxVal` at `0`** | The first element must be `1`. If you start at `0`, you will incorrectly count an extra `1`. | Start at `1`. |
| **Skipping `i = 0`** | The first element after sorting might not be `1`. However, we *force* `1` as the first element, so we ignore the actual value at index `0`. | Use `maxVal = 1` *before* the loop. |
| **Using `for (int x : arr)`** | You’ll compare `x` with `maxVal` before you “secure” the current chain; this may over‑count. | Iterate **from index 1** onward, because index 0 is always `1`. |
| **O(n²) naïve simulation** | Decreasing all elements individually is impossible for `n = 10⁵`. | One linear pass after sorting. |

---

## 4️⃣ “Ugly” – Where the problem gets tricky

1. **All numbers equal** – e.g., `[1, 2, 2, 2, 2]`.  
   The greedy algorithm automatically skips extra `2`s because they can’t be reduced to a new integer.

2. **Very large numbers** – e.g., `[10⁹, 10⁹, 10⁹]`.  
   After sorting, we still only add at most one more integer per element because we’re forced to decrease.

3. **Small arrays** – e.g., `[1]`.  
   The loop never runs, but we must still return `1`.

Handling these corner cases is trivial with the greedy algorithm – just make sure the loop runs only when `arr.length > 1`.

---

## 5️⃣ Solution Code

Below are clean, idiomatic solutions for **Python 3**, **Java 17**, and **C++17**.  
All three share the same O(n log n) time and O(1) space (aside from sorting overhead).

### 5.1 Python 3

```python
from typing import List

class Solution:
    def maximumElementAfterDecrementingAndRearranging(self, arr: List[int]) -> int:
        """
        :type arr: List[int]
        :rtype: int
        """
        arr.sort()                     # O(n log n)
        max_val = 1                    # we already secured 1

        # walk through the sorted array starting at index 1
        for x in arr[1:]:
            if x > max_val:            # can extend the chain
                max_val += 1

        return max_val
```

### 5.2 Java 17

```java
import java.util.Arrays;

class Solution {
    public int maximumElementAfterDecrementingAndRearranging(int[] arr) {
        Arrays.sort(arr);             // O(n log n)
        int maxVal = 1;               // we already have 1

        for (int i = 1; i < arr.length; i++) {
            if (arr[i] > maxVal) {
                maxVal++;             // extend the chain
            }
        }
        return maxVal;
    }
}
```

### 5.3 C++17

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int maximumElementAfterDecrementingAndRearranging(std::vector<int>& arr) {
        std::sort(arr.begin(), arr.end());   // O(n log n)
        int maxVal = 1;                      // we already have 1

        for (size_t i = 1; i < arr.size(); ++i) {
            if (arr[i] > maxVal) {
                ++maxVal;                    // extend the chain
            }
        }
        return maxVal;
    }
};
```

All three solutions perform a **single linear pass** after sorting, and they’re O(1) additional space (ignoring the recursion stack used by the built‑in sort routine).

---

## 6️⃣ Complexity Analysis

| Operation | Complexity |
|-----------|------------|
| Sort | `O(n log n)` time, `O(1)` (or `O(n)` if you count the heap used by quick‑sort) |
| Single linear scan | `O(n)` time, `O(1)` space |
| **Total** | **`O(n log n)` time, `O(1)` auxiliary space** |

> **Why not `O(n)`?**  
> The only sub‑`O(n)` operations available are linear scans.  
> Sorting is unavoidable because you need to know *which* values are large enough to extend the chain; without sorting you might skip a number that could have been decreased to a useful value.

---

## 7️⃣ Edge‑Case Checklist

| Case | Expected Output | Why |
|------|-----------------|-----|
| `[1]` | `1` | Only the first element exists. |
| `[5, 5, 5]` | `2` | Sorted → `[5,5,5]`. The first `5` can become `2`. |
| `[10⁹, 10⁹, …, 10⁹]` | `n` | Every huge number can be reduced to `2, 3, …, n`. |
| `[1, 100, 100, 100]` | `4` | `1` → `2` → `3` → `4`. |

Always test with a mix of small, equal, and huge numbers.

---

## 8️⃣ Sample Driver Code & Unit Tests

Below is a quick way to verify the implementation in any language.

```python
def run_tests():
    tests = [
        ([2,2,1,2,1], 2),
        ([100,1,1000], 3),
        ([1,2,2,2,2], 2),
        ([10**9, 10**9, 10**9], 3),
        ([1], 1),
    ]

    for arr, expected in tests:
        sol = Solution()
        res = sol.maximumElementAfterDecrementingAndRearranging(arr.copy())
        assert res == expected, f"FAIL: {arr} → {res} (expected {expected})"
    print("All tests passed!")

if __name__ == "__main__":
    run_tests()
```

Add the same test harness in Java and C++ (use JUnit / GoogleTest if you’re preparing for interviews).

---

## 9️⃣ Interview‑Ready Tips

| Tip | Why it matters |
|-----|----------------|
| **Explain the greedy invariant** – “`maxVal` is the largest integer we’ve already guaranteed” | Shows you understand the “state” of the algorithm. |
| **Mention the impossibility of increasing numbers** | Highlights the asymmetry of the problem and why sorting is natural. |
| **Show the O(n log n) proof** – sorting dominates | Makes the candidate confident about time complexity. |
| **Discuss corner cases** – single element, all equal, huge values | Demonstrates thoroughness. |
| **Talk about space trade‑offs** – `Arrays.sort` in Java uses `O(log n)` stack space; `std::sort` uses `O(log n)` recursion depth | Shows awareness of low‑level details. |

---

## 10️⃣ SEO & Job‑Search Friendly Keywords

- **LeetCode 1846**  
- **Maximum Element After Decreasing and Rearranging**  
- **LeetCode interview questions**  
- **Java algorithm**  
- **Python algorithm**  
- **C++ algorithm**  
- **Sorting + Greedy**  
- **O(n log n) time complexity**  
- **Software engineer interview prep**  
- **Data structures and algorithms**  

*Add these tags to your blog post, LinkedIn article, or GitHub README to attract recruiters searching for LeetCode mastery.*

---

## 11️⃣ Conclusion

The “good” part of LeetCode 1846 is its elegance: **one sort + one pass** gives the optimal answer.  
The “bad” part is the temptation to over‑think – some people try to simulate the operations, leading to quadratic blow‑ups.  
The “ugly” part is handling the subtle corner case when the sorted element is *not* large enough to extend the chain – you simply skip it.

With this clean solution in **Python, Java, and C++**, you can confidently tackle the problem in any interview setting.  

> **Pro‑tip** – When you get stuck on a LeetCode problem that allows “decrease” or “rearrange”, ask yourself: *Can I sort the array first?* Often the answer is yes, and a greedy pass will follow.

Good luck on your interview, and feel free to **share this post** on LinkedIn or Twitter to show recruiters that you’re already mastering the most efficient solutions! 🚀  

--- 

**#LeetCode #InterviewPreparation #Java #Python #C++ #Algorithm #Sorting #Greedy #SoftwareEngineer #JobInterview**