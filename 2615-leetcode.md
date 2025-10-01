---
title: LeetCode 2615. Sum of Distances - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2615 – Sum of Distances  
**Medium** – LeetCode

> **Problem**  
> Given an integer array `nums`, create an array `arr` such that  
> `arr[i]` is the sum of `|i-j|` over all indices `j` where `nums[j] == nums[i]` and `j != i`.  
> If there is no other index with the same value, `arr[i] = 0`.  
> Return `arr`.

---

## 1. Why this problem matters

* **Interview staple** – The question appears on many interview lists (LeetCode, GFG, etc.).  
* **Demonstrates mastery of data structures** – Requires a map (`HashMap` / `unordered_map`) and a two‑pass prefix‑sum trick.  
* **Highlights time‑space trade‑offs** – Naïve O(n²) solution is a quick fail; the optimal O(n) is expected.  

Below you’ll find clean, production‑ready solutions in **Java, Python, and C++**, followed by an in‑depth blog post that dives into the *good*, *bad*, and *ugly* parts of this problem.  The article is SEO‑optimised so recruiters searching for “Sum of Distances LeetCode” or “interview algorithm” will see it.

---

## 2. Brute‑Force vs Optimal

| Approach | Complexity | Comments |
|----------|------------|----------|
| Nested loops (for each `i`, scan all `j`) | **O(n²)** | Works for tiny arrays, but explodes when `n = 100,000`. |
| Two‑pass prefix‑sum with hash map | **O(n)** | Optimal – passes the array twice, updating cumulative sums per value. |

The two‑pass approach is the *gold standard* for this problem.  
We’ll walk through it in detail in the blog post.

---

## 3. Reference Solutions

> All three implementations run in **O(n)** time and **O(n)** extra space (the hash map).  
> `long` / `long long` is used because the sum can exceed the 32‑bit range.

### 3.1 Java

```java
import java.util.HashMap;

class Solution {
    /**
     * Return the sum of distances array for the given nums.
     *
     * @param nums input integer array
     * @return array of long where arr[i] is the sum of distances
     */
    public long[] distance(int[] nums) {
        int n = nums.length;
        long[] res = new long[n];

        // Prefix pass – accumulate distances from left to right
        HashMap<Integer, Long> sumMap = new HashMap<>();
        HashMap<Integer, Integer> countMap = new HashMap<>();

        for (int i = 0; i < n; i++) {
            int val = nums[i];
            long sum = sumMap.getOrDefault(val, 0L);
            int cnt  = countMap.getOrDefault(val, 0);

            res[i] += (long) i * cnt - sum;

            sumMap.put(val, sum + i);
            countMap.put(val, cnt + 1);
        }

        // Suffix pass – accumulate distances from right to left
        sumMap.clear();
        countMap.clear();

        for (int i = n - 1; i >= 0; i--) {
            int val = nums[i];
            long sum = sumMap.getOrDefault(val, 0L);
            int cnt  = countMap.getOrDefault(val, 0);

            res[i] += sum - (long) i * cnt;

            sumMap.put(val, sum + i);
            countMap.put(val, cnt + 1);
        }

        return res;
    }
}
```

### 3.2 Python

```python
from collections import defaultdict
from typing import List

def distance(nums: List[int]) -> List[int]:
    n = len(nums)
    res = [0] * n

    # Left‑to‑right pass
    sum_map = defaultdict(int)
    count_map = defaultdict(int)

    for i, val in enumerate(nums):
        res[i] += i * count_map[val] - sum_map[val]
        sum_map[val] += i
        count_map[val] += 1

    # Right‑to‑left pass
    sum_map.clear()
    count_map.clear()

    for i in range(n - 1, -1, -1):
        val = nums[i]
        res[i] += sum_map[val] - i * count_map[val]
        sum_map[val] += i
        count_map[val] += 1

    return res
```

### 3.3 C++

