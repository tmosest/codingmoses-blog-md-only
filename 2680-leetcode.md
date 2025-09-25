---
title: LeetCode 2680. Maximum OR - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧠 LeetCode 2680 – “Maximum OR”  
**Languages**: Java | Python | C++  
**Interview‑ready** solution + a *SEO‑friendly* blog post that explains the “good, bad & ugly” of the problem so you can talk confidently in your next interview.  

---

## 🚀 Quick Code Snippets  

| Language | Code |
|----------|------|
| **Java** | <details><summary>Click to expand</summary>```java
import java.util.*;

public class Solution {
    public long maximumOr(int[] nums, int k) {
        int n = nums.length;
        // suffix[i] = OR of nums[i+1 … n-1]
        int[] suffix = new int[n];
        for (int i = n - 2; i >= 0; --i) {
            suffix[i] = suffix[i + 1] | nums[i + 1];
        }

        long best = 0;
        int prefixOr = 0;           // OR of elements before current index
        for (int i = 0; i < n; i++) {
            long candidate = prefixOr | ((long)nums[i] << k) | suffix[i];
            if (candidate > best) best = candidate;
            prefixOr |= nums[i];
        }
        return best;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.maximumOr(new int[]{12, 9}, 1)); // 30
        System.out.println(s.maximumOr(new int[]{8, 1, 2}, 2)); // 35
    }
}
```</details> |
| **Python** | <details><summary>Click to expand</summary>```python
from typing import List

class Solution:
    def maximumOr(self, nums: List[int], k: int) -> int:
        n = len(nums)
        suffix = [0] * n
        for i in range(n - 2, -1, -1):
            suffix[i] = suffix[i + 1] | nums[i + 1]

        best = 0
        prefix_or = 0
        for i, val in enumerate(nums):
            candidate = prefix_or | (val << k) | suffix[i]
            if candidate > best:
                best = candidate
            prefix_or |= val
        return best

# quick tests
if __name__ == "__main__":
    s = Solution()
    print(s.maximumOr([12, 9], 1))          # 30
    print(s.maximumOr([8, 1, 2], 2))        # 35
```</details> |
| **C++** | <details><summary>Click to expand</summary>```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maximumOr(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> suffix(n, 0);
        for (int i = n - 2; i >= 0; --i)
            suffix[i] = suffix[i + 1] | nums[i + 1];

        long long best = 0;
        int prefixOr = 0;
        for (int i = 0; i < n; ++i) {
            long long candidate = (long long)prefixOr | ((long long)nums[i] << k) | suffix[i];
            best = max(best, candidate);
            prefixOr |= nums[i];
        }
        return best;
    }
};

int main() {
    Solution s;
    cout << s.maximumOr({12, 9}, 1) << endl;   // 30
    cout << s.maximumOr({8, 1, 2}, 2) << endl; // 35
    return 0;
}
```</details> |

---

## 📖 Blog Post – “Maximum OR (LeetCode 2680): The Good, The Bad, and The Ugly”

### 1. Introduction  

> **Problem Title**: *Maximum OR*  
> **LeetCode ID**: 2680  
> **Difficulty**: Medium  

You’re given an array `nums` and an integer `k`. In one operation you can double (multiply by 2) any element of the array. You can perform at most `k` operations. After all operations, you must return the maximum possible value of `nums[0] | nums[1] | … | nums[n‑1]`.  

> *Why is this interview‑friendly?*  
> It tests bit manipulation, greedy reasoning, and an understanding of how to keep the algorithm linear. The constraints (n ≤ 10⁵, k ≤ 15) hint that a naive exponential search will fail.

---

### 2. Problem Breakdown  

- **Bitwise OR** combines bits: if any number has a 1 at a position, the result will have a 1 there.  
- **Doubling an element** is the same as left‑shifting by one: `x * 2 == x << 1`.  
- You may perform the shift on *any* element, possibly the same element multiple times.  
- `k` is small (≤ 15), but `n` can be large (≤ 10⁵).

---

### 3. Brute‑Force Approach  

You could try all `k`-length sequences of indices to shift (each of the `n` elements can be chosen).  
- Complexity: O(nᵏ) → impossible for n = 10⁵.  
- Even enumerating all subsets of indices is 2ⁿ.  

**Good**: Easy to understand.  
**Bad**: Completely infeasible.

---

### 4. Insight – “Why Double Only One Element?”  

- Doubling moves a set bit one position left.  
- The **most significant set bit (MSB)** contributes the biggest value to the OR result.  
- If we split the `k` shifts among several numbers, each individual number’s MSB can only move `k₁, k₂,…` places, never exceeding `k` shifts.  
- The OR of all numbers can never have a bit set beyond the furthest shift of a single number.  
- Hence, *putting all `k` shifts on the element with the highest MSB* maximizes the final OR.  

> **Ugly part**: Proving the greedy choice formally might feel non‑intuitive. But a quick counter‑example shows any distribution that isn’t all‑to‑one leaves a higher bit unset.

---

