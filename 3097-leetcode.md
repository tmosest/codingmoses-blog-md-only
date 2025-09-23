---
title: LeetCode 3097. Shortest Subarray With OR at Least K II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌  Shortest Subarray With OR at Least K (LeetCode 3097)

**Keywords** – LeetCode 3097, shortest subarray OR, bitwise OR, sliding window, Java solution, Python solution, C++ solution, interview algorithm, coding interview

---

### 1️⃣ Problem Recap

Given an array `nums` of non‑negative integers and an integer `k`, find the length of the *shortest* non‑empty subarray whose bitwise **OR** is **≥ k**.  
Return `-1` if no such subarray exists.

*Constraints*

| | |
|---|---|
| `1 ≤ nums.length ≤ 2 × 10⁵` | `0 ≤ nums[i] ≤ 10⁹` |
| `0 ≤ k ≤ 10⁹` | |

The problem appears “easy” at first glance, but the constraints make a naïve O(n²) solution impossible.

---

### 2️⃣ Why the Naïve Approach Fails

The straightforward idea is:

```
for each start index i
    or = 0
    for each end index j ≥ i
        or |= nums[j]
        if or >= k: record j-i+1 and break
```

Time complexity: **O(n²)** → 4 × 10¹⁰ operations for the worst case.  
Memory usage is fine, but the runtime is far too high for 2 × 10⁵ elements.

---

### 3️⃣ The Winning Strategy – Sliding Window + Bit Counts

#### 🔍 Intuition

When we extend the window to the right, the OR of the window can only **increase** (bits can only turn from 0 → 1).  
When we shrink from the left, bits can disappear only if the bit was unique to the element we’re removing.

Because the numbers fit in 32 bits (`≤ 10⁹ < 2³¹`), we can maintain an array `cnt[32]` where  
`cnt[i]` = how many numbers in the current window have the `i`‑th bit set.

From `cnt` we can reconstruct the current OR in O(32) by setting a bit if `cnt[i] > 0`.

#### 🧪 Algorithm

```
left = 0
ans  = INF
cnt[32] = {0}

for right in 0 .. n-1
    add bits of nums[right] to cnt

    while left <= right and current_OR(cnt) >= k
        ans = min(ans, right-left+1)
        remove bits of nums[left] from cnt
        left++

return ans == INF ? -1 : ans
```

*Adding / removing bits* is simply incrementing / decrementing the counts for each bit that is set in the number.

#### 📈 Complexity

- Each element is added once and removed once → **O(n)** updates.
- Each update touches at most 32 bits → **O(32 · n) ≈ O(n)**.
- Reconstruction of the OR in the `while` loop is O(32), constant time.

Memory usage: **O(32)** → trivial.

---

### 4️⃣ Edge‑Case Checklist

| Case | Why it works | Notes |
|------|--------------|-------|
| `k == 0` | OR of any single element is ≥ 0 | Shortest subarray is always length 1 |
| No subarray reaches `k` | `ans` stays `INF` | Return `-1` |
| `nums` all zeros | OR never increases | Same as above |
| `nums` length 1 | Single element checked once | Works naturally |

---

### 5️⃣ Code Implementations

> **Tip:** The code snippets below are production‑ready. They use the same clear helper functions for adding/removing bits and reconstructing the OR.

#### 5.1 Java

```java
import java.util.*;

class Solution {
    public int minimumSubarrayLength(int[] nums, int k) {
        int[] cnt = new int[32];
        int left = 0, ans = Integer.MAX_VALUE;

        for (int right = 0; right < nums.length; right++) {
            addBits(cnt, nums[right], 1);

            while (left <= right && currentOr(cnt) >= k) {
                ans = Math.min(ans, right - left + 1);
                addBits(cnt, nums[left], -1);
                left++;
            }
        }
        return ans == Integer.MAX_VALUE ? -1 : ans;
    }

    private void addBits(int[] cnt, int num, int delta) {
        for (int i = 0; i < 32; i++) {
            if (((num >> i) & 1) == 1) cnt[i] += delta;
        }
    }

    private int currentOr(int[] cnt) {
        int val = 0;
        for (int i = 0; i < 32; i++) {
            if (cnt[i] > 0) val |= (1 << i);
        }
        return val;
    }
}
```

