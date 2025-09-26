---
title: LeetCode 2089. Find Target Indices After Sorting Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 2089 – Find Target Indices After Sorting Array  
**Languages:** Java | Python | C++  
**Difficulty:** Easy  
**Complexity:** O(n log n) time, O(m) auxiliary space ( m = number of target occurrences)

---

### 1.1 Problem Summary  
Given an integer array `nums` and a target value `target`, return **all indices** of the array *after* sorting it in non‑decreasing order.  
If the target is not present, return an empty list.  
Indices must be returned in ascending order.

| Example | Input | Output | Explanation |
|---------|-------|--------|-------------|
| 1 | `[1,2,5,2,3]`, `2` | `[1,2]` | Sorted → `[1,2,2,3,5]`; indices of `2` are `1` and `2`. |
| 2 | `[1,2,5,2,3]`, `3` | `[3]` | Sorted → `[1,2,2,3,5]`; index of `3` is `3`. |
| 3 | `[1,2,5,2,3]`, `5` | `[4]` | Sorted → `[1,2,2,3,5]`; index of `5` is `4`. |

**Constraints**

* `1 ≤ nums.length ≤ 100`
* `1 ≤ nums[i], target ≤ 100`

---

### 1.2 Intuition

The simplest approach is:

1. Sort the array.
2. Scan the sorted array once and record every index that equals `target`.

Because the array is small (`≤ 100`), this brute‑force method is fast enough and easy to understand.  
For larger inputs, we could avoid sorting by counting occurrences (frequency array) – a linear‑time O(n) solution – but the problem explicitly asks for the indices **after** sorting, so we keep the sort step.

---

### 1.3 Algorithm

```text
targetIndices(nums, target):
    sort(nums)                     // O(n log n)
    ans = empty list
    for i from 0 to nums.length-1:
        if nums[i] == target:
            ans.append(i)          // record 0‑based index
    return ans                     // already sorted
```

---

### 1.4 Correctness Proof  

We prove that the algorithm returns exactly the set of target indices after sorting.

1. **Sorting**  
   After `sort(nums)`, the array `nums` is in non‑decreasing order.  
   Therefore, any occurrence of `target` in the original array will appear at some position `i` in the sorted array, and the sorted array contains *exactly* those elements that were originally present (no duplicates removed or added).

2. **Scanning**  
   The loop iterates over every index `i` in the sorted array.  
   For each `i`, if `nums[i] == target`, the algorithm appends `i` to `ans`.  
   Thus, `ans` contains all and only the indices `i` for which `nums[i] == target`.

3. **Ordering**  
   Because the loop processes indices in increasing order, `ans` is sorted ascending, as required.

Therefore the algorithm returns precisely the target indices after sorting, satisfying the specification. ∎

---

### 1.5 Complexity Analysis

* **Time**  
  *Sorting*: `O(n log n)`  
  *Scanning*: `O(n)`  
  Overall: **`O(n log n)`**.

* **Space**  
  *Result list*: `O(m)` where `m` is the number of target occurrences (worst case `n`).  
  *No extra data structures*: **`O(m)`** auxiliary space.

---

### 1.6 Edge Cases & Pitfalls

| Edge case | Why it matters | How the algorithm handles it |
|-----------|----------------|-----------------------------|
| `nums` contains no `target` | Should return empty list | Loop never appends → returns empty list |
| All elements equal `target` | Result is `[0,1,2,…,n‑1]` | Loop appends all indices |
| `nums` already sorted | Still works, sorting is idempotent | Sorting still runs but costs minimal |
| `target` appears multiple times non‑contiguously | Sorting groups them | Indices captured correctly |

---

## 2.  Code Implementations

Below are fully commented, ready‑to‑paste solutions for **Java**, **Python**, and **C++**.

---

