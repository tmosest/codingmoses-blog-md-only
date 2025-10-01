---
title: LeetCode 325. Maximum Size Subarray Sum Equals k - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1.  Problem – LeetCode 325 : **Maximum Size Subarray Sum Equals k**

> **Given** an integer array `nums` and an integer `k`,  
> **Return** the *maximum length* of a sub‑array that sums to `k`.  
> If no such sub‑array exists, return `0`.

**Constraints**

| | |
|---|---|
| `1 ≤ nums.length ≤ 2 * 10⁵` | |
| `-10⁴ ≤ nums[i] ≤ 10⁴` | |
| `-10⁹ ≤ k ≤ 10⁹` | |

> Example 1  
> `nums = [1,-1,5,-2,3]`, `k = 3` → **output: 4**  
> (sub‑array `[1, -1, 5, -2]`)

> Example 2  
> `nums = [-2,-1,2,1]`, `k = 1` → **output: 2**  
> (sub‑array `[-1, 2]`)

---

## 2.  The “Good, the Bad, and the Ugly”

| | Good | Bad | Ugly |
|---|---|---|---|
| **Algorithmic Insight** | Use *prefix sums + hash map* to find the longest sub‑array in **O(n)** time. | A naïve double‑loop (O(n²)) works for small inputs but blows up to ~4 × 10¹⁰ operations. | Handling of **negative numbers**, **large ranges**, and **overflow** when computing sums. |
| **Time Complexity** | **O(n)** – one pass. | **O(n²)** – unacceptable for the upper limit. | **O(n)** still, but a careful design avoids accidental overflow. |
| **Space Complexity** | **O(n)** – one entry per unique prefix sum. | **O(1)** – but unacceptable time. | **O(n)** – still fine; memory stays within limits. |
| **Edge Cases** | Empty sub‑array (k = 0) – handled naturally by the map. | Off‑by‑one errors when computing indices. | Large negative `k` or sums that fit in `int` only if 64‑bit (`long`) is used. |
| **Implementation Language** | **Java, Python, C++** – all support hash maps and 64‑bit integers. | None – the approach itself is the issue. | None – just careful typing. |
| **Interview‑Ready** | Clear explanation of prefix sums, why a map is used, and a succinct code snippet. | A quick brute‑force code snippet that “works” but will not pass the test set. | Robustness: guard against null/empty arrays, overflow, and explain time/space trade‑offs. |

---

## 3.  Optimal Solution – Prefix Sum + HashMap

### 3.1  Idea

1. Let `prefix[i]` be the sum of the first `i` elements (`prefix[0] = 0`).  
2. For any two indices `i < j`, the sub‑array `nums[i] … nums[j‑1]` sums to  
   `prefix[j] - prefix[i]`.  
3. We want `prefix[j] - prefix[i] == k` → `prefix[i] == prefix[j] - k`.  
4. While iterating, keep a hash map that stores the **first** index at which each prefix sum occurs.  
5. When we encounter a current prefix sum `cur`, check if `cur - k` has already been seen.  
   If so, the length is `currentIndex - map.get(cur - k)`.  
6. Update the answer if this length is longer.

This single pass gives O(n) time and O(n) space.

---

## 4.  Reference Implementations

> **Note**: All codes use a 64‑bit integer (`long` in Java/C++/Python’s `int`) to safely store intermediate sums.

### 4.1  Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    /**
     * 325. Maximum Size Subarray Sum Equals k
     *
     * @param nums the input array
     * @param k    the target sum
     * @return maximum length of a subarray summing to k
     */
    public int maxSubArrayLen(int[] nums, int k) {
        // Map: prefix sum value -> earliest index where it occurs
        Map<Long, Integer> firstSeen = new HashMap<>();
        firstSeen.put(0L, -1);   // prefix sum 0 occurs before first element

        long prefix = 0;
        int maxLen = 0;

        for (int i = 0; i < nums.length; i++) {
            prefix += nums[i];                     // current prefix sum
            long target = prefix - k;              // we need this earlier prefix

            if (firstSeen.containsKey(target)) {   // subarray found
                int startIdx = firstSeen.get(target);
                maxLen = Math.max(maxLen, i - startIdx);
            }

            // store the earliest occurrence of this prefix
            firstSeen.putIfAbsent(prefix, i);
        }

        return maxLen;
    }

    // Simple driver to test the solution
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.maxSubArrayLen(new int[]{1, -1, 5, -2, 3}, 3)); // 4
        System.out.println(sol.maxSubArrayLen(new int[]{-2, -1, 2, 1}, 1));    // 2
    }
}
```

### 4.2  Python

```python
from typing import List

