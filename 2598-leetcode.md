---
title: LeetCode 2598. Smallest Missing Non-negative Integer After Operations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## LeetCode 2598 – *Smallest Missing Non‑negative Integer After Operations*  
### Java / Python / C++ implementation + SEO‑friendly blog post

---

## 1. Problem recap

```
You are given an array nums and an integer value.
In one operation you can add or subtract value from any element of nums.

Goal: After any number of operations, maximise the MEX of the array.
The MEX of an array is the smallest non‑negative integer that is **missing** from it.
```

> **Example**  
> `nums = [1,‑10,7,13,6,8]`, `value = 5`  
> Maximal MEX that can be achieved is **4**.

The constraints (`nums.length, value ≤ 10^5`, `|nums[i]| ≤ 10^9`) mean a linear‑time solution is required.



---

## 2. Key insight

For any number `x` in the array we can change it to  
`x + k * value`  **or**  `x – k * value`  for any integer `k`.  
Thus **only the remainder of each element modulo `value` matters** – all other
numbers are equivalent.

* `x % value = r`  (non‑negative remainder)  
  `x` can be turned into **any** integer whose remainder modulo `value` is `r`.

So we only need to know **how many elements we have for each remainder**.

If we want to build the integer `i` from the array, we need an element whose
remainder is `i % value`.  
If we run out of that remainder, `i` cannot be formed → it is the answer.



---

## 3. Algorithm (O(n + answer) time, O(value) space)

```
1. Count how many numbers have each remainder r ∈ [0, value‑1].
2. For i = 0,1,2,…:
        r = i % value
        if count[r] == 0        → i is missing → return i
        else count[r]--          → use one element of that remainder
```

Because each iteration removes one element from the count, the loop will finish
after at most `n` steps, where `n = nums.length`.  
The answer itself can be as large as `n`, so the total complexity is linear.



---

## 4. Correctness proof

### Lemma 1  
After any sequence of allowed operations, the multiset of remainders of the
array elements remains unchanged.

**Proof.**  
Adding or subtracting `value` does not change the remainder modulo `value`
(`(x ± value) % value = x % value`). ∎



### Lemma 2  
If for some `i` we have at least one element with remainder `i % value`, we can
construct the integer `i` from the array.

**Proof.**  
Pick any element `x` with remainder `r = i % value`.  
Set `k = (i – x) / value` (an integer because `x % value = r = i % value`).  
Add `k * value` to `x`.  
Resulting number is exactly `i`. ∎



### Lemma 3  
If for some `i` the count of remainder `i % value` is zero, then `i` cannot be
formed by any sequence of operations.

**Proof.**  
By Lemma 1 the multiset of remainders never changes, so we have no element that
can be turned into a number congruent to `i (mod value)`.  
By Lemma 2 this means `i` is impossible. ∎



### Theorem  
The algorithm returns the maximum possible MEX after any number of operations.

**Proof.**  
The algorithm checks integers `i = 0,1,2,…` in increasing order.

*For each i*:
- If a remainder exists (`count > 0`), the algorithm decreases it and continues.
  By Lemma 2 we can indeed create `i`, so `i` belongs to some achievable array,
  and the MEX is larger than `i`.
- If the remainder is missing, the algorithm returns `i`.  
  By Lemma 3 `i` cannot be formed; therefore `i` is the smallest missing
  non‑negative integer, i.e. the MEX of the best attainable array.

Thus the returned value is exactly the maximal MEX. ∎



---

## 5. Complexity analysis

| Operation | Time | Space |
|-----------|------|-------|
| Counting remainders | `O(n)` | `O(value)` |
| Scanning for answer | `O(answer)` ≤ `O(n)` | — |
| **Total** | **`O(n)`** | **`O(value)`** |

Both bounds satisfy the problem constraints.



---

## 6. Edge cases & pitfalls

| Situation | What to watch for |
|-----------|-------------------|
| `value` can be 1 | Every number becomes remainder 0; the answer is `n` (all numbers used). |
| Negative numbers in `nums` | Java/Python modulo can be negative → add `value` before taking `% value`. |
| Very large answer | The loop may iterate up to `n` times; ensure no `for(int i=0; i<value; i++)` that stops too early. |
| `value` larger than `n` | The count array may contain many unused remainders, still fine. |

---

