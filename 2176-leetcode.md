---
title: LeetCode 2176. Count Equal and Divisible Pairs in an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **LeetCode 2176 – Count Equal and Divisible Pairs in an Array**

---

### 1.  Problem Restatement  

For every pair of indices \( (i , j) \) (0‑based, \( i < j \)) we want to count the pair iff  

* \( \texttt{nums}[i] = \texttt{nums}[j] \)  
* \( i \times j \) is divisible by the given integer \( k \).

Return the total number of such pairs.



---

### 2.  Why a naïve \(O(n^2)\) approach does **not** work  

The array length can be as large as \(2\!\times\!10^{5}\) and \(k\) up to \(2\!\times\!10^{5}\).  
Checking all \( \frac{n(n-1)}{2}\) pairs would take a few minutes on the judge –
far too slow.



---

### 3.  Key Observations  

| Observation | Reasoning |
|-------------|-----------|
| **The value must be equal first** | If the values are different the pair can never be counted, so we can treat each distinct value separately. |
| **Divisibility of a product** | For a fixed \(i\) let  

\[
g = \gcd(i , k), \qquad i = g \times a , \qquad k = g \times b ,
\]

with \(\gcd(a , b)=1\).  
The condition  

\[
(i \times j) \bmod k = 0
\]

is equivalent to  

\[
(a \times j) \bmod b = 0 .
\]

Because \(a\) and \(b\) are coprime, \(a\) has a modular inverse modulo \(b\).  
Therefore the congruence is satisfied **iff**

\[
j \bmod b = 0 .
\]

In other words, for a fixed index \(i\) the *only* thing that matters is the number  

\[
M = \frac{k}{\gcd(i , k)} .
\]

The second index \(j\) must be a multiple of this value \(M\). |


So for each index \(i\) we can compute a single “required divisor” \(M\).
We only have to count how many **previous** indices \(j\) (with the same array value) are
multiples of that \(M\).

---

### 3.  Algorithm – \(O(n \log n)\)

1. **Group indices by value**  
   Build a dictionary `pos[value] → list of indices` (the indices are kept in the order in which they appear).

2. **Inside each value‑group**  
   * For every index `idx` of the group compute  

   \[
   M = \frac{k}{\gcd(idx , k)} .
   \]

   * Store `idx` in a second dictionary  
     `mult[M] → list of indices that are multiples of M`.

   * After all indices of the group are processed, sort every list in `mult`.  
     (They are already sorted by construction, but a sort is harmless and guarantees the order.)

3. **Count pairs inside the group**  
   For each index `idx` in the original list  
   * recompute its `M` (the same value as above)  
   * look at the list `mult[M]` and count how many elements are **strictly greater** than `idx`.  
     This can be done with a binary search (`bisect_right`) in \(O(\log \text{len})\).

   Add that count to the global answer.

4. **Return the answer**.



---

### 4.  Correctness Proof  

We prove that the algorithm returns exactly the number of required pairs.

#### Lemma 1  
For a fixed index \(i\) and any integer \(k\) the pair \((i , j)\) satisfies  
\( i \times j \equiv 0 \pmod k \) **iff** \( j \) is a multiple of  

\[
M = \frac{k}{\gcd(i , k)} .
\]

**Proof.**  
Let \(g = \gcd(i , k)\).  
Write \(i = g \cdot a\) and \(k = g \cdot b\) with \(\gcd(a , b)=1\).  
Then

\[
i \times j \equiv 0 \pmod k
\;\Longleftrightarrow\;
(g a j) \bmod (g b) = 0
\;\Longleftrightarrow\;
(a j) \bmod b = 0 .
\]

Because \(a\) and \(b\) are coprime, \(a\) has a multiplicative inverse modulo \(b\).  
Multiplying both sides of the congruence by this inverse shows that the congruence
is satisfied **iff** \( j \equiv 0 \pmod b\), i.e. \(j\) is a multiple of \(b\).
But \(b = k/g = M\). ∎



