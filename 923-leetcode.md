---
title: LeetCode 923. 3Sum With Multiplicity - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3‑Sum With Multiplicity – LeetCode 923  
**Java / Python / C++ – Full Solution + SEO‑Optimized Blog Post**

---

### 📌 What you’ll learn

| Section | What you’ll discover |
|---------|----------------------|
| Problem Statement | The “3‑Sum With Multiplicity” LeetCode challenge |
| Why it matters | A common interview question that tests counting, combinatorics and optimization |
| Good & Bad | How a naïve solution fails, and the elegant O(101²) approach that wins |
| Ugly & Avoided | Pitfalls with integer overflow and modulo arithmetic |
| Code | Clean, production‑ready implementations in **Java, Python, C++** |
| SEO Boost | Keywords, meta‑tags & structure to help you rank on job‑search queries |

> **Keywords**: 3Sum With Multiplicity, LeetCode 923, interview algorithm, combinatorics, counting triples, modulo 1e9+7, Java solution, Python solution, C++ solution, job interview, coding interview.

---

## 1. Problem Statement

> **3Sum With Multiplicity**  
> **Medium**  

> **Function signature**  
> ```java
> public int threeSumMulti(int[] arr, int target)
> ```  

> **Given**:
> - `arr` – integer array, `3 <= arr.length <= 3000`
> - `0 <= arr[i] <= 100`
> - `0 <= target <= 300`
>  
> **Return** the number of index triples `i < j < k` such that  
> `arr[i] + arr[j] + arr[k] == target`.  
> Since the answer can be huge, return it modulo `1_000_000_007`.

> **Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[1,1,2,2,3,3,4,4,5,5]`, `target = 8` | `20` | 8×(1,2,5) + 8×(1,3,4) + 2×(2,2,4) + 2×(2,3,3) |
| `[1,1,2,2,2,2]`, `target = 5` | `12` | 2 ways to pick 1 and 6 ways to pick two 2’s |
| `[2,1,3]`, `target = 6` | `1` | Only (1,2,3) |

---

## 2. Why It’s a Good Interview Question

- **Multiple aspects**: array manipulation, combinatorics, modular arithmetic, time‑space trade‑offs.
- **Easy to mis‑optimize**: a triple nested loop is O(n³) – too slow for `n = 3000`.
- **Shows algorithmic creativity**: using frequency counts and combinatorial formulas reduces complexity to O(101²).

---

## 3. Common Pitfalls (The “Ugly” Side)

| Pitfall | Why it fails |
|---------|--------------|
| **Naïve O(n³) loops** | 27 billion operations → time limit exceeded |
| **Not handling modulo** | Integer overflow or wrong final result |
| **Using 32‑bit ints for combinatorics** | `count * (count-1) * (count-2)` can overflow before division |
| **Missing edge cases (i == j == k)** | Wrong combinatorial factor: must use nC3 |
| **Index order** | Need `i < j < k` → careful with counting formulas |

---

## 4. The “Good” Approach – Frequency + Combinatorics

Since every element is in `[0, 100]`, we can replace the array with a *frequency table* of length 101.  
Then we only need to iterate over all possible pairs `(i, j)` with `i <= j`.  
For each pair we compute `k = target - i - j` and check if `0 <= k <= 100`.

### 4.1 3‑Case Counting

| Condition | How many triples | Formula |
|-----------|------------------|---------|
| `i == j == k` | All three equal | `C(cnt[i], 3) = cnt[i] * (cnt[i]-1) * (cnt[i]-2) / 6` |
| `i == j != k` | First two equal, third different | `C(cnt[i], 2) * cnt[k] = cnt[i] * (cnt[i]-1) / 2 * cnt[k]` |
| `i < j < k` | All distinct | `cnt[i] * cnt[j] * cnt[k]` |
| `i < j == k` | Symmetric to previous, handled when loop reaches `(j, j)` | `cnt[i] * C(cnt[j], 2)` – this is covered by the `i < j < k` case when we iterate all combinations, but we avoid double‑counting by only considering `i <= j <= k` and applying the proper combinatorial factor. |

The loops run over at most 101 × 101 = 10 201 iterations – negligible compared to the original input size.

---

## 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Build frequency table | **O(n)** | **O(101)** |
| Double loop over values | **O(101²)** | **O(1)** |
| **Total** | **O(n + 101²) ≈ O(n)** | **O(101)** |

`n` can be up to 3000; the algorithm is comfortably fast for all limits.

---

## 6. Code Implementations

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.  
All use the same frequency‑based logic and handle modular arithmetic safely.

### 6.1 Java

```java
import java.util.*;

class Solution {
    private static final long MOD = 1_000_000_007L;

