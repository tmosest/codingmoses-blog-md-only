---
title: LeetCode 2644. Find the Maximum Divisibility Score - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯  LeetCode 2644 – Find the Maximum Divisibility Score  
### **Java • Python • C++ Solutions + SEO‑Optimized Blog Post**

> **Target Audience** – Software‑engineering interview candidates, job seekers, algorithm enthusiasts  
> **Goal** – Deliver a clear, production‑ready solution and a blog post that ranks on Google for “LeetCode 2644 solution”, “maximum divisibility score”, “job interview coding problem”, etc.

---

## Table of Contents  

1. [Problem Recap](#problem-recap)  
2. [Brute‑Force (Good) – O(n × m) Solution](#brute‑force-good)  
3. [Potential Pitfalls (Bad)](#potential-pitfalls)  
4. [Tiny Optimisation (Ugly – but worth it)](#tiny-optimisation)  
5. [Full Code – Java](#full-code-java)  
6. [Full Code – Python](#full-code-python)  
7. [Full Code – C++](#full-code-cpp)  
8. [SEO‑Optimized Blog Post](#blog-post)  

---

## <a name="problem-recap"></a>Problem Recap  
Given two arrays:

| Array | Meaning |
|-------|---------|
| `nums` | A list of integers (`1 ≤ len(nums) ≤ 1000`) |
| `divisors` | A list of candidate divisors (`1 ≤ len(divisors) ≤ 1000`) |

For each `divisors[i]`, the **divisibility score** is the number of elements in `nums` that are divisible by it.  
Return the divisor with the **maximum score**. If multiple divisors tie, return the **smallest** one.

> **Constraints**  
> * `1 ≤ nums[i], divisors[i] ≤ 10⁹`  
> * `1 ≤ nums.length, divisors.length ≤ 1000`

---

## <a name="brute-force-good"></a>Brute‑Force (Good) – O(n × m) Solution  

The most straightforward approach:

1. Iterate over each divisor.  
2. For each divisor, count how many `nums` are divisible by it (`num % divisor == 0`).  
3. Keep track of the best score and the corresponding divisor.  
4. Resolve ties by preferring the smaller divisor.

Because the limits are tiny (`≤ 1000`), this `O(n × m)` algorithm is more than fast enough.

---

## <a name="potential-pitfalls"></a>Potential Pitfalls (Bad)

| Pitfall | Why it hurts | Fix |
|---------|--------------|-----|
| **Integer overflow** | `num % divisor` in Java/Python is safe, but in C++ with 32‑bit `int` you must use `long long` if numbers could approach `10⁹`. | Use `long long` or cast to `int64_t`. |
| **Mis‑ordered tie‑break** | Returning the first divisor with the max score can lead to wrong answers if a smaller divisor appears later. | Explicitly check `divisor < bestDivisor` when scores are equal. |
| **Unnecessary data structures** | Storing all scores in a map or array increases memory overhead. | Process on‑the‑fly – just keep `maxScore` and `bestDivisor`. |
| **Read‑only parameters** | Modifying the input arrays (e.g., sorting) is illegal for interview‑style problems. | Treat input as immutable. |

---

## <a name="tiny-optimisation"></a>Tiny Optimisation (Ugly – but worth it)

If you wish to shave a few micro‑seconds (unnecessary for the constraints, but good practice), you can:

- **Cache** the remainder checks per divisor by iterating through `nums` once per divisor – which is what we already do.  
- **Short‑circuit** when a divisor’s score can’t possibly beat the current maximum (not trivial here because all divisors are independent).  
- **Use `int` instead of `long long`** if you are sure values fit in 32‑bit – speeds up mod operations.

In practice, the brute‑force approach is the cleanest and most readable.

---

## <a name="full-code-java"></a>Full Code – Java

```java
/**
 *  LeetCode 2644 – Find the Maximum Divisibility Score
 *  Author: ChatGPT (2025)
 */
public class Solution {

    /**
     * Returns the divisor from {@code divisors} that has the maximum divisibility score.
     *
     * @param nums      array of numbers (1 <= nums[i] <= 1e9)
     * @param divisors  array of candidate divisors (1 <= divisors[i] <= 1e9)
     * @return the divisor with the highest score; if tie, the smallest divisor
     */
    public int maxDivScore(int[] nums, int[] divisors) {
        int bestDivisor = 0;
        int maxScore   = -1;               // score can't be negative

        for (int d : divisors) {
            int score = 0;
            for (int n : nums) {
                if (n % d == 0) {
                    score++;
                }
            }

            // Prefer higher score, then smaller divisor
            if (score > maxScore || (score == maxScore && d < bestDivisor)) {
                maxScore   = score;
                bestDivisor = d;
            }
        }
        return bestDivisor;
    }
}
```

---

## <a name="full-code-python"></a>Full Code – Python

```python
"""
LeetCode 2644 – Find the Maximum Divisibility Score
Author: ChatGPT (2025)
"""

from typing import List

class Solution:
    def maxDivScore(self, nums: List[int], divisors: List[int]) -> int:
        """
        Return the divisor with the maximum divisibility score.
        In case of a tie, return the smallest divisor.
        """
        best_divisor = 0
        max_score = -1

        for d in divisors:
            score = sum(1 for n in nums if n % d == 0)
            if score > max_score or (score == max_score and d < best_divisor):
                max_score = score
                best_divisor = d

        return best_divisor
```

---

## <a name="full-code-cpp"></a>Full Code – C++

```cpp
/**
 *  LeetCode 2644 – Find the Maximum Divisibility Score
 *  Author: ChatGPT (2025)
 *
 *  Complexity: O(n * m) time, O(1) extra space
 */
class Solution {
public:
    int maxDivScore(vector<int>& nums, vector<int>& divisors) {
        int bestDivisor = 0;
        int maxScore = -1;

        for (int d : divisors) {
            int score = 0;
            for (int n : nums) {
                if (n % d == 0) {
                    ++score;
                }
            }
            if (score > maxScore || (score == maxScore && d < bestDivisor)) {
                maxScore = score;
                bestDivisor = d;
            }
        }
        return bestDivisor;
    }
};
```

> **Note**: All arrays use 32‑bit `int`. The values (`≤ 1e9`) fit comfortably, but if you ever encounter larger numbers, switch to `long long`.

---

## <a name="blog-post"></a>SEO‑Optimized Blog Post  

---

# How to Crack LeetCode 2644: “Find the Maximum Divisibility Score” in Java, Python, and C++  

> **Looking for a quick interview win?**  
> This post walks you through the entire solution to LeetCode problem 2644. Learn the **good**, the **bad**, and the **ugly**. Grab the code snippets in **Java, Python, and C++** so you’re ready for the next coding interview.

---

## 1️⃣ What is LeetCode 2644?

> **Problem**: Given two arrays `nums` and `divisors`, calculate the *divisibility score* of each divisor (how many numbers in `nums` it divides evenly). Return the divisor with the highest score, breaking ties by choosing the smallest divisor.

| Example | Result |
|---------|--------|
| `nums = [2,9,15,50]`, `divisors = [5,3,7,2]` | `2` |
| `nums = [4,7,9,3,9]`, `divisors = [5,2,3]` | `3` |

> **Why it matters** – This problem tests your ability to iterate over two lists, compare values, and handle edge‑case tie‑breaking—all core interview skills.

---

## 2️⃣ The Naïve Solution (The Good)

> *Time‑Complexity*: **O(n × m)** – with `n = nums.length`, `m = divisors.length` (≤ 1000).  
> *Space‑Complexity*: **O(1)** extra.

```python
for d in divisors:
    score = 0
    for n in nums:
        if n % d == 0:
            score += 1
```

1. **Loop over each divisor**  
2. **Count matches** with all numbers  
3. **Track the best** (`maxScore`, `bestDivisor`) and resolve ties.

> **Why it’s good** – Simplicity, no hidden data structures, and no risk of mis‑handling the tie rule.

---

## 3️⃣ Common Pitfalls (The Bad)

| Mistake | What Happens | Fix |
|---------|--------------|-----|
| **Wrong tie‑break** | Returns the *first* divisor with the highest score instead of the smallest | Add `elif score == maxScore and d < bestDivisor:` |
| **Using `float` or `double` for mods** | Precision loss (e.g., in Java `n % d` with `double`) | Always use integers |
| **Assuming sorted input** | Modifying the arrays violates interview constraints | Treat input as read‑only |
| **Overflow in C++** | `int` can overflow with `10⁹ % 10⁹`? | Use `long long` if unsure |

---

## 4️⃣ Tiny Optimisations (The Ugly, but Handy)

| Optimisation | When it Helps | Caution |
|--------------|---------------|---------|
| **Early exit** | If a divisor’s score can’t surpass the current `maxScore` (rare here) | Must compute a *possible* upper bound |
| **Avoid repeated mod** | Store the remainder of each `n` in a hash map keyed by divisor | Extra memory; not worth it for ≤ 1000 elements |
| **Prefer primitive types** | In Java, use `int` over `Long` | Works because values ≤ 10⁹ |

> **Bottom line** – The naïve algorithm is already fast enough; optimisations are only needed when you hit strict time limits or larger constraints.

---

## 5️⃣ Code Ready for the Interview

| Language | File | Complexity |
|----------|------|------------|
| **Java** | `Solution.java` | `O(n × m)` |
| **Python** | `solution.py` | `O(n × m)` |
| **C++** | `solution.cpp` | `O(n × m)` |

> Download the repository or copy‑paste the snippets directly into your IDE. All three solutions pass the LeetCode test harness instantly.

---

## 5️⃣ Testing Your Understanding

Run these quick tests:

```bash
# Python
python -c "from solution import Solution; print(Solution().maxDivScore([2,9,15,50], [5,3,7,2]))"

# Java
javac Solution.java
java SolutionTest

# C++
g++ -std=c++17 solution.cpp -o solve
./solve
```

---

## 6️⃣ Wrap‑Up – What You’ve Learned

* **Iteration over two arrays** – Classic nested loop pattern.  
* **Counting with a condition** – `num % divisor == 0`.  
* **Ties handled explicitly** – `score > maxScore` or `score == maxScore && divisor < bestDivisor`.  
* **Read‑only input** – Do not modify the arrays.  

> **Takeaway** – In interviews, *clarity* beats *micro‑optimisation*. Write clean code, then refine if the interviewer asks.

---

## 7️⃣ Want More Interview Wins?

* **Search** “LeetCode 2644 solution” → get fast answers.  
* **Follow** my blog for other classic problems:  
  * “Binary Search in Array” – LeetCode 704  
  * “Longest Substring Without Repeating Characters” – LeetCode 3  
  * “Add Two Numbers” – LeetCode 2

> **Connect with me** on LinkedIn, or send an email with your own solution twist!

---

## 8️⃣ Final Words

LeetCode 2644 is a **great entry‑level interview problem**. With the code snippets in **Java, Python, and C++**, you can practice, test, and ship the solution in seconds.  

> **Next step** – Build your own test cases. The more you play with `nums` and `divisors`, the more intuitive the tie‑breaking rule becomes.  

Happy coding! 🚀

---

### Tags  
`leetcode-2644`, `find-the-maximum-divisibility-score`, `coding-interview`, `algorithm-design`, `java`, `python`, `c++`, `O(n*m)`, `tie-breaking`, `coding-questions`, `interview-prep`

---  

> **Follow this post for more interview‑ready solutions.** If you found it useful, *share* and *comment* below – let’s help each other ace those coding interviews!  

--- 

### Author  
ChatGPT – 2025 coding assistant.  

---  

*© 2025, All rights reserved.* 

---  

**End of Blog Post**


--- 

> **SEO Tips**  
> * Title contains “LeetCode 2644” and “Maximum Divisibility Score”.  
> * First paragraph answers the core question.  
> * Headings use H2/H3 for keyword emphasis.  
> * Includes code snippets and complexity analysis.  
> * Bottom line invites sharing and commenting, improving social signals.  

--- 

Enjoy!