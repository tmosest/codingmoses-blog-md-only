---
title: LeetCode 2089. Find Target Indices After Sorting Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 2089 – “Find Target Indices After Sorting Array”  
### Java | Python | C++ Solutions + In‑Depth Blog Post  

> **TL;DR**  
> Sort the array once (`O(n log n)`), then scan for the target.  
> Complexity: **Time = O(n log n)**, **Space = O(m)** (m = number of target hits).  
> Alternate O(n) solution exists using a counting sort (when values ≤ 100).

---

## 1. Code in Three Languages

```java
/*  Java 17+  */
import java.util.*;

public class Solution {
    public List<Integer> targetIndices(int[] nums, int target) {
        Arrays.sort(nums);                      // O(n log n)
        List<Integer> ans = new ArrayList<>();
        for (int i = 0; i < nums.length; ++i)   // O(n)
            if (nums[i] == target) ans.add(i);
        return ans;                             // sorted by construction
    }
}
```

```python
#  Python 3.10+
def targetIndices(nums: list[int], target: int) -> list[int]:
    nums.sort()                              # O(n log n)
    return [i for i, v in enumerate(nums) if v == target]
```

```cpp
//  C++17
#include <vector>
#include <algorithm>

class Solution {
public:
    std::vector<int> targetIndices(std::vector<int>& nums, int target) {
        std::sort(nums.begin(), nums.end());  // O(n log n)
        std::vector<int> ans;
        for (int i = 0; i < nums.size(); ++i)
            if (nums[i] == target) ans.push_back(i);
        return ans;
    }
};
```

---

## 2. Blog Article  
*(Markdown format – ready for a blog platform or GitHub README)*

```markdown
# Finding Target Indices After Sorting: The Good, the Bad, and the Ugly

*Published 2025‑10‑01 – Author: <Your Name>*  

> **Keywords:** LeetCode 2089, target indices, sorting, search, interview, job interview, Java, Python, C++, algorithm, time complexity, space complexity, counting sort.

## 1. The Problem in a Nutshell

> **LeetCode 2089**  
> Given a 0‑indexed integer array `nums` and a target `target`, return the list of all indices where `nums[i] == target` *after* sorting `nums` in non‑decreasing order.  
> Return the indices in **increasing order**. If the target is not present, return an empty list.

### Why is this a good interview question?

1. **Clarity of Requirements** – It tests whether you can read and translate a precise statement into code.  
2. **Multiple Solutions** – It can be solved in `O(n log n)` (sort + linear scan) or in `O(n)` using a counting sort (given the value bounds).  
3. **Edge Cases** – Empty arrays, single‑element arrays, all elements equal, target missing, etc.  
4. **Code Quality** – Encourages clean, idiomatic solutions in the language of choice.

## 2. The “Good” – The Straightforward, Readable Approach

The most obvious solution is to **sort** the array and then **scan** it once to collect indices where the element equals the target.

### Steps

1. **Sort** `nums` → `O(n log n)`  
2. **Iterate** through the sorted array  
   - If `nums[i] == target`, append `i` to the result.  
3. **Return** the result list (already sorted by construction).

### Why this is “good”

* **Simplicity** – Easy to understand and implement.  
* **Correctness** – Sorting guarantees that indices are in ascending order.  
* **Universality** – Works regardless of the value range or distribution.  

**Python implementation**

```python
def targetIndices(nums, target):
    nums.sort()
    return [i for i, v in enumerate(nums) if v == target]
```

**Java implementation**

```java
public List<Integer> targetIndices(int[] nums, int target) {
    Arrays.sort(nums);
    List<Integer> ans = new ArrayList<>();
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] == target) ans.add(i);
    }
    return ans;
}
```

**C++ implementation**

```cpp
vector<int> targetIndices(vector<int>& nums, int target) {
    sort(nums.begin(), nums.end());
    vector<int> ans;
    for (int i = 0; i < nums.size(); ++i)
        if (nums[i] == target) ans.push_back(i);
    return ans;
}
```

### Complexity

| Measure | Complexity | Explanation |
|---------|------------|-------------|
| Time    | `O(n log n)` | Sorting dominates; linear scan is `O(n)` |
| Space   | `O(m)` | `m` is the number of occurrences of `target` |

## 3. The “Bad” – The Naïve “Sort‑and‑Scan” with Edge‑Case Pitfalls

The straightforward solution is great, but it’s worth highlighting common mistakes that can cost interviewers a few points:

1. **Ignoring the Sorted Requirement** – Returning indices from the *original* array violates the “after sorting” clause.  
2. **Mutable vs. Immutable Arrays** – In Java, forgetting to pass a copy or using the same array reference may unintentionally modify caller data.  
3. **Using `List` Instead of `ArrayList`** – On Java, `List<Integer>` with default implementation might be `LinkedList`, incurring unnecessary overhead.  
4. **Indexing Errors** – Off‑by‑one mistakes in loops, especially when iterating through arrays of size 1.  

### How to Avoid Them

* **Double‑check the problem statement** – Confirm the “after sorting” requirement.  
* **Prefer `Arrays.sort`** – It’s a stable, well‑tested method.  
* **Use an `ArrayList`** – Faster for random access and resizing.  
* **Unit‑test** – Provide at least five test cases covering corner scenarios.

## 4. The “Ugly” – A Sub‑Optimal `O(n²)` or Incorrect Algorithm

A common beginner mistake is to **search the target in the original array**, then compute the sorted index manually, e.g.:

```python
for i, val in enumerate(nums):
    if val == target:
        # attempt to compute sorted index
