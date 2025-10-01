---
title: LeetCode 768. Max Chunks To Make Sorted II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Max Chunks To Make Sorted II (LeetCode 768) – The Good, the Bad, and the Ugly  
> **“Hard” → “Easy”** – If you master this one, you’re ready for the next level of interview questions.  

---

## 1. Problem Recap

You’re given an integer array `arr`.  
You may **split** `arr` into any number of contiguous *chunks*.  
Sort each chunk independently, then concatenate the chunks back together.  
The resulting array **must be sorted** (non‑decreasing).

**Goal:** return the **maximum** number of chunks you can make.

```
Input:  arr = [2,1,3,4,4]
Output: 4
Explanation: [2,1] [3] [4] [4] → sorted → [1,2,3,4,4]
```

*Constraints*  
`1 ≤ arr.length ≤ 2000`  
`0 ≤ arr[i] ≤ 10^8`

---

## 2. Why is This Hard?

The naïve approach is to try every possible split point and check if the concatenated array would be sorted.  
That is **O(n²)** and blows up on the upper limits.

What we really need is a linear‑time, constant‑extra‑space (or `O(n)` auxiliary) solution that
tells us *where* we can cut the array.  
The trick is to look at the *prefix maximum* vs the *suffix minimum*.

---

## 3. The Good – Prefix‑Max / Suffix‑Min (Monotonic‑Stack)  
### 3.1 Intuition

For a split at index `i` (end of the left chunk), the *largest* element on the left must be
less than or equal to the *smallest* element on the right; otherwise, after sorting each side
independently, the right side would still contain something smaller than the left side,
breaking global order.

Hence a **valid cut** occurs whenever

```
maxLeft[i] <= minRight[i+1]
```

where  
`maxLeft[i] = max(arr[0..i])`  
`minRight[i] = min(arr[i..n-1])`

Counting how many indices satisfy this gives the answer.

### 3.2 Complexity

| Complexity | Time | Extra Space |
|------------|------|-------------|
| Worst‑case | **O(n)** | **O(n)** (for the two auxiliary arrays) |

(If you want truly `O(1)` extra space you can compute `maxLeft` on the fly and build `minRight` backwards.)

### 3.3 Code

#### Java

```java
class Solution {
    public int maxChunksToSorted(int[] arr) {
        int n = arr.length;
        int[] maxLeft = new int[n];
        int[] minRight = new int[n];

        maxLeft[0] = arr[0];
        for (int i = 1; i < n; i++) {
            maxLeft[i] = Math.max(maxLeft[i - 1], arr[i]);
        }

        minRight[n - 1] = arr[n - 1];
        for (int i = n - 2; i >= 0; i--) {
            minRight[i] = Math.min(minRight[i + 1], arr[i]);
        }

        int count = 0;
        for (int i = 0; i < n - 1; i++) {
            if (maxLeft[i] <= minRight[i + 1]) count++;
        }
        return count + 1; // the last chunk is always valid
    }
}
```

#### Python

```python
class Solution:
    def maxChunksToSorted(self, arr: List[int]) -> int:
        n = len(arr)
        max_left = [0] * n
        min_right = [0] * n

        max_left[0] = arr[0]
        for i in range(1, n):
            max_left[i] = max(max_left[i-1], arr[i])

        min_right[-1] = arr[-1]
        for i in range(n-2, -1, -1):
            min_right[i] = min(min_right[i+1], arr[i])

        count = 0
        for i in range(n-1):
            if max_left[i] <= min_right[i+1]:
                count += 1
        return count + 1
```

#### C++

```cpp
class Solution {
public:
    int maxChunksToSorted(vector<int>& arr) {
        int n = arr.size();
        vector<int> maxLeft(n), minRight(n);

        maxLeft[0] = arr[0];
        for (int i = 1; i < n; ++i)
            maxLeft[i] = max(maxLeft[i-1], arr[i]);

        minRight[n-1] = arr[n-1];
        for (int i = n-2; i >= 0; --i)
            minRight[i] = min(minRight[i+1], arr[i]);

        int cnt = 0;
        for (int i = 0; i < n-1; ++i)
            if (maxLeft[i] <= minRight[i+1]) ++cnt;
        return cnt + 1;
    }
};
```

