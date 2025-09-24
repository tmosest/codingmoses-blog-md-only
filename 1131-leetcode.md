---
title: LeetCode 1131. Maximum of Absolute Value Expression - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1131 – Maximum of Absolute Value Expression  
*LeetCode | Medium*  
**Java | Python | C++ – One‑Pass, O(n) Solution**

---

## Table of Contents  
1. [Problem Statement](#problem-statement)  
2. [Why the Naïve O(n²) Approach is Deadly](#naïve-approach)  
3. [The O(n) Insight – Four Simple Transformations](#optimal-approach)  
4. [Implementation](#implementation)  
   - Java  
   - Python  
   - C++  
5. [Complexity Analysis](#complexity)  
6. [Edge‑Case Checklist](#edge-cases)  
7. [The Good, the Bad, and the Ugly](#good-bad-ugly)  
8. [Interview‑Ready Tips](#interview-tips)  
9. [Conclusion & Takeaway](#conclusion)

---

<a name="problem-statement"></a>
## 1. Problem Statement

You’re given two integer arrays `arr1` and `arr2` of equal length `n`.  
Return the maximum value of

```
|arr1[i] - arr1[j]| + |arr2[i] - arr2[j]| + |i - j|
```

for all `0 <= i, j < n`.

**Constraints**

| n | 2 ≤ n ≤ 40 000 |
|---|----------------|
| arr1[i], arr2[i] | –10⁶ ≤ value ≤ 10⁶ |

---

<a name="naïve-approach"></a>
## 2. Why the Naïve O(n²) Approach is Deadly

A straight‑forward implementation enumerates every pair `(i, j)`:

```text
maxVal = 0
for i in 0..n-1:
    for j in 0..n-1:
        val = abs(arr1[i]-arr1[j]) + abs(arr2[i]-arr2[j]) + abs(i-j)
        maxVal = max(maxVal, val)
```

**Complexity:** `O(n²)` time, `O(1)` space.  
With `n` up to 40 000, this is ~1.6 billion operations – far beyond a 1‑second runtime limit.  
Moreover, the nested loops hide a subtle mathematical property that allows a **linear** solution.

---

<a name="optimal-approach"></a>
## 3. The O(n) Insight – Four Simple Transformations

The key observation:  

```
|x - y| = max( (x - y), (y - x) )
```

Apply this to each absolute term:

```
|arr1[i] - arr1[j]|  = max( arr1[i] - arr1[j], arr1[j] - arr1[i] )
|arr2[i] - arr2[j]|  = max( arr2[i] - arr2[j], arr2[j] - arr2[i] )
|i - j|              = max( i - j,     j - i )
```

Expanding the sum gives 8 linear combinations of the form  
`(±arr1[i] ± arr2[i] ± i)`.  
Because `|i-j|` is symmetric, the combinations reduce to **four** distinct expressions:

| Expression | Meaning |
|------------|---------|
| `arr1[i] + arr2[i] + i` | `+ + +` |
| `-arr1[i] - arr2[i] + i` | `- - +` |
| `arr1[i] - arr2[i] + i` | `+ - +` |
| `-arr1[i] + arr2[i] + i` | `- + +` |

For any two indices `i` and `j`, the value of the original expression is equal to the difference between one of these expressions evaluated at `i` and the same expression evaluated at `j`.  
Hence the maximum possible value is simply the maximum difference between the **maximum** and **minimum** of each expression over all indices.

**Algorithm**

1. Scan the arrays once, compute the four values for each index.
2. Keep running `max` and `min` for each of the four expressions.
3. The answer is `max( max1 - min1, max2 - min2, max3 - min3, max4 - min4 )`.

This is a classic *one‑pass, O(n)* solution with *O(1)* extra space.

---

<a name="implementation"></a>
## 4. Implementation

Below are clean, ready‑to‑paste implementations in **Java**, **Python**, and **C++**.

> **Tip:** All three codes follow the same logic. They only differ in syntax and minor library usage.

### Java

```java
/**
 * LeetCode 1131 – Maximum of Absolute Value Expression
 * O(n) time, O(1) space
 */
public class Solution {
    public int maxAbsValExpr(int[] arr1, int[] arr2) {
        int n = arr1.length;
        // Initialize extremes
        int max1 = Integer.MIN_VALUE, min1 = Integer.MAX_VALUE;
        int max2 = Integer.MIN_VALUE, min2 = Integer.MAX_VALUE;
        int max3 = Integer.MIN_VALUE, min3 = Integer.MAX_VALUE;
        int max4 = Integer.MIN_VALUE, min4 = Integer.MAX_VALUE;

        for (int i = 0; i < n; i++) {
            int v1 = arr1[i] + arr2[i] + i;
            int v2 = -arr1[i] - arr2[i] + i;
            int v3 = arr1[i] - arr2[i] + i;
            int v4 = -arr1[i] + arr2[i] + i;

            max1 = Math.max(max1, v1);  min1 = Math.min(min1, v1);
            max2 = Math.max(max2, v2);  min2 = Math.min(min2, v2);
            max3 = Math.max(max3, v3);  min3 = Math.min(min3, v3);
            max4 = Math.max(max4, v4);  min4 = Math.min(min4, v4);
        }

        return Math.max(
                Math.max(max1 - min1, max2 - min2),
                Math.max(max3 - min3, max4 - min4));
    }
}
```

### Python

```python
# LeetCode 1131 – Maximum of Absolute Value Expression
# O(n) time, O(1) space

class Solution:
    def maxAbsValExpr(self, arr1: list[int], arr2: list[int]) -> int:
        max1 = min1 = float('-inf')
        max2 = min2 = float('-inf')
        max3 = min3 = float('-inf')
        max4 = min4 = float('-inf')

        for i, (a, b) in enumerate(zip(arr1, arr2)):
            v1 = a + b + i
            v2 = -a - b + i
            v3 = a - b + i
            v4 = -a + b + i

            max1 = max(max1, v1);  min1 = min(min1, v1)
            max2 = max(max2, v2);  min2 = min(min2, v2)
            max3 = max(max3, v3);  min3 = min(min3, v3)
            max4 = max(max4, v4);  min4 = min(min4, v4)

        return max(
            max1 - min1,
            max2 - min2,
            max3 - min3,
            max4 - min4
        )
```

### C++

```cpp
// LeetCode 1131 – Maximum of Absolute Value Expression
// O(n) time, O(1) space

class Solution {
public:
    int maxAbsValExpr(vector<int>& arr1, vector<int>& arr2) {
        int max1 = INT_MIN, min1 = INT_MAX;
        int max2 = INT_MIN, min2 = INT_MAX;
        int max3 = INT_MIN, min3 = INT_MAX;
        int max4 = INT_MIN, min4 = INT_MAX;

        for (int i = 0; i < arr1.size(); ++i) {
            int v1 = arr1[i] + arr2[i] + i;
            int v2 = -arr1[i] - arr2[i] + i;
            int v3 = arr1[i] - arr2[i] + i;
            int v4 = -arr1[i] + arr2[i] + i;

            max1 = max(max1, v1);  min1 = min(min1, v1);
            max2 = max(max2, v2);  min2 = min(min2, v2);
            max3 = max(max3, v3);  min3 = min(min3, v3);
            max4 = max(max4, v4);  min4 = min(min4, v4);
        }

        return max({max1 - min1, max2 - min2, max3 - min3, max4 - min4});
    }
};
```

---

<a name="complexity"></a>
## 5. Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n)` | `O(n)` | `O(n)` |
| **Space** | `O(1)` | `O(1)` | `O(1)` |

The algorithm is linear in the length of the arrays and uses constant auxiliary space, satisfying the problem’s constraints even at the upper bound (`n = 40 000`).

---

<a name="edge-cases"></a>
## 6. Edge‑Case Checklist

| Case | Why it matters | Handling |
|------|----------------|----------|
| `n = 2` | Smallest allowed size; ensure loops run. | Normal loop works. |
| Negative numbers in `arr1/arr2` | Signs in expressions change; still correct. | Expressions use `+` and `-` appropriately. |
| All elements equal | All differences become zero except `|i-j|`. | The formula still captures the `i-j` term. |
| Very large magnitude | Potential overflow in 32‑bit signed int? | With `-10^6` ≤ arr ≤ `10^6` and `n ≤ 40 000`, the largest intermediate value is `≈ 10^6 + 10^6 + 40 000 ≈ 2.04×10^6`, well within 32‑bit. No overflow. |
| Empty arrays | Not allowed by constraints. | No need to guard. |

---

<a name="good-bad-ugly"></a>
## 7. The Good, the Bad, and the Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Mathematical Insight** | Turns a cubic‑looking expression into four linear forms. | Requires careful derivation; easy to miss a sign. | None once derived. |
| **Runtime** | `O(n)` – passes LeetCode limits. | `O(n²)` brute force fails. | None. |
| **Readability** | Compact loops, clear variable names. | Over‑abstraction (e.g., using arrays for 4 values) can hurt clarity. | A single‑line `max({})` in C++ may look terse to beginners. |
| **Maintainability** | Works across languages with minimal changes. | None. | None. |

**Bottom line:** The elegant `max – min` trick is the star of this problem. Once you see it, the solution becomes straightforward and highly efficient.

---

<a name="conclusion"></a>
## 8. Conclusion

We have dissected **LeetCode 1131 – Maximum of Absolute Value Expression** to reveal its hidden linearity, derived a *linear* algorithm, and delivered ready‑to‑deploy solutions in three major languages.  

**Key takeaways for interviewers and candidates:**

- **Look for algebraic simplifications.** Absolute values can often be eliminated by considering all sign permutations.
- **Always analyze the problem’s constraints** – they usually hint at the correct complexity.
- **Test edge cases** rigorously; a wrong sign can silently defeat a correct implementation.

Use these solutions as a template for other “max abs diff” problems, and you’ll have a powerful tool in your algorithmic toolkit.

Happy coding! 🚀

---

**Search Terms to Boost Your SEO:**

- LeetCode 1131 solution
- Maximum absolute value expression algorithm
- O(n) array pair maximum difference
- Java Python C++ LeetCode array problems
- Linear combination optimization
- Interview algorithm insight

These keywords ensure that anyone searching for a concise, high‑performance solution to LeetCode 1131 finds this article.