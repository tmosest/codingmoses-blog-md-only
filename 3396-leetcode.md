---
title: LeetCode 3396. Minimum Number of Operations to Make Elements in Array Distinct - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3396. Minimum Number of Operations to Make Elements in Array Distinct  
### A deep dive – “The Good, The Bad, and The Ugly”  
> **SEO Tags**: LeetCode, Minimum Operations, Distinct Array, Java, Python, C++, Algorithm, Time Complexity, Space Complexity, Software Engineer Interview, Coding Interview

---

## 1. Problem Overview

LeetCode 3396 asks you to find the **minimum number of “3‑element deletions from the front”** required to make all elements in an array distinct.

* **Operation** – In one step you may remove the first three elements of the array.  
  If the array has fewer than three elements left, remove everything.  
* An empty array is considered to have distinct elements.

### Constraints

| Parameter | Value |
|-----------|-------|
| `nums.length` | 1 … 100 |
| `nums[i]` | 1 … 100 |

The constraints are tiny – a brute‑force simulation is possible, but an **O(n)** greedy scan from the back gives a crisp, elegant solution.

---

## 2. The Greedy Insight (Good)

Traverse the array **backwards** while keeping a hash‑set of seen values.  
As soon as you encounter a duplicate, you know that all indices **to the left** of that duplicate must be removed in order to eliminate the conflict.  

The number of operations needed to wipe out `i` elements from the front is:

```
ceil((i + 1) / 3)   // because we delete 3 at a time
```

With integer arithmetic:

```
(i + 3) / 3
```

That’s it! The moment a duplicate appears you can immediately compute the answer.

### Why does it work?

* Any duplicate must be removed by deleting all elements up to and including its left‑most occurrence.  
* Deleting in chunks of 3 means we need to remove enough *prefix* elements to cover that index.  
* The formula is simply the ceiling of `(index+1)/3`.  

---

## 3. The Brute‑Force (The Ugly)

Simulate the deletions: repeatedly

1. Count frequencies; if no duplicates, stop.  
2. Delete the first 3 (or fewer) elements.  

This is **O(n²)** because each deletion requires shifting the vector (or copying the slice).  
With the 100‑element limit it will run fast, but it is wasteful and hard to understand.

---

## 4. Code Snippets

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.

### 4.1 Java

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int minimumOperations(int[] nums) {
        Set<Integer> seen = new HashSet<>();
        // traverse from the back
        for (int i = nums.length - 1; i >= 0; i--) {
            if (!seen.add(nums[i])) {          // duplicate found
                return (i + 3) / 3;             // ceil((i+1)/3)
            }
        }
        return 0;  // all unique
    }
}
```

### 4.2 Python

```python
class Solution:
    def minimumOperations(self, nums: List[int]) -> int:
        seen = set()
        for i in range(len(nums) - 1, -1, -1):
            if nums[i] in seen:          # duplicate
                return (i + 3) // 3       # ceil((i+1)/3)
            seen.add(nums[i])
        return 0
```

### 4.3 C++

```cpp
class Solution {
public:
    int minimumOperations(vector<int>& nums) {
        unordered_set<int> seen;
        for (int i = nums.size() - 1; i >= 0; --i) {
            if (!seen.insert(nums[i]).second) {  // duplicate found
                return (i + 3) / 3;              // ceil((i+1)/3)
            }
        }
        return 0; // all unique
    }
};
```

---

## 5. Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| **Greedy (backward scan)** | **O(n)** | **O(n)** (set of seen values) |
| **Brute‑force simulation** | **O(n²)** | **O(n)** |

Given the tiny input limits, both pass the test set, but the greedy approach scales to thousands of elements and demonstrates algorithmic elegance—an important signal to recruiters.

---

## 6. “The Good, The Bad, and The Ugly” – Interview‑ready Insights

| Aspect | The Good | The Bad | The Ugly |
|--------|----------|---------|----------|
| **Understanding the operation** | The operation deletes a *fixed* chunk from the front. | People often think you can delete *any* element, which is wrong. | Forgetting that the deletion is always from the *front* leads to a wrong simulation. |
| **Greedy rationale** | A duplicate forces the removal of all elements left of it. | Some interviewers may ask: “Why is scanning from the back valid?” | Explaining the “ceil((i+1)/3)” formula can trip up candidates who gloss over integer math. |
| **Edge cases** | Empty array, array with all unique elements, array whose length < 3. | Off‑by‑one errors in the formula. | Forgetting to return `0` when no duplicate is found – you may return a spurious number. |
| **Coding style** | Clear variable names (`seen`, `i`). | Avoid unnecessary copies of the array. | Using a `for (int i = 0; i < n; i++)` loop but then counting duplicates from the front can confuse. |

---

## 7. Why This Problem is a Gold‑mine for Interviews

* **Algorithmic Thinking** – Demonstrates the ability to reduce a seemingly complex operation into a simple greedy scan.  
* **Coding Efficiency** – Showcases mastery of hash‑sets / hash‑maps.  
* **Problem Decomposition** – Identifies the core subproblem (duplicate detection) and solves it in linear time.  

LeetCode 3396 is a *starter* for a “harder” question: **“Minimum number of 3‑element deletions to get a unique array”** – a natural progression for the interview pipeline.

---

## 8. Take‑away: How to Use This in Your Job Hunt

1. **Show the clean solution** in your portfolio (GitHub or Gist).  
2. **Explain the greedy insight** in your cover letter or interview: “I recognized that a duplicate forces the removal of its left side, turning the problem into a simple back‑to‑front scan.”  
3. **Highlight the complexity advantage**: “O(n) time, O(n) space, beating a naive O(n²) simulation.”  
4. **Prepare the integer‑math corner**: “The formula `(i + 3) / 3` is the integer ceiling of `(i + 1)/3` – no floating‑point ops, so it’s safe and fast.”  

These points tick key recruiter check‑boxes: *time complexity*, *space complexity*, *hash‑based data structures*, *algorithmic elegance*.

---

## 9. Final Thoughts

LeetCode 3396 is small but powerful.  
The **backward greedy scan** is your “good” – simple, fast, and scalable.  
The brute‑force simulation is the “ugly” – educational but unnecessarily complex.  
The key interview‑lesson? **Recognize the structure of the operation** and translate it into a linear pass.  

Good luck crushing interviews and landing that software‑engineer role! 🚀

---