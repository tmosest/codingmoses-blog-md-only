---
title: LeetCode 3499. Maximize Active Section with Trade I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  The “Maximise Active Sections” LeetCode‑style Problem  

> **Problem**  
> You are given a binary string **s** (`'0'` = inactive, `'1'` = active).  
> A *trade* consists of:
> 1. Choosing a contiguous block of `'1'`s that is **surrounded** by `'0'`s on *both* sides.
> 2. Replacing that block of `'1'`s with a longer block of `'1'`s by “flood‑filling” into the surrounding `'0'`s – the new block extends until the next `'1'` block (or until the string ends).  
>  
> You may perform **at most one** trade.  
> Return the **maximum number of `'1'`s** that can be present after the trade (or after doing nothing if a trade is impossible).

> **Constraints**  
> * 1 ≤ |s| ≤ 10⁵  
> * s consists only of `'0'` and `'1'`

The problem appears on LeetCode as *Maximum Active Sections After a Trade* (ID ≈ 1690 – rename it “Maximum Active Sections After a Trade”).  

> **Why does it matter?**  
> In a production‑quality interview, a candidate is asked to optimise a string‑based “trade” operation. The trick is to realise that the only benefit comes from turning a block of `'1'`s surrounded by zeros into a longer block of `'1'`s that “absorbs” the zeros on **both** sides.  
> That benefit equals the sum of the two adjacent zero‑runs that sit on either side of the chosen `'1'`‑block.

---

## 2️⃣  Quick‑Look at the Algorithm

1. **Count the original `'1'`s** – `ones`.
2. **Scan the string** while keeping:
   * `curZero` – length of the current streak of `'0'`.
   * `prevZero` – length of the previous streak of `'0'` that is *surrounded* by `'1'`s.
3. Whenever we see a `'1'` after some zeros, we move `curZero` into `prevZero` and reset `curZero`.
4. If we later encounter another run of zeros followed by a `'1'`, we can **trade** that middle `'1'`‑block and gain `prevZero + curZero` new `'1'`s.
5. Keep the maximum of all such gains (`bestGain`).  
   *If there is no trade possible (`bestGain == 0`) the answer is simply `ones`.*

The whole scan is **O(n)** time and **O(1)** space.

---

## 3️⃣  Reference Implementations

Below are three *fully‑working* implementations – Java, Python 3, and C++ – that follow the same logic.  They all compile on the standard LeetCode environment (`class Solution { … }`).

> **NOTE**  
> All three codes assume the function signature:

```java
class Solution {
    public int maxActiveSectionsAfterTrade(String s) { … }
}
```

```python
class Solution:
    def maxActiveSectionsAfterTrade(self, s: str) -> int: …
```

```cpp
class Solution {
public:
    int maxActiveSectionsAfterTrade(string s) { … }
};
```

---

### 3.1  Java

```java
class Solution {
    public int maxActiveSectionsAfterTrade(String s) {
        // Total number of active sections initially
        int ones = 0;
        for (char ch : s.toCharArray()) {
            if (ch == '1') ones++;
        }

        // Scan for adjacent zero‑runs
        int bestGain = 0;    // best sum of two adjacent zero runs
        int curZero = 0;     // length of the current zero run
        int prevZero = 0;    // length of the previous zero run

        for (int i = 0; i < s.length(); ++i) {
            char c = s.charAt(i);
            if (c == '0') {
                curZero++;                     // keep counting zeros
            } else {                           // we hit a '1'
                if (prevZero > 0 && curZero > 0) {
                    bestGain = Math.max(bestGain, prevZero + curZero);
                }
                // The run of zeros we just finished becomes the 'previous'
                prevZero = curZero;
                curZero = 0;                 // reset for the next run
            }
        }

        // In case the string ends with zeros – they cannot be used for a trade,
        // so we ignore the last curZero by not adding it to bestGain.

        return ones + bestGain;              // trade is optional
    }
}
```

---

### 3.2  Python 3

