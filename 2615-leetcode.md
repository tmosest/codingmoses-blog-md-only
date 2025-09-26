---
title: LeetCode 2615. Sum of Distances - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution Code

Below are three complete, ready‑to‑paste solutions – **Java**, **Python**, and **C++** – that compute the array described in LeetCode 2615 “Sum of Distances”.

All three share the same **O(n)** time / **O(n)** space approach:
* Keep two hash tables while scanning the array from left to right –  
  `sumIdx` stores the sum of indices seen so far for each value,  
  `count` stores how many times the value has appeared.  
  The contribution from the left side is `idx * count - sumIdx`.  
* Then scan from right to left doing the same with reversed roles.  
* Finally, the two partial sums give the answer for each position.

---

### Java (JDK 17)

```java
import java.util.HashMap;

public class Solution {
    /**
     * Returns an array where arr[i] is the sum of distances from
     * index i to every other index j that contains the same value.
     */
    public long[] distance(int[] nums) {
        int n = nums.length;
        long[] ans = new long[n];

        // Left-to-right pass
        HashMap<Integer, Long> sumIdx = new HashMap<>();
        HashMap<Integer, Integer> count  = new HashMap<>();
        for (int i = 0; i < n; i++) {
            int val = nums[i];
            long s = sumIdx.getOrDefault(val, 0L);
            int c = count.getOrDefault(val, 0);
            ans[i] += (long) i * c - s;          // contribution from left side
            sumIdx.put(val, s + i);
            count.put(val, c + 1);
        }

        // Right-to-left pass
        sumIdx.clear();
        count.clear();
        for (int i = n - 1; i >= 0; i--) {
            int val = nums[i];
            long s = sumIdx.getOrDefault(val, 0L);
            int c = count.getOrDefault(val, 0);
            ans[i] += s - (long) i * c;          // contribution from right side
            sumIdx.put(val, s + i);
            count.put(val, c + 1);
        }

        return ans;
    }

    // Demo
    public static void main(String[] args) {
        Solution sol = new Solution();
        int[] nums = {1,3,1,1,2};
        long[] res = sol.distance(nums);
        System.out.println(java.util.Arrays.toString(res)); // [5, 0, 3, 4, 0]
    }
}
```

---

### Python (3.11+)

```python
from collections import defaultdict
from typing import List

class Solution:
    def distance(self, nums: List[int]) -> List[int]:
        n = len(nums)
        ans = [0] * n

        # Left to right
        sum_idx = defaultdict(int)
        count = defaultdict(int)
        for i, val in enumerate(nums):
            ans[i] += i * count[val] - sum_idx[val]
            sum_idx[val] += i
            count[val] += 1

        # Right to left
        sum_idx.clear()
        count.clear()
        for i in range(n - 1, -1, -1):
            val = nums[i]
            ans[i] += sum_idx[val] - i * count[val]
            sum_idx[val] += i
            count[val] += 1

        return ans

# Demo
if __name__ == "__main__":
    sol = Solution()
    print(sol.distance([1, 3, 1, 1, 2]))   # [5, 0, 3, 4, 0]
```

---

### C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<long long> distance(vector<int>& nums) {
        int n = nums.size();
        vector<long long> ans(n, 0);

        unordered_map<int, long long> sumIdx; // sum of indices
        unordered_map<int, int> cnt;          // occurrence count

        // Left → Right
        for (int i = 0; i < n; ++i) {
            int v = nums[i];
            ans[i] += 1LL * i * cnt[v] - sumIdx[v];
            sumIdx[v] += i;
            ++cnt[v];
        }

        // Clear maps for the right pass
        sumIdx.clear();
        cnt.clear();

        // Right → Left
        for (int i = n - 1; i >= 0; --i) {
            int v = nums[i];
            ans[i] += sumIdx[v] - 1LL * i * cnt[v];
            sumIdx[v] += i;
            ++cnt[v];
        }

        return ans;
    }
};

int main() {
    Solution s;
    vector<int> nums{1,3,1,1,2};
    auto res = s.distance(nums);
    for (auto x : res) cout << x << ' ';
    cout << '\n'; // 5 0 3 4 0
}
```

All three codes run in **O(n)** time and use **O(n)** auxiliary space – perfect for the constraints (`n ≤ 10^5`).

---

## 2.  Blog Article – “The Good, the Bad, and the Ugly of LeetCode 2615”

### Title
**Sum of Distances (LeetCode 2615) – The Good, the Bad, and the Ugly (Java, Python, C++)**

> *Search keywords*: “LeetCode 2615 solution”, “sum of distances interview”, “hash map prefix sum”, “Java O(n) algorithm”, “Python dictionary solution”, “C++ unordered_map”

---

### Introduction

In a coding interview, you’re often asked to compute a distance‑based metric over an array.  
LeetCode 2615 – **Sum of Distances** – is a classic example that tests two core skills:

1. **Efficient data‑structure use** (hash maps, prefix sums).  
2. **Two‑pass scan logic** – a pattern that surfaces in many interview questions.

Below we walk through the problem, a clean O(n) solution, common pitfalls, and the “ugly” edge cases that can trip even seasoned engineers.

---

### Problem Statement (Simplified)

> You’re given an integer array `nums`.  
> For each index `i`, compute the sum of `|i - j|` over all other indices `j` where `nums[j] == nums[i]`.  
> If no such `j` exists, the answer is `0`.

---

### The Good – Why the O(n) Solution is Elegant

| Why it’s great | What it teaches |
|-----------------|-----------------|
| **Linear time** – only two scans, each `O(n)` | Most interviewees fall back to quadratic `O(n²)` brute force. |
| **No heavy data‑structures** – just a couple of hash maps | Demonstrates mastery of unordered maps and prefix‑sum tricks. |
| **Clear two‑phase logic** – left side + right side | Reinforces the idea of “prefix” and “suffix” contributions that is common in array DP. |
| **Works for 10⁵ elements** | Meets production constraints; no hidden constants. |

#### Pseudocode Overview

```
ans = array(n)
sumIdx, cnt = empty maps

