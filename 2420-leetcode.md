---
title: LeetCode 2420. Find All Good Indices - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 2420 – Find All Good Indices  
**Languages:** Java | Python | C++  
**Complexity:** **Time** O(n) **Space** O(n) (can be reduced to O(1) with a sliding‑window trick)  

---

### 1.1  Problem Recap  

Given an array `nums` (size `n`) and an integer `k`, an index `i` is **good** if  

* `k ≤ i < n-k`  
* The `k` elements *before* `i` are in **non‑increasing** order  
* The `k` elements *after* `i` are in **non‑decreasing** order  

Return all good indices in ascending order.

> **Example**  
> `nums = [2,1,1,1,3,4,1], k = 2 → [2,3]`

---

### 1.2  Intuition  

The naive way would compare each window of length `k` around every candidate `i` – that is O(n k) and will TLE.  
The key is **pre‑computing** how long a monotone run extends from each position:

| left side | right side |
|-----------|------------|
| For every index `i` store `decLeft[i]` – how many consecutive non‑increasing elements end at `i` (including `i`). |
| For every index `i` store `incRight[i]` – how many consecutive non‑decreasing elements start at `i` (including `i`). |

Once we have those two arrays, index `i` is good iff  
`decLeft[i-1] ≥ k` **and** `incRight[i+1] ≥ k`.

Both arrays are built in one linear scan each → O(n) time, O(n) memory.  
The check for every candidate is O(1), so the whole algorithm is O(n) time, O(n) memory.

---

### 1.3  Implementation  

Below are clean, commented implementations in **Java, Python and C++**.

---

#### 1.3.1  Java

```java
import java.util.*;

public class Solution {
    public List<Integer> goodIndices(int[] nums, int k) {
        int n = nums.length;
        List<Integer> res = new ArrayList<>();

        // 1. prefix array: length of non‑increasing run ending at i
        int[] decLeft = new int[n];
        decLeft[0] = 1;                         // single element
        for (int i = 1; i < n; i++) {
            decLeft[i] = (nums[i - 1] >= nums[i]) ? decLeft[i - 1] + 1 : 1;
        }

        // 2. suffix array: length of non‑decreasing run starting at i
        int[] incRight = new int[n];
        incRight[n - 1] = 1;
        for (int i = n - 2; i >= 0; i--) {
            incRight[i] = (nums[i] <= nums[i + 1]) ? incRight[i + 1] + 1 : 1;
        }

        // 3. check each possible centre
        for (int i = k; i <= n - k - 1; i++) {
            if (decLeft[i - 1] >= k && incRight[i + 1] >= k) {
                res.add(i);
            }
        }

        return res;
    }
}
```

---

#### 1.3.2  Python

```python
from typing import List

class Solution:
    def goodIndices(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)

        # prefix: longest non‑increasing run ending at i
        dec_left = [0] * n
        dec_left[0] = 1
        for i in range(1, n):
            dec_left[i] = dec_left[i - 1] + 1 if nums[i - 1] >= nums[i] else 1

        # suffix: longest non‑decreasing run starting at i
        inc_right = [0] * n
        inc_right[-1] = 1
        for i in range(n - 2, -1, -1):
            inc_right[i] = inc_right[i + 1] + 1 if nums[i] <= nums[i + 1] else 1

        # collect good indices
        ans = []
        for i in range(k, n - k):
            if dec_left[i - 1] >= k and inc_right[i + 1] >= k:
                ans.append(i)
        return ans
```

---

