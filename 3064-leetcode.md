---
title: LeetCode 3064. Guess the Number Using Bitwise Questions I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🔍 LeetCode 3064 – “Guess the Number Using Bitwise Questions I”
*Your one‑stop guide to the cleanest solution in **Java**, **Python** and **C++**, plus a full‑blown interview‑ready blog post.*

---

### Table of Contents
| Section | What you’ll learn |
|---------|--------------------|
| 🧩 Problem Overview | Quick recap of the API and the challenge |
| 🧠 The Insight | Why checking each bit individually works |
| 🚀 The Algorithm | Step‑by‑step explanation |
| ⚡ Code | Java, Python, C++ implementations |
| 🕒 Complexity | Time & space |
| 🔧 Edge Cases & Pitfalls | What to watch out for |
| 🤝 Interview Tips | How to talk about this on the phone |
| 📚 Further Reading | Other LeetCode problems, blogs, books |

---

## 🧩 Problem Overview

**LeetCode 3064 – Guess the Number Using Bitwise Questions I**

- You need to discover a hidden integer `n` (1 ≤ n ≤ 2³⁰ – 1).
- You can ask the API: `int commonSetBits(int num)` which returns the number of bits that are `1` in both `n` and `num`.
- Each call costs a *question*.  
  *Goal*: recover `n` with **O(30)** API calls (i.e., 30 questions).

> **Key fact**: `commonSetBits(num)` is exactly `__builtin_popcount(n & num)`.

---

## 🧠 The Insight – “Ask One Bit at a Time”

Think of binary representation of `n`:

```
bit 29  bit 28  …  bit 1  bit 0
  1        0        1    1
```

If you call the API with a *mask* that has **exactly one bit set** (e.g., `1 << k`), the answer is either:

- `1` → the hidden number also has that bit set.
- `0` → the hidden number has that bit unset.

Hence, we can determine each bit of `n` independently, one question per bit, for all 30 possible bit positions (0‑29).  
That’s exactly 30 API calls – optimal!

---

## 🚀 The Algorithm

1. **Result variable** `res = 0`.
2. **Loop over every bit position** `k` from 0 to 29:
   - `mask = 1 << k`.
   - If `commonSetBits(mask) == 1` → set that bit in `res` (`res |= mask`).
3. Return `res`.

> ⚡ *No need to query higher‑order bits (`2³⁰`) because the constraints guarantee `n < 2³⁰`.*

---

## ⚡ Code

Below are clean, production‑ready solutions in the three languages most popular with interviewers.

### Java (LeetCode Template)

```java
/**
 * LeetCode 3064 – Guess the Number Using Bitwise Questions I
 * @author
 */
public class Solution extends Problem {

    /**
     * The API is provided by the LeetCode runtime.
     * int commonSetBits(int num);
     *
     * @return the hidden number n
     */
    public int findNumber() {
        int result = 0;                 // Will hold the reconstructed number

        // Iterate through 0..29 (inclusive) – 30 bits
        for (int bit = 0; bit < 30; bit++) {
            int mask = 1 << bit;       // One bit set at position `bit`

            // If the hidden number shares this bit, the API returns 1
            if (commonSetBits(mask) == 1) {
                result |= mask;        // Set that bit in the result
            }
        }
        return result;
    }
}
```

### Python (LeetCode Template)

```python
# LeetCode 3064 – Guess the Number Using Bitwise Questions I
class Solution(Problem):
    def findNumber(self) -> int:
        """
        Returns the hidden number n.
        """
        result = 0
        for bit in range(30):
            mask = 1 << bit
            if self.commonSetBits(mask) == 1:  # API returns number of common set bits
                result |= mask
        return result
```

### C++ (LeetCode Template)

```cpp
/**
 * LeetCode 3064 – Guess the Number Using Bitwise Questions I
 * @author
 */
class Solution : public Problem {
public:
    int findNumber() override {
        int result = 0;
        for (int bit = 0; bit < 30; ++bit) {
            int mask = 1 << bit;
            if (commonSetBits(mask) == 1) {   // API returns count of common set bits
                result |= mask;
            }
        }
        return result;
    }
};
```

> ✅ **Why 30 iterations?**  
> `n` is bounded by `2³⁰ – 1`. We don’t need to test the 30‑th bit because it would always be 0.

---

## 🕒 Complexity

| Metric | Analysis |
|--------|----------|
| **Time** | `O(30)` → constant time (30 API calls). |
| **Space** | `O(1)` – only a few integer variables. |

---

## 🔧 Edge Cases & Pitfalls

| Issue | Fix |
|-------|-----|
| **Off‑by‑one on bit range** – using `bit <= 30` would try `1 << 30` → overflow in 32‑bit signed int. | Loop `bit < 30`. |
| **Using multiplication instead of shifting** – `1 * (1 << bit)` still works but is slower. | Prefer `1 << bit`. |
| **Assuming 64‑bit ints** – LeetCode uses 32‑bit `int`. | Stick with `int`. |
| **Wrong API name** – some solutions use `commonSetBits(num)` vs. `commonSetBits` only. | Follow the problem signature exactly. |

---

## 🤝 Interview Tips

1. **Explain the API**: “It returns `popcount(n & num)`.”
2. **Show the observation**: “If I pass `1 << k`, I’ll get `1` iff bit `k` of `n` is set.”
3. **State the optimality**: 30 questions = 30 bits = minimal.
4. **Mention time‑space**: `O(1)` and constant memory.
5. **Discuss alternatives**: binary search over ranges would need `O(log 2³⁰)` ~ 30, but this direct‑bit approach is cleaner.
6. **Answer “why not more bits”**: Constraints guarantee 30 bits suffice.

---

## 📚 Further Reading

- **LeetCode 3065 – Guess the Number Using Bitwise Questions II**  
  *Extends the same idea to two hidden numbers.*  
- **Cracking the Coding Interview – Bit Manipulation**  
  *Covers common tricks like counting bits, flipping, and masking.*  
- **InterviewBit – Bit Manipulation Problems**  
  *Practice more nuanced bit‑wise puzzles.*

---

## 🎯 Bottom Line

- **Problem**: Reconstruct an unknown 30‑bit integer using a `commonSetBits` API.
- **Solution**: Test each bit individually – 30 API calls, `O(1)` time, `O(1)` space.
- **Why it’s great for interviews**: It shows *deep understanding* of bitwise operations, *constant‑time* reasoning, and *clean code*.

Add this solution to your portfolio and sprinkle the insights above in your next coding interview. Good luck landing that job! 🚀

--- 

> *If you found this post helpful, share it with friends, and let me know which interview problems you’re tackling next!*