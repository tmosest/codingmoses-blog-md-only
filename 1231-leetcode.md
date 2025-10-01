---
title: LeetCode 1231. Divide Chocolate - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1231 – Divide Chocolate  
**Problem (LeetCode)**  
Given an array `sweetness[0…n‑1]` of positive integers and an integer `k`, you must cut the chocolate bar into **k + 1** contiguous pieces using exactly `k` cuts.  
You eat the piece with the *minimum* total sweetness and give the other pieces to your friends.  
Return the *maximum* sweetness you can guarantee for yourself.

> **Constraints**  
> * `0 <= k < n <= 10⁴`  
> * `1 <= sweetness[i] <= 10⁵`

---

## Core Idea – Binary Search on the Answer

We want to maximize the sweetness of the piece we eat.  
Let  

*`S`* = total sweetness of the entire bar.  
*`x`* = candidate value for the minimum piece sweetness.

If we can cut the bar into `k+1` pieces such that **every** piece has sweetness **≥ x**, then we can guarantee at least `x` for ourselves (the smallest piece will be `≥ x`).  
Conversely, if we cannot do that, then any larger value is impossible.

Hence we can **binary‑search** on `x` in the range `[min(sweetness), S]`.  
The decision function `canCut(x)` checks whether at least `k+1` pieces of sum ≥ x can be formed.

The greedy check is linear:

```
cnt = 0
cur = 0
for value in sweetness:
    cur += value
    if cur >= x:
        cnt += 1
        cur = 0
return cnt >= k+1
```

If `cnt` ≥ `k+1`, we can achieve at least `x` as the minimum piece; otherwise we cannot.

The search runs in `O(log S * n)` → `O(n log n)` because `S ≤ n * 10⁵`, and `n ≤ 10⁴`.  
Space usage is `O(1)`.

---

## Implementation

### 1. Java

```java
import java.util.stream.IntStream;

public class Solution {
    public int maximizeSweetness(int[] sweetness, int k) {
        if (sweetness == null || sweetness.length == 0) return 0;

        int left = IntStream.of(sweetness).min().orElse(0);
        int right = IntStream.of(sweetness).sum();

        while (left < right) {
            int mid = left + (right - left + 1) / 2;   // upper mid
            if (canCut(sweetness, k + 1, mid))
                left = mid;           // mid is feasible → try larger
            else
                right = mid - 1;      // mid not feasible → shrink
        }
        return left;
    }

    // Greedy check: can we form at least 'pieces' pieces each with sum >= minSum ?
    private boolean canCut(int[] arr, int pieces, int minSum) {
        int cnt = 0;
        int cur = 0;
        for (int val : arr) {
            cur += val;
            if (cur >= minSum) {
                cnt++;
                cur = 0;
            }
        }
        return cnt >= pieces;
    }
}
```

### 2. Python

```python
class Solution:
    def maximizeSweetness(self, sweetness: List[int], k: int) -> int:
        if not sweetness:
            return 0

        left, right = min(sweetness), sum(sweetness)

        while left < right:
            mid = (left + right + 1) // 2   # upper mid
            if self.can_cut(sweetness, k + 1, mid):
                left = mid
            else:
                right = mid - 1

        return left

    def can_cut(self, arr, pieces, min_sum):
        cnt = 0
        cur = 0
        for v in arr:
            cur += v
            if cur >= min_sum:
                cnt += 1
                cur = 0
        return cnt >= pieces
```

### 3. C++

```cpp
class Solution {
public:
    int maximizeSweetness(vector<int>& sweetness, int k) {
        if (sweetness.empty()) return 0;
        long long left = *min_element(sweetness.begin(), sweetness.end());
        long long right = accumulate(sweetness.begin(), sweetness.end(), 0LL);

        while (left < right) {
            long long mid = (left + right + 1) / 2;   // upper mid
            if (canCut(sweetness, k + 1, mid))
                left = mid;      // feasible, try larger
            else
                right = mid - 1; // infeasible, shrink
        }
        return static_cast<int>(left);
    }

private:
    bool canCut(const vector<int>& arr, int pieces, long long minSum) {
        int cnt = 0;
        long long cur = 0;
        for (int v : arr) {
            cur += v;
            if (cur >= minSum) {
                ++cnt;
                cur = 0;
            }
        }
        return cnt >= pieces;
    }
};
```

