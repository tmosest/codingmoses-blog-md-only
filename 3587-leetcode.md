---
title: LeetCode 3587. Minimum Adjacent Swaps to Alternate Parity - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Minimum Adjacent Swaps to Alternate Parity  
**The Good, The Bad, and The Ugly – A Complete Guide for Your Next Coding Interview**

> **Keywords**: Leetcode, interview, algorithm, parity, adjacent swaps, coding interview, Java, Python, C++, job interview, software engineering

---

## 1. Why This Problem Rocks Your Resume

- **Classic interview topic**: It tests your ability to reason about *parity*, *indices*, and *optimal movement*.
- **Time‑complexity mindset**: The optimal solution is *O(n)*, but many candidates start with an *O(n²)* brute force.
- **Edge‑case handling**: The problem forces you to think about *impossibility* (`-1`), *unequal counts*, and *multiple valid targets*.
- **Multilingual**: You can present solutions in Java, Python, or C++ – perfect for a polyglot portfolio.

---

## 2. Problem Statement (LeetCode 3587)

You are given an array `nums` of distinct integers. In one operation you may swap **any two adjacent elements**.

An arrangement is **valid** if every pair of neighboring elements has different parity (one is even, the other odd).

**Return** the minimum number of adjacent swaps required to transform `nums` into *any* valid arrangement.  
If it’s impossible, return `-1`.

> **Examples**
> - `[2,4,6,5,7]` → `3`
> - `[2,4,5,7]` → `1`
> - `[1,2,3]` → `0`
> - `[4,5,6,8]` → `-1`

---

## 3. Intuition – The “Even & Odd Index” Trick

1. **Collect the indices** of all even numbers and all odd numbers.
2. A *valid* array must alternate parity, so the counts of evens and odds can differ by at most one.  
   *If `|evens - odds| > 1` → impossible → return `-1`*.
3. There are **at most two** valid patterns:
   * Start with even → evens occupy indices `0,2,4,…`  
   * Start with odd  → odds occupy indices `0,2,4,…`
4. For a given pattern, the number of swaps equals the sum of *distances* each element must move to reach its target index.  
   Why? Because every adjacent swap moves an element by exactly one position.

> **Key Formula**  
> If you have a list `pos` of original indices and you want them at target indices `0,2,4,…` (`k` elements),  
> the cost is `Σ |pos[i] - 2*i|`.

---

## 4. Algorithm (O(n) time, O(1) extra space)

```text
1. Build two lists: evenIdx, oddIdx.
2. If |evenIdx| - |oddIdx| > 1 → return -1.
3. Initialise ans = INF.
4. If evenIdx can occupy even positions → ans = min(ans, cost(evenIdx)).
5. If oddIdx can occupy even positions → ans = min(ans, cost(oddIdx)).
6. Return ans.
```

*`cost(list)`* implements the distance formula.

---

## 5. Reference Code

### 5.1 Java (LeetCode‑style)

```java
import java.util.*;

class Solution {
    // Helper: sum of distances to target indices 0,2,4,...
    private long cost(List<Integer> pos) {
        long swaps = 0;
        for (int i = 0; i < pos.size(); i++) {
            swaps += Math.abs(pos.get(i) - 2L * i);
        }
        return swaps;
    }

    public int minSwaps(int[] nums) {
        List<Integer> even = new ArrayList<>();
        List<Integer> odd  = new ArrayList<>();

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] % 2 == 0) even.add(i);
            else odd.add(i);
        }

        if (Math.abs(even.size() - odd.size()) > 1) return -1;

        long ans = Long.MAX_VALUE;

        if (even.size() >= odd.size()) ans = Math.min(ans, cost(even));
        if (odd.size()  >= even.size()) ans = Math.min(ans, cost(odd));

        return (int) ans;
    }
}
```

> **Why long?**  
> Each swap cost can be up to `n`, and summing up to `n` swaps gives `O(n²)` in worst case. `long` protects against overflow for `n = 10⁵`.

---

### 5.2 Python 3

```python
from typing import List

class Solution:
    def minSwaps(self, nums: List[int]) -> int:
        # Positions of ones (odds) and zeros (evens)
        odd_pos = [i for i, v in enumerate(nums) if v & 1]
        n = len(nums)

        # Helper: cost when starting pattern is 'start' (0 or 1)
        def count_swaps(start: int) -> int:
            return sum(abs(p - (start + 2 * i)) for i, p in enumerate(odd_pos))

        # Quick impossibility test
        m = 2 * len(odd_pos)
        if m < n or m > n:
            return -1

        if m == n:                # equal counts – try both starts
            return min(count_swaps(0), count_swaps(1))
        if m == n + 1:            # odds > evens – must start with odd
            return count_swaps(0)
        if m == n - 1:            # evens > odds – must start with even
            return count_swaps(1)

        return -1
```

> **Note**: Python’s integers are unbounded, so we don't worry about overflow.

---

