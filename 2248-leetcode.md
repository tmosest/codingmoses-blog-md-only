---
title: LeetCode 2248. Intersection of Multiple Arrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2248 – Intersection of Multiple Arrays  
*Java | Python | C++ – 3 complete solutions*  
*Blog – Good, Bad & Ugly of this easy problem (SEO‑friendly for job seekers)*  

---

### Problem Recap (LeetCode 2248)

> **Intersection of Multiple Arrays**  
> **Difficulty:** *Easy*  
> **Signature (Java):** `public List<Integer> intersection(int[][] nums);`  

You are given a 2‑D array `nums`.  
Each `nums[i]` is a non‑empty array of **distinct positive integers**.  
Return a sorted list of integers that appear in **every** array of `nums`.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[[3,1,2,4,5],[1,2,3,4],[3,4,5,6]]` | `[3,4]` | Only 3 and 4 appear in all three arrays |
| `[[1,2,3],[4,5,6]]` | `[]` | No common element |

**Constraints**

* `1 ≤ nums.length ≤ 1000`
* `1 ≤ sum(nums[i].length) ≤ 1000`
* `1 ≤ nums[i][j] ≤ 1000`
* All numbers inside a single array are distinct.

---

## 📌 Core Idea

Because the maximum value of an element is **1000**, a *frequency array* of size `1001` is enough.  
For every number we keep a counter of how many arrays it appeared in.  
After processing all arrays we collect numbers whose counter equals `nums.length`.

This yields **O(total_elements)** time and **O(1)** additional memory (1001 integers).

Other approaches:
* **Set intersection** (HashSet) – clean but slightly heavier on memory/time.
* **Sorting** each array and performing a multi‑pointer merge – unnecessary for this constraints.

---

## 🧩 Code Solutions

Below are three self‑contained solutions in Java, Python, and C++.

### 1️⃣ Java – Frequency Array

```java
import java.util.*;

class Solution {
    public List<Integer> intersection(int[][] nums) {
        List<Integer> result = new ArrayList<>();
        int[] freq = new int[1001]; // index 0 unused (values start at 1)

        for (int[] arr : nums) {
            for (int val : arr) {
                freq[val]++;            // count appearance per array
            }
        }

        for (int i = 1; i <= 1000; i++) {
            if (freq[i] == nums.length) {
                result.add(i);
            }
        }
        return result;
    }
}
```

> **Time Complexity:** `O(totalElements)`  
> **Space Complexity:** `O(1)` (fixed 1001 ints)

### 2️⃣ Python – Counter (frequency array via list)

```python
from typing import List

class Solution:
    def intersection(self, nums: List[List[int]]) -> List[int]:
        freq = [0] * 1001   # values 1..1000

        for arr in nums:
            for val in arr:
                freq[val] += 1

        return [i for i in range(1, 1001) if freq[i] == len(nums)]
```

### 3️⃣ C++ – Frequency Array

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> intersection(vector<vector<int>>& nums) {
        vector<int> freq(1001, 0);          // indices 0..1000
        for (auto &arr : nums) {
            for (int val : arr) freq[val]++;  // count per array
        }
        vector<int> ans;
        for (int i = 1; i <= 1000; ++i)
            if (freq[i] == nums.size()) ans.push_back(i);
        return ans;
    }
};
```

All three implementations are **O(totalElements)** and produce a sorted list of common integers.

---

## 📝 Blog Article – “Good, Bad & Ugly” of this LeetCode Problem

> **SEO Keywords:** *LeetCode Intersection of Multiple Arrays*, *Java intersection solution*, *Python intersection arrays*, *C++ intersection problem*, *job interview coding*, *algorithm interview questions*, *frequent interview problem*, *array intersection tutorial*  

---

### The Good: Why This Problem Is a Goldmine for Interview Prep

1. **Simplicity, Yet Deep Insight**  
   The problem looks trivial, but it forces you to think about *data structures* that balance speed and memory.  
   Choosing the right approach (frequency array vs. set vs. sorting) demonstrates understanding of time/space trade‑offs—a key interview skill.

2. **Clear Constraints Let You Optimize**  
   With `max(nums[i][j]) = 1000`, you can exploit a bounded frequency array.  
   Highlighting this optimization shows you can read constraints and adapt your algorithm accordingly.

3. **Deterministic Output**  
   The sorted list requirement is a tiny but important detail that tests your attention to detail—another interview staple.

4. **Cross‑Language Consistency**  
   Having working solutions in Java, Python, and C++ demonstrates adaptability and code‑transferability, which hiring managers love.

---

### The Bad: Common Pitfalls and How to Avoid Them

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Using a Set for each array and repeatedly intersecting** | Thought that “set intersection” is always fastest | Set operations are `O(n)` but with larger constants; plus you allocate new sets each time. |
| **Ignoring the “distinct” guarantee** | Assuming duplicates inside a single array | If duplicates were possible, you'd need a `HashSet` per array to dedupe before counting. |
| **Iterating over the entire 1001 size array** | Mistaking it as a big O cost | It's O(1); no real cost. |
| **Returning unsorted list** | Forgetting to sort or using an unordered container | Either sort the final vector/list or iterate in ascending order (as in the frequency array). |

### The Ugly: Edge Cases That Can Trip You Up

1. **Empty Intersection**  
   When no number appears in every array, you must return an empty list. A wrong counter check (e.g., `== nums.length - 1`) would produce a wrong answer.

2. **Large Number of Arrays vs. Small Total Elements**  
   `nums.length` can be up to 1000, but the sum of all elements is also bounded by 1000.  
   A naive approach that builds a map for every element might still pass, but it’s unnecessary. Optimize for the given bounds.

3. **Input Formatting in Different Languages**  
   In C++ you must be careful with `vector<vector<int>>& nums` vs. `vector<vector<int>> nums` to avoid copies.  
   In Java, using `int[][]` is fine, but you need to remember that `int` is primitive; no boxing overhead.

---

### SEO‑Optimized Summary

If you’re preparing for a coding interview, the **LeetCode 2248 – Intersection of Multiple Arrays** is a *must‑solve* problem.  
It’s a perfect showcase of:

* Using **frequency arrays** for bounded integer values  
* Handling **sorted output** without extra sorting steps  
* Writing **clean, language‑agnostic** code (Java, Python, C++)

These solutions will help you land that data‑structure & algorithm interview—and you’ll have a tangible example to talk about in your next job interview.

---

## 📌 Takeaway Checklist

- [ ] Use a frequency array (size 1001) for the best time‑space trade‑off.  
- [ ] Count each element only once per array.  
- [ ] Collect elements whose count equals `nums.length`.  
- [ ] Return the result in ascending order (iteration order ensures sorting).  
- [ ] Test edge cases: empty intersection, single array, maximum constraints.

Happy coding, and good luck landing that dream job!