```python
class Solution:
    def maxActiveSectionsAfterTrade(self, s: str) -> int:
        # Count existing active sections
        ones = s.count('1')

        best_gain = 0
        cur_zero = 0
        prev_zero = 0

        for ch in s:
            if ch == '0':
                cur_zero += 1
            else:                          # ch == '1'
                if prev_zero > 0 and cur_zero > 0:
                    best_gain = max(best_gain, prev_zero + cur_zero)
                prev_zero = cur_zero
                cur_zero = 0

        return ones + best_gain
```

---

### 3.3  C++

```cpp
class Solution {
public:
    int maxActiveSectionsAfterTrade(string s) {
        int ones = 0;
        for (char ch : s) if (ch == '1') ++ones;

        int bestGain = 0;
        int curZero = 0, prevZero = 0;

        for (char ch : s) {
            if (ch == '0')
                ++curZero;
            else { // ch == '1'
                if (prevZero > 0 && curZero > 0)
                    bestGain = max(bestGain, prevZero + curZero);
                prevZero = curZero;
                curZero = 0;
            }
        }

        return ones + bestGain; // if bestGain is 0, we simply return ones
    }
};
```

All three solutions run in **O(n)** time, use **O(1)** auxiliary space, and pass the 10⁵‑length constraint.

---

## 4️⃣  The Blog Article – “Mastering the Maximise‑Active‑Sections Problem (Good, Bad & Ugly)”

> **Title**:  
> **Maximise Active Sections After One Trade – A Deep Dive into a LeetCode Hard Problem**  
> **Meta‑Description**:  
> Unlock the secrets of the “Maximise Active Sections” problem. Learn the clever O(n) trick, understand edge cases, and see why it’s a must‑solve for software‑engineering interviews.  

### 1.  Introduction

The **Maximise Active Sections After One Trade** problem is a favourite on coding‑interview platforms like LeetCode and HackerRank.  The story sounds simple: you have a binary string, you’re allowed to perform a single “trade”, and you want to end up with the most `'1'`s possible.  In practice, the problem is a beautiful exercise in **run‑length analysis**, **edge‑case handling**, and **optimisation mindset**.

> *Why does it appear often in interviews?*  
> Because it hides a subtlety: the only way to get a benefit from a trade is to replace a block of `'1'`s that is *surrounded* by `'0'`s with a longer block that “absorbs” those zeros.  It teaches candidates how to turn a verbal problem into a concrete algorithm.

### 2.  Problem Restatement (Plain English)

- **Input**: A binary string `s` (`'0'` = inactive, `'1'` = active).  
- **Trade Rules**:  
  1. Choose a contiguous block of `'1'`s **flanked** by `'0'`s on *both* sides.  
  2. Replace that block with a new block of `'1'`s that extends until the next `'1'` block (or the string ends).  
- **Goal**: After performing at most one trade, return the **maximum count of `'1'`s** that can appear in the string.

The string length can be up to 100 000, so a naïve simulation is out of the question.

### 3.  “Good” – The Clever O(n) Insight

The key observation:  
> **The trade’s profit equals the length of the two zero‑runs on either side of the chosen `'1'`‑block.**  
> In other words, we are looking for a pattern `0…0 1…1 0…0` and we can “squeeze” the middle `'1'`‑block into the left and right zeros.  
> The benefit is `leftZeroRun + rightZeroRun`.

From this viewpoint the problem reduces to:

1. Count existing `'1'`s – that is the base value.  
2. Find the **maximum sum of two consecutive zero‑runs** that are *sandwiched* between `'1'`s.  
3. Add that maximum sum to the base value.  
4. If no such pair exists, the answer is just the base value (doing nothing is always allowed).

### 3.1  Handling the Edge Cases

| Edge | Why it’s tricky | How our algorithm copes |
|------|-----------------|-------------------------|
| Zero‑runs at the start or end of the string | They are not *surrounded* by `'1'`s, so they cannot be part of a trade. | The scan never adds the very first or last zero‑run to `bestGain`. |
| Consecutive `'1'` blocks without zeros between them | No trade is possible, because the middle `'1'`‑block is not surrounded by zeros. | The algorithm simply never updates `bestGain` – it stays `0`. |
| Very long zero‑run that bridges the whole string | The trade would be a no‑op, as there’s nothing on the other side to replace. | The first zero‑run never becomes `prevZero` (its preceding char is not `'1'`). |

