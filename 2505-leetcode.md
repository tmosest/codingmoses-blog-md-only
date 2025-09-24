---
title: LeetCode 2505. Bitwise OR of All Subsequence Sums - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Code Solutions

Below are concise, production‑ready implementations for **LeetCode 2505 – Bitwise OR of All Subsequence Sums** in **Java, Python, and C++**.  
All three solutions run in **O(n)** time and **O(1)** auxiliary space – perfect for interviews and real‑world use.

> **Problem Recap**  
> Given an integer array `nums`, compute  
> \[
> \bigvee_{S \subseteq nums}\left(\sum_{x \in S} x\right)
> \]
> i.e. the bitwise OR of *every possible* subsequence sum.

---

### C++

```cpp
// 2505. Bitwise OR of All Subsequence Sums
// Time:  O(n)
// Space: O(1)
class Solution {
public:
    long long subsequenceSumOr(vector<int>& nums) {
        long long ans = 0;      // final OR
        long long pref = 0;     // running prefix sum
        for (int x : nums) {
            pref += x;          // current prefix sum
            ans |= (long long)x | pref;  // include x and pref
        }
        return ans;
    }
};
```

---

### Java

```java
// 2505. Bitwise OR of All Subsequence Sums
// Time:  O(n)
// Space: O(1)
class Solution {
    public long subsequenceSumOr(int[] nums) {
        long ans = 0;      // final OR
        long pref = 0;     // running prefix sum
        for (int x : nums) {
            pref += x;     // current prefix sum
            ans |= (long) x | pref; // include x and pref
        }
        return ans;
    }
}
```

---

### Python 3

```python
# 2505. Bitwise OR of All Subsequence Sums
# Time:  O(n)
# Space: O(1)
class Solution:
    def subsequenceSumOr(self, nums: List[int]) -> int:
        ans = 0          # final OR
        pref = 0         # running prefix sum
        for x in nums:
            pref += x
            ans |= x | pref
        return ans
```

> **Why this works** –  
> Every bit that can become `1` in any subsequence sum either  
> 1. appears already in some element `x`, or  
> 2. is produced by a carry from a lower bit when we sum a prefix.  
> Tracking the running prefix `pref` guarantees that we capture all carries with a single linear pass.

---

## 2. SEO‑Optimized Blog Article

### Title  
**“Cracking LeetCode 2505: Bitwise OR of All Subsequence Sums – Java, Python & C++ Solutions, Intuition, and Interview Tips”**

---

### Meta Description  
Struggling with LeetCode 2505? Learn the O(n) solution that uses a running prefix sum, understand the intuition, and get ready to ace interview questions about bitwise operations in Java, Python, or C++. Boost your coding résumé today!

---

## 2.1 Introduction  

