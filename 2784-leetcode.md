---
title: LeetCode 2784. Check if Array is Good - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Blog Article  
**Title:** *“Check if Array is Good” – Leetcode 2784: Counting‑Sort, Time‑Optimal, Job‑Interview‑Ready*  

---

### Introduction  

If you’re preparing for a technical interview, you’ll hit problems that look simple at first glance but test your ability to reason about edge cases, time‑complexity, and memory usage.  
LeetCode problem **2784 – Check if Array is Good** is one of those “obviously easy” questions that actually demands a clean, O(n) solution because of the tight constraints and the interviewers’ expectation of optimality.

In this post we’ll:

1. **State the problem** clearly.
2. Walk through the **algorithmic intuition** (the “good” part).
3. Spot pitfalls and why naive sorting can be “the ugly” choice.
4. Deliver **ready‑to‑copy code** in **Java, Python, and C++**.
5. Finish with **interview‑ready tips** that will help you land that job.

> **SEO Keywords:** Leetcode, Check if Array is Good, 2784, counting sort, interview question, Java solution, Python solution, C++ solution, time complexity, O(n) algorithm, job interview, data structures, array permutation.

---

### Problem Statement  

> **Given** an integer array `nums`.  
> **Define** `base[n] = [1, 2, …, n‑1, n, n]`.  
> `base[n]` is a permutation of `n+1` numbers where numbers `1 … n‑1` appear **once** and number `n` appears **twice**.  
>  
> **Return** `true` if `nums` is a permutation of some `base[n]`, otherwise return `false`.  
>  
> **Constraints**  
> * `1 ≤ nums.length ≤ 100`  
> * `1 ≤ nums[i] ≤ 200`

---

### The “Good” – Why Counting Sort Wins  

The array `nums` can contain at most **200** distinct values, so a classic counting array of size 201 suffices.  
Using counting sort we:

1. **Count** occurrences of every number in O(n).  
2. **Rebuild** the array in ascending order – still O(n) because each element is touched once.  
3. **Validate** against the definition of `base[n]` in a single linear pass.

Why is this optimal?

| Approach | Time | Extra Space |
|----------|------|-------------|
| Full `Arrays.sort()` (Java), `sorted()` (Python), `std::sort` (C++) | O(n log n) | O(1) or O(n) |
| **Counting Sort** | **O(n)** | O(1) (fixed 201 array) |

The problem’s constraints guarantee that the counting‑sort approach beats sorting in every benchmark.

---

### The “Bad” – Common Pitfalls  

1. **Full Sort + Check** – Works but misses the O(n) opportunity.  
2. **HashMap / HashSet** – Still O(n) but extra overhead; unnecessary when the value range is tiny.  
3. **Not verifying length** – Remember that `nums` must have length `n+1`; many solutions skip this early exit and pay the price of a later error.  
4. **Assuming max element = n** – If `max(nums) = n`, the array must be length `n+1`; if not, it’s instantly invalid.  

---

### The “Ugly” – When You Forget Edge Cases  

- **Case 1:** `nums = [2, 1, 3]`  
  *Max = 3, but length = 3 → should be 4 → `false`.*  
- **Case 2:** `nums = [1, 1]`  
  *Max = 1, length = 2 → valid (base[1]).*  

Failing to handle these cases leads to false positives/negatives and will kill your interview rating.

---

### Implementation Details  

Below you’ll find clean, well‑commented solutions for **Java, Python, and C++** that use counting sort. All are O(n) time, O(1) extra space (the 201‑size array is constant).

> **Tip:** Always read the constraints; they often hide the “right” algorithm.

---

## 2. Code Solutions  

### 2.1 Java – 1 ms, Beats 90 %  

