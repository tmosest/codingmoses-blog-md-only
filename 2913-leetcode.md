---
title: LeetCode 2913. Subarrays Distinct Element Sum of Squares I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2913 – Subarrays Distinct Element Sum of Squares (Easy)

> **Problem** –  
> For every sub‑array `nums[i..j]` calculate the number of *distinct* values inside it, square that number, and sum it for **all** possible sub‑arrays.  
> Return the total sum.

The constraints are tiny (`n ≤ 100`, `nums[i] ≤ 100`), so a simple **brute‑force** approach is fast enough, but we’ll walk through the logic, pitfalls, and why it’s still worth understanding.

---

### TL;DR

| Language | Complexity | Code |
|----------|------------|------|
| **Java** | O(n²) time, O(n) space | `class Solution { public int sumCounts(List<Integer> nums) {...}}` |
| **Python** | O(n²) time, O(n) space | `class Solution: def sumCounts(self, nums: List[int]) -> int: ...` |
| **C++** | O(n²) time, O(n) space | `class Solution { public: int sumCounts(vector<int>& nums) {...}}` |

---

## 1. The Brute‑Force Idea – The Good

Since `n ≤ 100`, the number of sub‑arrays is only `n(n+1)/2 ≤ 5050`.  
For each sub‑array we need the count of distinct elements.  
The simplest way:

1. Iterate over all starting indices `i`.
2. Keep a `HashSet` (or `unordered_set`) of distinct numbers while extending the end index `j`.
3. Update the answer with `size * size`.

**Why it works** – Every sub‑array is examined exactly once, and the set guarantees we never double‑count duplicates.

```java
class Solution {
    public int sumCounts(List<Integer> nums) {
        int n = nums.size();
        int ans = 0;

        for (int i = 0; i < n; i++) {
            Set<Integer> seen = new HashSet<>();
            for (int j = i; j < n; j++) {
                seen.add(nums.get(j));
                int distinct = seen.size();
                ans += distinct * distinct;
            }
        }
        return ans;
    }
}
```

Python / C++ analogues are identical in logic.

---

## 2. Edge Cases & Pitfalls – The Bad

| Issue | Explanation | Fix |
|-------|-------------|-----|
| **Using `int` for the answer** | `n = 100` → max sub‑arrays ≈ 5050, each with at most `n` distinct numbers → worst sum ≈ 5050 * 100² = 50 500 000, fits in `int`. Still, for safety use `long` in Java / `long long` in C++. | `long ans = 0;` |
| **Wrong set type** | Using a `TreeSet` gives `O(log n)` inserts; unnecessary overhead. | Use `HashSet` / `unordered_set`. |
| **Mutable list from input** | `nums` may be passed as an immutable view. Accessing `nums.get(j)` is fine, but avoid `list.add()` on it. | Do not modify the input list. |
| **Large `n` assumption** | If constraints change to `n = 10⁵`, this algorithm would blow up. | Use a sliding window + two‑pointer technique for `O(n log n)` or `O(n)` with frequency array. |

---

## 3. Can We Do Better? – The Ugly

For *this* problem the brute‑force is already optimal because:

- The output requires **every** sub‑array to be considered; we cannot skip any.
- The data size is tiny – the constant factors of a more complex algorithm outweigh the asymptotic benefit.

However, if you ever need to scale:

1. **Two‑Pointer + Frequency Map**  
   Slide a window, keep a map of frequencies. The distinct count changes only when a value's frequency becomes 1 or 0. Still `O(n²)` because each sub‑array start needs its own window.

2. **Divide & Conquer / Mo’s Algorithm**  
   Useful for offline queries about sub‑array statistics. Not necessary here.

---

## 4. Full Code (Java, Python, C++)

### Java

```java
import java.util.*;

class Solution {
    public int sumCounts(List<Integer> nums) {
        int n = nums.size();
        long answer = 0;                     // use long to avoid overflow
        for (int i = 0; i < n; i++) {
            Set<Integer> seen = new HashSet<>();
            for (int j = i; j < n; j++) {
                seen.add(nums.get(j));
                int distinct = seen.size();
                answer += (long) distinct * distinct;
            }
        }
        return (int) answer;                 // result fits into int
    }
}
```

### Python

```python
class Solution:
    def sumCounts(self, nums: List[int]) -> int:
        n = len(nums)
        ans = 0
        for i in range(n):
            seen = set()
            for j in range(i, n):
                seen.add(nums[j])
                d = len(seen)
                ans += d * d
        return ans
```

### C++

```cpp
#include <vector>
#include <unordered_set>
using namespace std;

class Solution {
public:
    int sumCounts(vector<int>& nums) {
        int n = nums.size();
        long long ans = 0;
        for (int i = 0; i < n; ++i) {
            unordered_set<int> seen;
            for (int j = i; j < n; ++j) {
                seen.insert(nums[j]);
                int d = seen.size();
                ans += 1LL * d * d;
            }
        }
        return static_cast<int>(ans);
    }
};
```

