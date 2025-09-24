---
title: LeetCode 2006. Count Number of Pairs With Absolute Difference K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code

Below are three idiomatic solutions that run in **O(n)** time and **O(1)** additional space (a constant‑size frequency table or a hash map).

| Language | Solution |
|----------|----------|
| **Java** | `Solution.java` |
| **Python** | `solution.py` |
| **C++** | `solution.cpp` |

---

### 1.1  Java – `Solution.java`

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int countKDifference(int[] nums, int k) {
        // Frequency map: number -> its count in nums
        Map<Integer, Integer> freq = new HashMap<>();

        for (int num : nums) {
            freq.put(num, freq.getOrDefault(num, 0) + 1);
        }

        int count = 0;
        for (int num : nums) {
            // Only look for num + k (the pair i < j guarantees we don't double‑count)
            int target = num + k;
            Integer tCnt = freq.get(target);
            if (tCnt != null) {
                count += tCnt;
            }
        }
        return count;
    }
}
```

---

### 1.2  Python – `solution.py`

```python
from collections import Counter
from typing import List

def countKDifference(nums: List[int], k: int) -> int:
    freq = Counter(nums)          # O(n) time, O(1) extra space (max 100 distinct keys)
    count = 0
    for num in nums:
        count += freq.get(num + k, 0)
    return count
```

---

### 1.3  C++ – `solution.cpp`

```cpp
#include <vector>
#include <unordered_map>

class Solution {
public:
    int countKDifference(std::vector<int>& nums, int k) {
        std::unordered_map<int, int> freq;
        for (int num : nums) freq[num]++;            // build freq map

        int ans = 0;
        for (int num : nums) {
            auto it = freq.find(num + k);
            if (it != freq.end()) ans += it->second;
        }
        return ans;
    }
};
```

> **Why this works**  
> We count each element *once* and add the number of occurrences of the value that is exactly `k` larger. Because we only look forward (`num + k`) the pair `(i, j)` with `i < j` is counted exactly once, no double counting and no nested loops.

---

## 2.  Blog Article

**Title:**  
**“Count Number of Pairs With Absolute Difference K – The Good, The Bad, and The Ugly (Java/Python/C++)”**  

**Meta description (SEO):**  
Master LeetCode 2006 in Java, Python, and C++ with an O(n) solution. Learn the pitfalls, edge‑case handling, and how to ace this interview question. Perfect for software engineers hunting jobs.

---

### 2.1  Introduction

You’ve landed a coding interview and the interviewer asks:  
*“Given an array `nums` and an integer `k`, count the pairs (i, j) where i < j and |nums[i] – nums[j]| == k.”*  

This is LeetCode 2006 – a seemingly simple “difference‑k” problem that hides a few subtle traps. In this post we dissect the **good**, the **bad**, and the **ugly** approaches, and deliver a clean, production‑ready solution in **Java, Python, and C++**.

---

### 2.2  Problem Statement (Clear & Concise)

- **Input**  
  - `nums`: integer array, length 1 ≤ n ≤ 200  
  - `k`: 1 ≤ k ≤ 99  
  - `1 ≤ nums[i] ≤ 100`  

- **Output**  
  - The count of index pairs `(i, j)` where `i < j` and `|nums[i] - nums[j]| == k`.

- **Examples**  

  | nums | k | answer | explanation |
  |------|---|--------|-------------|
  | [1,2,2,1] | 1 | 4 | every 1‑2 pair |
  | [1,3] | 3 | 0 | no pair |
  | [3,2,1,5,4] | 2 | 3 | (3,1),(5,3),(4,2) |

---

### 2.3  The Good – O(n) Solution

**Idea** – Use a frequency table (or hash map) to remember how many times each number appears. For each element `x`, the only partner that can form a valid pair is `x + k` (because we enforce `i < j`). By adding `freq[x + k]` to the answer for each `x` we get the total count in linear time.

#### Why It’s Good

| Property | Reason |
|----------|--------|
| **Time** | `O(n)` – two linear passes, no nested loops |
| **Space** | `O(1)` – fixed size array (100 values) or `O(1)` for hash map (max 100 keys) |
| **Clarity** | Straightforward: count frequencies → scan once more |
| **Scalability** | Works for `n` up to millions (still linear) |
| **Testability** | Easy to unit‑test with small helpers |

#### Edge Cases Handled

| Edge | Explanation |
|------|-------------|
| `k = 0` | Problem constraints forbid `k = 0` (k ≥ 1), so no special handling required. |
| Duplicate values | Frequency map automatically counts multiplicity. |
| Negative `k` | Not allowed, but if present, the formula would still work because absolute difference is symmetric. |
| Max element 100 | Frequency array of size 101 is enough. |

---

### 2.4  The Bad – Quadratic Brute Force

A common but naïve approach:

```python
def bad(nums, k):
    count = 0
    for i in range(len(nums)):
        for j in range(i + 1, len(nums)):
            if abs(nums[i] - nums[j]) == k:
                count += 1
    return count
