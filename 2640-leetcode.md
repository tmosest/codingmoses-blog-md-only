---
title: LeetCode 2640. Find the Score of All Prefixes of an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2640. Find the Score of All Prefixes of an Array  
**A Complete, SEO‑Optimized Blog Post + 3‑Language Solution**  

> *LeetCode 2640 – “Find the Score of All Prefixes of an Array”*  
> *Java, Python, C++ implementations*  
> *Interview‑ready, O(n) time & O(1) space solution*  

---

## 1. Problem Recap  

You’re given a 0‑indexed integer array **nums** of length *n* (1 ≤ *n* ≤ 10⁵).  
For each prefix `nums[0..i]` you must:

1. Build the *conversion* array  
   `conver[j] = nums[j] + max(nums[0..j])`  
2. Sum the conversion array → the *score* of that prefix.

Return an array `ans` where `ans[i]` is the score of the prefix ending at index `i`.

> **Example**  
> nums = `[2,3,7,5,10]` → ans = `[4,10,24,36,56]`

---

## 2. Why It Looks Hard (The “Ugly” Part)

At first glance, you might think you need to recompute the maximum for every prefix, leading to **O(n²)**.  
The twist is that **max(nums[0..j])** can be maintained *online* as you iterate – a single pass is enough.

---

## 3. The Elegant “Good” Solution – Prefix Sum + Running Maximum

| Step | What we keep | Why it works |
|------|--------------|--------------|
| 1    | `currMax` – maximum value seen so far | `max(nums[0..i])` is exactly the running maximum. |
| 2    | `ans[i]` – running sum of conversion values | Each new conversion value is `nums[i] + currMax`; add it to previous total. |

That’s it – **one loop** over the array.

### Pseudocode

```
currMax = 0
totalScore = 0
for each num in nums:
    currMax = max(currMax, num)
    totalScore += num + currMax
    store totalScore in answer array
```

No extra array for the conversion values is required – we compute them on the fly.  
Complexities:

- **Time**: `O(n)` – one pass.  
- **Space**: `O(1)` – only a few scalars plus the output array.

---

## 4. Full Implementations

Below are clean, ready‑to‑copy solutions in **Java**, **Python**, and **C++**.

> **Note:** All three use `long`/`long long` to avoid overflow (nums[i] ≤ 10⁹, n ≤ 10⁵ → sum ≤ 2 × 10¹⁴).

---

### 4.1 Java

```java
import java.util.*;

public class Solution {
    public long[] findPrefixScore(int[] nums) {
        int n = nums.length;
        long[] ans = new long[n];

        int currMax = 0;
        long total = 0;
        for (int i = 0; i < n; i++) {
            currMax = Math.max(currMax, nums[i]);
            total += nums[i] + currMax;
            ans[i] = total;
        }
        return ans;
    }

    // Simple driver for quick testing
    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {2, 3, 7, 5, 10};
        System.out.println(Arrays.toString(s.findPrefixScore(nums)));
    }
}
```

---

### 4.2 Python

```python
from typing import List

class Solution:
    def findPrefixScore(self, nums: List[int]) -> List[int]:
        ans = []
        curr_max = 0
        total = 0
        for num in nums:
            curr_max = max(curr_max, num)
            total += num + curr_max
            ans.append(total)
        return ans

# Quick test
if __name__ == "__main__":
    s = Solution()
    print(s.findPrefixScore([2, 3, 7, 5, 10]))
```

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<long long> findPrefixScore(vector<int>& nums) {
        vector<long long> ans;
        ans.reserve(nums.size());

        int currMax = 0;
        long long total = 0;
        for (int num : nums) {
            currMax = max(currMax, num);
            total += num + currMax;
            ans.push_back(total);
        }
        return ans;
    }
};

// Simple test harness
int main() {
    Solution s;
    vector<int> nums = {2, 3, 7, 5, 10};
    auto res = s.findPrefixScore(nums);
    for (auto v : res) cout << v << " ";
    cout << endl;
}
```

---

## 5. Edge Cases & Common Pitfalls

| Case | What to watch out for |
|------|-----------------------|
| **Large numbers** | Use 64‑bit (`long` / `long long`). |
| **All equal elements** | `currMax` stays the same; algorithm still O(n). |
| **Strictly decreasing array** | `currMax` updates only at the first element. |
| **Input length = 1** | Works: ans[0] = 2 × nums[0]. |
| **Negative numbers** (not in constraints) | Algorithm still valid, but `currMax` might be negative. |

---

## 6. Why This Solution Wins Interviews

1. **Simplicity** – one loop, minimal variables.  
2. **Optimality** – passes all leetcode limits (10⁵ elements).  
3. **Readability** – clear variable names (`currMax`, `total`).  
4. **Language Agnostic** – the logic is the same across Java, Python, C++.  

Interviewers often ask: *“Can you avoid recomputing the maximum for each prefix?”* – answer with the running maximum trick.

---

## 7. Further Enhancements (Optional)

| Idea | Benefit | Trade‑off |
|------|---------|-----------|
| **Streaming** – if input comes as a stream, you can output scores immediately. | No extra array needed. | Need to handle output formatting. |
| **Parallel prefix sum** – for massive arrays on GPU. | Speed‑up on large data. | Overkill for LeetCode constraints. |

---

## 8. Final Takeaways

- **Prefix Score** = cumulative sum of `nums[i] + runningMax`.  
- Maintain a single `currMax` and a running `total`.  
- **O(n) time, O(1) auxiliary space** – the cleanest solution.  
- Works flawlessly in Java, Python, and C++.  

Feel confident to drop this into your interview toolkit!

---

## 9. SEO Tags & Keywords

- LeetCode 2640
- Find the Score of All Prefixes of an Array
- Prefix sum algorithm
- Java prefix sum solution
- Python prefix sum solution
- C++ prefix sum solution
- Interview coding problems
- O(n) time complexity
- O(1) space complexity
- Running maximum
- Algorithmic problem solving

Happy coding, and good luck landing that job!