### 2.1 Java 17

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Solution {
    /**
     * Return the list of indices where the target appears after sorting.
     *
     * @param nums   input array (mutable)
     * @param target target value
     * @return list of target indices in sorted array
     */
    public List<Integer> targetIndices(int[] nums, int target) {
        // 1. Sort the array
        Arrays.sort(nums);

        // 2. Scan and collect indices
        List<Integer> ans = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == target) {
                ans.add(i);           // 0‑based index
            }
        }
        return ans;                   // already sorted
    }

    // Simple main for quick manual testing
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.targetIndices(new int[]{1, 2, 5, 2, 3}, 2)); // [1, 2]
        System.out.println(s.targetIndices(new int[]{1, 2, 5, 2, 3}, 3)); // [3]
        System.out.println(s.targetIndices(new int[]{1, 2, 5, 2, 3}, 5)); // [4]
    }
}
```

---

### 2.2 Python 3

```python
from typing import List

class Solution:
    def targetIndices(self, nums: List[int], target: int) -> List[int]:
        """
        Return indices of target after sorting the array.
        """
        # 1. Sort in place
        nums.sort()

        # 2. Collect indices where element equals target
        ans = [i for i, val in enumerate(nums) if val == target]
        return ans

# Quick manual tests
if __name__ == "__main__":
    s = Solution()
    print(s.targetIndices([1, 2, 5, 2, 3], 2))  # [1, 2]
    print(s.targetIndices([1, 2, 5, 2, 3], 3))  # [3]
    print(s.targetIndices([1, 2, 5, 2, 3], 5))  # [4]
```

---

### 2.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> targetIndices(vector<int>& nums, int target) {
        // 1. Sort the vector
        sort(nums.begin(), nums.end());

        // 2. Gather target indices
        vector<int> ans;
        for (int i = 0; i < (int)nums.size(); ++i) {
            if (nums[i] == target) {
                ans.push_back(i);          // 0‑based index
            }
        }
        return ans;                       // already sorted
    }
};

// Simple driver for manual testing
int main() {
    Solution s;
    vector<int> res1 = s.targetIndices({1,2,5,2,3}, 2);
    vector<int> res2 = s.targetIndices({1,2,5,2,3}, 3);
    vector<int> res3 = s.targetIndices({1,2,5,2,3}, 5);

    auto printVec = [](const vector<int>& v){
        cout << "[";
        for (size_t i = 0; i < v.size(); ++i) {
            if (i) cout << ", ";
            cout << v[i];
        }
        cout << "]\n";
    };

    printVec(res1); // [1, 2]
    printVec(res2); // [3]
    printVec(res3); // [4]
}
```

---

## 3.  SEO‑Optimized Blog Post

> **Title**  
> *LeetCode 2089 – Find Target Indices After Sorting Array: Java, Python & C++ Solutions + Interview Insights*

> **Meta Description**  
> Master LeetCode 2089 in seconds. Learn the easy algorithm, read detailed Java, Python, and C++ code, and discover interview‑friendly explanations that will impress recruiters.

---

### 3.1 Introduction  

If you’re preparing for a software‑engineering interview, **LeetCode 2089 – Find Target Indices After Sorting Array** is a perfect “low‑hanging fruit” to showcase your coding style, problem‑solving mindset, and language fluency.  
This article walks you through:

1. A clear problem statement and constraints.  
2. Intuition behind the optimal solution.  
3. Step‑by‑step algorithm and proof of correctness.  
4. Java, Python, and C++ implementations with inline comments.  
5. “The Good, The Bad, and The Ugly” – common pitfalls and how to avoid them.  
6. Interview‑friendly talking points and why recruiters love this problem.

---

### 3.2 Problem Recap  

> **Goal** – Return all indices of `target` **after** sorting the input array in non‑decreasing order.  
> **Output** – A sorted list of 0‑based indices, or an empty list if `target` is absent.  

With constraints `1 ≤ nums.length ≤ 100` and `1 ≤ nums[i], target ≤ 100`, the problem is easy on a technical level but tricky enough to show your understanding of sorting, scanning, and edge‑case handling.

---

### 3.3 The Good

* **Clear & Concise** – The statement is straightforward; you only need to sort and scan.  
* **Small Data Size** – No worries about exceeding time limits; `O(n log n)` is more than fast enough.  
* **Multiple Languages** – You can showcase proficiency in Java, Python, or C++ – all three are common interview languages.  
* **Test‑Friendly** – You can write quick unit tests for each language, proving your code works for edge cases.

