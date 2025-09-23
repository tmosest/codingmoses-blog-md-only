---
title: LeetCode 2728. Count Houses in a Circular Street - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2728 – Count Houses in a Circular Street  
**LeetCode | Easy | O(k) time, O(1) space**  

> *“Your task is to count the number of houses in a circular street, but you can only interact with the street through a set of door‑related APIs.”*

---

### Why this problem matters  
- **Interview staple** – the problem tests your ability to design an algorithm that works with *limited state* and *implicit data structures* (here, the circular street).  
- **Coding style** – you must use the given interface, not an array, which forces you to think in *procedural steps* instead of “just loop over an array.”  
- **Optimization mindset** – you’re guaranteed that the number of houses ≤ k, so the solution must respect that bound.

---

## 1. Problem Restatement

| Term | Meaning |
|------|---------|
| `Street` | Object with the following API: |
| | • `openDoor()` – open the door in front of the current house. |
| | • `closeDoor()` – close it. |
| | • `isDoorOpen()` – returns `true` if the current door is open. |
| | • `moveRight()` / `moveLeft()` – move to the adjacent house in the respective direction (circular). |
| `k` | Upper bound on the number of houses. `1 ≤ n ≤ k ≤ 10³`. |

> Starting from an arbitrary house, you have to return `n`, the actual number of houses.

---

## 2. Intuition – “Use the doors as a marker”

1. **Clear every door** – walk `k` steps (the maximum possible number of houses) and close any open door you see.  
   After this loop, *all* doors are closed.

2. **Open the current door** – this marks the first house we’ve visited.

3. **Keep walking in the same direction** – after each move, if we encounter an open door, we’ve completed a full cycle.  
   The number of moves we made (including the first house) equals the number of houses.

The key observation: **doors can act as “visited” flags**. Because we know the street is circular and we’re guaranteed at most `k` houses, closing `k` doors guarantees that we’ve reset the state of every possible house.

---

## 3. Pseudocode

```
for i in 0 … k-1:
    street.closeDoor()
    street.moveLeft()

street.openDoor()          // first house
count = 1

while True:
    street.moveLeft()
    if street.isDoorOpen():
        break
    count += 1

return count
```

The algorithm is *O(k)* in time and *O(1)* in space.

---

## 4. Full Code

### Java

```java
/**
 * Definition for a street.
 * class Street {
 *     public Street(int[] doors);
 *     public void openDoor();
 *     public void closeDoor();
 *     public boolean isDoorOpen();
 *     public void moveRight();
 *     public void moveLeft();
 * }
 */
public class Solution {
    public int houseCount(Street street, int k) {
        // Step 1: reset every door
        for (int i = 0; i < k; i++) {
            street.closeDoor();
            street.moveLeft();
        }

        // Step 2: open the first house we encounter
        street.openDoor();
        int count = 1;

        // Step 3: walk until we hit the opened door again
        while (true) {
            street.moveLeft();
            if (street.isDoorOpen()) {
                break;                // a full loop is finished
            }
            count++;
        }

        return count;
    }
}
```

### Python

```python
# Definition for a street.
# class Street:
#     def __init__(self, doors: List[int]): ...
#     def openDoor(self) -> None: ...
#     def closeDoor(self) -> None: ...
#     def isDoorOpen(self) -> bool: ...
#     def moveRight(self) -> None: ...
#     def moveLeft(self) -> None: ...

class Solution:
    def houseCount(self, street: 'Street', k: int) -> int:
        # Step 1: close every possible door
        for _ in range(k):
            street.closeDoor()
            street.moveLeft()

        # Step 2: open the first house
        street.openDoor()
        count = 1

        # Step 3: walk until we hit the opened door again
        while True:
            street.moveLeft()
            if street.isDoorOpen():
                break
            count += 1

        return count
```

### C++

