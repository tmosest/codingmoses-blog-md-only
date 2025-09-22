---
title: LeetCode 1911. Maximum Alternating Subsequence Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 1911 – *Maximum Alternating Subsequence Sum*  
> **Java | Python | C++ Solutions + SEO‑Optimized Blog Post**  

---

## 🚀 TL;DR  
- **Goal:** Find the largest possible alternating sum of any subsequence of an array.  
- **Optimal Solution:** One‑pass DP with two running variables (`bestEven`, `bestOdd`).  
- **Complexities:** **O(n)** time, **O(1)** space.  
- **Why it matters:** Demonstrates greedy/DP intuition, integer overflow awareness, and clean code – perfect for an interview.

---

## 📚 Problem Recap

> The *alternating sum* of a 0‑indexed array is  
> \[
>  \sum_{\text{even }i} a_i \;-\; \sum_{\text{odd }i} a_i
> \]  
>  
> Given an array `nums`, choose any subsequence (order preserved) and compute its alternating sum.  
> Return the maximum possible value.

### Constraints
- `1 ≤ nums.length ≤ 10⁵`
- `1 ≤ nums[i] ≤ 10⁵`

---

## 🧠 Intuition & “Good, Bad, Ugly”

| **Aspect** | **What’s Good** | **Potential Bad** | **Ugly Edge‑Case** |
|-----------|-----------------|-------------------|--------------------|
| **Greedy idea** | Pick largest numbers alternatingly | Can miss the “look‑ahead” needed for later numbers | A sequence like `[1, 100, 2]` – greedy picks 100 alone, but `[1,100,2]` gives 101 |
| **DP formulation** | 2 states capture parity of last chosen element | Need to handle negative/large intermediate values | Very large sums → overflow if using `int` |
| **Implementation** | O(1) space, single pass | Easy to mess up indices | Forget that a subsequence can be *empty* (but constraints guarantee at least one element) |

The clean DP beats the greedy in *all* corner cases.  
The trick is remembering that the subsequence’s *parity* (even/odd position in the subsequence, not in the original array) decides whether we add or subtract the current number.

---

## 📈 The Optimal DP

Let  

- `bestEven` – maximum alternating sum of a subsequence that ends on an **even** position (i.e., we *add* the last chosen number).  
- `bestOdd` – maximum alternating sum of a subsequence that ends on an **odd** position (i.e., we *subtract* the last chosen number).

Initially `bestEven = 0` (empty subsequence, sum = 0).  
`bestOdd` starts at `-∞` because we cannot finish on an odd index without having taken at least one element.

For each number `x` in `nums`:

```
newEven = max(bestEven, bestOdd + x)   // either skip x or use x at an even position
newOdd  = max(bestOdd,  bestEven - x)  // either skip x or use x at an odd position
bestEven, bestOdd = newEven, newOdd
```

At the end `bestEven` is the answer.

Why it works?  
The recurrence is a classic “take or skip” DP.  
We keep the best possible sums for the two parities; the transition considers the new number being appended to a subsequence of the opposite parity (hence `bestOdd + x` or `bestEven - x`) or simply ignored (`bestEven` / `bestOdd` unchanged).

---

## 🏁 Code (Java, Python, C++)

### 1. Java

```java
import java.util.*;

public class Solution {
    public long maxAlternatingSum(int[] nums) {
        long bestEven = 0;            // best sum ending on an even position
        long bestOdd = Long.MIN_VALUE / 2; // -∞ (avoid overflow when adding)

        for (int x : nums) {
            long newEven = Math.max(bestEven, bestOdd + x);
            long newOdd  = Math.max(bestOdd, bestEven - x);
            bestEven = newEven;
            bestOdd  = newOdd;
        }
        return bestEven;
    }

    // For quick manual testing
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.maxAlternatingSum(new int[]{4,2,5,3})); // 7
        System.out.println(s.maxAlternatingSum(new int[]{5,6,7,8})); // 8
        System.out.println(s.maxAlternatingSum(new int[]{6,2,1,2,4,5})); // 10
    }
}
```

> **Why `Long.MIN_VALUE / 2`?**  
> Prevents `bestOdd + x` from overflowing when `bestOdd` is at `Long.MIN_VALUE`.

---

### 2. Python

```python
class Solution:
    def maxAlternatingSum(self, nums: list[int]) -> int:
        best_even = 0          # sum ending on even index
        best_odd  = -10**18     # effectively -∞

        for x in nums:
            new_even = max(best_even, best_odd + x)
            new_odd  = max(best_odd,  best_even - x)
            best_even, best_odd = new_even, new_odd

        return best_even

# Quick manual tests
if __name__ == "__main__":
    sol = Solution()
    print(sol.maxAlternatingSum([4,2,5,3]))          # 7
    print(sol.maxAlternatingSum([5,6,7,8]))          # 8
    print(sol.maxAlternatingSum([6,2,1,2,4,5]))      # 10
```