### 5. Optimal One‑Pass Solution  

Instead of choosing which element to shift, we can pre‑compute the OR of all elements **before** and **after** each position.  

#### 5.1 Prefix & Suffix OR  

- `prefix[i]` = OR of `nums[0 … i-1]`.  
- `suffix[i]` = OR of `nums[i+1 … n-1]`.  

These can be built in a single pass each.

#### 5.2 Candidate for Each Index  

For index `i`, if we apply all `k` shifts to `nums[i]`:

```
candidate = prefix[i] | (nums[i] << k) | suffix[i]
```

Take the maximum `candidate` over all `i`.

#### 5.3 Why This Works  

- `prefix[i]` and `suffix[i]` already contain all the bits from numbers *outside* `i`.  
- Shifting `nums[i]` by `k` positions only potentially sets new high bits that were not set elsewhere.  
- The OR of these three parts is the best we can achieve if we double `nums[i]` `k` times.  
- Checking all indices ensures we consider the best element to double.

#### 5.4 Complexity  

- **Time**: O(n) – one pass to build `suffix`, one pass to evaluate candidates.  
- **Space**: O(n) – arrays `prefix` & `suffix` (can be compressed to O(1) by building `suffix` on‑the‑fly if you’re memory‑tight).  
- The `k` value only influences a constant shift operation.

---

### 6. Edge‑Case Checklist  

| Edge Case | What to Watch For |
|-----------|-------------------|
| `k == 0` | No shift → answer is the plain OR of the array. |
| `n == 1` | Shifting the single element gives `nums[0] << k`. |
| Numbers may already have high bits set at positions ≥ k | Shifting still can set even higher bits. |
| Negative numbers? | The problem guarantees non‑negative ints (0 ≤ nums[i] < 2³¹). |

---

### 7. Pros & Cons (Good & Bad)

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Conceptual clarity** | Greedy intuition + OR pre‑computation is neat. | Requires understanding that MSB dominates. | The greedy proof can feel like a “horror” step if not explained. |
| **Scalability** | Works for n = 10⁵ easily. | – | – |
| **Memory** | O(n) extra arrays. | Might be a concern for strict memory limits. | Could be trimmed to O(1) by keeping the running suffix OR while iterating left‑to‑right. |
| **Implementability** | Straightforward loops. | None. | The left‑shift cast to 64‑bit (`long` in Java / `long long` in C++) is a tiny detail that can trip beginners. |
| **Interview‑talk** | You can talk about bitwise OR, greedy, prefix/suffix. | None. | The proof‑of‑optimality can be a conversation starter; be ready to explain it clearly. |

---

### 8. Alternate O(1) Space Variant  

You can avoid storing the entire suffix array by building the suffix OR in reverse and evaluating immediately:

```java
int suffixOr = 0;          // OR of elements after the current
for (int i = n - 1; i >= 0; --i) {
    long candidate = ((long)nums[i] << k) | suffixOr | prefixOr;
    best = Math.max(best, candidate);
    suffixOr |= nums[i];
    prefixOr |= nums[i];
}
```

This uses only a few integer variables.

---

### 9. Real‑World Interview Talk  

- **Highlight** the bit‑shifting equivalence to left‑shift.  
- **Explain** the greedy “all‑shifts‑to‑one” intuition.  
- **Show** the prefix/suffix trick as a classic O(n) pattern.  
- **Mention** the small `k` → we could do an O(k · n) DP if we wanted constant space, but the current solution is clean.  
- **Prepare** a quick counter‑example if the interviewer asks “why not split shifts?”

---

### 10. Take‑Away Checklist for Your Next Interview

| ✅ Item | Why it matters |
|---------|----------------|
| **Bitwise operations** | Core of many system‑design/algorithms interviews. |
| **Greedy proof** | Shows you can reason about optimality. |
| **Linear solution** | Demonstrates ability to handle large `n`. |
| **Multiple‑language implementation** | Interviewers love seeing you can adapt to language constraints. |
| **Complexity analysis** | Always discuss time/space. |

---

### 11. SEO Tags & Keywords  

- LeetCode 2680  
- Maximum OR  
- Bitwise OR interview question  
- Java Python C++ solution  
- Interview algorithm interview  
- Job interview prep  
- Algorithmic reasoning  
- Linear time bit manipulation  

> *These keywords will help recruiters find your post when they search for “LeetCode 2680 interview solutions” or “job interview algorithm problems.”*

---

### 12. Conclusion  

The “Maximum OR” problem is deceptively simple yet hides a powerful greedy principle. By realizing that *all shifts should go to one element* and using prefix/suffix OR pre‑computation, we arrive at a clean, O(n) solution that works comfortably within the given limits.  

Whether you’re a seasoned engineer or a junior developer preparing for your next interview, this problem is a great showcase of:

- **Bit‑level insight**  
- **Greedy reasoning**  
- **Efficient linear algorithm**  

Good luck – and may your interviewers be impressed by your mastery of both the code and the underlying theory! 🚀