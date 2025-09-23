---
title: LeetCode 3659. Partition Array Into K-Distinct Groups - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3659. Partition Array Into K‑Distinct Groups – Solution, Code & Blog

> **Problem (LeetCode 3659)**  
> Given an integer array `nums` and an integer `k`, determine whether all the elements of `nums` can be partitioned into one or more groups such that:
> 1. Each group contains **exactly `k` elements**.  
> 2. All elements inside a group are **distinct**.  
> 3. Every element of `nums` is used **exactly once**.  
> Return `true` if such a partition exists, otherwise `false`.

The solution below is written in **Java**, **Python**, and **C++**.  
The article that follows explains the intuition (“good”), the common pitfalls (“bad”), and the tricky edge cases (“ugly”).  
The article is SEO‑optimised for job‑interview success (keywords: *LeetCode 3659*, *partition array k distinct groups*, *hashmap solution*, *Java/Python/C++ interview problem*).

---

## ✅ The Core Idea – The “Good”

1. **Number of groups**  
   *If we have `n = nums.length` items and each group must have exactly `k` items, then we need `n % k == 0` groups.*  
   If this condition fails, the answer is instantly `false`.

2. **Frequency bound**  
   *Every distinct value can appear at most once in a single group.*  
   Hence if a value occurs `cnt` times, we need at least `cnt` groups to place them all.  
   The number of available groups is `n / k`.  
   Therefore **`cnt` must not exceed `n / k`** for any value.

3. **Final decision**  
   If the array size is a multiple of `k` **and** no frequency exceeds `n/k`, the partition is always possible – we can simply distribute each copy of a value into a different group.

This logic is both **O(n)** time and **O(n)** space (hash map).

---

## 🧪 Code

### 1. Java

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public boolean partitionArray(int[] nums, int k) {
        int n = nums.length;
        if (n % k != 0) return false;          // groups must be integral

        int groups = n / k;
        Map<Integer, Integer> freq = new HashMap<>();

        for (int v : nums) {
            freq.put(v, freq.getOrDefault(v, 0) + 1);
            if (freq.get(v) > groups) {         // pigeon‑hole violation
                return false;
            }
        }
        return true;
    }
}
```

### 2. Python

```python
from collections import Counter
from typing import List

class Solution:
    def partitionArray(self, nums: List[int], k: int) -> bool:
        n = len(nums)
        if n % k:
            return False                # cannot split into full groups

        groups = n // k
        freq = Counter(nums)

        return all(c <= groups for c in freq.values())
```

### 3. C++

```cpp
#include <unordered_map>
#include <vector>

class Solution {
public:
    bool partitionArray(std::vector<int>& nums, int k) {
        int n = nums.size();
        if (n % k != 0) return false;          // groups must be integer

        int groups = n / k;
        std::unordered_map<int, int> freq;

        for (int v : nums) {
            if (++freq[v] > groups) return false;   // pigeon‑hole violation
        }
        return true;
    }
};
```

---

## 📖 Blog Article – “The Good, The Bad, and the Ugly of Partitioning an Array into K‑Distinct Groups”

### Introduction

If you’re preparing for a software‑engineering interview, the **LeetCode 3659** problem is a sweet spot: it tests your ability to combine basic data structures with combinatorial reasoning. In this article we’ll dissect the problem, walk through a clean solution in Java, Python, and C++, and explore common mistakes (“bad”) and subtle pitfalls (“ugly”). We’ll finish with SEO‑friendly tags that’ll help you land that next interview call.

---

### 1. Problem Recap (Good)

> *Given `nums` and `k`, can we partition `nums` into groups of size `k` with distinct elements in each group?*

Key points to remember:

- **Full groups only** – you can’t leave any element unused.
- **Distinctness** – within a group, every value must be unique.
- **No group size constraints** – groups can be any number as long as they each contain `k` items.

---

### 2. Observations – The “Pigeon‑hole Principle” (Good)

- **Groups Count**: If `n` is not divisible by `k`, there’s no way to split into equal‑sized groups.  
  **→ Quick check**: `n % k != 0` → `false`.

- **Frequency Cap**: Suppose the number `5` appears 4 times and you have only 3 groups (`n/k = 3`). At least one group would need to contain two `5`s – violating distinctness.  
  **→ Check**: For every value `x`, `freq[x]` must be ≤ `n/k`.

The combination of these two checks is both necessary and sufficient.

---

### 3. Algorithm & Complexity (Good)

```text
1. n ← length(nums)
2. if n % k != 0 → return false
3. groups ← n / k
4. Count frequencies with a hash map
5. If any frequency > groups → return false
6. return true
```

- **Time**: `O(n)` – a single pass to count.
- **Space**: `O(n)` – hash map holds at most `n` distinct keys.

---

### 4. Common Pitfalls – The “Bad”

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| **Sorting and greedy pairing** | Sorting doesn’t respect the *distinct* rule across groups. | Use frequency count instead. |
| **Ignoring `n % k`** | You might return `true` for `[1,1,2]`, `k=2` (because counts fit), but you can’t form full groups. | Always perform the divisibility check first. |
| **Using `maxFrequency > k`** | Wrong bound: should compare to number of groups, not `k`. | Compute `groups = n / k` and compare to it. |
| **Mutable counters in recursion** | Unnecessary recursion introduces high overhead. | Iterative approach with a hashmap is cleaner. |

---

### 5. Edge Cases – The “Ugly”

- **All elements equal** (`nums = [1,1,1,1]`, `k=2`) → `n/k = 2`, `freq[1] = 4` > 2 → `false`.  
- **Maximum allowed values** (`nums.length = 10^5`, each value up to `10^5`) → ensure your hashmap/Counter can handle this size.  
- **k = 1** → trivial; just need `n % 1 == 0` (always true) and `max freq <= n`. Always `true`.  
- **k = n** → one group; condition reduces to all elements distinct → check `max freq == 1`.

---

### 6. Final Thoughts

The elegance of this problem lies in two simple mathematical observations. Once you see them, the solution is a single pass over the array. The real interview challenge is remembering the *frequency cap* vs. *group count* distinction and avoiding over‑complicated greedy or recursive hacks.

---

### 7. SEO Checklist (Job‑Interview Boost)

- **Title**: “LeetCode 3659 – Partition Array Into K‑Distinct Groups: Java, Python, C++ Solutions & Interview Tips”
- **Meta Description**: “Master LeetCode 3659 in minutes! Read our detailed walkthrough, get Java/Python/C++ code, and learn how to ace the interview.”
- **Headings**: H1 (problem), H2 (Observations, Algorithm, Pitfalls, Edge Cases), H3 (Code Snippets)
- **Keywords**: *LeetCode 3659*, *partition array*, *k distinct groups*, *hashmap solution*, *Java interview coding*, *Python coding interview*, *C++ coding interview*
- **Internal Links**: Link to other LeetCode solution posts if part of a blog series.
- **External Links**: Reference the official LeetCode problem page and the top-rated solution threads.

---

### 8. Takeaway

- **Remember**: `n % k == 0` and `maxFreq <= n / k`.  
- **Implement**: A single pass frequency counter – that's all you need.  
- **Practice**: Try variations like “partition into groups of size `k` with at least one duplicate” to deepen your understanding.

Good luck on your interview journey – and happy coding! 🚀

--- 

*Prepared by [Your Name], Software Engineer & Interview Coach*