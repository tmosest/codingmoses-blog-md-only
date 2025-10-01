---
title: LeetCode 3635. Earliest Finish Time for Land and Water Rides II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1️⃣  LeetCode 3635 – Earliest Finish Time for Land and Water Rides II  
> **Difficulty:** Medium  
> **Tags:** Array, Greedy  

## ✅  Problem Recap

You have two lists of rides:  
- **Land rides** – `landStartTime[i]` (opening) and `landDuration[i]` (length)  
- **Water rides** – `waterStartTime[j]` (opening) and `waterDuration[j]` (length)

You must experience **exactly one** land ride **and** one water ride, in any order.  
A ride can start at its opening time or later. After finishing a ride you can start the second ride immediately if it’s already open, otherwise you wait until it opens.

**Goal:** Return the *earliest possible* finish time of the two rides.

---

## 2️⃣  Algorithm Overview

The key observation is that only the **earliest finishing ride** of each category matters for the optimal schedule.

1. **Earliest finish of a land ride**  
   ```text
   minLandFinish = min(landStartTime[i] + landDuration[i])   (over all i)
   ```
2. **Earliest finish of a water ride**  
   ```text
   minWaterFinish = min(waterStartTime[j] + waterDuration[j])   (over all j)
   ```

We then evaluate the two possible sequences:

| Sequence | Finish time calculation |
|----------|--------------------------|
| Land → Water | `waterDuration[j] + max(minLandFinish, waterStartTime[j])`  (for every water ride j) |
| Water → Land | `landDuration[i] + max(minWaterFinish, landStartTime[i])`   (for every land ride i) |

The answer is the minimum of all computed finish times.  
Complexity: **O(n + m)** time, **O(1)** extra space.

---

## 3️⃣  Code Implementations

Below are clean, production‑ready implementations in **Java, Python, and C++**.  
All follow the same greedy strategy and use the exact signatures expected by LeetCode.

---

### 3.1  Java

```java
/**
 * LeetCode 3635 – Earliest Finish Time for Land and Water Rides II
 * Time   : O(n + m)
 * Space  : O(1)
 */
public class Solution {
    public int earliestFinishTime(int[] landStartTime, int[] landDuration,
                                  int[] waterStartTime, int[] waterDuration) {
        // 1️⃣  Earliest finish times for each category
        int minLandFinish = Integer.MAX_VALUE;
        for (int i = 0; i < landStartTime.length; i++) {
            minLandFinish = Math.min(minLandFinish, landStartTime[i] + landDuration[i]);
        }

        int minWaterFinish = Integer.MAX_VALUE;
        for (int j = 0; j < waterStartTime.length; j++) {
            minWaterFinish = Math.min(minWaterFinish, waterStartTime[j] + waterDuration[j]);
        }

        int answer = Integer.MAX_VALUE;

        // 2️⃣  Land first, water second
        for (int j = 0; j < waterStartTime.length; j++) {
            int finish = waterDuration[j] + Math.max(minLandFinish, waterStartTime[j]);
            answer = Math.min(answer, finish);
        }

        // 3️⃣  Water first, land second
        for (int i = 0; i < landStartTime.length; i++) {
            int finish = landDuration[i] + Math.max(minWaterFinish, landStartTime[i]);
            answer = Math.min(answer, finish);
        }

        return answer;
    }
}
```

---

### 3.2  Python

```python
"""
LeetCode 3635 – Earliest Finish Time for Land and Water Rides II
Time   : O(n + m)
Space  : O(1)
"""
class Solution:
    def earliestFinishTime(
        self,
        landStartTime: list[int],
        landDuration: list[int],
        waterStartTime: list[int],
        waterDuration: list[int],
    ) -> int:

        # 1️⃣  Earliest finish times for each category
        min_land_finish = min(ls + ld for ls, ld in zip(landStartTime, landDuration))
        min_water_finish = min(ws + wd for ws, wd in zip(waterStartTime, waterDuration))

        ans = float("inf")

        # 2️⃣  Land → Water
        for ws, wd in zip(waterStartTime, waterDuration):
            finish = wd + max(min_land_finish, ws)
            ans = min(ans, finish)

        # 3️⃣  Water → Land
        for ls, ld in zip(landStartTime, landDuration):
            finish = ld + max(min_water_finish, ls)
            ans = min(ans, finish)

        return ans
```

---

### 3.3  C++

```cpp
/**
 * LeetCode 3635 – Earliest Finish Time for Land and Water Rides II
 * Time   : O(n + m)
 * Space  : O(1)
 */
class Solution {
public:
    int earliestFinishTime(vector<int>& landStartTime,
                           vector<int>& landDuration,
                           vector<int>& waterStartTime,
                           vector<int>& waterDuration) {
        // 1️⃣  Minimum finish times of each category
        int minLandFinish = INT_MAX;
        for (size_t i = 0; i < landStartTime.size(); ++i) {
            minLandFinish = min(minLandFinish, landStartTime[i] + landDuration[i]);
        }

        int minWaterFinish = INT_MAX;
        for (size_t j = 0; j < waterStartTime.size(); ++j) {
            minWaterFinish = min(minWaterFinish, waterStartTime[j] + waterDuration[j]);
        }

        int answer = INT_MAX;

        // 2️⃣  Land first, water second
        for (size_t j = 0; j < waterStartTime.size(); ++j) {
            int finish = waterDuration[j] + max(minLandFinish, waterStartTime[j]);
            answer = min(answer, finish);
        }

        // 3️⃣  Water first, land second
        for (size_t i = 0; i < landStartTime.size(); ++i) {
            int finish = landDuration[i] + max(minWaterFinish, landStartTime[i]);
            answer = min(answer, finish);
        }

        return answer;
    }
};
```

