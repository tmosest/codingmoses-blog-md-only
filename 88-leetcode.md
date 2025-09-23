---
title: LeetCode 88. Merge Sorted Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🚀 LeetCode 88 – Merge Sorted Array  
**Difficulty:** Easy  
**Languages:** Java | Python | C++  
**Time Complexity:** *O(m + n)* (optimal)  
**Space Complexity:** *O(1)* (in‑place)  

> **Want to land that data‑structures interview?**  
> Mastering problems like *Merge Sorted Array* shows recruiters you know how to write clean, optimal code and you can think in terms of *time* vs. *space*. Below you’ll find a deep dive – the “good, the bad, and the ugly” – plus production‑ready solutions in three major languages.  
> **Keywords:** Merge Sorted Array, Leetcode 88, interview coding problem, O(m+n) algorithm, Java, Python, C++ solutions, job interview preparation, data structure interview, technical interview tips.

---

## 📄 Problem Recap

You’re given two integer arrays that are already sorted in non‑decreasing order:

| Variable | Meaning | Size |
|----------|---------|------|
| `nums1`  | First array, **has space for `m + n` elements**. The first `m` elements are valid, the last `n` are `0` and meant as “buffer.” | `m + n` |
| `m`      | Number of valid elements in `nums1`. | `m` |
| `nums2`  | Second array, all `n` elements are valid. | `n` |
| `n`      | Number of elements in `nums2`. | `n` |

Your task is to merge `nums2` into `nums1` such that the resulting array is sorted in non‑decreasing order. The merge must happen **in‑place** (no extra array allocation).

> **Example**  
> ```text
> nums1 = [1,2,3,0,0,0], m = 3
> nums2 = [2,5,6],           n = 3
> → [1,2,2,3,5,6]
> ```

---

## 🎯 Why This Problem Rocks in Interviews

1. **Showcases pointer manipulation** – recruiters love to see how you handle indices.
2. **Space‑time trade‑off** – the follow‑up asks for O(m + n) time, highlighting your understanding of optimal algorithms.
3. **Edge‑case awareness** – zero‑length arrays, negative numbers, identical values, etc.  
4. **Common beginner mistake** – using `Arrays.sort` on the whole array (O((m+n)log(m+n))) instead of a linear merge.

---

## 🔧 The Good, The Bad, The Ugly

| Aspect | What to Aim For | What to Avoid | Why It Matters |
|--------|-----------------|---------------|----------------|
| **Good** | *O(m + n) time, O(1) space* merge from the **end**. | | In‑place, no extra allocation, clear pointer logic. |
| **Bad** | Over‑thinking: using built‑in `sort` or creating a new array. | | Extra O(m + n) space or O((m+n)log(m+n)) time – looks sloppy on an interview screen. |
| **Ugly** | Off‑by‑one bugs: forgetting that `nums1`’s buffer is at the end, mis‑handling empty arrays. | | Easy to get wrong on the first attempt; interviewer will catch. |

---

## 🛠️ Optimal Solution (O(m + n))

### Core Idea

Traverse **from the end** of both arrays:

1. Maintain three indices:  
   - `i = m‑1` (last valid element in `nums1`)  
   - `j = n‑1` (last element in `nums2`)  
   - `k = m + n - 1` (last position in `nums1`)

2. While `j >= 0`:  
   - If `i >= 0` **and** `nums1[i] > nums2[j]`, place `nums1[i]` at `nums1[k]`.  
   - Else place `nums2[j]` at `nums1[k]`.  
   - Move the corresponding pointer left and decrement `k`.

3. If `j` becomes negative, the remaining `nums1` elements are already in place; if `i` becomes negative, the loop terminates early (remaining `nums2` are already copied).

### Why It Works

- We never overwrite a value that hasn't been considered yet because we’re moving from the end toward the front.
- Each element is moved at most once → **linear time**.
- No additional array → **constant auxiliary space**.

---

## 📦 Code Implementations

> **Tip:** Test with the edge cases from the examples, especially `m = 0`, `n = 0`, and negative numbers.

### Java

```java
public class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int i = m - 1;          // last valid index in nums1
        int j = n - 1;          // last index in nums2
        int k = m + n - 1;      // last index in nums1 overall

        while (j >= 0) {        // only need to check nums2
            if (i >= 0 && nums1[i] > nums2[j]) {
                nums1[k--] = nums1[i--];
            } else {
                nums1[k--] = nums2[j--];
            }
        }
    }
}
```