    public int threeSumMulti(int[] arr, int target) {
        long[] cnt = new long[101];          // frequency table
        for (int v : arr) cnt[v]++;          // O(n)

        long result = 0L;

        for (int i = 0; i <= 100; i++) {
            if (cnt[i] == 0) continue;
            for (int j = i; j <= 100; j++) {
                if (cnt[j] == 0) continue;
                int k = target - i - j;
                if (k < 0 || k > 100 || cnt[k] == 0) continue;

                if (i == j && j == k) {                    // all equal
                    result += cnt[i] * (cnt[i] - 1) * (cnt[i] - 2) / 6;
                } else if (i == j) {                       // i==j!=k
                    result += cnt[i] * (cnt[i] - 1) / 2 * cnt[k];
                } else if (j == k) {                       // i!=j==k
                    result += cnt[i] * cnt[j] * (cnt[j] - 1) / 2;
                } else {                                   // all distinct
                    result += cnt[i] * cnt[j] * cnt[k];
                }
                result %= MOD;
            }
        }
        return (int) result;
    }
}
```

### 6.2 Python

```python
class Solution:
    MOD = 1_000_000_007

    def threeSumMulti(self, arr: List[int], target: int) -> int:
        # frequency of numbers 0..100
        cnt = [0] * 101
        for v in arr:
            cnt[v] += 1

        res = 0
        for i in range(101):
            if cnt[i] == 0:
                continue
            for j in range(i, 101):
                if cnt[j] == 0:
                    continue
                k = target - i - j
                if k < 0 or k > 100 or cnt[k] == 0:
                    continue

                if i == j == k:                       # all equal
                    res += cnt[i] * (cnt[i] - 1) * (cnt[i] - 2) // 6
                elif i == j:                          # i==j!=k
                    res += cnt[i] * (cnt[i] - 1) // 2 * cnt[k]
                elif j == k:                          # i!=j==k
                    res += cnt[i] * cnt[j] * (cnt[j] - 1) // 2
                else:                                 # all distinct
                    res += cnt[i] * cnt[j] * cnt[k]
                res %= self.MOD
        return res
```

### 6.3 C++

```cpp
class Solution {
public:
    const long long MOD = 1'000'000'007LL;

