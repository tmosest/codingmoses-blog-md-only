---
title: LeetCode 1521. Find a Value of a Mysterious Function Closest to Target - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1521 – *Find a Value of a Mysterious Function Closest to Target*  
> **Language‑agnostic solution** | Java | Python | C++  
> **Blog** – The Good, The Bad & The Ugly (SEO‑friendly, interview‑ready)

---

### 📌 Problem Recap  

Given an integer array `arr` and a target `t`, we need to pick any sub‑array `[l, r]` (inclusive) and evaluate

```
func(arr, l, r) = arr[l] & arr[l+1] & … & arr[r]    // bitwise AND
```

Return the minimum possible value of `|func(arr, l, r) – t|`.

| Constraint | Value |
|------------|-------|
| 1 ≤ arr.length ≤ 10⁵ |  |
| 1 ≤ arr[i] ≤ 10⁶ |  |
| 0 ≤ target ≤ 10⁷ |  |

The naive O(n²) scan of all sub‑arrays is impossible for the given limits.

---

## 🧠 Core Insight – Bitwise AND is Monotone

For a fixed starting index `l`, extending the sub‑array to the right can **only turn 1‑bits into 0‑bits**.  
Therefore, the set of possible AND values for sub‑arrays ending at a particular index is *shrinking*.

This property allows us to keep, for each index, the *distinct* AND results of all sub‑arrays that end at that index.  
Because each new AND operation can at most keep the same value or reduce it, the number of distinct results is bounded by `O(log(max(arr)))` (≈ 20 for 10⁶).  
Hence we can process the array in linear time.

---

## 🔧 Algorithm (Set‑Based O(n log k) Solution)

```
ans = ∞
prev = {}            // AND values for sub‑arrays ending at previous index

for num in arr:
    curr = {num}     // sub‑array consisting only of num
    ans = min(ans, |num - target|)

    for val in prev:
        newVal = val & num
        curr.add(newVal)
        ans = min(ans, |newVal - target|)

    prev = curr
return ans
```

### Why it Works

* `prev` contains **all distinct** AND results of sub‑arrays that end at the previous element.
* For the current element `num`, every sub‑array ending here is either:
  * just `num` itself,
  * or a previous sub‑array extended by `num` (`val & num`).
* Because AND is monotone, merging identical results keeps `curr` small.
* The global minimum difference `ans` is updated every time we generate a candidate value.

### Complexity

| | Time | Space |
|---|---|---|
| Total | **O(n log k)** (k ≈ number of bits, ≤ 20) | **O(log k)** |

With `n = 10⁵`, this easily fits into the limits.

---

## 🏆 Code Implementations

### 1️⃣ Java

```java
import java.util.*;

class Solution {
    public int closestToTarget(int[] arr, int target) {
        int ans = Integer.MAX_VALUE;
        Set<Integer> prev = new HashSet<>();

        for (int num : arr) {
            Set<Integer> curr = new HashSet<>();
            curr.add(num);
            ans = Math.min(ans, Math.abs(num - target));

            for (int val : prev) {
                int newVal = val & num;
                curr.add(newVal);
                ans = Math.min(ans, Math.abs(newVal - target));
            }
            prev = curr;
        }
        return ans;
    }
}
```

> **Why HashSet?**  
> It guarantees O(1) insert & lookup. Since the set size is tiny (≤ 20), overhead is negligible.

---

### 2️⃣ Python

```python
class Solution:
    def closestToTarget(self, arr: List[int], target: int) -> int:
        ans = float('inf')
        prev = set()

        for num in arr:
            curr = {num}
            ans = min(ans, abs(num - target))

            for val in prev:
                new_val = val & num
                curr.add(new_val)
                ans = min(ans, abs(new_val - target))

            prev = curr

        return ans
```

