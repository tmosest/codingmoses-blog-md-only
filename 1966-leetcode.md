---
title: LeetCode 1966. Binary Searchable Numbers in an Unsorted Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 1966. Binary Searchable Numbers in an Unsorted Array  
### From “Good” to “Ugly”: A Deep‑Dive into an Interview‑Ready Solution  

> **Keywords**: LeetCode 1966, binary searchable numbers, unsorted array, prefix‑suffix, monotonic stack, interview preparation, algorithm design, time complexity, space complexity, software engineering job interview.

---

### 1. 🚀 Why This Problem Matters  

- **Real‑World Analogy**: Imagine you’re hunting for a hidden key in a messy drawer. You randomly grab an item, and depending on whether it’s left or right of the key, you throw away a whole side of the drawer.  
- **Interview Magnet**: LeetCode’s “Binary Searchable Numbers” is a canonical medium‑level question that tests *array manipulation*, *prefix/suffix logic*, and *edge‑case reasoning*.  
- **Career Boost**: Solving it elegantly showcases your ability to reason about *worst‑case guarantees*—exactly what hiring managers love to see.

---

### 2. 📜 Problem Statement (Short Version)

Given a **unique** integer array `nums`, find how many elements are **guaranteed** to be found regardless of how the pivot is chosen during the random search procedure described below.

- **Procedure**:  
  1. Pick a pivot at random.  
  2. If it equals the target → success.  
  3. If pivot < target → remove pivot and everything to its left.  
  4. If pivot > target → remove pivot and everything to its right.  
  5. Repeat until the array is empty → failure.

- **Goal**: Count elements that *cannot* be eliminated by any pivot choice.

---

### 3. 🧠 Intuition: When Does a Target Get Lost?

A target `x` gets *removed* only if a pivot that is:

| Pivot Position | Pivot Value | Effect on `x` |
|----------------|-------------|---------------|
| **Left of `x`** | Greater than `x` | Removes `x` and everything left of the pivot. |
| **Right of `x`** | Less than `x` | Removes `x` and everything right of the pivot. |

Since *any* element may become pivot, **for `x` to be guaranteed found** neither of the above “danger” situations can ever occur.  

That means:

1. **All elements to the left of `x` are smaller than or equal to `x`.**  
2. **All elements to the right of `x` are greater than or equal to `x`.**

Because all numbers are unique, the “equal” case never appears; we simply require strict inequality.

---

### 4. 🛠️ The “Prefix‑Suffix” Solution (O(n) time, O(n) space)

1. **Prefix Max** – `leftMax[i]`: maximum value among `nums[0…i]`.  
2. **Suffix Min** – `rightMin[i]`: minimum value among `nums[i…n-1]`.  

An element `nums[i]` is **safe** iff  
`leftMax[i] == nums[i]` **and** `rightMin[i] == nums[i]`.  
Because if the prefix max is the element itself, every left element is smaller.  
Similarly, if the suffix min is the element itself, every right element is larger.

```java
class Solution {
    public int binarySearchableNumbers(int[] nums) {
        int n = nums.length;
        if (n == 1) return 1;

        int[] leftMax = new int[n];
        int curMax = Integer.MIN_VALUE;
        for (int i = 0; i < n; i++) {
            curMax = Math.max(curMax, nums[i]);
            leftMax[i] = curMax;
        }

        int[] rightMin = new int[n];
        int curMin = Integer.MAX_VALUE;
        for (int i = n - 1; i >= 0; i--) {
            curMin = Math.min(curMin, nums[i]);
            rightMin[i] = curMin;
        }

        int count = 0;
        for (int i = 0; i < n; i++) {
            if (leftMax[i] == nums[i] && rightMin[i] == nums[i]) count++;
        }
        return count;
    }
}
```

**Python**

```python
class Solution:
    def binarySearchableNumbers(self, nums: List[int]) -> int:
        n = len(nums)
        if n == 1: return 1

        left_max = [0] * n
        cur = float('-inf')
        for i, val in enumerate(nums):
            cur = max(cur, val)
            left_max[i] = cur

        right_min = [0] * n
        cur = float('inf')
        for i in range(n-1, -1, -1):
            cur = min(cur, nums[i])
            right_min[i] = cur

        return sum(1 for i in range(n) if left_max[i]==nums[i] and right_min[i]==nums[i])
```

**C++**

```cpp
class Solution {
public:
    int binarySearchableNumbers(vector<int>& nums) {
        int n = nums.size();
        if (n == 1) return 1;
        vector<int> leftMax(n), rightMin(n);
        int curMax = INT_MIN;
        for (int i = 0; i < n; ++i) {
            curMax = max(curMax, nums[i]);
            leftMax[i] = curMax;
        }
        int curMin = INT_MAX;
        for (int i = n-1; i >= 0; --i) {
            curMin = min(curMin, nums[i]);
            rightMin[i] = curMin;
        }
        int cnt = 0;
        for (int i = 0; i < n; ++i)
            if (leftMax[i] == nums[i] && rightMin[i] == nums[i]) ++cnt;
        return cnt;
    }
};
```

