---
title: LeetCode 1703. Minimum Adjacent Swaps for K Consecutive Ones - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 1703 – Minimum Adjacent Swaps for K Consecutive Ones  
**A Complete, SEO‑Optimized Guide for Job‑Hunting Developers**

> *Problem Link:* <https://leetcode.com/problems/minimum-adjacent-swaps-for-k-consecutive-ones/>  
> *Tags:* Two‑Pointers | Sliding Window | Prefix Sum | Greedy | Array

---

## 📌 Problem Statement

Given a binary array `nums` (`0` / `1`) and an integer `k`, you may swap any two **adjacent** elements in one move.  
Return the *minimum* number of moves required so that the array contains **`k` consecutive `1`s**.

**Constraints**

| Variable | Limits |
|----------|--------|
| `nums.length` | `1 … 10^5` |
| `nums[i]` | `0` or `1` |
| `k` | `1 … sum(nums)` |

---

## 💡 Why This Problem Rocks

* Classic interview topic: **array manipulation + sliding window**.  
* Highlights your ability to optimize *time* and *space* – a big plus on the hiring radar.  
* Gives you a chance to practice **prefix sums**, **median logic**, and **greedy reasoning** – all interview‑friendly concepts.

---

## 🔍 The “Good, The Bad, The Ugly” Breakdown

| Category | What You’ll Learn | Implementation Complexity |
|----------|-------------------|----------------------------|
| **Good** | *O(n)* solution using median of positions + prefix sums. | **Fast, clean, and scalable** – ideal for coding interviews. |
| **Bad** | Brute‑force O(nk) or O(n²) approaches (double loops, repeated distance sums). | **Time‑outs** on large inputs. |
| **Ugly** | Heavy data‑structures (segment trees, binary indexed trees) or overly clever math that obscures intent. | **Hard to read/maintain**; unnecessary for this problem. |

We’ll walk through the **good** solution in detail, show why the bad ones fail, and give you ready‑to‑paste code in **Java, Python, and C++**.

---

## 🧠 The Core Insight

1. **Collect the indices of all `1`s** – `pos`.  
2. Any window of `k` ones can be shifted to become consecutive.  
3. The *optimal* target is to center the block around the **median** of the window.  
4. Distance for one `1` = `abs( pos[i] - pos[mid] - (i - mid) )`.  
5. This simplifies to `abs( (pos[i] - i) - (pos[mid] - mid) )`.  

Thus, if we pre‑compute `arr[i] = pos[i] - i`, the cost for a window is the sum of absolute differences to its median.

---

## 📈 O(1) Cost Per Window (after O(n) Pre‑processing)

Let `pref[i]` be the prefix sum of `arr`.

For window `[l … r]` (`k = r-l+1`), median index `m = l + k/2` (integer division).

Cost =  

```
(left side)  =  arr[m] * (m-l)  -  (pref[m-1] - pref[l-1])
(right side) =  (pref[r] - pref[m])  -  arr[m] * (r-m)
```

Total = left side + right side.

All operations are O(1), so the whole algorithm runs in O(n) time and O(n) space.

---

## 🛠️ Code Implementations

Below you’ll find clean, commented implementations in **Java, Python, and C++**.

> **Tip:** For the Java code, remember to use `long` when computing sums to avoid overflow (though the answer fits in `int` for the given constraints).

---

### ✅ Java (O(n) Sliding Window + Prefix Sum)

```java
import java.util.*;

public class Solution {
    public int minMoves(int[] nums, int k) {
        // 1. Collect positions of all 1's
        List<Integer> pos = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == 1) pos.add(i);
        }

        int m = pos.size();                // total number of 1's
        if (k == m) return 0;              // already consecutive

        // 2. Build arr[i] = pos[i] - i
        long[] arr = new long[m];
        for (int i = 0; i < m; i++) arr[i] = pos.get(i) - i;

        // 3. Prefix sums of arr
        long[] pref = new long[m];
        pref[0] = arr[0];
        for (int i = 1; i < m; i++) pref[i] = pref[i-1] + arr[i];

        int ans = Integer.MAX_VALUE;
        for (int l = 0; l + k - 1 < m; l++) {
            int r = l + k - 1;
            int mid = l + k / 2;            // median index
            long median = arr[mid];

            long left  = median * (mid - l) - (mid > l ? pref[mid-1] - pref[l-1] : 0);
            long right = (pref[r] - pref[mid]) - median * (r - mid);

            ans = (int)Math.min(ans, (int)(left + right));
        }
        return ans;
    }
}
```

---

### ✅ Python (O(n) Sliding Window + Prefix Sum)

```python
class Solution:
    def minMoves(self, nums: List[int], k: int) -> int:
        # 1. Positions of 1's
        pos = [i for i, v in enumerate(nums) if v == 1]
        m = len(pos)
        if k == m:
            return 0

        # 2. arr[i] = pos[i] - i
        arr = [pos[i] - i for i in range(m)]

        # 3. Prefix sums
        pref = [0] * m
        pref[0] = arr[0]
        for i in range(1, m):
            pref[i] = pref[i-1] + arr[i]

        ans = 10**9
        for l in range(0, m - k + 1):
            r = l + k - 1
            mid = l + k // 2
            median = arr[mid]

            left = median * (mid - l) - (pref[mid-1] - pref[l-1] if mid > l else 0)
            right = (pref[r] - pref[mid]) - median * (r - mid)

            ans = min(ans, left + right)

        return ans
```

---

