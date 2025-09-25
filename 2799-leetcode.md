---
title: LeetCode 2799. Count Complete Subarrays in an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code – 3 Languages

Below are clean, production‑ready solutions for LeetCode 2799 – **Count Complete Subarrays in an Array**.  
All three implementations use the same O(n) sliding‑window / two‑pointer idea and run in linear time and constant extra space (O(k) where *k* = number of distinct values in `nums`).

> **Problem Recap**  
> A *complete* subarray contains **exactly** the same number of distinct elements as the whole array.  
> Return the number of such subarrays.

---

### 1.1  Java (≥ Java 8)

```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;

public class Solution {
    public int countCompleteSubarrays(int[] nums) {
        // 1️⃣ Count distinct values in the entire array
        Set<Integer> all = new HashSet<>();
        for (int v : nums) all.add(v);
        int target = all.size();          // required number of distinct values

        // 2️⃣ Sliding window: map of element → frequency
        Map<Integer, Integer> freq = new HashMap<>();
        int left = 0, res = 0;

        for (int right = 0; right < nums.length; right++) {
            freq.merge(nums[right], 1, Integer::sum);

            // 3️⃣ While window already contains all distinct values
            while (freq.size() == target) {
                // All subarrays that start in [left, right] and end at right
                // are valid because shrinking from the left cannot reduce
                // the number of distinct values below target.
                res += nums.length - right;

                // Move left pointer to shrink window
                int lVal = nums[left++];
                int cnt = freq.get(lVal) - 1;
                if (cnt == 0) freq.remove(lVal);
                else freq.put(lVal, cnt);
            }
        }
        return res;
    }
}
```

---

### 1.2  Python 3

```python
from collections import defaultdict
from typing import List

class Solution:
    def countCompleteSubarrays(self, nums: List[int]) -> int:
        # 1️⃣ Distinct count of whole array
        target = len(set(nums))

        freq = defaultdict(int)          # element -> frequency in window
        left = 0
        res = 0

        for right, val in enumerate(nums):
            freq[val] += 1

            while len(freq) == target:
                # Every subarray that ends at 'right' and starts in [left, right]
                # is complete
                res += len(nums) - right

                # shrink from left
                l_val = nums[left]
                freq[l_val] -= 1
                if freq[l_val] == 0:
                    del freq[l_val]
                left += 1

        return res
```

---

### 1.3  C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int countCompleteSubarrays(vector<int>& nums) {
        // 1️⃣ Distinct elements in whole array
        unordered_set<int> all(nums.begin(), nums.end());
        int target = all.size();          // required distinct count

        unordered_map<int,int> freq;      // element -> count in window
        int left = 0, res = 0;

        for (int right = 0; right < (int)nums.size(); ++right) {
            freq[nums[right]]++;

            // 2️⃣ When window already has all distinct values
            while ((int)freq.size() == target) {
                res += nums.size() - right;   // all subarrays ending at 'right'

                // shrink from left
                int l = nums[left++];
                if (--freq[l] == 0) freq.erase(l);
            }
        }
        return res;
    }
};
```

All three snippets compile with a recent compiler/interpreter and run in **O(n)** time, **O(k)** auxiliary memory.

---

## 2.  Blog Article – “The Good, the Bad, and the Ugly of Counting Complete Subarrays”

> **Title:** *The Good, The Bad, and The Ugly of Counting Complete Subarrays – A LeetCode 2799 Deep‑Dive*  
> **Meta‑Description:** Master LeetCode 2799 “Count Complete Subarrays” with a sliding‑window solution. Learn the pitfalls of the O(n²) brute force, the elegance of two‑pointer technique, and real‑world interview tips.  

### 2.1  Introduction

When the recruiter asks you to *count complete subarrays*, they’re not just checking your knowledge of arrays – they’re probing your ability to think about **distinctness**, **windows**, and **optimization**.  
LeetCode problem 2799 (“Count Complete Subarrays in an Array”) is a classic example where a naïve double loop gives a correct answer but will be rejected on the biggest test cases.  

This article walks through the **good** approach (O(n) sliding window), the **bad** approach (O(n²) brute force), and the **ugly** pitfalls that frequently trip up candidates.  

---

### 2.2  Problem Recap

Given `nums` (1 ≤ n ≤ 1000, 1 ≤ nums[i] ≤ 2000), a subarray is *complete* if it contains **exactly** as many distinct elements as the whole array.  
Return the number of complete subarrays.

**Examples**

| `nums` | `complete` subarrays | Output |
|--------|----------------------|--------|
| [1,3,1,2,2] | [1,3,1,2], [1,3,1,2,2], [3,1,2], [3,1,2,2] | 4 |
| [5,5,5,5] | all 10 subarrays | 10 |

---

### 2.3  The Bad – O(n²) Brute Force

#### 2.3.1  Why It Works

```text
for every l from 0 to n-1:
    for every r from l to n-1:
        count distinct in nums[l..r]
        if equal to total distinct: ans++