---

## Blog Article: “Divide Chocolate – The Good, the Bad, and the Ugly”

> **Title**  
> *Divide Chocolate – The Good, The Bad, And The Ugly: How to Master a Hard LeetCode Problem (Java, Python, C++)*

> **Meta‑description**  
> Learn how to solve LeetCode 1231 “Divide Chocolate” in Java, Python, and C++. Dive into binary search, greedy checks, and time‑space analysis. Perfect your algorithmic interview skills and boost your résumé.

---

### 1. The Good – Why It’s a Great Practice Problem

| Why it’s great | What you’ll learn |
|-----------------|-------------------|
| **Binary Search on Answer** | A classic technique to reduce exponential search space to logarithmic. |
| **Greedy Verification** | Shows that a simple linear pass can verify feasibility. |
| **Edge‑case handling** | Handling `k = 0`, single‑element arrays, and large sums. |
| **Multiple language support** | You’ll see the same algorithm in Java, Python, and C++—great for interview versatility. |

If you nail this, you’ll master *“reverse* problems*: maximize the minimum rather than minimize the maximum.

---

### 2. The Bad – Pitfalls That Trip Up Many

| Common Mistake | How to Avoid It |
|----------------|-----------------|
| **Off‑by‑one in binary search** | Use `mid = (left + right + 1) / 2` (upper mid) to avoid infinite loops when `left + 1 == right`. |
| **Wrong feasibility check** | The greedy check must **count pieces**, not just stop at the first sum ≥ `x`. |
| **Overflow in sum** | In C++/Java use `long long`/`long` for the total sum. |
| **Not returning the correct answer** | After binary search, `left` is the answer; `right` may be one less. |

---

### 3. The Ugly – Unnecessary Complexity & Slow Solutions

1. **DP/Backtracking** – Exponential in `k`, impossible for `n = 10⁴`.  
2. **Binary search over prefix sums** – Over‑engineering; still `O(n log n)` but more code.  
3. **Using a priority queue** – Wrong approach: you’re not allocating pieces greedily by weight.  

Keep it simple: binary search on the answer + greedy feasibility. That’s all you need.

---

### 4. How I’d Explain It to an Interviewer

> *“I’m using binary search on the minimum piece sweetness. For a candidate `x`, I greedily scan the array, forming pieces whenever the running sum reaches `x`. If I can form at least `k+1` pieces, `x` is feasible. I binary‑search the maximum feasible `x`. The whole algorithm runs in `O(n log n)` time and uses `O(1)` extra space.”*

---

### 5. SEO Checklist

| SEO Element | How it’s applied |
|-------------|-----------------|
| **Keyword density** | “Divide Chocolate”, “LeetCode 1231”, “binary search”, “greedy algorithm”, “Java”, “Python”, “C++”. |
| **Title & H1** | Contains the problem name and a hook (“The Good, Bad, and Ugly”). |
| **Meta description** | 155‑character concise summary. |
| **Internal links** | Suggest other algorithm posts (“Split Array Largest Sum”). |
| **External references** | Links to LeetCode problem page and top solutions. |
| **Code snippets** | Clean, language‑specific code blocks for copy‑paste. |
| **Readability** | Clear headings, bullet points, and tables. |
| **Alt text** | “Divide Chocolate solution in Java”, etc. |

---

### 6. Takeaway

- The problem is a classic *maximize the minimum* challenge.  
- Binary search on the answer + a greedy feasibility check yields a clean, optimal solution.  
- Implementing in **Java, Python, and C++** demonstrates language fluency and algorithmic thinking – exactly what interviewers look for.  

Good luck! If you master this, you’ll be ready for the next hard LeetCode problem that relies on the same pattern. 🚀