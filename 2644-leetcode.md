---
title: LeetCode 2644. Find the Maximum Divisibility Score - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2644. Find the Maximum Divisibility Score  
**LeetCode 2644 – Easy** | **Java | Python | C++** | **Interview Tips**

> **Goal** – For each divisor `d` in `divisors`, count how many numbers in `nums` are divisible by `d`.  
> Return the divisor with the **maximum** count; if several divisors tie, return the **smallest** one.

> **Constraints**  
> * 1 ≤ `nums.length`, `divisors.length` ≤ 1000  
> * 1 ≤ `nums[i]`, `divisors[i]` ≤ 10⁹  

Because the limits are tiny, a straightforward double‑loop runs in under a millisecond. Below you’ll find clean, production‑ready implementations in **Java, Python, and C++**.

---

## 1. Java Solution

```java
// 2644. Find the Maximum Divisibility Score – Java
class Solution {
    public int maxDivScore(int[] nums, int[] divisors) {
        // result holds the divisor with the best score so far
        // maxCount holds its score
        int result = 0;
        int maxCount = -1;           // guarantees the first divisor wins ties

        for (int divisor : divisors) {
            int count = 0;
            for (int num : nums) {
                if (num % divisor == 0) count++;
            }
            // Update if we found a higher score, or an equal score but a smaller divisor
            if (count > maxCount || (count == maxCount && divisor < result)) {
                maxCount = count;
                result = divisor;
            }
        }
        return result;
    }
}
```

**Why it works**

* `O(n · m)` time – two nested loops, each at most 1000 iterations.  
* `O(1)` extra space – only a few integer variables.

---

## 2. Python Solution

```python
# 2644. Find the Maximum Divisibility Score – Python
def maxDivScore(nums: List[int], divisors: List[int]) -> int:
    result, max_count = 0, -1  # same idea as Java
    for divisor in divisors:
        count = sum(1 for num in nums if num % divisor == 0)
        if count > max_count or (count == max_count and divisor < result):
            max_count = count
            result = divisor
    return result
```

*Uses a generator expression for brevity while keeping the same `O(n·m)` runtime.*

---

## 3. C++ Solution

```cpp
// 2644. Find the Maximum Divisibility Score – C++
class Solution {
public:
    int maxDivScore(vector<int>& nums, vector<int>& divisors) {
        int result = 0;
        int maxCount = -1;
        for (int d : divisors) {
            int cnt = 0;
            for (int num : nums)
                if (num % d == 0) ++cnt;
            if (cnt > maxCount || (cnt == maxCount && d < result)) {
                maxCount = cnt;
                result = d;
            }
        }
        return result;
    }
};
```

*Simple, readable, and fully compliant with the LeetCode judge.*

---

## 4. Blog‑Style Walk‑Through: The Good, the Bad, and the Ugly

### 4.1 Problem Recap

> **Input** – Two arrays `nums` and `divisors`.  
> **Output** – A divisor from `divisors` that divides the most numbers in `nums`.  
> **Tie‑breaker** – Return the smallest divisor when multiple tie.

### 4.2 The Good – Why the Brute‑Force Approach Wins

1. **Simplicity** – Two nested loops, no extra data structures.  
2. **Correctness by construction** – Every `(divisor, num)` pair is examined exactly once.  
3. **Predictable performance** – `n,m ≤ 1000` guarantees < 1 million iterations, well below time limits.  
4. **Easy to test** – You can enumerate all possibilities manually.

### 4.3 The Bad – Where the Brute‑Force Might Fail

1. **Scalability** – If constraints grew to 10⁵, `O(n·m)` would be infeasible.  
2. **Cache‑miss heavy** – The inner loop is memory bound for very large arrays.  
3. **Redundant work** – The same modulus operation is recomputed for each divisor.

> *Real‑world advice:* For production‑grade code, consider pre‑computing divisor counts or using a hash map keyed by remainder. But for this problem, the straightforward solution is optimal.

### 4.4 The Ugly – Edge Cases & Common Pitfalls

| Pitfall | Why it Happens | Fix |
|---------|----------------|-----|
| **Wrong tie‑breaker** | Using `<=` instead of `<` when updating the result. | Use `if (count > maxCount || (count == maxCount && divisor < result))`. |
| **Initial result value** | Setting `result` to `divisors[0]` and `maxCount` to `0` may skip a divisor with 0 score if all have 0. | Initialise `maxCount = -1` so the first divisor always wins ties. |
| **Overflow** | `num % divisor` fits in `int` for values ≤ 10⁹, but if values were larger you’d need `long`. | Use `long` for safety when values approach 2³¹−1. |
| **Empty arrays** | Not allowed by constraints, but defensive coding is good. | Add a guard: if `divisors.isEmpty()` return `-1`. |

### 4.5 Complexity Analysis

| Implementation | Time | Space |
|----------------|------|-------|
| Java / Python / C++ | **O(n · m)** | **O(1)** |
| *Explanation*: two nested loops, constant auxiliary variables. |

With `n, m ≤ 1000`, the worst‑case runtime is roughly 1 000 000 operations – easily handled by any modern CPU.

### 4.6 Interview‑Ready Tips

1. **Explain the brute‑force first** – It demonstrates understanding of the problem statement.  
2. **Mention the constraints** – Show you’re aware of where the solution is efficient.  
3. **Discuss possible optimizations** – Talk about pre‑computing divisor sets or using a frequency table if constraints changed.  
4. **Write clean, commented code** – Recruiters love maintainable code.  
5. **Test edge cases** – All zeros, all same numbers, single element arrays.

### 4.7 Take‑away

> For LeetCode 2644, a double‑loop solution is **perfectly adequate**.  
> The key to acing the interview is not just the algorithm but also your **clarity of thought**, **edge‑case awareness**, and **clean code**.  

> Keep practicing similar “counting” problems:  
> * 1815. Count Items Matching a Rule (LeetCode)  
> * 1515. Minimum Cost to Hire K Workers (LeetCode)  
> These reinforce loops, modular arithmetic, and tie‑breaking logic.

---

## 5. SEO‑Optimized Meta‑Description (for your résumé or blog)

> “Learn how to solve LeetCode 2644 – Find the Maximum Divisibility Score – with clean Java, Python, and C++ code. Understand time/space complexity, tie‑breaking logic, and interview‑ready tips to land your next software engineering job.”

Feel free to copy the code snippets, publish the blog post, or incorporate the discussion into your next coding interview. Happy coding!