```

#### Why It’s Bad

- **O(n²)** – For `n = 200`, still fine, but for interview scenarios you may face arrays up to 10⁵.
- **Hard to Optimize** – Two nested loops give little room for early exit unless you add complex pruning.
- **Readability** – The intent is obvious, but the inefficiency shows lack of algorithmic thinking.

---

### 2.5  The Ugly – Frequency Array Misuse

Sometimes people use a frequency array but forget to account for **duplicate pairs** or the **i < j** constraint, leading to double‑counting:

```java
int[] freq = new int[101];
for (int x : nums) freq[x]++;

int ans = 0;
for (int x : nums) {
    if (x + k <= 100) ans += freq[x + k];   // wrong if we also counted freq[x] earlier
}
```

**Result** – counts each pair twice. The fix is to **only add** once per occurrence of `x`. That’s why we either:
- Iterate over the *frequency map* itself: `for (int x : freq) ans += freq[x] * freq[x + k]` (and divide by 2 if k = 0).
- Or keep the original loop but rely on `i < j` implicitly (by scanning the original array and looking up only `x + k`).

---

### 2.6  Full Walk‑through – Java Code Explained

```java
public int countKDifference(int[] nums, int k) {
    // Build frequency table
    int[] freq = new int[101];            // indices 1..100
    for (int n : nums) freq[n]++;

    int ans = 0;
    // For each element, count partners that are k larger
    for (int n : nums) {
        int target = n + k;
        if (target <= 100) ans += freq[target];
    }
    return ans;
}
```

*Key points:*

1. **Fixed‑size array** – avoids hash overhead, thanks to the bound `nums[i] ≤ 100`.
2. **Single pass to populate** – O(n) memory ops.
3. **Second pass** – we only look forward (`n + k`), so each valid pair contributes exactly once.

---

### 2.7  Complexity Analysis (All Languages)

| Measure | O(n) Solution | Brute Force |
|---------|---------------|-------------|
| Time | **O(n)** | **O(n²)** |
| Extra Space | **O(1)** | **O(1)** |
| Worst‑case | Linear | Quadratic |

Because the constraints are tiny, all languages hit the same asymptotic complexity. But for a larger dataset or interview interview, the linear solution is the only practical one.

---

### 2.8  Common Pitfalls & Tips

| Pitfall | Fix |
|---------|-----|
| **Double counting** | Only count `x + k`, not `x - k`. |
| **Off‑by‑one in array** | When using a fixed array, make sure indices cover the full range (1–100). |
| **Assuming k ≥ 0** | The problem guarantees `k ≥ 1`, but in general you’d need to handle negative k or `k == 0`. |
| **Ignoring input bounds** | A hash map is safer when values can be large; but if bounds are small, a frequency array is faster. |
| **Not handling k = 0** | If allowed, use `freq[x] * (freq[x] - 1) / 2` to avoid double counting. |

---

### 2.9  Take‑Away

- Use a **frequency map** (or array) to reduce the pair‑checking problem to a single linear scan.
- The **i < j** condition is naturally enforced by only looking for `num + k`.
- Keep your solution **compact** and **well‑documented** – interviewers value clear reasoning as much as correct code.

---

### 2.10  SEO‑Friendly Keywords

- LeetCode 2006
- Count Number of Pairs With Absolute Difference K
- Java solution LeetCode 2006
- Python solution LeetCode 2006
- C++ solution LeetCode 2006
- O(n) pair counting algorithm
- interview coding questions
- algorithmic problem solving
- data structure interview questions
- frequency table algorithm

---

### 2.11  Conclusion

The “Count Number of Pairs With Absolute Difference K” problem is a classic interview puzzle that tests your understanding of **frequency counting**, **hash maps**, and **algorithmic efficiency**. By delivering a clean O(n) solution in Java, Python, and C++, you not only solve the problem but also showcase the ability to spot and correct the common bad and ugly patterns. Good luck landing that job, and happy coding!