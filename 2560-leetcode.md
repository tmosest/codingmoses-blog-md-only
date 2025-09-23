---
title: LeetCode 2560. House Robber IV - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📘 The Complete, SEO‑Optimized Guide to **House Robber IV (LeetCode 2560)**  
*From “The Good” to “The Ugly” – All the Code, Theory, and Interview Tips You Need*

---

### 1️⃣ Problem Recap

> **House Robber IV**  
>  *Medium – 2560*  
> 
> We have `n` houses on a street.  
> `nums[i]` is the amount of money in house `i`.  
> A robber can never rob two adjacent houses.  
> The *capability* of the robber is the **maximum** amount taken from a single house among all the houses he robbed.  
>  
> **Goal**: Rob **at least `k` houses** (there’s always a feasible way) and return the **minimum possible capability**.

---

### 2️⃣ The Core Idea – Binary Search on the Answer

*Why binary search?*  
The answer is a value between `1` and `max(nums)`.  
If we can check whether a particular `mid` (the candidate capability) is feasible, we can decide which half of the search space to keep.

#### Feasibility Test (`canRob`)

- We scan the array from left to right.
- When we encounter a house with `nums[i] ≤ mid`, we *rob* it and skip the next house (because adjacent houses are forbidden).
- Count how many houses we robbed.
- If the count is at least `k`, `mid` is **good**; otherwise it’s **bad**.

This greedy scan guarantees the *maximum* number of houses we can rob with capability `mid`.  
If even that maximum is `< k`, no arrangement with capability `mid` can satisfy the requirement.

#### Complexity

| Step | Time | Space |
|------|------|-------|
| Feasibility check | `O(n)` | `O(1)` |
| Binary search over `[1, max(nums)]` | `O(log(max(nums)))` iterations | `O(1)` |

Total: `O(n log(max(nums)))` time, `O(1)` auxiliary space.

---

### 3️⃣ The Code

Below are three complete, idiomatic solutions – **Java**, **Python**, and **C++** – ready to paste into LeetCode’s online editor.

> **Tip** – The LeetCode environment only requires the `Solution` class and the method signature.  
> Nothing else (no `main` function) is needed.

---

#### ⚙️ Java

```java
class Solution {
    // Feasibility check: can we rob at least k houses with capability <= mid?
    private boolean canRob(int[] nums, int mid, int k) {
        int count = 0;
        for (int i = 0; i < nums.length; ) {
            if (nums[i] <= mid) {
                count++;          // rob this house
                i += 2;           // skip the adjacent house
            } else {
                i++;              // skip this house only
            }
        }
        return count >= k;
    }

    public int minCapability(int[] nums, int k) {
        int left = 1, right = 0;
        for (int v : nums) right = Math.max(right, v);   // max(nums)

        int ans = right;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (canRob(nums, mid, k)) {
                ans = mid;
                right = mid - 1;   // try to lower the capability
            } else {
                left = mid + 1;    // need a larger capability
            }
        }
        return ans;
    }
}
```

---

#### 🐍 Python

```python
class Solution:
    def can_rob(self, nums: list[int], limit: int, k: int) -> bool:
        """Return True if we can rob at least k houses with max amount <= limit."""
        cnt = 0
        i = 0
        n = len(nums)
        while i < n:
            if nums[i] <= limit:
                cnt += 1
                i += 2          # skip the next house
            else:
                i += 1
        return cnt >= k

    def minCapability(self, nums: list[int], k: int) -> int:
        lo, hi = 1, max(nums)
        ans = hi
        while lo <= hi:
            mid = (lo + hi) // 2
            if self.can_rob(nums, mid, k):
                ans = mid
                hi = mid - 1
            else:
                lo = mid + 1
        return ans
```

---

#### 🗜️ C++

```cpp
class Solution {
public:
    // Greedy feasibility test
    bool canRob(const vector<int>& nums, int limit, int k) {
        int cnt = 0, i = 0, n = nums.size();
        while (i < n) {
            if (nums[i] <= limit) {
                ++cnt;
                i += 2;           // skip adjacent house
            } else {
                ++i;
            }
        }
        return cnt >= k;
    }

    int minCapability(vector<int>& nums, int k) {
        int lo = 1, hi = *max_element(nums.begin(), nums.end()), ans = hi;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            if (canRob(nums, mid, k)) {
                ans = mid;
                hi = mid - 1;
            } else {
                lo = mid + 1;
            }
        }
        return ans;
    }
};
```

---

### 4️⃣ “The Good” – Why This Works

| ✔️ | Explanation |
|---|---|
| **Optimal** | Binary search guarantees the minimal capability. |
| **Linear Scan** | Feasibility check is just a single pass (`O(n)`). |
| **No DP Needed** | Unlike classic House Robber, we don’t need to keep track of two states; a greedy count suffices. |
| **Scalable** | Works comfortably for `n ≤ 10⁵` and `nums[i] ≤ 10⁹`. |
| **Clear Code** | No hidden magic – simple loops, straightforward conditions. |

---

### 5️⃣ “The Bad” – Common Pitfalls

