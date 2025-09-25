---
title: LeetCode 2032. Two Out of Three - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2032. Two Out of Three – LeetCode – A Complete, SEO‑Optimized Guide

> **Keywords**: “Two Out of Three LeetCode”, “leetcode solution Java”, “leetcode solution Python”, “leetcode solution C++”, “coding interview”, “job interview”, “array manipulation”, “hash set”, “bitmask”, “algorithm”.

---

## 📌 Problem Overview

| ID | Title | Difficulty |
|----|-------|------------|
| 2032 | Two Out of Three | Easy |

**Statement**  
You are given three integer arrays `nums1`, `nums2`, `nums3`.  
Return a **distinct** array of all values that appear in **at least two** of the three arrays.  
Order does not matter.

**Constraints**

- `1 ≤ nums1.length, nums2.length, nums3.length ≤ 100`
- `1 ≤ nums1[i], nums2[j], nums3[k] ≤ 100`

---

## 🚀 The “Good” – A Straight‑Forward O(N) Approach

The simplest idea is to use a **frequency counter**.  
Because all numbers are bounded by 100 we can use a plain array (or `List<Integer>` in Java) to store the count of each value across the three arrays.  
After filling the counter we pick the values whose count is ≥ 2.

**Complexities**

- **Time** – `O(n1 + n2 + n3)` – linear in the total number of elements.  
- **Space** – `O(1)` – we only allocate an array of size 101 (constant).

### Java (No `HashSet`)

```java
import java.util.*;

class Solution {
    public List<Integer> twoOutOfThree(int[] nums1, int[] nums2, int[] nums3) {
        // Numbers are between 1 and 100 – 101 slots are enough.
        int[] freq = new int[101];

        for (int v : nums1) freq[v]++;
        for (int v : nums2) freq[v]++;
        for (int v : nums3) freq[v]++;

        List<Integer> result = new ArrayList<>();
        for (int v = 1; v <= 100; v++) {
            if (freq[v] >= 2) result.add(v);
        }
        return result;
    }
}
```

### Python

```python
from typing import List

def twoOutOfThree(nums1: List[int], nums2: List[int], nums3: List[int]) -> List[int]:
    freq = [0] * 101          # index 0 unused
    for v in nums1: freq[v] += 1
    for v in nums2: freq[v] += 1
    for v in nums3: freq[v] += 1

    return [v for v in range(1, 101) if freq[v] >= 2]
```

### C++

```cpp
#include <vector>

std::vector<int> twoOutOfThree(const std::vector<int>& nums1,
                               const std::vector<int>& nums2,
                               const std::vector<int>& nums3) {
    int freq[101] = {0};

    for (int v : nums1) ++freq[v];
    for (int v : nums2) ++freq[v];
    for (int v : nums3) ++freq[v];

    std::vector<int> res;
    for (int v = 1; v <= 100; ++v)
        if (freq[v] >= 2) res.push_back(v);

    return res;
}
```

---

## ❌ The “Bad” – Over‑engineering with Unnecessary Collections

Some people write a solution that uses multiple `HashSet`s or nested loops, which works but has higher memory consumption and can be slower in practice.

```java
Set<Integer> a = new HashSet<>();
Set<Integer> b = new HashSet<>();
Set<Integer> c = new HashSet<>();

for (int v : nums1) a.add(v);
for (int v : nums2) b.add(v);
for (int v : nums3) c.add(v);

Set<Integer> res = new HashSet<>();

for (int v : a) if (b.contains(v) || c.contains(v)) res.add(v);
for (int v : b) if (c.contains(v) && !res.contains(v)) res.add(v);

return new ArrayList<>(res);
```

- **Time** still O(n), but the `contains` operations add overhead.  
- **Space** grows to O(n) because we keep three separate sets.  
- Harder to read – the logic is spread across multiple data structures.

---

## 👿 The “Ugly” – Using Bit‑Masks and Integer Tricks

While clever bit‑mask solutions exist, they are usually harder to understand and maintain. They’re often used on coding challenge sites for bragging rights rather than real interview readiness.

```java
int[] mask = new int[101];
for (int v : nums1) mask[v] |= 1;   // 001
for (int v : nums2) mask[v] |= 2;   // 010
for (int v : nums3) mask[v] |= 4;   // 100

List<Integer> res = new ArrayList<>();
for (int v = 1; v <= 100; v++)
    if (Integer.bitCount(mask[v]) >= 2) res.add(v);
```

- Works in constant space but requires a deeper understanding of bitwise operations.  
- Not idiomatic for interviewees who need to deliver a clear, maintainable solution.

---

## 📚 Why the Simple Counter is the Interview‑Friendly Choice

| Feature | Counter Array | HashSet Approach | Bit‑Mask |
|---------|---------------|------------------|----------|
| **Readability** | ★★★★★ | ★★☆ | ★★ |
| **Runtime** | Linear, no hash lookups | Linear, but more constant factor | Linear, but with bit ops |
| **Space** | Constant | Linear | Constant |
| **Edge‑cases** | None | Must manage duplicates | Must handle mask overflow |
| **Scalability** | Works for any range if you allocate accordingly | Works for any range, but memory heavy | Works for small ranges only |

In a real interview, the interviewer values a solution that is *correct, efficient, and easy to explain*. The frequency counter fulfills all three.

---

## 🎯 SEO‑Ready Takeaways for Your Blog

- **Title**: “Two Out of Three LeetCode – Easy Java, Python & C++ Solutions (With SEO Optimized Interview Guide)”
- **Meta description**: “Learn how to solve LeetCode problem 2032 (Two Out of Three) in Java, Python, and C++. Understand the best algorithm, avoid pitfalls, and impress interviewers. Get the code, complexity analysis, and a job‑ready blog post!”
- **H1**: Two Out of Three – LeetCode Problem 2032  
- **H2**: Problem Statement, Constraints, Examples  
- **H2**: The Good Solution – Frequency Counter  
- **H3**: Java Implementation  
- **H3**: Python Implementation  
- **H3**: C++ Implementation  
- **H2**: Common Mistakes – Bad & Ugly Approaches  
- **H2**: Time & Space Complexity Analysis  
- **H2**: How to Prepare for Interviewers  
- **H2**: Bonus: Bit‑Mask Trick (Optional)  

Include internal links to your other coding blogs (e.g., “Top 10 LeetCode Array Problems”), and external links to the LeetCode problem page and discussion threads. Use bullet points, code blocks, and short paragraphs for readability.

---

## 🎓 Final Thoughts

- **Stick to the simple counter** for clarity and speed.  
- Avoid over‑engineering unless you’re sure the interviewer is looking for a particular data structure.  
- Practice explaining the solution verbally – the “why” is as important as the “how”.  
- Use this blog to showcase your understanding, not just your code. A well‑written article that explains the trade‑offs demonstrates both technical skill and communication ability—exactly what recruiters want.

Happy coding, and best of luck landing that job! 🚀