---
title: LeetCode 1968. Array With Elements Not Equal to Average of Neighbors - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Problem Overview – LeetCode 1968  
**Title:** *Array With Elements Not Equal to Average of Neighbors*  
**Difficulty:** Medium  
**Key Idea:** Sort the array and swap every adjacent pair (a “wave” array). The resulting ordering guarantees that no element is the average of its two neighbors.

---

## ✅ Why the Wave Array Works

After sorting `nums` in ascending order:

```
a0 ≤ a1 ≤ a2 ≤ … ≤ an-1
```

When we swap each pair `(ai, ai+1)` for `i = 0, 2, 4 …`, we get:

```
a1, a0, a3, a2, a5, a4, …
```

- **At an even index `i`** (original odd position) the value is the *larger* of the two numbers that were adjacent before sorting.  
  Its neighbors are both **smaller** because one came from a lower position and the other from a higher position in the sorted array.  
  Therefore the middle value can never be the arithmetic mean of the two smaller neighbors.

- **At an odd index `i`** (original even position) the value is the *smaller* of the two numbers that were adjacent before sorting.  
  Its neighbors are both **larger** (they come from the preceding and following sorted values).  
  Again the middle value can’t be the average of two larger numbers.

Since the array elements are distinct, the two neighbors are never equal to the middle element, and the average will always differ from the middle value.  

---

## 🛠️ 3 Complete Solutions

| Language | Code |
|----------|------|
| **Java** | **[Java.java]** |
| **Python** | **[Python.py]** |
| **C++** | **[C++].cpp** |

> *All solutions run in O(n log n)* due to the sort; the subsequent linear pass is O(n).

### 1. Java – Greedy “Wave” Implementation

```java
// Java solution – LeetCode 1968
import java.util.Arrays;

public class Solution {
    public int[] rearrangeArray(int[] nums) {
        // 1️⃣ Sort the array
        Arrays.sort(nums);

        // 2️⃣ Swap every adjacent pair to create a wave pattern
        for (int i = 0; i + 1 < nums.length; i += 2) {
            int tmp = nums[i];
            nums[i] = nums[i + 1];
            nums[i + 1] = tmp;
        }

        return nums;
    }
}
```

**Why it’s good:**  
- Simple, readable, no extra memory beyond the input array.  
- Works for all valid `nums` (distinct integers, length ≥ 3).

**Potential pitfall (the ugly part):**  
If the input array were **not** guaranteed distinct, swapping could still work but the “average” check would need to handle equal neighbors. For LeetCode’s constraints this isn’t an issue.

---

### 2. Python – Elegant One‑Liner Wave

```python
# Python solution – LeetCode 1968
from typing import List

class Solution:
    def rearrangeArray(self, nums: List[int]) -> List[int]:
        nums.sort()                     # O(n log n)
        return [nums[i ^ 1] for i in range(len(nums))]
```

**Explanation:**  
- `i ^ 1` flips the last bit: `0→1`, `1→0`, `2→3`, `3→2`, …  
- The list‑comprehension builds the wave array in a single pass.

**Pros/Cons:**  
- **Good:** Extremely concise, no explicit swap loop.  
- **Bad:** Might be harder for beginners to read.  
- **Ugly:** Uses XOR trick – avoid if code clarity is paramount.

---

### 3. C++ – Standard Library & In‑Place Swap

```cpp
// C++ solution – LeetCode 1968
#include <algorithm>
#include <vector>

class Solution {
public:
    std::vector<int> rearrangeArray(std::vector<int>& nums) {
        std::sort(nums.begin(), nums.end());               // O(n log n)

        for (size_t i = 0; i + 1 < nums.size(); i += 2) {   // O(n)
            std::swap(nums[i], nums[i + 1]);               // wave
        }
        return nums;
    }
};
```

**Why it’s clean:**  
- Uses the standard `std::swap`, no manual temp variable.  
- Works for any `std::vector<int>` satisfying LeetCode’s constraints.

---

## 📚 Step‑by‑Step Walk‑through (With Diagram)

```
Original unsorted:   [6, 2, 0, 9, 7]
After sort:          [0, 2, 6, 7, 9]
Swap pairs (0 & 1):  [2, 0, 7, 6, 9]
Swap pairs (2 & 3):  [2, 0, 9, 6, 7]
Result:              [2, 0, 9, 6, 7]
```

- **Even indices (0,2,4):** values `2, 9, 7` are **larger** than their two neighbors.
- **Odd indices (1,3):** values `0, 6` are **smaller** than their two neighbors.

Check for i=1:  
`(2 + 9)/2 = 5.5 ≠ 0`  
For i=2:  
`(0 + 6)/2 = 3 ≠ 9`  
For i=3:  
`(9 + 7)/2 = 8 ≠ 6`

All checks pass, confirming the property.

---

## 🚀 Performance Analysis

| Step | Complexity | Reason |
|------|------------|--------|
| Sorting | **O(n log n)** | Required to order the numbers before wave transformation. |
| Swapping | **O(n)** | One linear pass over the array. |
| Total | **O(n log n)** | Dominated by the sort. |
| Extra space | **O(1)** (in‑place) | No auxiliary array beyond the input. |

---

## 🔍 Edge Cases & Validation

| Scenario | Outcome |
|----------|---------|
| Length = 3 | Works; wave guarantees property. |
| Already a wave | The algorithm still sorts and swaps, producing a valid wave. |
| All numbers distinct (LeetCode guarantee) | Works flawlessly. |
| Numbers at extreme limits (0 and 10⁵) | Sorting handles them without overflow; average calculation uses integer division, but we only compare equality, not compute the actual average. |

---

