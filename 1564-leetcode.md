---
title: LeetCode 1564. Put Boxes Into the Warehouse I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Put Boxes Into the Warehouse I – The Complete “Good, Bad, Ugly” Guide  
*(LeetCode 1564 – Medium – Interview‑Ready Solution in Java, Python & C++)*  

> **Meta‑Description** – Want to ace LeetCode’s “Put Boxes Into the Warehouse I”? This post walks you through the intuition, a greedy O(n log n) solution, and fully‑tested code in Java, Python, and C++. Learn the *good*, *bad*, and *ugly* approaches, and boost your interview résumé with SEO‑optimized keywords: **LeetCode, greedy, sorting, warehouse, boxes, algorithm, interview**.  

---

### 1. Problem Overview  

You’re given two arrays:  

* `boxes` – heights of unit‑width boxes.  
* `warehouse` – heights of consecutive rooms (left to right).  

Rules  

1. Boxes can’t be stacked.  
2. You may reorder the boxes arbitrarily.  
3. Boxes are inserted from left to right.  
4. If a box is taller than a room, it and all boxes *behind* it are stopped before that room.  

**Goal** – return the maximum number of boxes that can be placed into the warehouse.

> **Examples**  
> 1. `boxes=[4,3,4,1]`, `warehouse=[5,3,3,4,1]` → `3`  
> 2. `boxes=[1,2,2,3,4]`, `warehouse=[3,4,1,2]` → `3`  
> 3. `boxes=[1,2,3]`, `warehouse=[1,2,3,4]` → `1`

---

### 2. Constraints  

* `1 ≤ boxes.length, warehouse.length ≤ 10⁵`  
* `1 ≤ boxes[i], warehouse[i] ≤ 10⁹`  

So a **O(n log n)** algorithm is required; anything worse will TLE.

---

### 3. The Naïve / “Bad” Approach  

A brute‑force idea: try every permutation of `boxes` and simulate the insertion.  
*Time Complexity:* `O(n! · n)` – utterly impossible for `n = 10⁵`.  
*Why it fails:* The state space explodes; there is no deterministic way to decide which box goes where without exploring all orders.

---

### 4. The “Good” Greedy Insight  

Think of the warehouse from **right to left**.  
While moving leftwards, the **minimum** room height seen so far determines how high a box can be in that segment.  

* Build an array `minPref[i]` = minimum of `warehouse[0…i]`.  
* Sort `boxes` ascending.  
* Start from the rightmost room (`i = warehouse.length‑1`) and from the smallest box (`j = 0`).  
* If `minPref[i] ≥ boxes[j]`, the box fits → place it, increment counters.  
* Move to the next room (i‑1) and next box (j+1).  
* Stop when boxes are exhausted or no more rooms.

This greedy works because:  
* We always try to fit the smallest possible box into the “most restrictive” remaining room (the one with the lowest allowable height).  
* If the smallest box doesn’t fit, no larger box will fit either, so we skip that room.

---

### 5. Algorithm (Pseudo Code)

```
sort boxes ascending
build minPref[0…m-1]  // m = warehouse.length
    minPref[0] = warehouse[0]
    for i = 1 … m-1
        minPref[i] = min(minPref[i-1], warehouse[i])

cnt = 0          // number of boxes placed
i = m-1          // current room (from right)
j = 0            // current box (from left)

while i >= 0 and j < boxes.length
    if minPref[i] >= boxes[j]
        cnt++
        j++            // place box
    i--                // move to previous room

return cnt
```

**Complexities**  

*Time* – `O(n log n)` for sorting + `O(n)` for prefix + `O(n)` two‑pointer scan.  
*Space* – `O(n)` for the prefix array (can be `O(1)` if you compute on the fly).

---

### 6. Edge‑Case Checklist  

| Case | Why it matters | Handling |
|------|----------------|----------|
| Empty `boxes` | No boxes to place | Return 0 immediately |
| Empty `warehouse` | No rooms to place into | Return 0 |
| All boxes taller than the first room | None can fit | Greedy will skip all rooms |
| All rooms lower than the smallest box | None fit | Same as above |

---

### 7. Full Code – Java, Python, C++  

#### 7.1 Java (Compatible with Java 17)

