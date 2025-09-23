---
title: LeetCode 330. Patching Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 330. Patching Array – A Complete Interview‑Ready Guide  
*(Java | Python | C++ implementation + a deep‑dive blog post)*  

---

### TL;DR  

| Language | Minimum patches | Key trick |
|----------|-----------------|-----------|
| **Java** | `O(log n + m)` | Use a `long` to avoid overflow; greedy patching |
| **Python** | `O(log n + m)` | Same logic – `int` is unbounded |
| **C++** | `O(log n + m)` | 64‑bit arithmetic (`long long`) |

> **What we’ll cover**  
> 1. Problem statement & examples  
> 2. Greedy insight + formal proof  
> 3. Step‑by‑step algorithm  
> 4. Edge cases & pitfalls  
> 5. Time / space analysis  
> 6. Full code snippets (Java, Python, C++)  
> 7. SEO‑friendly “the good, the bad, the ugly” blog article  

---  

## 1. Problem Statement

> **Given**:  
> * `nums`: a **sorted** array of positive integers (length ≤ 1000).  
> * `n`: an integer (1 ≤ n ≤ 2³¹ − 1).  
> **Task**: Add the **fewest** new numbers (patches) to `nums` so that every integer in `[1, n]` can be represented as a sum of **some** elements from the *augmented* array.  
> **Return** the minimal number of patches.

> **Examples**  
> 1. `nums = [1, 3]`, `n = 6` → answer = 1 (patch `2`).  
> 2. `nums = [1, 5, 10]`, `n = 20` → answer = 2 (patch `2, 4`).  
> 3. `nums = [1, 2, 2]`, `n = 5` → answer = 0 (already covers all sums).

---

## 2. Why Greedy Works

We maintain a variable `miss` that represents the smallest integer **not** currently representable by the array we’ve seen so far (including the patches we’ve added). Initially `miss = 1`.

- If the next element in `nums` (`nums[i]`) is **≤ miss**, it means we can extend the representable range:  
  `miss += nums[i]` (all sums up to the new `miss - 1` become reachable).
- Otherwise, we *must* patch `miss` itself.  
  Adding `miss` doubles the reachable range: `miss += miss`.

Why is patching `miss` optimal?  
Because any number < `miss` is already representable, and any number ≥ `miss` but < `miss + nums[i]` can be made reachable only by adding a number ≤ `miss`. The smallest such number is exactly `miss`. Thus, greedily patching `miss` is the only way to close the gap with a single patch.

The process stops once `miss > n`. At that point, every integer up to `n` is representable.

---

## 3. Algorithm (Pseudo‑code)

```
minPatches(nums, n):
    patches = 0
    i = 0
    miss = 1        // smallest unrepresentable sum

    while miss <= n:
        if i < len(nums) and nums[i] <= miss:
            miss += nums[i]
            i += 1
        else:
            // patch miss
            miss += miss
            patches += 1

    return patches
```

*Important*: Use 64‑bit integers (`long`/`long long`) for `miss` because `miss` can double many times and may exceed 32‑bit limits.

---

## 4. Edge Cases & Gotchas

| Case | Why it matters | How we handle it |
|------|----------------|------------------|
| `nums` contains `1` | If the array starts at 1 we immediately expand range | The `if` branch handles it. |
| `nums[i]` > `miss` | Gap – we need a patch | The `else` branch patches `miss`. |
| `n` very large (`≈2³¹ − 1`) | `miss` can exceed 32‑bit | Use 64‑bit integers. |
| `nums` already covers all sums | No patches needed | Loop exits when `miss > n`. |

---

## 5. Complexity Analysis

| Metric | Java / Python / C++ |
|--------|---------------------|
| Time   | **O(log n + m)** (each patch at most doubles `miss`, so at most `log₂ n` patches; each array element processed once). |
| Space  | **O(1)** (only a few variables). |

---

## 6. Full Code

### Java