// Left to right
for i = 0 .. n-1:
    ans[i] += i * cnt[nums[i]] - sumIdx[nums[i]]
    sumIdx[nums[i]] += i
    cnt[nums[i]]   += 1

clear maps

// Right to left
for i = n-1 .. 0:
    ans[i] += sumIdx[nums[i]] - i * cnt[nums[i]]
    sumIdx[nums[i]] += i
    cnt[nums[i]]   += 1
```

Each step keeps track of:
* `cnt[value]` – how many times a value has appeared so far.  
* `sumIdx[value]` – the sum of all indices where that value has appeared.

The left pass contributes distances to indices on the left; the right pass completes the symmetric calculation.

---

### The Bad – Common Pitfalls

1. **Using 32‑bit integers for the answer**  
   *Sum of distances* can reach up to `n * n` (≈10¹⁰).  
   **Fix:** Use `long` (Java, C++) or `int64`/`long` (Python’s int is arbitrary precision, but be explicit).

2. **Forgetting to clear the hash maps** between passes  
   *Result:* The right pass uses data from the left pass, leading to wrong answers.

3. **Neglecting the `j ≠ i` condition**  
   In some naive solutions people double‑count the same index; the formula above naturally excludes `i` because `cnt` and `sumIdx` only contain *previous* indices in the left pass, and *future* indices in the right pass.

4. **Using recursion or DFS** – an overkill; the problem is purely linear and iterative.

---

### The Ugly – Edge Cases and Debugging Tips

| Edge case | What to watch out for |
|-----------|-----------------------|
| **All elements equal** (`[1,1,1,…]`) | The answer for each position is `i*(i-1)/2 + (n-1-i)*(n-i)/2`. Your algorithm handles it automatically. |
| **Array of length 1** | Return `[0]`. Both passes still work because the loops simply skip updates. |
| **Very large numbers (`10⁹`)** | They are only keys in the hash map, so memory usage stays `O(n)`. |
| **Negative indices?** | Not allowed by the constraints, but if the problem changed, the absolute value logic would still hold. |
| **Integer overflow in 32‑bit languages** | Explicitly cast to `long` before multiplication. |

**Debugging checklist**

1. Run a small test (`[1,3,1,1,2]`) and compare manually.  
2. Print intermediate `sumIdx` and `cnt` for a couple of indices.  
3. Verify that the left pass results in *negative* contributions before the right pass.  
4. Cross‑check the Python and Java outputs – they should match exactly.

---

### Variations & Follow‑Ups

1. **Sum of absolute differences between *all* pairs** (LeetCode 2121) – this is the same problem.  
2. **Modular arithmetic** – if asked to return the answer modulo `10⁹+7`, just apply `%` after each addition.  
3. **Range queries** – could be extended to dynamic queries where you change one element and recompute. This would require a segment tree or Binary Indexed Tree.

---

### Interview Takeaways

| Skill | How it’s demonstrated |
|-------|-----------------------|
| **Time complexity analysis** | O(n) vs O(n²) |
| **Space optimization** | `O(n)` hash maps |
| **Two‑pass prefix/suffix logic** | Classic pattern |
| **Attention to integer limits** | Use `long`/`int64` |

> **Tip:** When explaining, mention that the *left* contribution is `i * cnt - sumIdx` because you’re effectively summing `i - j` over all previous `j`. The *right* contribution is symmetric. This clear mental model often impresses interviewers.

---

### Final Thoughts

LeetCode 2615 is deceptively simple but a *rich* interview question that touches on core CS concepts.  
The O(n) solution above works across Java, Python, and C++.  
Master this pattern, and you’ll be ready for many distance‑oriented array problems in real‑world interviews and on platforms like LeetCode.

Happy coding, and good luck landing that next job!

---

### About the Author

> *Your name*, a software engineer with 5+ years in backend development, has distilled dozens of LeetCode problems into clean, cross‑language solutions.  
> Follow me on LinkedIn and GitHub for more interview prep, algorithm breakdowns, and career‑growth content.

--- 

### Call‑to‑Action

> If you found this article helpful, **share** it on LinkedIn or Twitter with the hashtag #CodingInterview.  
> Got a different solution or a unique variation? Drop a comment below – let’s keep the conversation going!

---

> **Disclaimer**: The code snippets are provided for educational purposes. For production use, remember to handle null inputs, invalid indices, and other defensive checks.

---

**End of Blog Article**

--- 

> *Meta‑note*: The article includes structured sections, a clear problem summary, an elegant O(n) algorithm, pitfalls, debugging strategies, and interview‑oriented insights—exactly what hiring managers look for in a candidate. By tagging the article with high‑volume search terms (“LeetCode 2615 solution Java”), it will rank in Google search results for job‑seekers preparing for algorithm interviews.