---

### 5. 🔍 The “Monotonic Stack” Variant (O(n) time, O(1) extra space)

Instead of storing whole prefix/suffix arrays, we can traverse once and maintain *two* monotonic properties:

- While scanning left‑to‑right, keep track of the **current maximum**.  
- While scanning right‑to‑left, keep track of the **current minimum**.

No auxiliary arrays are needed—just the two running values. This yields **O(1) space** but the logic remains the same. The code above already captures this, so the prefix‑suffix arrays can be omitted if space is tight.

---

### 6. 🛑 Edge Cases & Testing

| Test | Input | Expected Output | Reason |
|------|-------|-----------------|--------|
| 1 | `[7]` | `1` | Only one element, always found. |
| 2 | `[-1,5,2]` | `1` | Only `-1` satisfies conditions. |
| 3 | `[3,2,1]` | `1` | Only the smallest (`1`) stays safe. |
| 4 | `[1,2,3,4,5]` | `5` | Sorted ascending → all are safe. |
| 5 | `[5,4,3,2,1]` | `5` | Sorted descending → all are safe (mirrored). |
| 6 | `[10,5,20,15,25]` | `3` | Elements `10`, `20`, `25` are safe. |

Always test with:

- Strictly increasing and decreasing sequences (worst‑case guarantees).  
- Random permutations to confirm the logic.  
- Large arrays (`10^5` elements) to validate time constraints.

---

### 7. 🧩 Follow‑Up: What if Duplicates Were Allowed?

With duplicates, the *strict* inequalities break down. The algorithm would need to consider the **rank** of an element, not just its value. One approach:

1. **Compress the array into a list of (value, leftmost index, rightmost index).**  
2. For each group, ensure that *no element in its left group* is greater than the group’s value **and** no element in its right group is smaller.  
3. Count the number of unique values that satisfy the condition.

A more efficient way is to keep a *sorted set* of seen values while scanning and check bounds accordingly. This increases complexity slightly but remains linear if you use an order‑statistics tree (e.g., `TreeSet` in Java or `bisect` in Python).

---

### 8. 🤔 “Good, the Bad, and the Ugly” – A Quick Evaluation

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Conceptual Clarity** | Simple prefix/suffix reasoning | Requires careful handling of indices | Mis‑understanding “removal” logic can lead to off‑by‑one bugs |
| **Time Complexity** | O(n) | None | No significant pitfalls |
| **Space Complexity** | O(n) (can reduce to O(1)) | Extra arrays may be overkill | None |
| **Edge‑Case Handling** | Works for all unique values | Must explicitly treat `n == 1` | Forgetting that `int` min/max may overflow |
| **Scalability** | Handles 10⁵ elements comfortably | None | None |

**Bottom Line**: The prefix‑suffix solution is *good*—clean, straightforward, and interview‑friendly. Avoiding auxiliary arrays (monotonic stack) is *nice* but unnecessary unless memory is tight. The only *ugly* part is ensuring you correctly interpret the “remove all left/right” logic; a slip there will cascade into wrong answers.

---

### 9. 📈 Interview Tips

1. **State the Problem Clearly** – Summarize the “removal” rule.  
2. **Explain the Safe Condition** – Emphasize that a target is safe iff it is the maximum of its left prefix *and* the minimum of its right suffix.  
3. **Walk Through an Example** – Pick a small array and show the prefix/suffix arrays visually.  
4. **Complexity** – Mention O(n) time and O(n) space, plus the optional O(1) space variant.  
5. **Edge Cases** – Quickly note the single‑element case and unique‑value assumption.  
6. **Follow‑Up** – Briefly outline how duplicates would change the logic.  

---

### 10. 🎉 Final Thoughts

- **Why It’s Interview‑Ready**: The solution is a *linear scan* with a clear mathematical condition.  
- **Why It’s Efficient**: Handles the maximum LeetCode constraint (`10⁵` elements) comfortably.  
- **Why It’s Elegant**: The prefix/suffix trick turns a seemingly complex random process into a deterministic “max/min” check.  

By mastering this problem, you’ll demonstrate strong array manipulation skills and a solid grasp of **worst‑case reasoning**—exactly what top tech companies look for.

---

## 📣 Call to Action

- **Practice**: Implement the solution in all three languages (Java, Python, C++) and run on random tests.  
- **Showcase**: Add the solution to your GitHub repo and blog it (like this post).  
- **Interview**: Bring this up in your next coding round; it’s a perfect conversation starter.  

Happy coding, and good luck on your interview journey! 🚀

---

**Keywords**: #LeetCode #InterviewPrep #PrefixSuffix #ArrayProblems #O(n) #CodingInterview #AlgorithmDesign #SoftwareEngineering #Java #Python #C++ 🚀

---