> **Pythonic touches**  
> - `set` comprehensions are fast for tiny sets.  
> - `float('inf')` is a clean initial value.

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int closestToTarget(vector<int>& arr, int target) {
        int ans = INT_MAX;
        unordered_set<int> prev;

        for (int num : arr) {
            unordered_set<int> curr;
            curr.insert(num);
            ans = min(ans, abs(num - target));

            for (int val : prev) {
                int newVal = val & num;
                curr.insert(newVal);
                ans = min(ans, abs(newVal - target));
            }
            prev = move(curr);          // reuse memory
        }
        return ans;
    }
};
```

> **Move semantics**  
> `move(curr)` avoids copying the small set, keeping the routine fast.

---

## 📚 Blog Article – The Good, The Bad & The Ugly

> **Title**: *LeetCode 1521 – Mastering the Mysterious Function with Bit Manipulation*  
> **Meta‑Description**: Solve LeetCode 1521 in O(n log k) using bitwise AND. Java, Python, C++ codes + interview‑ready insights.  
> **Keywords**: LeetCode 1521, closest to target, bit manipulation, subarray AND, interview algorithm, O(n log k) solution, job interview coding, Python, Java, C++.

---

### 1️⃣ The Good – Why This Approach Rocks

1. **Linear Time** – Handles 10⁵ elements comfortably.  
2. **Space‑Efficient** – Only a handful of values per index.  
3. **Conceptual Elegance** – Uses the monotonic property of AND; no heavy data structures.  
4. **Cross‑Language** – Easy to translate between Java, Python, and C++.  
5. **Interview Friendly** – Demonstrates understanding of bitwise operations, dynamic programming, and optimization.

---

### 2️⃣ The Bad – Edge Cases & Pitfalls

| Issue | Fix |
|-------|-----|
| **Large Numbers** – 32‑bit overflow in languages like C++ if using signed ints | Use `int64_t` or cast to `long long` during intermediate AND, but AND of positive numbers stays within 32‑bit range. |
| **Empty Array** – Problem guarantees at least one element, but defensive coding helps | Add guard clause if `arr.empty()` → return `target`. |
| **Precision in abs** – In Java `Math.abs(Integer.MIN_VALUE)` overflows | Use `Math.abs((long)val - target)` or `Math.abs((long)val - (long)target)`. |
| **Large `target`** – > 2³¹ may overflow in `int` abs | Use `long` for diff calculation. |

---

### 3️⃣ The Ugly – When the Simple Set Fails

If `arr` contains all ones (e.g., 1,000,000 repeated), each sub‑array AND stays the same.  
The set size remains 1, which is fine.  
But if numbers are highly varied, the intermediate set can momentarily grow (still ≤ log k).  
In worst‑case contrived data, `prev` can approach `log₂(10⁶) ≈ 20`, still trivial.

**What if you were forced to use a Segment Tree?**  
- Complexity: O(n log n log max)  
- Memory: O(n)  
- Implementation overhead: harder to explain in an interview.  
The set method is preferable for interview scenarios.

---

### 4️⃣ SEO‑Optimized Take‑aways

- **Keywords**: LeetCode 1521 solution, closest to target algorithm, bitwise AND subarray, interview coding problem, O(n log k) approach, Java/Python/C++ code, bit manipulation interview.
- **Readability**: Use clear headings (`The Good`, `The Bad`, `The Ugly`) and bullet points for quick scanning.
- **CTA**: Invite readers to try the code on LeetCode, to subscribe for more interview prep, or to contact you for a technical interview coaching session.

---

## 🎯 Final Checklist for Your Portfolio

| ✅ | Item |
|----|------|
| ✅ | Java solution in a class `Solution` with method `closestToTarget`. |
| ✅ | Python function with type hints. |
| ✅ | C++ implementation using unordered_set. |
| ✅ | Blog article covering algorithm, complexity, edge cases, SEO. |
| ✅ | Link to LeetCode problem and solution discussion. |
| ✅ | GitHub repository link (optional). |

---

### 🚀 Wrap‑Up

You now have a production‑ready, interview‑grade solution to LeetCode 1521 in three popular languages. The blog post not only showcases your technical depth but also positions you for top‑tier software engineering roles. Happy coding!