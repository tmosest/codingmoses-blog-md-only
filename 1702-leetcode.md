---
title: LeetCode 1702. Maximum Binary String After Change - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1702 – **Maximum Binary String After Change**  
*The good, the bad, and the ugly – a deep‑dive into an interview‑style greedy problem that can win you a job.*

---

### TL;DR  
* **Problem:** Transform a binary string with two allowed swaps (“00 → 10”, “10 → 01”) to get the numerically largest possible string.  
* **Key insight:** All operations can be simulated by moving a single `0` leftwards over adjacent `1`s.  
* **Optimal solution:**  
  1. Find the first `0`.  
  2. Count the `1`s that appear **after** that zero.  
  3. Construct a string of all `1`s, then place one `0` at index `n - cnt_ones_after_first_zero - 1`.  
* **Complexity:** `O(n)` time, `O(1)` extra space.  

---

## 1. Problem Statement (LeetCode 1702)

You’re given a binary string `binary` (`0`‑`1` only).  
You may repeatedly apply the following two operations any number of times:

| Operation | Condition | Example |
|-----------|-----------|---------|
| 1 | Substring `"00"` | `"00010" → "10010"` |
| 2 | Substring `"10"` | `"00010" → `"00001"` |

Return the **maximum** binary string you can obtain after any number of operations.  
“Maximum” means the decimal value of the binary string is the largest possible.

**Constraints**

* `1 ≤ binary.length ≤ 10⁵`
* `binary[i] ∈ {'0', '1'}`

---

## 2. Why Greedy Works – The “Pattern” Observation

Let’s experiment with a few strings:

| Original | After many ops | Result |
|----------|----------------|--------|
| `000110` | `111011` | ✓ |
| `01` | `01` | ✓ |
| `00110` | `11010` | ✓ |

You’ll notice:

1. **Only one `0` can stay in the final string** – everything else turns into `1`.  
   Any `00` can be replaced by `10`, moving a `0` rightwards.  
   Any `10` can be replaced by `01`, moving a `0` leftwards.  
   Repeatedly applying these, the zeros will “clump” into a single zero that can be moved to any position we want, except the very beginning if the string started with `1`s.

2. **Where the zero lands depends on the number of `1`s that follow the first zero in the original string**.  
   Every `1` after the first zero can be “pushed” to the left of the zero by operation 2.  
   Thus the zero will finally be positioned so that exactly that many `1`s are to its right.

3. **The remaining string is all `1`s except for that single zero** – that’s the maximum possible binary value.

So the greedy rule is:

> *Keep all the leading `1`s as they are, drop every other `0` except one, and push that one `0` as far right as possible given the count of `1`s that were after the first `0`.*

---

## 3. The Algorithm – Step‑by‑Step

1. **Scan from left to right until the first `0`**  
   *If you never encounter a `0`, the string is already maximal – return it.*  

2. **Count `1`s that appear after that first `0`** (`cnt`).  

3. **Build the result**  
   * Create a string of length `n` filled with `1`.  
   * Place a single `0` at index `n - cnt - 1`.  

Why `n - cnt - 1`?  
Because `cnt` is the number of `1`s that must stay to the right of the zero.  
In an all‑ones string, the zero would naturally occupy the *leftmost* spot (index `0`).  
To shift it right by `cnt` positions, we simply move it to `cnt` places from the left, which is `n - cnt - 1` from the right.

---

## 4. Code – Three Languages

Below are clean, production‑ready implementations that run in **O(n)** time and **O(1)** extra space (aside from the output string).

> **Note:** All three codes are identical in logic; only syntax differs.

---

### 4.1 Java

```java
class Solution {
    public String maximumBinaryString(String binary) {
        int n = binary.length();
        int firstZero = binary.indexOf('0');

        // No zero at all – already maximal
        if (firstZero == -1) return binary;

        // Count 1's after the first zero
        int onesAfterZero = 0;
        for (int i = firstZero + 1; i < n; i++) {
            if (binary.charAt(i) == '1') onesAfterZero++;
        }

        // Build the result: all 1's with one 0 placed appropriately
        StringBuilder sb = new StringBuilder();
        sb.append("1".repeat(n));
        int zeroPos = n - onesAfterZero - 1;
        sb.setCharAt(zeroPos, '0');
        return sb.toString();
    }
}
```

---

### 4.2 Python

```python
class Solution:
    def maximumBinaryString(self, binary: str) -> str:
        n = len(binary)
        first_zero = binary.find('0')
        if first_zero == -1:
            return binary

        # Count 1's after the first zero
        ones_after_zero = binary[first_zero+1:].count('1')

        # Build result
        res = ['1'] * n
        zero_pos = n - ones_after_zero - 1
        res[zero_pos] = '0'
        return ''.join(res)