### 5.3 C++ (Fast & Clean)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    long long cost(const vector<int> &pos) {
        long long swaps = 0;
        for (size_t i = 0; i < pos.size(); ++i) {
            swaps += llabs(pos[i] - 2LL * i);
        }
        return swaps;
    }
public:
    int minSwaps(vector<int>& nums) {
        vector<int> even, odd;
        for (int i = 0; i < nums.size(); ++i) {
            (nums[i] % 2 == 0 ? even : odd).push_back(i);
        }

        if (abs((int)even.size() - (int)odd.size()) > 1) return -1;

        long long ans = LLONG_MAX;

        if (even.size() >= odd.size()) ans = min(ans, cost(even));
        if (odd.size()  >= even.size()) ans = min(ans, cost(odd));

        return static_cast<int>(ans);
    }
};
```

---

## 6. Complexity Analysis

| Metric          | Java | Python | C++ |
|-----------------|------|--------|-----|
| **Time**        | O(n) | O(n)   | O(n)|
| **Auxiliary**   | O(1) (except for input lists) | O(1) | O(1) |
| **Space**       | O(1) | O(1)   | O(1) |

> **Why is it O(1)?**  
> We only keep the original indices in two *lists* – the same as the input size. Once we calculate the cost we discard the lists, so the *extra* space is constant.

---

## 7. The “Bad” Parts – Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Thinking of `-1` as a special index** | Some candidates treat `-1` as a sentinel and accidentally use it in distance calculations. | Perform the impossible check **before** computing costs. |
| **Using 1‑based indices** | The cost formula is based on 0‑based indices (`0,2,4,…`). Off‑by‑one errors break everything. | Test both patterns explicitly (`start = 0` or `start = 1`). |
| **Assuming both patterns are always valid** | If the counts differ by one, only one pattern is legal. | Use `>=` checks when deciding which cost to compute. |
| **Missing overflow** | Summing `n` distances can reach 10⁵ × 10⁵ = 10¹⁰, which exceeds 32‑bit int. | Use 64‑bit integers (`long` in Java, `long long` in C++, default `int` in Python). |

---

## 8. Edge‑Case Checklist

| Scenario | What to test |
|----------|--------------|
| **All evens** | `[2,4,6]` → `-1` |
| **All odds**  | `[1,3,5]`  → `-1` |
| **Equal counts** | `[2,1,4,3]` – should return `0` (already valid). |
| **One extra odd** | `[1,2,4]` – must start with odd → cost `0`. |
| **One extra even** | `[2,1,3]` – must start with even → cost `0`. |
| **Large alternating** | `[2,4,6,8,9,11,13,15]` – test performance for 10⁵ elements. |

---

## 9. Interview Tips – How to Nail This Question

1. **Explain the impossibility early**.  
   “If the difference in counts is greater than one, no valid arrangement exists.”  
   This shows you read the problem statement thoroughly.

2. **Draw a diagram** of indices.  
   Even indices → `0,2,4,…` and Odd indices → `1,3,5,…`.  
   Visual aids convince interviewers that you can reason on paper.

3. **Talk about cost as distance**.  
   “Every adjacent swap moves an element one step. Thus the minimal number of swaps equals the total distance the elements have to travel.”

4. **Edge‑case walk‑through**.  
   Show that the algorithm works for both patterns and why we need the `min` over them.

5. **Time & space**.  
   “O(n) time because we only traverse the array a constant number of times, and O(1) space because we only keep a few counters.”

6. **Bonus** – Talk about *stable vs. unstable* swaps.  
   Since all elements are distinct, the relative order of elements of the same parity doesn’t matter.

---

## 10. Summary – What You Should Remember

- **Collect indices** → `evens` & `odds`.
- **Check feasibility** → `|evens - odds| <= 1`.
- **Compute two possible costs** → `Σ |pos[i] - 2*i|`.
- **Take the minimum** → that’s the answer.
- **Return `-1`** if impossible.

> **Practice**: Try modifying the problem:  
> *What if you could swap any two elements (not just adjacent)?*  
> *What if the array had duplicates?*  
> This will deepen your understanding of the parity–index concept.

---

## 11. Final Thought – Turn This into a Job‑Securing Skill

LeetCode problems like 3587 are *micro‑projects* that showcase a candidate’s:

- Algorithmic sharpness
- Coding fluency in multiple languages
- Attention to edge cases
- Ability to communicate ideas clearly

Add the code snippets (Java, Python, C++) to your portfolio, blog about the “good, bad, ugly” aspects, and practice explaining the problem aloud. Interviewers love candidates who *not only solve the problem but also articulate their thought process.*

> **Next steps**  
> 1. Clone this repo and add unit tests for all edge cases.  
> 2. Post the solution on GitHub with a clear README.  
> 3. Share the blog on LinkedIn/Medium with the tag #CodingInterview.  
> 4. Keep refining: ask peers to review your code and critique the explanations.

Good luck on your next interview – you’ve got this!