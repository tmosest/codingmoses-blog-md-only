---
title: LeetCode 1913. Maximum Product Difference Between Two Pairs - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 1913 – “Maximum Product Difference Between Two Pairs”

**Problem Statement**  
> Given an integer array `nums`, choose four distinct indices `w, x, y, z` such that the product difference  
> \[(nums[w] * nums[x]) - (nums[y] * nums[z])\]  
> is maximized.  
> Return that maximum product difference.

> **Constraints**  
> * `4 ≤ nums.length ≤ 10⁴`  
> * `1 ≤ nums[i] ≤ 10⁴`

The goal is to pick **the two largest numbers** for one pair and **the two smallest numbers** for the other pair – that always yields the maximum difference.

---

## 2.  Algorithm (O(N) time, O(1) space)

1. **Find the two largest numbers** `max1 ≥ max2`.  
2. **Find the two smallest numbers** `min1 ≤ min2`.  
3. Return `max1 * max2 - min1 * min2`.

Because the array may contain duplicates, we still pick distinct indices – the algorithm works for that too.

---

## 3.  Reference Implementations  

Below are clean, production‑ready implementations in **Java, Python, and C++**.  
Feel free to copy‑paste into your IDE or interview environment.

---

### 3.1 Java  

```java
/**
 * 1913. Maximum Product Difference Between Two Pairs
 * O(N) time, O(1) space
 */
public class Solution {
    public int maxProductDifference(int[] nums) {
        // Two largest values
        int max1 = Integer.MIN_VALUE, max2 = Integer.MIN_VALUE;
        // Two smallest values
        int min1 = Integer.MAX_VALUE, min2 = Integer.MAX_VALUE;

        for (int n : nums) {
            // Update largest two
            if (n > max1) {
                max2 = max1;
                max1 = n;
            } else if (n > max2) {
                max2 = n;
            }

            // Update smallest two
            if (n < min1) {
                min2 = min1;
                min1 = n;
            } else if (n < min2) {
                min2 = n;
            }
        }

        return max1 * max2 - min1 * min2;
    }
}
```

---

### 3.2 Python  

```python
class Solution:
    def maxProductDifference(self, nums: List[int]) -> int:
        # Initialize extremes
        max1 = max2 = -1          # largest two
        min1 = min2 = 10 ** 5 + 1 # smallest two

        for n in nums:
            # largest two
            if n > max1:
                max2, max1 = max1, n
            elif n > max2:
                max2 = n

            # smallest two
            if n < min1:
                min2, min1 = min1, n
            elif n < min2:
                min2 = n

        return max1 * max2 - min1 * min2
```

---

### 3.3 C++  

```cpp
/**
 * 1913. Maximum Product Difference Between Two Pairs
 * O(N) time, O(1) space
 */
class Solution {
public:
    int maxProductDifference(vector<int>& nums) {
        int max1 = INT_MIN, max2 = INT_MIN;
        int min1 = INT_MAX, min2 = INT_MAX;

        for (int n : nums) {
            // largest two
            if (n > max1) {
                max2 = max1;
                max1 = n;
            } else if (n > max2) {
                max2 = n;
            }

            // smallest two
            if (n < min1) {
                min2 = min1;
                min1 = n;
            } else if (n < min2) {
                min2 = n;
            }
        }

        return max1 * max2 - min1 * min2;
    }
};
```

---

## 4.  Blog Article: “The Good, the Bad, and the Ugly of LeetCode 1913”

> **Title:** *Cracking LeetCode 1913: The Good, the Bad, and the Ugly – A Job‑Interview Friendly Guide*  
> **Meta Description:** Master LeetCode 1913 in minutes. Learn the O(N) solution, pitfalls, and how to nail it in your next coding interview.  

### 4.1 Introduction  
LeetCode problems are the rite‑of‑passage for anyone hunting a software‑engineering role.  
Problem **1913 – Maximum Product Difference Between Two Pairs** is tagged **Easy**, but it’s a great teaching moment for:

* **O(N) algorithm design** – no sorting, just a single pass.  
* **Careful handling of edge cases** – duplicates, small arrays, large numbers.  
* **Code readability** – variables that say what they are.

