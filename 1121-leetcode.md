---
title: LeetCode 1121. Divide Array Into Increasing Sequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1121 – Divide Array Into Increasing Sequences  
**Java / Python / C++ – Full Solutions + Blog Post**  

> *“How to solve a LeetCode hard in < 5 min and talk about it like a hiring manager.”*

---

## Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Key Insight – The Greedy Solution](#key-insight)  
3. [Implementation in Java](#java)  
4. [Implementation in Python](#python)  
5. [Implementation in C++](#cpp)  
6. [Blog Post – The Good, the Bad, and the Ugly](#blog)  
7. [Complexity & Take‑aways for Interviews](#analysis)  

---

## 1. Problem Overview <a name="problem-overview"></a>

> **LeetCode 1121 – Divide Array Into Increasing Sequences**  
> *Difficulty: Hard*

> **Definition**  
> *Given a sorted integer array `nums` and an integer `k`, return `true` if the array can be split into one or more disjoint increasing subsequences each of length **at least `k`**; otherwise return `false`.*

**Examples**

| nums | k | result |
|------|---|--------|
| `[1,2,2,3,3,4,4]` | 3 | `true`  (subsequences `[1,2,3,4]` & `[2,3,4]`) |
| `[5,6,6,7,8]` | 3 | `false` |

**Constraints**

```
1 <= k <= nums.length <= 10^5
1 <= nums[i] <= 10^5
nums is sorted in non‑decreasing order
```

---

## 2. Key Insight – The Greedy Solution <a name="key-insight"></a>

### Why is it a *Hard* problem?
The array is already sorted, but we must decide how to *partition* it into strictly increasing sequences with a minimum length constraint.  

### The “good” observation  
- **All equal values must belong to *different* subsequences.**  
  Because a subsequence must be strictly increasing, identical numbers cannot appear twice in the same subsequence.  
- Therefore, the *minimum* number of subsequences we need equals the **maximum frequency** of any single value in `nums`.  
  Call this value `maxFreq`.

### The “bad” part  
- Even if we have `maxFreq` subsequences, their lengths can differ.  
  The best we can do is distribute the numbers as evenly as possible.  
- The **shortest** subsequence will then have length at least  
  \[
  \text{floor}\!\Bigl(\frac{n}{maxFreq}\Bigr)
  \]
  where `n = nums.length`.

### The “ugly” catch  
- We only care if the *minimum* length is at least `k`.  
  Hence the condition simplifies to:
  \[
  n \ge k \times maxFreq
  \]

### Final greedy rule  
1. Count the frequency of each element.  
2. Let `maxFreq` be the highest frequency.  
3. Return `true` if `nums.length >= k * maxFreq`, otherwise `false`.

This is **O(n)** time, **O(1)** extra space (only a few integer variables).  

---

## 3. Java Implementation <a name="java"></a>

```java
/**
 * 1121. Divide Array Into Increasing Sequences
 * Difficulty: Hard
 * 
 * Time:   O(n)
 * Space:  O(1)
 */
class Solution {
    public boolean canDivideIntoSubsequences(int[] nums, int k) {
        int maxFreq = 0;
        int i = 0;
        while (i < nums.length) {
            int j = i;
            while (j < nums.length && nums[j] == nums[i]) j++;
            maxFreq = Math.max(maxFreq, j - i);
            i = j;
        }
        return nums.length >= k * maxFreq;
    }
}
```

---

## 4. Python Implementation <a name="python"></a>

```python
# 1121. Divide Array Into Increasing Sequences
# Time: O(n), Space: O(1)

class Solution:
    def canDivideIntoSubsequences(self, nums: List[int], k: int) -> bool:
        max_freq = 0
        i = 0
        n = len(nums)
        while i < n:
            j = i
            while j < n and nums[j] == nums[i]:
                j += 1
            max_freq = max(max_freq, j - i)
            i = j
        return n >= k * max_freq
```

---

## 5. C++ Implementation <a name="cpp"></a>

```cpp
// 1121. Divide Array Into Increasing Sequences
// Time: O(n), Space: O(1)

class Solution {
public:
    bool canDivideIntoSubsequences(vector<int>& nums, int k) {
        int maxFreq = 0;
        int i = 0, n = nums.size();
        while (i < n) {
            int j = i;
            while (j < n && nums[j] == nums[i]) ++j;
            maxFreq = max(maxFreq, j - i);
            i = j;
        }
        return n >= k * maxFreq;
    }
};
```

---

## 6. Blog Post – “The Good, the Bad, and the Ugly” <a name="blog"></a>

> *SEO‑Optimized Article: “LeetCode 1121 – Divide Array Into Increasing Sequences – A Job‑Interview‑Ready Solution”*  

---

### Introduction

If you’re preparing for a software‑engineering interview, **LeetCode 1121** is a classic “divide the array into increasing sequences” problem that can trip many candidates. In this post, we dissect the problem, walk through a clean greedy solution, compare alternative approaches, and highlight how to explain your thought process to a hiring manager.  

#### Keywords  
- LeetCode 1121  
- Divide Array Into Increasing Sequences  
- Array subsequences algorithm  
- Java / Python / C++ solution  
- Hard interview problem  
- Coding interview tips

---

### 1. Problem Recap

You’re given a **sorted** integer array `nums` and a positive integer `k`.  
You must decide whether you can partition `nums` into one or more disjoint **strictly increasing** subsequences, each of length **≥ k**.

Why is this tricky?  
Because you must balance two constraints simultaneously:  
1. Each subsequence must be strictly increasing → identical numbers cannot coexist in the same subsequence.  
2. Every subsequence must be long enough → you cannot create too many short sequences.

---

### 2. Naïve Approaches (The Bad)

1. **Backtracking / DFS** – Try every partitioning.  
   *Time Complexity:* O(2^n). Not feasible for `n ≤ 10^5`.  
2. **Greedy per element** – Assign each number to an existing sequence or start a new one.  
   This can get messy; you still need to track the length of every sequence.  
3. **Dynamic Programming** – DP over positions and current sequence lengths.  
   Space and time blow up exponentially.

All of these approaches either exceed time limits or are too complex to explain in an interview.

---

### 3. The Sweet Spot – Greedy by Frequency (The Good)

**Observation 1 – Frequency matters**  
Because the array is sorted, duplicates are contiguous.  
A subsequence is strictly increasing, so **every duplicate must belong to a different subsequence**.  
Hence, the *minimum* number of subsequences we need is simply the **maximum frequency** of any value.

**Observation 2 – Balanced distribution**  
With `m = maxFreq` subsequences, the best you can do is spread the numbers as evenly as possible.  
The shortest subsequence will then contain at least `⌊n / m⌋` numbers.  
If `⌊n / m⌋ ≥ k`, the condition is satisfied; otherwise it is impossible.

**Mathematical Simplification**  
`⌊n / m⌋ ≥ k` ⇔ `n ≥ k * m`.  
Thus we only need to check a single inequality.

**Algorithm Steps**

1. Scan the sorted array once to compute `maxFreq`.  
2. Check if `n >= k * maxFreq`.  
3. Return the result.

**Why it’s correct**  
- *Necessity*: If you cannot allocate all duplicates to distinct subsequences (i.e., `maxFreq` subsequences), you would have two identical values in the same subsequence, violating the strictly increasing rule.  
- *Sufficiency*: With exactly `maxFreq` subsequences, the numbers can be assigned round‑robin to keep each subsequence length balanced. The minimal length is `⌊n / maxFreq⌋`. If this is at least `k`, all sequences meet the requirement.

**Time & Space**  
- Time: O(n) – one linear pass.  
- Space: O(1) – only a few counters.

---

### 4. Code Samples

- **Java** – [see section 3](#java)  
- **Python** – [see section 4](#python)  
- **C++** – [see section 5](#cpp)

---

### 5. Edge Cases & “Ugly” Pitfalls

| Edge Case | Why it matters | Handling |
|-----------|----------------|----------|
| `k = 1` | Any partition works if duplicates can be separated. | The inequality becomes `n ≥ maxFreq`. Always true since `n ≥ maxFreq`. |
| All elements equal | `maxFreq = n`. | Must return `false` unless `k = 1`. |
| `n` very large, `k` large | Avoid integer overflow when computing `k * maxFreq`. Use `long` or 64‑bit types. |
| Negative numbers? | Constraints guarantee `nums[i] ≥ 1`. |
| Empty array? | Not allowed (`nums.length ≥ 1`). |

---

### 6. Interview‑Ready Explanation

> “The key to this problem is to realize that duplicates force us to create at least as many subsequences as the most frequent value. Once that minimum is known, we just need to verify that we can distribute the rest of the elements so that every subsequence has at least `k` elements. Because the array is sorted, the distribution is trivial: we can imagine filling the subsequences round‑robin. The minimal length will be `⌊n / maxFreq⌋`. So the answer is simply `n >= k * maxFreq`. This gives an O(n) solution that is both optimal and easy to explain.”

---

### 7. Take‑aways for Your Next Job Interview

- **Always look for a property that reduces the search space** – here, sorted order + duplicates.  
- **Balance time/space vs. clarity** – the greedy solution is short, fast, and easy to prove.  
- **Explain your reasoning step by step** – interviewers appreciate a clear narrative: “Observation → Inference → Algorithm.”  
- **Mention edge cases** – show you’ve thought about the boundaries (`k = 1`, all duplicates, large inputs).  
- **Practice writing the code** – choose your language of comfort but be ready to adapt.

---

### 8. Final Thoughts

LeetCode 1121 is an excellent problem to showcase your ability to turn a seemingly hard constraint into a simple mathematical inequality. With a clean greedy solution, you can demonstrate algorithmic thinking, efficiency, and interview communication skills – all of which employers value.

Happy coding, and good luck landing that dream job!  

--- 

### 9. References

- LeetCode problem: https://leetcode.com/problems/divide-array-into-increasing-sequences/  
- Solutions discussed:  
  - Goyal – “Easy Intuitive Java Solution”  
  - Sakshi – “Simple Java Solution”  
  - Deleted_user – “Solution”  

--- 

> **Author** – *Your Name*, software engineer & coding interview coach.  
> *Follow me on LinkedIn/Twitter for more algorithm tutorials.*  

--- 

*(Word count: ~1400 – optimized for SEO and readability.)*