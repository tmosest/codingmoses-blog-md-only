---
title: LeetCode 2595. Number of Even and Odd Bits - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Master LeetCode 2595 – “Number of Even and Odd Bits”  
*Learn how to solve it in **Java, Python, and C++** and ace your next software‑engineering interview.*

---

### Table of Contents  

| Section | Link |
|---------|------|
| 📝 Problem Statement | #problem-statement |
| ⚙️ Solution Overview | #solution-overview |
| 💡 Good, Bad, Ugly | #good-bad-ugly |
| 🔢 Bit‑Mask Approach | #bit‑mask-approach |
| 📦 Code Snippets | #code-snippets |
| ⏱️ Complexity | #complexity |
| ⚠️ Edge Cases & Pitfalls | #pitfalls |
| 🗣️ Interview Tips | #interview-tips |
| 📚 Alternatives | #alternatives |
| 🎯 Summary | #summary |

> **SEO keywords**: *LeetCode 2595, Number of Even and Odd Bits, bit manipulation, Java solution, Python solution, C++ solution, interview coding, job interview, software engineer, bitmasking, popcount, coding interview tips*

---

## 📝 Problem Statement

> **Given** a positive integer `n`  
> **Return** an array `[even, odd]` where:
> * `even` = number of **even‑indexed** bits (0‑based from the right) that are set to `1`
> * `odd`  = number of **odd‑indexed** bits that are set to `1`

**Indices** are counted from the least significant bit (LSB) to the most significant bit (MSB), starting at `0`.

**Examples**

| `n` | Binary | Even‑index 1s | Odd‑index 1s | Output |
|-----|--------|--------------|--------------|--------|
| 50  | 110010 | 1 (index 4)  | 2 (indices 1,5) | `[1, 2]` |
| 2   | 10     | 0            | 1 (index 1) | `[0, 1]` |

**Constraints**

```
1 <= n <= 1000
```

---

## ⚙️ Solution Overview

| Approach | Idea | Complexity | When to use |
|----------|------|------------|-------------|
| **Bit‑Mask & Popcount** | Use two alternating masks (`0b010101…` & `0b101010…`) to isolate even and odd bits, then count the set bits with a built‑in popcount. | **O(1)** time, **O(1)** space | Production‑grade, interview‑friendly |
| **String Conversion** | Convert `n` to binary string, iterate from LSB to MSB and increment counters based on index parity. | **O(log n)** time, **O(log n)** space | Quick prototyping |
| **Manual Bit Loop** | Shift `n` right each iteration, check LSB, toggle index parity. | **O(log n)** time, **O(1)** space | Educational, if built‑ins aren’t allowed |

The **Bit‑Mask & Popcount** method is the “good” one—short, efficient, and demonstrates mastery of bit manipulation.

---

## 💡 Good, Bad, Ugly

| Stage | What to do | What to avoid |
|-------|------------|---------------|
| **Good** | Use bitwise masks + built‑in popcount (e.g., `Integer.bitCount`). | Anything that loops over the entire binary string unless you have to. |
| **Bad** | Convert to string when debugging, but not in production. | Forgetting that indices start at `0`. |
| **Ugly** | Manually maintain counters and index parity in an `if/else` block that gets tangled. | Writing a long hard‑coded mask like `0b0101010101010101` without explanation. |

---

## 🔢 Bit‑Mask Approach

1. **Masks**  
   * Even indices (0, 2, 4, …) → binary `010101…`  
   * Odd indices (1, 3, 5, …)  → binary `101010…`  

   In decimal:  
   * Even mask = `0b0101010101` = `341` (for numbers up to 10 bits; you can generate it programmatically if you like).  
   * Odd mask  = `0b1010101010` = `682`.

2. **Apply & Count**  
   * `even = popcount(n & evenMask)`  
   * `odd  = popcount(n & oddMask)`

3. **Return** `[even, odd]`

---

## 📦 Code Snippets

### Java

```java
class Solution {
    public int[] evenOddBit(int n) {
        // Even indices: 0,2,4,... -> 0b0101010101 (341)
        // Odd indices: 1,3,5,...  -> 0b1010101010 (682)
        int evenMask = 0b0101010101; // 341
        int oddMask  = 0b1010101010; // 682
        int even = Integer.bitCount(n & evenMask);
        int odd  = Integer.bitCount(n & oddMask);
        return new int[]{even, odd};
    }
}
```

#### One‑liner (Java 17+)

```java
public int[] evenOddBit(int n) {
    return new int[]{Integer.bitCount(n & 0b0101010101), Integer.bitCount(n & 0b1010101010)};
}
```

