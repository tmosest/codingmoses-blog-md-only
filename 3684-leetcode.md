---
title: LeetCode 3684. Maximize Sum of At Most K Distinct Elements - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap

**LeetCode #3684 – “Maximize Sum of At Most K Distinct Elements”**

> Given an array `nums` of positive integers and an integer `k`, pick **at most** `k` elements such that  
>  1. The chosen numbers are **distinct**  
>  2. Their sum is maximised  
>  3. Return the chosen numbers in **strictly descending** order

The array length is up to 100, each element up to \(10^9\).

---

## 2.  Quick Solution Sketch

1. **Deduplicate** – put all numbers into a `Set` (or `unordered_set` / `TreeSet`).
2. **Sort descending** – convert to a list/array, sort in decreasing order.
3. **Take the first `k`** – that’s the maximum‑sum subset.

Because `k` is never larger than the number of distinct elements, we can simply slice the first `k` values.  
The algorithm runs in \(O(n \log n)\) time (sorting dominates) and uses \(O(n)\) extra space.

---

## 3.  Reference Implementations

### 3.1 Python 3

```python
class Solution:
    def maxKDistinct(self, nums: list[int], k: int) -> list[int]:
        """
        Deduplicate, sort descending, slice k.
        """
        # 1. Deduplicate
        unique = set(nums)

        # 2. Sort descending
        sorted_desc = sorted(unique, reverse=True)

        # 3. Take the first k elements
        return sorted_desc[:k]
```

**One‑liner version** (perfect for coding contests):

```python
class Solution:
    def maxKDistinct(self, nums, k): 
        return sorted(set(nums), reverse=True)[:k]
```

---

### 3.2 Java 17

```java
import java.util.*;

public class Solution {
    public int[] maxKDistinct(int[] nums, int k) {
        // Use a TreeSet to keep numbers sorted automatically (descending order)
        TreeSet<Integer> ts = new TreeSet<>(Collections.reverseOrder());
        for (int n : nums) ts.add(n);

        int[] result = new int[Math.min(k, ts.size())];
        int idx = 0;
        for (int val : ts) {
            if (idx >= k) break;
            result[idx++] = val;
        }
        return result;
    }
}
```

> **Why TreeSet?**  
> It removes duplicates *and* keeps the elements sorted, so we never need an explicit sort step.

---

### 3.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> maxKDistinct(vector<int>& nums, int k) {
        // unordered_set for O(1) deduplication
        unordered_set<int> uniq(nums.begin(), nums.end());
        // copy to vector and sort descending
        vector<int> vals(uniq.begin(), uniq.end());
        sort(vals.begin(), vals.end(), greater<int>());
        // take up to k elements
        if (k < (int)vals.size()) vals.resize(k);
        return vals;
    }
};
```

---

## 4.  Blog Article – “The Good, The Bad, The Ugly of LeetCode #3684”

### 4.1  Introduction

If you’re prepping for a tech interview, one of the most common “Easy” problems is **Maximize Sum of At Most K Distinct Elements**. It looks simple but teaches you how to think about **deduplication, sorting, and early termination** – all skills that interviewers love to see.

In this post we’ll walk through:

- Why the problem looks easy but can trip you up  
- A clean, 3‑line solution (Python) and its Java/C++ equivalents  
- Edge cases that can bite you  
- Tips to keep the code clean and test‑friendly  
- How to use this problem to land a job

And, of course, we’ll keep it SEO‑friendly: “LeetCode 3684 solution”, “maximize sum at most k distinct elements”, “interview coding challenge”, etc.

---

### 4.2  The Good: What Makes This Problem Great for Interviews

1. **Small Input Size** – `n <= 100` lets you afford an \(O(n \log n)\) solution, but also gives you time to think about data structures.
2. **Multiple Language Support** – You can showcase Java TreeSet, Python set, C++ unordered_set + sort.  
3. **Conceptual Depth** – It tests *sorting*, *set operations*, and *slice logic* in a single statement.  
4. **Clear Expected Output** – Strictly descending order removes ambiguity.  
5. **Potential for Variation** – Once you know the core, you can extend it to “return the subset” instead of just sum.

---

### 4.3  The Bad: Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Not handling duplicates** | Many solve the “pick top k” problem but forget the distinct requirement. | Use a `set` or a `TreeSet`/`unordered_set`. |
| **Over‑sorting** | Sorting the entire array when only the top `k` is needed. | Sort the *unique* list, then slice. |
| **Wrong order** | Returning ascending instead of descending. | Explicitly sort with `reverse=True` or `Collections.reverseOrder()`. |
| **Index out of bounds** | `k` may be larger than number of distinct elements. | Use `Math.min(k, set.size())` or slice safely. |
| **Time limit on big ints** | For huge numbers, Python’s `int` is fine, but be careful with C++ overflow. | Use `long long` in C++ if needed (though here values <= 1e9). |

---

### 4.4  The Ugly: Things You Might Do That Make the Code Messy

1. **Manual Duplicate Removal** – Writing a nested loop to filter duplicates is tedious and error‑prone.  
2. **Sorting the Whole Array** – `Arrays.sort(nums)` followed by a manual deduplication step.  
3. **Verbose Variable Names** – Over‑commenting the trivial parts.  
4. **Ignoring Edge Cases** – Forgetting the case when `k` equals the number of distinct elements.

*Clean code* is the best interview asset. Use language‑idioms (sets, streams, etc.) to keep it short.

---

### 4.5  The 3‑Line Python Solution (Why It Beats 100%)

```python
class Solution:
    def maxKDistinct(self, nums, k):
        return sorted(set(nums), reverse=True)[:k]
