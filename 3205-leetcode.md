---
title: LeetCode 3205. Maximum Array Hopping Score I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  Code – 3205 Maximum Array Hopping Score I  

The problem is a classic **O(n)** greedy problem.  
Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.

> **Signature (LeetCode)**
> ```text
> public int maxScore(int[] nums);        // Java
> int maxScore(vector<int>& nums);        // C++
> int maxScore(vector<int> nums);         // Python (class based)
> ```

---

### 📌  Java 17
```java
import java.util.*;

public class Solution {
    public int maxScore(int[] nums) {
        int res = 0;
        int mx = 0;                     // best value seen on the right
        for (int i = nums.length - 1; i > 0; i--) {
            mx = Math.max(mx, nums[i]); // keep the maximum to the right
            res += mx;                  // jump from i-1 to i (or further)
        }
        return res;
    }
}
```

**Why it works** –  
We traverse from the rightmost element to the left.  
At every step we keep the maximum value seen so far (`mx`).  
Jumping from the previous index to the *current* maximum gives the highest score, so we simply add `mx` to the result.

---

### 📌  Python 3
```python
class Solution:
    def maxScore(self, nums: List[int]) -> int:
        res = 0
        mx = 0
        # iterate from the end to the second element (index 1)
        for i in range(len(nums) - 1, 0, -1):
            mx = max(mx, nums[i])   # best value on the right of i-1
            res += mx               # score for jumping from i-1 to i (or farther)
        return res
```

*One‑liner version* (if you love brevity):

```python
from itertools import accumulate
class Solution:
    def maxScore(self, nums: List[int]) -> int:
        return sum(accumulate(nums[:0:-1], max))
```

---

