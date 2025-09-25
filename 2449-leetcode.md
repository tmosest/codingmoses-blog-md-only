---
title: LeetCode 2449. Minimum Number of Operations to Make Arrays Similar - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1.  Problem Recap

**LeetCode 2449 – Minimum Number of Operations to Make Arrays Similar**

```
Input
    nums   : int[]  (size n)
    target : int[]  (size n)

Operation
    Pick two different indices i, j
    nums[i] += 2
    nums[j] -= 2

Two arrays are *similar* when every value appears the same number of times in each array.

Goal
    Return the minimum number of operations needed to make `nums` similar to `target`.

Guarantee
    It is always possible to transform `nums` into a similar array.
```

The constraints (`n ≤ 10⁵`, values ≤ 10⁶) force us to think in **O(n log n)** or better.

---

## 2.  Key Insight – Parity Is Immutable

Adding or subtracting `2` never changes the **parity** (odd/even) of a number.  
Therefore:

| element | can move to | cannot move to |
|---------|-------------|----------------|
| odd     | another odd | even           |
| even    | another even| odd            |

So:

*The number of odd elements in `nums` must equal the number of odd elements in `target`, and the same for evens.*  
The problem guarantees this, so we can safely separate the two parity groups.

---

## 3.  Greedy Matching After Sorting

Once we split the two arrays into odd and even buckets, the task becomes:

> For each bucket, pair every element of `nums` with an element of `target` so that the total cost  
>  (number of operations) is minimized.

**Why sorting works**

If we sort both `nums_odd` and `target_odd`, the optimal pairing is simply `nums_odd[i] ↔ target_odd[i]`.  
This is a classic “minimum sum of absolute differences” problem where the sorted order gives the minimum.

The same logic applies to the even bucket.

---

## 4.  Computing the Cost

For a pair `(a, b)` (both odd or both even) we need to make `a` equal to `b` by repeatedly adding 2 to one and subtracting 2 from the other.

The difference `|a - b|` is always even (both numbers have the same parity).  
Each operation changes the difference by `4` (increase one by 2, decrease the other by 2).  
Thus the number of operations needed for this pair is

```
|a - b| / 2
```

Because each operation touches **two** indices, the sum over all pairs must be divided by `2` at the very end.

---

## 5.  Final Formula

```
total_moves = ( Σ |odd_i  - targetOdd_i| / 2
               + Σ |even_i - targetEven_i| / 2 ) / 2
```

All arithmetic fits in 64‑bit (`long` / `long long`) because  
`n * maxDiff / 2 ≤ 10⁵ * 10⁶ / 2 = 5·10¹⁰`, well below 2⁶³.

---

## 6.  Implementation

Below are clean, idiomatic implementations in **Java, Python, and C++**.  
All three share the same structure: split by parity → sort → accumulate absolute differences → divide by 2.

> **Tip for interviews** – write the split‑by‑parity part once, then simply reuse it for both odd and even groups.

---

### 6.1 Java

```java
import java.util.*;

class Solution {
    public long makeSimilar(int[] nums, int[] target) {
        List<Integer> oddNums = new ArrayList<>();
        List<Integer> evenNums = new ArrayList<>();
        List<Integer> oddTarget = new ArrayList<>();
        List<Integer> evenTarget = new ArrayList<>();

        for (int i = 0; i < nums.length; i++) {
            if ((nums[i] & 1) == 0) evenNums.add(nums[i]); else oddNums.add(nums[i]);
            if ((target[i] & 1) == 0) evenTarget.add(target[i]); else oddTarget.add(target[i]);
        }

        Collections.sort(oddNums);
        Collections.sort(evenNums);
        Collections.sort(oddTarget);
        Collections.sort(evenTarget);

        long moves = 0;
        for (int i = 0; i < oddNums.size(); i++)
            moves += Math.abs(oddNums.get(i) - oddTarget.get(i)) / 2;
        for (int i = 0; i < evenNums.size(); i++)
            moves += Math.abs(evenNums.get(i) - evenTarget.get(i)) / 2;

        return moves / 2;
    }
}
```

---

### 6.2 Python

```python
from typing import List

class Solution:
    def makeSimilar(self, nums: List[int], target: List[int]) -> int:
        odd_nums, even_nums = [], []
        odd_target, even_target = [], []

        for n, t in zip(nums, target):
            if n & 1:
                odd_nums.append(n)
            else:
                even_nums.append(n)
            if t & 1:
                odd_target.append(t)
            else:
                even_target.append(t)

        odd_nums.sort()
        even_nums.sort()
        odd_target.sort()
        even_target.sort()

        moves = 0
        moves += sum(abs(a - b) // 2 for a, b in zip(odd_nums, odd_target))
        moves += sum(abs(a - b) // 2 for a, b in zip(even_nums, even_target))

        return moves // 2
```

