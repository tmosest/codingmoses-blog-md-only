---
title: LeetCode 665. Non-decreasing Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 665 – Non‑Decreasing Array  
> **Difficulty:** Medium  
> **Signature:** `public boolean checkPossibility(int[] nums)`  

---

### Problem Recap  
You’re given an integer array `nums`. You may change **at most one element** to any value you want. After the change, the array must become **non‑decreasing** (`nums[i] ≤ nums[i+1]` for all valid `i`).  

Return `true` if it’s possible, otherwise `false`.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[4,2,3]` | `true` | Change `4` → `1`. |
| `[4,2,1]` | `false` | No single change can fix both decreases. |

**Constraints**

| n | 1 ≤ n ≤ 10⁴ |
| nums[i] | –10⁵ ≤ nums[i] ≤ 10⁵ |

---

## 🚀 Solution Overview

The array is **only “almost” sorted** – at most one violation `nums[i] < nums[i‑1]` is allowed.  
The crux is to decide *how* to fix a violation:

* **Option 1** – Lower `nums[i‑1]` to `nums[i]`.
* **Option 2** – Raise `nums[i]` to `nums[i‑1]`.

Both may be valid depending on the surrounding numbers (`nums[i‑2]` and `nums[i+1]`).  
If you hit a second violation or a situation where *neither* option keeps the sequence sorted, the answer is `false`.

The algorithm is a single linear scan, O(n) time and O(1) space.

---

## 📝 Pseudocode

```
for i from 1 to n-1
    if nums[i] < nums[i-1]          // found a decrease
        if we already saw a decrease
            return false
        if i>1 and i<n-1 and nums[i-2] > nums[i] and nums[i+1] < nums[i-1]
            return false            // both sides would still be wrong
        mark that we saw one decrease
return true
```

The condition `nums[i-2] > nums[i]` means we would have to *raise* `nums[i]` to keep the left side sorted, while `nums[i+1] < nums[i-1]` means we would have to *lower* `nums[i-1]` to keep the right side sorted.  
If both are true, neither choice works → impossible.

---

## 📄 Code

### Java

```java
class Solution {
    public boolean checkPossibility(int[] nums) {
        for (int i = 1, err = 0; i < nums.length; i++) {
            if (nums[i] < nums[i - 1]) {
                // Second violation
                if (err++ > 0) return false;
                // YABY situation – cannot fix
                if (i > 1 && i < nums.length - 1 &&
                    nums[i - 2] > nums[i] && nums[i + 1] < nums[i - 1]) {
                    return false;
                }
            }
        }
        return true;
    }
}
```

### Python 3

```python
class Solution:
    def checkPossibility(self, nums: list[int]) -> bool:
        err = 0
        for i in range(1, len(nums)):
            if nums[i] < nums[i - 1]:
                if err or (i > 1 and i < len(nums) - 1 and
                           nums[i - 2] > nums[i] and nums[i + 1] < nums[i - 1]):
                    return False
                err = 1
        return True