---

### 3.4 The Bad

* **Sorting vs. Counting** – Some candidates over‑engineer by counting frequencies to achieve `O(n)`, but they lose the required “after sorting” property.  
* **Wrong Indexing** – Off‑by‑one errors when collecting indices after sorting are common for beginners.  
* **Ignoring Edge Cases** – Forgetting that the target might not exist or that all elements are the same can lead to subtle bugs.

---

### 3.5 The Ugly

* **Mutable Inputs** – Sorting the input array mutates it. If the problem statement or your testing harness expects the original array to stay intact, you’ll be penalized.  
* **Performance Myths** – Believing that `sort` is *always* the bottleneck, you might add unnecessary complexity (e.g., building a frequency map).  
* **Code Readability** – A block of code that blindly sorts, loops, and appends without comments looks unprofessional to interviewers.

---

### 3.6 Step‑by‑Step Algorithm  

```text
1. Sort nums ascending.
2. For each index i in 0…n‑1:
       if nums[i] == target:
           append i to answer list.
3. Return answer list (already sorted).
```

**Why this works**  
* Sorting guarantees that all `target` values are grouped together in the resulting array.  
* Scanning in linear order collects the indices in ascending order automatically.  

**Proof of Correctness** – See the “Proof of Correctness” section earlier; the loop only captures indices that match the target, and sorting preserves the relative positions.

---

### 3.7 Code Samples  

*(Use the code blocks from Section 2 above. Remember to add explanatory comments if you’re writing in an interview environment.)*

---

### 3.8 Common Pitfalls & Fixes  

| Pitfall | Fix |
|---------|-----|
| Mutating the input array | `int[] sorted = nums.clone();` before sorting. |
| Off‑by‑one when appending | Use 0‑based `i` from `enumerate` (Python) or loop counter (Java/C++). |
| Not handling missing target | Loop logic naturally returns empty list; no special case needed. |
| Using `Arrays.sort(nums)` but forgetting `Collections` | Ensure you import `java.util.*` or `std::vector` utilities correctly. |

---

### 3.9 Interview Talking Points  

1. **Explain the requirement** – “After sorting” means we cannot just count frequencies.  
2. **Discuss time vs. space** – `O(n log n)` vs. `O(n)` – why `O(n)` counting would fail.  
3. **Mention mutability** – Clarify that we’re allowed to modify the input or, if not, show a clone.  
4. **Edge cases** – Talk through scenarios: no target, all targets, already sorted.  
5. **Testing** – Show unit tests or quick assertions in your language of choice.  

Recruiters love candidates who articulate their thought process, anticipate corner cases, and keep code clean.

---

### 3.10 Why Recruiters Love This Problem

1. **Fast to Solve** – You can produce a working solution in under 5 minutes.  
2. **Language Agnostic** – The same logic applies to Java, Python, and C++.  
3. **Demonstrates Sorting Knowledge** – Sorting is a core algorithm taught in CS.  
4. **Clear Evaluation** – Automated test cases produce deterministic outputs, making grading simple for the hiring manager.

---

### 3.11 Final Takeaway  

*LeetCode 2089* may look trivial, but it’s a great “mini‑assignment” that checks for:

* Sorting‑correctness  
* Proper index collection  
* Handling of missing or multiple target values  
* Clean, language‑appropriate code

Use the provided Java, Python, and C++ snippets as a template, but add your own comments, tests, and edge‑case checks to make your solution shine in any interview setting.

Happy coding, and good luck on your next interview!  

--- 

### 4.  Conclusion  

You now have:

* A rigorous understanding of LeetCode 2089, complete with algorithm, proof, and complexity analysis.  
* Ready‑to‑paste, commented code in **Java**, **Python**, and **C++**.  
* A SEO‑optimized blog post that explains the problem, showcases the solution, and prepares you for interview questions.  

Show this code in your portfolio, include it in your LeetCode “Discuss” threads, or present it in your next technical interview – either way, it’s a conversation starter that demonstrates both coding proficiency and a thoughtful problem‑solving approach. 🚀

--- 

**Happy coding!**