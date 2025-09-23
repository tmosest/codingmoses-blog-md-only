---
title: LeetCode 2216. Minimum Deletions to Make Array Beautiful - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 2216 – Minimum Deletions to Make Array Beautiful  
**Solution in Java, Python & C++** | **O(1) Greedy** | **LeetCode‑ready**  

---

### 1. Problem Recap

> **Beautiful array**  
> * `nums.length` is even  
> * For every even index `i`, `nums[i] != nums[i+1]`  

We may delete any number of elements (the remaining elements keep their relative order).  
Return the **minimum** deletions needed.

```
Input:  nums = [1,1,2,3,5]
Output: 1   // delete one of the two 1s
```

---

### 2. Key Observation  

When we build the “beautiful” array from left to right we only ever need to know:

* **What the last element at an even index is** (`pre`).  
  - If the next number equals `pre`, we must delete it (otherwise `nums[i] == nums[i+1]`).
  - If it differs, we can keep it and *reset* the “even index” marker (the next slot is odd).

The algorithm never needs to look farther ahead – a greedy decision is optimal.

---

### 3. Greedy, O(1) Space Algorithm  

| step | action | explanation |
|------|--------|-------------|
| Start | `res = 0` (deletion count), `pre = -1` (no even‑index element yet) | |
| Iterate over each element `a` | *If `a == pre` → delete it (`res++`).*<br>*Else* → set `pre = (pre < 0 ? a : -1)` | When we keep a number at an even position we “reset” `pre` to `-1` so that the next element can be placed at the odd position. |
| After loop | If `pre >= 0` (the array length is odd) → `res++` (delete the last element) | A beautiful array must be even‑sized. |

---

### 4. Correctness Proof (Sketch)

*We prove by induction that the algorithm produces a valid beautiful array with the minimal number of deletions.*

1. **Base**: Before processing any element, the empty array is beautiful.  
2. **Induction step**:  
   *Assume the processed prefix (after `k` iterations) is the **shortest** beautiful subsequence of the first `k` numbers.*  
   * For the `k+1`‑th number `a`:  
     *If `a == pre`*: keeping `a` would violate the adjacency rule at an even index → any beautiful subsequence must delete `a`. The algorithm deletes it, keeping the minimality property.  
     *If `a != pre`*:  
       - If `pre` is negative, `a` can safely occupy the current even slot → the algorithm keeps it.  
       - If `pre` is non‑negative, that means the previous element was placed at an odd slot, so `a` can safely occupy the next even slot, resetting `pre`.  
     In both cases the algorithm keeps `a` when it does not hurt feasibility, preserving minimal deletions.

3. **Post‑processing**: If `pre >= 0` after the loop, the current length is odd. Any beautiful array must be even‑sized, so the last element must be deleted. The algorithm deletes it, still minimal.

Thus the algorithm yields the optimal solution.

---

### 5. Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time   | **O(n)** | **O(n)** | **O(n)** |
| Space  | **O(1)** | **O(1)** | **O(1)** |

`n` ≤ 10⁵, so the linear scan is easily fast enough.

---

### 6. Reference Implementations

> **Note**: All three versions follow the same greedy logic and can be copy‑pasted into LeetCode.

#### 6.1 Java (LeetCode signature)

```java
class Solution {
    public int minDeletion(int[] nums) {
        int deletions = 0;   // answer so far
        int pre = -1;        // last element at an even index (-1 means none)

        for (int a : nums) {
            if (a == pre) {          // would break the beauty rule
                deletions++;
            } else {
                // keep a
                pre = (pre < 0) ? a : -1;  // if we just placed an even element, reset
            }
        }

        // If we ended with an odd length array, delete the last element
        if (pre >= 0) {
            deletions++;
        }
        return deletions;
    }
}
```

#### 6.2 Python 3

```python
class Solution:
    def minDeletion(self, nums: List[int]) -> int:
        deletions = 0
        pre = -1                # last even‑index element

        for a in nums:
            if a == pre:          # would violate the rule
                deletions += 1
            else:
                pre = a if pre < 0 else -1  # keep or reset

        if pre >= 0:              # odd length at the end
            deletions += 1
        return deletions
```

#### 6.3 C++17

```cpp
class Solution {
public:
    int minDeletion(vector<int>& nums) {
        int deletions = 0;
        int pre = -1;                       // last even‑index element

        for (int a : nums) {
            if (a == pre) {
                ++deletions;                // must delete
            } else {
                pre = (pre < 0) ? a : -1;   // keep or reset
            }
        }

        if (pre >= 0) {                     // odd length
            ++deletions;
        }
        return deletions;
    }
};
```

---

### 7. “Good, Bad, and Ugly” – What to Watch Out For

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Logic** | Pure greedy, no DP or stack required | Mis‑thinking “reset” flag → infinite loop | Forgetting to handle the odd‑length final case (common bug) |
| **Edge Cases** | Empty array → 0 deletions | Single element → 1 deletion (must delete) | Very long run of equal numbers → all but one must be deleted |
| **Testing** | `[1]`, `[1,1]`, `[1,2]`, `[1,1,1,1]` | Mixed equal / unequal patterns | Random large input to confirm `O(n)` speed |

---

### 8. Interview‑Ready Tips

1. **Explain the invariant**: “`pre` holds the value that, if repeated, would break the beauty rule.”  
2. **Show the proof**: Use induction or a small counter‑example argument.  
3. **Talk about space**: O(1) is a strong selling point.  
4. **Mention edge‑case handling**: The final odd‑length deletion is the subtlety that often trips candidates.  
5. **Show sample runs**: Walk through `[1,1,2,2,3,3]` and highlight where each deletion occurs.

---

### 9. Final Take‑away

The greedy, O(1) solution for **LeetCode 2216** is clean, fast, and perfect for a coding interview.  
Its elegance lies in recognizing that *only the last even‑indexed element matters* and that the array’s parity decides if an extra deletion is needed at the end.

Happy coding! 🚀

---  

**Keywords**: LeetCode 2216, Minimum Deletions to Make Array Beautiful, O(1) Greedy, Java solution, Python solution, C++ solution, interview prep, array beauty, time complexity, space complexity.