---

## 4️⃣  Blog Article – “The Good, the Bad, and the Ugly of LeetCode 3635”

> **Title:**  
> *Earliest Finish Time for Land and Water Rides II – The Good, the Bad, and the Ugly*  
> **Meta Description:**  
> Dive deep into LeetCode 3635. Understand the greedy solution, why it’s optimal, common pitfalls, and how mastering this problem can land you a senior software engineer role.  

---

### 4.1  Why This Problem Rocks for Interviews

| Aspect | Why It Matters |
|--------|----------------|
| **Greedy + O(n+m)** | Demonstrates you can find optimal solutions without brute force. |
| **Array Manipulation** | Tests array indexing, summation, and `max`/`min` usage. |
| **Two‑stage Scheduling** | A micro‑version of real‑world job scheduling (pipeline, resource allocation). |
| **Moderate Constraints** | `1 ≤ n,m ≤ 5·10⁴`, forcing a linear algorithm. |
| **LeetCode Difficulty: Medium** | Solving it shows you’re comfortable with problems that sit between “easy” and “hard.” |

> *Pro Tip:* Show the interviewer that you can derive **`minLandFinish`** and **`minWaterFinish`** by a single pass, then use those values to evaluate every ride in the opposite list.

---

### 4.2  The “Good” – What Makes It Elegant

1. **One‑Liner Concept**  
   *“Only the earliest finishing ride in each category matters.”*  
   That’s the core insight. Once you grasp it, the rest falls into place.

2. **No Sorting Needed**  
   Sorting would add `O((n+m) log (n+m))`. The greedy approach keeps it linear.

3. **Reusability of Code**  
   The same pattern (compute a min‑sum, then iterate over the other list) can solve many “minimum‑cost‑two‑task” problems.  

---

### 4.3  The “Bad” – Common Mistakes & Debugging Tips

| Mistake | Why It Happens | Fix |
|---------|----------------|-----|
| Using `Math.max` incorrectly | Forgetting that the second ride’s start time is compared with the *earliest finish* of the first category, not its own start time. | Explicitly write `finish = secondDuration + max(minFirstFinish, secondStartTime)`. |
| Off‑by‑one in array loops | `i < landStartTime.size()` vs `<=` for C++/Java. | Always use `size()`/`length` as the upper bound. |
| Integer overflow in Java | Adding two `int` values that sum > 2³¹−1 can overflow. | Problem constraints guarantee the sum fits in `int`. For safety, cast to `long` in languages that allow it. |
| Mis‑interpreting “earliest finish” | Thinking you need the *earliest start* instead of *earliest finish*. | Remember: `start + duration`. |

---

### 4.4  The “Ugly” – Edge Cases That Trip Up Even Smart Coders

1. **Both lists contain rides that finish at the exact same time**  
   Example: `landStartTime = [1, 2]`, `landDuration = [3, 1]` → both finish at 4.  
   The algorithm still works because `minLandFinish` will be 4.

2. **One list is a single element**  
   Works fine, but you must still compute both min finishes to keep the logic symmetric.

3. **All rides start after the other category’s earliest finish**  
   `waterStartTime = [10, 11]`, `landStartTime = [1, 2]`.  
   The `max` operation guarantees you wait correctly.

4. **Very large input size** (`5·10⁴`)  
   Python’s `zip` with generator expressions keeps memory usage low; Java/C++ use `int` instead of `long`.

---

### 4.5  Complexity Recap (SEO Friendly)

- **Time Complexity:** `O(n + m)` – linear, no nested loops.  
- **Space Complexity:** `O(1)` – only a handful of integers are stored.  
- **Worst‑Case Scenario:** `n = m = 5·10⁴` → ~10⁵ operations, easily under the 1 s LeetCode limit.

---

### 4.6  How Mastering This Problem Boosts Your Resume

1. **Showcase Optimal Thinking** – LeetCode problems that require *greedy proofs* are rare and highly valued in senior roles.  
2. **Highlight Algorithmic Breadth** – From sorting to dynamic programming, this problem covers a *middle ground* that demonstrates versatility.  
3. **Prove Clean Code Discipline** – The solutions above emphasize readability, use of built‑in functions (`max`, `min`, `zip`), and single‑responsibility methods – all traits interviewers love.  
4. **Prepare for Follow‑ups** – Interviewers often ask *“Why is this greedy approach optimal?”* or *“Could there be a counter‑example?”*. The blog article gives you a ready answer.

---

### 4.7  Final Thoughts

- **Good**: A clean greedy solution that runs in linear time and uses only constant space.  
- **Bad**: A tiny pitfall – many coders compute the wrong “earliest finish” (they use the earliest *start* instead of *finish*).  
- **Ugly**: Mis‑reading the “wait until the other ride opens” rule can lead to off‑by‑one errors and TLE in brute‑force attempts.  

> **Takeaway:**  
> Understand the underlying principle (only the fastest ride of each type matters), implement the two‑sequence check, and you’ll be able to tackle a whole family of scheduling problems that come up in real‑world interviews.

---

## 5️⃣  Wrap‑up

You now have:

- A **fully‑tested greedy solution** in **Java, Python, and C++**  
- A **SEO‑friendly blog post** that showcases your understanding of the problem, common pitfalls, and how it can help you land a senior role

Happy coding, and good luck on your next interview! 🚀