```cpp
/**
 * Definition for a street.
 * class Street {
 * public:
 *     Street(vector<int> doors);
 *     void openDoor();
 *     void closeDoor();
 *     bool isDoorOpen();
 *     void moveRight();
 *     void moveLeft();
 * };
 */
class Solution {
public:
    int houseCount(Street* street, int k) {
        // Step 1: reset all doors
        for (int i = 0; i < k; ++i) {
            street->closeDoor();
            street->moveLeft();
        }

        // Step 2: mark the first house
        street->openDoor();
        int count = 1;

        // Step 3: walk until we encounter the open door again
        while (true) {
            street->moveLeft();
            if (street->isDoorOpen())
                break;
            ++count;
        }

        return count;
    }
};
```

---

## 5. Complexity Analysis

| Metric | Calculation |
|--------|-------------|
| **Time** | `2k` operations at most (closing + opening phase). → **O(k)** |
| **Space** | Only a handful of integer variables. → **O(1)** |

With `k ≤ 10³`, the solution comfortably runs in < 1 ms on modern hardware.

---

## 6. Edge‑Case Checklist

| Case | Why it’s safe | How the algorithm behaves |
|------|---------------|---------------------------|
| All doors initially closed | No door is left open after the first loop. | First `openDoor()` still marks the first house. |
| All doors initially open | First loop closes every one. | Same as above. |
| `n = k` (maximum) | We walk exactly `k` steps; no extra moves. | Count will be `k`. |
| `n < k` | Some positions we never visit during the first loop. | Closing `k` doors still covers every real house. |
| Starting door is open | It’s closed in the first loop, then opened again later. | Correctly counted. |

---

## 7. Good, Bad & Ugly from an Interview Perspective

| Aspect | What’s Good | What’s Bad | What’s Ugly |
|--------|-------------|------------|-------------|
| **Good** | • Constant space; no extra data structure. <br>• Uses only the provided API. <br>• Works for *any* starting house. |  |  |
| **Bad** | • The “moveLeft” assumption might look arbitrary, but the direction can be either `right` or `left` as long as you stay consistent. | • If you mix directions in the first or second phase, you’ll never hit the opened door again. |  |
| **Ugly** | • A naive solution that iterates over an array would be *O(n)* but would not satisfy the “API‑only” constraint. <br>• Forgetting to open the first door before starting the loop will return 0 instead of `n`. |  |  |

**Interview Tip:**  
> Always **explain your direction choice** (left vs. right) and why you keep it consistent. It shows you understand the circular nature of the problem.

---

## 8. Interview Take‑aways

| Skill Tested | Why It’s Valuable |
|--------------|-------------------|
| *State‑ful iteration* | You can only read/write via side‑effects. |
| *Upper‑bound reasoning* | You use `k` to bound work instead of blindly iterating over an unknown array. |
| *Problem de‑composition* | Clearing all doors first, then using them as markers is a classic pattern. |
| *Coding against an interface* | A common real‑world scenario: you have a library or black‑box API and must build logic around it. |

If you’re preparing for coding interviews, this problem is a **must‑solve**. It blends algorithmic thinking with practical constraints.

---

## 9. SEO‑Friendly Summary

- **Keywords**: `Count Houses in a Circular Street`, `LeetCode 2728 solution`, `Java Python C++`, `circular street problem`, `interview algorithm`, `time complexity O(k)`, `space complexity O(1)`  
- **Title**: “LeetCode 2728 – Count Houses in a Circular Street (Java / Python / C++ Solution)”  
- **Meta Description**: “Solve LeetCode 2728 – Count Houses in a Circular Street – with clean Java, Python, and C++ code. Learn the O(k) algorithm, detailed explanation, and interview tips.”  

---

### Final Words

The “door‑as‑marker” trick is a neat, interview‑ready trick that keeps the state minimal while still enabling a complete traversal. Implementing it in **Java, Python, or C++** is straightforward once you understand the API and the circular nature of the street.  

Happy coding, and good luck on your next interview!