### 📌  C++ (g++17)
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int maxScore(vector<int>& nums) {
        int res = 0, mx = 0;
        for (int i = nums.size() - 1; i > 0; --i) {
            mx = max(mx, nums[i]); // maximum to the right of i-1
            res += mx;             // score for this hop
        }
        return res;
    }
};
```

---

## 2️⃣  Blog Article – “The Good, The Bad, and The Ugly of **Maximum Array Hopping Score**”

> **Keywords**: Leetcode, Maximum Array Hopping Score, algorithm, greedy, dynamic programming, O(n), O(1), interview, coding interview, Java, Python, C++  

### 🚀 Introduction  

During a recent interview I was stumped by LeetCode #3205 “Maximum Array Hopping Score I”.  It’s a deceptively simple problem that hides a neat greedy insight.  In this post I’ll walk you through the intuition, prove why the greedy choice is optimal, present clean Java/Python/C++ solutions, and point out common pitfalls.  By the end you’ll be ready to ace this problem (and similar ones) in your next coding interview.

---

### 📄 Problem Statement (LeetCode #3205)

> You are given an array `nums`. Starting at index 0, you must hop to the last index.  
> In one hop you can jump from index `i` to any `j > i`.  
> Each hop awards you a score of `(j - i) * nums[j]`.  
> Find the maximum total score you can obtain.

**Constraints**

- `2 ≤ nums.length ≤ 10^3`
- `1 ≤ nums[i] ≤ 10^5`

---

### 🔎 Intuition – Why a Greedy Solution Works

1. **Local Decision, Global Optimum**  
   Consider any state at index `i`.  Suppose the maximum element on the right of `i` is at position `k` with value `nums[k]`.  
   *If you jump from `i` to `k` in one hop, the score is `(k-i)*nums[k]`.  
   *If you first hop to some `j` (`i < j < k`) and then to `k`, the total score is `(j-i)*nums[j] + (k-j)*nums[k]`.

2. **Comparing the Two**  
   ```
   (k-i)*nums[k]  versus  (j-i)*nums[j] + (k-j)*nums[k]
   ```
   Move the common `(k-j)*nums[k]` to the left:
   ```
   (j-i)*nums[j]  <  (k-i)*nums[k] - (k-j)*nums[k] = (j-i)*nums[k]
   ```
   Since `nums[k]` is the *maximum* among all `nums[j]`, the inequality always holds.  
   Therefore the *single* hop to the maximum element on the right is strictly better (or equal if `nums[j] == nums[k]`).

3. **Induction**  
   By repeating the argument for every index, the optimal path is:
   - Start at `0`
   - Jump to the maximum element in `nums[1 … n-1]`
   - From there, jump to the maximum in its suffix, and so on, until the last element

   The algorithm is essentially “keep jumping to the largest number to your right”.

---

### ⚙️ Implementation – O(n) / O(1)

Traverse the array from right to left:

| Step | Action |
|------|--------|
| 1    | Keep a variable `mx` = maximum value seen so far (initially `0`). |
| 2    | For each index `i` from `n-1` down to `1`:  
   - Update `mx = max(mx, nums[i])` (now `mx` is the best value to the right of `i-1`).  
   - Add `mx` to the running answer `res`. |

The final `res` is the maximal score.

- **Time**: `O(n)` – single pass.  
- **Space**: `O(1)` – constant extra memory.

---

### 📚 Code Snippets

> **Java**
> ```java
> public int maxScore(int[] nums) {
>     int res = 0, mx = 0;
>     for (int i = nums.length - 1; i > 0; i--) {
>         mx = Math.max(mx, nums[i]);
>         res += mx;
>     }
>     return res;
> }
> ```

> **Python**
> ```python
> class Solution:
>     def maxScore(self, nums: List[int]) -> int:
>         res = mx = 0
>         for i in range(len(nums)-1, 0, -1):
>             mx = max(mx, nums[i])
>             res += mx
>         return res
> ```

> **C++**
> ```cpp
> class Solution {
> public:
>     int maxScore(vector<int>& nums) {
>         int res = 0, mx = 0;
>         for (int i = nums.size() - 1; i > 0; --i) {
>             mx = max(mx, nums[i]);
>             res += mx;
>         }
>         return res;
>     }
> };
> ```

---

### 🏗️ “The Good”

- **Simplicity** – A single reverse loop and two integer variables.  
- **Performance** – Linear time and constant memory.  
- **Proof‑based** – The greedy choice can be formally proven optimal.  
- **Cross‑language** – The same logic works in Java, Python, C++ (and many others).  

---

### ⚠️ “The Bad”

- **Edge‑case oversight** – Forgetting to start the loop from `n-1` and not including the last element in the answer (some naive solutions mistakenly ignore the final hop).  
- **Large Input** – While `n ≤ 1000` here, the same pattern works for huge arrays; just ensure 64‑bit arithmetic (`long` in Java, `long long` in C++) if values exceed `2^31-1`.  
- **Misunderstanding the formula** – Thinking that the score is `i * nums[j]` instead of `(j-i)*nums[j]` leads to wrong answers.  

---

### 🧟 “The Ugly”

- **Dynamic‑Programming Redundancy** – Some candidates try a full DP table `dp[i] = max over j>i of (j-i)*nums[j] + dp[j]`, which is `O(n^2)` and unnecessary.  
- **Complex Recursion** – Recursive memoization with an index argument is overkill for a simple greedy solution.  
- **Over‑Optimizing** – Micro‑optimizing the inner loop with bit‑wise tricks or fancy libraries can hurt readability, which is a key interview criterion.

---

### 💡 Interview Tips

1. **Explain the Greedy Idea Early** – Show the intuition before writing code.  
2. **State the Invariant** – “`mx` is the maximum value to the right of the current index”.  
3. **Prove Correctness** – Walk through the two‑jump comparison succinctly.  
4. **Mention Edge Cases** – e.g., all elements equal, strictly decreasing array.  
5. **Time/Space Complexity** – Always mention `O(n)` / `O(1)`.  

---

### 🔚 Conclusion  

Maximum Array Hopping Score I is a perfect example of a problem where a *single* greedy observation turns a seemingly complex DP into a one‑liner.  By keeping the maximum suffix value, we guarantee the best jump at every step and achieve optimality in linear time.

Feel free to copy the snippets above into your favorite IDE, run the provided test cases, and share them on your portfolio or GitHub.  Good luck with your next interview—remember: clarity, proof, and brevity are your best allies! 🚀

---

**Tags:** #LeetCode #Algorithm #Greedy #DynamicProgramming #CodingInterview #Java #Python #C++ #JobInterview #ProgrammingTips