class Solution:
    def maxSubArrayLen(self, nums: List[int], k: int) -> int:
        # prefix sum -> earliest index
        first_seen = {0: -1}
        prefix = 0
        max_len = 0

        for i, num in enumerate(nums):
            prefix += num
            target = prefix - k
            if target in first_seen:
                max_len = max(max_len, i - first_seen[target])
            # record earliest occurrence
            first_seen.setdefault(prefix, i)

        return max_len

# Quick test
if __name__ == "__main__":
    sol = Solution()
    print(sol.maxSubArrayLen([1, -1, 5, -2, 3], 3))  # 4
    print(sol.maxSubArrayLen([-2, -1, 2, 1], 1))     # 2
```

### 4.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxSubArrayLen(vector<int>& nums, int k) {
        unordered_map<long long, int> firstSeen;
        firstSeen[0] = -1;          // prefix sum 0 before array starts

        long long prefix = 0;
        int maxLen = 0;

        for (int i = 0; i < nums.size(); ++i) {
            prefix += nums[i];
            long long target = prefix - k;

            auto it = firstSeen.find(target);
            if (it != firstSeen.end()) {
                maxLen = max(maxLen, i - it->second);
            }

            // keep earliest index for this prefix
            if (!firstSeen.count(prefix))
                firstSeen[prefix] = i;
        }
        return maxLen;
    }
};

// Test harness
int main() {
    Solution s;
    cout << s.maxSubArrayLen({1, -1, 5, -2, 3}, 3) << endl; // 4
    cout << s.maxSubArrayLen({-2, -1, 2, 1}, 1) << endl;   // 2
}
```

---

## 5.  Why This Is Interview‑Ready

| Question | What to say |
|---|---|
| **Why does the map store the *first* occurrence of a prefix?** | The longest sub‑array ending at the current index uses the *earliest* start that gives the desired sum. Storing later occurrences would only shorten potential lengths. |
| **What about negative numbers?** | The algorithm works for any integers because it relies on *difference* of prefix sums, not on monotonicity. |
| **Why 64‑bit integer?** | The cumulative sum can reach up to `2 * 10⁵ * 10⁴ = 2 * 10⁹`, which is safe in `int` on most platforms, but to avoid accidental overflow when subtracting `k` (which can be ±10⁹), we use `long`/`long long`/Python’s arbitrary precision `int`. |
| **Complexity?** | **Time**: O(n) – one scan. **Space**: O(n) – hash map of at most n+1 entries. |
| **Edge Cases?** | Empty sub‑array when `k == 0` is handled by the initial map entry. All negative or all positive arrays are covered. |

---

## 6.  SEO‑Optimized Blog Article

> **Title**: *LeetCode 325 – Maximum Size Subarray Sum Equals k: Master the HashMap Trick to Ace Your Interview*  
> **Meta‑Description**: Learn the fast O(n) solution for LeetCode 325 in Java, Python, and C++. Understand the good, bad, and ugly parts of the problem and how to impress hiring managers.  
> **Keywords**: LeetCode 325, Maximum Size Subarray Sum Equals k, array subarray sum, prefix sum, hash map interview, Java solution, Python solution, C++ solution, coding interview prep, job interview tips.

---

### 6.1  Introduction

If you’re preparing for a software engineering interview, you’ll quickly encounter **sub‑array** problems. One classic on LeetCode is **“Maximum Size Subarray Sum Equals k”** (problem 325). It tests your understanding of prefix sums, hash maps, and edge‑case handling—all of which are highly prized by hiring managers at FAANG and beyond.

In this post we dissect the problem, show the cleanest *good* solution, warn you about the *bad* brute‑force pitfall, and reveal the *ugly* corner cases that can trip up even seasoned coders. We’ll provide working code in **Java, Python, and C++**, and give you a cheat‑sheet you can cite in interviews.

> **Quick Takeaway**: A single hash map + prefix sum gives you **O(n)** time and **O(n)** space, which is the optimal solution.  

---

### 6.2  Problem Recap

> **Given** an integer array `nums` and integer `k`, return the **maximum length** of a contiguous sub‑array that sums to `k`. If no such sub‑array exists, return `0`.

> **Constraints**  
> * 1 ≤ `nums.length` ≤ 200,000  
> * –10⁴ ≤ `nums[i]` ≤ 10⁴  
> * –10⁹ ≤ `k` ≤ 10⁹

---

### 6.3  The Good – O(n) HashMap + Prefix Sum

1. **Prefix Sum** – `prefix[i]` = sum of first `i` elements (`prefix[0] = 0`).  
2. **Key Observation** – Sub‑array `i … j‑1` sums to `k` iff  
   `prefix[j] – prefix[i] == k` ⇔ `prefix[i] == prefix[j] – k`.  
