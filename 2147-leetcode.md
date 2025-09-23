---
title: LeetCode 2147. Number of Ways to Divide a Long Corridor - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 2147. Number of Ways to Divide a Long Corridor  
### (Hard) – Leetcode, O(N) time, O(1) space

| Language |  Time  |  Space |
|----------|--------|--------|
| Java     |  O(n)  |  O(1)  |
| Python   |  O(n)  |  O(1)  |
| C++      |  O(n)  |  O(1)  |

---

### TL;DR  
1. **If the corridor contains an odd number of seats (`S`) → answer = 0.**  
2. **Otherwise** walk the string, keep the index of the *previous* seat.  
3. Whenever we hit the *3rd, 5th, 7th…* seat, multiply the result by the distance between this seat and the previous seat.  
4. Return `result % 1_000_000_007`.

Why it works: every “gap” between a pair of seats can host a divider in any of the positions that lie between the two seats. The number of possible divider positions for a particular pair is exactly the number of indices between the two seats (`i - prevSeat`). Multiplying all these independent choices yields the total number of ways.

---

## 🚀 Code (All 3 Languages)

### Java

```java
public class Solution {
    private static final long MOD = 1_000_000_007L;

    public int numberOfWays(String corridor) {
        long res = 1L;
        int seatCount = 0;
        int prevSeatIdx = 0;      // will be overwritten on first seat

        for (int i = 0; i < corridor.length(); i++) {
            if (corridor.charAt(i) == 'S') {
                seatCount++;

                if (seatCount > 2 && seatCount % 2 == 1) {
                    res = (res * (i - prevSeatIdx)) % MOD;
                }
                prevSeatIdx = i;
            }
        }

        if (seatCount % 2 == 1 || seatCount < 2) {
            return 0;
        }
        return (int) (res % MOD);
    }
}
```

### Python

```python
class Solution:
    MOD = 10**9 + 7

    def numberOfWays(self, corridor: str) -> int:
        res = 1
        seat_cnt = 0
        prev_seat = 0

        for idx, ch in enumerate(corridor):
            if ch == 'S':
                seat_cnt += 1
                if seat_cnt > 2 and seat_cnt % 2 == 1:
                    res = (res * (idx - prev_seat)) % self.MOD
                prev_seat = idx

        return 0 if seat_cnt % 2 == 1 or seat_cnt < 2 else res % self.MOD
```

### C++

```cpp
class Solution {
public:
    int numberOfWays(string corridor) {
        const long long MOD = 1'000'000'007LL;
        long long res = 1;
        int seatCnt = 0;
        int prevSeat = 0;

        for (int i = 0; i < (int)corridor.size(); ++i) {
            if (corridor[i] == 'S') {
                ++seatCnt;
                if (seatCnt > 2 && seatCnt % 2 == 1) {
                    res = (res * (i - prevSeat)) % MOD;
                }
                prevSeat = i;
            }
        }

        if (seatCnt % 2 == 1 || seatCnt < 2) return 0;
        return (int)(res % MOD);
    }
};
```

---

## 📖 Blog Article – “The Good, The Bad, and The Ugly of Leetcode 2147”

### Introduction
When you’re preparing for a software‑engineering interview, Leetcode’s **Number of Ways to Divide a Long Corridor** (Problem 2147) is a classic string‑DP puzzle that seems deceptively simple at first glance. In this post, we dissect the problem from **every angle**:

| Good | Bad | Ugly |
|------|-----|------|
| **Dynamic‑Programming** meets **combinatorics** | Edge‑case pitfalls | Misinterpreting “gap” positions |
| **O(N)** time – ideal for a 30‑minute interview | The *odd‑seat* condition can trip you up | The product of gaps can overflow if you’re not careful |

With detailed explanations, you’ll know *why* the algorithm works, *what* could go wrong, and *how* to avoid common mistakes.  

**SEO keywords** – Leetcode solution, interview algorithm, dynamic programming string, Java/Python/C++ coding interview, O(n) algorithm, modulo 1e9+7, coding interview prep.

---

### 1. Problem Statement (Re‑stated)
Given a string `corridor` of length **≤ 10⁵** that contains only `S` (seat) and `P` (person), you have two seats in a row that must form a “block” of exactly two seats. The goal is to place dividers between consecutive blocks of two seats such that:

* every block has exactly two seats,
* a divider can be placed in any *empty* position between two consecutive seats,
* the whole corridor is partitioned into such blocks.

Return the number of distinct divider placements modulo `10⁹+7`.

---

### 2. Intuition: Why Seats Matter, Not People
If the number of seats is **odd**, it’s impossible to split the corridor into pairs of two seats—think of trying to split 5 seats into groups of 2. That’s the **first “ugly” edge case**: forget it and you’ll return a non‑zero number for an impossible input.

For an **even** number of seats, you can think of the corridor as a sequence of seat positions  
`s₁, s₂, s₃, …, s₂k`.  
Every *divider* has to sit *between* two consecutive seat positions: `s₂` and `s₃`, `s₄` and `s₅`, …, `s₂k-2` and `s₂k-1`.  
Between two consecutive seats you can place the divider in any position that lies strictly *between* them. If the distance between the seats is `d`, there are exactly `d` valid positions.

