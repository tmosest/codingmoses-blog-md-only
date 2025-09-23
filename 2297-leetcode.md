---
title: LeetCode 2297. Jump Game VIII - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 2297. Jump Game VIII – The Complete Solution (Java | Python | C++)  
### A “Good, Bad, & Ugly” Guide + SEO‑Optimized Blog Post

---

### TL;DR
| Language | Key Idea | Complexity |
|----------|----------|------------|
| **Java** | Monotonic‑stack + DP | **O(n)** time, **O(n)** space |
| **Python** | Monotonic‑stack + DP | **O(n)** time, **O(n)** space |
| **C++** | Monotonic‑stack + DP | **O(n)** time, **O(n)** space |

The problem boils down to: for each index `i` you can jump **once** (or zero times) to the *next* larger or *next* smaller element that satisfies the “strictly increasing” or “strictly decreasing” gap rule.  
Once we know the only possible target for every index, the rest is a classic DP: `dp[i] = min(dp[j] + cost[i])` over all allowed `j` that can reach `i`.

Below are clean, production‑ready implementations in **Java, Python, and C++** followed by an SEO‑friendly blog post that explains the intuition, pitfalls, and trade‑offs.

---

## 1. The Problem (Re‑Stated)

> **Given**  
> - `nums[0…n‑1]` – the “height” of each index  
> - `costs[0…n‑1]` – the cost to land on that index  
>  
> **You start at index 0** and may jump from `i` to `j` (`i < j`) if one of the following holds:
> 1. `nums[i] ≤ nums[j]` **and** every element between them is **strictly smaller** than `nums[i]`.  
> 2. `nums[i] >  nums[j]` **and** every element between them is **greater or equal** to `nums[i]`.  
> **Goal:** reach index `n‑1` with minimum total cost (sum of `costs` of every landed index except the start).

**Constraints**: `1 ≤ n ≤ 10⁵`, `0 ≤ nums[i], costs[i] ≤ 10⁵`.

---

## 2. Intuition & Observation

1. **At most one outgoing jump per index** –  
   If you can jump from `i` to two different indices `j1` and `j2`, then the two conditions can’t both hold. This guarantees that the graph of possible jumps is a **collection of directed chains**.

2. **Only the “next” qualifying element matters** –  
   For the increasing‑case, you only need the *next* element that is **≥ nums[i]**; all earlier ones would be blocked by a smaller number.  
   Similarly, for the decreasing‑case, you need the *next* element that is **< nums[i]**.

3. **Monotonic stacks** give us exactly those “next” elements in linear time.

4. **Dynamic Programming** –  
   Once we know the only allowed predecessor for each index, `dp[i]` is the minimum cost to reach `i`:  
   `dp[i] = min(dp[prev] + costs[i])` over all `prev` that can jump to `i`.  
   Because each index has **≤ 1** predecessor in each direction, the transition is trivial.

---

## 3. Implementation Details

| Language | Highlights |
|----------|------------|
| **Java** | Uses `ArrayDeque<Integer>` as a stack.  `long[] dp` to avoid overflow (`costs` up to 10⁵ and `n` up to 10⁵ → 10¹⁰). |
| **Python** | List as stack (`append`, `pop`).  `float('inf')` for initial dp values. |
| **C++** | `vector<long long>`, `vector<int>` as stacks.  `numeric_limits<long long>::max()` for INF. |

### 3.1 Java – Monotonic Stack + DP

```java
import java.util.*;

public class Solution {
    public long minCost(int[] nums, int[] costs) {
        int n = nums.length;
        long INF = Long.MAX_VALUE / 4;          // avoid overflow in additions
        long[] dp = new long[n];
        Arrays.fill(dp, INF);
        dp[0] = 0;                               // starting point costs nothing

        Deque<Integer> incStack = new ArrayDeque<>(); // decreasing stack for next >=
        Deque<Integer> decStack = new ArrayDeque<>(); // increasing stack for next <

        for (int i = 0; i < n; i++) {
            // Case 1: nums[i] >= nums[stack.peek()]  → jump from stack.top() to i
            while (!incStack.isEmpty() && nums[i] >= nums[incStack.peek()]) {
                int j = incStack.pop();
                dp[i] = Math.min(dp[i], dp[j] + costs[i]);
            }
            // Case 2: nums[i] <  nums[stack.peek()]  → jump from stack.top() to i
            while (!decStack.isEmpty() && nums[i] < nums[decStack.peek()]) {
                int j = decStack.pop();
                dp[i] = Math.min(dp[i], dp[j] + costs[i]);
            }

            incStack.push(i);
            decStack.push(i);
        }
        return dp[n - 1];
    }
}
```