> **Why use `-10**18`?**  
> Python's integers are arbitrary‑precision, but using a large negative sentinel keeps the logic clear.

---

### 3. C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maxAlternatingSum(vector<int>& nums) {
        long long bestEven = 0;                     // best sum ending on even position
        long long bestOdd  = LLONG_MIN / 4;         // -∞ (safe from overflow)

        for (int x : nums) {
            long long newEven = max(bestEven, bestOdd + x);
            long long newOdd  = max(bestOdd, bestEven - x);
            bestEven = newEven;
            bestOdd  = newOdd;
        }
        return bestEven;
    }
};

// Example usage
int main() {
    Solution s;
    vector<int> a1 = {4,2,5,3};
    vector<int> a2 = {5,6,7,8};
    vector<int> a3 = {6,2,1,2,4,5};
    cout << s.maxAlternatingSum(a1) << endl; // 7
    cout << s.maxAlternatingSum(a2) << endl; // 8
    cout << s.maxAlternatingSum(a3) << endl; // 10
}
```

> **Why `LLONG_MIN / 4`?**  
> Avoids accidental overflow when adding `x`. In practice, `LLONG_MIN/4` is far less than any possible intermediate sum.

---

## 📊 Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| One scan over `nums` | **O(n)** | **O(1)** (just two long variables) |
| Additional overhead | None | None |

`n ≤ 10⁵`, so the algorithm comfortably fits in time limits and uses negligible memory.

---

## 🔎 SEO‑Optimized Blog Article

> **Title:**  
> *LeetCode 1911: Master the Maximum Alternating Subsequence Sum in Java, Python & C++ – A Job‑Interview Essential*  

---

### Introduction

When preparing for a software engineering interview, the *Maximum Alternating Subsequence Sum* problem (LeetCode 1911) often appears on the “must‑know” list. It tests a candidate’s ability to think about **parity**, **dynamic programming**, and **greedy pitfalls**—skills every hiring manager looks for.

In this post we walk through the problem, provide clean implementations in **Java**, **Python**, and **C++**, and explain why the DP approach is the gold standard. Whether you’re a junior coder or a senior engineer polishing your algorithmic skills, this guide will help you nail the question—and impress your interviewers.

---

### Problem Statement (Short & Sweet)

> Given an array `nums`, find the maximum alternating sum of any subsequence after reindexing the elements.  
> *Alternating sum* = sum of elements at even indices minus sum at odd indices.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[4,2,5,3]` | `7` | Take subsequence `[4,2,5]`: `(4+5) - 2 = 7` |
| `[5,6,7,8]` | `8` | Best subsequence is `[8]` |
| `[6,2,1,2,4,5]` | `10` | Subsequence `[6,1,5]`: `(6+5) - 1 = 10` |

**Constraints**

- `1 ≤ nums.length ≤ 10⁵`
- `1 ≤ nums[i] ≤ 10⁵`

---

### Why It Matters for Interviews

- **Parity Handling** – Many interviewers check if you understand that “even/odd” refers to the subsequence position, not the original array index.
- **Space‑Efficiency** – The optimal solution uses O(1) space, a common requirement for “high‑performance” interview questions.
- **Overflow Awareness** – Using 64‑bit integers (`long`/`long long`) is essential; naive `int` solutions fail on large test cases.

---

### The Greedy Misstep (What *not* to do)

A tempting solution is to greedily pick the largest numbers alternatingly.  
However, it fails on sequences like `[1, 100, 2]`.  
Greedy would pick `100` alone (sum = 100), but the optimal subsequence is `[1,100,2]` → `(1+2)-100 = -97`? Wait, that’s smaller. Actually in that case, greedy works. But consider `[1, 2, 3]`: greedy picks `3` → 3; optimal is `[1,3]` → `(1+3) - 2 = 2`. Greedy misses the benefit of having an odd element in between. This illustrates why a DP approach that keeps track of both parities is necessary.

---

### The Elegant DP Solution

#### Key Idea

Maintain two variables:

- `bestEven`: maximum alternating sum of a subsequence that ends on an **even** position.
- `bestOdd`: maximum alternating sum of a subsequence that ends on an **odd** position.

At each step we decide to *use* the current number or *skip* it. If we use it, we must flip the parity of the subsequence, hence the transitions:

```
newEven = max(bestEven, bestOdd + x)
newOdd  = max(bestOdd,  bestEven - x)
```

Finally, `bestEven` is the answer.

#### Why It Works

It’s a classic “take or skip” DP.  
Each transition considers the best possible subsequence of the opposite parity, appends the current number appropriately, or ignores it.  
Because we always use the best known sums for each parity, we never miss a better subsequence later.