#### 5.2 Python

```python
from typing import List

class Solution:
    def minimumSubarrayLength(self, nums: List[int], k: int) -> int:
        cnt = [0] * 32
        left, ans = 0, float('inf')

        for right, num in enumerate(nums):
            self._add_bits(cnt, num, 1)

            while left <= right and self._current_or(cnt) >= k:
                ans = min(ans, right - left + 1)
                self._add_bits(cnt, nums[left], -1)
                left += 1

        return -1 if ans == float('inf') else ans

    @staticmethod
    def _add_bits(cnt: List[int], num: int, delta: int) -> None:
        for i in range(32):
            if (num >> i) & 1:
                cnt[i] += delta

    @staticmethod
    def _current_or(cnt: List[int]) -> int:
        val = 0
        for i, c in enumerate(cnt):
            if c:
                val |= (1 << i)
        return val
```

#### 5.3 C++

```cpp
class Solution {
public:
    int minimumSubarrayLength(vector<int>& nums, int k) {
        array<int, 32> cnt{};          // all zeros initially
        int left = 0, ans = INT_MAX;

        for (int right = 0; right < (int)nums.size(); ++right) {
            addBits(cnt, nums[right], 1);

            while (left <= right && currentOr(cnt) >= k) {
                ans = min(ans, right - left + 1);
                addBits(cnt, nums[left], -1);
                ++left;
            }
        }
        return (ans == INT_MAX) ? -1 : ans;
    }

private:
    void addBits(array<int, 32>& cnt, int num, int delta) {
        for (int i = 0; i < 32; ++i) {
            if ((num >> i) & 1)
                cnt[i] += delta;
        }
    }

    int currentOr(const array<int, 32>& cnt) {
        int val = 0;
        for (int i = 0; i < 32; ++i)
            if (cnt[i] > 0)
                val |= (1 << i);
        return val;
    }
};
```

---

### 6️⃣ The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | O(n) → passes easily | Naïve O(n²) would TLE | Misusing bitwise operations (e.g., rebuilding OR with a loop over 64 bits) could still be **slow** |
| **Space** | Constant (32 ints) | Extra `or` array not needed | Forgetting to reset counts leads to **incorrect OR** |
| **Implementation** | Clear helpers (`addBits`, `currentOr`) → readability | None | Over‑engineering (e.g., segment trees) for a 32‑bit problem |
| **Portability** | Works in Java, Python, C++ | Some languages need 64‑bit integers | In Java, `1 << 31` is negative but still correct – may confuse newcomers |
| **Maintainability** | Reusable bit‑count logic | None | Using `unordered_map<int,int>` per bit would be **overkill** |

---

### 7️⃣ How to Land That Interview

1. **Show the Thought Process** – Start by explaining why a naïve double loop is too slow.  
2. **Explain OR Monotonicity** – Interviewers love seeing that you understand the core property that makes a sliding window viable.  
3. **Highlight Constant‑Time Bit Operations** – Mention that 32 bits → constant, so the “inner” loop is still O(1).  
4. **Test Edge Cases** – Ask the interviewer to think about `k == 0`, all zeros, etc.  
5. **Mention Space** – 32 counters are negligible; you can even compress them into a 32‑bit integer if you want to be fancy.  
6. **Write Clean Code** – As shown above, use helper functions and avoid magic numbers.  

> *“Why are you adding and removing bits? Isn’t OR associative?”*  
> Answer: “It’s associative, but because we need to detect when the OR drops below k while shrinking, we track how many elements contribute to each bit. That lets us know when a bit disappears.”

---

### 7️⃣ Final Takeaway

- **LeetCode 3097** is a textbook example of how a simple observation (the OR only grows) lets you convert an O(n²) brute force into an O(n) sliding‑window algorithm.  
- The key insight is to keep a **frequency array of bits** instead of recomputing the OR from scratch.  
- The solution is *language‑agnostic*: it works in Java, Python, C++ and any other language with 32‑bit integers.

> **Pro‑tip for recruiters**: Mention that your solution uses *constant‑time bit operations*, making it *super fast* even on the upper limits of the constraints. That’s exactly the kind of “performance‑aware” thinking interviewers are looking for.

Happy coding! 🚀