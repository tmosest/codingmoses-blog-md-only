---
title: LeetCode 930. Binary Subarrays With Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 930 – Binary Subarrays With Sum  
### 📈 How to Nail This Interview Question (Java | Python | C++)

> **Goal** – Given a binary array `nums` and an integer `goal`, count all non‑empty subarrays whose sum equals `goal`.

---

### 🔎 Quick Facts (SEO Friendly)

| Keyword | Why it matters |
|---------|----------------|
| *LeetCode 930* | Direct match for the problem number |
| *Binary subarrays with sum* | Exact problem title |
| *prefix sum + hashmap* | Most efficient solution |
| *sliding window fails* | Common pitfall |
| *O(n) time, O(n) space* | Performance guarantees |
| *coding interview* | Search intent for job seekers |

---

## 🧠 Problem Recap

```text
Input: nums = [1,0,1,0,1], goal = 2
Output: 4
```

The four subarrays are:
```
[1,0,1], [1,0,1], [0,1,0,1], [1,0,1,0,1]  (illustrated in bold/underline on LeetCode)
```

---

## 📦 Solution Overview

The naïve `O(n²)` double‑loop works but is too slow for 30 k elements.  
A *sliding window* works for **positive** numbers only; zeros break it.  
The optimal way is to use **prefix sums + a hash map**:

1. Compute running prefix sum `currSum`.  
2. For each index, the number of subarrays ending at `i` with sum `goal` equals  
   `count[currSum - goal]` (how many times the needed previous sum has appeared).  
3. Update `count[currSum]++` for future indices.

This runs in **O(n)** time and **O(n)** auxiliary space.

---

## ✅ Code Implementations

Below are clean, production‑ready snippets in **Java**, **Python**, and **C++**.

### 1️⃣ Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    /**
     * Counts the number of non‑empty subarrays of a binary array with sum == goal.
     *
     * @param nums  Binary array (elements are 0 or 1)
     * @param goal  Target sum
     * @return      Number of qualifying subarrays
     */
    public int numSubarraysWithSum(int[] nums, int goal) {
        // prefix sum -> frequency map
        Map<Integer, Integer> freq = new HashMap<>();
        freq.put(0, 1);  // empty prefix

        int currSum = 0;
        int result = 0;

        for (int num : nums) {
            currSum += num;
            // Subarrays ending here with sum==goal
            result += freq.getOrDefault(currSum - goal, 0);
            // Record current prefix sum
            freq.put(currSum, freq.getOrDefault(currSum, 0) + 1);
        }
        return result;
    }
}
```

### 2️⃣ Python

```python
from collections import defaultdict
from typing import List

class Solution:
    def numSubarraysWithSum(self, nums: List[int], goal: int) -> int:
        """
        Counts subarrays of a binary array whose sum equals goal.
        """
        pref_count = defaultdict(int)
        pref_count[0] = 1          # empty prefix
        curr_sum = 0
        res = 0

        for n in nums:
            curr_sum += n
            res += pref_count[curr_sum - goal]   # subarrays ending here
            pref_count[curr_sum] += 1

        return res