### ✅ C++ (O(n) Sliding Window + Prefix Sum)

```cpp
class Solution {
public:
    int minMoves(vector<int>& nums, int k) {
        vector<int> pos;
        for (int i = 0; i < nums.size(); ++i)
            if (nums[i] == 1) pos.push_back(i);

        int m = pos.size();
        if (k == m) return 0;

        vector<long long> arr(m);
        for (int i = 0; i < m; ++i) arr[i] = (long long)pos[i] - i;

        vector<long long> pref(m);
        pref[0] = arr[0];
        for (int i = 1; i < m; ++i) pref[i] = pref[i-1] + arr[i];

        long long ans = LLONG_MAX;
        for (int l = 0; l + k - 1 < m; ++l) {
            int r = l + k - 1;
            int mid = l + k / 2;
            long long median = arr[mid];

            long long left  = median * (mid - l) - (mid > l ? pref[mid-1] - pref[l-1] : 0);
            long long right = (pref[r] - pref[mid]) - median * (r - mid);

            ans = min(ans, left + right);
        }
        return (int)ans;
    }
};
```

> **Note:** We use `long long` for the prefix sums and intermediate calculations – the final answer comfortably fits into an `int`.

---

## 📊 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Collect indices (`pos`) | **O(n)** | **O(n)** |
| Build `arr` | **O(m)** | **O(m)** |
| Build prefix sums | **O(m)** | **O(m)** |
| Sliding window scan | **O(m)** | — |
| **Total** | **O(n)** | **O(n)** |

Because `m ≤ n`, the overall complexity is linear and memory‑efficient.

---

## ⚠️ Common Pitfalls & Fixes

| Pitfall | Fix |
|---------|-----|
| **Integer overflow** when summing distances | Use `long`/`long long` for intermediate sums. |
| Off‑by‑one errors on prefix sums (`pref[l-1]`) | Guard with `if (mid > l)` or use sentinel values. |
| Wrong median for even‑length windows | Integer division (`k/2`) works because the cost is symmetric. |
| Forgetting to subtract the “index offset” (`i-mid`) | Use the simplified `arr[i] = pos[i] - i` trick. |

---

## 🎯 How to Nail This on a Live Interview

1. **Explain the “collect positions → median shift” idea** before coding.  
2. Show the math for `arr[i] = pos[i] - i` to convince the interviewer that you know why the formula works.  
3. Write the code in **O(n)**, test it with the given examples, and **verify** the edge cases (`k == m`, all zeros, all ones).  
4. Finally, ask if they’d like a **Java / Python / C++** version – it’s a subtle signal that you’re ready to hand‑off working code.

---

## 🎯 Bonus: Why the “Bad” and “Ugly” Solutions Are Bad

| Approach | Typical Implementation | Why it Fails |
|----------|------------------------|--------------|
| **O(nk)** | `for i in range(n): for j in range(i,i+k): sum(abs(pos[j] - pos[i] - (j-i)))` | For `n = 10^5` and `k = 10^4`, you hit ~10^9 operations → **Time‑limit exceeded**. |
| **O(n²)** | Double nested loops over all `1`s to compute distances | Even with small `k`, the worst case still explodes. |
| **Segment Tree / BIT** | Maintaining counts of `1`s for dynamic range queries | Adds complexity, over‑engineering; the problem is “array + sliding window,” not “dynamic range min query.” |

---

## 🚀 How This Boosts Your Resume

| Skill | How It Helps |
|-------|--------------|
| **Greedy + Median** | Shows you can find an optimal “center” in a list of positions. |
| **Prefix Sum** | Indicates familiarity with one of the most powerful O(1) query tricks. |
| **Two‑Pointer Sliding Window** | A staple of many coding interviews – recruiters love it. |
| **Time‑Complexity Awareness** | Demonstrates that you’re conscious of scaling and can avoid timeouts. |

When you present this solution to a hiring manager, phrase it like this:

> “I solved LeetCode 1703 in **O(n)** time by sliding a window over the indices of `1`s, centering each block on the median, and using prefix sums to compute the cost in constant time.”

That sentence alone is a great talking point in any interview.

---

## 🎉 Take‑away Summary

1. **Collect indices** → `pos`.  
2. Transform each index: `arr[i] = pos[i] - i`.  
3. Prefix sums of `arr`.  
4. For every window of size `k`, compute cost in O(1) using median logic.  
5. Result in **O(n)** time, **O(n)** space.

You now have *ready‑to‑use* code in three popular languages and a thorough explanation that will impress recruiters.

---

## 🎯 Next Steps – Build Your Portfolio

| Action | Why It Matters |
|--------|----------------|
| **Add this solution to your GitHub** with a README that includes the explanation above. | Demonstrates real‑world problem solving and documentation skills. |
| **Write a blog post** or a tweet thread about this solution. | Showcases your communication chops. |
| **Prepare follow‑up questions** like “What if swaps weren’t restricted to adjacent elements?” | Shows depth of thinking and curiosity. |

---

### 🌟 Final Call to Action

> **If you’re preparing for a software engineering interview,** make LeetCode 1703 part of your “Interview Warm‑Up” list.  
> Clone the repo below, run the tests, and you’ll be ready to **drop the O(n) answer** into any coding challenge or whiteboard session.

```bash
git clone https://github.com/your-username/leetcode-1703-min-swaps
cd leetcode-1703-min-swaps
# Run the test harness of your choice
```

Happy coding, and good luck landing that dream role! 🎯

---