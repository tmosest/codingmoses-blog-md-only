---
title: LeetCode 668. Kth Smallest Number in Multiplication Table - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 668. Kth Smallest Number in Multiplication Table  
### From “Good, Bad, and Ugly” – A Full‑Stack Interview Deep‑Dive

| Language | Complexity | Notes |
|----------|------------|-------|
| **Java** | `O(m log (m·n))` | Uses `long` for safety, standard binary‑search helper |
| **Python** | `O(m log (m·n))` | Built‑in `min` keeps the loop fast |
| **C++** | `O(m log (m·n))` | Uses `long long`, `std::min` |

> **Target keywords**: *kth smallest number multiplication table*, *LeetCode 668*, *binary search interview*, *Java Python C++ solution*, *coding interview questions*.

---

### 1. Problem Recap

The multiplication table is a matrix `mat` where `mat[i][j] = i * j` (1‑indexed).  
Given integers `m`, `n`, and `k`, return the **kth smallest** element in the `m × n` table.

```
Example
m = 3, n = 3, k = 5  → 3
```

The constraints (`m, n ≤ 3·10⁴`) rule out constructing the full table (`O(mn)` space).  
We need a **time‑efficient** method that works with large inputs.

---

### 2. The “Good” – Classic Binary Search on Value

The key insight:  
*The table’s values are in the range `[1, m·n]`. We can binary‑search that range, counting how many numbers are ≤ mid.*

#### 2.1 Counting ≤ mid

For a given `mid`, each row `i` contains `min(n, mid / i)` elements ≤ `mid` because  
`i * j ≤ mid  ⇔  j ≤ mid / i`.  

Summing over all rows gives:

```
count(mid) = Σ_{i=1}^{m} min(n, mid / i)
```

If `count(mid) ≥ k`, the kth smallest is **≤ mid**; otherwise it’s **> mid**.

#### 2.2 Binary Search Loop

```
low  = 1
high = m * n
while low < high:
    mid = (low + high) // 2
    if count(mid) >= k:
        high = mid
    else:
        low = mid + 1
return low
```

When the loop ends, `low` (or `high`) is the smallest value with at least `k` elements ≤ it – i.e., the kth smallest.

#### 2.3 Correctness Proof (Sketch)

1. **Monotonicity**: `count(x)` is non‑decreasing in `x` because adding more to `x` can only increase the number of rows that satisfy `i * j ≤ x`.  
2. **Binary Search Termination**: We shrink `[low, high]` until `low == high`.  
3. **Invariant**: After each iteration, the answer lies within `[low, high]`.  
4. **Base Case**: When `low == high`, that value satisfies `count(value) ≥ k` and for any `v < value`, `count(v) < k`. Hence it is the kth smallest.

---

### 3. The “Bad” – Trivial Approaches That Fail

| Approach | Why It Fails |
|----------|--------------|
| **Generate entire table & sort** | `O(mn)` memory and time; impossible for `m, n = 30,000` (900M cells). |
| **Priority Queue with duplicates** | Still `O(mn log mn)` operations. |
| **Two‑pointer merge** | Same asymptotic cost; requires `O(mn)` memory to store sorted list. |

These naive methods are great for teaching data structures but **won’t pass LeetCode’s strict limits**.

---

### 4. The “Ugly” – Edge Cases & Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Integer overflow** (in `m * n`) | Use `long` (Java) / `long long` (C++) / `int` (Python) – Python ints are unbounded. |
| **Division by zero** | Not a concern – rows start at 1. |
| **`mid / i` truncation** | Integer division is intentional; ensures we count complete rows. |
| **Binary search bounds** | `high` must be `m * n`, not `m + n`. |
| **Time limit** | Avoid repeated `min` calls by bounding loop: stop when `mid / i == 0`. |

---

### 5. Reference Implementations

> **Each code block is self‑contained, annotated, and ready for copy‑paste.**

#### 5.1 Java