If you can articulate the solution clearly in an interview, you’ll impress hiring managers.

---

### 4.2 The Good: Why This Problem is a Winner

| Feature | Why It Matters |
|---------|----------------|
| **Straightforward O(N) solution** | Demonstrates linear‑time thinking, a staple in technical interviews. |
| **No extra space needed** | Shows you can manage memory efficiently (important for real‑world systems). |
| **Clear mathematical intuition** | Picking largest two and smallest two feels “obvious” once you explain it. |
| **Multiple language support** | Allows you to showcase versatility (Java, Python, C++). |
| **Good testability** | Small input, deterministic output – easy to unit‑test. |

If you nail this problem, you can confidently move to the next level (Medium/Hard) knowing you’ve mastered the fundamentals.

---

### 4.3 The Bad: Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Sorting the array** | Sorting is O(N log N); unnecessary when a single scan suffices. |
| **Using only two variables for extremes** | Need four variables: `max1, max2, min1, min2`. Forgetting one leads to wrong answer. |
| **Assuming indices are unique** | Even if values repeat, indices must be distinct. Our single‑pass logic respects that. |
| **Overflow in languages with fixed int size** | In C++/Java, `int` is 32‑bit; product of two 10⁴ numbers fits (10⁸), so safe. |
| **Edge case: array length exactly 4** | Still works – the two largest and two smallest are the four numbers themselves. |

---

### 4.4 The Ugly: A “Tricky” Twist

Some interviewers love to twist the problem slightly:

> *What if we wanted the **minimum** product difference?*

The answer would simply reverse the roles: pick the two largest for the *smaller* product and two smallest for the *larger* product. It’s a quick mental exercise to test how well you understand the core logic.

---

### 4.5 Writing a Job‑Interview‑Ready Explanation

1. **State the problem clearly** – *“Given an array, pick four distinct indices to maximize `(a*b)-(c*d)`.”*
2. **Explain the intuition** – *“The product of two numbers is maximized by the two largest, minimized by the two smallest.”*
3. **Outline the algorithm** – *“Single pass, keep four running extremes.”*
4. **Discuss complexity** – *“O(N) time, O(1) space.”*
5. **Show code** – Pick the language you’re most comfortable with. (Java, Python, C++ code snippets above.)
6. **Talk about edge cases** – *“Duplicates, minimal array size, large values.”*
7. **Optional twist** – *“What if we needed the minimum difference?”*

Keep your explanation short (≤ 1 minute) but full of technical depth. That’s the sweet spot for most hiring managers.

---

### 4.6 SEO & Career Tips

| SEO Hook | Why It Works |
|----------|--------------|
| *“LeetCode 1913 solution”* | People searching this exact phrase want the answer. |
| *“Maximum Product Difference interview”* | Adds interview context, increases relevance for recruiters. |
| *“O(N) algorithm LeetCode”* | Highlights efficiency, a key interview quality. |
| *“Java/Python/C++ LeetCode solutions”* | Shows versatility across languages. |

**Career advice:**  
- Publish a **blog post** (like this one) and share on LinkedIn, Twitter, or dev blogs.  
- Tag it with `#LeetCode`, `#CodingInterview`, `#SoftwareEngineering`.  
- Link back to your GitHub repository containing the clean solutions.  

Recruiters love to see candidates who *teach* – it proves deep understanding and communication skills.

---

### 4.7 Final Checklist Before Your Next Interview

1. ✅  Implement the O(N) solution in your preferred language.  
2. ✅  Write unit tests for edge cases (`[1,1,1,1]`, `[10000,1,1,9999]`).  
3. ✅  Be able to explain the algorithm in 60 seconds.  
4. ✅  Know the *why* – not just the *what*.  
5. ✅  Have a demo code snippet ready on a whiteboard or laptop.  

Good luck! 🚀  

--- 

**Keywords:** LeetCode, Maximum Product Difference, O(N) solution, coding interview, Java solution, Python solution, C++ solution, software engineering interview, algorithm interview, job interview, interview preparation.