```

This approach is:

* **Incorrect** – The index in the sorted array is not the same as the original index.  
* **Inefficient** – May involve repeated sorting or counting, leading to `O(n²)` or higher complexity.  
* **Confusing** – Makes the code harder to read and maintain.

> **Lesson** – Always stick to the problem’s constraints: *sort first, then collect indices.*

## 5. A Faster Alternative – Counting Sort (O(n))

Given the constraints (`1 ≤ nums[i], target ≤ 100`), you can bypass the general `O(n log n)` sort and achieve linear time with a counting sort:

1. **Build a frequency array** of size 101 (`freq[1..100]`).  
2. **Compute prefix sums** to determine the starting index of each value in the sorted array.  
3. **Scan the original array** to fill a result vector with indices where `nums[i] == target`.

**Python (illustrative, not production‑ready)**

```python
def targetIndices(nums, target):
    freq = [0] * 101
    for v in nums:
        freq[v] += 1

    start = 0
    for v in range(1, target):
        start += freq[v]

    # All indices for target will start at `start`
    return list(range(start, start + freq[target]))
```

**Pros**  
* `O(n)` time, no comparison‑based sort overhead.  
* `O(1)` auxiliary memory (fixed 101‑size array).

**Cons**  
* Tied to the known value range (≤ 100).  
* Slightly more complex code, harder to understand at a glance.

> **When to use** – In an interview if you want to demonstrate awareness of counting sort, or if the interviewer explicitly asks for the fastest possible solution.

## 6. Test Suite

| # | Input | Target | Expected Output |
|---|-------|--------|-----------------|
| 1 | [1,2,5,2,3] | 2 | [1,2] |
| 2 | [1,2,5,2,3] | 3 | [3] |
| 3 | [1,2,5,2,3] | 5 | [4] |
| 4 | [4,4,4,4] | 4 | [0,1,2,3] |
| 5 | [10] | 1 | [] |
| 6 | [1,2,3] | 4 | [] |

Run the code in your chosen language and verify each case.

## 7. Interview Takeaways

| Skill | What to Show |
|-------|--------------|
| **Reading** | Spot that you must sort first. |
| **Algorithmic Thinking** | Offer the counting‑sort optimization. |
| **Coding** | Clean, idiomatic code; proper naming; comments. |
| **Testing** | Edge cases: single element, missing target, all equal. |
| **Time/Space Analysis** | Explain `O(n log n)` vs. `O(n)` and when to choose each. |

## 8. Final Thoughts

* The problem is deceptively simple, but it is a great litmus test for fundamentals: sorting, linear scans, and careful reading of requirements.  
* The “good” solution is easy, readable, and passes all tests.  
* The “bad” pitfalls are common and worth avoiding; a brief self‑check before you hand over code is invaluable.  
* The “ugly” approach is a reminder: keep your algorithm consistent with the problem statement.  

**Want to land that software engineering role?**  
Mastering these micro‑problems builds confidence, demonstrates solid fundamentals, and showcases your ability to write clean, efficient code—exactly what hiring managers look for.

Happy coding! 🚀
```

---

### How to Use This Blog Post

1. **Copy the Markdown** into your blog platform (WordPress, Medium, Dev.to, etc.).  
2. **Replace `<Your Name>`** with your real name or handle.  
3. **Add a hero image** (e.g., a screenshot of the LeetCode interface or a sorted array diagram).  
4. **Publish** and share on LinkedIn / Twitter with the hashtags: `#LeetCode2089 #CodingInterview #Java #Python #Cpp #Algorithms`.  

*SEO note:* The first paragraph and section headings contain the target keyword “LeetCode 2089”, ensuring search engines understand the content focus.

---

**Happy interviewing!**