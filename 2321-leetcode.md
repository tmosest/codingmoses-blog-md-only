---
title: LeetCode 2321. Maximum Score Of Spliced Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 2321. Maximum Score Of Spliced Array  
### The Good, The Bad, & The Ugly – A Job‑Interview‑Ready Guide  

---

### 🚀 TL;DR  
- **Goal:** Choose one sub‑array to swap between two equal‑length arrays and maximize  
  `max( sum(nums1), sum(nums2) )`.  
- **Optimal Idea:**  
  1. Compute the two totals (`sum1`, `sum2`).  
  2. The swap effect is `Δ = sum2[l…r] – sum1[l…r]`.  
  3. We want the *maximum* Δ to boost the larger array, and the *minimum* Δ to boost the smaller array.  
  4. A single linear scan (Kadane) finds both the maximum and minimum sub‑array sum of the difference array.  
- **Complexity:** **O(n)** time, **O(1)** extra space.  
- **Languages:** Java, Python, C++ – all shown below.

> **SEO keywords**: LeetCode, interview, coding interview, Java, Python, C++, Kadane’s algorithm, O(n) solution, array manipulation, resume, job interview, coding interview prep.

---

## 1. Problem Recap

You are given two integer arrays of equal length, `nums1` and `nums2`.  
You may pick any sub‑array `[left…right]` (inclusive) and *swap* the two sub‑arrays between the arrays **once** (or do nothing).  
The score is the larger of the two total sums after the optional swap.  
Return the maximum achievable score.

```text
Input  : nums1 = [60,60,60], nums2 = [10,90,10]
Output : 210  (swap index 1)
```

Constraints: `1 ≤ n ≤ 10^5`, `1 ≤ nums[i] ≤ 10^4`.

---

## 2. Why the Naïve Brute‑Force Fails

A brute‑force solution would enumerate all `O(n^2)` sub‑arrays, compute the swap effect and keep the best.  
Even with `n = 10^5`, that’s ~10^10 operations – far beyond practical limits.  
You need an **O(n)** algorithm.

---

## 3. The Insight

Let:

```
sum1 = Σ nums1[i]
sum2 = Σ nums2[i]
diff[i] = nums2[i] – nums1[i]   // element‑wise difference
```

If we swap sub‑array `[l…r]`:

```
new_sum1 = sum1 + Σ diff[l…r]
new_sum2 = sum2 – Σ diff[l…r]
```

The **only thing that matters** is the sum of `diff[l…r]`.  
Hence we just need the *maximum* and *minimum* sub‑array sums of the `diff` array.

- **Maximizing the larger array**: add the **maximum** Δ.  
- **Boosting the smaller array**: subtract the **minimum** Δ (i.e. add its absolute value).

The problem reduces to two classic one‑dimensional maximum/minimum sub‑array problems, solvable by **Kadane’s algorithm** in linear time.

---

## 4. The O(n) Algorithm (Kadane + One Pass)

1. Compute `sum1` and `sum2`.  
2. Build `diff[i] = nums2[i] - nums1[i]` on the fly.  
3. Run Kadane twice:
   - **maxDiff**: maximum sub‑array sum of `diff`.  
   - **minDiff**: minimum sub‑array sum of `diff` (can be found by running Kadane on `-diff` or using a dual‑track approach).  
4. Final answer = `max( sum1 + max(0, maxDiff),  sum2 + max(0, -minDiff) )`.

---

## 5. Code Walkthrough

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.  
Each uses a single scan and O(1) extra memory.

### 5.1 Java (LeetCode‑style)

```java
class Solution {
    public int maximumScoreOfSplicedArray(int[] nums1, int[] nums2) {
        long sum1 = 0, sum2 = 0;
        int n = nums1.length;
        for (int i = 0; i < n; ++i) {
            sum1 += nums1[i];
            sum2 += nums2[i];
        }

        // diff[i] = nums2[i] - nums1[i]
        long maxDiff = Long.MIN_VALUE, curMax = 0;
        long minDiff = Long.MAX_VALUE, curMin = 0;

        for (int i = 0; i < n; ++i) {
            long d = (long) nums2[i] - nums1[i];

            // Kadane for maximum sub‑array
            curMax = Math.max(d, curMax + d);
            maxDiff = Math.max(maxDiff, curMax);

            // Kadane for minimum sub‑array
            curMin = Math.min(d, curMin + d);
            minDiff = Math.min(minDiff, curMin);
        }

        long option1 = sum1 + Math.max(0, maxDiff);
        long option2 = sum2 + Math.max(0, -minDiff);
        return (int) Math.max(option1, option2);
    }
}
```

**Why `long`?**  
The sums can reach `10^5 * 10^4 = 10^9`.  
Adding a difference can push it slightly higher; `long` guarantees no overflow.

### 5.2 Python 3

```python
class Solution:
    def maximumScoreOfSplicedArray(self, nums1: list[int], nums2: list[int]) -> int:
        sum1, sum2 = sum(nums1), sum(nums2)

        max_diff = cur_max = 0
        min_diff = cur_min = 0

        for a, b in zip(nums1, nums2):
            d = b - a

            # Kadane for max
            cur_max = max(d, cur_max + d)
            max_diff = max(max_diff, cur_max)

            # Kadane for min
            cur_min = min(d, cur_min + d)
            min_diff = min(min_diff, cur_min)

        option1 = sum1 + max(0, max_diff)
        option2 = sum2 + max(0, -min_diff)
        return max(option1, option2)
```

**Note**: Python automatically handles big integers, so no extra type juggling is needed.

### 5.3 C++ (Fast & Idiomatic)