#### 1.3.3  C++ (C++17)

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    vector<int> goodIndices(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> decLeft(n), incRight(n);

        // prefix
        decLeft[0] = 1;
        for (int i = 1; i < n; ++i) {
            decLeft[i] = (nums[i-1] >= nums[i]) ? decLeft[i-1] + 1 : 1;
        }

        // suffix
        incRight[n-1] = 1;
        for (int i = n-2; i >= 0; --i) {
            incRight[i] = (nums[i] <= nums[i+1]) ? incRight[i+1] + 1 : 1;
        }

        vector<int> ans;
        for (int i = k; i <= n-k-1; ++i) {
            if (decLeft[i-1] >= k && incRight[i+1] >= k)
                ans.push_back(i);
        }
        return ans;
    }
};
```

---

## 2.  Blog Article – “The Good, the Bad, and the Ugly of LeetCode 2420”

> **Title**: *“LeetCode 2420 – Find All Good Indices: The Good, the Bad, and the Ugly”*  
> **Target Keywords**: LeetCode 2420, Find All Good Indices, algorithm interview, Java solution, Python solution, C++ solution, job interview coding

---

### 2.1  Introduction  

If you’re hunting for a software engineering role, you’ve probably stared at the same LeetCode problems over and over again. One of those “medium”‑tier questions is **2420. Find All Good Indices**. While the statement is straightforward, naïve solutions can be deceptively slow.  

In this article we’ll walk through:

1. **What makes this problem “good”** – why it’s a great interview question.  
2. **The good solution** – a clean O(n) DP/prefix‑suffix approach (Java/Python/C++).  
3. **The bad approaches** – common pitfalls that lead to TLE or MLE.  
4. **The ugly** – edge cases and how to handle them without over‑engineering.  
5. **Practical take‑aways** for your next interview.

---

### 2.2  Why This Problem is Worth Studying  

| Aspect | Why it matters in an interview |
|--------|--------------------------------|
| **Array + Two‑Pointer DP** | Demonstrates you can transform an “O(n·k)” naive brute‑force into an O(n) solution. |
| **Monotonic Runs** | Shows understanding of prefix/suffix techniques – a staple in many interview problems. |
| **Space/Time Trade‑offs** | Opportunity to discuss reducing O(n) memory to O(1) with a sliding window. |
| **Edge‑case Awareness** | `k = 1`, array of all equal numbers, array with alternating peaks – good for discussion. |

---

### 2.3  The Good Solution – Prefix + Suffix DP  

**Core Idea**  
*Pre‑compute, for every index, how far the monotone property extends on both sides.*

1. **Left prefix** – `decLeft[i]`: length of the longest non‑increasing subarray that ends at `i`.  
   ```text
   decLeft[i] = (nums[i-1] >= nums[i]) ? decLeft[i-1] + 1 : 1
   ```
2. **Right suffix** – `incRight[i]`: length of the longest non‑decreasing subarray that starts at `i`.  
   ```text
   incRight[i] = (nums[i] <= nums[i+1]) ? incRight[i+1] + 1 : 1
   ```
3. **Check each candidate** – for `k ≤ i < n-k`:  
   ```text
   if decLeft[i-1] ≥ k and incRight[i+1] ≥ k → i is good
   ```

**Why it works**  
The left side of a good index must contain `k` consecutive elements that never rise. That exactly means the run ending at `i-1` is at least `k`. The same logic applies to the right side.

**Complexities**  
- **Time**: `O(n)` (two linear scans + one linear check)  
- **Space**: `O(n)` for the two auxiliary arrays (can be trimmed to O(1) if you prefer the sliding‑window version).

**Code snippets**  
*(see the Java, Python, and C++ implementations above)*

---

### 2.4  The Bad Approaches – Common Pitfalls  

| Approach | Why it fails |
|----------|--------------|
| **Brute force nested loops** – for each `i` scan left `k` elements and right `k` elements | O(n·k) → TLE when `n` = 10⁵ and `k` ≈ n/2 |
| **Using `ArrayList<Integer>` for prefixes without pre‑allocating** | Extra overhead, but not catastrophic. |
| **Incorrect index bounds** – off‑by‑one errors when accessing `decLeft[i-1]` or `incRight[i+1]` | Leads to `ArrayIndexOutOfBoundsException`. |
| **Misinterpreting “non‑increasing” vs “non‑decreasing”** | Swapping the comparison signs breaks the logic. |

**Takeaway:**  
Always double‑check boundary conditions; the problem explicitly restricts `i` to `k ≤ i < n-k`.

---

### 2.5  The Ugly – Edge Cases & Corner Conditions  

| Edge case | What to watch for | Fix |
|-----------|------------------|-----|
| `k = 1` | The run length requirement becomes trivial; still need to ensure we don’t access out‑of‑bounds indices. | Keep loops as `k ≤ i <= n-k-1`. |
| Array of equal numbers | Every side satisfies monotonicity; answer is all indices in range. | The DP formulas correctly handle `>=` and `<=` comparisons. |
| Alternating peaks (`1 3 2 4 3 5 …`) | No `k` consecutive non‑decreasing or non‑increasing runs. | The DP arrays will all be `1`; algorithm returns empty list as expected. |
| Array length exactly `2·k + 1` | Only one candidate `i = k`. | The loop still runs; ensure you don’t miss the last valid `i`. |
| Large `n` but small memory limit (e.g., 256 MB) | Two integer arrays might consume ~8 MB each → still fine. | If tight, implement the O(1) sliding window: keep only the current `k`‑run lengths while scanning. |

---

### 2.6  Practical Tips for Your Interview  

1. **Explain your plan first** – write the two DP passes on the whiteboard before coding.  
2. **Highlight the O(n) optimisation** – interviewers love to hear “I avoided nested loops.”  
3. **Mention space optimisation** – show awareness of O(1) variant.  
4. **Discuss testing** – ask the interviewer to help think of corner cases.  
5. **Keep your code readable** – even in an interview, clean variable names (`decLeft`, `incRight`) and concise logic help.  

---

### 2.7  Conclusion  

LeetCode 2420 is a “medium” problem that hides a neat DP trick. The **good** solution is a textbook prefix‑suffix approach that runs in linear time. The **bad** approaches teach you to watch for TLE and off‑by‑one bugs. The **ugly** edge cases remind us that even a clean algorithm can fail if you miss a single bound.

Mastering this problem gives you a reusable pattern – prefix/suffix for monotonic constraints – and demonstrates you’re ready to take on real‑world interview challenges. Happy coding, and best of luck on your next technical interview!

---

### 2.8  Resources & Further Reading  

- LeetCode Discussion on Problem 2420  
- “Algorithmic Toolbox” – Prefix & Suffix Techniques  
- “Cracking the Coding Interview” – Two‑Pointer DP examples  
- GitHub Gist with all three language solutions (Java, Python, C++)

---

*Author*: *Your Name*, *Software Engineer & Tech Recruiter*  
*Published on*: *2024‑04‑27*

--- 

> **Meta‑Note**: The article is structured to provide value to readers who might be on the fence about the problem’s difficulty, and it ends with actionable interview strategies. By incorporating the target keywords naturally in headings, body text and code comments, the article is SEO‑friendly and ready to rank on search engines for the specified tags.