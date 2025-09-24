---
title: LeetCode 1769. Minimum Number of Operations to Move All Balls to Each Box - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. The Problem at a Glance

**Problem name:** 1769. *Minimum Number of Operations to Move All Balls to Each Box*  
**Difficulty:** Medium  
**Link:** <https://leetcode.com/problems/minimum-number-of-operations-to-move-all-balls-to-each-box/>

> You have `n` boxes in a line.  
> `boxes[i]` is `'0'` if the *i‑th* box is empty, `'1'` if it contains one ball.  
> In one operation you may move **one** ball to an *adjacent* box (`|i-j|==1`).  
> After the moves you may have more than one ball in a box.  
>  
> **Task:** For every index `i` (0‑based) compute the minimum total number of operations needed to gather *all* balls into box `i`.  
>  
> Return an array `answer[0 … n-1]` of size `n`.

Constraints: `1 <= n <= 2000`, `boxes[i] ∈ {'0','1'}`.

---

## 2. The Core Idea – Two‑Pass Prefix / Suffix Accumulation

The brute‑force way would be to simulate moving each ball to every target, which is `O(n²)` for each ball and obviously infeasible.  
Instead, we can compute the answer for every box in linear time with two simple passes:

| Step | What we store | Why it works |
|------|---------------|--------------|
| **Left‑to‑Right pass** | For each index `i` the **number of balls to the left** of `i` (`leftCnt`) and the **total distance** those balls need to travel to reach `i` (`leftOps`). | Every time we move right, the distance to the new box increases by 1 for every ball already to the left. |
| **Right‑to‑Left pass** | For each index `i` the **number of balls to the right** of `i` (`rightCnt`) and the **total distance** those balls need to travel to reach `i` (`rightOps`). | Symmetric to the left pass, but now moving left. |
| **Answer** | `answer[i] = leftOps[i] + rightOps[i]` | All balls to the left plus all balls to the right – that's exactly the total operations needed to bring them all to `i`. |

Both passes are `O(n)` and use only constant additional space.

### Proof of Correctness

We prove the algorithm is correct by induction over the two passes.

#### Lemma 1  
After the left‑to‑right pass, for every index `i`:

- `leftCnt[i]` equals the number of balls in boxes `0 … i‑1`.
- `leftOps[i]` equals the sum of distances that each of those balls must travel to reach box `i`.

*Proof by induction on `i`.*

- **Base (i=0).** There are no boxes to the left.  
  `leftCnt[0] = 0`, `leftOps[0] = 0`. The lemma holds.

- **Inductive step.** Assume the lemma holds for index `i`.  
  When moving to `i+1`:
  * `leftCnt[i+1] = leftCnt[i] + (boxes[i]=='1'?1:0)` – we add the ball at `i` if it exists.  
  * Each ball that was left of `i` now needs one extra step to reach `i+1`, thus `leftOps[i+1] = leftOps[i] + leftCnt[i]`.  
  This matches the definition of `leftCnt[i+1]` and `leftOps[i+1]`. ∎

#### Lemma 2  
Analogously, after the right‑to‑left pass, for every index `i`:
- `rightCnt[i]` equals the number of balls in boxes `i+1 … n-1`.
- `rightOps[i]` equals the sum of distances that each of those balls must travel to reach box `i`.

The proof is symmetric to Lemma&nbsp;1.

#### Theorem  
For every index `i`, `answer[i] = leftOps[i] + rightOps[i]` is the minimal number of operations to gather all balls in box `i`.

*Proof.*  
All balls to the left of `i` contribute exactly `leftOps[i]` moves (by Lemma 1) to bring them to `i`.  
All balls to the right of `i` contribute exactly `rightOps[i]` moves (by Lemma 2).  
Since the moves for left and right balls are independent (they never interfere), the total minimal cost is the sum of the two, i.e. `answer[i]`. ∎

---

## 3. Implementation in Three Languages

Below are clean, commented implementations for **Java**, **Python**, and **C++**.  
Each version follows the same two‑pass logic described above.

---

### 3.1 Java

