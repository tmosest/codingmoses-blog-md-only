---
title: LeetCode 3644. Maximum K to Sort a Permutation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 3644 – “Maximum K to Sort a Permutation”

**Problem link**: <https://leetcode.com/problems/maximum-k-to-sort-a-permutation/>

> You are given a permutation `nums` of the integers `[0 … n‑1]`.  
> You may swap elements at indices `i` and `j` *only if*  
> `nums[i] & nums[j] == k` (bitwise AND).  
> All swaps must use the *same* `k`.  
> Return the **maximum** `k` that allows you to sort the array in non‑decreasing order.  
> If the array is already sorted return `0`.

The constraints are large (`n ≤ 10⁵`), so an `O(n)` solution is required.



--------------------------------------------------------------------

## 2.  Intuitive Insight

For a number that is already in its correct position we do **not** need to touch it.  
The only numbers that matter are those that are *misplaced*.  

For any two misplaced numbers `a` and `b` we can use them directly as a swap only if  
`a & b == k`.  
However, we are free to introduce *any* correctly‑placed number as an intermediate “bridge” because a correctly‑placed number does not disturb the sorted order – it can be moved back later.

> **Key Observation**  
> The bitwise AND of **all** misplaced numbers is always a valid choice for `k`.  
> This `k` can be used to connect every misplaced element to the correctly‑placed ones, ultimately enabling us to bring each number to its rightful index.

Why does this work?

* Every misplaced number `x` has the property `x & k == k` because `k` is the AND of *all* such numbers.  
  Consequently, we can always find a pair `(x, y)` such that `x & y == k`.  
* Starting from any misplaced position we can swap it with a correctly placed element that shares the same `k`.  
  Repeating this step moves all misplaced elements into place while preserving the sorted order of the rest.

Thus the maximum admissible `k` is simply the cumulative bitwise AND of all numbers that are out of place.



--------------------------------------------------------------------

## 3.  Algorithm

```
mask = ALL_BITS_SET   // e.g. 0x7FFFFFFF for 32‑bit ints
for i from 0 to n-1
        if nums[i] != i          // number is misplaced
                mask &= nums[i]   // keep only the bits common to all misplaced numbers

if mask was never changed            // array was already sorted
        return 0
else
        return mask
```

*`ALL_BITS_SET` can be `~0` or `(1 << 31) - 1` for signed 32‑bit ints.*  
The algorithm runs in linear time and uses constant extra space.



--------------------------------------------------------------------

## 4.  Correctness Proof  

We prove that the algorithm returns the maximum possible `k`.

---

### Lemma 1  
Let `S` be the set of all indices `i` such that `nums[i] != i`.  
Let `k* = ∧_{i∈S} nums[i]` (bitwise AND of all misplaced numbers).  
Then for every `i ∈ S`, `nums[i] & k* = k*`.

**Proof.**  
`k*` is the AND of *all* elements in `S`.  
The AND operation can only turn a bit off; it can never turn a bit on that is off in one operand.  
Therefore, every bit that is on in `k*` is on in each `nums[i]`.  
Hence `nums[i] & k* = k*`. ∎



### Lemma 2  
Using `k*` as the common swap value, the permutation can be sorted.

**Proof.**  
Take any misplaced index `i`.  
By Lemma&nbsp;1, `nums[i] & k* = k*`.  
Let `j = nums[i]` (the correct position of the value currently at `i`).  
Since `nums[j] = j` (because the array is a permutation of `0…n‑1`), `nums[j]` is correctly placed.  
Now, `nums[i] & nums[j] = k*` because `nums[j] = j` and `j & k* = k*` (the bitwise AND of any number with `k*` is `k*` if the number has all bits of `k*`).  
Thus swapping positions `i` and `j` is allowed.  
After the swap, the value at index `i` is correct, and the value at index `j` becomes misplaced (but still shares `k*`).  
Repeating this procedure eventually places every number in its correct index.  
Because each swap involves at least one element that was already correctly placed, the rest of the array remains sorted throughout the process. ∎



### Lemma 3  
No `k` larger than `k*` can sort the permutation.

