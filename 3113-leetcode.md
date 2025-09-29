---
title: LeetCode 3113. Find the Number of Subarrays Where Boundary Elements Are Maximum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 LeetCode 3113 – Find the Number of Subarrays Where Boundary Elements Are Maximum  
### The Good, The Bad, and The Ugly  
> *Target audience: front‑end, back‑end, full‑stack engineers preparing for coding interviews and hiring managers.*  

---

## 📌 Problem Recap

> **Given** an array of positive integers `nums`, return the number of subarrays such that the first and the last element of the subarray are **equal to the maximum element** of that subarray.

### Constraints
- `1 ≤ nums.length ≤ 10⁵`
- `1 ≤ nums[i] ≤ 10⁹`

The answer can be as large as `n*(n+1)/2`, so use a 64‑bit integer (`long` / `long long`).

---

## 🔍 Why Is This Problem Interesting?

* **Count subarrays** – a classic interview theme.
* **Boundary elements must be the maximum** – this property leads to a monotonic stack pattern.
* **Large constraints** – brute‑force (`O(n²)` or `O(n³)`) is impossible.  
* **Edge‑case awareness** – all equal numbers, strictly increasing/decreasing, single element, etc.

---

## 📚 The Good

| ✅ Feature | Why It Matters |
|-----------|----------------|
| **O(n) time** – Linear scan with a monotonic stack | Handles the 100k limit comfortably |
| **O(n) space** – Only the stack is stored | Memory footprint is small and predictable |
| **Intuitive logic** – “While the top is smaller, pop” | Easy to explain to interviewers |
| **Reusable pattern** – Monotonic stack solves many “next greater” / “subarray count” problems | Leverage it for other questions |

---

## ⚠️ The Bad

| ❌ Issue | Mitigation |
|----------|------------|
| **Integer overflow** – The answer can exceed `int32` | Use `long` (`int64`) for the result |
| **Mis‑handling equal values** – Need to group consecutive equal elements | Store a `(value, count)` pair in the stack |
| **Off‑by‑one errors** – Subarray boundaries inclusive vs exclusive | Test with small arrays, e.g., `[3,3,3]` → 6 |

---

## 🤬 The Ugly

| 😡 Problem | Typical Mistake |
|------------|-----------------|
| Brute‑force O(n²) counting | Too slow, times out on large input |
| Two nested loops without pruning | Same as above |
| Forgetting the “maximum” constraint | Counting all subarrays instead of those that satisfy the boundary condition |
| Using `ArrayList` for the stack and doing `remove`/`add` at the beginning | O(n²) due to shifting |
| Not resetting the count of equal elements | Over‑counting subarrays |

---

## 🧠 Key Insight

We enumerate **the right boundary** of every subarray.  
While we move the right boundary `i` from left to right, we keep a **monotonically decreasing** stack of pairs `(value, count)`:

* **Value** – the actual number at that stack level.  
* **Count** – how many consecutive positions (to the left) have this value as the maximum so far.

When we encounter a new element `a = nums[i]`:

1. **Pop** all stack elements whose value is **smaller** than `a`.  
   Those elements cannot be the maximum for any subarray ending at `i` because `a` is larger.
2. If the stack is **empty** or its top value is **different** from `a`, **push** `(a, 0)`.  
   This starts a new block of equal elements.
3. **Increment** the count of the top element: `stack.top.count += 1`.  
   That count now represents how many subarrays ending at `i` have `a` as the maximum and start with this `a` (or a smaller value that has been popped).
4. **Add** that count to the global answer.

Why does this work?  
All subarrays ending at position `i` that have `a` as the maximum must start somewhere **after** the last element larger than `a`.  
All elements after that point but **before** `i` are **≤ a**; the stack guarantees that we only keep values ≤ a, and consecutive equal values are grouped, so we can count them in O(1) per step.

---

## 📦 Code Snippets

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.  
All three share the same logic and run in `O(n)` time.

> ⚠️ **Important** – use `long` (`int64`) for the result.

---

### Java (Java 17)

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class Solution {
    public long numberOfSubarrays(int[] nums) {
        // Stack element: {value, count}
        Deque<int[]> stack = new ArrayDeque<>();
        long res = 0;

        for (int a : nums) {
            // 1. Remove smaller elements
            while (!stack.isEmpty() && stack.peek()[0] < a) {
                stack.pop();
            }

            // 2. Push new element if needed
            if (stack.isEmpty() || stack.peek()[0] != a) {
                stack.push(new int[]{a, 0});
            }

            // 3. Increment count and add to result
            stack.peek()[1]++;          // count++
            res += stack.peek()[1];
        }
        return res;
    }
}
```

---

### Python (Python 3.11)

```python
from typing import List

