---
title: LeetCode 2970. Count the Number of Incremovable Subarrays I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – LeetCode 2970  
**Count the Number of Incremovable Subarrays I**  

You are given a 0‑indexed array of positive integers `nums`.  
A **subarray** `[i … j]` (contiguous, non‑empty) is called *incremovable* if removing it makes the remaining array **strictly increasing**.  
Return the total number of incremovable subarrays.

*Examples*

| `nums` | answer | explanation |
|--------|--------|-------------|
| `[1,2,3,4]` | 10 | every subarray is incremovable |
| `[6,5,7,8]` | 7 | only the listed 7 subarrays work |
| `[8,7,6,6]` | 3 | `[8,7,6]`, `[7,6,6]`, `[8,7,6,6]` |

*Constraints*

```
1 ≤ nums.length ≤ 50
1 ≤ nums[i] ≤ 50
```

Because the array is tiny (≤ 50), a simple O(n³) brute‑force solution runs in a few microseconds and is acceptable for an interview setting.  
Below you’ll find three clean implementations – **Java**, **Python**, and **C++** – all using the same O(n³) logic.  
We’ll also show a brief O(n²) optimisation that’s worth keeping in mind for larger inputs.

---

## 2.  The Brute‑Force Idea (O(n³))

1. **Pick a subarray** `[i … j]`.  
2. **Skip all elements inside** `[i … j]` and traverse the remaining elements from left to right.  
3. Check that each visited element is **strictly greater** than the previously visited one.  
4. If the check passes, the subarray is incremovable → increment the counter.

The algorithm only needs a few integer variables (`i`, `j`, `k`, `last`, `ok`) – constant extra space.

### Why It Works

Removing a subarray is equivalent to concatenating the prefix (`0 … i‑1`) and suffix (`j+1 … n‑1`).  
The concatenated array is strictly increasing iff every element of the suffix is greater than the last element of the prefix, and all elements within each segment are already increasing.  
The algorithm mimics this test by scanning the array once per candidate subarray.

---

## 3.  Implementation Details

| Language | Code Highlights |
|----------|-----------------|
| **Java** | Uses `int` arrays, a `long` counter to avoid overflow (not necessary for n=50, but safe). |
| **Python** | Straightforward loops; Python’s simplicity shines for this tiny n. |
| **C++** | Uses `vector<int>`, `long long` counter, and `bool ok` flag. |

Below are the exact snippets.

### 3.1 Java (Java 17)

```java
public class Solution {
    public long incremovableSubarrayCount(int[] nums) {
        int n = nums.length;
        long ans = 0;

        // start index
        for (int i = 0; i < n; i++) {
            // end index
            for (int j = i; j < n; j++) {
                boolean ok = true;
                int last = Integer.MIN_VALUE;   // sentinel: no previous element

                // scan the entire array
                for (int k = 0; k < n; k++) {
                    if (k >= i && k <= j) {          // inside subarray – skip
                        continue;
                    }
                    if (last >= nums[k]) {           // not strictly increasing
                        ok = false;
                        break;                      // no need to keep checking
                    }
                    last = nums[k];
                }
                if (ok) ans++;
            }
        }
        return ans;
    }
}
```

### 3.2 Python (Python 3.11)

```python
class Solution:
    def incremovableSubarrayCount(self, nums: list[int]) -> int:
        n = len(nums)
        ans = 0
        for i in range(n):            # start
            for j in range(i, n):     # end
                ok = True
                last = float('-inf')
                for k in range(n):
                    if i <= k <= j:   # inside the subarray
                        continue
                    if last >= nums[k]:
                        ok = False
                        break
                    last = nums[k]
                if ok:
                    ans += 1
        return ans
```

### 3.3 C++ (C++17)

```cpp
class Solution {
public:
    long long incremovableSubarrayCount(vector<int>& nums) {
        int n = nums.size();
        long long ans = 0;

        for (int i = 0; i < n; ++i) {          // start
            for (int j = i; j < n; ++j) {      // end
                bool ok = true;
                int last = INT_MIN;           // sentinel

                for (int k = 0; k < n; ++k) {
                    if (k >= i && k <= j) continue; // skip subarray
                    if (last >= nums[k]) {          // not strictly increasing
                        ok = false;
                        break;
                    }
                    last = nums[k];
                }
                if (ok) ++ans;
            }
        }
        return ans;
    }
};
```

---

## 4.  A More Optimised (O(n²)) View

Although the brute force passes comfortably, you can reduce the inner loop by pre‑computing:

- `leftInc[i]` – is the prefix `nums[0…i]` strictly increasing?
- `rightInc[i]` – is the suffix `nums[i…n-1]` strictly increasing?

Then for each `[i…j]` you only need to check:

1. `leftInc[i-1]` (if `i>0`) – ensures prefix before `i` is fine.  
2. `rightInc[j+1]` (if `j+1<n`) – ensures suffix after `j` is fine.  
3. `nums[i-1] < nums[j+1]` (if both sides exist) – guarantees the join point is increasing.  

With these arrays the decision for a subarray becomes **O(1)**, turning the whole algorithm into **O(n²)**.  
For the given constraints it is over‑engineering, but it’s an excellent trick to remember for interview questions where `n` might be up to `10⁵`.

---

## 5.  Edge Cases & Testing

| Test | Input | Expected | Why |
|------|-------|----------|-----|
| 1 | `[1]` | `1` | Only subarray `[1]` is valid. |
| 2 | `[2,1]` | `2` | `[2]` and `[1]` both leave a single element array, which is strictly increasing. |
| 3 | `[1,1]` | `1` | Only `[1,1]` leaves an empty array. |
| 4 | `[3,2,1]` | `3` | `[3]`, `[2]`, `[1]` each produce `[2,1]`, `[3,1]`, `[3,2]` – all strictly decreasing, so only the full subarray `[3,2,1]` works. |
| 5 | `[1,2,3,4]` | `10` | All subarrays work. |

Run these tests in any language; all three solutions should match.

---

## 6.  The Good, the Bad, the Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Simplicity** | Easy to read, no auxiliary data structures. | Brute force may look slow at first glance. | Triple nested loops can be intimidating to newcomers. |
| **Performance** | Runs < 1 ms for n ≤ 50. | Still O(n³), cannot scale beyond ~2000 elements. | The inner `if (k >= i && k <= j)` check is repeated many times. |
| **Extensibility** | Straightforward to adapt to other “removal” problems. | Hard to reuse for larger constraints. | The logic isn’t reusable for non‑strictly increasing sequences. |
| **Interview Appeal** | Shows understanding of “prefix + suffix” reasoning. | Might raise a red flag about time complexity. | Shows ability to write clean loops, but missing optimisation discussion may hurt. |

> **Takeaway** – For interview‑style constraints, *readability* often beats *optimality*.  
> If the interviewer explicitly asks for a faster solution, the O(n²) method above is a quick next step.

---

## 7.  Summary – Why You’ll Score Points

- The problem is a classic “remove a segment → check monotonicity” trick.  
- A brute‑force approach is perfect for the given limits.  
- The Java/Python/C++ snippets share the same logical skeleton, proving the solution is *language‑agnostic*.  
- You can always mention the O(n²) optimisation to demonstrate awareness of more efficient patterns.  
- The code is ready to paste into a coding‑interview platform, and you can confidently explain each loop to the interviewer.

Good luck landing that coding‑interview call‑out! 🚀