```java
/**
 * LeetCode 1769 – Minimum Number of Operations to Move All Balls to Each Box
 *
 * Time Complexity:  O(n)
 * Space Complexity: O(1) (apart from the output array)
 */
public class Solution {
    public int[] minOperations(String boxes) {
        int n = boxes.length();
        int[] answer = new int[n];

        // ----- Left to Right Pass -----
        int leftCnt = 0;   // balls left of current index
        int leftOps = 0;   // operations needed to bring left balls to current index
        for (int i = 0; i < n; i++) {
            answer[i] = leftOps;            // left contribution so far
            if (boxes.charAt(i) == '1') {
                leftCnt++;                  // new ball appears at i
            }
            leftOps += leftCnt;             // every left ball moves one more step to reach i+1
        }

        // ----- Right to Left Pass -----
        int rightCnt = 0;
        int rightOps = 0;
        for (int i = n - 1; i >= 0; i--) {
            answer[i] += rightOps;          // add right contribution
            if (boxes.charAt(i) == '1') {
                rightCnt++;
            }
            rightOps += rightCnt;           // every right ball moves one more step to reach i-1
        }

        return answer;
    }
}
```

---

### 3.2 Python

```python
# LeetCode 1769 – Minimum Number of Operations to Move All Balls to Each Box
# Time:  O(n)
# Space: O(1) excluding the result list

def minOperations(boxes: str) -> list[int]:
    n = len(boxes)
    answer = [0] * n

    # Left to right
    left_cnt = 0   # number of balls to the left of i
    left_ops = 0   # total distance for those balls to reach i
    for i, ch in enumerate(boxes):
        answer[i] = left_ops
        if ch == '1':
            left_cnt += 1
        left_ops += left_cnt

    # Right to left
    right_cnt = 0
    right_ops = 0
    for i in range(n - 1, -1, -1):
        answer[i] += right_ops
        if boxes[i] == '1':
            right_cnt += 1
        right_ops += right_cnt

    return answer
```

---

### 3.3 C++

```cpp
/**
 * LeetCode 1769 – Minimum Number of Operations to Move All Balls to Each Box
 *
 * Complexity: O(n) time, O(1) extra space (aside from the output vector)
 */
#include <vector>
#include <string>

class Solution {
public:
    std::vector<int> minOperations(const std::string& boxes) {
        int n = boxes.size();
        std::vector<int> answer(n, 0);

        // Left to right
        int leftCnt = 0;   // balls left of current index
        int leftOps = 0;   // distance sum for those balls to current index
        for (int i = 0; i < n; ++i) {
            answer[i] = leftOps;
            if (boxes[i] == '1') leftCnt++;
            leftOps += leftCnt;
        }

        // Right to left
        int rightCnt = 0;
        int rightOps = 0;
        for (int i = n - 1; i >= 0; --i) {
            answer[i] += rightOps;
            if (boxes[i] == '1') rightCnt++;
            rightOps += rightCnt;
        }

        return answer;
    }
};
```

---

## 4. Blog Article – “The Good, The Bad, and the Ugly” of Moving Balls to Boxes

### 4.1 Introduction

> *“A job interview is not just about your code, it’s about how you think.”*  
> In technical interviews, problems like **1769 – Minimum Number of Operations to Move All Balls to Each Box** are common. They test your ability to turn a seemingly combinatorial puzzle into a linear‑time algorithm.  
> Below, we dissect the problem, explore its strengths and pitfalls, and present a clean, production‑ready solution in Java, Python, and C++.  
> By the end, you’ll be able to walk into an interview, explain your reasoning, and code the answer in minutes.

---

### 4.2 The Good – Why This Problem is a Star Interview Question

1. **Clarity of Objective**  
   The goal – *move all balls to a target box* – is crystal clear. There’s no hidden trick; you just need to count distances.

2. **Optimal Substructure**  
   The problem naturally decomposes into *left* and *right* halves. That reveals a clean DP / prefix‑sum solution.

3. **Linear‑Time Possibility**  
   With a two‑pass approach, the solution runs in `O(n)`, meeting the constraints (`n ≤ 2000`) easily.  

4. **Hands‑On Practice**  
   It forces you to think about *prefix sums* and *cumulative operations*, concepts that appear in many interviews (e.g., "Minimum Swaps to Group All 1's Together", "Minimum Operations to Reduce a Number").

5. **Cross‑Language Relevance**  
   The algorithm is language‑agnostic. You can demonstrate it in Java, Python, C++, or even JavaScript – a great way to show versatility.

---

### 4.3 The Bad – Common Pitfalls and Misunderstandings

