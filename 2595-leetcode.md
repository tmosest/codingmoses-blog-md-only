---
title: LeetCode 2595. Number of Even and Odd Bits - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# LeetCode 2595 – Number of Even and Odd Bits  
*Easy – Bit‑Manipulation – Java / Python / C++ Solutions – SEO‑Optimised Blog Post*  

---

## 1️⃣ Problem Overview  

> **LeetCode #2595 – Number of Even and Odd Bits**  
> **Difficulty:** Easy  
> **Tags:** Bit Manipulation, Integer, Counting  

> **Statement**  
> Given a positive integer `n`, let:
> - **even** = the number of set bits (1‑bits) whose index is *even* (0‑based from the rightmost bit).  
> - **odd**  = the number of set bits whose index is *odd*.  
> Return the array `[even, odd]`.

> **Examples**  
> | `n` | Binary | `even` | `odd` | Result |  
> |-----|--------|--------|-------|--------|  
> | 50  | 110010 | 1      | 2     | `[1, 2]` |  
> | 2   | 10      | 0      | 1     | `[0, 1]` |

> **Constraints**  
> `1 ≤ n ≤ 1000`  

> **LeetCode link**: https://leetcode.com/problems/number-of-even-and-odd-bits/  

---

## 2️⃣ Why This Problem Matters  

Bit‑manipulation is a classic topic in technical interviews, especially for roles that involve systems, embedded programming, or performance‑critical code. Mastering small tricks—like counting bits on even/odd positions—helps you:
- **Think in binary** (a core skill for any software engineer).  
- **Write cleaner, faster code** by avoiding string conversion.  
- **Answer tricky interview questions** that test your depth of understanding.  

---

## 3️⃣ Solution Ideas  

### 3.1 Naïve String‑Based Approach  
Convert `n` to a binary string, iterate from the right, and increment counters based on the parity of the index.  
*Pros*: Easy to understand.  
*Cons*: Extra memory allocation, slower due to string manipulation.

### 3.2 Bit Mask + Pop‑Count (One‑liner)  
Use two masks:

- `0x55555555` (binary `0101 0101 0101 …`) selects *even* indices.  
- `0xAAAAAAAA` (binary `1010 1010 1010 …`) selects *odd* indices.  

Apply `n & mask` and count the remaining set bits with the CPU’s pop‑count instruction (or language‑provided helper).  

*Pros*: O(1) time, no loops, minimal memory.  
*Cons*: Slightly less readable for beginners; requires knowledge of masks.

### 3.3 Classic Bit‑Loop  
Iterate over the bits of `n`, using a flag that flips between even and odd on each iteration.  

*Pros*: No external helpers; easy to implement.  
*Cons*: O(log n) loops; slower than the mask‑based method for large inputs.

---

## 4️⃣ Reference Implementations  

Below are clean, production‑ready solutions in **Java, Python, and C++** that you can paste directly into your LeetCode workspace.

| Language | Code |
|----------|------|
| **Java** | ```java<br>import java.util.*;<br>public class Solution {<br>    public int[] evenOddBit(int n) {<br>        // Masks for even (0‑based) and odd bits<br>        final int EVEN_MASK = 0x55555555; // 0101…<br>        final int ODD_MASK  = 0xAAAAAAAAL; // 1010…<br>        int even = Integer.bitCount(n & EVEN_MASK);<br>        int odd  = Integer.bitCount(n & ODD_MASK);<br>        return new int[]{even, odd};<br>    }<br>}<br>``` |
| **Python** | ```python<br>from typing import List<br>class Solution:<br>    def evenOddBit(self, n: int) -> List[int]:<br>        # Masks for even and odd positions (32‑bit)<br>        EVEN_MASK = 0x55555555  # 0101…<br>        ODD_MASK  = 0xAAAAAAAAL  # 1010…<br>        even = (n & EVEN_MASK).bit_count()<br>        odd  = (n & ODD_MASK).bit_count()<br>        return [even, odd]<br>``` |
| **C++** | ```cpp<br>#include <vector><cstdint> // for uint32_t<br>using namespace std;<br>class Solution {<br>public:<br>    vector<int> evenOddBit(int n) {<br>        const uint32_t EVEN_MASK = 0x55555555; // 0101…<br>        const uint32_t ODD_MASK  = 0xAAAAAAAAL; // 1010…<br>        int even = __builtin_popcount(n & EVEN_MASK);<br>        int odd  = __builtin_popcount(n & ODD_MASK);<br>        return {even, odd};<br>    }<br>};<br>``` |

