---
title: LeetCode 962. Maximum Width Ramp - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Fastest Way to Solve LeetCode 962 – Maximum Width Ramp  
### (Java | Python | C++ – 3 Complete Solutions + SEO‑Optimized Blog)

---

### 📌 Problem Summary  

**LeetCode 962 – Maximum Width Ramp**  
Given an integer array `nums`, find the maximum width `j - i` such that `i < j` and `nums[i] <= nums[j]`. If no such ramp exists, return `0`.  

**Constraints**

|   |  |
|---|---|
| `nums.length` | `2 ≤ n ≤ 5 × 10⁴` |
| `nums[i]` | `0 ≤ nums[i] ≤ 5 × 10⁴` |

---

## 🎯 Why This Problem Is a Must‑Know for Interviews

* **Classic “monotonic stack” pattern** – frequently appears on coding interviews (e.g., *Largest Rectangle in Histogram*, *Next Greater Element*).  
* **Linear‑time solution** – O(n) time, O(n) space; beating the naive O(n²) brute‑force is critical.  
* **Showcases array manipulation + data structure knowledge** – a perfect “show‑off” problem for interviews and résumé bullets.

---

## 🔧 The O(n) Strategy – Monotonic Stack + Two‑Pointer Scan

1. **Build a decreasing stack**  
   * Traverse `nums` from left to right.  
   * Push index `i` onto the stack **only if** the stack is empty or `nums[i]` is **strictly smaller** than the value at the top of the stack.  
   * After this pass the stack contains indices of a strictly decreasing subsequence.

2. **Scan from right to left**  
   * For each index `j` from `n-1` down to `0`, pop from the stack while `nums[stack.top()] ≤ nums[j]`.  
   * Each pop gives a valid ramp `(i, j)`; compute width `j - i` and keep the maximum.  
   * Stop when the stack becomes empty.

> **Why does this work?**  
> The stack keeps candidate left‑indices `i` that are as early as possible while keeping `nums[i]` high enough to match future `j`.  
> Scanning from the right guarantees we find the farthest possible `j` for each `i` in O(1) amortized time.

---

## 📑 Complexity Analysis

|  | Time | Space |
|---|------|-------|
| Algorithm | **O(n)** | **O(n)** (stack) |
| Brute‑Force | O(n²) | O(1) |

The stack is built once and popped at most once per element, giving linear time.

---

## 🧑‍💻 Code Snippets

Below are fully‑commented, ready‑to‑run solutions for **Java**, **Python**, and **C++**.

> ⚠️ **Make sure to import required packages** (`java.util.*`, `typing.List`, `vector`, `stack`).

---

### 1️⃣ Java (Java 17+)

```java
import java.util.Stack;

public class Solution {
    /**
     * Returns the maximum width ramp in the array.
     * Uses a monotonic decreasing stack + reverse scan.
     */
    public int maxWidthRamp(int[] nums) {
        int n = nums.length;
        Stack<Integer> stack = new Stack<>();

        // Step 1: Build a decreasing stack of indices
        for (int i = 0; i < n; i++) {
            if (stack.isEmpty() || nums[i] < nums[stack.peek()]) {
                stack.push(i);
            }
        }

        int maxWidth = 0;

        // Step 2: Scan from the right, popping while valid
        for (int j = n - 1; j >= 0; j--) {
            while (!stack.isEmpty() && nums[stack.peek()] <= nums[j]) {
                int i = stack.pop();
                maxWidth = Math.max(maxWidth, j - i);
            }
        }

        return maxWidth;
    }

    // Simple main for quick testing
    public static void main(String[] args) {
        Solution sol = new Solution();
        int[] nums = {6,0,8,2,1,5};
        System.out.println(sol.maxWidthRamp(nums)); // Output: 4
    }
}
```

---

### 2️⃣ Python 3.10+

```python
from typing import List

class Solution:
    def maxWidthRamp(self, nums: List[int]) -> int:
        """
        Monotonic decreasing stack + reverse scan.
        :type nums: List[int]
        :rtype: int
        """
        stack = []  # indices with decreasing nums values

        # Build stack
        for i, val in enumerate(nums):
            if not stack or val < nums[stack[-1]]:
                stack.append(i)

        max_width = 0
        # Scan from right to left
        for j in range(len(nums) - 1, -1, -1):
            while stack and nums[stack[-1]] <= nums[j]:
                i = stack.pop()
                max_width = max(max_width, j - i)

        return max_width

# Quick test
if __name__ == "__main__":
    sol = Solution()
    print(sol.maxWidthRamp([6,0,8,2,1,5]))  # 4
```