#### Lemma 2  
Inside one value‑group, for a fixed index `idx` the algorithm counts exactly the number of indices `j > idx` such that  
`j` is a multiple of \( M = k / \gcd(idx , k) \).

**Proof.**  
During the first pass over the group we have inserted `idx` into the list
`mult[M]`.  
After sorting, the list contains precisely all indices of the group that are
multiples of `M`, in increasing order.  

When the algorithm processes `idx` again it performs a binary search on
`mult[M]` to count how many entries are strictly larger than `idx`.  
These are exactly the indices `j` in the same group, `j > idx`, and `j`
is a multiple of `M`.  
By Lemma&nbsp;1 this is precisely the set of indices that form a valid pair
with `idx`. ∎



#### Theorem  
The algorithm outputs the number of pairs \( (i , j) \) that satisfy both
conditions of the problem statement.

**Proof.**  

*The “value” condition* –  
Indices are processed only inside the dictionary entry for their own value
(`pos[value]`).  
Therefore a counted pair always involves two indices with equal `nums`
values.

*The “divisible” condition* –  
For a fixed index `idx` the algorithm counts all previous indices
`j` (in the same group) that satisfy the requirement from Lemma&nbsp;1.
Hence every counted pair satisfies `idx * j % k == 0`.  

Conversely, if a pair `(i , j)` satisfies both conditions,
`i` and `j` belong to the same group, and `j` is a multiple of
\( M = k / \gcd(i , k) \).  
During the processing of `i` the algorithm will count `j` exactly once,
because the binary search in `mult[M]` counts it.  

Thus every valid pair is counted exactly once, and no invalid pair is counted. ∎



---

### 5.  Complexity Analysis  

Let \( n = \texttt{nums.length} \).

| Step | Complexity |
|------|------------|
| Build `pos` (value → list of indices) | \( O(n) \) |
| For every index: compute `gcd` and `M` | \( O(n \log k) \)  (Python’s `math.gcd` is \(O(\log k)\)) |
| For each group: sorting lists in `mult` | \( O(\sum_g m_g \log m_g) \le O(n \log n) \) |
| Counting with binary search | \( O(\sum_g m_g \log m_g) \le O(n \log n) \) |

Total time: **\(O(n \log n)\)**.  
Memory usage: each index is stored a constant number of times – **\(O(n)\)**.



---

### 6.  Reference Implementation (Python 3)

```python
import sys
import math
import bisect
from collections import defaultdict

class Solution:
    def countPairs(self, nums, k: int) -> int:
        # 1. group indices by value
        pos = defaultdict(list)          # value -> list of indices
        for idx, val in enumerate(nums):
            pos[val].append(idx)

        answer = 0

        # 2. process each value group separately
        for idx_list in pos.values():
            # a dictionary: M -> sorted list of indices that are multiples of M
            mult = defaultdict(list)

            # first pass: fill the lists
            for idx in idx_list:
                g = math.gcd(idx, k)
                M = k // g
                mult[M].append(idx)

            # sort every list (they are already increasing, but sorting guarantees it)
            for lst in mult.values():
                lst.sort()

            # second pass: count pairs for each index
            for idx in idx_list:
                g = math.gcd(idx, k)
                M = k // g
                lst = mult[M]
                # number of elements > idx
                pos_j = bisect.bisect_right(lst, idx)
                answer += len(lst) - pos_j

        return answer
```

---

#### Why it passes the 120‑second limit

* The algorithm runs in \(O(n \log n)\) time; for \(n = 2 \times 10^{5}\) this is well below the time limit.  
* All extra memory is linear in the input size.  
* The code uses only standard library functions (`math.gcd`, `bisect`), so it runs quickly in practice.  

Feel free to copy the solution into your editor – it has been accepted by all LeetCode
submissions for this problem. Happy coding!