---

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long makeSimilar(vector<int>& nums, vector<int>& target) {
        vector<int> oddNum, evenNum, oddTar, evenTar;
        for (size_t i = 0; i < nums.size(); ++i) {
            if (nums[i] & 1) oddNum.push_back(nums[i]); else evenNum.push_back(nums[i]);
            if (target[i] & 1) oddTar.push_back(target[i]); else evenTar.push_back(target[i]);
        }

        sort(oddNum.begin(), oddNum.end());
        sort(evenNum.begin(), evenNum.end());
        sort(oddTar.begin(), oddTar.end());
        sort(evenTar.begin(), evenTar.end());

        long long moves = 0;
        for (size_t i = 0; i < oddNum.size(); ++i)
            moves += llabs(oddNum[i] - oddTar[i]) / 2;
        for (size_t i = 0; i < evenNum.size(); ++i)
            moves += llabs(evenNum[i] - evenTar[i]) / 2;

        return moves / 2;
    }
};
```

---

## 7.  Complexity Analysis

| Operation | Time | Memory |
|-----------|------|--------|
| Splitting by parity | **O(n)** | **O(n)** (four auxiliary lists) |
| Sorting each list | **O(n log n)** (sum of four sorts, but overall O(n log n)) | **O(n)** |
| Summation | **O(n)** | — |
| **Total** | **O(n log n)** | **O(n)** |

This meets the constraints comfortably.

---

## 8.  Edge Cases & “The Ugly”

| Situation | Why it’s tricky | Fix |
|-----------|-----------------|-----|
| All elements already similar | You must return `0` – the algorithm naturally gives `0`. | No special handling needed. |
| Extremely unbalanced parity counts | The problem guarantees feasibility, so counts always match. | Trust the guarantee – otherwise you’d have to return `-1` or throw. |
| Large numbers (close to 10⁶) | `int` in Java/C++ is fine; Python uses arbitrary precision. | Use 64‑bit (`long`/`long long`) to avoid overflow in sum. |
| `n = 1` | Only one element, operation count must be `0`. | Works because both lists have length 1, difference divided by 2, then halved → `0`. |

---

## 9.  Alternate Approaches (Why We Avoided Them)

1. **Greedy without sorting**  
   - Pairing in arbitrary order can lead to suboptimal costs.  
   - Sorting guarantees minimal sum of absolute differences.

2. **Two‑pointer on sorted lists**  
   - Similar to sorting + linear scan; our solution already does this implicitly.

3. **Multiset / Frequency map**  
   - You could track how many of each number you need, but the cost of an operation depends on the numeric difference, not just counts.  
   - Hence a simple frequency map misses the “move magnitude” aspect.

---

## 10.  Blog Article – “The Good, The Bad, and The Ugly”  

> **SEO Keywords**: `leetcode 2449`, `array transformation`, `parity greedy`, `minimum operations`, `algorithm interview`, `Java Python C++ solution`, `job interview coding`

---

### 10.1 Introduction  

In today's competitive hiring landscape, coding interview questions are often the first gatekeepers.  
LeetCode 2449, *Minimum Number of Operations to Make Arrays Similar*, is a seemingly simple problem that actually reveals a lot about your algorithmic thinking.

This article walks through the **good**, **bad**, and **ugly** aspects of the solution, explains why sorting by parity is the key, and shows how to implement it in **Java, Python, and C++**.  

By the end, you'll not only have a clean reference implementation but also a narrative you can confidently discuss in an interview.

---

### 10.2 The Good – A Clean, O(n log n) Solution

* **Parity invariance** – A single observation: adding or subtracting 2 preserves odd/even.  
  This collapses a 2‑dimensional problem into two 1‑dimensional problems.

* **Greedy sorting** – Once split, the optimal pairing is achieved by aligning sorted arrays.  
  This is a classic algorithmic trick: *sort, pair by index, compute cost*.

* **Simplicity in code** – Three almost identical functions in three languages that are easy to read, test, and explain.

---

### 10.3 The Bad – Potential Pitfalls

1. **Misunderstanding parity** – Many candidates mistakenly think an element can hop across parities.  
   Emphasizing this during the conversation shows you understand the constraints.

2. **Over‑engineering** – Trying to use complex data structures (priority queues, maps) obscures the real cost of an operation: the numeric difference, not just the counts.

3. **Ignoring overflow** – Summing up to 5×10¹⁰ operations with 32‑bit integers overflows in Java/C++.  
   Use 64‑bit types and explain the reasoning.

---

### 10.4 The Ugly – Hidden Subtleties

* **Why sorting works** – Pairing elements arbitrarily can lead to an exponentially larger operation count.  
  For example, matching a very large odd number with a tiny odd number while ignoring a closer partner results in unnecessary moves.

* **Dividing by two** – Each operation touches *two* indices, so the naive sum over pairs is *twice* the real answer.  
  Forgetting the final division leads to a common off‑by‑two bug.

* **Edge cases** – Although the problem guarantees feasibility, handling `n = 1` or already‑similar arrays gracefully is still vital.  
  A well‑structured loop automatically returns 0, but you should mention it in your explanation.

---

### 10.5 The Ugly – When It Goes Wrong

Imagine you try to solve the problem by building a frequency map of `nums` and `target`.  
You’ll correctly notice the parity counts match, but you’ll still be stuck:  

> “I need to increase 7 to 11, but I don't know how many operations it really takes.”

The cost depends on the *numeric distance*, not just on the presence of a value.  
Missing this nuance can lead to a 100‑line brute force simulation that times out on the test set.

---

### 10.6 Talking About the Solution in an Interview  

1. **State the parity invariance** – This shows you can spot invariants.  
2. **Explain the two‑bucket strategy** – “Odd can only move to odd, even to even.”  
3. **Justify sorting** – “Sorting aligns the smallest differences together, yielding the minimum sum of absolute differences.”  
4. **Show the math** – “For a pair `(a,b)` the cost is |a‑b|/2; each operation touches two indices, so we divide the total by 2.”  
5. **Mention complexity** – “O(n log n) time, O(n) space, comfortably within the constraints.”  
6. **Highlight language‑specific tips** – Java’s `ArrayList`, Python’s list comprehensions, C++’s `llabs`.

If you can articulate these steps in 5–7 minutes, you’ll impress any interviewer.

---

### 10.7 Conclusion  

LeetCode 2449 is a perfect showcase of how a **simple invariant** (parity) turns a complex array transformation into a tidy greedy algorithm.  
The **good** is a clean, test‑ready implementation.  
The **bad** is the temptation to over‑complicate.  
The **ugly** is the subtle pitfall of miscounting operations due to parity mixing.

Bring the three reference codes to your next interview, explain the core idea in your own words, and you’ll stand out as a candidate who can **turn insight into elegance**.

---  

> **Author**: *[Your Name]* – Full‑stack engineer & interview coach  
> **Follow** on LinkedIn or GitHub for more algorithm walkthroughs.

---

## 11.  Closing Thought

Parity is often the hidden gatekeeper in array problems.  
Once you notice it, a lot of seemingly hard questions shrink into manageable sub‑problems.  

Feel free to adapt the implementations above to your own coding style and bring them up confidently during your next interview. Happy coding! 🚀

---


# 2449.  Minimum Number of Operations to Make Arrays Similar
## Problem Statement
Given two integer arrays, `nums` and `target`, both of size `n`, find the minimum number of operations required to make `nums` equal to `target`. In each operation, you can either:

1. Choose a position `i` in `nums` and add 2 to the **original** value at that position.
2. Choose a position `i` in `target` 1.
The problem states that there’s a **reversible** operation which we just have a **invariant**  to **count** **n**-1/?? This problem is from 2449.
We must find minimum number of **s**.. **?**


We are asked to solve Leetcode problem 2449 - Minimum number of operations to make arrays similar. We need to provide a solution. The conversation: user posted a problem statement but with some errors, but we need to give a full answer. The conversation also had a solution explanation: parity invariance, split odd/even, sort, compute cost. We need to produce a final answer that covers that.

We need to produce a solution description and perhaps code. We can produce final answer as a thorough explanation with code in Java, Python, C++.

Given the "analysis" above, we can produce a final answer summarizing the problem, key insight, algorithm, complexity, and provide implementations.

Thus answer: Problem: Given two arrays of integers, each operation increments one element by 2 and decrements another by 2, find minimum operations to transform nums into target.

Key insight: parity invariant. Separate odd/even. Sort each group. Pair by sorted indices, compute total abs difference, divide by 2 each pair, sum and divide by 2. Complexity O(n log n).

We can provide code. Also note that this is LeetCode 2449.

We can produce final answer accordingly. Let's produce a final answer summarizing the solution, with algorithm steps and code snippets in Java, Python, C++.

We'll also mention the explanation.

Let's answer.

We'll produce a final answer:

**Solution**:

- Separate odd/even numbers from nums and target.
- Sort each list.
- For each group, sum abs difference / 2.
- Divide total sum by 2.

Complexities: O(n log n) time, O(n) space.

Provide Java, Python, C++ implementations.

We'll give them.

Ok. Let's produce.**LeetCode 2449 – Minimum Number of Operations to Make Arrays Similar**  

---

### Problem (short)

For two integer arrays `nums` and `target` of the same length `n` ( ≤ 10⁵ ), one operation is

* pick an index `i`
* add 2 to the value at `nums[i]` **or** at `target[i]`
* subtract 2 from the other value

The two arrays must become equal.  
The task is to output the *minimum* number of operations.  
(Values are ≤ 10⁶, the test set guarantees that a solution exists.)

---

### Key observation – parity never changes

Adding or subtracting 2 never changes a number’s odd/even nature.  
So

```
odd numbers can only become odd,
even numbers can only become even.
```

Therefore the count of odd numbers in `nums` equals the count of odd numbers in `target`, and the same for evens (the statement guarantees this).

---

### From parity to two independent problems

Split each array into

* `numsOdd`, `targetOdd`
* `numsEven`, `targetEven`

Now the problem is: for each bucket, pair each element of `nums` with one element of `target` so that the total number of operations is minimal.

---

### Why sorting gives the optimum pairing

For any two lists `A` and `B` of the same size, the minimum of  

```
Σ |A_i – B_{π(i)}|
```

over all permutations π is achieved when both lists are sorted and paired by index (`A[i] ↔ B[i]`).  
This is a classic “minimum sum of absolute differences” lemma.

Thus we sort the four buckets and pair by index.

---

### Cost of one pair

For a pair `(a, b)` with the same parity, `|a – b|` is even.  
Each operation changes the difference by 4 ( +2 on one side, –2 on the other ), therefore the number of operations needed for this pair is

```
|a – b| / 2
```

Because one operation touches two indices, after summing over all pairs we divide the total by 2.

---

### Final formula

```
total = ( Σ|odd_i  – targetOdd_i| / 2
          + Σ|even_i – targetEven_i| / 2 ) / 2
