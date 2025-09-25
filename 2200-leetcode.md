---
title: LeetCode 2200. Find All K-Distant Indices in an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1.  Code Solutions  

Below you will find **three complete, self‑contained implementations** of the LeetCode 2200 problem – “Find All K‑Distant Indices in an Array” – written in **Java, Python, and C++**.  
All solutions run in **O(n)** time and **O(1)** auxiliary space (apart from the output list).

> **Problem Recap**  
> Given an integer array `nums`, a `key` value that appears in the array, and an integer `k`, return **all indices** `i` such that there exists at least one index `j` with `nums[j] == key` and `|i-j| ≤ k`.  
> The result must be sorted in ascending order.

---

### 1.1 Java

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<Integer> findKDistantIndices(int[] nums, int key, int k) {
        List<Integer> result = new ArrayList<>();
        int n = nums.length;
        int start = 0;                     // the first index that is not yet marked

        for (int j = 0; j < n; j++) {
            if (nums[j] == key) {
                int left  = Math.max(start, j - k);
                int right = Math.min(n - 1, j + k);

                for (int i = left; i <= right; i++) {
                    result.add(i);
                }
                start = right + 1;          // skip already added indices
            }
        }
        return result;
    }

    // Simple test harness
    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {3,4,9,1,3,9,5};
        System.out.println(s.findKDistantIndices(nums, 9, 1)); // [1, 2, 3, 4, 5, 6]
    }
}
```

---

### 1.2 Python

```python
from typing import List

class Solution:
    def findKDistantIndices(self, nums: List[int], key: int, k: int) -> List[int]:
        n = len(nums)
        res = []
        start = 0                         # first unmarked index

        for j, val in enumerate(nums):
            if val == key:
                left  = max(start, j - k)
                right = min(n - 1, j + k)
                res.extend(range(left, right + 1))
                start = right + 1          # skip already marked indices
        return res

# Demo
if __name__ == "__main__":
    sol = Solution()
    print(sol.findKDistantIndices([3,4,9,1,3,9,5], 9, 1))   # [1, 2, 3, 4, 5, 6]
```

---

### 1.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> findKDistantIndices(vector<int>& nums, int key, int k) {
        int n = nums.size();
        vector<int> res;
        int start = 0;                 // first index not yet marked

        for (int j = 0; j < n; ++j) {
            if (nums[j] == key) {
                int left  = max(start, j - k);
                int right = min(n - 1, j + k);
                for (int i = left; i <= right; ++i) res.push_back(i);
                start = right + 1;       // skip already marked indices
            }
        }
        return res;
    }
};

int main() {
    Solution s;
    vector<int> nums{3,4,9,1,3,9,5};
    auto res = s.findKDistantIndices(nums, 9, 1);
    for (int x : res) cout << x << ' ';
    cout << '\n';                     // 1 2 3 4 5 6
}
```

---

## 2.  The Good, The Bad, and The Ugly – A SEO‑Optimized Blog Article

### Title
**“LeetCode 2200 – The Good, The Bad, and The Ugly of Finding K‑Distant Indices”**

---

#### Meta Description
> Master LeetCode 2200 “Find All K‑Distant Indices” with a deep dive into the best O(n) solution, pitfalls, and a clean Java/Python/C++ implementation. Ideal for coding interview prep, algorithm tutorials, and boosting your software engineering résumé.

---

### Introduction  

When preparing for software‑engineering interviews, **LeetCode 2200** is a popular “easy” problem that tests your understanding of array traversal and range marking. Even though the statement is simple, many candidates fall into common traps that cost them runtime or memory.

In this article we will dissect the problem, expose the “good”, “bad”, and “ugly” aspects, and provide a **ready‑to‑copy** solution in Java, Python, and C++. The goal is not just to solve the problem but to showcase the **clean, interview‑ready style** that recruiters look for.

> **Keywords**: LeetCode 2200, K‑Distant Indices, algorithm interview, coding interview, job interview, software engineer, Java, Python, C++ solutions, time complexity, space complexity.

---

### 2.1 Problem Restatement (for context)

> **Input**  
> `nums` – integer array (size ≤ 10⁵)  
> `key` – an integer that appears at least once in `nums`  
> `k` – maximum allowed distance

> **Output**  
> All indices `i` such that ∃ `j` with `nums[j] == key` and `|i‑j| ≤ k`.  
> The output must be sorted ascendingly.

---

### 2.2 The “Good” – Why this problem is a great interview teaching moment

| Why | What it teaches |
|-----|-----------------|
| **Simple Input** | You can focus on algorithmic patterns instead of language syntax. |
| **Linear Complexity** | Demonstrates that **O(n)** is achievable with clever marking – a key skill for interviewers. |
| **No Additional Constraints** | Allows you to show clean code and comment style – essential for a professional résumé. |
| **Reusable Pattern** | The “interval marking” trick is a micro‑variant of sweep‑line, useful in interval covering, scheduling, and range update problems. |

