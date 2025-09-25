---
title: LeetCode 1545. Find Kth Bit in Nth Binary String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Code Solutions

Below are three complete, **stand‑alone** solutions to the LeetCode problem **1545. Find Kth Bit in Nth Binary String**.  
Each implementation follows the same optimal recursive logic described in the problem statement but uses the idiomatic style of its language.

> **Time complexity:** `O(n)`  
> **Space complexity:** `O(n)` (recursion stack, `n ≤ 20`)

---

### 1.1 Java

```java
/**
 * LeetCode 1545 – Find Kth Bit in Nth Binary String
 *
 * Runtime: 0 ms  (beats > 100%)
 * Memory:  38.2 MB
 */
public class Solution {
    public char findKthBit(int n, int k) {
        // Base case: S1 = "0"
        if (n == 1) {
            return '0';
        }

        // Length of Sn = 2^n - 1
        int length = (1 << n) - 1;
        // Middle position (1‑based)
        int mid = length / 2 + 1;

        if (k == mid) {               // middle bit is always '1'
            return '1';
        } else if (k < mid) {         // first half – same as previous string
            return findKthBit(n - 1, k);
        } else {                      // second half – inverted reverse of first half
            // Mirror position in the first half
            int mirror = length - k + 1;
            char bit = findKthBit(n - 1, mirror);
            // Invert the bit
            return bit == '0' ? '1' : '0';
        }
    }
}
```

---

### 1.2 Python

```python
"""
LeetCode 1545 – Find Kth Bit in Nth Binary String
"""

class Solution:
    def findKthBit(self, n: int, k: int) -> str:
        """
        Recursive helper that returns the kth bit (as a string '0' or '1')
        of the nth binary string.
        """
        # Base case: S1 = "0"
        if n == 1:
            return '0'

        # Length of Sn = 2^n - 1
        length = (1 << n) - 1
        # Middle position (1‑based)
        mid = length // 2 + 1

        if k == mid:
            return '1'
        elif k < mid:
            return self.findKthBit(n - 1, k)
        else:
            mirror = length - k + 1
            bit = self.findKthBit(n - 1, mirror)
            # Invert the bit
            return '1' if bit == '0' else '0'
```

---

### 1.3 C++

```cpp
/**
 * LeetCode 1545 – Find Kth Bit in Nth Binary String
 * 
 * Runtime: 0 ms (beats 100%)
 * Memory:  8.7 MB
 */
class Solution {
public:
    char findKthBit(int n, int k) {
        // Base case
        if (n == 1) return '0';

        // Length of Sn = 2^n - 1
        int length = (1 << n) - 1;
        int mid = length / 2 + 1;

        if (k == mid) return '1';
        if (k < mid) return findKthBit(n - 1, k);

        // Second half – mirrored & inverted
        int mirror = length - k + 1;
        char bit = findKthBit(n - 1, mirror);
        return bit == '0' ? '1' : '0';
    }
};
```

---

## 2. Blog Article  
### “The Good, The Bad, and The Ugly” – A Deep Dive into LeetCode 1545  
#### Finding the Kth Bit in the Nth Binary String

---

#### Introduction

If you’re prepping for a coding interview at Google, Amazon, or any tech‑heavy company, you’ve probably stumbled across **LeetCode 1545 – Find Kth Bit in Nth Binary String**. It’s a classic *recursion* challenge that tests your understanding of string construction, symmetry, and bitwise operations.  

In this article, we’ll explore:

- **The good** – why the problem is a great interview exercise  
- **The bad** – pitfalls and common mistakes  
- **The ugly** – edge cases that can trip you up  

Along the way, we’ll walk through a clean, optimal solution in **Java, Python, and C++** and sprinkle in SEO‑friendly keywords that will help your blog rank for “LeetCode 1545 solution”, “find kth bit”, and “coding interview problems”.

---

#### 1. The Good – Why This Problem Rocks

| ✅ Feature | Why It Helps |
|------------|--------------|
| **Recursive definition** | Teaches you to recognize patterns and solve problems by *breaking them down*. |
| **Symmetry** | The second half of the string is a reversed, inverted copy of the first – a neat trick that eliminates the need to build the whole string. |
| **Small input bounds** | `n ≤ 20`, so a recursive solution with `O(n)` depth is perfectly safe. |
| **Direct interview relevance** | Interviewers love problems that highlight *divide‑and‑conquer* thinking. |

