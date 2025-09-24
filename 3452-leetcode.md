---
title: LeetCode 3452. Sum of Good Numbers - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 3452 – “Sum of Good Numbers”  
**Java | Python | C++** – One‑pass, O(n) solutions  
**Blog article** – The good, the bad, and the ugly of this problem (SEO‑optimized for hiring)

---

### 1. Problem Recap

> **Given** an integer array `nums` and an integer `k`, an element `nums[i]` is **good** if it is strictly greater than the elements at indices `i‑k` and `i+k` (if those indices exist).  
> **Return** the sum of all good elements.

**Constraints**  
* 2 ≤ nums.length ≤ 100  
* 1 ≤ nums[i] ≤ 1000  
* 1 ≤ k ≤ ⌊nums.length / 2⌋  

---

### 2. Why this problem is a “Must‑Know” interview trick

| Good | Bad | Ugly |
|------|-----|------|
| **Good** – Very short, O(n) time, O(1) space, no extra data structures. | **Bad** – The “distance‑k” condition can be misunderstood; people sometimes check `i‑1` or `i+1` instead of `i‑k`. | **Ugly** – Off‑by‑one errors when `k` equals the array size or when `i‑k` or `i+k` goes out of bounds. |

---

### 3. One‑Pass Solution (All three languages)

#### 3.1 Java

```java
// Java 17
public class Solution {
    public int sumOfGoodNumbers(int[] nums, int k) {
        int n = nums.length;
        int sum = 0;

        for (int i = 0; i < n; i++) {
            boolean good = true;

            if (i - k >= 0 && nums[i] <= nums[i - k]) good = false;
            if (i + k < n  && nums[i] <= nums[i + k]) good = false;

            if (good) sum += nums[i];
        }
        return sum;
    }
}
```

#### 3.2 Python

```python
# Python 3
def sum_of_good_numbers(nums: list[int], k: int) -> int:
    n = len(nums)
    total = 0

    for i in range(n):
        good = True
        if i - k >= 0 and nums[i] <= nums[i - k]:
            good = False
        if i + k < n and nums[i] <= nums[i + k]:
            good = False
        if good:
            total += nums[i]
    return total
```

#### 3.3 C++

```cpp
// C++17
int sumOfGoodNumbers(vector<int>& nums, int k) {
    int n = nums.size(), sum = 0;
    for (int i = 0; i < n; ++i) {
        bool good = true;
        if (i - k >= 0 && nums[i] <= nums[i - k]) good = false;
        if (i + k < n  && nums[i] <= nums[i + k]) good = false;
        if (good) sum += nums[i];
    }
    return sum;
}
```

All three snippets share the same **O(n)** time, **O(1)** space complexity.

---

### 4. Step‑by‑Step Explanation

1. **Initialize the accumulator** `sum` (or `total` in Python).  
2. **Loop through every index** `i` in the array.  
3. **Assume** the current element is good (`good = true`).  
4. **Check the left neighbor**:  
   * If `i-k >= 0` and `nums[i] <= nums[i-k]`, the element is **not** good.  
5. **Check the right neighbor**:  
   * If `i+k < n` and `nums[i] <= nums[i+k]`, the element is **not** good.  
6. **If still good**, add `nums[i]` to the accumulator.  
7. After the loop, **return** the final sum.

The two boundary checks (`i-k >= 0` and `i+k < n`) automatically handle the “edge” cases where only one neighbor exists or none.

---

### 5. Edge Cases & Common Pitfalls

| Pitfall | Why it happens | Fix |
|---------|----------------|-----|
| **Using `i-1` and `i+1` instead of `i-k` / `i+k`** | Misreading the problem statement. | Double‑check the distance parameter `k`. |
| **Ignoring out‑of‑bounds indices** | Accessing `nums[-1]` or `nums[n]` triggers a runtime error. | Always guard the check with `>= 0` and `< n`. |
| **Missing the “strictly greater” condition** | Using `>=` instead of `>` allows equal values to be considered good. | Keep the comparison as `<=`. |
| **Confusing `sum` vs `count`** | Some solutions mistakenly count the number of good elements instead of summing them. | Use a variable that clearly represents “total value” (e.g., `total` or `sum`). |

---

### 6. Performance Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time Complexity | O(n) | O(n) | O(n) |
| Space Complexity | O(1) | O(1) | O(1) |
| Constant Factor | Very low (only a few integer ops per iteration) | Low | Very low |

Because the array size is at most 100, the runtime is negligible on modern machines, but the pattern scales to larger inputs (the logic works for `n` up to millions).

---

### 7. Interview‑Ready Tips

1. **Read the statement carefully** – `k` can be up to `floor(n/2)`; there can be two neighbors, one neighbor, or none.
2. **Explain your reasoning** – Mention that we iterate once, check two conditions, and accumulate.  
3. **Talk about edge cases** – Discuss indices that might go out of bounds and how you guard against them.  
4. **Show the time/space complexity** – A succinct “O(n) time, O(1) space” is usually enough.  
5. **Ask clarifying questions** – “What if `k` equals `n/2`? What about negative numbers?” (the constraints guarantee positive values, but it’s a good habit).

---

### 8. SEO‑Optimized Blog Post Outline

| Section | SEO Keywords |
|---------|--------------|
| Title | “Sum of Good Numbers LeetCode 3452 – Java, Python, C++ Solutions” |
| Meta Description | “Learn how to solve LeetCode 3452 ‘Sum of Good Numbers’ with clear Java, Python, and C++ code. Understand the algorithm, edge cases, and interview tips.” |
| H1 | Sum of Good Numbers – LeetCode 3452 |
| H2 | Problem Statement |
| H2 | One‑Pass Algorithm |
| H3 | Java Implementation |
| H3 | Python Implementation |
| H3 | C++ Implementation |
| H2 | Edge Cases & Pitfalls |
| H2 | Time & Space Complexity |
| H2 | Interview Tips & Best Practices |
| H2 | Conclusion – Why This Problem Matters |

Add internal links to related LeetCode topics (e.g., “Array Manipulation”, “Two Pointers”) and external links to the official LeetCode problem page and discussion solutions for credibility.

---

### 9. Final Thoughts

The “Sum of Good Numbers” problem is a textbook example of a **simple yet subtle** interview question. Mastering it demonstrates:

* Ability to translate problem statements into precise conditions.  
* Comfort with boundary checking.  
* Clarity in communicating complexity and edge cases.

With the Java, Python, and C++ solutions above, you’re ready to impress both coding platforms and hiring managers alike. Happy coding! 🚀