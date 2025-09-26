---
title: LeetCode 2970. Count the Number of Incremovable Subarrays I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Summary  

**Leetcode 2970 – Count the Number of Incremovable Subarrays (Easy)**  

You are given a 0‑indexed array `nums` of positive integers.  
A **subarray** is a contiguous non‑empty slice of `nums`.  
A subarray `[l … r]` is **incremovable** if, after deleting it, the remaining array is **strictly increasing** (empty arrays count as strictly increasing).

Return the number of incremovable subarrays.

| Example | Input | Output | Reason |
|---------|-------|--------|--------|
| 1 | `[1,2,3,4]` | `10` | every non‑empty subarray works |
| 2 | `[6,5,7,8]` | `7`  | only 7 subarrays satisfy the property |
| 3 | `[8,7,6,6]` | `3`  | `[8,7,6]`, `[7,6,6]`, `[8,7,6,6]` |

Constraints: `1 ≤ nums.length ≤ 50`, `1 ≤ nums[i] ≤ 50`.  
These small limits let us solve the problem with a clean O(n²) algorithm.

---

## 2.  The “Good, The Bad, The Ugly”

| Category | What we love | What we hate | What we tolerate |
|----------|--------------|--------------|------------------|
| **The Good** | *Simplicity* – we can reason about the property in only a few lines. |  |  |
| **The Bad** | *O(n³) brute force* is easy to code but still fast enough for `n ≤ 50`. |  |  |
| **The Ugly** | Nested loops with poor variable names (`i, j, k, ok, lst`) make the code unreadable and error‑prone. |  |  |

Our final solution will keep the *good* (clean reasoning), avoid the *bad* (quadratic time, not cubic) and steer clear of the *ugly* (meaningful names, comments).

---

## 3.  Optimal O(n²) Solution – The Idea

For a subarray `[l … r]` to be removable:

1. **Left side** (indices `< l`) must already be strictly increasing.  
   → `prefixInc[l‑1] == true` (or `l == 0`).

2. **Right side** (indices `> r`) must already be strictly increasing.  
   → `suffixInc[r+1] == true` (or `r == n‑1`).

3. If both sides exist, the last element before the removed part must be < the first element after it.  
   → `nums[l‑1] < nums[r+1]`.

We pre‑compute two boolean arrays:

```text
prefixInc[i] = true  iff nums[0 … i] is strictly increasing
suffixInc[i] = true  iff nums[i … n‑1] is strictly increasing
```

Both arrays are built in O(n).  
Then we try every pair `(l, r)` in O(n²) and count the ones that satisfy the three conditions.

Because `n ≤ 50`, this solution is far more than fast enough, but it is also clean and easy to understand.

---

## 4.  Reference Implementations  

Below are full, self‑contained solutions in **Java**, **Python**, and **C++**.  
All three use the optimal O(n²) algorithm described above.

### 4.1 Java

```java
import java.util.*;

class Solution {
    public int incremovableSubarrayCount(int[] nums) {
        int n = nums.length;
        // prefixInc[i] = true if nums[0..i] strictly increasing
        boolean[] prefixInc = new boolean[n];
        boolean inc = true;
        for (int i = 0; i < n; i++) {
            if (i > 0 && nums[i] <= nums[i - 1]) inc = false;
            prefixInc[i] = inc;
        }

        // suffixInc[i] = true if nums[i..n-1] strictly increasing
        boolean[] suffixInc = new boolean[n];
        inc = true;
        for (int i = n - 1; i >= 0; i--) {
            if (i < n - 1 && nums[i] >= nums[i + 1]) inc = false;
            suffixInc[i] = inc;
        }

        int count = 0;
        for (int l = 0; l < n; l++) {
            for (int r = l; r < n; r++) {
                boolean leftOk   = (l == 0)     || prefixInc[l - 1];
                boolean rightOk  = (r == n - 1) || suffixInc[r + 1];
                boolean borderOk = (l == 0) || (r == n - 1) || nums[l - 1] < nums[r + 1];
                if (leftOk && rightOk && borderOk) count++;
            }
        }
        return count;
    }
}
```

---

### 4.2 Python

```python
class Solution:
    def incremovableSubarrayCount(self, nums: List[int]) -> int:
        n = len(nums)

        # prefix_inc[i] = True if nums[0..i] strictly increasing
        prefix_inc = [True] * n
        for i in range(1, n):
            prefix_inc[i] = prefix_inc[i - 1] and nums[i] > nums[i - 1]

        # suffix_inc[i] = True if nums[i..n-1] strictly increasing
        suffix_inc = [True] * n
        for i in range(n - 2, -1, -1):
            suffix_inc[i] = suffix_inc[i + 1] and nums[i] < nums[i + 1]

        ans = 0
        for l in range(n):
            for r in range(l, n):
                left_ok   = l == 0 or prefix_inc[l - 1]
                right_ok  = r == n - 1 or suffix_inc[r + 1]
                border_ok = l == 0 or r == n - 1 or nums[l - 1] < nums[r + 1]
                if left_ok and right_ok and border_ok:
                    ans += 1
        return ans
```