```cpp
class Solution {
public:
    int maximumScoreOfSplicedArray(vector<int>& nums1, vector<int>& nums2) {
        long long sum1 = 0, sum2 = 0;
        int n = nums1.size();

        for (int i = 0; i < n; ++i) {
            sum1 += nums1[i];
            sum2 += nums2[i];
        }

        long long maxDiff = LLONG_MIN, curMax = 0;
        long long minDiff = LLONG_MAX, curMin = 0;

        for (int i = 0; i < n; ++i) {
            long long d = static_cast<long long>(nums2[i]) - nums1[i];

            curMax = max(d, curMax + d);
            maxDiff = max(maxDiff, curMax);

            curMin = min(d, curMin + d);
            minDiff = min(minDiff, curMin);
        }

        long long option1 = sum1 + max(0LL, maxDiff);
        long long option2 = sum2 + max(0LL, -minDiff);
        return static_cast<int>(max(option1, option2));
    }
};
```

All three solutions keep the algorithmic core identical: *single pass + Kadane*.  
Feel free to copy‑paste them into your IDE or a LeetCode submission.

---

## 6. The Good

| Aspect | What to Emphasize |
|--------|-------------------|
| **Time‑efficiency** | Linear scan, ideal for interviewers looking for *O(n)* solutions. |
| **Space‑efficiency** | Constant extra space – a huge plus for coding interviews. |
| **Readability** | Simple variable names (`maxDiff`, `minDiff`, `curMax`, `curMin`). |
| **Language‑agnostic** | Works in Java, Python, C++ – shows you can solve the same problem in multiple languages. |
| **Edge‑case safety** | Handles “do nothing” naturally via `max(0, …)` and `max(0, -minDiff)`. |

---

## 7. The Bad – Common Pitfalls

| Pitfall | Why It Happens | How to Fix |
|---------|----------------|------------|
| **Overflow in 32‑bit integers** | Sum can exceed 2 × 10^9 in extreme cases. | Use `long` (Java/C++) or `long long` (C++). In Python it’s automatic. |
| **Two separate passes** | Some implement two Kadane passes, doubling runtime. | Run Kadane *once* while tracking both max and min simultaneously. |
| **Ignoring negative differences** | You might add negative Δ, reducing the score. | Take `max(0, maxDiff)` and `max(0, -minDiff)` to avoid harmful swaps. |
| **Off‑by‑one errors in the loop** | `diff` array mis‑computed leads to wrong Δ. | Use `zip(nums1, nums2)` or `for (int i = 0; i < n; ++i)` consistently. |
| **Missing the “do nothing” option** | You must consider the case where swapping hurts. | The `max(0, …)` part handles it automatically. |

---

## 8. The Ugly – Less‑Known Edge Cases

| Edge Case | Description | Quick Fix |
|-----------|-------------|-----------|
| **All differences are zero** | Arrays identical. | Max/Min Δ will be 0; answer is simply `max(sum1, sum2)`. |
| **All differences negative** | `nums2` strictly smaller at every index. | `maxDiff` will be negative, so you rely on boosting the smaller array via `minDiff`. |
| **All differences positive** | `nums2` strictly larger at every index. | Symmetric to the previous; swap never hurts. |
| **Large intermediate sums** | While computing `curMax + d`, intermediate sum could overflow 32‑bit. | Use `long`/`long long`. |
| **`n == 1`** | Only one possible sub‑array. | Kadane works fine, but initialise `maxDiff` & `minDiff` with the first difference. |

---

## 9. How to Test the Solution

```text
Test 1: Basic swap
nums1 = [1,2,3]
nums2 = [3,2,1]   -> Score 6

Test 2: No swap better
nums1 = [100, 200]
nums2 = [1, 1]    -> Score 300

Test 3: All negatives difference
nums1 = [5, 5, 5]
nums2 = [1, 1, 1] -> Score 15

Test 4: Large random arrays
nums1 = [randint(1,10000) for _ in range(100000)]
nums2 = [randint(1,10000) for _ in range(100000)]
```

Run these in your favourite IDE or via unit tests (`@Test` in Java, `unittest` in Python, or GoogleTest in C++).

---

## 10. Time & Space Complexity

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n)` | `O(n)` | `O(n)` |
| **Extra Space** | `O(1)` | `O(1)` | `O(1)` |
| **Worst‑case** | `~2 * 10^5` integer ops | Same | Same |

---

## 11. Takeaway for Your Next Coding Interview

1. **Understand the swap effect** – it boils down to a difference array.  
2. **Know Kadane** – the single‑pass maximum/minimum sub‑array trick is interview gold.  
3. **Keep it simple** – one scan, one pass, constant memory.  
4. **Explain your reasoning** – interviewers love a clear “why” behind every step.  
5. **Show versatility** – present solutions in multiple languages to demonstrate breadth of skill.

> **Pro Tip:** When explaining the solution in an interview, mention the “do nothing” case explicitly. Many interviewers ask if you considered that – it’s a subtle but important detail.

---

## 🎉 Wrap‑Up & Job‑Interview Boost

Solving LeetCode 2321 with an O(n) Kadane solution showcases:

- **Algorithmic depth** – you can turn a quadratic brute‑force into a linear, elegant approach.  
- **Attention to edge‑cases** – handling overflow, negative differences, and the “do nothing” option.  
- **Multilingual fluency** – Java, Python, C++ code shows you can translate concepts across ecosystems.  

These are exactly the qualities hiring managers look for when they see “array manipulation”, “dynamic programming”, or “Kadane’s algorithm” in your portfolio.  

**Next step:** Push the solution to your GitHub, add the problem description to your README, and practice explaining it on a whiteboard.  Your résumé will have an interview‑ready LeetCode story that impresses recruiters.

Happy coding, and best of luck landing that dream job! 🚀