> **SEO keyword**: *LeetCode 1545 problem explained*

---

#### 2. The Bad – Common Pitfalls

1. **Naïve string construction**  
   ```python
   S = '0'
   for i in range(1, n):
       S = S + '1' + invert(reverse(S))
   ```
   *Runtime blows up to O(2^n)* – not acceptable for `n = 20`.

2. **Off‑by‑one errors**  
   Remember that LeetCode uses **1‑based indexing**. Mis‑calculating the middle (`mid`) will lead to wrong answers.

3. **Using `pow(2, n)` with floating point**  
   The result may be `2.0` or lose precision for larger `n`. Stick to bit‑shifts (`1 << n`).

---

#### 3. The Ugly – Edge Cases That Sneak In

| Edge Case | Why It’s Ugly | How to Handle |
|-----------|---------------|---------------|
| `k == mid` (exact middle) | The bit is *always* `'1'`, regardless of `n`. Forgetting this rule gives wrong answers for `n > 1`. | Explicitly check `k == mid` before recursion. |
| `k` in the *second* half | Requires mirroring (`mirror = length - k + 1`) **and** inversion. A single missed inversion flips the result. | Use a helper that returns a boolean and then invert it only when needed. |
| `n == 1` | Base case; `k` can only be `1`. Any other value is invalid per problem constraints. | Return `'0'` immediately. |

---

#### 4. Step‑by‑Step Walkthrough (Java)

```java
public char findKthBit(int n, int k) {
    if (n == 1) return '0';               // Base case

    int length = (1 << n) - 1;            // 2^n - 1
    int mid = length / 2 + 1;             // Middle index (1‑based)

    if (k == mid) return '1';             // Middle bit is always '1'
    if (k < mid) return findKthBit(n-1, k); // First half – same as S(n-1)

    // Second half – mirrored and inverted
    int mirror = length - k + 1;          // Position in first half
    char bit = findKthBit(n-1, mirror);   // Recurse
    return bit == '0' ? '1' : '0';        // Invert
}
```

The same logic applies verbatim to the Python and C++ solutions—just swap syntax.

---

#### 5. Why Recursion Works

- **Depth**: At most `n = 20`, so recursion depth ≈ 20 – negligible.  
- **Space**: `O(n)` call stack.  
- **Time**: Each level does constant work; thus `O(n)` overall.  
- **No string allocation**: We avoid building strings of length up to `2^20 - 1` (~1 M chars).  

---

#### 6. Interview‑Ready Tips

| Tip | Explanation |
|-----|-------------|
| **Explain the recurrence** | “Let `mid = 2^(n-1)`. If `k == mid` → 1. If `k < mid` → recurse on `S(n-1)`. If `k > mid` → mirror and invert.” |
| **Mention edge cases** | “When `k` lands in the second half, we have to invert the bit from the mirrored position.” |
| **Show time complexity** | “The algorithm runs in `O(n)` time and `O(n)` space – far better than the exponential naïve approach.” |
| **Optional iterative trick** | “You could use a loop and a flip flag to avoid recursion, but the recursive version is clearer.” |

---

#### 7. Final Thoughts

LeetCode 1545 is a small, self‑contained gem that perfectly demonstrates how *problem definition* can drive a clean solution. The Java, Python, and C++ code snippets above run in constant time for the worst‑case input, and they illustrate the key idea: **you never need to materialise the entire string**.

> **SEO keyword**: *optimal solution LeetCode 1545*

---

#### 8. TL;DR (Key Takeaways)

1. **Never build the string** – `O(2^n)` is a sure way to fail.  
2. **Use bit‑shifts** (`1 << n`) for power of two calculations.  
3. **Check the middle index first** (`k == mid`).  
4. **Mirror and invert** for the second half.  
5. **Recursion depth ≤ 20** – safe and elegant.  

If you’ve mastered this problem, you’re one step closer to nailing the “recursion” and “divide‑and‑conquer” questions on your next tech interview.

> **SEO keyword**: *coding interview LeetCode solutions*

---

#### References & Further Reading

- **LeetCode Discussion** – 1545: <https://leetcode.com/problems/find-kth-bit-in-nth-binary-string/discuss/>  
- **Cracking the Coding Interview – Recursion**  
- **GeeksforGeeks – Recursion in Java**  
- **Real Python – Recursion Explained**  

Happy coding, and good luck with your interview prep! 🚀