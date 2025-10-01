---
title: LeetCode 1927. Sum Game - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Sum Game – LeetCode 1927  
**“Alice vs. Bob – Who Wins?”**  

> **Problem ID:** 1927  
> **Difficulty:** Medium  
> **Tags:** Game Theory, Greedy, Mathematics  

> **TL;DR** –  The winner can be decided in **O(n)** time by looking only at the
> two halves of the string and the number of missing digits (`?`).  
>  Code is available in **Java, Python, and C++** – all O(1) extra memory.

---

## 1.  Problem Recap

| Player | Turn | Action |
|--------|------|--------|
| Alice  | 1st | Replace one `?` with a digit `0–9` |
| Bob    | 2nd | Replace one `?` with a digit `0–9` |
| …      | …    | … |
| Game ends | – | No `?` left |

Let  

* `leftSum`  – sum of the digits in the left half  
* `rightSum` – sum of the digits in the right half  
* `leftQ`    – number of `?` in the left half  
* `rightQ`   – number of `?` in the right half  

Bob wins **iff** after all moves  
`leftSum == rightSum`.  
Alice wins otherwise.  
Both play optimally.

---

## 2.  Intuition & The “Easiest” Insight

* Each `?` can be turned into any digit `0–9`.  
  The **maximum imbalance** one player can create with a single `?` is `9`
  (by choosing `9` on one side or `0` on the other).  
* Players alternate turns; therefore the **difference** in the number of `?`
  on each side tells us how many “extra” turns Alice will get on the side
  that is already ahead.  
* The game boils down to a single arithmetic check:

\[
\text{If}\;  (rightSum - leftSum) \times 2 \;=\; (leftQ - rightQ) \times 9
\]

then Bob can balance the sums; otherwise Alice can force a difference.

**Why does this work?**

* `rightSum - leftSum` is the current gap.  
  If it is zero, the game is already balanced → Bob wins.  
* Each of Alice’s turns on the *left* side can change the gap by at most `+9`
  (she puts a `9`), whereas each of Bob’s turns on the *right* side can
  reduce the gap by at most `-9`.  
  The *net* effect after all turns is `9 * (leftQ - rightQ)`.  
* If this net effect can exactly cancel the initial gap (i.e. the
  equality above), Bob can force equality; otherwise Alice wins.

The formula is symmetric: if the sides are swapped the algebra stays the same.

---

## 3.  Algorithm

```text
1. n = len(num)          // even
2. leftSum = rightSum = 0
3. leftQ   = rightQ   = 0

4. For i in [0 .. n/2-1]:
       if num[i] == '?' : leftQ++
       else             : leftSum += digit(num[i])

5. For i in [n/2 .. n-1]:
       if num[i] == '?' : rightQ++
       else             : rightSum += digit(num[i])

6. If leftQ == 0 and rightQ == 0:
       return leftSum != rightSum     // no moves → Alice wins iff sums differ

7. return (rightSum - leftSum) * 2 != (leftQ - rightQ) * 9
   // true → Alice wins, false → Bob wins
```

*Time*: **O(n)** – one scan of the string.  
*Space*: **O(1)** – only four counters.

---

## 4.  Edge Cases

| Case | Why it matters | Outcome |
|------|----------------|---------|
| All digits, no `?` | Game ends immediately | Alice wins iff sums differ |
| All `?` | Both halves have the same number of `?` | Bob wins (gap can be 0) |
| Odd number of `?` on one side | Alice gets an extra turn on that side | Formula handles it automatically |
| `?` only on one side | The other side is fixed | Alice can force a difference if gap > 9·Q |
| Empty string | Not allowed by constraints | – |

---

## 5.  Implementations

Below are clean, production‑ready solutions for **Java**, **Python**, and **C++**.  
All follow the same algorithmic idea and use only constant extra memory.

---

### 5.1  Java

