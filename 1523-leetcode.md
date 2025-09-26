---
title: LeetCode 1523. Count Odd Numbers in an Interval Range - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1523 – Count Odd Numbers in an Interval Range  
### The Good, the Bad, and the Ugly  
*(SEO‑friendly guide that will help you land that software engineering interview)*  

---

### TL;DR  
- **Problem**: Count the odd integers in the inclusive range `[low, high]`.  
- **Constraints**: `0 ≤ low ≤ high ≤ 10^9`.  
- **Optimal solution**: O(1) time, O(1) space.  
- **Common pitfalls**: off‑by‑one errors, integer division quirks, ignoring large values.  

> **Want to impress interviewers?**  
> Show you know *why* the math works, and be ready to discuss *edge cases* and *alternatives* (e.g., two‑pointer).  

---

## 1. Problem Statement

> **LeetCode 1523 – Count Odd Numbers in an Interval Range**  
> Given two non‑negative integers `low` and `high`, return the count of odd numbers between `low` and `high` (inclusive).

Examples  
| low | high | Output | Explanation |
|-----|------|--------|-------------|
| 3   | 7    | 3      | 3, 5, 7 |
| 8   | 10   | 1      | 9 |

---

## 2. Constraints Recap

| Item | Value |
|------|-------|
| `low` | `0 … 10^9` |
| `high` | `low … 10^9` |

Because the input range can be huge, *iterating* over every integer is **not** acceptable for an interview‑style solution.

---

## 3. Approaches

### 3.1. Brute‑Force (O(n) time)

```text
count = 0
for i from low to high:
    if i % 2 == 1:
        count += 1
return count
```

**Why it’s bad**:  
- Linear time → 1 billion iterations worst‑case.  
- Fails time limits on large inputs.  
- Not something an interviewer will expect.

---

### 3.2. Math Formula – **Good** (O(1) time)

**Observation**  
- The number of odd integers in `[0, x]` is `(x + 1) / 2` (integer division).  
  *Why?*  
  - For `x = 0` → 0 odds → `(0+1)/2 = 0`.  
  - For `x = 1` → 1 odd → `(1+1)/2 = 1`.  
  - Each step adds 1 odd if `x` is odd, otherwise no new odd.

Therefore:

```
oddCount(high) - oddCount(low-1)
```

```cpp
int countOdds(int low, int high) {
    auto oddUpTo = [](long long x) {
        return (x + 1) / 2;        // integer division
    };
    return oddUpTo(high) - oddUpTo(low - 1);
}
```

- **Time**: `O(1)`  
- **Space**: `O(1)`  
- **Edge‑case**: When `low = 0`, `low-1 = -1` → `(−1+1)/2 = 0`, which is correct.

> **Why this is the “good” solution**  
> *Simplicity*, *correctness*, *constant time*, and *constant memory*.  
> Perfect for interviews.

---

### 3.3. Two‑Pointer – **The Ugly** (O(n) but still better than brute‑force)

```text
1. Make low the first odd number in the range.
2. Make high the last odd number in the range.
3. Count how many steps you can jump in 2‑step increments.
```

**Java** (from the snippet you posted):

```java
class Solution {
    public int countOdds(int low, int high) {
        if (low == high) return (low % 2 == 0) ? 0 : 1;

        // Make low odd
        low = (low % 2 == 0) ? low + 1 : low;
        // Make high odd
        high = (high % 2 == 0) ? high - 1 : high;

        int count = (low != high) ? 2 : 0;
        while (low < high) {
            low += 2;
            high -= 2;
            if (low < high) count += 2;
        }
        if (low == high) count += 1;
        return count;
    }
}
```

- **Time**: `O(n/2)` (worst‑case ~5 × 10⁸ iterations → still too slow for interview).  
- **Space**: `O(1)`  

> **Why it’s the “ugly” solution**  
> It works, but you still loop over the interval.  
> The math version is far cleaner and faster.

---

### 3.4. One‑Line Python – **Clean** (O(1))

```python
class Solution:
    def countOdds(self, low: int, high: int) -> int:
        return (high + 1) // 2 - low // 2
```

- Uses integer division (`//`).  
- Handles the edge case `low = 0` automatically.

---

### 3.5. One‑Line C++ – **Even Cleaner** (O(1))

```cpp
class Solution {
public:
    int countOdds(int low, int high) {
        return (high + 1) / 2 - low / 2;
    }
};
```

---

## 4. Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Brute‑Force | `O(high - low + 1)` | `O(1)` |
| Math Formula | `O(1)` | `O(1)` |
| Two‑Pointer | `O(high - low)` | `O(1)` |
| One‑Line Python / C++ | `O(1)` | `O(1)` |

The optimal solution runs in constant time, regardless of input size, and uses only a couple of variables.

---

## 5. Common Edge Cases

| Edge Case | Why it matters | How the math solution handles it |
|-----------|----------------|---------------------------------|
| `low = 0` | `low-1 = -1` → negative division | `(−1+1)/2 = 0` → correct |
| `low = high` | Single number interval | The formula still works |
| Large inputs (`10^9`) | Potential overflow in some languages | Use `long long` in C++ / `long` in Java if needed |
| `low` or `high` odd vs. even | Off‑by‑one in naive loop | The integer division logic naturally accounts for parity |

---

## 6. Interview Tips

1. **Start with the math insight** – explain how you can count odds up to any `x`.  
2. **Write the formula** on the board: `count = (high + 1) / 2 - low / 2`.  
3. **Discuss edge cases**: zero, single element ranges, very large numbers.  
4. **Time & space complexity**: mention O(1) and why it’s optimal.  
5. **Show awareness of alternatives**: you could use a two‑pointer approach, but it’s unnecessary.  

Interviewers love when you:
- *Know* the fastest solution.  
- *Explain* why it works (mathematical reasoning).  
- *Anticipate* pitfalls and edge cases.  

---

## 7. Takeaway

- **Good**: Constant‑time formula.  
- **Bad**: Brute‑force enumeration.  
- **Ugly**: Two‑pointer with unnecessary looping.  

Master the formula, understand its derivation, and you’ll nail this question in any technical interview.  

---  

### 🚀 Ready to impress recruiters?  
- **Add** this solution to your portfolio.  
- **Tag** your blog post with: `LeetCode 1523`, `Count Odd Numbers`, `O(1) algorithm`, `Java`, `Python`, `C++`, `Coding Interview`, `Software Engineer`.

Happy coding—and good luck on your next interview!