```

Because we literally test every possible subarray, correctness is guaranteed.

#### 2.3.2  Drawbacks

* **Time complexity O(n²)** – with *n* = 1000, the loop runs ~500 000 iterations.  
* **Worst‑case memory** – we use a hash set per inner loop, costing O(n) each time.  
* **LeetCode verdict** – the problem’s “hard” version (when *n* = 2 × 10⁵ in interview‑style sites) will timeout.  

**Interview takeaway:** If you hand this solution to a recruiter, say *“I realize this is too slow for large inputs, so let’s find a linear‑time trick.”*  That shows self‑awareness.

---

### 2.4  The Good – O(n) Sliding Window

#### 2.4.1  Intuition

1. **Pre‑compute** the target distinct count `k` for the entire array.  
2. Expand a **right** pointer while keeping a frequency map of the current window.  
3. When the window already contains *k* distinct values, **every subarray that ends at the current right index and starts anywhere between left and right is complete** – because shrinking from the left cannot reduce distinctness below `k`.  
4. Shrink the window from the left until distinct count drops below `k` again.

#### 2.4.2  Pseudocode

```
k = distinctCount(nums)
freq = empty map
left = 0
ans = 0

for right = 0 .. n-1:
    freq[nums[right]] += 1

    while freq.size() == k:
        ans += n - right      // all endings at right are good
        lVal = nums[left]
        freq[lVal] -= 1
        if freq[lVal] == 0: remove lVal
        left += 1

return ans
```

#### 2.4.3  Why It Works

* When `freq.size() == k`, the window already has **all** distinct values.  
* Moving the left pointer **removes** an element that is still present in the window (its frequency > 1) **doesn’t** reduce the number of distinct values.  
* Only when we delete the **last** occurrence of a value do we need to stop shrinking.  
* Therefore, every subarray that **ends at `right`** and **starts in `[left, right]`** is guaranteed to be complete – no need to check each one individually.

#### 2.4.4  Complexity

| Metric | Formula | Reasoning |
|--------|---------|-----------|
| **Time** | `O(n)` | Each element enters and leaves the window once. |
| **Space** | `O(k)` | Frequency map holds at most the distinct count of the array. |
| **Worst‑case** | `k = min(n, 2000)` | Still constant for the given constraints. |

---

### 2.5  The Ugly – Common Pitfalls

| Pitfall | What Happens | Fix |
|---------|--------------|-----|
| **Edge duplicates at the start or end** | Shrinking may delete a value prematurely, breaking the distinct count. | Use `freq.merge` / `freq[l]--` carefully; only erase when count hits zero. |
| **Using a set for `freq.size()`** | `set.size()` is O(1) in C++/Java but O(k) in Python. In Python, use `len(freq)` – it’s still O(1) because the dict keeps a count of keys. | Ensure you use the appropriate data structure. |
| **Off‑by‑one in result addition** | Adding `n - right` vs. `n - right - 1` is a common source of 1‑off errors. | Think of the window as `[left, right]`. All subarrays that end at `right` have `n - right` possible endings beyond `right`. |
| **Not resetting left after loop** | In some test cases (e.g., all elements equal), the while loop never runs; we still need to handle the final window. | After the main loop, you can run one more shrink‑loop or rely on the `while` inside the loop to handle it. |

---

### 2.6  Interview Tips

| Tip | Why It Helps |
|-----|--------------|
| **Ask clarifying questions** | “Is the array sorted?” or “Can values repeat?” Clarifies assumptions and saves you from writing unnecessary code. |
| **Explain the sliding window logic aloud** | Recruiters love to see you articulate “if we shrink from the left, distinctness stays the same until we remove the last occurrence of a value.” |
| **Show a small hand‑tracing** | Even on paper, write a two‑pointer trace for `[1, 2, 1]` to demonstrate how `res += n - right` works. |
| **Mention corner cases** | All elements the same, all elements distinct, duplicates clustered at both ends. |
| **Mention complexity** | “O(n) time, O(k) space. Works for up to 2 × 10⁵ elements in real interview settings.” |

---

### 2.7  Summary

| Approach | Complexity | Where It shines |
|----------|------------|-----------------|
| **Brute‑Force** | **O(n²)** | Small inputs, quick sanity checks |
| **Sliding Window** | **O(n)** | Production‑grade, interview‑ready |
| **Ugly edge cases** | – | Requires careful pointer shrink logic |

Adopting the sliding‑window method demonstrates that you can **optimize**, **handle duplicates**, and **use hash maps** effectively. Recruiters often use LeetCode 2799 as a litmus test for *array + distinct element* problems, so mastering it gives you a strong edge.

---

### 2.8  Closing – Land That Software Engineer Job

If you can write the O(n) solution, explain the **two‑pointer intuition**, and discuss pitfalls on the whiteboard, you’ll impress both technical and hiring managers.  

Remember:

1. **Pre‑compute** the target distinct count once.  
2. **Expand** the right pointer, **contract** when you hit the target.  
3. **Count** all subarrays that end at the current right index in one jump.  

Add this technique to your interview toolbox, and you’ll be ready for LeetCode 2799 and any *counting‑subarray* follow‑ups. Good luck – you’re one step closer to landing that software engineer role!  

---  

### 2.9  SEO Keywords

```
Count Complete Subarrays, LeetCode 2799, sliding window, two pointers, hash map, Java solution, Python solution, C++ solution, coding interview, software engineer interview, algorithm interview, distinct elements, array problem, interview question, job interview preparation, coding challenge, efficient algorithm, job interview help
```

Feel free to copy‑paste the code snippets into your favorite IDE, run the sample tests, and add them to your portfolio or LinkedIn “Projects” section – recruiters love clean, optimized code!