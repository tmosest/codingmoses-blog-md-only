---
title: LeetCode 3496. Maximize Score After Pair Deletions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ LeetCode 3496 – “Maximize Score After Pair Deletions”  
### The Good, The Bad, and The Ugly  
### A Complete, Job‑Ready Guide (Java, Python, C++)

---

### 🚀 TL;DR  
* **Idea:** Keep the *least valuable* part of the array (a single element if the length is odd, or an adjacent pair if the length is even) and delete everything else.  
* **Result:**  
  * `odd length` → `maxScore = sum(nums) – min(nums)`  
  * `even length` → `maxScore = sum(nums) – min(adjacent‑pair‑sum)`  
* **Complexity:** `O(n)` time, `O(1)` auxiliary space.  
* **Why it’s a perfect interview story:**  
  * Elegant greedy proof  
  * Zero‑alloc, one‑pass implementation  
  * Fits Java/Python/C++ patterns

---

## 📌 Problem Statement (LeetCode 3496)

> **You are given an integer array `nums`. While the array has more than two elements you may perform one of the following operations:**
> 1. Remove the first two elements.  
> 2. Remove the last two elements.  
> 3. Remove the first and last element.  
>  
> For each operation you add the sum of the removed elements to your total score.  
> **Return the maximum possible score you can achieve.**

---

## 📊 Examples

| Input | Output | Explanation |
|-------|--------|-------------|
| `[2,4,1]` | `6` | Delete first two: `2+4=6` → best. |
| `[5,-1,4,2]` | `7` | Delete first+last: `5+2=7` → best. |

---

## 📐 Why the Strategy Works

1. **Observation**  
   *Every operation deletes exactly two elements.*  
   Therefore, after all possible deletions we will be left with either **one** element (odd length) or **two** adjacent elements (even length).

2. **Goal**  
   *Maximize the score → minimize the sum of the final remaining elements.*  
   Because total sum of the array is fixed, the best we can do is keep the “cheapest” part of the array.

3. **Odd Length**  
   We can reduce to any single element.  
   → Keep the minimum value `min(nums)`.  
   → Score = `sum(nums) – min(nums)`.

4. **Even Length**  
   We can reduce to any **adjacent** pair.  
   → Keep the pair with the smallest sum: `min(nums[i] + nums[i+1])`.  
   → Score = `sum(nums) – min_adjacent_pair_sum`.

5. **Proof Sketch**  
   *By induction on length* – each deletion reduces length by 2, preserving the ability to reach any required final state.  
   The greedy “keep the cheapest” is optimal because the score is linear in deleted elements and the remaining elements are the only thing we are penalized for.

---

## ⌨️ One‑Pass Implementation (O(n) time, O(1) space)

Below you’ll find fully‑commented code for **Java**, **Python**, and **C++**.  
All three use the same linear‑time, constant‑space logic.

### 1️⃣ Java

```java
// Java 17
import java.util.*;

public class Solution {
    public int maxScore(int[] nums) {
        long total = 0;          // use long to avoid overflow
        int n = nums.length;

        // Compute total sum and the minimal element
        int minElem = Integer.MAX_VALUE;
        for (int v : nums) {
            total += v;
            if (v < minElem) minElem = v;
        }

        // If length is odd – keep one minimal element
        if (n % 2 == 1) {
            return (int) (total - minElem);
        }

        // Even length – find minimal adjacent pair sum
        long minPair = Long.MAX_VALUE;
        for (int i = 0; i < n - 1; i++) {
            long pairSum = (long) nums[i] + nums[i + 1];
            if (pairSum < minPair) minPair = pairSum;
        }
        return (int) (total - minPair);
    }
}
```

> **Why `long`?**  
> Sum of up to 1e5 elements each up to 1e4 fits in `int` (≤ 1e9), but subtraction may go negative; `long` keeps things safe.

---

### 2️⃣ Python

```python
# Python 3.10
class Solution:
    def maxScore(self, nums: list[int]) -> int:
        total = sum(nums)
        n = len(nums)

        if n % 2:            # odd length
            return total - min(nums)

        # even length – minimal adjacent pair sum
        min_pair = min(nums[i] + nums[i + 1] for i in range(n - 1))
        return total - min_pair
```