---

### Python

```python
class Solution:
    def evenOddBit(self, n: int) -> List[int]:
        even_mask = 0b0101010101  # 341
        odd_mask  = 0b1010101010  # 682
        even = (n & even_mask).bit_count()
        odd  = (n & odd_mask).bit_count()
        return [even, odd]
```

#### One‑liner (Python 3.10+)

```python
class Solution:
    def evenOddBit(self, n: int) -> List[int]:
        return [(n & 0b10101010101).bit_count(), (n & 0b01010101010).bit_count()]
```

---

### C++

```cpp
class Solution {
public:
    vector<int> evenOddBit(int n) {
        // Even indices mask: 0b0101010101 (341)
        // Odd indices  mask: 0b1010101010 (682)
        int evenMask = 0b0101010101;
        int oddMask  = 0b1010101010;
        int even = __builtin_popcount(n & evenMask);
        int odd  = __builtin_popcount(n & oddMask);
        return {even, odd};
    }
};
```

#### One‑liner (C++14+)

```cpp
vector<int> evenOddBit(int n) {
    return {__builtin_popcount(n & 0b0101010101), __builtin_popcount(n & 0b1010101010)};
}
```

---

## ⏱️ Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Bit‑Mask + Popcount | **O(1)** (constant, independent of `n`) | **O(1)** |
| String conversion | **O(log n)** | **O(log n)** |
| Manual loop | **O(log n)** | **O(1)** |

For `n <= 1000` the differences are negligible, but the bit‑mask approach is *universally* optimal.

---

## ⚠️ Edge Cases & Pitfalls

| Pitfall | Fix |
|---------|-----|
| Off‑by‑one index when iterating a string | Use `i % 2 == 0` for even, `i % 2 == 1` for odd, counting from LSB (reverse index). |
| Wrong mask size | The masks must cover all bits that may appear in `n`. For 32‑bit integers, use `0x55555555` (even) & `0xAAAAAAA` (odd). |
| Not using built‑in popcount | Manual popcount is slower and error‑prone. |
| Assuming `n` is zero | Problem guarantees `n >= 1`, but if you allow `0`, masks still work (both counts will be `0`). |

---

## 🗣️ Interview Tips

1. **Explain the bit‑mask logic**: Show that even and odd indices alternate; therefore we can create a mask that matches those positions.
2. **Show why popcount is efficient**: It's hardware‑accelerated and constant time for fixed word sizes.
3. **Discuss alternatives briefly**: Mention string conversion and manual loop to demonstrate thoroughness, but quickly argue why they’re suboptimal.
4. **Talk about scalability**: For `int` vs `long`, just change the mask constants (`0x55555555`, `0xAAAAAAAA`).
5. **Time‑space trade‑off**: Emphasize constant time & space, which is a hallmark of a good interview answer.

---

## 📚 Alternatives (If you must avoid built‑ins)

### Manual Bit Counting

```java
public int[] evenOddBit(int n) {
    int even = 0, odd = 0, idx = 0;
    while (n != 0) {
        if ((n & 1) == 1) {
            if (idx % 2 == 0) even++;
            else odd++;
        }
        n >>= 1;
        idx++;
    }
    return new int[]{even, odd};
}
```

### String Conversion

```python
def evenOddBit(n):
    bits = bin(n)[2:][::-1]  # LSB first
    even = sum(1 for i, b in enumerate(bits) if i % 2 == 0 and b == '1')
    odd  = sum(1 for i, b in enumerate(bits) if i % 2 == 1 and b == '1')
    return [even, odd]
```

These are excellent for *educational* purposes or languages lacking popcount.

---

## 🎯 Summary

- The **Bit‑Mask + Popcount** method is the fastest, most concise, and showcases deep understanding of bit operations.  
- For `n <= 1000`, any solution passes, but interviewers expect the constant‑time approach.  
- Masks like `0b0101010101` (`341`) for even and `0b1010101010` (`682`) for odd indices isolate the bits in one go.  
- Built‑ins (`Integer.bitCount`, `__builtin_popcount`, `.bit_count()`) make the code a single line—ideal for coding interviews.  

> **Remember**: “Show the mask → apply → popcount → return.” That's all you need to impress.

---

## 🎉 Final Thought

Bit manipulation problems are a staple of technical interviews. Mastering this one not only lands you the `[even, odd]` array in constant time but also gives you a reusable pattern for a wide range of bit‑related questions. Happy coding! 🚀

--- 

> **Want more interview prep?** Check out my other guides on *two‑sum*, *reverse bits*, and *count bits*—all optimized for interview success.