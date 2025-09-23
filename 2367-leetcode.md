---
title: LeetCode 2367. Number of Arithmetic Triplets - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Overview  

**LeetCode 2367 – Number of Arithmetic Triplets**  

You’re given a strictly increasing array `nums` and a positive integer `diff`.  
A triplet `(i, j, k)` is called an *arithmetic triplet* when

| Condition | Description |
|-----------|-------------|
| `i < j < k` | Indices are in ascending order |
| `nums[j] - nums[i] == diff` | Consecutive difference is `diff` |
| `nums[k] - nums[j] == diff` | Same consecutive difference |

Return the count of unique arithmetic triplets in `nums`.

> **Constraints**  
> * `3 ≤ nums.length ≤ 200`  
> * `0 ≤ nums[i] ≤ 200`  
> * `1 ≤ diff ≤ 50`  
> * `nums` is strictly increasing

---

## 2.  Why This Problem Is a Great Interview Starter  

| What’s good | Why it matters |
|-------------|----------------|
| **Very small input size** | Lets you experiment with different strategies without worrying about TLE. |
| **Strictly increasing** | Eliminates duplicate indices and allows us to use simple set‑based look‑ups. |
| **Straightforward logic** | Focuses on the candidate’s ability to spot a pattern rather than obscure tricks. |
| **Multiple optimal solutions** | Enables discussion of trade‑offs between clarity and speed. |

---

## 3.  Solution Ideas

1. **Brute‑Force (`O(n³)`)** – iterate over all triples and check the condition.  
   *Fast enough for the given limits, but not elegant.*

2. **Two‑Pointer / Two‑Loop (`O(n²)`)** – for each `i` find `j` such that `nums[j] = nums[i] + diff`, then find `k` with `nums[k] = nums[j] + diff`.  
   *Good for learning pointer techniques.*

3. **HashSet / Boolean Array (`O(n)`)** – store every element in a set.  
   For each element `x`, if both `x+diff` and `x+2*diff` are present, we found a triplet.  
   *Optimal and clean.*

Below are the three implementations in **Java, Python, and C++**.

---

## 4.  Code

### 4.1  Java

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    /* ------------------ O(n) HashSet solution ------------------ */
    public int arithmeticTriplets(int[] nums, int diff) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) set.add(num);

        int count = 0;
        for (int num : nums) {
            if (set.contains(num + diff) && set.contains(num + 2 * diff)) {
                count++;
            }
        }
        return count;
    }

    /* ------------------ O(n) Boolean array solution ------------------ */
    public int arithmeticTripletsBoolean(int[] nums, int diff) {
        // values <= 200, so fixed-size array is safe
        boolean[] present = new boolean[201];
        int count = 0;

        for (int num : nums) {
            if (num >= 2 * diff && present[num - diff] && present[num - 2 * diff]) {
                count++;
            }
            present[num] = true;
        }
        return count;
    }

    /* ------------------ O(n^2) two‑loop solution ------------------ */
    public int arithmeticTripletsO2(int[] nums, int diff) {
        int n = nums.length;
        int count = 0;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (nums[j] - nums[i] != diff) break;   // array is increasing
                for (int k = j + 1; k < n; k++) {
                    if (nums[k] - nums[j] == diff) count++;
                    else if (nums[k] - nums[j] > diff) break;
                }
            }
        }
        return count;
    }
}
```

### 4.2  Python

```python
from typing import List

class Solution:
    # -------------------------------------------------------------
    # O(n) solution using a set
    # -------------------------------------------------------------
    def arithmeticTriplets(self, nums: List[int], diff: int) -> int:
        s = set(nums)
        count = 0
        for x in nums:
            if x + diff in s and x + 2 * diff in s:
                count += 1
        return count

    # -------------------------------------------------------------
    # O(n) solution using a fixed‑size boolean list (value <= 200)
    # -------------------------------------------------------------
    def arithmeticTriplets_bool(self, nums: List[int], diff: int) -> int:
        present = [False] * 201
        count = 0
        for x in nums:
            if x >= 2 * diff and present[x - diff] and present[x - 2 * diff]:
                count += 1
            present[x] = True
        return count

    # -------------------------------------------------------------
    # O(n^2) brute‑force with two loops
    # -------------------------------------------------------------
    def arithmeticTriplets_bruteforce(self, nums: List[int], diff: int) -> int:
        n = len(nums)
        count = 0
        for i in range(n):
            for j in range(i + 1, n):
                if nums[j] - nums[i] != diff:
                    break
                for k in range(j + 1, n):
                    if nums[k] - nums[j] == diff:
                        count += 1
                    elif nums[k] - nums[j] > diff:
                        break
        return count
```

### 4.3  C++

```cpp
#include <vector>
#include <unordered_set>

class Solution {
public:
    // -------------------------------------------------------------
    // O(n) solution using unordered_set
    // -------------------------------------------------------------
    int arithmeticTriplets(const std::vector<int>& nums, int diff) {
        std::unordered_set<int> s(nums.begin(), nums.end());
        int count = 0;
        for (int x : nums) {
            if (s.count(x + diff) && s.count(x + 2 * diff))
                ++count;
        }
        return count;
    }

