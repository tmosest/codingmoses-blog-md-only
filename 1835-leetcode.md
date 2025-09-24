---
title: LeetCode 1835. Find XOR Sum of All Pairs Bitwise AND - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 1835 – Find XOR Sum of All Pairs Bitwise AND  
**Java | Python | C++ Solutions + In‑Depth Blog Post**  

> *A full‑stack guide that shows the cleanest math‑based solution, proves why it works, and turns the problem into a job‑winning interview story.*

---

## Table of Contents  
1. [Problem Recap](#problem-recap)  
2. [Naïve Brute‑Force Approach](#naïve-approach)  
3. [The Elegant O(n+m) Solution](#efficient-solution)  
4. [Mathematical Proof](#proof)  
5. [Time & Space Complexity](#complexity)  
6. [Edge Cases & Pitfalls](#edge-cases)  
7. [Code Walkthrough](#code)  
   - Java  
   - Python  
   - C++  
8. [Interview Tips & Tricks](#tips)  
9. [SEO‑Optimized Closing & CTA](#seo)  

---

## <a id="problem-recap"></a>1. Problem Recap

> **LeetCode 1835 – Find XOR Sum of All Pairs Bitwise AND**  
> *Hard*

You’re given two integer arrays `arr1` and `arr2`.  
For every pair `(i, j)` you compute `arr1[i] & arr2[j]`.  
All those results form a list; you must return the XOR sum of that list.

Constraints  
- `1 ≤ arr1.length, arr2.length ≤ 10⁵`  
- `0 ≤ arr1[i], arr2[j] ≤ 10⁹`

---

## <a id="naïve-approach"></a>2. Naïve Brute‑Force Approach

```text
for each a in arr1:
    for each b in arr2:
        res ^= (a & b)
```

* **Time**: `O(n*m)` – 10⁵ * 10⁵ = 10¹⁰ ops → impossible.  
* **Space**: `O(1)` aside from the input.  

While easy to code, this approach will *time‑out* on large inputs.  
It’s a classic “pairwise” pitfall – remember to look for algebraic simplifications.

---

## <a id="efficient-solution"></a>3. The Elegant O(n+m) Solution

The key observation is the distributive law of XOR over AND:

> **(a & b) ⊕ (a & c) = a & (b ⊕ c)**

Applying this property repeatedly we get:

```
XOR over all pairs = (XOR of arr1) & (XOR of arr2)
```

So we just need two single scans:

1. `xor1 = a0 ⊕ a1 ⊕ … ⊕ a_{m-1}`
2. `xor2 = b0 ⊕ b1 ⊕ … ⊕ b_{n-1}`
3. Return `xor1 & xor2`

That’s it – **O(n+m) time, O(1) space**.

---

## <a id="proof"></a>4. Mathematical Proof

Let  

```
S = {arr1[i] & arr2[j] | 0 ≤ i < m, 0 ≤ j < n}
X = ⊕_{x∈S} x   (the XOR sum of all pairs)
```

Fix an element `arr1[i] = a`.  
All terms containing `a` are:

```
a & arr2[0], a & arr2[1], …, a & arr2[n-1]
```

XOR of those terms:

```
a & arr2[0] ⊕ a & arr2[1] ⊕ … ⊕ a & arr2[n-1]
   = a & (arr2[0] ⊕ arr2[1] ⊕ … ⊕ arr2[n-1])   (by distributivity)
   = a & xor2
```

Now XOR over all `i`:

```
X = (arr1[0] & xor2) ⊕ (arr1[1] & xor2) ⊕ … ⊕ (arr1[m-1] & xor2)
   = (arr1[0] ⊕ arr1[1] ⊕ … ⊕ arr1[m-1]) & xor2
   = xor1 & xor2
```

Thus the whole XOR sum collapses to a single AND of two XORs.

---

## <a id="complexity"></a>5. Time & Space Complexity

| Approach | Time Complexity | Space Complexity |
|----------|-----------------|------------------|
| Brute‑Force | **O(n · m)** | O(1) |
| Optimized (XOR + AND) | **O(n + m)** | O(1) |

With `n, m ≤ 10⁵`, the optimized method runs in < 0.01 s in most languages.

---

## <a id="edge-cases"></a>6. Edge Cases & Pitfalls

| Scenario | What to Watch |
|----------|---------------|
| Empty arrays | Problem guarantees length ≥ 1, so no need to handle empty. |
| Numbers up to 10⁹ | 32‑bit int is safe; XOR/AND keep you in 32‑bit range. |
| Large inputs | Keep loops tight; avoid creating intermediate arrays. |
| Over‑reading input | For contests, read with fast I/O. |
| Negative numbers | Not present per constraints; if they were, XOR works but AND may need unsigned handling. |

---

## <a id="code"></a>7. Code Walkthrough

Below are clean, production‑ready implementations in **Java, Python, and C++**.

> **Tip:** The same logic works for any language with 32‑bit integer arithmetic.

### 7.1 Java

```java
// LeetCode 1835 – Find XOR Sum of All Pairs Bitwise AND
class Solution {
    public int getXORSum(int[] arr1, int[] arr2) {
        int xor1 = 0;
        int xor2 = 0;

        for (int a : arr1) xor1 ^= a;
        for (int b : arr2) xor2 ^= b;

        return xor1 & xor2;
    }
}
```

### 7.2 Python

```python
# LeetCode 1835 – Find XOR Sum of All Pairs Bitwise AND
class Solution:
    def getXORSum(self, arr1: List[int], arr2: List[int]) -> int:
        xor1 = 0
        xor2 = 0

        for a in arr1:
            xor1 ^= a
        for b in arr2:
            xor2 ^= b

        return xor1 & xor2
```

> *Python’s integers are unbounded, but the result fits in 32 bits as per constraints.*

### 7.3 C++

```cpp
// LeetCode 1835 – Find XOR Sum of All Pairs Bitwise AND
class Solution {
public:
    int getXORSum(vector<int>& arr1, vector<int>& arr2) {
        int xor1 = 0, xor2 = 0;
        for (int a : arr1) xor1 ^= a;
        for (int b : arr2) xor2 ^= b;
        return xor1 & xor2;
    }
};
```

All three snippets run in **O(n+m)** time and **O(1)** extra space.

---

## <a id="tips"></a>8. Interview Tips & Tricks

| Point | Why It Helps |
|-------|--------------|
| **Show the math first** – ask if the candidate can spot a property before coding. | Demonstrates deep understanding of bitwise operators. |
| **Ask for edge cases** – test negative numbers, zeros, large values. | Reveals robustness. |
| **Time‑complexity discussion** – confirm O(n+m) vs O(n*m). | Shows algorithmic awareness. |
| **Explain distributive law** – `(a&b) ⊕ (a&c) = a & (b ⊕ c)`. | Proves why the solution is correct. |
| **Ask for alternative solutions** – e.g., using bit counting. | Tests creativity. |

> **Interview Highlight**: Mention that this problem is a textbook example of *“look for algebraic simplification”*. Interviewers love candidates who turn a seemingly hard O(n²) task into a clean O(n) trick.

---

## <a id="seo"></a>9. SEO‑Optimized Closing & Call‑to‑Action

If you’re looking to *boost your coding interview game*, mastering LeetCode 1835 showcases your ability to:

- Think *bitwise*  
- Apply *mathematical identities*  
- Write *O(n+m)* code in **Java, Python, and C++**  

Add this problem to your portfolio, share the elegant proof, and tag your posts with `#LeetCode1835`, `#BitwiseOperations`, `#CodingInterview`, and `#AlgorithmDesign`. Recruiters love seeing concise, math‑driven solutions.

> **Ready to nail the next interview?**  
> 👉 Visit [LeetCode](https://leetcode.com) → Search “1835” → Solve → Submit → Share your solution on LinkedIn with a brief write‑up.  
> 👥 Connect with me on LinkedIn for more interview‑ready guides and job‑search support.

Happy coding! 🚀

---