```java
import java.util.*;

public class Solution {
    public int maxBoxesInWarehouse(int[] boxes, int[] warehouse) {
        if (boxes == null || warehouse == null || boxes.length == 0 || warehouse.length == 0) {
            return 0;
        }

        // 1️⃣ Sort boxes ascending
        Arrays.sort(boxes);

        // 2️⃣ Build min prefix of warehouse heights
        int m = warehouse.length;
        int[] minPref = new int[m];
        minPref[0] = warehouse[0];
        for (int i = 1; i < m; i++) {
            minPref[i] = Math.min(minPref[i - 1], warehouse[i]);
        }

        // 3️⃣ Two‑pointer greedy scan
        int i = m - 1;          // room index (right to left)
        int j = 0;              // box index (smallest to largest)
        int count = 0;

        while (i >= 0 && j < boxes.length) {
            if (minPref[i] >= boxes[j]) {
                count++;   // place the box
                j++;        // next smallest box
            }
            i--;            // move to the previous room
        }
        return count;
    }
}
```

#### 7.2 Python 3

```python
from typing import List

class Solution:
    def maxBoxesInWarehouse(self, boxes: List[int], warehouse: List[int]) -> int:
        if not boxes or not warehouse:
            return 0

        # Sort boxes (ascending)
        boxes.sort()

        # Build min prefix of warehouse
        min_pref = [0] * len(warehouse)
        min_pref[0] = warehouse[0]
        for i in range(1, len(warehouse)):
            min_pref[i] = min(min_pref[i - 1], warehouse[i])

        i, j, count = len(warehouse) - 1, 0, 0
        while i >= 0 and j < len(boxes):
            if min_pref[i] >= boxes[j]:
                count += 1
                j += 1
            i -= 1

        return count
```

#### 7.3 C++ (C++17)

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int maxBoxesInWarehouse(std::vector<int>& boxes, std::vector<int>& warehouse) {
        if (boxes.empty() || warehouse.empty()) return 0;

        std::sort(boxes.begin(), boxes.end());          // ascending

        int m = warehouse.size();
        std::vector<int> minPref(m);
        minPref[0] = warehouse[0];
        for (int i = 1; i < m; ++i)
            minPref[i] = std::min(minPref[i - 1], warehouse[i]);

        int i = m - 1, j = 0, count = 0;
        while (i >= 0 && j < boxes.size()) {
            if (minPref[i] >= boxes[j]) {
                ++count;
                ++j;
            }
            --i;
        }
        return count;
    }
};
```

---

### 8. Testing the Implementation  

```python
# Example run in Python
sol = Solution()
print(sol.maxBoxesInWarehouse([4,3,4,1], [5,3,3,4,1]))  # → 3
print(sol.maxBoxesInWarehouse([1,2,2,3,4], [3,4,1,2]))  # → 3
print(sol.maxBoxesInWarehouse([1,2,3], [1,2,3,4]))       # → 1
```

Unit tests in Java/C++ follow the same pattern – instantiate `Solution`, call the method, and assert the expected output.

---

### 9. Interview‑Ready Tips  

| Topic | What Interviewers Look For | How This Solution Helps |
|-------|----------------------------|-------------------------|
| **Greedy Reasoning** | Ability to justify “take the smallest box that fits” | Demonstrates strong problem‑solving logic |
| **Complexity Analysis** | Explain `O(n log n)` sorting & `O(n)` scanning | Shows awareness of time/space limits |
| **Edge Cases** | Handling empty inputs, extreme heights | Avoids common pitfalls |
| **Coding Style** | Clean, readable, use of built‑in sorting | Conveys professionalism |
| **Language Adaptability** | Implement in Java/Python/C++ | Highlights versatility for multiple tech stacks |

---

### 10. Conclusion – Why This Code Rocks  

* **Simplicity** – The greedy logic is easy to explain, so you can articulate it on a whiteboard.  
* **Performance** – Meets LeetCode’s strict limits (`10⁵` elements) while remaining highly readable.  
* **Cross‑Language** – Works in Java, Python, and C++ – perfect for tailoring your resume to a tech stack you’re targeting.  

**Takeaway:** Mastering “Put Boxes Into the Warehouse I” showcases your ability to think greedy, sort data efficiently, and write clean, production‑ready code – all essential for landing that next software engineering role.  

Happy coding! 🚀

--- 

#### 📚 Further Reading  
* LeetCode Discuss – [Simple Java solution](https://leetcode.com/problems/put-boxes-into-the-warehouse-i/solutions/5789805/simple-java-solution-by-sakshikishore-cwrh/)  
* Algorithms & Data Structures – *Sorting & Two‑Pointer* chapter  
* Interview Preparation – *Greedy Algorithm* patterns

--- 

**Keywords** – LeetCode, Put Boxes Into the Warehouse, greedy algorithm, sorting, two pointers, Java, Python, C++, interview prep, algorithm interview, time complexity, space complexity.