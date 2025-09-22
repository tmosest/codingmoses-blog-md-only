---
title: LeetCode 1984. Minimum Difference Between Highest and Lowest of K Scores - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution Code  

Below are three clean, production‑ready implementations of the “Minimum Difference Between Highest and Lowest of K Scores” problem (LeetCode 1984).  
All three use the same optimal strategy – **sort the array and slide a window of size k** – but are written in Java, Python, and C++.

---

### 1.1 Java 17

```java
import java.util.Arrays;

public class Solution {
    /**
     * Returns the minimal possible difference between the highest
     * and lowest scores of any k chosen students.
     *
     * @param nums array of student scores
     * @param k    number of students to pick
     * @return minimal difference
     */
    public int minimumDifference(int[] nums, int k) {
        if (k == 1) return 0;          // trivial case
        Arrays.sort(nums);             // O(n log n)

        int best = Integer.MAX_VALUE;
        for (int i = 0; i <= nums.length - k; i++) {
            int diff = nums[i + k - 1] - nums[i];
            if (diff < best) best = diff;
        }
        return best;                   // O(n)
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.minimumDifference(new int[]{9,4,1,7}, 2)); // 2
    }
}
```

---

### 1.2 Python 3

```python
from typing import List

class Solution:
    def minimumDifference(self, nums: List[int], k: int) -> int:
        """Return minimal difference between max and min of any k scores."""
        if k == 1:
            return 0

        nums.sort()                     # O(n log n)
        best = float('inf')
        for i in range(len(nums) - k + 1):
            diff = nums[i + k - 1] - nums[i]
            best = min(best, diff)
        return best                     # O(n)

# Demo
if __name__ == "__main__":
    sol = Solution()
    print(sol.minimumDifference([9,4,1,7], 2))   # 2
```

---

### 1.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumDifference(vector<int>& nums, int k) {
        if (k == 1) return 0;
        sort(nums.begin(), nums.end());          // O(n log n)

        int best = INT_MAX;
        for (int i = 0; i <= nums.size() - k; ++i) {
            int diff = nums[i + k - 1] - nums[i];
            best = min(best, diff);
        }
        return best;                             // O(n)
    }
};

int main() {
    Solution s;
    vector<int> v = {9,4,1,7};
    cout << s.minimumDifference(v, 2) << '\n';   // 2
    return 0;
}
```

---

## 2.  Blog Article – “Minimum Difference Between Highest and Lowest of K Scores: The Good, The Bad, The Ugly”

### 2.1 Title (SEO‑Optimized)

> **Minimum Difference Between Highest and Lowest of K Scores – Easy Java, Python, C++ Solutions (LeetCode 1984)**

---

### 2.2 Introduction

When interviewing for software engineering roles, interviewers love problems that test a candidate’s ability to reason about data structures and algorithmic complexity.  
LeetCode 1984 – *Minimum Difference Between Highest and Lowest of K Scores* – is one such “easy” problem that nevertheless showcases clarity, efficiency, and code quality.

In this article we’ll:

1. **Explain the problem statement** in plain English.
2. Discuss the **constraints** that shape our approach.
3. Walk through the **optimal solution** – sorting + sliding window – and why it wins.
4. Show three **complete, idiomatic implementations**: Java, Python, C++.
5. Highlight the **good, the bad, and the ugly** of common pitfalls.
6. Provide **SEO tags** so recruiters searching for this problem can find this guide.

---

### 2.3 Problem Restatement

> **Input**: An integer array `nums` (`1 ≤ nums.length ≤ 1000`, each element `0 … 10⁵`) and an integer `k` (`1 ≤ k ≤ nums.length`).  
> **Task**: Pick any `k` scores from `nums`. Among those `k` scores, compute the difference between the highest and lowest. Return the *minimum possible* difference achievable by any choice of `k` scores.

> **Examples**  
> * `nums = [90]`, `k = 1` → answer `0`.  
> * `nums = [9,4,1,7]`, `k = 2` → answer `2` (pick `7` and `9`).

---

### 2.4 Why the Straight‑Forward Brute Force Fails

A naïve solution would enumerate every combination of `k` elements (`O(n choose k)`), compute the max/min for each, and track the best difference.  
For `n = 1000` and even moderate `k`, the number of combinations explodes combinatorially – clearly impractical for interview or production code.

Hence we need a **polynomial‑time** algorithm.

---

### 2.5 The Optimal Strategy – Sorting + Sliding Window

#### 2.5.1 Intuition

If the array is sorted, any group of `k` consecutive elements will have a **minimal spread** among that subset: the first element is the smallest, the last is the largest.  
Conversely, any group of `k` elements that is **not consecutive** can be “tightened” by swapping out an extreme element for a closer one, thus never increasing the spread.

So:

1. **Sort** `nums` – `O(n log n)`.
2. Slide a window of size `k` over the sorted array – `O(n)`.  
   For each window `i…i+k-1`, the difference is `nums[i+k-1] - nums[i]`.
3. Keep the minimal difference encountered.

This gives an overall **time complexity of `O(n log n)`** and **`O(1)` auxiliary space** (ignoring sorting library overhead).

#### 2.5.2 Edge Cases

| Case | Reason | Handling |
|------|--------|----------|
| `k == 1` | Any single element gives diff = 0 | Return 0 immediately |
| `k == nums.length` | Must take all elements | Diff = max(nums) – min(nums) |
| Duplicate scores | Sorting keeps them together; window handles duplicates automatically | No special treatment needed |

---

### 2.6 Full Implementation Walk‑through

#### 2.6.1 Java

*Uses `Arrays.sort()` for in‑place sorting and a simple `for` loop for the sliding window.*

```java
int minimumDifference(int[] nums, int k) {
    if (k == 1) return 0;
    Arrays.sort(nums);
    int best = Integer.MAX_VALUE;
    for (int i = 0; i <= nums.length - k; i++) {
        best = Math.min(best, nums[i + k - 1] - nums[i]);
    }
    return best;
}
```

#### 2.6.2 Python

*Leverages list sort and a `for` range loop.*

```python
def minimumDifference(nums: List[int], k: int) -> int:
    if k == 1:
        return 0
    nums.sort()
    best = float('inf')
    for i in range(len(nums) - k + 1):
        best = min(best, nums[i + k - 1] - nums[i])
    return best
