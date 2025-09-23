---
title: LeetCode 2310. Sum of Numbers With Units Digit K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2310. Sum of Numbers With Units Digit K  
**A complete, interview‑ready solution in Java, Python and C++ + a “good‑bad‑ugly” blog post**

> **Keywords** – *Leetcode 2310*, *Sum of Numbers With Units Digit K*, *Java solution*, *Python solution*, *C++ solution*, *coding interview*, *software engineer*, *dynamic programming*, *greedy*, *time‑complexity O(1)*

---

## 1. Problem Statement

> You are given two integers `num` (0 ≤ num ≤ 3000) and `k` (0 ≤ k ≤ 9).  
> You must form a set of **positive** integers that satisfies  
> * every integer ends with the digit `k` (units digit), and  
> * the sum of all integers equals `num`.  
> The set may contain duplicate numbers.  
> Return the **minimum possible size** of such a set.  
> If no such set exists, return `-1`.  
> An empty set is allowed only when `num == 0` (its sum is 0).

---

## 2. Why This Problem is Interview‑Friendly

| Reason | Why It Matters |
|--------|----------------|
| **Simple Math + Observation** | Allows you to test reasoning without heavy data‑structures. |
| **Fixed Bound (10)** | You learn how to exploit digit periodicity to prune the search. |
| **Edge‑Case Heavy** | Trains defensive programming (`k==0`, `num==0`, etc.). |
| **Multiple Languages** | Showcases that you can translate logic between Java, Python, C++. |

---

## 3. High‑Level Insight

For any integer that ends with digit `k`, it can be written as `10 × x + k`.  
If we pick `i` such integers, their sum is `i × k + 10 × S` for some integer `S`.  
The only thing that matters is the **units digit** of the sum: it must match `num % 10`.  
Hence we only need to check for the smallest `i` such that

```
i * k ≤ num          (we cannot exceed the target sum)
i * k % 10 == num % 10   (units digit matches)
```

Because the units digit of `i × k` repeats every 10 values of `i`, we never need to
check beyond `i = 10`.  
So the entire problem boils down to a constant‑time loop of at most 10 iterations.

---

## 4. Correctness Proof Sketch

1. **If `num == 0`** – the empty set satisfies all conditions; size 0 is minimal.  
2. **If a solution exists** with size `i`, then obviously `i × k ≤ num` and the units digits match, so our loop will find it (or an earlier `i` that also works, giving the true minimum).  
3. **If no `i` ≤ 10 satisfies both conditions**, then no `i` ≥ 11 can either, because the units digit pattern repeats every 10 steps. Thus the answer is `-1`.  

Therefore the algorithm returns the minimal size when it exists, otherwise `-1`.

---

## 5. Complexity Analysis

| Metric | Result |
|--------|--------|
| **Time** | `O(1)` – at most 10 iterations. |
| **Space** | `O(1)` – only a few integer variables. |

---

## 6. Edge Cases

| Scenario | What Happens |
|----------|--------------|
| `num == 0` | Returns `0`. |
| `k == 0` | Only multiples of 10 are achievable. The loop still works; it returns `1` if `num % 10 == 0`, otherwise `-1`. |
| `num < k` | The loop condition `i * k <= num` fails immediately; answer `-1`. |
| `num > 3000` | Not allowed by constraints. |
| `k == 5` and `num == 5` | Works; returns `1`. |
| `k == 3`, `num == 1` | No solution → `-1`. |

---

## 7. Code (Java, Python, C++)

### Java

```java
class Solution {
    public int minimumNumbers(int num, int k) {
        if (num == 0) return 0;               // Empty set
        for (int i = 1; i * k <= num && i <= 10; i++) {
            if ((k * i) % 10 == num % 10) {
                return i;                     // Smallest i that works
            }
        }
        return -1;                            // No valid set
    }
}
```

### Python

```python
class Solution:
    def minimumNumbers(self, num: int, k: int) -> int:
        if num == 0:
            return 0
        for i in range(1, 11):                 # 1..10
            if k * i % 10 == num % 10 and i * k <= num:
                return i
        return -1
```

### C++

```cpp
class Solution {
public:
    int minimumNumbers(int num, int k) {
        if (num == 0) return 0;
        for (int i = 1; i * k <= num && i <= 10; ++i) {
            if ((k * i) % 10 == num % 10) return i;
        }
        return -1;
    }
};
```

---

## 8. “The Good, The Bad, The Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic Simplicity** | O(1) loop, no DP, no data structures | Might look trivial, but missing subtle digit‑periodicity can lead to an O(num) solution | Forgetting the `i <= 10` guard may cause a huge loop and TLE on larger constraints |
| **Edge‑Case Coverage** | Handles `num == 0`, `k == 0`, and all digit mismatches | Common pitfall: assuming `k == 0` always works when `num % 10 == 0` | Not checking `i * k <= num` can produce incorrect answers (over‑summing) |
| **Readability** | Clear variable names (`i`, `k`, `num`) and comments | Over‑commenting can clutter the code | Writing a single line `if (k*i%10==num%10 && i*k<=num) return i;` looks clever but hides intent |
| **Language‑specific quirks** | Java’s integer overflow is impossible here | Python’s `range(1, 11)` is inclusive of 10 | C++’s missing check for `num==0` can be missed if copy‑pasted |

---

## 9. Interview Tips

1. **Explain the periodicity** – say why 10 is enough.  
2. **Discuss edge cases first** – `num == 0`, `k == 0`.  
3. **Mention time/space** explicitly – interviewer loves O(1) claims.  
4. **Show alternative** – a quick DP or greedy approach (just to show depth).  
5. **Talk about testing** – give examples of `num=58,k=9`, `num=37,k=2`, `num=0,k=7`.  

---

## 10. Conclusion

This Leetcode problem demonstrates how a seemingly open‑ended “sum” question can be solved in constant time by exploiting the periodic nature of decimal units digits.  
The key take‑away: **always look for hidden patterns** that can shrink a potentially large search space to a fixed small loop.  
Mastering this trick not only lands you a correct solution but also shows interviewers that you can spot elegant shortcuts—a trait every senior engineer covets.

Happy coding, and may your next interview be as smooth as this 10‑iteration loop!