> **Why this is great**  
> - `while (j >= 0)` guarantees we finish copying `nums2`.  
> - The guard `i >= 0` protects us from accessing `nums1[-1]`.  
> - No `Arrays.sort`, no extra array.

---

### Python

```python
class Solution:
    def merge(self, nums1: List[int], m: int,
              nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        i, j, k = m - 1, n - 1, m + n - 1
        while j >= 0:
            if i >= 0 and nums1[i] > nums2[j]:
                nums1[k] = nums1[i]
                i -= 1
            else:
                nums1[k] = nums2[j]
                j -= 1
            k -= 1
```

> **Pythonic touch**  
> - Uses clear variable names `i`, `j`, `k`.  
> - The loop condition `while j >= 0` ensures all `nums2` elements are placed.

---

### C++

```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m,
               vector<int>& nums2, int n) {
        int i = m - 1;           // last valid element in nums1
        int j = n - 1;           // last element in nums2
        int k = m + n - 1;       // last index in nums1 overall

        while (j >= 0) {
            if (i >= 0 && nums1[i] > nums2[j]) {
                nums1[k--] = nums1[i--];
            } else {
                nums1[k--] = nums2[j--];
            }
        }
    }
};
```

> **C++ nuances**  
> - Uses reference to avoid copying `nums1`.  
> - `vector<int>&` keeps the signature LeetCode expects.

---

## 📚 Step‑by‑Step Walkthrough (Java Example)

| Step | Action | Result |
|------|--------|--------|
| 1 | `i = 2`, `j = 2`, `k = 5` | Pointers at ends |
| 2 | `nums1[i] = 3`, `nums2[j] = 6` → place `6` at `nums1[5]` | `nums1 = [1,2,3,0,0,6]` |
| 3 | `j = 1`, `k = 4` | Move left |
| 4 | `nums1[i] = 3`, `nums2[j] = 5` → place `5` | `nums1 = [1,2,3,0,5,6]` |
| 5 | `j = 0`, `k = 3` | ... |
| 6 | `nums1[i] = 3`, `nums2[j] = 2` → place `3` | `nums1 = [1,2,3,3,5,6]` |
| 7 | `i = 1`, `k = 2` | ... |
| 8 | `nums1[i] = 2`, `nums2[j] = 2` → place `2` | `nums1 = [1,2,2,3,5,6]` |
| 9 | `j = -1` → loop ends | Merge finished |

---

## 🎯 Interview‑Ready Checklist

1. **State the constraints** – `nums1.length == m + n`, `nums2.length == n`.  
2. **Explain time/space trade‑off** – “We’ll do O(m+n) time and O(1) space.”  
3. **Handle edge cases** – `m == 0`, `n == 0`, equal elements.  
4. **Show the pointer approach** – emphasize “start from the end” to avoid overwriting.  
5. **Write clean code** – clear variable names, comments where needed.  
6. **Test mentally** – run through a couple of scenarios on the whiteboard.  

> **Pro tip**: If the interviewer asks for the *follow‑up* (O(m + n) time), point out that the algorithm above already achieves it. If they ask for *O(m + n) space*, mention that you could use a temporary array but it defeats the purpose of in‑place merging.

---

## 📈 SEO Snapshot (Meta Tags & Snippets)

```html
<title>Merge Sorted Array – LeetCode 88 (Java, Python, C++) | O(m+n) Solution</title>
<meta name="description" content="Solve LeetCode 88 Merge Sorted Array in Java, Python, and C++ with an optimal O(m+n) in‑place algorithm. Learn the pitfalls, edge cases, and interview tips.">
<meta name="keywords" content="Merge Sorted Array, Leetcode 88, interview problem, O(m+n) algorithm, Java merge array, Python merge sorted array, C++ merge array, coding interview prep">
```

> **Why this helps**  
> These tags surface your article in search results for anyone looking to master this specific LeetCode problem—perfect for recruiters scanning candidate portfolios.

---

## 📣 Final Takeaway

- **Good**: The three‑pointer from‑back approach is clean, linear, and in‑place.  
- **Bad**: Using `sort` or allocating extra memory shows a lack of optimal thinking.  
- **Ugly**: Forgetting that the buffer is at the end leads to `ArrayIndexOutOfBounds`.  

Mastering this problem demonstrates your ability to read constraints, design an efficient algorithm, and write production‑ready code. Bring this knowledge into your next technical interview, and you’ll show recruiters that you’re ready to tackle real‑world data‑structure challenges.

Good luck, and happy coding! 🚀