**Proof.**  
Assume a larger `k' > k*` works.  
There must exist at least one bit `b` that is `1` in `k'` but `0` in `k*`.  
Since `k*` is the AND of all misplaced numbers, there exists a misplaced number `x` that does **not** have bit `b` set (`x_b = 0`).  
Now, for any pair of indices `(i, j)` involving `x`, `nums[i] & nums[j]` cannot have bit `b` set because one operand lacks it.  
Thus no swap involving `x` satisfies `nums[i] & nums[j] == k'`.  
Without being able to move `x`, the array cannot be fully sorted – contradiction. ∎



### Theorem  
The algorithm returns the maximum possible `k`.

**Proof.**  
If the array is already sorted, the algorithm returns `0`.  
Otherwise, it returns `k*`.  
By Lemma&nbsp;2, `k*` can sort the permutation.  
By Lemma&nbsp;3, no larger `k` can sort it.  
Therefore `k*` is the maximum admissible `k`. ∎



--------------------------------------------------------------------

## 5.  Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| One linear scan | **O(n)** | **O(1)** |

The algorithm meets the required constraints (`n ≤ 10⁵`).

--------------------------------------------------------------------

## 6.  Code

Below are clean, idiomatic implementations in **Java**, **Python**, and **C++**.  
Each one follows the same algorithmic steps.

### 6.1 Java

```java
import java.util.*;

public class Solution {
    /**
     * Returns the maximum k that allows sorting the permutation by swapping
     * only pairs whose bitwise AND equals k.
     *
     * @param nums the permutation of [0..n-1]
     * @return maximum admissible k (0 if already sorted)
     */
    public int sortPermutation(int[] nums) {
        int mask = Integer.MAX_VALUE;          // all 31 bits set
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != i) {
                mask &= nums[i];
            }
        }
        return (mask == Integer.MAX_VALUE) ? 0 : mask;
    }

    // Simple test harness
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.sortPermutation(new int[]{0, 3, 2, 1})); // 1
        System.out.println(sol.sortPermutation(new int[]{0, 1, 3, 2})); // 2
        System.out.println(sol.sortPermutation(new int[]{3, 2, 1, 0})); // 0
    }
}
```

### 6.2 Python

```python
def sortPermutation(nums):
    """
    Return the maximum k that permits sorting the permutation by swaps
    whose bitwise AND equals k. 0 if the array is already sorted.
    """
    mask = (1 << 31) - 1          # 31‑bit all‑ones
    for i, val in enumerate(nums):
        if val != i:
            mask &= val
    return 0 if mask == ((1 << 31) - 1) else mask


# Demo
if __name__ == "__main__":
    print(sortPermutation([0, 3, 2, 1]))   # 1
    print(sortPermutation([0, 1, 3, 2]))   # 2
    print(sortPermutation([3, 2, 1, 0]))   # 0
```

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

int sortPermutation(vector<int>& nums) {
    int mask = numeric_limits<int>::max();   // all bits set
    for (int i = 0; i < (int)nums.size(); ++i) {
        if (nums[i] != i)
            mask &= nums[i];
    }
    return (mask == numeric_limits<int>::max()) ? 0 : mask;
}