All three snippets share the same logic; the only differences are language syntax and minor type details.

---

## 5. SEO‑Optimized Blog Article

> **Title**  
> “Solving LeetCode 2913 – Subarrays Distinct Element Sum of Squares (Java, Python, C++)”

> **Meta Description**  
> Learn how to crack LeetCode 2913 with clean, O(n²) code in Java, Python, and C++. Understand the problem, edge cases, and why brute‑force is perfect for this easy challenge. Boost your interview prep today!

---

### 🚀 Introduction

If you’re hunting a software engineering role, cracking LeetCode problems is a must‑have. Today we tackle **LeetCode 2913 – Subarrays Distinct Element Sum of Squares**. Though the problem is marked *Easy*, it’s a fantastic exercise in:

- **Brute‑force elegance** – sometimes the simplest solution is the best.
- **Set usage** – counting distinct values efficiently.
- **Code readability** – writing clean code that impresses recruiters.

Below, we’ll walk through the solution in Java, Python, and C++, highlight the good, bad, and ugly aspects, and give you a polished write‑up you can share on your portfolio or LinkedIn.

---

### 🎯 Problem Recap

> For every contiguous sub‑array `nums[i..j]`, count the number of *distinct* elements. Square that count and add it to a running total. Return the final sum.

**Constraints**

- `1 ≤ nums.length ≤ 100`
- `1 ≤ nums[i] ≤ 100`

Because the array is tiny, an `O(n²)` solution is perfectly acceptable.

---

### 🛠️ The Brute‑Force Approach (Good)

1. **Outer loop** – choose the start index `i`.  
2. **Inner loop** – extend the end index `j`.  
3. **HashSet** – keep all distinct elements seen so far.  
4. **Update answer** – `ans += size(set) * size(set)`.

#### Why it’s clean

- No complicated data structures beyond `HashSet`.  
- Every sub‑array is processed once.  
- The code reads almost verbatim from the problem statement.

---

### ⚠️ Common Mistakes (Bad)

| Mistake | What to Avoid | How to Fix |
|---------|---------------|------------|
| Using `int` for the sum | Overflow on edge cases. | Use `long` (Java/C++) or `long long`. |
| `HashSet` vs `TreeSet` | Extra log‑n overhead. | Stick with `HashSet`/`unordered_set`. |
| Modifying input | Unexpected side‑effects. | Never mutate the input list/array. |
| Forgetting to reset the set | Wrong counts across sub‑arrays. | Re‑instantiate the set for each `i`. |

---

### 🔧 Optimization Thoughts (Ugly)

Given the constraints, you **don’t** need more advanced techniques. However, if `n` were larger, consider:

- **Two‑pointer sliding window** – maintain a frequency map and adjust distinct count when the window slides.  
- **Mo’s algorithm** – sort queries to minimize adjustments.  
- **Divide‑and‑conquer** – treat sub‑array start/end as independent problems.

For this problem, brute‑force is *exactly* the right level of complexity.

---

### 📋 Final Code (All Languages)

*Java*  

```java
class Solution {
    public int sumCounts(List<Integer> nums) {
        int n = nums.size();
        long answer = 0;
        for (int i = 0; i < n; i++) {
            Set<Integer> seen = new HashSet<>();
            for (int j = i; j < n; j++) {
                seen.add(nums.get(j));
                int d = seen.size();
                answer += (long) d * d;
            }
        }
        return (int) answer;
    }
}
```

*Python*  

```python
class Solution:
    def sumCounts(self, nums: List[int]) -> int:
        ans = 0
        for i in range(len(nums)):
            seen = set()
            for j in range(i, len(nums)):
                seen.add(nums[j])
                d = len(seen)
                ans += d * d
        return ans
```

*C++*  

```cpp
class Solution {
public:
    int sumCounts(vector<int>& nums) {
        long long ans = 0;
        for (int i = 0; i < nums.size(); ++i) {
            unordered_set<int> seen;
            for (int j = i; j < nums.size(); ++j) {
                seen.insert(nums[j]);
                int d = seen.size();
                ans += 1LL * d * d;
            }
        }
        return static_cast<int>(ans);
    }
};
```

---

### 📈 Takeaways for Interviews

- **Explain your approach** – Even if you use a brute‑force solution, articulate why it fits the constraints.  
- **Discuss edge cases** – Show you’ve considered overflow, mutable inputs, and data structure choice.  
- **Show awareness of scalability** – Mention that while brute‑force works here, larger inputs would need a different strategy.

---

### 🎉 Final Thoughts

LeetCode 2913 is a classic “write the simplest correct solution” problem. Mastering it shows recruiters you can:

- Translate problem statements into clean code.  
- Pick the right data structure for the job.  
- Be mindful of language‑specific pitfalls.

Use the snippets above to add to your GitHub or portfolio, and feel confident stepping into your next coding interview!

---

**Happy coding, and may your interview be as straightforward as the solution!**