```java
public class Solution {
    public boolean sumGame(String num) {
        int n = num.length();
        int leftSum = 0, rightSum = 0;
        int leftQ = 0, rightQ = 0;

        // first half
        for (int i = 0; i < n / 2; i++) {
            char c = num.charAt(i);
            if (c == '?') leftQ++;
            else leftSum += c - '0';
        }

        // second half
        for (int i = n / 2; i < n; i++) {
            char c = num.charAt(i);
            if (c == '?') rightQ++;
            else rightSum += c - '0';
        }

        // no moves left
        if (leftQ == 0 && rightQ == 0) {
            return leftSum != rightSum;
        }

        // core inequality – Alice wins if it is not satisfied
        return (rightSum - leftSum) * 2 != (leftQ - rightQ) * 9;
    }
}
```

* **Time**: O(n)  
* **Space**: O(1)

---

### 5.2  Python

```python
class Solution:
    def sumGame(self, num: str) -> bool:
        n = len(num)
        left_sum = right_sum = 0
        left_q = right_q = 0

        for i in range(n // 2):
            ch = num[i]
            if ch == '?':
                left_q += 1
            else:
                left_sum += int(ch)

        for i in range(n // 2, n):
            ch = num[i]
            if ch == '?':
                right_q += 1
            else:
                right_sum += int(ch)

        # no '?' left
        if left_q == 0 and right_q == 0:
            return left_sum != right_sum

        # core check
        return (right_sum - left_sum) * 2 != (left_q - right_q) * 9
```

* **Time**: O(n)  
* **Space**: O(1)

---

### 5.3  C++

```cpp
class Solution {
public:
    bool sumGame(string num) {
        int n = num.size();
        int leftSum = 0, rightSum = 0;
        int leftQ = 0, rightQ = 0;

        for (int i = 0; i < n / 2; ++i) {
            char c = num[i];
            if (c == '?') ++leftQ;
            else leftSum += c - '0';
        }

        for (int i = n / 2; i < n; ++i) {
            char c = num[i];
            if (c == '?') ++rightQ;
            else rightSum += c - '0';
        }

        if (leftQ == 0 && rightQ == 0)
            return leftSum != rightSum;

        return (rightSum - leftSum) * 2 != (leftQ - rightQ) * 9;
    }
};
```

* **Time**: O(n)  
* **Space**: O(1)

---

## 6.  “The Good, The Bad, and The Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | O(n) – linear scan | – | – |
| **Space Complexity** | O(1) – constant | – | – |
| **Readability** | Simple arithmetic, clear variable names | Might be hard to spot the `*2` / `*9` trick | The trick can be opaque to newcomers |
| **Extensibility** | Works for any even‑length string | Not designed for odd lengths (invalid per constraints) | If the game changed to allow other digits or weights, the formula would break |
| **Proof of Optimality** | Game theory reasoning gives a rigorous proof | No exhaustive simulation needed | A novice might think a brute‑force DP is required and waste time |

---

## 7.  Why This Blog Helps Your Job Hunt

* **Demonstrates Game‑Theory Thinking** – LeetCode 1927 is a classic test of strategic reasoning.  
* **Shows Algorithmic Elegance** – Turning a seemingly complex turn‑by‑turn game into a single linear check impresses interviewers.  
* **Multi‑Language Proficiency** – Providing Java, Python, and C++ solutions shows you can code in the language the company prefers.  
* **SEO‑Ready Content** – Keywords such as *“Sum Game LeetCode 1927 solution”*, *“Alice Bob optimal play”*, *“Python O(n) string game”* help recruiters find this post when searching for interview preparation material.  

---

## 8.  Final Takeaway

The **Sum Game** is a beautiful example of how a deep observation (maximum effect of a `?` is `9`) can collapse an apparent combinatorial explosion into a **constant‑time arithmetic test**.  
Remember:  
1. Split the string.  
2. Count sums and question marks.  
3. Apply the inequality  

\[
(rightSum - leftSum) \times 2 \neq (leftQ - rightQ) \times 9
\]

If true, Alice wins; otherwise Bob wins.  

Happy coding—and good luck on your next interview!