---

### 4.3 C++

```cpp
class Solution {
public:
    int incremovableSubarrayCount(vector<int>& nums) {
        int n = nums.size();

        vector<bool> pref(n, true), suff(n, true);

        // prefix increasing
        for (int i = 1; i < n; ++i)
            pref[i] = pref[i-1] && (nums[i] > nums[i-1]);

        // suffix increasing
        for (int i = n-2; i >= 0; --i)
            suff[i] = suff[i+1] && (nums[i] < nums[i+1]);

        int ans = 0;
        for (int l = 0; l < n; ++l)
            for (int r = l; r < n; ++r) {
                bool leftOk  = (l == 0) || pref[l-1];
                bool rightOk = (r == n-1) || suff[r+1];
                bool borderOk = (l == 0) || (r == n-1) || nums[l-1] < nums[r+1];
                if (leftOk && rightOk && borderOk) ++ans;
            }
        return ans;
    }
};
```

---

## 5.  Blog Article – “The Good, The Bad, The Ugly of Leetcode 2970”

> **Title**: *Leetcode 2970 – Master the “Incremovable Subarray” Problem (Java / Python / C++)*  
> **Meta‑description**: Learn the optimal O(n²) solution for Leetcode 2970, compare it with a brute‑force approach, and see clear code in Java, Python, and C++. Perfect for job‑prep interviews.

### 5.1 Why This Problem Rocks for Interviews

- **Array + Subarray**: Core data‑structure concept.
- **Strictly Increasing Check**: A frequent interview question.
- **Counting Combinations**: Brings DP / combinatorics to the table.
- **Small Constraints**: Allows you to focus on reasoning rather than performance hacks.

### 5.2 Brute Force = “Good? Bad? Ugly?”  

> **The Bad**:  
> ```text
> for (l) for (r) for (k) { delete, check, restore }
> ```
> This code is 10 lines long, but the `i, j, k, ok, lst` monster hides logic in loops.  
> It is **O(n³)**, but with `n ≤ 50` it still runs in < 10 ms.  
> *Verdict*: Works, but we want something *more elegant*.

### 5.3 The “Ugly” – Why the Brute‑Force Is Hard to Read

Nested loops with single‑letter variables quickly become a maintenance nightmare. If you need to debug it, you’ll spend 20 % of the time guessing what `ok` or `lst` represent.

### 5.4 Our Clean O(n²) Approach

> **The Good** – we prove that a subarray is removable iff:  
> 1️⃣ Left prefix is already increasing,  
> 2️⃣ Right suffix is already increasing,  
> 3️⃣ Border elements satisfy `nums[l‑1] < nums[r+1]`.  
>  
> **The Optimal Algorithm**: Build two prefix/suffix boolean arrays in linear time, then iterate over every `(l, r)` pair once.

**Proof Sketch**  
When we delete `[l … r]`, the remaining sequence is `nums[0…l-1] + nums[r+1…n-1]`.  
Each side must be strictly increasing on its own; otherwise the result can never be increasing.  
If both sides exist, the last element before deletion (`nums[l-1]`) must be smaller than the first element after deletion (`nums[r+1]`).  
These are *exactly* the conditions implemented.

### 5.5 Complexity Analysis

| Approach | Time | Space | Comments |
|----------|------|-------|----------|
| Brute‑Force | O(n³) | O(1) | Only possible because `n` is tiny. |
| Optimal O(n²) | **O(n²)** | **O(n)** | Clean, easy to explain, no loops inside loops that are hidden. |

---

### 5.6 How to Explain This in a Live Interview

> **Step‑by‑step**  
> 1. “Let’s pre‑compute whether each prefix and suffix is already strictly increasing.”  
> 2. “Now we just need to check three simple boolean conditions for each subarray.”  
> 3. “The whole thing runs in O(n²) which is trivial for the constraints.”  

This concise explanation shows you understand the problem deeply and can translate it into code quickly.

---

### 5.7 Take‑Away Code Snippets

The reference implementations above are your go‑to solutions.  
Pick the language you’ll use in the interview and practice typing them quickly.  

**Tip**: Keep variable names descriptive (`prefixInc`, `suffixInc`, `borderOk`) and add a single comment block at the start of each file.

---

### 5.8 Final Words

- **Practice**: Run the solution on the three examples, try edge cases (single element, already increasing array, all equal elements).  
- **Explain**: During the interview, articulate the logic before coding.  
- **Review**: The optimal O(n²) method is a *pattern* that will surface in many other subarray‑counting problems.

> *Ready for the next Leetcode? The “incremovable subarray” trick is a solid interview staple. Keep it simple, keep it fast, and you’ll pass the array‑centric portion of most coding interviews.*  

--- 

### 5.9 Related Keywords

- Leetcode 2970  
- Incremovable subarray  
- Brute force array counting  
- O(n²) algorithm  
- Java interview problem  
- Python array solution  
- C++ interview code  

--- 

Happy coding, and good luck on your next interview!