```

### 3️⃣ C++

```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    int numSubarraysWithSum(vector<int>& nums, int goal) {
        unordered_map<int, long long> freq;   // use long long for large counts
        freq[0] = 1;                          // empty prefix
        long long currSum = 0, ans = 0;

        for (int num : nums) {
            currSum += num;
            auto it = freq.find(currSum - goal);
            if (it != freq.end()) ans += it->second;
            freq[currSum]++;                  // record current prefix
        }
        return static_cast<int>(ans);
    }
};
```

> **Why `long long` in C++?**  
> `n` can be 30 k, so the number of subarrays can reach ~450 M. A 32‑bit int would overflow.

---

## 🚀 Performance

| Language | Time Complexity | Space Complexity | Notes |
|----------|-----------------|------------------|-------|
| Java     | O(n)            | O(n)             | Uses `HashMap` |
| Python   | O(n)            | O(n)             | `defaultdict` |
| C++      | O(n)            | O(n)             | `unordered_map` |

All three pass the 3 × 10⁴ element limit in milliseconds.

---

## 🔍 The Good, Bad, & Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic Idea** | Prefix sum + hashmap is O(n) – *the “good” standard* | Sliding window fails with zeros – *common interview mistake* | None once the correct approach is known |
| **Edge Cases** | Handles `goal == 0`, all zeros, all ones | Forgetting to pre‑populate `freq[0] = 1` causes off‑by‑one | Large answer overflow if int is used (C++) |
| **Readability** | Clear variable names, in‑line comments | Some solutions use nested loops → hard to follow | Extremely terse solutions with no comments become unreadable |
| **Testing** | Verify with `goal = 0` and random arrays | Failing to test with all zeros leads to hidden bugs | Not using `long long` for counts → silent overflow |

### What to Avoid

- **Naïve O(n²)**: still passes small tests but will TLE on max input.  
- **Sliding Window**: Works only for positive numbers; zeros break the invariant.  
- **Over‑optimization**: Removing the hash map for speed hurts correctness.

### What to Emphasize in Interviews

- Explain *why* prefix sums help: we reduce the subarray sum problem to a difference of two prefixes.  
- Show that zeros are handled seamlessly—each zero increments the same prefix sum, so the hash map correctly counts multiple subarrays ending at different indices.  
- Discuss the trade‑off: O(n) time vs. O(n) extra memory.

---

## 🎓 Interview‑Ready Takeaway

1. **Understand the Problem** – The array is binary, so prefix sums are small.  
2. **Pick the Right Algorithm** – Prefix sum + hashmap = linear time, linear space.  
3. **Write Clean Code** – Use descriptive names, comment the logic, and handle edge cases.  
4. **Test Extensively** – `goal = 0`, all zeros, all ones, single element arrays.  
5. **Explain in Words** – Articulate the solution during the interview: “We keep a running sum; every time we see the same sum again, we found a subarray that sums to zero, etc.”

---

## 📄 Blog Article: “Binary Subarrays With Sum – The Interview‑Winning Solution”

> **Title**  
> *LeetCode 930: Binary Subarrays With Sum – O(n) Solution (Java, Python, C++)*

> **Meta Description**  
> Master LeetCode 930 in minutes! Learn the optimal O(n) prefix‑sum hash map solution in Java, Python, and C++. Perfect your interview skills with clear code, edge‑case handling, and SEO‑optimized content.

### 1. Introduction

> In coding interviews, LeetCode 930 (“Binary Subarrays With Sum”) is a popular medium‑difficulty problem. It tests your ability to convert a summation requirement into a linear scan with a hash map. This article walks you through the **good**, **bad**, and **ugly** parts of the problem, provides clean code in three major languages, and offers interview‑style explanations.

### 2. Problem Statement & Constraints

> *Input*: `nums` – binary array (`0` or `1`), length up to 30 000.  
> *Goal*: Count subarrays where the sum equals `goal`.  
> *Goal* ranges from `0` to `nums.length`.  
> *Output*: Integer count of qualifying subarrays.

### 3. Common Pitfalls

| Mistake | Why it fails | Example |
|---------|--------------|---------|
| Using a sliding window | Zeros break the “strictly increasing” property of the window sum | `[0,0,1]`, goal = 1 → fails |
| O(n²) brute force | Time Limit Exceeded on large input | 30 k elements → 450 M iterations |

### 4. The Optimal Approach: Prefix Sum + Hash Map

- **Idea**: Let `prefix[i]` be the sum of the first `i` elements.  
- **Observation**: A subarray `(l, r]` sums to `goal` iff `prefix[r] - prefix[l] = goal`.  
- **Implementation**: For each `prefix[r]`, look up how many times `prefix[r] - goal` has appeared before.

> **Time**: O(n)  
> **Space**: O(n)

### 5. Code Walkthrough (Java)

```java
public int numSubarraysWithSum(int[] nums, int goal) {
    Map<Integer, Integer> freq = new HashMap<>();
    freq.put(0, 1);           // empty prefix

    int sum = 0, ans = 0;
    for (int n : nums) {
        sum += n;
        ans += freq.getOrDefault(sum - goal, 0);
        freq.put(sum, freq.getOrDefault(sum, 0) + 1);
    }
    return ans;
}
```

*Key points*:  
- `freq[0] = 1` handles subarrays that start at index `0`.  
- `sum - goal` is the *target prefix* we need to have seen earlier.

### 6. Python & C++ Variants

> (Show the earlier provided snippets.)

### 7. Edge‑Case Checklist

| Test | Expected | Why it matters |
|------|----------|----------------|
| `goal = 0`, all zeros | `n*(n+1)/2` | Zero sums many subarrays |
| `goal = 0`, mixed | Counts subarrays of only zeros | Must not count subarrays with ones |
| `goal = n`, all ones | 1 | Whole array only |
| Single element | 1 if equal to goal else 0 | Minimal input sanity |

### 8. Interview Talk‑Points

1. *Explain* the prefix‑sum intuition.  
2. *Mention* the necessity of the initial `freq[0] = 1`.  
3. *Discuss* time/space trade‑offs.  
4. *Show* the solution in your preferred language.

### 9. Conclusion

> LeetCode 930 is a classic example where a smart use of data structures turns an otherwise quadratic problem into a linear one. Mastering this pattern not only gives you a job‑ready algorithm but also demonstrates to interviewers that you can think critically under constraints.

---

## 🎯 Final Checklist for Your Job Hunt

- ✅ Include the code in your portfolio (Java, Python, C++).  
- ✅ Write a short blog post (like the one above) and host it on Medium/Dev.to.  
- ✅ Add the article to your GitHub README with tags: `#leetcode #930 #binary-subarrays`.  
- ✅ Practice explaining the solution out loud (mock interview).  
- ✅ Optimize for SEO: add keywords, meta tags, backlinks.

Good luck—your interviewers will appreciate the clean, efficient solution and the clear explanations!