3. **HashMap** – Store the earliest index where each prefix sum occurs.  
4. **One Pass** – While iterating, check if `currentPrefix – k` has been seen.  
   If so, update the answer with `currentIndex - firstSeen[currentPrefix – k]`.  

#### Why “first” index?  
Because the *longest* sub‑array ending at the current index uses the **earliest** possible start. Storing later indices would only shrink candidate lengths.

---

### 6.4  The Bad – O(n²) Brute Force

A straightforward double loop checks every sub‑array’s sum:

```python
for i in range(len(nums)):
    s = 0
    for j in range(i, len(nums)):
        s += nums[j]
        if s == k:
            best = max(best, j - i + 1)
```

This works for tiny inputs but quickly times out:  
For `n = 200,000`, the loop runs ~2 × 10¹⁰ operations – far beyond acceptable limits.

> **Takeaway**: Avoid the naive approach unless you’re absolutely certain the array is tiny.

---

### 6.5  The Ugly – Edge Cases & Overflow

| Issue | Explanation | How We Fix It |
|---|---|---|
| **Negative numbers** | Sub‑array sums can decrease, breaking algorithms that rely on monotonicity (e.g., sliding window). | Prefix sums + hash map work with any integer. |
| **Large sums** | Cumulative sum can reach ±2 × 10⁹; subtracting `k` (also up to ±10⁹) may overflow 32‑bit `int`. | Use 64‑bit (`long` in Java/C++, Python’s `int` is arbitrary). |
| **k = 0** | An empty sub‑array (before any element) has sum 0. We must not double‑count it. | Initialize `firstSeen[0] = -1` before scanning. |
| **All elements negative / all positive** | Still covered by the algorithm because it only looks at prefix differences. | No special handling required. |
| **Empty array** | Not allowed by constraints, but if it were, the map entry guarantees 0 → -1. | Already handled. |

---

### 6.6  Full Code – Java, Python, C++

See the **Code Block** section above for clean, commented implementations in all three major languages. Each example includes a tiny driver that prints the expected outputs (`4` and `2`) for the sample tests.

> **Tip for the Interview**: Mention that the map is *unordered* (`unordered_map` in C++, `HashMap` in Java) for O(1) lookup, and that you’re careful about 64‑bit arithmetic.

---

### 6.7  Interview Cheat‑Sheet

> **Question**: *Explain how you’d find the longest sub‑array that sums to `k`.*  
> **Answer**:  
> *Use prefix sums.*  
> *Keep a hash map of the earliest index for each prefix.*  
> *At each index, if `prefix - k` is in the map, update the maximum length.*  
> *Complexity: O(n) time, O(n) space.*

> **Follow‑up**: *Why does negative `k` not break this?*  
> *Because we’re only taking differences of prefix sums—no monotonicity is required.*

> **Follow‑up**: *What if the array is all negative?*  
> *The algorithm still works; the map will simply contain decreasing prefix sums.*

> **Follow‑up**: *Any risk of overflow?*  
> *Yes, if you use 32‑bit `int`. We use 64‑bit `long`/`long long`.*

---

### 6.8  Conclusion

LeetCode 325 is a *simple* yet *powerful* problem that reveals whether you understand fundamental array tricks. The O(n) solution is elegant, handles every corner case, and is ready to deploy in a real interview.

Remember:  

* **Good** – prefix sums + hash map, O(n).  
* **Bad** – double loop, O(n²).  
* **Ugly** – negative numbers, overflow – solved with 64‑bit arithmetic.

Feel confident enough to show off this pattern. And if you’re looking for *job interview tips*, keep this post handy – it’s exactly the kind of concise, technical explanation hiring managers want to see.

---

### 6.9  Call to Action

*Want to practice more?*  
Try LeetCode 56 (Subarray Sum Equals K) or 209 (Minimum Size Subarray Sum).  
Add them to your study list and use the *hash map + prefix sum* pattern consistently.

Happy coding, and good luck on your next interview!  

---

*Author: [Your Name], Software Engineer & Technical Recruiter*  

--- 

> **End of Blog Post**  

--- 

### 7.  Conclusion

You now have:

1. A **fast O(n)** solution that’s proven in practice and works for any integer array.  
2. **Reference implementations** in three popular languages.  
3. A **ready‑to‑cite interview cheat‑sheet** explaining the logic and complexity.  
4. An **SEO‑friendly article** that will rank in search results for LeetCode 325 queries.

Use this material to polish your interview skills, avoid common pitfalls, and land that coveted engineering role. Happy coding!