```

#### 2.6.3 C++

*Uses `std::sort` and a classic `for` loop.*

```cpp
int minimumDifference(vector<int>& nums, int k) {
    if (k == 1) return 0;
    sort(nums.begin(), nums.end());
    int best = INT_MAX;
    for (size_t i = 0; i + k <= nums.size(); ++i) {
        best = min(best, nums[i + k - 1] - nums[i]);
    }
    return best;
}
```

All three implementations run in `O(n log n)` time and `O(1)` extra space.

---

### 2.7 Good, Bad, Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | Sorting + window = `O(n log n)` (optimal) | Brute‑force `O(n choose k)` – impractical | None – algorithm is simple |
| **Readability** | Clear separation: sort → window → min | Too many nested loops or unnecessary variables | Magic numbers or hard‑coded indices |
| **Edge Handling** | Explicit `k==1` guard, window bounds safe | Forgetting `k==nums.length` case, off‑by‑one errors | Over‑optimizing with additional data structures that add overhead |
| **Space** | In‑place sort, `O(1)` auxiliary | Building all combinations → `O(nCk)` memory | None |
| **Maintainability** | Comments, consistent style | Mixed naming conventions | Over‑commented or redundant notes |

#### Common Pitfalls

- **Off‑by‑one** in the sliding window loop (`i <= nums.length - k`).
- **Integer overflow**: Use `int` for scores up to `10^5` but keep in mind `nums[i+k-1] - nums[i]` still fits in `int`.
- **Ignoring trivial cases**: `k==1` always yields `0`; early return saves time.

---

### 2.8 SEO Metadata (for Recruiters)

| Meta Tag | Value |
|----------|-------|
| **Title** | Minimum Difference Between Highest and Lowest of K Scores – Easy Java, Python, C++ Solutions (LeetCode 1984) |
| **Description** | Learn how to solve LeetCode 1984 in Java, Python, and C++. Understand the sorting + sliding window strategy, time complexity, edge cases, and interview tips. |
| **Keywords** | LeetCode 1984, Minimum Difference Between Highest and Lowest of K Scores, Java solution, Python solution, C++ solution, sliding window, sorting, algorithm interview, data structures, job interview, coding challenge |

---

### 2.9 Conclusion

LeetCode 1984 is deceptively simple but is a **classic interview test** of:

1. **Problem comprehension** – picking the right sub‑array.
2. **Algorithmic thinking** – recognizing sorting + window as the optimal pattern.
3. **Clean coding** – implementing with clarity and handling edge cases gracefully.

The three implementations above should serve you well in both coding interviews and real‑world codebases.  
Add the article to your portfolio, share it on LinkedIn, and tag it with the SEO metadata so recruiters hunting for this exact problem will see it.

> *Happy coding!* 🚀

--- 

*Feel free to download the full source code snippets or adapt them to your preferred language.*