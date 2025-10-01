---
title: LeetCode 2799. Count Complete Subarrays in an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2799 – Count Complete Subarrays  
**Medium** | **Sliding Window | Hash‑Map | O(n)**

> You are given an array `nums` consisting of positive integers.  
> A subarray is *complete* if the number of distinct elements in the subarray
> equals the number of distinct elements in the whole array `nums`.  
> Return the number of complete subarrays.

---

### Why This Problem Is a Great Interview Question

| ✅ Good | ⚠️ Bad | ❌ Ugly |
|--------|--------|---------|
| *Small input* (≤ 1000) → easy to brute‑force, but we’ll show an optimal O(n) solution | *“You have to use a set”* → many candidates over‑complicate with nested loops | *“I tried the two‑pointer approach but my code keeps throwing an error”* → missing edge‑case handling (empty window, moving `left` correctly) |

The problem tests:

* Understanding of *distinct elements* in a sliding window
* Ability to transform a brute‑force O(n²) solution into a linear one
* Correctly handling maps / counters while shrinking the window

---

## 1️⃣ High‑Level Idea

1. **Count the distinct elements in the whole array** – let this be `k`.  
2. Use a *two‑pointer sliding window* (`left` and `right`).  
3. While moving `right` through the array, maintain a map of element → frequency inside the current window.  
4. When the window’s distinct count equals `k`, every suffix starting at `right` (i.e., `[right … n‑1]`) will also contain `k` distinct elements.  
   * So add `n - right` to the answer.  
5. Shrink the window from the left until the distinct count drops below `k`.  
6. Continue until `right` reaches the end.

This is a classic “at least k distinct” trick that works because the set of distinct elements can only shrink when you remove the last occurrence of a value.

---

## 2️⃣ Brute‑Force Reference (O(n²))

```java
class BruteForce {
    public int countCompleteSubarrays(int[] nums) {
        int k = (int) IntStream.of(nums).distinct().count();
        int ans = 0;
        for (int i = 0; i < nums.length; i++) {
            Set<Integer> set = new HashSet<>();
            for (int j = i; j < nums.length; j++) {
                set.add(nums[j]);
                if (set.size() == k) ans++;
            }
        }
        return ans;
    }
}
```

The code is easy to read but does not scale to larger inputs (the limit is 1000, but a linear solution is still preferable).

---

## 3️⃣ Optimal Sliding‑Window Solution (O(n))

### 📌 Java

```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;

public class Solution {
    public int countCompleteSubarrays(int[] nums) {
        // Step 1 – total distinct elements
        Set<Integer> all = new HashSet<>();
        for (int v : nums) all.add(v);
        int k = all.size();

        // Step 2 – sliding window
        Map<Integer, Integer> freq = new HashMap<>();
        int left = 0, answer = 0;
        for (int right = 0; right < nums.length; right++) {
            freq.merge(nums[right], 1, Integer::sum);

            // While window has exactly k distinct elements
            while (freq.size() == k) {
                answer += nums.length - right; // all suffixes from right
                int leftVal = nums[left++];
                freq.put(leftVal, freq.get(leftVal) - 1);
                if (freq.get(leftVal) == 0) freq.remove(leftVal);
            }
        }
        return answer;
    }
}
```

### 📌 Python

```python
from collections import defaultdict
from typing import List

class Solution:
    def countCompleteSubarrays(self, nums: List[int]) -> int:
        # Step 1 – total distinct elements
        k = len(set(nums))

        # Step 2 – sliding window
        freq = defaultdict(int)
        left = 0
        ans = 0
        for right, val in enumerate(nums):
            freq[val] += 1

            while len(freq) == k:
                ans += len(nums) - right   # all suffixes
                left_val = nums[left]
                freq[left_val] -= 1
                if freq[left_val] == 0:
                    del freq[left_val]
                left += 1
        return ans
```

