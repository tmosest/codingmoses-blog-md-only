---
title: LeetCode 3375. Minimum Operations to Make Array Values Equal to K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Minimum Operations to Make Array Values Equal to K  
*(LeetCode 3375 – Easy)*  

> **Problem recap**  
> You’re given an integer array `nums` and a target value `k`.  
> In one operation you may choose an integer `h` such that **all** elements larger than `h` are *identical*.  
> Then you set every element `> h` to `h`.  
> The goal is to turn every element in `nums` into `k` using as few operations as possible.  
> Return `-1` if it is impossible.

> **Key constraints**  
> * `1 ≤ nums.length ≤ 100`  
> * `1 ≤ nums[i], k ≤ 100`

---

## 1. Why the answer is “the number of distinct values greater than k”

1. **You can never increase a value** – an operation only lowers numbers.
2. **You can only touch a set of equal numbers** – all numbers larger than your chosen `h` must be the same.  
   Consequently, if you have two *different* values larger than `k`, you cannot merge them in one step.
3. **Lowering a higher value to `k` requires exactly one operation** – pick `h = k`, then all numbers `> k` become `k`.  
   But you can only do this if the *current* set of numbers larger than `k` is already identical.  
   So you have to first “flatten” the array by turning the largest value into the next largest, and so on.

Thus the process is equivalent to:  
> For every distinct value `v` that is **> k**, you need one operation to collapse it to the next smaller distinct value (or directly to `k`).  
> The total number of operations equals the count of distinct values that are > k.

The only time it is impossible is when there is a number **smaller than k** – you can never raise it up to `k`.

---

## 2. Optimal solution (O(n) time, O(n) space)

```text
1.  If any element < k → return -1.
2.  Count how many distinct values > k appear in nums.
3.  Return that count.
```

Because the constraints are tiny, even a sorting‑based O(n log n) solution works, but the hash‑set approach is clean and fast.

---

## 3. Code implementations

Below are ready‑to‑paste solutions in **Java, Python, and C++**.

### 3.1 Java

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public int minOperations(int[] nums, int k) {
        int min = Integer.MAX_VALUE;
        Set<Integer> greaterSet = new HashSet<>();

        for (int num : nums) {
            if (num < k) return -1;      // impossible
            if (num > k) greaterSet.add(num);
            min = Math.min(min, num);
        }

        return greaterSet.size();
    }
}
```

### 3.2 Python

```python
class Solution:
    def minOperations(self, nums: List[int], k: int) -> int:
        # Early exit if any number is smaller than k
        if any(x < k for x in nums):
            return -1

        # Count distinct numbers greater than k
        return len({x for x in nums if x > k})
```

### 3.3 C++

```cpp
class Solution {
public:
    int minOperations(vector<int>& nums, int k) {
        unordered_set<int> greater;          // distinct values > k
        for (int x : nums) {
            if (x < k) return -1;           // impossible
            if (x > k) greater.insert(x);
        }
        return greater.size();
    }
};
```

All three solutions run in **O(n)** time and use at most **O(n)** additional space for the hash‑set.

---

## 4. “The good, the bad, and the ugly” – What interviewers really want

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time complexity** | O(n) is optimal. | O(n log n) (sort) is acceptable but less elegant. | O(n²) brute‑force simulation is a red flag. |
| **Space usage** | Constant or O(n) for a hash‑set. | O(n) is fine given tiny limits. | Unnecessary allocation of big arrays or bit‑sets > 100 k. |
| **Edge‑case handling** | Explicitly check for `x < k` before counting. | Forgetting the “smaller than k” rule → wrong answer on hidden tests. | Not handling empty arrays or single‑element cases gracefully. |
| **Readability** | Clear, one‑liner logic. | Over‑complicated loops or needless sorting. | Mixing language features (e.g., Python `set` inside a loop) in a single solution. |
| **Explainability** | Explain why distinct > k values matter; tie it to the operation rule. | Simply “count distinct > k” without reasoning. | Claim “we can always lower to k directly” – misses the identical‑value requirement. |

### How to talk it out in an interview

1. **State your intuition first** – “I can only lower values, so a lower value can’t become k; therefore any value < k is impossible.”
2. **Sketch the process** – “I must flatten the largest value first, then the next one … so each distinct value > k costs one operation.”
3. **Pick the data structure** – “A HashSet gives me O(n) time; I’ll just insert all numbers > k and then return its size.”
4. **Explain edge‑cases** – “If the array already contains only `k`, we return 0; if it contains a value < k, we return -1; otherwise the set size is the answer.”

---

## 5. Testing your solution

| Test case | Expected | Explanation |
|-----------|----------|-------------|
| `nums = [9,10,5,10,8]`, `k=5` | `3` | Distinct values > 5 are {9,10,8}. |
| `nums = [5,5,5]`, `k=5` | `0` | Already all k. |
| `nums = [4,5,6]`, `k=5` | `-1` | 4 < 5, impossible. |
| `nums = [7,7,7]`, `k=5` | `1` | Only one distinct > 5 → one operation. |

Run these tests in your IDE or online judge to verify correctness.

---

## 5. Why this problem is a great interview “soft‑skill” exercise

* It forces candidates to translate a **process** (flattening by identical values) into a **static property** (distinct values > k).  
* It tests understanding of *hash‑sets* vs. *sorting* trade‑offs.  
* It checks if the candidate notices the *impossibility* condition (values < k).  

Most interviewers love a solution that is:

1. **Simple** – one‑liner logic plus a small helper set.  
2. **Correct** – passes all corner cases.  
3. **Well‑commented** – you can point to the three rules that justify the algorithm.

---

## 6. SEO‑friendly blog meta‑data

```html
<meta name="description" content="Learn how to solve LeetCode 3375 – Minimum Operations to Make Array Values Equal to K in Java, Python, and C++. Discover the optimal O(n) solution and interview tips.">
<meta name="keywords" content="LeetCode 3375, Minimum Operations to Make Array Values Equal to K, Java solution, Python solution, C++ solution, interview problem, coding interview, algorithm, hashset, O(n) time">
<title>LeetCode 3375: Minimum Operations to Make Array Values Equal to K – Java/Python/C++ Solutions & Interview Tips</title>
```

---

## 7. Take‑away checklist for interview prep

1. **Read the statement carefully** – identify forbidden operations (you can’t increase values).
2. **Translate rules into constraints** – here, you can only touch equal numbers.
3. **Seek the minimal “count”** – often the answer is simply the number of distinct values above a threshold.
4. **Implement with a hash‑set** – guarantees O(n) time and O(n) space.
5. **Explain your reasoning** – highlight why the solution is optimal and why edge cases matter.

---

### Final Thought

The “minimum operations” problem is a textbook example of how **a seemingly complicated transformation boils down to a simple counting problem**.  
Master it, and you’ll have a clean, efficient solution that impresses interviewers and scores high on LeetCode’s “Beats” leaderboard!