```

### C++ (C++17)

```cpp
class Solution {
public:
    bool checkPossibility(vector<int>& nums) {
        for (int i = 1, err = 0; i < nums.size(); ++i) {
            if (nums[i] < nums[i - 1]) {
                if (err++ || (i > 1 && i < nums.size() - 1 &&
                              nums[i - 2] > nums[i] && nums[i + 1] < nums[i - 1]))
                    return false;
            }
        }
        return true;
    }
};
```

All three implementations run in **O(n)** time, **O(1)** auxiliary space, and match the greedy strategy described above.

---

## 📚 Blog Post: “The Good, The Bad, and The Ugly of LeetCode 665”

### Title (SEO‑Optimized)  
**How to Crack LeetCode 665 – Non‑Decreasing Array: The Good, The Bad, & The Ugly (Java, Python, C++)**

### Meta Description  
Master LeetCode 665 in minutes! Learn the intuitive greedy solution, pitfalls to avoid, and full Java, Python, C++ code. Boost your coding interview prep and land your dream tech job.

---

### Introduction  
* LeetCode’s “Almost Sorted” problems are a sweet spot for interviewers.  
* Problem 665 asks: **Can a single element fix an almost‑sorted array?**  
* We’ll walk through the reasoning, edge‑case traps, and a production‑ready solution in Java, Python, and C++.

> *Keywords:* LeetCode 665, Non‑Decreasing Array, interview coding, algorithmic problem, greedy solution, Java coding, Python coding, C++ coding, data‑structures, interview prep.

---

## 📌 The Good

| What’s Great | Why it Helps |
|--------------|--------------|
| **Clear Input Constraints** | Easy to reason about time and space limits. |
| **Single Pass Greedy** | A classic interview favorite – shows you can think in O(n) time. |
| **Elegant Edge‑Case Check** | The `nums[i-2] > nums[i] && nums[i+1] < nums[i-1]` condition is a one‑liner that captures all failure modes. |
| **Language‑agnostic** | Same logic in Java, Python, and C++; shows language versatility. |

### Takeaway  
A clean, O(n) greedy solution is often the *most interview‑friendly* because it demonstrates:

* Efficient thinking  
* Attention to detail with edge cases  
* Ability to write concise, bug‑free code

---

## 📉 The Bad

| Issue | How to Fix It |
|-------|---------------|
| **Misreading “modify at most one element”** | It isn’t “swap two elements”; you can change to any value. Forgetting this leads to wrong solutions that try to *swap* instead of *set*. |
| **Over‑optimistic “fix left or right”** | Simply lowering `nums[i-1]` or raising `nums[i]` may still break the next pair. Always check `nums[i-2]` and `nums[i+1]`. |
| **Counting violations incorrectly** | Some newbies use `i++` incorrectly and end up missing the second violation. |
| **Complex “in‑place” modifications** | Changing the array while iterating can obscure the logic. Prefer a *virtual* prev value or just keep a counter. |

**Common Pitfall Example**

```python
# Wrong approach: always set nums[i-1] = nums[i]
for i in range(1, len(nums)):
    if nums[i] < nums[i-1]:
        nums[i-1] = nums[i]
return True
```

This fails on `[3,4,2,3]`. The left side becomes `[3,4,2]` → still decreasing.

---

## 😈 The Ugly

1. **Misinterpreting “non‑decreasing” vs “strictly increasing”**  
   *Non‑decreasing* allows equality (`≤`). A subtle bug: using `<` everywhere will incorrectly reject arrays like `[1,1,1]`.

2. **Time‑out on large inputs**  
   If you write an O(n²) nested loop (e.g., trying all possible single edits), the solution will hit LeetCode’s 2 s limit on 10⁴‑element arrays.

3. **Unnecessary space usage**  
   Creating a copy of the array for every edit attempt doubles memory usage and slows down due to cache misses.

4. **Reading the wrong “answer” from the community**  
   Many blog posts claim “change `nums[i]` to `nums[i-1]`” blindly. That’s only correct when `nums[i-2] ≤ nums[i]`.  
   The “YABY” situation is the *real* ugly – both sides still broken if you pick the wrong side.

---

## 🎯 Interview Tips

| Tip | Why it Matters |
|-----|----------------|
| **Start with a clear definition of “violation.”** | Prevents confusion over `nums[i] < nums[i-1]`. |
| **Use a single counter (`modified` / `err`).** | Makes the “at most one change” rule explicit. |
| **Think about neighbors (`i-2`, `i+1`).** | The fix decision hinges on these two elements. |
| **Test edge cases first.** | `[1]`, `[1,1]`, `[2,1]`, `[3,2,1]` are quick sanity checks. |
| **Explain your logic in plain English before coding.** | Interviewers appreciate clear thought processes. |
| **Practice the “without modifying the array” version.** | Shows you can keep the array intact – a subtle skill interviewers love. |

---

## 📖 Summary

LeetCode 665 is a classic greedy problem that rewards:

* **Linear‑time thinking**  
* **Meticulous edge‑case handling**  
* **Cross‑language clarity**

The greedy solution above is the minimal‑overhead, production‑ready code that works in Java, Python, and C++. Master it, add it to your portfolio, and you’ll be ready to ace any “array‑smoothing” interview question!

--- 

**Happy coding!** 🚀  
*Feel free to drop your own take‑aways in the comments, or let me know if you’d like a deeper dive into dynamic‑programming variants.*