class Solution:
    def numberOfSubarrays(self, nums: List[int]) -> int:
        stack: List[List[int]] = []   # each element is [value, count]
        res = 0

        for a in nums:
            # Pop smaller values
            while stack and stack[-1][0] < a:
                stack.pop()

            # Push new block if needed
            if not stack or stack[-1][0] > a:
                stack.append([a, 0])

            # Increment count and accumulate
            stack[-1][1] += 1
            res += stack[-1][1]

        return res
```

---

### C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long numberOfSubarrays(vector<int>& nums) {
        // Stack element: pair<value, count>
        vector<vector<int>> st;      // stack stored as vector<value, count>
        long long res = 0;

        for (int a : nums) {
            // 1. Pop smaller elements
            while (!st.empty() && st.back()[0] < a) {
                st.pop_back();
            }

            // 2. Push new element if needed
            if (st.empty() || st.back()[0] > a) {
                st.push_back({a, 0});
            }

            // 3. Increment count and accumulate
            st.back()[1]++;           // count++
            res += st.back()[1];
        }
        return res;
    }
};
```

> In C++ we use a `vector<vector<int>>` to mimic the `(value, count)` pair.  
> The `back()` accessor gives constant‑time top‑of‑stack operations.

---

## ✅ Testing & Edge Cases

| Test | Input | Expected Output | Why |
|------|-------|-----------------|-----|
| 1 | `[3,3,3]` | `6` | Every subarray starts & ends with `3` and `3` is the maximum. |
| 2 | `[1,2,3]` | `3` | Only `[1,2]`, `[2,3]`, and `[3]` qualify. |
| 3 | `[3,2,1]` | `3` | Subarrays `[3]`, `[3,2]`, `[3,2,1]`. |
| 4 | `[1,1,1,1]` | `10` | `4*5/2` = 10. |
| 5 | `[10^9] * 100000` | `n*(n+1)/2` (≈ 5e9) | Ensure no overflow; `long` handles it. |

Run the following simple script to double‑check:

```python
sol = Solution()
print(sol.numberOfSubarrays([3,3,3]))          # 6
print(sol.numberOfSubarrays([1,2,3,2,1]))     # 7
```

---

## 📈 Complexity Analysis

| Step | Cost | Reason |
|------|------|--------|
| Scan `nums` once | `O(n)` | Linear traversal |
| Stack operations | `O(n)` overall | Each element is pushed and popped at most once |
| Final complexity | **O(n) time**, **O(n) space** | Meets the 100k‑length limit easily |

The only `long` addition that could overflow is the final answer; `long` can store up to `9×10¹⁸`, which is far above the maximum possible subarray count (`≈ 5×10⁹` for `n = 10⁵`).

---

## 💡 Interview Tips

1. **Explain the stack invariant**: *“Stack values are in decreasing order, and each pair holds how many subarrays end at the current position that start with this value.”*
2. **Why pop smaller elements?** – “A larger element on the right side dominates, so smaller ones cannot be maxima.”
3. **Why group equal values?** – Avoids re‑counting each occurrence separately.
4. **Check for overflows** – Mention using `long` / `int64`.
5. **Discuss edge cases** – All equal values, single element, or strictly monotonic arrays.

> *Pro tip:* When you’re stuck, try writing a **brute‑force** version for `n ≤ 10` and compare outputs. That will reveal subtle bugs early.

---

## 🎯 Takeaway

The monotonic stack gives a clean, linear solution that’s:

* **Fast** – handles the maximum input size.  
* **Memory‑friendly** – only the stack matters.  
* **Interview‑ready** – you can explain the invariant in less than 5 minutes.

Avoid the pitfalls of overflow, equal‑value handling, and brute‑force pitfalls. Show that you understand the core pattern, and you’ll impress hiring managers who love reusable, elegant code.

---

## 📣 SEO Keywords

- **LeetCode 3113**
- **subarray maximum boundary**
- **count subarrays maximum**
- **monotonic stack algorithm**
- **O(n) solution**
- **Java Python C++ interview**
- **coding interview patterns**
- **job interview tips**
- **software engineer coding challenge**

---

### Final Thought

A problem that at first glance feels like a counting nightmare actually boils down to a beautiful monotonic stack dance. Master this pattern and you’ll not only crush LeetCode 3113, but you’ll also unlock a powerful tool for your next technical interview. Happy coding! 🚀