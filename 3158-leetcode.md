---
title: LeetCode 3158. Find the XOR of Numbers Which Appear Twice - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3158. Find the XOR of Numbers Which Appear Twice  
**Difficulty:** Easy | **Tags:** Array, Hash Table, Bit Manipulation  

> **Problem**  
> You are given an integer array `nums`. Every number in the array appears **once or twice**.  
> Return the bitwise XOR of **all numbers that appear twice**. If no number appears twice, return `0`.  

### Example
| Input | Output | Explanation |
|-------|--------|-------------|
| `[1,2,1,3]` | `1` | Only `1` appears twice. |
| `[1,2,3]` | `0` | No duplicates. |
| `[1,2,2,1]` | `3` | `1 XOR 2 = 3` |

### Constraints
- `1 ≤ nums.length ≤ 50`
- `1 ≤ nums[i] ≤ 50`
- Each element appears once or twice.

---

## ✅ Short & Efficient Solution (O(n) time, O(n) space)

We count the frequency of each number with a `HashMap` (or an array of size 51 because the values are bounded).  
During a second pass we XOR all keys that have a frequency of `2`.  
Because XOR is associative, the final result is the XOR of all duplicate numbers.

### Java
```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int duplicateNumbersXOR(int[] nums) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int n : nums) {
            freq.put(n, freq.getOrDefault(n, 0) + 1);
        }

        int xor = 0;
        for (Map.Entry<Integer, Integer> e : freq.entrySet()) {
            if (e.getValue() == 2) xor ^= e.getKey();
        }
        return xor;
    }
}
```

### Python
```python
from collections import Counter

class Solution:
    def duplicateNumbersXOR(self, nums: List[int]) -> int:
        freq = Counter(nums)
        result = 0
        for num, cnt in freq.items():
            if cnt == 2:
                result ^= num
        return result
```

### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int duplicateNumbersXOR(vector<int>& nums) {
        unordered_map<int, int> freq;
        for (int n : nums) freq[n]++;

        int xorVal = 0;
        for (auto &p : freq)
            if (p.second == 2) xorVal ^= p.first;
        return xorVal;
    }
};
```

---

## 📖 Blog Article – “The Good, The Bad, and the Ugly” of LeetCode’s *Find the XOR of Numbers Which Appear Twice*  

### Meta Description
> Master LeetCode’s “Find the XOR of Numbers Which Appear Twice” with a clean, O(n) solution in Java, Python, and C++. Learn the pros, pitfalls, and interview‑friendly tricks to ace this easy problem and land your next tech role.

---

### Table of Contents
1. [Problem Overview](#problem-overview)
2. [Why XOR?](#why-xor)
3. [Solution Walk‑through](#solution-walk-through)
4. **The Good** – Clean, Linear‑time algorithm
5. **The Bad** – Edge cases and over‑engineering
6. **The Ugly** – Common mistakes and performance traps
7. [Alternate Approaches](#alternate-approaches)
8. [Testing & Edge‑Case Checklist](#testing)
9. [SEO & Interview Tips](#seo-interview-tips)
10. [Conclusion](#conclusion)

---

## 1. Problem Overview <a name="problem-overview"></a>
LeetCode’s *Find the XOR of Numbers Which Appear Twice* is an **easy** array problem that tests:

- Frequency counting
- Bitwise XOR properties
- Efficient use of hash structures

Because the numbers are small (`≤ 50`) we can even replace a hash map with a fixed‑size array, but a hash map keeps the code generic.

---

## 2. Why XOR? <a name="why-xor"></a>
XOR is a perfect fit for this problem:

| Property | Why It Helps |
|----------|--------------|
| **XOR of identical numbers is 0** | `x ^ x = 0` – duplicates cancel each other out. |
| **XOR is associative & commutative** | Order doesn’t matter – we can accumulate results in any pass. |
| **XOR of `0` with a number is that number** | Starting from `0` is natural. |

These properties let us *collect* duplicates efficiently, without sorting or nested loops.

---

## 3. Solution Walk‑through <a name="solution-walk-through"></a>
1. **Count frequencies** – One linear pass with a hash map or counting array.
2. **XOR duplicates** – Second pass: XOR only those numbers whose count is exactly `2`.
3. **Return** – If no duplicates were found, `xor` remains `0` (the correct answer).

*Time Complexity:* **O(n)** – one or two passes over the array.  
*Space Complexity:* **O(n)** – hash map size proportional to distinct elements (≤ 50).

---

## 4. The Good <a name="the-good"></a>
| Aspect | Why It’s Good |
|--------|---------------|
| **Simplicity** – Two clear loops; no hidden logic. |
| **Deterministic** – Works for all inputs in constraints. |
| **Scalable** – If constraints grow (e.g., `nums.length = 10⁶`), the algorithm still scales linearly. |
| **Language‑agnostic** – Implementation exists in Java, Python, C++ with minimal changes. |
| **Interview‑friendly** – Demonstrates knowledge of hash tables and bitwise ops, both common interview topics. |

---

## 5. The Bad <a name="the-bad"></a>
| Pitfall | Remedy |
|---------|--------|
| **Assuming all duplicates are exactly 2** – Problem guarantees it, but in real data you might see triplicates. | Validate input or add assertions. |
| **Using heavy data structures** – A full `HashMap` is overkill for small `nums`. | Replace with a fixed 51‑element array (`int[51]`). |
| **Not handling empty array** – Not needed for LeetCode but good practice. | Return `0` or raise an exception if `nums.isEmpty()`. |

---

## 6. The Ugly <a name="the-ugly"></a>
| Mistake | Impact | Fix |
|---------|--------|-----|
| **Brute force double loop** – `O(n²)` time. | TLE on larger inputs (even if hidden tests exceed constraints). | Use hash map or frequency array. |
| **Using `Set` to detect duplicates** – `O(n)` but you still need to XOR all duplicates. | Misses duplicates appearing twice if you only check presence. | Store counts, not just presence. |
| **Relying on bitwise magic without explanation** – Interviewers may ask *why* you used XOR. | Risk of being seen as a “black‑box coder”. | Briefly explain XOR properties in your explanation. |
| **Ignoring integer overflow** – XOR doesn’t overflow, but adding frequencies might if you use `int` for large counts. | Rare for constraints but a good habit to use `long`. | Use `int` for counts when max is known, otherwise use `long`. |

---

## 7. Alternate Approaches <a name="alternate-approaches"></a>

1. **Counting Array** (since values ≤ 50):
   ```java
   int[] freq = new int[51];
   for (int n : nums) freq[n]++;
   int xor = 0;
   for (int i = 1; i <= 50; i++) if (freq[i] == 2) xor ^= i;
   ```

2. **Bit Manipulation Without Hash**  
   For each bit position `b` (0..31), count how many numbers have bit `b` set.  
   If a bit appears an even number of times across duplicates, it cancels out.  
   This is more complex and not needed for the given constraints.

3. **Sorting + Scan**  
   Sort the array (O(n log n)) and scan once for pairs.  
   Not as efficient as O(n) counting, but easier to code in languages lacking hash tables.

---

## 8. Testing & Edge‑Case Checklist <a name="testing"></a>
| Test | Input | Expected Output |
|------|-------|-----------------|
| All unique | `[1,2,3,4]` | `0` |
| All duplicates | `[5,5,5,5]` (invalid per constraints) | N/A |
| Single duplicate | `[10,20,10]` | `10` |
| Multiple duplicates | `[1,2,2,1,3,3]` | `0` (1^2^3 = 0) |
| Boundary values | `[50,50]` | `50` |
| Mixed order | `[3,1,2,3,2,1]` | `0` |

---

## 9. SEO & Interview Tips <a name="seo-interview-tips"></a>
| Focus | Why it Matters |
|-------|----------------|
| **Keywords** – “LeetCode XOR problem”, “find XOR of duplicate numbers”, “Java bit manipulation interview” | Increases search traffic and recruiter visibility. |
| **Clear Code Blocks** – Separate language sections, add comments | Makes the article shareable on GitHub, StackOverflow, and coding blogs. |
| **Interview Insight** – Discuss trade‑offs, time/space complexity | Shows depth, a must for senior roles. |
| **Result‑oriented** – Mention “runs in O(n) time” and “handles edge cases” | Recruiters look for efficient solutions. |
| **Call to Action** – “Try this on LeetCode, submit, and track your rating” | Engages readers and boosts engagement metrics. |

---

## 10. Conclusion <a name="conclusion"></a>
LeetCode’s *Find the XOR of Numbers Which Appear Twice* is a quick sanity check for your ability to combine counting with bitwise operations.  
By using a frequency map (or array) and leveraging XOR’s properties, you can solve the problem in linear time with minimal code—exactly the sort of clean, efficient solution interviewers love.

Give it a try on the LeetCode platform, add a couple of unit tests, and don’t forget to document your approach in your portfolio. A solid, interview‑ready solution like this one can be the difference between landing an interview call and going unnoticed. Happy coding! 🚀

---

### 📌 Final Code Snippets (Copy‑Paste Ready)

#### Java
```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int duplicateNumbersXOR(int[] nums) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int n : nums) freq.put(n, freq.getOrDefault(n, 0) + 1);

        int xor = 0;
        for (var entry : freq.entrySet())
            if (entry.getValue() == 2) xor ^= entry.getKey();

        return xor;
    }
}
```

#### Python
```python
from collections import Counter
from typing import List

class Solution:
    def duplicateNumbersXOR(self, nums: List[int]) -> int:
        freq = Counter(nums)
        result = 0
        for num, cnt in freq.items():
            if cnt == 2:
                result ^= num
        return result
```

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int duplicateNumbersXOR(vector<int>& nums) {
        unordered_map<int,int> freq;
        for (int n : nums) ++freq[n];

        int xorVal = 0;
        for (auto &p : freq)
            if (p.second == 2) xorVal ^= p.first;

        return xorVal;
    }
};
```

Happy solving, and best of luck landing that tech job!