### 📌 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int countCompleteSubarrays(vector<int>& nums) {
        // Step 1 – total distinct elements
        unordered_set<int> all(nums.begin(), nums.end());
        int k = all.size();

        // Step 2 – sliding window
        unordered_map<int,int> freq;
        int left = 0, ans = 0;
        for (int right = 0; right < (int)nums.size(); ++right) {
            freq[nums[right]]++;

            while ((int)freq.size() == k) {
                ans += nums.size() - right;          // all suffixes
                int leftVal = nums[left++];
                if (--freq[leftVal] == 0)
                    freq.erase(leftVal);
            }
        }
        return ans;
    }
};
```

All three implementations run in **O(n)** time and use **O(k)** extra space (the hash‑map holds at most `k` keys).

---

## 4️⃣ Step‑by‑Step Example

Let `nums = [1, 3, 1, 2, 2]` → distinct elements `{1, 2, 3}`, so `k = 3`.

| right | nums[right] | freq (map)            | distinct | action |
|-------|-------------|-----------------------|----------|--------|
| 0 | 1 | {1:1} | 1 | nothing |
| 1 | 3 | {1:1,3:1} | 2 | nothing |
| 2 | 1 | {1:2,3:1} | 2 | nothing |
| 3 | 2 | {1:2,3:1,2:1} | 3 | **window complete** → add `5-3=2` → ans = 2; shrink left → remove 1 (freq[1] → 1) |
| 3 | 2 | {1:1,3:1,2:1} | 3 | **again** → add `5-3=2` → ans = 4; shrink left → remove 1 (freq[1] → 0 → erase) → distinct 2 |
| 4 | 2 | {3:1,2:2} | 2 | nothing |

Final answer = **4**, which matches the brute‑force result.

---

## 5️⃣ Common Pitfalls & How to Avoid Them

| Pitfall | Why it happens | Fix |
|---------|----------------|-----|
| **Leaving stale entries in the map** | When you decrement a counter but forget to erase keys with frequency 0, `freq.size()` never drops below `k`. | Always `if (--freq[val] == 0) freq.erase(val);` |
| **Off‑by‑one when adding `n-right`** | Forgetting that `right` is *already inside* the window when the suffix is counted. | Use `ans += nums.length - right;` (Java / C++) or `ans += len(nums) - right;` (Python). |
| **Handling duplicates on the left** | Removing only one duplicate can still leave the left side of the window containing repeated elements that we don’t need. | While shrinking, skip duplicates: `while (freq[nums[left]] > 1) { freq[nums[left]]--; left++; }` if you need the minimal window before counting. (Our linear solution does not need this because we always shrink until distinct < k.) |

---

## 5️⃣ Time & Space Complexity Recap

| Approach | Time | Space |
|----------|------|-------|
| Brute‑force | **O(n²)** | **O(k)** |
| Sliding window | **O(n)** | **O(k)** |

With `n ≤ 1000`, both are fast enough, but the interview expects the linear solution. The `O(n)` approach demonstrates optimal thinking and reduces the risk of a *time‑limit‑exceeded* error on the hidden test cases.

---

## 5️⃣ Why This Blog Helps You Land a Job

1. **Clear, annotated code** – recruiters can copy the solution and run tests.  
2. **Conceptual depth** – the blog explains the “suffix trick” that’s widely used in LeetCode hard problems.  
3. **SEO‑friendly structure** – a candidate’s article that covers *good, bad, ugly* aspects shows you’re self‑aware and can troubleshoot.  
4. **Performance metrics** – recruiters value candidates who think about complexity before coding.

> **Takeaway:** Practice the sliding‑window pattern on other “distinct element” problems (e.g., *Longest Substring with K Distinct Characters*, *Number of Subarrays with Exactly K Different Integers*). Master the trick of adding `n - right` to the answer when the window satisfies the condition.

---

# 🎯 SEO‑Optimized Blog Post

### Title  
**“Master LeetCode 2799: Count Complete Subarrays – A Sliding‑Window Masterclass for Interview Success”**

### Meta Description  
"Learn the O(n) sliding‑window solution for LeetCode 2799, with Java, Python, and C++ code. Understand good, bad, ugly approaches, and boost your coding interview prep!"

---

## 📚 Article Body

> **Introduction**  
>  
> LeetCode’s *Count Complete Subarrays* (Problem 2799) is a surprisingly elegant test of your understanding of sliding windows, hash‑maps, and distinct element counting. In this article we’ll dissect the problem, present a brute‑force baseline, and then reveal the linear solution that will impress any hiring manager.

### 1️⃣ Problem Recap  
*(As shown above)*

### 2️⃣ The Brute‑Force Approach  
*(Insert Java snippet from section 2.1)*  
> *Pros*: Simple, readable.  
> *Cons*: O(n²) – still passes for n = 1000, but wasteful.

### 3️⃣ The Optimal Solution – Sliding Window  
*(Insert the Java/Python/C++ snippets side‑by‑side)*  

> **Why it works**:  
> * When the window contains exactly `k` distinct values, all later suffixes will keep that property because you’re not removing any of the last occurrences of those `k` values.  
> * Adding `n - right` counts all those suffixes in one step.  
> * Shrinking the window only when the distinct count reaches `k` keeps the algorithm linear.

### 4️⃣ Edge‑Case Checklist  
| Case | What to Watch For | Fix |
|------|-------------------|-----|
| All elements equal | `k = 1` – the window always has 1 distinct | Ensure `while freq.size() == k` triggers immediately after first element |
| Array of length 1 | `right` and `left` start at same index | Works automatically – the answer will be 1 |
| Large numbers (≤ 2000) | Using an array of size 2001 in Java/C++ for frequency can be faster than a hash‑map | Either use array or hash‑map – both O(k) space |

### 5️⃣ Performance Analysis  
| Implementation | Time | Space | Notes |
|----------------|------|-------|-------|
| Brute‑force | **O(n²)** | **O(k)** | Acceptable for n = 1000 but slower on larger data |
| Sliding‑window | **O(n)** | **O(k)** | Preferred in interviews; scales to millions of elements |

### 6️⃣ “Good, Bad, Ugly” Summary  

| Category | Good | Bad | Ugly |
|----------|------|-----|------|
| **Good** | Linear time, clear map logic, easy to test. | Use `merge`/`freq.merge` for brevity. |  
| **Bad** | `while (freq.size() == k)` – mis‑placing `+= n - right` can give half the answer. |  
| **Ugly** | Forgetting to `erase` zero‑frequency keys → window never shrinks. |  

### 7️⃣ How to Show This in an Interview  
1. **Explain the plan**: “I’ll first compute the total distinct set, then slide a window.”  
2. **Write the counter**: show `freq` update and shrink logic.  
3. **Discuss complexity**: “O(n) time, O(k) space.”  
4. **Optional**: present the O(n²) brute‑force as a sanity check.  
5. **Ask for corner cases**: e.g., all equal, all distinct.

---

## 🚀 Final Thought

LeetCode 2799 is a classic sliding‑window puzzle that is **easy to solve, hard to over‑think**. By mastering the “suffix‑counting” trick and handling the frequency map cleanly, you’ll solve the problem in linear time and demonstrate a deep understanding of hash‑based window algorithms—exactly the skill set recruiters look for in a backend or algorithm engineer.

Happy coding, and good luck on your next interview!