```cpp
#include <vector>
#include <unordered_map>

using namespace std;

class Solution {
public:
    vector<long long> distance(const vector<int>& nums) {
        int n = nums.size();
        vector<long long> res(n, 0);

        unordered_map<int, long long> sumMap;
        unordered_map<int, int> countMap;

        // Left to right
        for (int i = 0; i < n; ++i) {
            int val = nums[i];
            res[i] += 1LL * i * countMap[val] - sumMap[val];
            sumMap[val] += i;
            ++countMap[val];
        }

        // Right to left
        sumMap.clear();
        countMap.clear();

        for (int i = n - 1; i >= 0; --i) {
            int val = nums[i];
            res[i] += sumMap[val] - 1LL * i * countMap[val];
            sumMap[val] += i;
            ++countMap[val];
        }

        return res;
    }
};
```

All three snippets compile and run in sub‑second time for `n = 1e5`.

---

## 4. Blog Post  
*(SEO‑optimised, “The good, the bad, and the ugly”)*

> **Title:** *Sum of Distances LeetCode – The Good, The Bad, The Ugly – A Deep Dive for Interview Success*  
> **Meta‑description:** Master LeetCode 2615 “Sum of Distances” with Java, Python, and C++ solutions. Learn the optimal O(n) algorithm, pitfalls, and interview hacks.  

---

### 4.1 Introduction

When recruiters search “Sum of Distances LeetCode”, they’re usually looking for candidates who can:

1. Translate a problem statement into a clean algorithm.  
2. Justify time‑space trade‑offs.  
3. Write production‑ready code in multiple languages.

This article gives you everything you need to ace that interview question – from a **brute‑force explanation** to a **time‑optimal** implementation and a discussion of real‑world pitfalls.

---

### 4.2 Problem Recap (Good)

- **Input:** `int[] nums` of length `n` (1 ≤ n ≤ 10⁵).  
- **Output:** `long[] arr` where  
  `arr[i] = Σ |i - j|` over all `j` such that `nums[j] == nums[i]` and `j != i`.  
- **Example:**  
  `nums = [1,3,1,1,2] → arr = [5,0,3,4,0]`.

The goal is to compute these sums *efficiently*.

---

### 4.3 Naïve Approach (Bad)

The straightforward method:

```pseudo
for i in 0..n-1:
    sum = 0
    for j in 0..n-1:
        if nums[j] == nums[i] and i != j:
            sum += abs(i-j)
    arr[i] = sum
```

**Complexity:** O(n²) time, O(1) space.

Why this is *bad*:

- For `n = 100,000`, you end up with ~10⁹ distance calculations—far beyond the time limits on LeetCode or a coding interview (usually 1–2 seconds).  
- Even a skilled candidate will waste a lot of interview time debugging a brute‑force solution instead of discussing data structures.

---

### 4.4 Optimal Two‑Pass Strategy (Good)

#### 4.4.1 Intuition

For a fixed value `v`, consider all indices where `nums[i] == v`.  
If you walk left‑to‑right, you can maintain:

- **count(v)** – how many times `v` has appeared so far.
- **sumIndices(v)** – sum of those indices.

When you arrive at index `i`, the contribution of all *previous* occurrences of `v` is:

```
i * count(v) - sumIndices(v)
```

Why?  
`i - j` for each earlier `j` equals `i` minus that `j`. Summing over all earlier `j` gives `i * count(v) - sumIndices(v)`.

Similarly, in the right‑to‑left pass you accumulate:

```
sumIndices(v) - i * count(v)
```

The two passes together give the total distance to *both* sides.

#### 4.4.2 Algorithm

```
res = array[n] initialized to 0
maps: sumMap, countMap

// Left to right
for i = 0 .. n-1:
    val = nums[i]
    res[i] += i * countMap[val] - sumMap[val]
    sumMap[val] += i
    countMap[val] += 1

// Clear maps for second pass
clear sumMap, countMap

// Right to left
for i = n-1 .. 0:
    val = nums[i]
    res[i] += sumMap[val] - i * countMap[val]
    sumMap[val] += i
    countMap[val] += 1
```

#### 4.4.3 Correctness Proof (Brief)

1. **Left pass** accounts for all `j < i` with `nums[j] == nums[i]`.  
   Each contributes `i - j`.  
   Sum over all such `j` equals `i * count - sumIndices`.  