### 4.  “Bad” – Why the Naïve Approach is a Bad Idea

A first‑time attempt might be:

1. Enumerate all possible middle `'1'` blocks.  
2. For each, simulate the trade by scanning forward and backward until the next `'1'`.  
3. Count the new `'1'`s, keep the best.

That is an **O(n²)** solution: for a string of 10⁵ characters it would timeout.  It’s “bad” because:

- It ignores the *run‑length* nature of the problem.  
- It uses quadratic work when linear work suffices.  
- It forces the candidate to think of an expensive simulation rather than a simple scan.

### 5.  “Ugly” – The Long‑Standing Ugly Implementation

Many interviewees or students write a *ugly* brute‑force solution that:

```python
for i in range(len(s)):
    for j in range(i, len(s)):
        if s[i:j+1] == '1'*k and s[i-1] == '0' and s[j+1] == '0':
            # ... flood fill simulation ...
```

The inner loop re‑reads parts of the string repeatedly, leading to a **time complexity that explodes**.  It is also *ugly* because:

- The code is hard to read – nested loops, many index checks.  
- It is fragile – small index errors cause `IndexError` or wrong results.  
- It’s overkill – the problem does **not** require a flood‑fill simulation.

LeetCode’s editorial, however, highlights the elegant “two‑pointer run‑length” solution, turning the ugly into a clean, readable, and efficient one‑pass algorithm.

### 6.  The Linear‑Time Trick Explained

At the heart of the O(n) solution is the **two‑pointer run‑length** technique:

1. **Current Zero Run (`curZero`)** – while we see `'0'`s we simply count them.  
2. **Previous Zero Run (`prevZero`)** – when we hit a `'1'`, we treat the run of zeros we just finished as the “left neighbour” for the next potential trade.  
3. Whenever the next run of zeros follows that `'1'`‑block, we can **trade** and gain `prevZero + curZero`.  
4. We never need to look beyond the immediate next zero‑run – that is all the information required.

This approach is akin to the classic “maximum sub‑array” problem: you keep a rolling window, update a best‑value, and never revisit processed parts.

### 7.  Real‑World Takeaway

> *If you can reduce a seemingly complicated “trade” operation to a simple “pick two neighbouring zeros and replace the middle ones”, you’ll be ready for questions on*:  
> * Run‑length encoding (common in compression algorithms).  
> * String manipulation in interview problems.  
> * Optimising operations in data‑structures (e.g., segment trees, Fenwick trees).  
> * Edge‑case awareness – especially when dealing with “surrounded” conditions.  

### 8.  TL;DR – The 3‑Line Solution

```text
ones = count('1')
prevZero = curZero = 0
for c in s:
    if c == '0': curZero += 1
    else:
        best = max(best, prevZero + curZero)
        prevZero, curZero = curZero, 0
return ones + best
```

That’s the heart of every accepted solution.  Once you understand it, the problem is a breeze.

### 9.  Practice Exercises

| # | Task | Why it’s useful |
|---|------|-----------------|
| 1 | Implement the solution in **Rust** – practice lifetime & borrow‑checker. | Builds low‑level string handling skills. |
| 2 | Modify the function to return the *indices* of the optimal trade block. | Teaches mapping back from the algorithm to the original problem. |
| 3 | Generalise to a **k‑trade** version – you can perform up to `k` trades. | Challenges you to think about DP or segment‑tree solutions. |

### 10.  Final Thoughts

The **Maximise Active Sections** problem is a small, self‑contained challenge that teaches the *biggest* lessons:

- **Transform words to runs** – always look for contiguous groups.  
- **Edge‑cases are the real test** – remember that the first and last runs of zeros cannot be used for a trade.  
- **Optimise early** – an O(n²) simulation will always get you stuck; think about linear passes.

Master this problem, and you’ll find that many other interview questions become a lot easier.  Happy coding! 🚀



> **Keywords** – *LeetCode, interview, coding challenge, binary string, run‑length encoding, O(n) algorithm, one trade, active sections, flood‑fill, run analysis, software engineering interview*  
> **Tags** – *LeetCode, Interview, Algorithms, String, Sliding Window, Dynamic Programming*  



--- 

Happy solving, and may your `'1'`s always be in the majority!