```java
import java.util.*;

public class Solution {
    public int findKthNumber(int m, int n, int k) {
        long low = 1;
        long high = (long) m * n;          // 64‑bit to avoid overflow

        while (low < high) {
            long mid = (low + high) / 2;
            long cnt = countLessOrEqual(mid, m, n);

            if (cnt >= k) {
                high = mid;               // kth <= mid
            } else {
                low = mid + 1;            // kth > mid
            }
        }
        return (int) low;                  // low == high
    }

    private long countLessOrEqual(long mid, int m, int n) {
        long count = 0;
        for (int i = 1; i <= m; i++) {
            count += Math.min(n, (int) (mid / i));
            if (count >= Integer.MAX_VALUE) break; // early stop if too big
        }
        return count;
    }
}
```

#### 5.2 Python

```python
class Solution:
    def findKthNumber(self, m: int, n: int, k: int) -> int:
        low, high = 1, m * n

        while low < high:
            mid = (low + high) // 2
            if self._count_le(mid, m, n) >= k:
                high = mid
            else:
                low = mid + 1
        return low

    @staticmethod
    def _count_le(mid: int, m: int, n: int) -> int:
        return sum(min(n, mid // i) for i in range(1, m + 1))
```

#### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findKthNumber(int m, int n, int k) {
        long long low = 1, high = 1LL * m * n;

        while (low < high) {
            long long mid = (low + high) / 2;
            if (countLE(mid, m, n) >= k)
                high = mid;
            else
                low = mid + 1;
        }
        return static_cast<int>(low);
    }

private:
    long long countLE(long long mid, int m, int n) {
        long long cnt = 0;
        for (int i = 1; i <= m; ++i)
            cnt += min<long long>(n, mid / i);
        return cnt;
    }
};
```

---

### 6. Interview‑Ready Summary

| Question | Key Points |
|----------|------------|
| *Why binary search on the value?* | The table’s values are sorted in a conceptual 1‑D sense. |
| *What is the time complexity?* | `O(m log (m·n))` – the `log` comes from binary search over the value range; the `m` loop is counting. |
| *Can we improve to `O(log m + log n)`?* | No, because each count requires iterating over `m` rows (or `n` columns). |
| *How to handle large `m, n`?* | Use 64‑bit integers for calculations. |
| *Why not use a heap?* | Heap would need `O(mn)` pushes/pops → far too slow. |

**Takeaway**: The binary‑search‑on‑value strategy is elegant, fast, and scales to the limits. It’s the *exact* pattern many hiring managers look for when probing your algorithmic intuition.

---

### 7. SEO‑Optimized Blog Post Structure

```
<title>Kth Smallest Number in Multiplication Table – LeetCode 668 Solution (Java, Python, C++)</title>
<meta name="description" content="Learn the optimal binary search solution for LeetCode 668 – Kth Smallest Number in Multiplication Table. Code snippets in Java, Python, C++. Interview tips.">
```

#### H1: LeetCode 668 – Kth Smallest Number in Multiplication Table

#### H2: Problem Overview & Constraints  
#### H2: Why Naive Approaches Fail – The “Bad”  
#### H2: Optimal Binary Search Solution – The “Good”  
- H3: Counting ≤ mid  
- H3: Binary Search Loop  
- H3: Correctness & Complexity  

#### H2: Common Pitfalls – The “Ugly”  
#### H2: Reference Code in Java, Python, C++  
#### H2: Interview Tips & How to Explain Your Thought Process  
#### H2: Wrap‑Up & Further Reading

Use **bullet lists**, **code fences**, and **bold keywords** to improve readability and SEO. Include internal links (e.g., “Other LeetCode problems similar to 668”) and external links to the LeetCode discussion posts you referenced.

---

### 8. Closing Thought

Mastering *Kth Smallest Number in Multiplication Table* showcases your ability to:

1. Recognize **implicit order** in a 2‑D structure.  
2. Apply **binary search** over a value range, not indices.  
3. Handle large inputs with **careful integer arithmetic**.  

These skills are exactly what interviewers at Google, Amazon, and Stripe ask for. Drop this article on your portfolio or LinkedIn and let the job offers roll in!