2. **Right pass** accounts for all `j > i` with `nums[j] == nums[i]`.  
   Each contributes `j - i`.  
   Sum over all such `j` equals `sumIndices - i * count`.  

3. Adding the two sums yields the exact definition of `arr[i]`.

#### 4.4.4 Complexity

- **Time:** O(n) – two linear scans.  
- **Space:** O(n) – the hash maps store at most one entry per distinct value (`≤ n`).  

---

### 4.5 Edge Cases & Pitfalls (Ugly)

| Issue | What to Watch For | Fix |
|-------|-------------------|-----|
| **Large sums** | `|i - j|` can reach 10⁵; sum of many such distances can exceed 2³¹‑1. | Use 64‑bit (`long` / `long long`). |
| **Hash map overflow** | In Java, `HashMap<Integer, Integer>` can hit default load factor and rehash – still fine but slower. | Use `HashMap<Integer, Integer>(initialCapacity)` or `unordered_map` in C++. |
| **Clearing maps** | Forgetting to clear maps before the suffix pass results in mixing left and right contributions. | `sumMap.clear(); countMap.clear();` |
| **Integer overflow during multiplication** | `i * count` may overflow 32‑bit. | Cast `i` to `long` before multiplication (`1LL * i * cnt`). |
| **Zero‑based indexing** | The formula `i * count - sum` works only for zero‑based arrays. | Keep array indexing consistent (avoid off‑by‑one). |
| **Duplicate values** | When all elements are identical, maps grow large but still ≤ n. | Nothing extra needed; algorithm remains O(n). |
| **Map key type** | In C++ `unordered_map<int, long long>` uses hash on `int`. For negative numbers it works fine. | Ensure you use `int` as key; `long long` for values. |

---

### 4.6 Interview‑Ready Code Checklist

1. **Type‑safe variables** – always use `long long` or `long` for sums.  
2. **Clear intent** – add comments explaining why the formula works.  
3. **Consistent naming** – `sumMap`, `countMap`, `res` – easy to read.  
4. **Avoid unnecessary globals** – keep all data within the function.  
5. **Test with provided samples** before sending.  

---

### 4.7 Multi‑Language Cheat Sheet

| Language | Key Differences |
|----------|-----------------|
| **Java** | `long[]` for output; `HashMap` requires two maps (`sum` & `count`). |
| **Python** | Use `defaultdict(int)` for maps; explicit `1LL` is unnecessary. |
| **C++** | `unordered_map<int, long long>`; remember to cast multiplications to `long long`. |

Having the same algorithm in three languages showcases versatility—something interviewers value highly.

---

### 4.8 Final Thoughts (Good)

- The **two‑pass prefix‑sum** is the *canonical* solution for “Sum of Distances”.  
- It demonstrates mastery of **hash maps**, **cumulative sums**, and **linear‑time reasoning**.  
- By explaining the algorithm and complexity, you can show *why* your solution is optimal – a conversation that impresses recruiters more than raw speed.

---

### 4.9 Quick Checklist for Interviewers

> *“Could you explain how you would solve LeetCode 2615 in less than 1 second? And write a quick solution in Java/Python/C++?”*

Use this cheat sheet:

1. Mention O(n²) brute‑force first, then pivot to the optimal two‑pass.  
2. Outline the prefix‑sum trick, show the formula.  
3. Provide one of the reference codes.  
4. Talk through edge cases and why `long` is needed.  
5. Wrap up with time/space complexity.

You’ll leave the interviewer convinced that you’re not just a coder but a *solver*.

---

## 5. Conclusion

- **Brute‑force** is *educational* but *time‑consuming* – avoid it in interviews.  
- **Two‑pass hash‑map** algorithm is the optimal solution – O(n) time, O(n) space.  
- Reference code in Java, Python, and C++ demonstrates language flexibility.

Master the intuition, be ready to discuss pitfalls, and bring the reference solutions to your next interview.  
You’re now equipped to make that recruiter’s search results show *you* as the top candidate for LeetCode 2615.

---  

*Happy coding!* 🚀

--- 

> *(End of Blog Post)*

--- 

**End of Documentation**