---

### Full Implementations

> **(See the code snippets above – Java, Python, C++)**

Feel free to copy/paste into your IDE or online editor. Each implementation follows the same O(n) time and O(1) space pattern.

---

### Handling Overflow

When we start `bestOdd` with `Long.MIN_VALUE` (or `LLONG_MIN`), adding `x` can overflow.  
A safe trick: use a large negative sentinel such as `-10**18` (Python) or `LLONG_MIN / 4` (C++/Java).  
This keeps the logic intact and protects against hidden test cases that push the sum near 2⁶³.

---

### Summary

| Language | Complexity | Why It’s a Go‑To Solution |
|----------|------------|---------------------------|
| **Java** | O(n) time, O(1) space | Uses 64‑bit `long`; clean object‑oriented style |
| **Python** | O(n) time, O(1) space | Simplicity & readability, arbitrary‑precision safety |
| **C++** | O(n) time, O(1) space | Fastest execution, use of `long long` for safety |

The DP method is simple, fast, and robust. Master it, and you’ll handle parity‑based subsequence problems with confidence.

---

### Practice, Practice, Practice

- **Time yourself** on the provided examples and a handful of random tests.
- **Try edge cases**: monotonic arrays, alternating high/low patterns, and the maximum allowed length with all values = 10⁵.
- **Explain the DP to a friend** or even aloud in a mock interview; articulation is as important as the code.

---

### Final Words

LeetCode 1911 is more than just a coding exercise; it’s a test of *conceptual clarity* and *robustness*.  
With the DP solution above, you can confidently approach any job interview that throws this problem your way.  

Happy coding, and may your interviews be as smooth as our `bestEven` transition!  

---  

> *For more interview‑ready articles, subscribe to our newsletter and get weekly algorithm challenges delivered straight to your inbox.*  

---

## 🎯 Takeaway

- **Always think about subsequence parity**, not original indices.  
- Keep two *best sums*: even → add, odd → subtract.  
- Update with `max(skip, take)` transitions.  
- Use 64‑bit integers to avoid overflow.

Implement the DP once in Java, Python, and C++; now you’re ready to solve LeetCode 1911 in any language your interviewers choose. Good luck!

---

### Tags

`algorithms`, `dynamic-programming`, `leetcode`, `Java`, `Python`, `C++`, `interview-preparation`, `job-interview`, `parity`, `greedy`

--- 

**Author:** *Your Name – Senior Software Engineer & Algorithm Enthusiast*  
**Follow on Twitter:** @YourHandle  
**Newsletter:** Subscribe for weekly algorithmic challenges.

---

### Closing

The *Maximum Alternating Subsequence Sum* is a perfect micro‑lesson on **how to design a DP that tracks state succinctly**.  
When you can explain this approach clearly in an interview, you’re not just answering a single problem—you’re showcasing the *mindset* interviewers look for: clean, efficient, and aware of edge cases.

Good luck! 🚀

--- 

### 🎯 Bonus: Quick Checklist for the Interview

1. **Read the problem** – confirm understanding of *subsequence parity*.
2. **Plan the DP** – two variables for even/odd sums.
3. **Use 64‑bit integers** – `long` / `long long` / Python int.
4. **Single pass** – iterate once over `nums`.
5. **Return** `bestEven` (subsequence ending on even position).
6. **Explain** your recurrence clearly to the interviewer.

---

*End of Post*  

---

**Keywords**: LeetCode 1911, maximum alternating subsequence sum, dynamic programming, interview problem, Java implementation, Python implementation, C++ implementation, job interview, algorithm, parity, O(1) space.  

--- 

> *Stay tuned for our next post: “Top 10 LeetCode Problems Every Senior Engineer Should Master.”*

--- 

*Happy coding!*  

--- 

(End of blog article)  

--- 

### 🎉 Bonus: Run the Code Locally

If you want to test the Java solution locally, simply copy the class above into a file named `Solution.java`, compile with `javac Solution.java`, and run `java Solution`.  

For Python, run `python3 solution.py`.  
For C++, compile with `g++ -std=c++17 solution.cpp -O2 -Wall && ./a.out`.  

All outputs match the expected results.

---

### Closing Thought

Algorithmic elegance is not just about speed—it's also about *clarity* and *robustness*.  
The DP solution to LeetCode 1911 demonstrates that: a single line of recurrence, two `long` variables, and one loop.  
With this in your toolbox, you’re one step closer to acing that next interview.

Good luck, and may your jobs be as good as the sums you compute! 🚀

--- 

*End of article.*  

--- 

**Done!**  

--- 

> If you liked this content, share it with friends or leave a comment below with your own implementation tricks!  

--- 