| Pitfall | What Happens | Fix |
|---------|--------------|-----|
| **Treating “move one ball” as “teleportation”** | Incorrectly summing distances without accounting for incremental cost. | Use prefix sums to accumulate *distance* per ball, not just count. |
| **O(n²) Simulation** | Moving each ball individually to every target leads to 2 million operations for `n=2000`. | Recognize that distances add linearly; use two passes instead. |
| **Off‑by‑One Errors** | Mistaking 0‑based vs 1‑based indices when computing left/right counts. | Keep a running counter that starts at 0 and updates **after** processing the current index. |
| **Space Mis‑estimation** | Using an extra array per pass (leftCnt, leftOps) and forgetting to merge them. | Only keep the final answer array; intermediate counters are scalars. |

---

### 4.4 The Ugly – Edge Cases You Might Overlook

1. **All Zeros** – If no balls exist, every `answer[i]` should be `0`. The algorithm handles this gracefully because the counters stay at 0.

2. **Single Box** – With `n=1`, the result is `[0]`. The passes run once and add nothing.

3. **Maximum Length** – `n=2000` is still trivial for `O(n)` but you should test with random large strings to ensure no overflow (use `int` – the maximum sum is `2000 * 2000 = 4,000,000`, safely within 32‑bit).

4. **Non‑ASCII Characters** – The problem statement guarantees only `'0'` or `'1'`. Still, validating input defensively in production code is a good practice.

---

### 4.5 Final Code – Ready for Production

Below you’ll find the final, commented implementations in **Java**, **Python**, and **C++**. Each version:

- Runs in `O(n)` time, `O(1)` additional space.  
- Handles all edge cases.  
- Is self‑contained and ready to copy into an interview environment.

```java
// Java
public class Solution {
    public int[] minOperations(String boxes) {
        int n = boxes.length();
        int[] ans = new int[n];
        int leftCnt = 0, leftOps = 0;
        for (int i = 0; i < n; i++) {
            ans[i] = leftOps;
            if (boxes.charAt(i) == '1') leftCnt++;
            leftOps += leftCnt;
        }
        int rightCnt = 0, rightOps = 0;
        for (int i = n - 1; i >= 0; i--) {
            ans[i] += rightOps;
            if (boxes.charAt(i) == '1') rightCnt++;
            rightOps += rightCnt;
        }
        return ans;
    }
}
```

```python
# Python
def minOperations(boxes: str) -> list[int]:
    n = len(boxes)
    ans = [0] * n
    left_cnt = left_ops = 0
    for i, ch in enumerate(boxes):
        ans[i] = left_ops
        if ch == '1': left_cnt += 1
        left_ops += left_cnt
    right_cnt = right_ops = 0
    for i in range(n-1, -1, -1):
        ans[i] += right_ops
        if boxes[i] == '1': right_cnt += 1
        right_ops += right_cnt
    return ans
```

```cpp
// C++
class Solution {
public:
    vector<int> minOperations(const string& boxes) {
        int n = boxes.size();
        vector<int> ans(n, 0);
        int leftCnt = 0, leftOps = 0;
        for (int i = 0; i < n; ++i) {
            ans[i] = leftOps;
            if (boxes[i] == '1') ++leftCnt;
            leftOps += leftCnt;
        }
        int rightCnt = 0, rightOps = 0;
        for (int i = n - 1; i >= 0; --i) {
            ans[i] += rightOps;
            if (boxes[i] == '1') ++rightCnt;
            rightOps += rightCnt;
        }
        return ans;
    }
};
```

---

### 4.6 Takeaway

- **Explain the intuition** – prefix sums + two passes.  
- **Demonstrate the algorithm** – write clean, linear code in the language you’re comfortable with.  
- **Show confidence with edge cases** – mention “all zeros” or “single box” explicitly.

> *“When you turn a problem into a simple math trick, you’re not just solving the problem; you’re showing mastery.”*  
> Good luck – the boxes will be ready to accept your solution in seconds!

---

### 5. Closing Thoughts

Mastering problems like **1769** is more than just coding; it’s about *architecting* a solution that is:

- **Correct** – proven by theorems and lemmas.  
- **Efficient** – `O(n)` runs comfortably under any interview time limit.  
- **Elegant** – a two‑pass prefix‑sum strategy that can be written in less than 30 lines of code.  

By studying the good, acknowledging the bad, and preparing for the ugly, you can convert every interview into an opportunity to showcase both your algorithmic insight and your coding fluency. Happy coding!