---

### 3️⃣ C++17

```cpp
#include <vector>
#include <stack>
#include <algorithm>

class Solution {
public:
    int maxWidthRamp(std::vector<int>& nums) {
        int n = nums.size();
        std::stack<int> st;

        // Build decreasing stack of indices
        for (int i = 0; i < n; ++i) {
            if (st.empty() || nums[i] < nums[st.top()]) {
                st.push(i);
            }
        }

        int maxWidth = 0;
        // Scan from the end
        for (int j = n - 1; j >= 0; --j) {
            while (!st.empty() && nums[st.top()] <= nums[j]) {
                int i = st.top(); st.pop();
                maxWidth = std::max(maxWidth, j - i);
            }
        }
        return maxWidth;
    }
};

// Optional main for testing
/*
#include <iostream>
int main() {
    Solution s;
    std::vector<int> nums = {6,0,8,2,1,5};
    std::cout << s.maxWidthRamp(nums) << std::endl; // 4
    return 0;
}
*/
```

---

## 🗂️ “The Good, The Bad, and The Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Concept** | Monotonic stack is elegant, linear‑time | Some candidates forget to check `nums[i] <= nums[j]` → off‑by‑one errors | Mis‑using a *normal* stack (LIFO) instead of a *monotonic* stack can lead to quadratic behavior. |
| **Complexity** | O(n) time, O(n) space | O(n²) brute‑force (common early fail) | O(n²) due to nested loops after wrong stack logic. |
| **Readability** | Stack comments + two‑pass explanation | Too many magic numbers, missing variable names | Confusing variable names (`st`, `i`, `j`) make it hard to follow. |
| **Edge Cases** | Handles duplicate values, non‑decreasing arrays | Forget to pop all stack elements | Forget to break when stack empty → infinite loop. |
| **Interview Tips** | Explain the intuition first, then the two‑pass logic | Show your time complexity estimate | Avoid writing full code before thinking; be prepared to justify each line. |

---

## 🎯 How to Use This in Your Interview Preparation

1. **Explain the intuition**: Talk about “we want the farthest right index j for each possible left index i”.  
2. **Show the stack invariant**: Indices in the stack represent potential left endpoints where `nums[i]` is strictly decreasing.  
3. **Walk through an example**: Use the LeetCode example `[6,0,8,2,1,5]` to show stack building and popping.  
4. **Discuss edge cases**: All equal numbers, strictly increasing, strictly decreasing arrays.  
5. **Mention alternatives**: Binary search on a sorted copy of indices, two‑pointer approach with a sorted list – but note that they are usually O(n log n).  

---

## 📈 SEO & Career‑Boosting Takeaways

* **Keywords**: *Maximum Width Ramp*, *LeetCode 962*, *monotonic stack*, *coding interview*, *Java interview question*, *Python interview*, *C++ interview*, *algorithm interview*, *O(n) array problem*  
* **Meta‑Description** (≈155 chars):  
  > “Learn the fastest Java, Python, and C++ solution to LeetCode 962 – Maximum Width Ramp. Understand the monotonic stack trick, time/space analysis, and interview tips to ace your coding interview.”
* **Title Tags**:  
  * “Maximum Width Ramp – 962 | O(n) Monotonic Stack Solution (Java, Python, C++)”  
  * “Job‑Ready LeetCode 962 Solution – Fastest Way to Max Width Ramp”
* **Structure**: Use H1 for the title, H2 for sections (Problem, Approach, Complexity, Code, FAQ, etc.) to help search engines index the article cleanly.  

---

## 📝 Final Thoughts

- **Why it matters**: Mastering the monotonic stack technique not only lands you a *Top‑30 LeetCode* problem in an interview but also shows recruiters you can design efficient, clean solutions.
- **Practice**: Run the three snippets on random test cases, including edge cases. Use `assert` statements or unit tests to validate correctness.
- **Show confidence**: In an interview, walk through the algorithm with a whiteboard, then translate it to code. The explanation often weighs more than a perfect implementation.

Good luck! 🌟  
*If you found this helpful, share it on LinkedIn and comment your own interview experience with LeetCode problems.*