    // -------------------------------------------------------------
    // O(n) solution using fixed-size boolean array (value <= 200)
    // -------------------------------------------------------------
    int arithmeticTripletsBool(const std::vector<int>& nums, int diff) {
        bool present[201] = {false};
        int count = 0;
        for (int x : nums) {
            if (x >= 2 * diff && present[x - diff] && present[x - 2 * diff])
                ++count;
            present[x] = true;
        }
        return count;
    }

    // -------------------------------------------------------------
    // O(n^2) solution with two nested loops
    // -------------------------------------------------------------
    int arithmeticTripletsO2(const std::vector<int>& nums, int diff) {
        int n = nums.size();
        int count = 0;
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                if (nums[j] - nums[i] != diff) break;
                for (int k = j + 1; k < n; ++k) {
                    if (nums[k] - nums[j] == diff) ++count;
                    else if (nums[k] - nums[j] > diff) break;
                }
            }
        }
        return count;
    }
};
```

---

## 5.  Blog Article: “The Good, The Bad, and The Ugly of Number of Arithmetic Triplets”

### 5.1  Title  

**Number of Arithmetic Triplets – LeetCode 2367: A Coding Interview Deep‑Dive**  

*(Keywords: LeetCode, Number of Arithmetic Triplets, Java, Python, C++, interview, coding interview, algorithm, hashset, time complexity, O(n), job interview)*  

---

### 5.2  Intro  

> “During your next coding interview, you’re asked to find all *arithmetic triplets* in a sorted array. It looks easy—so why do many candidates struggle?”  

This article unpacks the **good** (what makes the problem approachable), the **bad** (common pitfalls), and the **ugly** (the tricky edge cases) of LeetCode 2367. We’ll also see how to solve it in **Java, Python, and C++** in under 10 lines of code.  

---

### 5.3  The Good

| Aspect | Explanation |
|--------|-------------|
| **Intuitive Pattern** | `nums[i] + diff` and `nums[i] + 2*diff` – a clear “look‑ahead” property. |
| **Small Input Space** | With only 200 elements, we can experiment with multiple algorithms. |
| **Strictly Increasing** | Guarantees that indices are unique; no need to worry about duplicate triplets. |
| **Multiple Optimal Solutions** | Shows candidates that a problem can have both an “obvious” and a “clever” approach. |

> *SEO cue*: “LeetCode 2367 easy solution”, “arithmetic triplet interview question”

---

### 5.4  The Bad

| Pitfall | Fix |
|---------|-----|
| **Confusing `+` vs `-`** | Many candidates accidentally check `x‑diff` instead of `x+diff`. |
| **Off‑by‑one Index Errors** | Mis‑placing `<` versus `<=` in the nested loops can double‑count or miss a triplet. |
| **Ignoring the Strictly Increasing Property** | Writing a generic set‑lookup that works for any array (including duplicates) wastes time. |
| **Ignoring Constraints** | Using a dynamic array of size `nums.size()*2` instead of a 201‑slot boolean array can lead to memory inefficiency. |

> *SEO cue*: “common interview mistakes”, “hashset pitfalls”, “time complexity LeetCode”

---

### 5.5  The Ugly

| Tricky Edge | How to Handle It |
|-------------|------------------|
| **Large `diff`** | If `diff` is close to 100, `x + 2*diff` might exceed 200; use a safe guard (`x >= 2*diff`). |
| **Overflow in C++** | `x + 2*diff` can overflow `int` if `diff` were huge; though not in this problem, always cast to `long long` for safety in real‑world code. |
| **Negative Numbers** | Though constraints forbid them, a generic solution should still handle negatives gracefully. |
| **Sorting Mis‑Assumption** | Remember: the array *is* sorted. Skipping the sort step saves time but you can’t reorder it. |

> *SEO cue*: “LeetCode coding challenge pitfalls”, “debugging interview code”

---

## 6.  Take‑Away Checklist (for Interviewers)

- **Ask for the optimal `O(n)` solution** – the candidate should propose a set/boolean‑array approach.  
- **Probe for edge‑case handling** – check if they consider `x ≥ 2*diff`.  
- **Discuss trade‑offs** – why a boolean array is faster than a hash‑set in this case.  
- **Optional follow‑up** – “What if `nums` were not strictly increasing?” (introduces need for duplicate handling).

---

## 7.  TL;DR  

- **Goal**: Count `(i, j, k)` with `nums[j] - nums[i] = nums[k] - nums[j] = diff`.  
- **Optimal**: `O(n)` using a hash‑set or a fixed‑size boolean array.  
- **Code**: Ready‑to‑run solutions in **Java, Python, C++**.  
- **Interview Value**: Demonstrates pattern recognition, set usage, and time‑complexity awareness.  

Good luck crushing this LeetCode problem and nailing your next coding interview!