---

### 3. The Greedy‑Multiplicative Algorithm (Good)
The elegant O(N) solution boils down to one pass:

1. Count seats (`seatCnt`).
2. Keep the index of the *previous* seat (`prevSeatIdx`).
3. When we reach seat number 3, 5, 7, … (odd seat count > 2), we multiply the answer by `i - prevSeatIdx`.

**Why?**  
The choices of divider placement for each *gap* are independent. The total number of configurations is the product of the options for each gap.

#### Proof of Correctness
- *Base*: For the first two seats there is only one way to split the corridor – the divider is *before* seat 3, so we start `res = 1`.
- *Induction Step*: Assume we’ve correctly processed the first `2t` seats (forming `t` blocks).  
  When we encounter seat `2t+1` (the start of the next block), the number of positions between seat `2t` (the last seat of block `t`) and seat `2t+1` is exactly `i - prevSeatIdx`. Multiplying by this factor preserves correctness because each new block’s placement decisions are independent of the previous ones.

By induction, after the loop finishes, `res` equals the product of all independent gap choices, i.e., the total number of divider configurations.

---

### 4. The “Bad” – Common Pitfalls

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| **Ignoring modulo** – Using `int` for the product | Integer overflow → Wrong answer | Use `long long` (Java `long`, Python `int` handles big ints) and apply `% MOD` each multiplication. |
| **Returning on every seat** – `prevSeat` uninitialized | Wrong distance on first odd seat | Initialize `prevSeat` only after encountering the first seat, or set a dummy value that is overwritten on the first seat. |
| **Checking `seatCnt > 1` instead of `seatCnt >= 2`** | Mis‑counts when corridor has exactly 2 seats but no dividers | The answer is 1 (only the initial divider) when `seatCnt == 2`. Ensure `seatCnt >= 2` before returning non‑zero. |
| **Off‑by‑one in the distance** | Counting `i - prevSeat` when `prevSeat` is the *first* seat for the third seat instead of the second | In the provided algorithm `prevSeat` is updated after every seat, so when seat #3 is processed it holds seat #2’s index, giving the correct distance. |
| **Not handling odd seat count** | Returning a positive number for impossible inputs | Add a final check: if `seatCnt % 2 == 1` → return 0. |

---

### 5. The “Ugly” – Alternative Naïve Approaches (and Why They Fail)

| Approach | Complexity | Why It Fails |
|----------|------------|--------------|
| **Brute‑force all divider placements** – DFS/Backtracking | O(2ⁿ) | Exponential time, impossible for `n = 10⁵`. |
| **Dynamic programming with state** – `dp[i][j]` (positions, parity) | O(n²) memory | Too large for 10⁵. |
| **Divide & Conquer by splitting on the first odd seat** | O(n log n) if not careful | Still too slow for worst case. |

These “ugly” solutions are a reminder that **over‑engineering** can kill your runtime on Leetcode. The product‑of‑gaps trick is the cleanest, fastest, and most interview‑friendly.

---

### 6. Tips for the Interview

1. **Read the constraints carefully.**  
   *String length up to 10⁵ → O(N) is mandatory.*  
2. **Explain the intuition first.**  
   “The number of divider positions between two seats is simply the distance between them.”  
3. **State the algorithm step‑by‑step.**  
   This demonstrates that you can translate intuition into a concrete plan.  
4. **Mention the modulo.**  
   Leetcode always asks for modulo 1 000 000 007; forgetting to take `% MOD` can trip the judge.  
5. **Test edge cases yourself.**  
   ```text
   ""          → 0
   "S"         → 0
   "SS"        → 1
   "SPSPSS"    → 0  (odd seats)
   "SSPSPSS"   → 2
   ```
   Show that your code passes these.

---

### 🎉 Conclusion

The “Number of Ways to Divide a Long Corridor” problem is a shining example of **O(N) cleverness** hidden behind a seemingly complicated statement.  
- The *good* is its linear solution—just one scan, one multiplication per odd seat.  
- The *bad* is that a single oversight (odd seat count or forgetting modulo) instantly invalidates the whole answer.  
- The *ugly* lies in the temptation to over‑complicate with DP or backtracking; the simple math is the key.

If you’re tackling your next coding interview, remember: *look for independent choices*, *reduce the problem to a product of gaps*, and *apply modulo early and often*.  

Happy coding! 🚀  

> **If you found this post helpful, give it a thumbs‑up, share it with your teammates, and follow my GitHub/Twitter for more interview‑ready solutions.**  

---  

### 🔗 References  

- Leetcode Problem 2147: [https://leetcode.com/problems/number-of-ways-to-divide-a-long-corridor](https://leetcode.com/problems/number-of-ways-to-divide-a-long-corridor)  
- Discuss Thread – “Number of Ways to Divide a Long Corridor” – [https://leetcode.com/problems/number-of-ways-to-divide-a-long-corridor/discuss](https://leetcode.com/problems/number-of-ways-to-divide-a-long-corridor/discuss)  

---  

> **Want to master more Leetcode “hard” problems?**  
> Subscribe to my newsletter: *[Your Newsletter Link]*  
> Follow me on Twitter: *[@YourHandle]*  
> Good luck, and may your interviews be as clean as this solution!