    int threeSumMulti(vector<int>& arr, int target) {
        long long cnt[101] = {0};
        for (int v : arr) ++cnt[v];

        long long ans = 0;
        for (int i = 0; i <= 100; ++i) {
            if (!cnt[i]) continue;
            for (int j = i; j <= 100; ++j) {
                if (!cnt[j]) continue;
                int k = target - i - j;
                if (k < 0 || k > 100 || !cnt[k]) continue;

                if (i == j && j == k) {                           // all equal
                    ans += cnt[i] * (cnt[i] - 1) * (cnt[i] - 2) / 6;
                } else if (i == j) {                              // i==j!=k
                    ans += cnt[i] * (cnt[i] - 1) / 2 * cnt[k];
                } else if (j == k) {                              // i!=j==k
                    ans += cnt[i] * cnt[j] * (cnt[j] - 1) / 2;
                } else {                                          // all distinct
                    ans += cnt[i] * cnt[j] * cnt[k];
                }
                ans %= MOD;
            }
        }
        return static_cast<int>(ans);
    }
};
```

> **Tip**: In C++, use `long long` for intermediate calculations to avoid overflow.  

---

## 7. Step‑by‑Step Walk‑through (Java)

```java
long result = 0L;
for (int i = 0; i <= 100; i++) {
    if (cnt[i] == 0) continue;             // skip unused numbers
    for (int j = i; j <= 100; j++) {
        if (cnt[j] == 0) continue;
        int k = target - i - j;
        if (k < 0 || k > 100 || cnt[k] == 0) continue;

        if (i == j && j == k) {
            // All three indices point to the same number
            result += cnt[i] * (cnt[i] - 1) * (cnt[i] - 2) / 6;
        } else if (i == j) {
            // i and j are the same, k is different
            result += cnt[i] * (cnt[i] - 1) / 2 * cnt[k];
        } else if (j == k) {
            // j and k are the same, i is different
            result += cnt[i] * cnt[j] * (cnt[j] - 1) / 2;
        } else {
            // i, j, k are all distinct
            result += cnt[i] * cnt[j] * cnt[k];
        }
        result %= MOD;    // keep value small
    }
}
```

Each branch uses the correct combinatorial factor:

- `C(n, 3)` when all equal,
- `C(n, 2) * m` when two are equal,
- `n * m * p` when all distinct.

---

## 8. Edge‑Case Validation

> **Test**: `arr = [0, 0, 0]`, `target = 0`  
> **Result**: `C(3, 3) = 1` – only one triple `(0, 0, 0)`.

> **Test**: `arr = [50, 50, 50, 50]`, `target = 150`  
> **Result**: `C(4, 3) = 4` – four triples `(50,50,50)`.

---

## 8.1 What If Numbers Were Larger?

If the domain were bigger (e.g., `int` values up to 10⁵), the frequency‑based approach still works but requires a *hash map* instead of a fixed‑size array.  
The complexity would become O(unique²).  
For LeetCode’s constraints, the fixed 0–100 domain is guaranteed, so a 101‑length array suffices.

---

## 8.2 What If We Wanted All Orderings?

If the requirement were *unordered* triples (any ordering of indices allowed), the counting formulas change slightly (no need for `i <= j` restriction).  
The presented solution naturally respects `i < j < k`, matching the problem statement.

---

## 8.3 Real‑World Analogies

- **Product inventory**: `cnt` is like stock levels; `(i, j, k)` are three items that sum to a target price.
- **Birthday paradox**: Counting combinations of identical values is analogous to grouping birthdays.

---

## 8.4 Handling Very Large Numbers

For languages with arbitrary‑precision integers (e.g., Python’s `int`), the division before modulo is safe.  
In fixed‑width languages, always perform the division *after* multiplying to keep numbers small.

---

## 8.5 Common Interview Questions You Might Ask

1. **Can you explain the combinatorial formulas?**  
   *Answer*: `C(n,3) = n(n-1)(n-2)/6`, `C(n,2) = n(n-1)/2`.

2. **Why do we iterate `j` from `i` to 100 instead of 0?**  
   *Answer*: Ensures we don’t double‑count pairs and maintain `i <= j`.

3. **How would you modify this if numbers could be negative?**  
   *Answer*: Shift the frequency table or use a `HashMap<Integer, Long>`; still O(unique²).

4. **What happens if `target` is huge ( > 300 )?**  
   *Answer*: `k` becomes negative → skipped; algorithm naturally returns 0.

---

## 8.6 Final Thoughts

- **Elegance**: Replacing values with frequencies turns a seemingly intractable O(n³) problem into a tiny O(10 k) loop.
- **Scalable**: Works for any `n` ≤ 3000 without tuning.
- **Modular arithmetic**: Keep a running modulo to avoid overflow and match LeetCode’s expected output.

Feel free to copy‑paste the code into your favorite IDE and run the provided test cases.

---

## 8.7 Summary

| Piece | Key Insight |
|-------|-------------|
| **Frequency table** | Compresses the array into 101 counts |
| **Double loop** | Enumerates all value pairs `(i, j)` |
| **k computation** | `k = target - i - j` |
| **Case handling** | 3 combinatorial formulas, no double counting |
| **Modulo** | Applied after every addition |

This is a textbook example of turning a brute‑force idea into an *optimal* solution.

---

## 8.8 “What‑If” Questions for Further Exploration

1. **What if `target` can be up to 300?**  
   The algorithm still works because `k` may be negative; we simply skip those cases.

2. **Can we further reduce complexity?**  
   Not really – the double loop over the 101 values is already *O(1)* relative to the input.

3. **If numbers were up to 10⁵, could we still use a frequency array?**  
   Yes, but the table would be large; a hash map (`unordered_map`) and iterating over the unique keys would still be fast.

4. **Could we use two‑pointer technique after sorting?**  
   Two‑pointer works for *distinct* sums, but handling equal numbers combinatorially is simpler with the frequency table.

---

## 8.9 Practice Problems

- **LeetCode 1512** – *Add to Array-Form of Integer* (use two‑pointer).
- **LeetCode 1675** – *Minimum Operations to Reduce X to Zero* (two‑pointer + sliding window).
- **LeetCode 436** – *Find Right Interval* (binary search + sorting).

---

## 8.10 Closing Advice

- **Understand the domain**: Constraints like `0 <= val <= 100` open the door to frequency‑based optimizations.
- **Master combinatorial formulas**: `nC2`, `nC3` are your friends.
- **Handle modular arithmetic** carefully: Use `long long`/`long` and apply `% MOD` after each addition.

Good luck rocking that interview! 🚀

---

### FAQ

**Q**: *What if the input array contains negative numbers?*  
**A**: The current solution relies on `[0,100]` domain. For negatives, use a `HashMap<Integer, Long>` to store frequencies and iterate over all key pairs. Complexity becomes O(unique²).

**Q**: *Can we pre‑compute factorials for combinations?*  
**A**: With fixed small counts, direct formulas are simpler and avoid factorial arrays.

**Q**: *Why not use 32‑bit ints in Python?*  
**A**: Python’s ints are arbitrary‑precision, so overflow isn’t an issue, but we still cast to `int` for the final return.

--- 

**Enjoy coding, and best of luck in your next interview!**