| ⚠️ | Mistake | Fix |
|---|---|---|
| Binary search bounds off by one | Using `while (left < right)` and returning `left` may miss the exact answer if the last comparison is wrong. | Use `while (left <= right)` and keep an explicit `ans` variable. |
| Skipping adjacency incorrectly | Incrementing index by `2` **only** when robbing; otherwise increment by `1`. | Ensure the `else` clause doesn’t skip houses unnecessarily. |
| Using `int` for large sums | Not an issue here because we never sum values – but be wary if you change the problem. | Stick to `int` for capability values; they fit within 32‑bit. |
| Overflow in `mid` calculation | `mid = (left + right) / 2` can overflow if using `int`. | Use `mid = left + (right - left) / 2`. |
| Wrong return type on LeetCode | Returning `long` where `int` is expected can cause a type mismatch. | Return the same type as the signature (`int`). |

---

### 6️⃣ “The Ugly” – Edge Cases & Extra Considerations

1. **All houses have the same value**  
   *E.g.*, `nums = [5, 5, 5, 5]`, `k = 2`.  
   The answer is simply that value (5). Binary search still works but is overkill.

2. **Very large `k`**  
   `k` may equal `(n+1)/2` (the maximum number of non‑adjacent houses).  
   The greedy counter will pick every other house – still `O(n)`.

3. **Array of size 1**  
   If `k = 1`, the answer is `nums[0]`.  
   Our algorithm naturally handles this.

4. **Memory constraints**  
   The solution uses `O(1)` additional memory – perfect for interview scenarios where “constant space” is highlighted.

5. **Time limit in a language like Python**  
   Although `O(n log(max(nums)))` is fine for `n = 10⁵`, Python’s slower loops might push the time limit close to 2 seconds on LeetCode.  
   *Optimization*: Convert the input list to a `list[int]` (already the case) and avoid repeated function calls by inline logic if needed.

---

### 7️⃣ SEO‑Optimized Blog Draft

> **Title**: *Mastering LeetCode 2560 – House Robber IV: Binary Search, Greedy, and Interview‑Ready Code (Java, Python, C++)*  
> **Meta Description**: Learn the efficient solution to LeetCode House Robber IV using binary search. Get step‑by‑step explanations, code snippets in Java, Python, and C++, and interview tips.  

---

#### Introduction

If you’re preparing for coding interviews, **House Robber IV (LeetCode 2560)** is a must‑study problem. It tests your ability to combine binary search, greedy logic, and careful edge‑case handling. In this post we’ll walk through the problem, derive an optimal algorithm, discuss pitfalls, and deliver polished code in three of the most popular languages.

> **Why this problem matters**  
> - Shows proficiency with **binary search on answer** – a common interview pattern.  
> - Highlights greedy reasoning vs. dynamic programming.  
> - Tests your understanding of **non‑adjacent constraints** and maximum‑value optimization.

---

#### Problem Recap

(Include the full problem statement with examples as above)

---

#### Intuition & Strategy

(Explain the binary search on the maximum capability, the greedy feasibility check, and why it works. Use diagrams if possible.)

---

#### Pseudocode

```
canRob(nums, limit, k):
    count = 0
    i = 0
    while i < n:
        if nums[i] <= limit:
            count += 1
            i += 2        // skip next house
        else:
            i += 1
    return count >= k

minCapability(nums, k):
    lo = 1
    hi = max(nums)
    ans = hi
    while lo <= hi:
        mid = (lo + hi) // 2
        if canRob(nums, mid, k):
            ans = mid
            hi = mid - 1
        else:
            lo = mid + 1
    return ans
```

---

#### Full Implementations

(Insert the Java, Python, and C++ code blocks above.)

---

#### Complexity Analysis

- **Time**: `O(n log(max(nums)))`  
- **Space**: `O(1)` auxiliary

Explain the log factor comes from binary searching the answer, not the array.

---

#### Common Mistakes & How to Avoid Them

(Use the “Bad” table; emphasize off‑by‑one errors, incorrect adjacency handling.)

---

#### Edge Cases

(Summarize the “Ugly” table; provide quick sanity checks for minimal array sizes.)

---

#### Interview Tips

- **Explain your thought process**: “I’m binary searching the maximum possible robbery amount because the answer is monotonic.”  
- **Talk about the greedy counter**: why we can simply count houses rather than building DP states.  
- **Mention the difference** from the classic House Robber (DP with two states).  

---

#### Conclusion

(Encourage readers to practice variations: e.g., different adjacency rules, or adding a constraint on the total number of robbed houses.)

---

#### Further Reading

- Binary Search on Answer – <https://leetcode.com/articles/binary-search-on-answer/>  
- Greedy vs. DP – <https://www.geeksforgeeks.org/greedy-vs-dp/>

---

#### Final Words

By mastering this solution you’ll not only ace House Robber IV but also gain a solid template for a wide range of interview questions. Happy coding, and good luck on your next interview!

---

> **Keywords**: *LeetCode 2560, House Robber IV, binary search, greedy algorithm, coding interview, Java solution, Python solution, C++ solution, constant space, DP vs greedy*

---

### 8️⃣ Closing

You now have:

- A **clear, optimal algorithm**.  
- **Robust code** ready for LeetCode.  
- Knowledge of **pitfalls** to avoid during an interview.  

Feel free to tweak the snippets to match your own coding style, but the core logic will always hold. Good luck! 🚀

---