> **Pythonic One‑liner**  
> `return sum(nums) - (min(nums) if len(nums)%2 else min(nums[i]+nums[i+1] for i in range(len(nums)-1)))`

---

### 3️⃣ C++

```cpp
// C++17
class Solution {
public:
    int maxScore(vector<int>& nums) {
        long long total = 0;
        int n = nums.size();

        int minElem = INT_MAX;
        for (int v : nums) {
            total += v;
            minElem = min(minElem, v);
        }

        if (n & 1) {               // odd
            return static_cast<int>(total - minElem);
        }

        long long minPair = LLONG_MAX;
        for (int i = 0; i < n - 1; ++i) {
            long long pair = static_cast<long long>(nums[i]) + nums[i + 1];
            minPair = min(minPair, pair);
        }
        return static_cast<int>(total - minPair);
    }
};
```

---

## 📈 Time & Space Complexity

| Language | Time | Space |
|----------|------|-------|
| Java | **O(n)** | **O(1)** |
| Python | **O(n)** | **O(1)** |
| C++ | **O(n)** | **O(1)** |

All three run in linear time and use constant auxiliary space – perfect for interview benchmarks.

---

## 🛠️ Common Pitfalls & Edge Cases

| Pitfall | Fix |
|---------|-----|
| Using `int` for total sum → overflow on large inputs | Use `long`/`long long` |
| Forgetting that we keep *adjacent* pair for even length | Iterate `i` from `0` to `n-2` |
| Misinterpreting “first two” vs “last two” → need to think globally, not locally | The greedy solution bypasses the need to simulate deletions |
| Off‑by‑one in even length check (`n % 2 == 0` vs `n & 1`) | Be consistent; both work but be clear |
| Negative numbers → `min` still works, but test with all negatives | No change needed – min works fine |

---

## 🧪 Quick Test Harness

### Java (JUnit)

```java
@Test
public void testExamples() {
    Solution s = new Solution();
    assertEquals(6, s.maxScore(new int[]{2,4,1}));
    assertEquals(7, s.maxScore(new int[]{5,-1,4,2}));
}
```

### Python (unittest)

```python
import unittest

class TestSolution(unittest.TestCase):
    def test_examples(self):
        sol = Solution()
        self.assertEqual(sol.maxScore([2,4,1]), 6)
        self.assertEqual(sol.maxScore([5,-1,4,2]), 7)

if __name__ == "__main__":
    unittest.main()
```

### C++ (Google Test)

```cpp
TEST(SolutionTest, Examples) {
    Solution s;
    EXPECT_EQ(6, s.maxScore({2,4,1}));
    EXPECT_EQ(7, s.maxScore({5,-1,4,2}));
}
```

---

## 🎯 Why This Blog Will Help You Land a Job

1. **Algorithmic Clarity** – The greedy proof shows depth in understanding problem constraints.  
2. **Multi‑Language Mastery** – Shows you can translate logic across Java, Python, and C++—a must‑have for full‑stack and systems roles.  
3. **SEO‑Optimized Content** – Keywords like *LeetCode, interview, algorithm, greedy, C++, Java, Python, maximize score, job interview* attract recruiters looking for candidates who have tackled real LeetCode problems.  
4. **Readable, Production‑Ready Code** – Clean, well‑commented snippets that you can paste into your portfolio or GitHub.  
5. **Discussion of Edge Cases** – Demonstrates that you think beyond the happy path—a trait recruiters look for.  

---

## 🚀 Next Steps for Your Interview

1. **Implement from scratch** on a whiteboard: write the linear pass, discuss the greedy reasoning.  
2. **Explain complexity** verbally; show why `O(n)` is optimal.  
3. **Show edge‑case handling** (negative numbers, all positives, single‑element array).  
4. **If asked** about time‑space trade‑offs, propose a variation that uses a priority queue to handle dynamic deletions, but explain why it’s unnecessary here.  
5. **Wrap up** by highlighting how this problem showcases greedy reasoning, linear‑time thinking, and language‑agnostic design—all key interview signals.

---

## 📚 Takeaway

> *When you can reduce a problem to “keep the cheapest part” and delete everything else, the greedy solution is almost always the simplest and fastest. Apply this mindset to similar pair‑deletion or removal problems.*

Happy coding, and may your LeetCode streak and résumé shine! 🌟