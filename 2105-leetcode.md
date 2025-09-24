---
title: LeetCode 2105. Watering Plants II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣ Problem Recap – LeetCode 2105: *Watering Plants II*

> **Goal** – Count the minimum number of times Alice and Bob must refill their watering cans while watering a row of plants.  
> **Rules**  
> * Alice starts at index 0 and moves right, Bob starts at index *n‑1* and moves left.  
> * Both cans are initially full (`capacityA` and `capacityB`).  
> * If a person has enough water in the current can to *fully* water the plant they are about to water, they simply use it.  
> * Otherwise they *instantly* refill to full capacity and then water the plant.  
> * If both reach the same plant, the one with *more* water in their can waters it; if equal, Alice waters it.

**Constraints**

| | |
|---|---|
| `1 ≤ n ≤ 10⁵` | `1 ≤ plants[i] ≤ 10⁶` |
| `max(plants[i]) ≤ capacityA, capacityB ≤ 10⁹` | |

---

## 2️⃣ The Good, The Bad, & The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic Idea** | Two‑pointer simulation is *O(n)*, *O(1)* space. | Without the two‑pointer insight you might think of a DP or brute‑force which would be *O(n²)* or worse. | If you forget the “same plant” rule, you can end up mis‑counting refills and the solution fails on hidden tests. |
| **Edge Cases** | Odd‑length arrays, the middle plant, capacity equals plant water, large capacities. | Many interviewees ignore the “same plant” priority rule. | The “middle plant” logic can become a source of subtle bugs – e.g. forgetting to check which can has more water before deciding a refill. |
| **Implementation** | Clear variables: `left`, `right`, `currA`, `currB`, `refillA`, `refillB`. | Over‑complicating with extra functions or objects. | Using global mutable state or re‑computing current water after each refill inside loops (makes the code messy and error‑prone). |

> **Bottom line:** Keep the two‑pointer loop, handle the middle plant carefully, and write clean, commented code. That’s the “good.” The “bad” is to overthink the simulation or forget the priority rule. The “ugly” is messy state changes that make the logic unreadable.

---

## 3️⃣ Solution Walk‑Through

1. **Pointers**  
   * `left` starts at the leftmost plant (index 0).  
   * `right` starts at the rightmost plant (index *n‑1*).  
   They move toward each other until they meet or cross.

2. **Current Water & Refills**  
   * `currA` and `currB` hold the remaining water in Alice’s and Bob’s cans.  
   * `refillA` and `refillB` count the number of refills each person performed.

3. **Simulate a Step**  
   For each iteration where `left < right`:
   * **Alice** – If `plants[left] ≤ currA`, subtract it; otherwise increment `refillA` and set `currA = capacityA - plants[left]`.
   * **Bob** – Analogous logic using `right`.

   Then advance the pointers (`left++`, `right--`).

4. **Middle Plant**  
   If the array length is odd, after the loop `left == right`.  
   * The plant at `left` must be watered by the person who currently has more water (`currA` vs. `currB`).  
   * If that person lacks enough water, they must refill once more (increment their refill counter).

5. **Answer** – Return `refillA + refillB`.

---

## 4️⃣ Code Implementations

Below are ready‑to‑compile / run snippets in **Java**, **Python**, and **C++**.

---

### 📌 Java

```java
/**
 * LeetCode 2105 – Watering Plants II
 *
 * Two‑pointer simulation with O(n) time and O(1) space.
 */
class Solution {
    public int minimumRefill(int[] plants, int capacityA, int capacityB) {
        int left = 0;
        int right = plants.length - 1;
        int currA = capacityA, currB = capacityB;
        int refillA = 0, refillB = 0;

        while (left < right) {
            // Alice
            if (plants[left] > currA) {
                refillA++;
                currA = capacityA - plants[left];
            } else {
                currA -= plants[left];
            }
            left++;

            // Bob
            if (plants[right] > currB) {
                refillB++;
                currB = capacityB - plants[right];
            } else {
                currB -= plants[right];
            }
            right--;
        }

        // One plant left in the middle (odd length)
        if (left == right) {
            if (currA >= currB) {
                if (plants[left] > currA) {
                    refillA++;
                }
            } else {
                if (plants[left] > currB) {
                    refillB++;
                }
            }
        }

        return refillA + refillB;
    }
}
```

---

### 📌 Python

```python
"""
LeetCode 2105 – Watering Plants II
Time:  O(n)
Space: O(1)
"""

class Solution:
    def minimumRefill(self, plants: list[int], capacityA: int, capacityB: int) -> int:
        left, right = 0, len(plants) - 1
        currA, currB = capacityA, capacityB
        refillA = refillB = 0

        while left < right:
            # Alice
            if plants[left] > currA:
                refillA += 1
                currA = capacityA - plants[left]
            else:
                currA -= plants[left]
            left += 1

            # Bob
            if plants[right] > currB:
                refillB += 1
                currB = capacityB - plants[right]
            else:
                currB -= plants[right]
            right -= 1

        # Middle plant (odd length)
        if left == right:
            if currA >= currB:
                if plants[left] > currA:
                    refillA += 1
            else:
                if plants[left] > currB:
                    refillB += 1

        return refillA + refillB
```