## 7. “Good, Bad, Ugly” breakdown

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Concept** | Remainder counting is intuitive once you think of “value” as a modulus. | Requires understanding that only remainders matter, not actual values. | Forgetting negative modulo handling leads to wrong counts. |
| **Implementation** | One‑pass counting + simple loop. | Need to normalise remainder for negative numbers. | For very large `value`, allocating a huge array could waste memory (but `value ≤ 10^5`). |
| **Performance** | Linear time, works easily up to 10^5. | If you use a HashMap instead of array, you still get linear but with higher constant. | If you mistakenly pre‑compute all numbers up to some bound, you’ll hit `O(n^2)` and time‑outs. |
| **Testing** | Easy to write unit tests by checking known small examples. | Need to test negative cases, `value=1`, `value>n`. | Edge case where answer is exactly `n` (no remainder left) may trip off‑by‑one bugs. |

---

## 8. Alternative approaches (why we didn’t choose them)

1. **Dynamic programming over the whole number range**  
   – Requires a huge DP table (≈ answer²), far too slow.
2. **Sorting the array and greedy subtraction**  
   – Sorting is `O(n log n)`; unnecessary extra work and higher constants.
3. **Using a multiset of values**  
   – Re‑computing each possible `i` would be `O(n·answer)`; infeasible.

The chosen “count‑and‑scan” method is the canonical solution for this class of
modulus problems.



---

## 8. Interview‑ready take‑away

* **LeetCode 2598** is a classic “smallest missing number” problem that turns
  into a “smallest missing remainder” problem once you see the operations as
  modulo operations.
* The core idea is the same as in problems like “Count the number of ways to
  make an integer with given steps” or “Smallest number that cannot be formed
  from a multiset”.
* Be prepared to explain the negative‑modulo issue—interviewers love to see that
  you know the quirks of the language you’re using.
* Discuss time/space trade‑offs: array vs. `HashMap`.  Show the array gives
  better constant‑time access.

**Keywords to sprinkle in your resume or portfolio:**  
`LeetCode 2598`, `Smallest Missing Non‑negative Integer After Operations`, `MEX`, `modulo counting`, `Java algorithm`, `Python solution`, `C++ solution`, `algorithm interview`, `data‑structure interview`, `job interview problem`, `software engineer interview`.

---

## 8. Final code

Below are clean, production‑ready solutions in **Java**, **Python** and **C++**.

### Java

```java
import java.util.*;

class Solution {
    public int findSmallestInteger(int[] nums, int value) {
        int n = nums.length;
        int[] count = new int[value];

        // 1. Count remainders (handle negative numbers)
        for (int num : nums) {
            int r = num % value;
            if (r < 0) r += value;          // Java/Python negative modulo hack
            count[r]++;
        }

        // 2. Scan for the missing integer
        for (int i = 0; ; i++) {
            int r = i % value;
            if (count[r] == 0) {
                return i;                    // smallest missing -> maximal MEX
            }
            count[r]--;                      // use one element of this remainder
        }
    }
}
```

### Python (3.8+)

```python
from collections import Counter
from typing import List

class Solution:
    def findSmallestInteger(self, nums: List[int], value: int) -> int:
        # Normalise remainder for negative numbers
        cnt = Counter(((x % value) + value) % value for x in nums)

        i = 0
        while True:
            r = i % value
            if cnt[r] == 0:
                return i
            cnt[r] -= 1
            i += 1
```

### C++

```cpp
#include <vector>
#include <unordered_map>
#include <numeric>

class Solution {
public:
    int findSmallestInteger(std::vector<int>& nums, int value) {
        std::vector<int> cnt(value, 0);

        // Count remainders, normalise negative numbers
        for (int x : nums) {
            int r = ((x % value) + value) % value;
            cnt[r]++;
        }

        // Scan for answer
        for (int i = 0; ; ++i) {
            int r = i % value;
            if (cnt[r] == 0) return i;
            cnt[r]--;
        }
    }
};
```

All three snippets follow the same linear‑time logic and handle negative values
correctly.



---

## 9. Take‑away for recruiters & interviewers

* LeetCode 2598 is a **canonical modulus‑based counting problem**.  
  Understanding that “value” is just a modulus is the key to the solution.
* The solution is **robust, fast, and easy to explain** – perfect for coding‑rounds.
* Highlight your knowledge of:
  * Modulo arithmetic (including negative numbers)
  * Linear‑time counting techniques
  * Reasoning about the MEX
* Bring the “good, bad, ugly” discussion: it shows you can think critically about
  design decisions and pitfalls, a highly valuable skill in real‑world software
  engineering.



---

### TL;DR

* **Problem** – Maximise the MEX after repeatedly adding/subtracting `value`.  
* **Insight** – Only remainders modulo `value` matter.  
* **Solution** – Count remainders → iterate `i = 0,1,…`, consume a matching
  remainder until one is missing.  
* **Complexity** – `O(n)` time, `O(value)` space.  
* **Code** – Provided in Java, Python and C++.  

Happy coding and good luck cracking that interview!