---

### 2.3 The “Bad” – Common pitfalls

1. **Brute‑Force O(n²)**  
   *Nested loops checking every pair of indices.*  
   - *Runtime*: O(10⁵²) → TLE on LeetCode.  
   - *Memory*: Unnecessary extra checks.

2. **Over‑Marking with a Boolean Array**  
   ```python
   marked = [False] * n
   for j in key_indices:
       for i in range(max(0, j-k), min(n, j+k+1)):
           marked[i] = True
   ```
   - *Time*: Still O(n²) in worst case (when many key positions).  
   - *Space*: O(n) extra array – acceptable but not optimal.

3. **Using `set` to Deduplicate**  
   ```java
   Set<Integer> res = new HashSet<>();
   // ...
   res.add(i);
   ```
   - *Problem*: HashSet insertion & conversion to sorted list adds overhead and defeats the “sorted‑output” requirement.

---

### 2.4 The “Ugly” – Why naive solutions are unattractive in interviews

| Problem | What recruiters dislike |
|---------|--------------------------|
| **Excessive nested loops** | Shows lack of algorithmic thinking, leads to TLE, indicates weak problem‑solving skills. |
| **Unnecessary extra memory** | In interviews, every byte counts. |
| **Duplicate work** | Adds micro‑overheads and indicates sloppy implementation. |

---

### 3.  The “Best” – One‑Pass Interval Marking (Optimized Solution)

#### 3.1 Core Idea  

- **Key Observation**: If `nums[j] == key`, then every index `i` in the inclusive interval `[max(0, j‑k), min(n‑1, j+k)]` is automatically a valid k‑distant index.  
- **Goal**: Collect all such intervals and **merge** them efficiently, **avoiding duplicates**.

#### 3.2 Algorithm Walkthrough  

1. **Initialize**  
   - `start = 0` – the smallest index that has **not yet been added** to the result.  
   - `result = []` – final list.

2. **Traverse the array once** (`j = 0 … n-1`):  
   - If `nums[j] == key`:
     - Compute the left boundary `left = max(start, j - k)`.  
     - Compute the right boundary `right = min(n-1, j + k)`.  
     - Append all indices from `left` to `right` inclusive to `result`.  
     - Update `start = right + 1` – we can safely skip indices already added.

3. **Return** `result`.

Because we skip already‑added indices (`start` always moves forward), every index is appended **exactly once**, guaranteeing both **uniqueness** and **sorted order** without any post‑sorting.

---

#### 3.3 Complexity Analysis  

| Complexity | Explanation |
|------------|-------------|
| **Time** | Each array element is visited once. The inner loop that appends indices runs only for indices that are **new** (monotonic `start`). Thus total operations = O(n). |
| **Space** | Only the output list is needed. No auxiliary arrays or hash sets. Hence O(1) extra space. |

---

#### 3.4 Ready‑to‑Copy Code (Java, Python, C++)

*(Refer to the code section above for full implementations.)*

---

### 4.  Interview‑Friendly Tips  

| Tip | Why it matters |
|-----|----------------|
| **Read the statement twice** – confirm that `key` *does* appear, and the result must be sorted. |
| **Avoid nested loops** – think of intervals instead of pairs. |
| **Use a moving pointer (`start`)** – prevents double‑counting and keeps linear time. |
| **Test edge cases**:  
  - All elements equal to `key`.  
  - `k` = 0.  
  - `key` at array boundaries. | Ensures robustness. |
| **Explain your solution verbally** – recruiters love candidates who can articulate the algorithm, not just write code. |

---

### 5.  How This Helps Your Job Hunt  

- **Showcasing the O(n) solution** demonstrates your ability to **optimize** naïve approaches, a highly prized skill in performance‑critical software roles.  
- **Providing multi‑language implementations** signals versatility (Java for enterprise, Python for data science, C++ for systems).  
- **Discussing pitfalls** reflects deep understanding of algorithmic complexity, which interviewers look for.  

---

### 6.  Take‑away Checklist  

- ✅ Understand the **interval marking** pattern.  
- ✅ Implement a **single‑pass** solution with a `start` pointer.  
- ✅ Verify edge cases (`k = 0`, key at extremes, dense key occurrences).  
- ✅ Comment code and explain complexity.  
- ✅ Practice explaining the algorithm out loud.

---

#### Closing Thought  

LeetCode 2200 is a *teachable micro‑problem*: it forces you to translate a mathematical condition into an efficient sweep. Mastering it, and being able to explain the pattern in interviews, signals to recruiters that you’re ready for **real‑world performance‑oriented coding**.

Happy coding, and good luck with your next interview!