---

### 📌 C++

```cpp
/**
 * LeetCode 2105 – Watering Plants II
 * Two‑pointer solution, O(n) time, O(1) space
 */
class Solution {
public:
    int minimumRefill(vector<int>& plants, int capacityA, int capacityB) {
        int left = 0, right = plants.size() - 1;
        int currA = capacityA, currB = capacityB;
        int refillA = 0, refillB = 0;

        while (left < right) {
            // Alice
            if (plants[left] > currA) {
                ++refillA;
                currA = capacityA - plants[left];
            } else {
                currA -= plants[left];
            }
            ++left;

            // Bob
            if (plants[right] > currB) {
                ++refillB;
                currB = capacityB - plants[right];
            } else {
                currB -= plants[right];
            }
            --right;
        }

        // One plant left (odd number of plants)
        if (left == right) {
            if (currA >= currB) {
                if (plants[left] > currA) ++refillA;
            } else {
                if (plants[left] > currB) ++refillB;
            }
        }

        return refillA + refillB;
    }
};
```

---

## 5️⃣ How to Use These Solutions in a Technical Interview

| Language | Where to Paste | What to Highlight |
|----------|----------------|-------------------|
| **Java** | `class Solution` in the LeetCode editor | Mention *two‑pointer* and *refill simulation* in the explanation |
| **Python** | `class Solution` with `def minimumRefill` | Show clean loop and the “middle‑plant” guard |
| **C++** | `class Solution` with `public:` | Emphasize constant space – interviewers love it |

**Tip:** During an interview, walk the interviewer through the loop on a small example (`[1, 2, 3, 2, 1]`) and point out the priority rule at the middle plant.

---

## 5️⃣ SEO‑Optimized Blog Post  
> *Title: The Good, The Bad, & The Ugly of Watering Plants II – Java, Python, & C++ LeetCode 2105 Solution*

> *Meta‑Description:* Master LeetCode 2105 “Watering Plants II” with a clean two‑pointer solution in Java, Python, and C++. Learn the pitfalls, edge‑case handling, and why this problem is a favorite in technical interviews.

---

### 🌟 **The Good** – Why this problem is a *Golden Ticket* for your interview prep

- **Conceptual clarity** – The “two‑pointer” pattern is a must‑know for algorithm interviews.  
- **Real‑world relevance** – It’s an excellent example of *state simulation* (like “robot vacuum” or “car parking” problems).  
- **Language agnostic** – You can solve it in **Java, Python, or C++**, proving versatility to hiring managers.

> **SEO keyword**: *two‑pointer technique LeetCode*, *Watering Plants II solution*.

---

### ⚠️ **The Bad** – Common traps

- **Forgetting the “same plant” priority** – leads to wrong answer on odd‑length inputs.  
- **Mixing up the pointers** – e.g., incrementing `right` before decrementing `currB`.  
- **Using extra memory** – e.g., creating a vector of refills that is never needed.

> **SEO keyword**: *Watering Plants II bugs*, *priority rule LeetCode 2105*.

---

### 👹 **The Ugly** – The messy, hard‑to‑debug implementation

- **Mutating many variables in a single line** – makes the logic unreadable.  
- **Recursion or DP** – `O(n²)` or `O(n log n)` solutions that TLE or MLE on large inputs.  
- **Ignoring integer overflow** – especially when `capacity` and `plants[i]` are both close to `10⁹`.

> **SEO keyword**: *Watering Plants II interview fail*, *time‑complexity LeetCode 2105*.

---

## 6️⃣ Final Checklist Before Submitting

1. **Read the prompt carefully** – especially the “same plant” rule.  
2. **Run through all edge cases**:  
   * Single plant.  
   * Odd vs. even length.  
   * Capacities larger than all plant waters.  
   * Capacities exactly equal to the plant’s water.  
3. **Verify with the sample cases** from LeetCode.  
4. **Add a few custom tests** (e.g., `plants=[10,10,10], capacityA=10, capacityB=10` → answer `2`).  
5. **Time your solution** – It should be O(n) on all languages; no nested loops or extra data structures.

---

## 🎯 Wrap‑up

* **Watering Plants II** is a *textbook* example of a two‑pointer simulation.  
* The key is handling the middle plant correctly.  
* Clean code in Java, Python, and C++ showcases your ability to translate a problem statement into production‑ready code.

Good luck on your next technical interview – this problem is a great “show‑off” item! 🚀