```

---

### 4.3 C++

```cpp
class Solution {
public:
    string maximumBinaryString(string binary) {
        int n = binary.size();
        int firstZero = binary.find('0');
        if (firstZero == string::npos) return binary;   // no zero

        // Count 1's after the first zero
        int onesAfterZero = 0;
        for (int i = firstZero + 1; i < n; ++i)
            if (binary[i] == '1') ++onesAfterZero;

        // Build result
        string res(n, '1');
        int zeroPos = n - onesAfterZero - 1;
        res[zeroPos] = '0';
        return res;
    }
};
```

---

## 5. The Good, The Bad, and The Ugly

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Algorithmic Idea** | Linear scan + simple math. | None. | Over‑engineering with BFS/DP. |
| **Complexity** | `O(n)` time, `O(1)` space. | O(n²) if you simulate every swap. | O(2ⁿ) brute‑force. |
| **Readability** | Clean, one‑pass. | Hard to follow if you mix logic. | Confusing recursive or stack solutions. |
| **Edge Cases** | Handles all‑ones, all‑zeros, mixed. | Fails if zero position isn’t calculated correctly. | Fails on very long strings (stack overflow). |
| **Interview Verdict** | ★★★★★ – simple, deterministic, covers constraints. | ★★☆☆☆ – too slow, unnecessary complexity. | ★☆☆☆☆ – risky, fails on tests. |

**Why the greedy works:**  
Because the operations are local and only affect a `0` and an adjacent `1`, you can think of the string as a “parking lot” where each `0` can drive through `1`s but can’t jump over another `0`. Thus, at most one `0` can survive and its final position is fully determined by how many `1`s were to its right originally. That’s the mathematical heart of the solution.

---

## 6. Why This Blog Helps Your Job Hunt

1. **SEO Keywords** – The article is peppered with terms recruiters search for: *Maximum Binary String After Change*, *LeetCode 1702*, *binary string transformation*, *Java solution*, *Python solution*, *C++ solution*, *greedy algorithm*, *O(n) time*.

2. **Depth of Explanation** – Recruiters appreciate candidates who not only write code but can articulate *why* a solution works.

3. **Code Samples in Multiple Languages** – Demonstrates versatility and ability to work in the language your team uses.

4. **Structured Sections & Headings** – Makes it easy for hiring managers to skim and find what they need.

5. **Problem Context** – Shows you can tackle real LeetCode interview problems, a common requirement for software engineering roles.

---

## 7. Take‑away Cheat‑Sheet

| Step | What to do | Why |
|------|------------|-----|
| 1 | Find first `0`. | Determines if any transformation is possible. |
| 2 | Count `1`s after that `0`. | Those are the ones that will end up to the right of the final zero. |
| 3 | Build an all‑`1` string and insert a `0` at `n - cnt - 1`. | Positions the zero exactly where the operations allow. |
| 4 | Return the string. | Already maximal by construction. |

---

## 8. Final Thoughts

The *Maximum Binary String After Change* problem is a classic example of how a seemingly complicated series of local swaps can collapse into a single linear‑time greedy solution. Mastering this problem showcases:

* **Algorithmic intuition** – spotting the invariant that all zeros collapse into one.
* **Coding elegance** – clean, readable implementation.
* **Interview readiness** – ability to explain both “how” and “why”.

Keep this pattern in your toolkit: whenever you see local operations that only move an element left or right, check if the whole sequence reduces to a single global choice.

Good luck – your next coding interview won’t know what hit it! 🚀

--- 

> *Prepared for you by ChatGPT, the AI assistant that loves clean code and good explanations.*