> **Why the masks?**  
> `0x55555555` in binary is `0101 0101 …`.  
> When you `AND` this mask with `n`, all bits at odd positions are zeroed out, leaving only the even‑indexed bits.  
> The pop‑count operation (`bitCount`, `.bit_count()`, `__builtin_popcount`) counts the number of `1`s in that result, which is precisely the `even` count.  
> The same logic applies to the odd mask.

---

## 5️⃣ The Good, The Bad, and The Ugly  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Readability** | Mask‑based solution is concise and fast for seasoned developers. | Beginners may find masks opaque; the string‑based method is more intuitive. | Using magic numbers without explanation can confuse reviewers. |
| **Performance** | O(1) time; no loops; CPU pop‑count is hardware‑accelerated. | String conversion introduces heap allocation and O(log n) time. | Using a loop that checks each bit individually (e.g., shifting and modulo) is slower but still passes for small `n`. |
| **Portability** | Relies on language built‑ins (`bitCount`, `bit_count`, `__builtin_popcount`). | Some environments may lack these helpers; fallback logic may be required. | Hard‑coding masks for 32‑bit while `int` could be 64‑bit on some platforms – leads to subtle bugs. |
| **Edge Cases** | Works for `n = 1` and `n = 1000` without special handling. | String‑based approach mis‑counts if leading zeros are considered. | Negative numbers (not in constraints) would break the bit‑mask logic unless unsigned types are used. |

### Best Practices  

1. **Comment the Masks** – Write a short note that explains which indices each mask covers.  
2. **Use `long` / `uint64_t` if 64‑bit integers are possible** – adjust the mask accordingly.  
3. **Avoid Magic Numbers** – Name the masks (e.g., `EVEN_MASK`, `ODD_MASK`).  

---

## 6️⃣ Complexity Analysis  

| Approach | Time | Space |
|----------|------|-------|
| Mask + Pop‑Count | **O(1)** | **O(1)** |
| String conversion | **O(log n)** | **O(log n)** (binary string length) |
| Classic bit‑loop | **O(log n)** | **O(1)** |

For the given constraints (`n ≤ 1000`), all approaches are trivial, but the mask‑based method remains the most elegant for production code.

---

## 7️⃣ Variations & Extensions  

- **Count Bits in a Range** – Summing the even/odd counts for every number in `[L, R]`.  
- **Bit‑parity Problems** – “Number of bits with odd parity” or “bitwise XOR with all numbers”.  
- **Hardware‑Accelerated APIs** – Using `std::popcount` in C++20 or `std::bitset` in modern C++.  

---

## 8️⃣ Interview Tips  

1. **Explain the Mask Idea** – Show you understand binary patterns.  
2. **Mention Pop‑Count** – Many interviewers expect knowledge of built‑in pop‑count functions.  
3. **Talk About Edge Cases** – Discuss how the solution handles the minimum and maximum values in the constraints.  
4. **Time & Space** – Be ready to state the complexity succinctly.  

---

## 9️⃣ SEO‑Optimised Takeaway  

If you’re targeting roles in **software engineering**, **embedded systems**, or **performance optimisation**, mastering *bit‑manipulation tricks* like the **Number of Even and Odd Bits** problem boosts both your interview performance and your code‑quality skills.  

**Key SEO keywords**:  
- LeetCode 2595  
- Number of Even and Odd Bits  
- Bit manipulation interview question  
- Java bit count solution  
- Python bit count example  
- C++ popcount technique  
- Job interview preparation  

Include these in your résumé, LinkedIn articles, and personal blog posts to increase visibility to recruiters searching for *bit‑wise logic*, *integer manipulation*, or *coding interview solutions*.  

---

## 10️⃣ Conclusion  

The **Number of Even and Odd Bits** problem is a textbook illustration of how a clever bit mask can turn a seemingly “O(log n)” question into an *O(1)* one. By implementing the one‑liner mask solution in **Java, Python, and C++**, you demonstrate:

- **Efficiency** – leveraging CPU‑level pop‑count instructions.  
- **Clarity** – using well‑named constants and concise logic.  
- **Readiness for Interviews** – showcasing your ability to translate problem constraints into optimal code.  

Feel free to copy the snippets above into your LeetCode workspace or into your personal project to showcase your mastery of bit‑wise operations. Good luck on your coding journey and in your next technical interview! 🚀  

---  

*Happy coding!*