## 📖 Blog Article Draft – “The Good, the Bad, and the Ugly of LeetCode 1968”

> **Title:** *LeetCode 1968 – The Good, the Bad, and the Ugly of Re‑arranging an Array*  
> **Meta‑Description:** Discover the greedy wave solution, why it works, and how to implement it in Java, Python, and C++. Perfect for your next coding interview prep.  
> **Target Keywords:** LeetCode 1968, Array with Elements Not Equal to Average of Neighbors, Java solution, Python solution, C++ solution, greedy algorithm, wave array, interview question.

---

### 1. Introduction

When preparing for a software‑engineering interview, you’ll often encounter “array re‑arrangement” problems. LeetCode’s **1968** is a prime example: rearrange a distinct integer array so that **no element is the arithmetic mean of its two neighbors**. The problem looks tricky at first glance, but a greedy trick turns it into a one‑liner.

---

### 2. The Problem in Plain English

You’re given a distinct integer array `nums` (size ≥ 3). Rearrange it such that for every internal index `i` (1 ≤ i < n‑1):

```
nums[i] ≠ (nums[i-1] + nums[i+1]) / 2
```

You can return **any** valid rearrangement.

---

### 3. Naïve Approaches (Why They’re Bad)

| Approach | Complexity | Why It’s Not Ideal |
|----------|------------|--------------------|
| Brute force permutations | O(n!) | Feasible only for tiny arrays. |
| Random shuffling until success | Expected O(k n!) | No guarantee of success; bad for large n. |
| Greedy “zig‑zag” without sorting | O(n) | Fails on certain sorted inputs (e.g., `[1,2,3]`). |

These methods either explode combinatorially or risk infinite loops.

---

### 4. The Greedy Wave Solution (The Good)

1. **Sort** the array in ascending order.  
2. **Swap** every pair of adjacent elements (`0↔1`, `2↔3`, …).

Why does this work?

- After sorting, the neighbors of any element are guaranteed to be on opposite sides of it in value.
- Swapping adjacent pairs creates a *wave* pattern: small, large, small, large, …
- In a wave, every *even* index holds a *larger* value than its neighbors, and every *odd* index holds a *smaller* value.
- Consequently, the middle element can never be the exact average of its two neighbors (since the two neighbors differ in magnitude on the same side).

**Proof Sketch:**  
Let `a ≤ b ≤ c`. After sorting, the triple is `[a, b, c]`. Swapping gives `[b, a, c]`.  
- For the middle index (`a`), the neighbors `b` and `c` are both larger → average > `a`.  
- For index `b`, neighbors `c` and `a` are on opposite sides → average ≠ `b` (distinct integers).

This reasoning extends across the entire array.

---

### 5. Implementation Snippets

#### Java

```java
public int[] rearrangeArray(int[] nums) {
    Arrays.sort(nums);
    for (int i = 0; i + 1 < nums.length; i += 2) {
        int tmp = nums[i];
        nums[i] = nums[i + 1];
        nums[i + 1] = tmp;
    }
    return nums;
}
```

#### Python

```python
def rearrangeArray(self, nums: List[int]) -> List[int]:
    nums.sort()
    return [nums[i ^ 1] for i in range(len(nums))]
```

#### C++

```cpp
vector<int> rearrangeArray(vector<int>& nums) {
    sort(nums.begin(), nums.end());
    for (size_t i = 0; i + 1 < nums.size(); i += 2)
        swap(nums[i], nums[i + 1]);
    return nums;
}
```

---

### 6. The Ugly – Edge Cases & Pitfalls

- **Non‑distinct numbers**: The problem guarantees distinctness, but if you drop that assumption, the algorithm still works. However, you must handle equality carefully if you compute the actual average as a float.
- **Very small arrays** (`n = 3`): The wave still holds, but double‑check the boundaries in your test harness to avoid off‑by‑one errors.
- **Integer Division**: In languages like Java, Python, and C++, integer division truncates. Since we only compare equality, this doesn’t matter. If you ever need the exact average, cast to `double`.

---

### 7. Time & Space Complexity

| Operation | Complexity |
|-----------|------------|
| Sorting | **O(n log n)** |
| Swapping pairs | **O(n)** |
| Total | **O(n log n)** |
| Extra space | **O(1)** (in‑place swap) |

---

### 8. Takeaway & Interview Tips

- Always read constraints first: sorting is cheap and guarantees structure.  
- “Wave” or “zig‑zag” patterns are powerful for problems involving local comparisons.  
- When presenting the solution, walk through the reasoning on the whiteboard—clarify the swap pattern and why averages can’t match.  
- Test with both random data and hand‑crafted edge cases to convince the interviewer of correctness.

---

### 8. Conclusion

LeetCode **1968** is a showcase of how a simple greedy trick can tame a seemingly complex constraint. The wave solution is clean, fast, and reliable across all inputs the platform guarantees. Mastering this pattern will give you confidence for a wide range of array manipulation interview questions.

---

### 9. Call‑to‑Action

> **Ready to ace array re‑arrangement questions?**  
> Practice LeetCode 1968 in Java, Python, or C++ and join our weekly interview prep series. **Sign up for free** and get a curated list of similar problems!  

---

### 10. Further Reading

- [LeetCode 150 – Evaluate Reverse Polish Notation]  
- [Interview Cake – Sorting & Swapping Tricks]  
- [Cracking the Coding Interview – Array & String Problems]

---

**End of Article**

---

## 📌 Final Takeaway

LeetCode 1968’s greedy wave strategy is both **optimal** and **elegant**. With clear implementation in Java, Python, and C++, you’ll not only solve the problem but also showcase the power of a simple sorting‑and‑swap trick in your next coding interview. Good luck, and happy coding!