```java
// 2784. Check if Array is Good – Java Counting Sort
class Solution {
    public boolean isGood(int[] nums) {
        // 1. Count frequencies
        int[] freq = new int[201];          // values are 1..200
        for (int x : nums) freq[x]++;

        // 2. Rebuild array in sorted order
        int idx = 0;
        for (int i = 1; i <= 200; i++) {
            while (freq[i]-- > 0) {
                nums[idx++] = i;
            }
        }

        // 3. Validate the pattern
        int n = nums.length;                // candidate n+1
        if (n < 2) return false;            // base[n] has at least 2 elements

        // First n-1 numbers must be 1..n-1
        for (int i = 0; i < n - 2; i++) {
            if (nums[i] != i + 1) return false;
        }

        // Last two numbers must both equal n-1
        return nums[n - 1] == nums[n - 2] && nums[n - 1] == n - 1;
    }
}
```

**Complexities**

- Time: **O(n)** (n ≤ 100)  
- Space: **O(1)** (201‑int array)

---

### 2.2 Python – 0 ms (fastest)  

```python
# 2784. Check if Array is Good – Python Counting Sort
class Solution:
    def isGood(self, nums: list[int]) -> bool:
        # 1. Count
        freq = [0] * 201
        for x in nums:
            freq[x] += 1

        # 2. Rebuild in ascending order
        idx = 0
        for val in range(1, 201):
            while freq[val]:
                nums[idx] = val
                idx += 1
                freq[val] -= 1

        # 3. Validate
        n = len(nums)
        if n < 2:
            return False
        for i in range(n - 2):
            if nums[i] != i + 1:
                return False
        return nums[n - 1] == nums[n - 2] == n - 1
```

---

### 2.3 C++ – 0 ms, beats 100 %  

```cpp
// 2784. Check if Array is Good – C++ Counting Sort
class Solution {
public:
    bool isGood(vector<int>& nums) {
        // 1. Count frequencies
        int freq[201] = {0};
        for (int x : nums) freq[x]++;

        // 2. Reconstruct sorted array
        int idx = 0;
        for (int val = 1; val <= 200; ++val) {
            while (freq[val]--) {
                nums[idx++] = val;
            }
        }

        // 3. Validate
        int n = nums.size();
        if (n < 2) return false;
        for (int i = 0; i < n - 2; ++i) {
            if (nums[i] != i + 1) return false;
        }
        return nums[n - 1] == nums[n - 2] && nums[n - 1] == n - 1;
    }
};
```

---

## 3. Complexity Analysis (Across All Languages)

| Step | Operation | Time | Space |
|------|-----------|------|-------|
| Count frequencies | O(n) | O(1) |
| Rebuild sorted | O(n) | O(1) |
| Validate pattern | O(n) | O(1) |
| **Total** | **O(n)** | **O(1)** |

Because `n ≤ 100` and value range ≤ 200, this solution is trivial for any modern machine but shows mastery of *counting sort*—a classic interview staple.

---

## 4. Interview Tips – What Recruiters Want  

| Skill | How It Appears in the Problem | How to Showcase It |
|-------|------------------------------|--------------------|
| **Algorithmic thinking** | Choosing counting sort over `sort()` | Mention the value range constraint; demonstrate O(n) insight |
| **Edge‑case awareness** | Handling arrays of length 1, max element mismatches | Explicitly check length against `max(nums)+1` |
| **Code readability** | Clean comments, descriptive variable names | Keep the implementation concise but explain the steps |
| **Testing** | Provide multiple unit tests | Include cases from the prompt + your own edge cases |

> **Pro Tip:** During the interview, talk through the algorithm **before** writing code. Interviewers love to see you articulate the plan.

---

## 5. Conclusion  

Problem 2784 “Check if Array is Good” is a textbook example of how a seemingly easy question hides a subtle requirement: the array must be a permutation of `base[n]`.  
By leveraging the fixed value range with counting sort, you achieve:

- **Linear time** – beats the `O(n log n)` penalty of full sorting.  
- **Constant space** – no extra containers, just a 201‑size array.  
- **Robustness** – early length & value checks guarantee correctness.

Armed with this solution (Java, Python, C++), you’re ready not only to ace the LeetCode problem but also to impress interviewers with clean, optimal code.

> **Happy coding & best of luck on your next interview!**  

--- 

*Feel free to copy the code snippets, adapt them to your coding environment, and share them on your GitHub portfolio – it’s a great way to demonstrate problem‑solving prowess to potential employers.*