---

### 3.2 Python – Monotonic Stack + DP

```python
from typing import List
import sys

class Solution:
    def minCost(self, nums: List[int], costs: List[int]) -> int:
        n = len(nums)
        INF = 10 ** 18
        dp = [INF] * n
        dp[0] = 0

        inc_stack = []   # stack for next >= (monotonic decreasing)
        dec_stack = []   # stack for next <  (monotonic increasing)

        for i in range(n):
            while inc_stack and nums[i] >= nums[inc_stack[-1]]:
                j = inc_stack.pop()
                dp[i] = min(dp[i], dp[j] + costs[i])

            while dec_stack and nums[i] < nums[dec_stack[-1]]:
                j = dec_stack.pop()
                dp[i] = min(dp[i], dp[j] + costs[i])

            inc_stack.append(i)
            dec_stack.append(i)

        return dp[-1]
```

---

### 3.3 C++ – Monotonic Stack + DP

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long minCost(vector<int>& nums, vector<int>& costs) {
        int n = nums.size();
        const long long INF = LLONG_MAX / 4;
        vector<long long> dp(n, INF);
        dp[0] = 0;

        vector<int> incStack; // decreasing stack for next >=
        vector<int> decStack; // increasing stack for next <

        for (int i = 0; i < n; ++i) {
            while (!incStack.empty() && nums[i] >= nums[incStack.back()]) {
                int j = incStack.back();
                incStack.pop_back();
                dp[i] = min(dp[i], dp[j] + costs[i]);
            }
            while (!decStack.empty() && nums[i] < nums[decStack.back()]) {
                int j = decStack.back();
                decStack.pop_back();
                dp[i] = min(dp[i], dp[j] + costs[i]);
            }
            incStack.push_back(i);
            decStack.push_back(i);
        }
        return dp.back();
    }
};
```

---

## 4. Blog Post – “The Good, The Bad, and The Ugly of Jump Game VIII”

> **Title:** *Jump Game VIII: From Monotonic Stacks to O(n) DP – A Complete Guide*  
> **Keywords:** LeetCode 2297, Jump Game VIII, Dynamic Programming, Monotonic Stack, Java, Python, C++, Interview Prep, Algorithm Analysis  

---

### Introduction

LeetCode’s *Jump Game VIII* (Problem 2297) sits at the “medium” difficulty tier, but its twist on the classic jump‑game narrative can trip up even seasoned coders. The key is to recognize that every index has **at most one** outgoing jump in each direction, thanks to the strict “gap” rules. This observation turns a seemingly graph‑heavy problem into a tidy DP that can be solved in **O(n)** time using **monotonic stacks**.

Let’s dissect what makes this problem **good**, what pitfalls (the **bad**) lurk beneath, and where the solution can become **ugly** if we’re not careful.

---

#### The Good

1. **Linear Time & Space**  
   The monotonic stack approach guarantees `O(n)` runtime and `O(n)` auxiliary space. This is critical for the given constraints (`n ≤ 10⁵`).

2. **Single Pass, Two Stacks**  
   By processing indices left‑to‑right and maintaining two stacks (one decreasing, one increasing), we can simultaneously handle the *next greater* and *next smaller* transitions without backtracking.

3. **Clean DP Transition**  
   Since each index has at most one predecessor in each direction, the DP recurrence reduces to a single `min` operation. No need for nested loops or complex state tracking.

4. **Language‑agnostic**  
   The same logic translates seamlessly to Java, Python, and C++, making it an excellent interview “portable” solution.

---

#### The Bad

1. **Overflow Risk**  
   `costs[i]` can be up to `10⁵` and you may chain up to `10⁵` jumps. Using `int` for `dp` will overflow. All implementations use `long`/`long long` and a safe INF value.

2. **Stack Growth Mis‑understanding**  
   It’s easy to confuse “next greater or equal” with “next strictly greater.” The problem’s first condition allows equality (`nums[i] ≤ nums[j]`). The stack logic must preserve the correct inequality when popping.

3. **Edge Cases**  
   - When `n = 1`, the answer is trivially `0`.  
   - When the first element cannot reach any other index (e.g., a strictly decreasing array), the DP stays at INF for unreachable indices – the code must handle this gracefully.

4. **Memory Footprint**  
   Using a `Deque<Integer>` or `vector<int>` for the stacks can cost extra memory if you push all indices. However, the stacks never grow beyond `n`, so the total memory stays `O(n)`.

---

#### The Ugly

1. **Hard to Read Without Comments**  
   The two‑stack logic looks cryptic at first glance. Adding inline comments or a diagram in production code greatly improves maintainability.

2. **Stack Ordering Mistakes**  
   If you push indices onto the wrong stack or forget to update `dp[i]` before pushing, the solution silently becomes incorrect, which is harder to debug because all transitions occur in a single pass.

3. **Non‑Monotonic Implementation**  
   A naive approach that scans the array for the “next” qualifying element for every index (O(n²)) is technically correct but will time‑out. Avoid such “ugly” quadratic solutions unless you’re in a very constrained interview setting.

4. **Copy‑Paste Boilerplate**  
   In interviews, people often copy‑paste boilerplate without adjusting INF or stack sizes, leading to subtle bugs.

---

### Step‑by‑Step Example

> **Array**: `nums = [2, 1, 4, 3]`  
> **Costs**: `costs = [0, 5, 2, 1]`  

| Index | `nums[i]` | Popped from `incStack`? | Popped from `decStack`? | `dp[i]` after transitions |
|-------|-----------|------------------------|------------------------|--------------------------|
| 0     | 2         | –                      | –                      | 0                        |
| 1     | 1         | –                      | pop (0) → `dp[1] = 0 + 5 = 5` | 5      |
| 2     | 4         | pop (1) → `dp[2] = 5 + 2 = 7` | – | 7 |
| 3     | 3         | –                      | pop (2) → `dp[3] = 7 + 1 = 8` | 8 |

Result: `dp[3] = 8` – the minimum cost to reach the last index.

A quick diagram can help readers visualize the two directed chains.

---

### Final Thoughts

Jump Game VIII showcases the beauty of algorithmic reductions: a problem that feels like a graph traversal is, with the right observation, a classic DP solved with two monotonic stacks. Keep an eye on integer overflow, inequalities, and edge cases to avoid the *bad*, and add clear comments to tame the *ugly*.

Happy coding, and may your jumps always be minimal!

---

### References & Further Reading

- LeetCode 2297 – [Problem Statement](https://leetcode.com/problems/jump-game-iv/)
- *Introduction to Monotonic Stacks* – [GeeksforGeeks](https://www.geeksforgeeks.org/stack-data-structure/)
- *Dynamic Programming 101* – *Cracking the Coding Interview*, 7th Edition.

---

**Author:** [Your Name] – Interview Coach, Algorithm Enthusiast, Open Source Contributor.  
**Contact:** `youremail@example.com`

---

## 5. Final Checklist

- [ ] Use `long`/`long long` for DP to avoid overflow.  
- [ ] Ensure the stack pop condition respects `≤` vs `<`.  
- [ ] Handle `n == 1` immediately.  
- [ ] Add comments or diagrams for clarity in real interview or production code.  
- [ ] Test against random cases, especially edge arrays (strictly increasing/decreasing).  

With this, you’re fully equipped to tackle *Jump Game VIII* in any interview setting and any programming language you prefer. Happy coding! 🚀

---