```

All arithmetic fits in 64‑bit.

---

### Complexity

| Step            | Time            | Space |
|-----------------|-----------------|-------|
| split by parity | O(n)            | O(n)  |
| sort each list  | O(n log n)      | O(n)  |
| sum differences | O(n)            | –     |
| **Total**       | **O(n log n)**  | **O(n)** |

Satisfies the limits easily.

---

## Reference Implementations

### Java

```java
class Solution {
    public long minimumOperations(int[] nums, int[] target) {
        ArrayList<Integer> oddA = new ArrayList<>();
        ArrayList<Integer> oddB = new ArrayList<>();
        ArrayList<Integer> evenA = new ArrayList<>();
        ArrayList<Integer> evenB = new ArrayList<>();

        for (int i = 0; i < nums.length; i++) {
            if ((nums[i] & 1) == 0) {
                evenA.add(nums[i]);
                evenB.add(target[i]);
            } else {
                oddA.add(nums[i]);
                oddB.add(target[i]);
            }
        }

        Collections.sort(oddA);
        Collections.sort(oddB);
        Collections.sort(evenA);
        Collections.sort(evenB);

        long diff = 0;
        for (int i = 0; i < oddA.size(); i++) {
            diff += Math.abs(oddA.get(i) - oddB.get(i));
        }
        for (int i = 0; i < evenA.size(); i++) {
            diff += Math.abs(evenA.get(i) - evenB.get(i));
        }
        // each pair costs |a-b|/2, each operation touches two indices
        return diff / 4;
    }
}
```

### Python

```python
class Solution:
    def minimumOperations(self, nums: List[int], target: List[int]) -> int:
        odd_a, odd_b, even_a, even_b = [], [], [], []

        for a, b in zip(nums, target):
            if a & 1:
                odd_a.append(a); odd_b.append(b)
            else:
                even_a.append(a); even_b.append(b)

        odd_a.sort(); odd_b.sort()
        even_a.sort(); even_b.sort()

        diff = sum(abs(a - b) for a, b in zip(odd_a, odd_b))
        diff += sum(abs(a - b) for a, b in zip(even_a, even_b))

        return diff // 4          # two halves of the division
```

### C++

```cpp
class Solution {
public:
    long long minimumOperations(vector<int>& nums, vector<int>& target) {
        vector<int> oddA, oddB, evenA, evenB;
        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i] & 1) { oddA.push_back(nums[i]); oddB.push_back(target[i]); }
            else             { evenA.push_back(nums[i]); evenB.push_back(target[i]); }
        }

        sort(oddA.begin(), oddA.end());
        sort(oddB.begin(), oddB.end());
        sort(evenA.begin(), evenA.end());
        sort(evenB.begin(), evenB.end());

        long long diff = 0;
        for (int i = 0; i < oddA.size(); ++i) diff += llabs(oddA[i] - oddB[i]);
        for (int i = 0; i < evenA.size(); ++i) diff += llabs(evenA[i] - evenB[i]);

        return diff / 4;   // /2 for pair cost + /2 for touching two indices
    }
};
```

---

**Takeaway**  
The minimum number of operations is found by exploiting the invariant that parity never changes, splitting into odd/even groups, sorting each group, pairing by index, summing the absolute differences (divided by two), and finally dividing the total by two.  
This gives an `O(n log n)` solution that easily passes the constraints.