// Simple test
int main() {
    vector<int> a = {0,3,2,1};
    cout << sortPermutation(a) << endl;   // 1

    vector<int> b = {0,1,3,2};
    cout << sortPermutation(b) << endl;   // 2

    vector<int> c = {3,2,1,0};
    cout << sortPermutation(c) << endl;   // 0
}
```

--------------------------------------------------------------------

## 7.  Blog Article – “The Good, The Bad, and The Ugly of Maximum K to Sort a Permutation”

> **SEO Keywords**: LeetCode 3644, Maximum K to Sort a Permutation, bitwise AND sorting, permutation sorting algorithm, interview prep, algorithm analysis, C++ solution, Java solution, Python solution, data structures, bit manipulation.

---

### 7.1 Introduction

Sorting a permutation with a single, fixed bitwise constraint is an unusual twist on classic sorting problems.  
LeetCode’s **Maximum K to Sort a Permutation** (ID 3644) asks: *“What is the largest integer k such that you can sort the array by only swapping pairs whose bitwise AND equals k?”*  

For many, this feels like a trick question.  It’s easy to get lost in the combinatorics of swap operations, yet the answer is remarkably simple once you recognize the role of misplaced numbers and bitwise AND.

---

### 7.2 The “Good” – A Beautifully Simple Solution

**Why it’s good**

| Aspect | How it shines |
|--------|----------------|
| **Linear Time** | A single pass of O(n) suffices for n = 100 000. |
| **O(1) Space** | No auxiliary arrays or recursion. |
| **Language‑agnostic** | Works identically in Java, Python, or C++. |
| **Maximum K** | Guarantees you’re not just finding *a* k, but the maximum one. |

At first glance, you might think you need a complex graph‑theory approach or a bit‑mask DP.  
Instead, the core insight is that **the AND of all misplaced numbers is the answer**.  
Once you have that, the algorithm is a one‑liner in most languages.

### 7.3 The “Bad” – What Misleads New Solvers

1. **Assuming You Need to Compare All Pairs**  
   A naive solution might try to find the largest k by checking every possible swap pair, an O(n²) nightmare.  
   The problem’s constraints (n = 10⁵) make this infeasible.

2. **Misunderstanding “Fixed k”**  
   Some readers think the “fixed” constraint means we cannot ever introduce a correctly‑placed number as an intermediate.  
   The catch is that any correctly‑placed element can be temporarily moved without ruining the final sorted order.

3. **Thinking the AND Must be a Subset of All Numbers**  
   It’s tempting to take the AND of the entire array, but that would trivially yield 0 for a sorted array.  
   The key is *misplaced* numbers only.

### 7.4 The “Ugly” – Pitfalls in Implementation

| Pitfall | Explanation | Fix |
|---------|-------------|-----|
| Using `~0` as the initial mask in signed languages | Some languages treat `~0` as `-1`, which contains a leading 1 in the sign bit. | Use `numeric_limits<int>::max()` (31‑bit) or `(1 << 31) - 1`. |
| Forgetting that Java’s `int` is signed | ANDing with a negative mask can propagate the sign bit incorrectly. | Keep the mask 31‑bit and compare with `Integer.MAX_VALUE`. |
| Over‑reading “maximum k” | Returning the mask even if the array is sorted leads to `k = 2^31-1`. | Detect “never changed” and return 0. |

Once you avoid these, the solution is as clean as a single line.

### 7.5 Interview Preparation Tips

1. **Visualize the Swap Graph**  
   Picture nodes as indices, edges as allowed swaps.  
   With a fixed k, each node must connect to a correctly‑placed node.  
   The AND of all misplaced numbers guarantees such connectivity.

2. **Practice Bit Manipulation**  
   LeetCode often tests AND/OR/SHIFT tricks.  
   Remember:  
   * `x & y == k` → every bit set in k must be set in both x and y.  
   * The AND of a set of numbers is the set of bits present in *every* number.

3. **Write the O(1) Space Proof**  
   Interviewers love seeing you articulate why you’re not allocating an extra array or recursion stack.

### 7.6 Variations & Extensions

| Variation | Difficulty | Key Idea |
|-----------|------------|----------|
| **k can change after each swap** | Medium | Use BFS over state space or DSU to track connectivity. |
| **Multiple arrays, each with its own k** | Hard | Need to compute per‑array AND and then intersect. |
| **Large integers beyond 32‑bit** | Hard | Use 64‑bit mask and careful handling of signedness. |

Exploring these keeps your interview mindset fresh and showcases deep understanding of bitwise operations.

### 7.7 Conclusion

The Good: A surprisingly elegant solution that runs in linear time.  
The Bad: The deceptive nature of the constraint that can mislead even seasoned coders.  
The Ugly: The subtle bugs that arise from signed integer quirks.

By mastering this problem, you not only ace LeetCode 3644 but also demonstrate mastery of **bit manipulation**, **algorithmic thinking**, and **problem‑solving under constraints**—all invaluable skills for technical interviews.

---

**Happy coding!**  
Feel free to adapt the code snippets and article for your personal portfolio or blog.  

---


--------------------------------------------------------------------

## 8.  Final Remarks

* The solution is **provably optimal** and runs in time.
* The observation that *the cumulative AND of misplaced numbers is the answer* is the cornerstone—once recognized, the rest is a straightforward linear pass.
* Whether you’re implementing in **Java**, **Python**, or **C++**, the same concise logic applies.

Good luck tackling LeetCode 3644 in your next interview!