LeetCode’s **“Bitwise OR of All Subsequence Sums”** (problem #2505) is a *medium‑level* challenge that tests your grasp of **bitwise manipulation**, **dynamic programming**, and **array traversal**.  
It’s a favorite among hiring managers at tech giants because it forces candidates to think about how bits propagate through addition—a concept that appears in **C++, Java, and Python** interview stacks.

In this article, we’ll:

1. Walk through the **good** aspects of the problem.
2. Reveal the **bad** pitfalls that often trip up interviewees.
3. Discuss the **ugly** corner cases and how to handle them.
4. Present clean, production‑ready solutions in **Java**, **Python**, and **C++**.
5. Share interview‑style questions to deepen your understanding.

> **Keywords**: LeetCode, bitwise OR, subsequence sums, algorithm, O(n), interview prep, coding interview, Java, Python, C++, job interview, coding resume

---

## 2.2 The “Good” – Why This Problem Matters  

- **Real‑world relevance**: Bitwise operations are core to systems programming, cryptography, and performance‑critical code.  
- **Conceptual clarity**: It forces you to think about how carries work in binary addition.  
- **Cross‑language applicability**: The same O(n) logic translates to Java, Python, and C++, showing mastery over language nuances.  
- **Scalable constraints**: `n` up to `10^5` and `nums[i]` up to `10^9` make a naïve solution infeasible—testing your ability to design efficient algorithms.

---

## 2.3 The “Bad” – Common Mistakes  

| Mistake | Why it Happens | Fix |
|---------|----------------|-----|
| **Enumerating all subsequences** | Thinking “try every combination” | Use prefix sums – a single pass is enough. |
| **Using `int` for sums** | Overflow when `n * max(nums)` > 2³¹ | Use `long`/`long long` or Python’s arbitrary precision ints. |
| **Ignoring bit carries** | Failing to understand that carries propagate bits | Track running prefix `pref` and OR it with each element. |
| **Confusing OR with XOR** | Misreading “bitwise OR” as “XOR” | Remember that `|` is OR; XOR would be `^`. |

---

## 2.4 The “Ugly” – Edge Cases & Implementation Quirks  

1. **All zeros** – The answer must be `0`.  
2. **Large sums** – Even if individual numbers are small, the running sum can reach `10^14`; still fits in 64‑bit.  
3. **Negative numbers?** – Problem guarantees non‑negative, but if you adapt the solution, you must use signed types carefully.  
4. **Very long arrays** – Avoid building auxiliary arrays; the O(1) space solution is essential.  

---

## 2.5 Intuition & Proof Outline  

1. **Bitwise OR definition**  
   \[
   u = \bigvee_{S} \text{sum}(S) \implies u[i] = 1 \;\text{iff}\; \exists S: \text{sum}(S)[i] = 1
   \]
2. **Two sources for a set bit**  
   - The bit is already present in some element `x`.  
   - The bit is produced by a carry from lower bits during a subsequence sum.
3. **Prefix sum captures carries**  
   Adding the current element `x` to the running prefix `pref` effectively considers all subsequences that *end* at `x`.  
   Therefore `pref` contains **all carry contributions** up to that point.  
4. **OR everything**  
   `ans |= x | pref` ensures we capture both sources.  
5. **Induction**  
   Assume after processing first `k-1` elements the OR is correct.  
   For element `k`, we include its own bits (`x`) and all carry bits (`pref`).  
   Thus after the update, the OR is correct for first `k`.  

This simple O(n) algorithm is both optimal and elegant.

---

## 2.6 Production‑Ready Code Snippets  

*(See the Code section at the top for full implementations.)*  

### Java (Java 11+)

```java
class Solution {
    public long subsequenceSumOr(int[] nums) {
        long ans = 0, pref = 0;
        for (int x : nums) {
            pref += x;
            ans |= (long) x | pref;
        }
        return ans;
    }
}
```

### Python 3

```python
class Solution:
    def subsequenceSumOr(self, nums: List[int]) -> int:
        ans = pref = 0
        for x in nums:
            pref += x
            ans |= x | pref
        return ans
```

### C++

```cpp
class Solution {
public:
    long long subsequenceSumOr(vector<int>& nums) {
        long long ans = 0, pref = 0;
        for (int x : nums) {
            pref += x;
            ans |= (long long)x | pref;
        }
        return ans;
    }
};
```

---

## 2.7 Interview‑Style Questions to Practice  

1. **Proof** – Why does `pref` capture all possible carry bits for subsequence sums?  
2. **Complexity** – If `n` were `10^6`, would this solution still pass? Explain.  
3. **Edge case** – What would happen if negative numbers were allowed?  
4. **Extension** – How would you modify the algorithm to return the *set* of all possible subsequence sums (not just OR)?  
5. **Alternate approach** – Can you solve this problem using dynamic programming? Compare with the prefix‑sum method.

---

## 2.8 Takeaway & Job‑Ready Skills  

- Mastery of **bitwise operations** and understanding how they interact with arithmetic.  
- Ability to design **linear‑time, constant‑space** algorithms for high‑constraint problems.  
- Comfortable translating an algorithm between **Java, Python, and C++** – a highly desirable skill for full‑stack roles.  
- Clear reasoning: able to explain *why* a solution works, which is exactly what hiring managers ask.

---

### Final Thought  

LeetCode 2505 may look simple at first glance, but it’s a **gateway problem** that tests both conceptual depth and coding polish. By mastering the prefix‑sum OR trick, you’ll not only ace this challenge but also showcase the level of algorithmic thinking that top tech companies value.

Happy coding, and good luck on your next interview! 🚀

---


---


> **Author**: *[Your Name]* – Software Engineer, LeetCode enthusiast, and career‑coach for aspiring developers.  
> **Contact**: `[email] | LinkedIn | GitHub`  

--- 

**End of Article**  

--- 

This completes the comprehensive solution set and the SEO‑friendly blog post ready to land your next coding interview.