```

- **Deduplication** – `set(nums)` removes duplicates instantly.  
- **Sorting** – `sorted(..., reverse=True)` gives descending order.  
- **Slicing** – `[:k]` grabs the first `k` items or all if fewer.

That’s all you need. It runs in \(O(n \log n)\) and uses \(O(n)\) memory. Interviewers love the brevity and correctness.

---

### 4.6  Extending the Solution (Optional)

If you’re stuck on interview questions, try these variations:

1. **Return the sum instead of the elements** – just `sum(sorted(set(nums), reverse=True)[:k])`.
2. **Return the indices of the selected elements** – store a map from value to index list.
3. **Maximize sum with a *minimum* number of distinct elements** – pick the smallest distinct count that reaches the sum.

Each extension reinforces the same core ideas.

---

### 4.7  Test Your Solution

```python
assert Solution().maxKDistinct([84,93,100,77,90], 3) == [100,93,90]
assert Solution().maxKDistinct([84,93,100,77,93], 3) == [100,93,84]
assert Solution().maxKDistinct([1,1,1,2,2,2], 6) == [2,1]
assert Solution().maxKDistinct([5], 1) == [5]
assert Solution().maxKDistinct([5,5,5], 2) == [5]
```

Running these assertions ensures that duplicates, small `k`, and single‑element arrays are all handled.

---

### 4.8  SEO‑Friendly Wrap‑Up

*Keywords:* **LeetCode 3684 solution**, **maximize sum of at most k distinct elements**, **Python LeetCode easy solution**, **Java TreeSet deduplication**, **C++ unordered_set sorting**, **interview coding challenge**.

**Why this post matters:**  
- It shows you a fast, clean solution.  
- It highlights pitfalls to avoid.  
- It’s written in a format that recruiter bots love: clear title, code blocks, bullet lists.

If you’re preparing for a coding interview, add this to your “Easy” bag and practice the variations. Good luck!

--- 

### 5.  TL;DR (Take‑away)

| Language | Core Steps | Code Snippet |
|----------|------------|--------------|
| **Python** | `set → sorted(reverse) → slice` | `sorted(set(nums), reverse=True)[:k]` |
| **Java** | `TreeSet<>(reverse)` → iterate up to `k` | See Java section above |
| **C++** | `unordered_set → vector → sort(greater)` → resize | See C++ section above |

Happy coding—and good luck with your next interview!