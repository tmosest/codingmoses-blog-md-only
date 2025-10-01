---
title: LeetCode 3041. Maximize Consecutive Elements in an Array After Modification - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Maximize Consecutive Elements in an Array After Modification – 3041 (LeetCode)  
> *A deep‑dive into DP, sorting, and space‑optimisation. 3‑language solution + SEO‑friendly interview‑prep guide.*

---

### 📌 Table of Contents
1. [Problem Recap](#problem-recap)
2. [Why This Problem Matters for Interviews](#why-this-problem-matters-for-interviews)
3. [Intuition & Key Observations](#intuition--key-observations)
4. [Approach #1 – DP with a HashMap](#approach-1---dp-with-a-hashmap)
5. [Approach #2 – Constant‑Space Sliding Window DP](#approach-2---constant‑space-sliding-window-dp)
6. [Full Code](#full-code)  
   - Java  
   - Python  
   - C++
7. [Testing & Edge Cases](#testing--edge-cases)
8. [Complexity Analysis](#complexity-analysis)
9. [Common Pitfalls & “Ugly” Cases](#common-pitfalls--“ugly”-cases)
10. [Alternative Approaches & Trade‑offs](#alternative-approaches--trade‑offs)
11. [Conclusion & Job‑Ready Takeaway](#conclusion--job‑ready-takeaway)
12. [FAQ](#faq)

---

## Problem Recap <a name="problem-recap"></a>
> **3041. Maximize Consecutive Elements in an Array After Modification**  
> **Difficulty:** Hard  

You are given an array `nums` of positive integers.  
* You can **increase any element by at most 1** (or leave it unchanged).  
* After performing these modifications, you must **select a subset of elements** that are *consecutive* when sorted (e.g., `[3,4,5]` is good, `[3,4,6]` is not).  
* Return the **maximum possible size** of such a subset.

**Constraints**

| Constraint | Value |
|------------|-------|
| `1 ≤ nums.length ≤ 10⁵` | |
| `1 ≤ nums[i] ≤ 10⁶` | |

---

## Why This Problem Matters for Interviews <a name="why-this-problem-matters-for-interviews"></a>
* **Hard LeetCode problem** – a great test for DP + greedy intuition.  
* Combines **sorting** (to bring order), **dynamic programming** (to keep track of two states), and **space optimisation** – all concepts interviewers love to see.  
* Typical in *coding‑interview‑prep* for tech giants (Google, Amazon, Microsoft, etc.).  
* Showcases your ability to **think about “how many ways can we modify?”** and still keep track of optimal subsequences.

---

## Intuition & Key Observations <a name="intuition--key-observations"></a>
1. **Sort first**  
   * After sorting, any valid consecutive streak must respect the order of indices.  
2. **Adding 1 to an element is the only modification** – we never subtract or add more than 1.  
3. **Two possible states for each element**  
   * `plain` – we *do not* add 1 to this element.  
   * `plusOne` – we *do* add 1.  
   The streak depends on what we did to the **previous** element.  
4. The **gap between consecutive numbers is at most 2** after modification:  
   * `a` → `a+1` (plain)  
   * `a+1` → `a+2` (plain)  
   * `a` → `a+2` (previous got +1, current plain)  
   * `a` → `a` (duplicate, we can use one of them, the other becomes part of the streak).  

These observations lead to a DP that runs in linear time *after sorting*.

---

## Approach #1 – DP with a HashMap <a name="approach-1---dp-with-a-hashmap"></a>
### ✅ Idea
* Sort the array.  
* Traverse the sorted list from left to right and maintain a **HashMap** `dp` where:  
  * `dp[v]` → maximum streak that **ends at value `v`** (assuming we *did not* increment the element that finally equals `v`).  
  * When we *do* increment the current element, we treat its value as `v+1`.  
* Transition:
  * If `v-1` already exists → extend the streak: `dp[v] = dp[v-1] + 1`.  
  * If `v-2` exists, it means the previous element was already +1: `dp[v] = max(dp[v], dp[v-2] + 1)`.

Because we only need the latest values, this approach uses **O(n log n)** time for sorting and **O(n)** extra space for the map.

### 🧩 Code Snippet (Java, Python, C++)
```java
// Java
class Solution {
    public int maxSelectedElements(int[] nums) {
        Arrays.sort(nums);
        Map<Integer, Integer> dp = new HashMap<>();
        int ans = 0;
        for (int v : nums) {
            int plain = dp.getOrDefault(v - 1, 0) + 1;          // no +1 on current
            int plusOne = dp.getOrDefault(v - 2, 0) + 1;        // current gets +1
            dp.put(v, Math.max(plain, plusOne));
            ans = Math.max(ans, dp.get(v));
        }
        return ans;
    }
}
```

```python
# Python
class Solution:
    def maxSelectedElements(self, nums: List[int]) -> int:
        nums.sort()
        dp = {}
        ans = 0
        for v in nums:
            plain   = dp.get(v-1, 0) + 1          # current stays v
            plus_one = dp.get(v-2, 0) + 1         # current +1 becomes v
            dp[v] = max(plain, plus_one)
            ans = max(ans, dp[v])
        return ans
```

```cpp
// C++
class Solution {
public:
    int maxSelectedElements(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        unordered_map<int,int> dp;
        int ans = 0;
        for (int v : nums) {
            int plain   = dp.count(v-1) ? dp[v-1]+1 : 1;
            int plusOne = dp.count(v-2) ? dp[v-2]+1 : 1;
            dp[v] = max(plain, plusOne);
            ans = max(ans, dp[v]);
        }
        return ans;
    }
};
```

> **Note:** The HashMap version is simple but uses *O(n)* extra memory.

---

## Approach #2 – Constant‑Space Sliding Window DP <a name="approach-2---constant‑space-sliding-window-dp"></a>
### ✅ Idea
* After sorting, we only need to remember **two previous states** (plain and plusOne).  
* Use two variables `plain` and `plusOne` that represent the streak length ending at the **previous** element under the two possible actions.  
* For the current element `cur` and the previous element `prev` we compute:

| Condition | New `plain` | New `plusOne` |
|-----------|-------------|---------------|
| `cur == prev + 1` | `plain + 1` | `plusOne + 1` |
| `cur == prev + 2` | `plusOne + 1` | `1` |
| `cur == prev` | `plain` (duplicate, no extension) | `plain + 1` |
| otherwise | `1` | `1` |

The key is that a duplicate can only be followed by a value that is **one larger** if the duplicate is **incremented**; otherwise the streak restarts.

This yields an **O(n log n)** time (dominated by sorting) and **O(1)** space solution.

### 🧩 Code Snippet (Java)
```java
class Solution {
    public int maxSelectedElements(int[] nums) {
        Arrays.sort(nums);
        int plain = 1;      // dp[i-1][0]
        int plusOne = 1;    // dp[i-1][1]
        int ans = 1;

        for (int i = 1; i < nums.length; i++) {
            int cur = nums[i];
            int prev = nums[i - 1];

            int newPlain = 1;
            if (cur == prev + 1) newPlain = plain + 1;
            else if (cur == prev + 2) newPlain = plusOne + 1;
            else if (cur == prev) newPlain = plain;   // duplicate: keep previous plain

            int newPlusOne = 1;
            if (cur == prev) newPlusOne = plain + 1;
            else if (cur == prev + 1) newPlusOne = plusOne + 1;

            plain = newPlain;
            plusOne = newPlusOne;
            ans = Math.max(ans, Math.max(plain, plusOne));
        }
        return ans;
    }
}
```

### 🧩 Code Snippet (Python)
```python
class Solution:
    def maxSelectedElements(self, nums: List[int]) -> int:
        nums.sort()
        plain = plus_one = 1
        ans = 1

        for i in range(1, len(nums)):
            cur, prev = nums[i], nums[i - 1]

            # Compute new states
            if cur == prev + 1:
                new_plain = plain + 1
                new_plus = plus_one + 1
            elif cur == prev + 2:
                new_plain = plus_one + 1
                new_plus = 1
            elif cur == prev:
                new_plain = plain          # duplicate: we cannot extend plain
                new_plus = plain + 1       # we can add 1 to this duplicate
            else:
                new_plain = new_plus = 1

            plain, plus_one = new_plain, new_plus
            ans = max(ans, plain, plus_one)

        return ans
```

### 🧩 Code Snippet (C++)
```cpp
class Solution {
public:
    int maxSelectedElements(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        int plain = 1, plusOne = 1, ans = 1;

        for (int i = 1; i < nums.size(); ++i) {
            int cur = nums[i], prev = nums[i-1];
            int newPlain, newPlus;

            if (cur == prev + 1) {
                newPlain = plain + 1;
                newPlus = plusOne + 1;
            } else if (cur == prev + 2) {
                newPlain = plusOne + 1;
                newPlus = 1;
            } else if (cur == prev) {
                newPlain = plain;            // duplicate: keep previous plain
                newPlus = plain + 1;         // add 1 to current duplicate
            } else {
                newPlain = newPlus = 1;
            }

            plain = newPlain;
            plusOne = newPlus;
            ans = max(ans, max(plain, plusOne));
        }
        return ans;
    }
};
```

> **Why Constant‑Space?**  
> Only the last element’s `plain` and `plusOne` values influence the next step. We never need the entire DP table.

---

## Testing & Edge Cases <a name="testing--edge-cases"></a>
| Test | Expected Output | Why it matters |
|------|-----------------|----------------|
| `[1, 1, 1, 1]` | `4` | All duplicates can be incremented to form `[1, 2]` streak |
| `[2, 4, 4, 5]` | `3` | Gap of 2 + duplicate |
| `[5, 7, 8, 10]` | `3` | Non‑consecutive numbers |
| `[1, 3, 5, 7]` | `2` | Only 1‑gap after increment |
| `[10^9, 10^9]` | `2` | Large values, check overflow handling |
| `[]` | `0` | Empty array case (rare but good to handle) |
| `[2]` | `1` | Single element |

Run a script to assert all cases; remember to test **duplicate clusters** and **maximum gap scenarios**.

---

## Summary of Solutions <a name="summary-of-solutions"></a>
| Approach | Time | Extra Space | Complexity |
|----------|------|-------------|------------|
| HashMap DP | **O(n log n)** | **O(n)** | Simple, easy to understand |
| Constant‑Space DP | **O(n log n)** | **O(1)** | Optimized, perfect for interviews |

---

## When to Use Which? <a name="when-to-use-which"></a>
* If you are **prototyping** or need a quick solution, use the **HashMap DP**.  
* If you are writing an **interview** solution or a **production‑grade** algorithm, go with the **constant‑space DP** – it shows you can optimize memory usage.

---

## Takeaway <a name="takeaway"></a>
* Sort → DP with two states → Constant‑Space.  
* This problem is a **classic pattern**:  
  1. **Sort** to handle order constraints.  
  2. **Two‑state DP** because each element has two choices.  
  3. **Compress** DP to *O(1)* if only the previous row matters.  

Mastering these steps demonstrates you can craft **efficient, memory‑aware solutions**—exactly what interviewers look for.

---

## 🎯 Final Checklist Before Interview
- [ ] Understand why *sorting* is essential.  
- [ ] Explain the **two states** (`plain`, `plusOne`).  
- [ ] Show how **duplicate handling** works.  
- [ ] Demonstrate the **DP transition** and why only the last row matters.  
- [ ] Provide code for **Java, Python, and C++** – ready to paste into the interview console.  
- [ ] Run edge cases (`[1,1,1]`, `[1,3,5]`, `[10^9]`) to ensure no overflow or off‑by‑one errors.  

With this preparation, you’ll ace the **Leetcode #XXX (maxSelectedElements)** question and impress hiring managers at top tech firms. Good luck! 🚀

--- 

**Want more Leetcode explanations?**  
Subscribe to our newsletter for weekly *“Daily Leetcode Breakdowns”* and *“Interview‑Ready Patterns”*!