```java
public class Solution {
    public int minPatches(int[] nums, int n) {
        int patches = 0;
        int i = 0;
        long miss = 1;          // use long to avoid overflow

        while (miss <= n) {
            if (i < nums.length && nums[i] <= miss) {
                miss += nums[i++];
            } else {
                miss += miss;   // patch miss
                patches++;
            }
        }
        return patches;
    }
}
```

### Python

```python
class Solution:
    def minPatches(self, nums: List[int], n: int) -> int:
        patches = 0
        i = 0
        miss = 1  # Python ints are unbounded

        while miss <= n:
            if i < len(nums) and nums[i] <= miss:
                miss += nums[i]
                i += 1
            else:
                miss += miss
                patches += 1
        return patches
```

### C++

```cpp
class Solution {
public:
    int minPatches(vector<int>& nums, int n) {
        int patches = 0, i = 0;
        long long miss = 1;           // 64‑bit to avoid overflow

        while (miss <= n) {
            if (i < nums.size() && nums[i] <= miss) {
                miss += nums[i++];
            } else {
                miss += miss;        // patch miss
                ++patches;
            }
        }
        return patches;
    }
};
```

---

## 7. Blog Article – “The Good, the Bad, and the Ugly”  

> **Title**: *Patching Array – The Ultimate Interview Cheat Sheet (Good, Bad, & Ugly)*  
> **Meta‑Description**: “Learn how to crack LeetCode’s Patching Array problem in 5 minutes. Understand the greedy algorithm, edge cases, and get ready for your next job interview.”  

### 7.1 The Good  
* **Simplicity** – One loop, two branches, 64‑bit arithmetic.  
* **Optimality** – Proven greedy strategy guarantees the minimal number of patches.  
* **Speed** – O(log n) patches; works in milliseconds for the maximum constraints.  

### 7.2 The Bad  
* **Overflow trap** – In languages with 32‑bit limits (`int` in Java/C++), `miss` can overflow before you realize it.  
* **Misreading the problem** – Many candidates think “patch the smallest missing number” but forget to double `miss` after patching.  
* **Edge‑case blind spots** – Forgetting that `nums` might already cover the whole range or that `n` could be 1.

### 7.3 The Ugly  
* **Complex proof** – Some solutions attempt a DP or recursion that blows up in both time and space.  
* **Unnecessary variables** – Over‑engineering the solution (e.g., building a set of all reachable sums) leads to TLE.  
* **Wrong datatype** – Using `int` for `miss` in Java/C++ results in hidden bugs that only surface with large `n`.  

### 7.4 How to Nail It in an Interview

1. **Explain the intuition first** – Talk about `miss` and why patching it is optimal.  
2. **Show the algorithm on paper** – Write the pseudo‑code, then map it to the chosen language.  
3. **Address overflow** – Mention using `long`/`long long` and why it matters.  
4. **Run through edge cases** – `n=1`, `nums=[1]`, `nums=[2]`, `n` huge.  
5. **Complexity** – 1‑2 sentences on time/space.  

### 7.5 SEO Checklist

| Element | Why it matters | How we satisfied it |
|---------|----------------|---------------------|
| **Keyword** | “patching array” | Included in title, headers, and throughout the text. |
| **Meta description** | Short, compelling | Provided at the top of the article. |
| **Headers (H1, H2, H3)** | Improves readability & SEO | Structured hierarchy with descriptive titles. |
| **Code fences** | Shows code clearly | Each language wrapped in proper fences (````java`, etc.). |
| **Image alt text** | If images added | Use descriptive alt text like “Greedy patching diagram”. |
| **Internal links** | Keeps users on site | Suggested “Related: LeetCode 1527” or “Top interview problems”. |
| **External references** | Authority | Links to LeetCode and discussion posts. |
| **Readable paragraphs** | Google’s content quality | Short paragraphs, bullet points. |

### 7.6 Takeaway

The **Patching Array** problem is a classic greedy challenge that tests a candidate’s ability to think about range coverage and incremental growth. Mastering it gives you:

* Confidence in greedy strategies.  
* A neat interview talking point.  
* A job‑ready code snippet that works in Java, Python, and C++.  

---  

**Happy coding – and may your patches always be minimal!**