---

## 4. The Bad – Hash‑Table Frequency Matching  

An alternative is to walk the array while maintaining two hash maps: one for the sorted copy
and one for the original.  
Whenever the two maps match, you have a valid chunk.  

**Drawbacks**

| Issue | Reason |
|-------|--------|
| **O(n log n)** time | Requires sorting first |
| **High constant factor** | Map operations (hashing, equals) are slower than simple comparisons |
| **Memory overhead** | Two hash maps of size `O(n)` |

For interviewers this solution is still correct but rarely used because the `prefix‑max / suffix‑min` approach is cleaner and faster.

**Python skeleton**

```python
def maxChunksToSorted(arr):
    sorted_arr = sorted(arr)
    freq_sorted, freq_orig = Counter(), Counter()
    count = 0
    for a, s in zip(arr, sorted_arr):
        freq_orig[a] += 1
        freq_sorted[s] += 1
        if freq_orig == freq_sorted:
            count += 1
            freq_orig.clear()
            freq_sorted.clear()
    return count
```

---

## 5. The Ugly – Brute Force O(n²) Split Checking  

You can iterate over every split position, sort the two sides and compare to the global
sorted array.  

| Problem | Time | Space |
|---------|------|-------|
| **O(n²)** time | Too slow for `n = 2000` |
| **O(n)** space | Sorting each side repeatedly |

This is educational for understanding the problem but never advisable in a real interview.

---

## 6. When to Use Which Approach

| Scenario | Preferred Method |
|----------|------------------|
| **Time is critical** | Prefix‑Max / Suffix‑Min (O(n)) |
| **Simplicity matters** | Prefix‑Max / Suffix‑Min (no extra data structures) |
| **You need a “hacky” solution for a small dataset** | Hash‑Table Frequency (quick to code) |
| **You’re just practising** | Brute Force (understand constraints) |

---

## 7. Summary – Take‑Home Points for Interviews

1. **Read the problem carefully** – understand the requirement to *concatenate sorted chunks*.
2. **Think in terms of invariants** – a cut is valid when the left’s max ≤ right’s min.
3. **Use linear passes** – one forward (prefix max) and one backward (suffix min) give you the answer in `O(n)`.
4. **Avoid unnecessary sorting** – sorting first gives an `O(n log n)` solution that’s acceptable but not optimal.
5. **Explain your reasoning** – interviewers love a clear thought process; mention why you chose your solution over the others.

---

## 8. SEO & Career Tips

- **Keywords**: “LeetCode 768 solution”, “Max Chunks To Make Sorted II”, “hard leetcode problems”, “coding interview solutions”, “Java Python C++ interview”.
- **Title**: “Master LeetCode 768 – Max Chunks To Make Sorted II (Java, Python, C++)”.
- **Meta Description**: “Learn the fastest O(n) solution for LeetCode 768, with Java, Python, and C++ code. Understand the good, bad, and ugly approaches and boost your interview performance.”
- **Link**: Provide your GitHub repo with all three implementations for potential employers.
- **Follow‑up**: Ask for comments, share the article on LinkedIn, Reddit r/cscareerquestions, or StackOverflow.

---

## 9. Full Code Repository (GitHub)

> **[GitHub – leetcode-768-max-chunks-to-make-sorted-II](https://github.com/yourusername/leetcode-768-max-chunks-to-make-sorted-II)**  
> Contains the three solutions above, unit tests, and a `README.md` with detailed explanations.

---

### Closing Thought

Mastering **Max Chunks To Make Sorted II** demonstrates that you can convert a seemingly hard problem into a clean, linear‑time algorithm by spotting the right invariant.  
That’s exactly the mindset hiring managers look for. Happy coding!