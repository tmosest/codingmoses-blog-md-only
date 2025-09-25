---
title: LeetCode 1564. Put Boxes Into the Warehouse I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Put Boxes Into the Warehouse I – 1564  
**LeetCode Medium – 2024 Interview‑Ready Problem**

> *“You are given two arrays of positive integers, `boxes` and `warehouse`, representing the heights of some boxes of unit width and the heights of n rooms in a warehouse respectively. ... Return the maximum number of boxes you can put into the warehouse.”*  

---

## Why You’ll Need This on Your Next Interview

- **Array manipulation + greedy** – classic “two‑pointer” style.
- **Sorting** – you’ll learn why we sort the boxes instead of the warehouse.
- **Prefix minimum** – a neat trick to answer “can this room reach this box?”
- **Time complexity**: *O(m log m + n)* – perfect for 1 ≤ n, m ≤ 10⁵.

If you can explain this problem in under 5 minutes, you’ll have a strong talking‑point for *Data‑Structure & Algorithm* interviews at Google, Amazon, Microsoft, and other tech giants.

---

## 📌 Problem Recap (Simplified)

| Input | Description |
|-------|-------------|
| `boxes[]` | Heights of the boxes (any order) |
| `warehouse[]` | Heights of the rooms from left to right |

**Rules**

1. Boxes can’t be stacked.  
2. You can reorder the boxes before inserting.  
3. Boxes are pushed only from the left.  
4. If a box is taller than a room it reaches, it stops before that room and all following boxes are blocked.

**Goal** – Return the maximum number of boxes that can be inserted.



---

## 🧠 Key Observation

If you look at the warehouse from the *right*, each position `i` can be viewed as the **minimum height** of all rooms from the left up to that point.  
Why? Because a box that is taller than **any** room left of it will stop there, so the *lowest* room left of it is the real restriction.

So for each index `i` we need to know:

```
prefixMin[i] = min(warehouse[0], warehouse[1], … , warehouse[i])
```

A box of height `h` can be placed at room `i` iff `prefixMin[i] >= h`.

Once we have those minimums, the problem becomes a simple “fit the smallest boxes into the widest spots” greedy exercise.



---

## 🔧 Step‑by‑Step Algorithm

1. **Sort** `boxes` in **ascending** order.  
   *We always try to place the smallest remaining box – this is a classic greedy idea.*

2. **Build the prefix minimum array** for the warehouse:
   ```text
   pref[0] = warehouse[0]
   pref[i] = min(pref[i‑1], warehouse[i])   for i > 0
   ```

3. **Two‑pointer walk** from the **right** of the warehouse to the **left**:
   * `i` – current box index (starts at 0, the smallest box).  
   * `j` – current warehouse room index (starts at `n-1`).  

   While `i < boxes.length`:
   ```text
   if pref[j] >= boxes[i]   // this room can fit the current box
       count++, i++        // place the box, move to the next one
   j--                      // move to the previous room
   ```

4. Return `count`.

The algorithm uses only linear passes after sorting, so it’s efficient even for 10⁵ elements.



---

## 🚀 Code Implementations

### Java (LeetCode‑style)

```java
import java.util.Arrays;

class Solution {
    public int maxBoxesInWarehouse(int[] boxes, int[] warehouse) {
        Arrays.sort(boxes);                // Step 1

        int n = warehouse.length;
        int[] pref = new int[n];
        pref[0] = warehouse[0];            // Step 2
        for (int i = 1; i < n; i++) {
            pref[i] = Math.min(pref[i - 1], warehouse[i]);
        }

        int i = 0, count = 0;              // Step 3
        for (int j = n - 1; j >= 0 && i < boxes.length; j--) {
            if (pref[j] >= boxes[i]) {
                count++;
                i++;
            }
        }
        return count;
    }
}
```

---

### Python 3

```python
class Solution:
    def maxBoxesInWarehouse(self, boxes: list[int], warehouse: list[int]) -> int:
        boxes.sort()                      # Step 1

        pref = [0] * len(warehouse)
        pref[0] = warehouse[0]             # Step 2
        for i in range(1, len(warehouse)):
            pref[i] = min(pref[i - 1], warehouse[i])

        i = 0
        count = 0                          # Step 3
        for j in range(len(warehouse) - 1, -1, -1):
            if i >= len(boxes):
                break
            if pref[j] >= boxes[i]:
                count += 1
                i += 1

        return count
```

---

### C++ (GNU‑C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxBoxesInWarehouse(vector<int>& boxes, vector<int>& warehouse) {
        sort(boxes.begin(), boxes.end());                     // Step 1

        int n = warehouse.size();
        vector<int> pref(n);
        pref[0] = warehouse[0];                               // Step 2
        for (int i = 1; i < n; ++i)
            pref[i] = min(pref[i - 1], warehouse[i]);

        int i = 0, count = 0;                                 // Step 3
        for (int j = n - 1; j >= 0 && i < boxes.size(); --j) {
            if (pref[j] >= boxes[i]) {
                ++count;
                ++i;
            }
        }
        return count;
    }
};
```

All three solutions run in **O(m log m + n)** time and **O(n)** additional space.



---

## 🎯 Testing the Solution

| Test | boxes | warehouse | Expected | Explanation |
|------|-------|-----------|----------|-------------|
| 1 | [4,3,4,1] | [5,3,3,4,1] | 3 | Place 1→room 4, 3→room 1‑3, 4→room 0 |
| 2 | [1,2,2,3,4] | [3,4,1,2] | 3 | 1→room 3, 2→room 2, 3→room 1 (4 is impossible) |
| 3 | [1,2,3] | [1,2,3,4] | 1 | Only room 0 allows height 1 |
| 4 | [10,10] | [5,5,5,5] | 0 | No room tall enough |
| 5 | [1] | [100] | 1 | One small box fits everywhere |

Run the unit tests in your IDE or `pytest` for Python, or `g++` + `./a.out` for C++.



---

## 🔎 The Good, The Bad, and the Ugly

| Aspect | What’s Good | Potential Pitfalls | Ugly Real‑World Edge Cases |
|--------|-------------|---------------------|---------------------------|
| **Sorting boxes** | Ensures we always place the smallest remaining box → optimal. | Sorting can be expensive if `boxes` is huge and we sort many times. | If `boxes` has a massive number of equal heights, sorting is still fine but could use counting sort for 32‑bit ints. |
| **Prefix minimum** | One pass, constant time per index. | Forgetting that `pref[i]` must consider *all* left rooms; wrong logic leads to over‑count. | Warehouse with `1e9` heights; use 64‑bit integers to avoid overflow when computing min (not an issue in Java/Python but good practice in C++). |
| **Two‑pointer walk** | Linear, O(n+m). | Off‑by‑one errors: starting `j` at `n-1` and moving left; stopping when `i == boxes.size()`. | If the warehouse is empty – though constraints forbid it, defensive programming should handle it. |
| **Space** | Only O(n) additional, negligible. | The prefix array could be merged into the warehouse array (in‑place) to save memory. | For memory‑constrained embedded systems, use a rolling minimum instead of storing the whole prefix. |
| **Time** | Meets 1 s limits for 1e5 elements easily. | Mis‑implementing the inner loop (`j--` missing) can cause O(n²). | When boxes and warehouse arrays are both 10⁵ and each element is near 10⁹, ensure you use fast IO in C++ (e.g., `scanf`/`printf`). |

---

## 📚 Take‑Away Lessons for Your Interview

1. **Greedy + Sorting** – always try to fit the “smallest” or “cheapest” item first; it’s often optimal.
2. **Prefix/minimum pre‑computation** – transforms a dynamic constraint into a static array that can be queried in O(1).
3. **Two‑pointer strategy** – elegant for problems that involve pairing or matching between two sorted sequences.
4. **Edge‑case awareness** – think about extremes: very tall boxes, very short rooms, equal heights, etc.
5. **Explain your reasoning** – on an interview, walk through the example, state the algorithm, and discuss complexity before coding.



---

## 🎤 Blog‑Ready SEO‑Optimized Article

> **Title**  
> *“Master LeetCode 1564 – Put Boxes Into the Warehouse I: A Complete Java, Python, and C++ Guide”*  

### Meta Description  
Learn the greedy algorithm for LeetCode #1564. Get Java, Python, and C++ solutions, plus a deep dive into why the algorithm works, complexity analysis, and interview‑ready tips. Perfect for software engineering interview prep.

### Keywords  
LeetCode, Put Boxes Into the Warehouse, algorithm interview, greedy algorithm, two‑pointer, Java, Python, C++, sorting, prefix minimum, time complexity, coding interview, software engineer, technical interview prep.

### Article Outline

1. **Introduction** – Why this problem matters in interviews.  
2. **Problem Restatement** – Clear, concise.  
3. **Key Insight** – Prefix minimum trick.  
4. **Algorithm Walk‑through** – Step‑by‑step with pseudo‑code.  
5. **Code Implementations** – Java, Python, C++.  
6. **Complexity Analysis** – Big‑O.  
7. **Edge Cases & Testing** – Practical examples.  
8. **The Good, Bad, Ugly** – Interview tips.  
9. **Take‑Away** – What you should remember.  
10. **Call‑to‑Action** – “Try it yourself”, “Join the discussion”, etc.  

Each section includes code snippets, diagrams, and bullet points for readability. End with a **“Want more LeetCode solutions?”** banner linking to a newsletter or GitHub repository.

---

## 🚀 Final Thoughts

- The **prefix minimum + greedy** solution is not only correct but also clean and fast.  
- All three implementations share the same core logic, making it easy to adapt to any language you’re comfortable with.  
- Understanding the *why* behind the greedy choice (smallest boxes first) is critical for impressing interviewers.  

Feel free to paste the code into your favorite IDE, run the tests, and add your own enhancements. Happy coding and good luck on your next software engineering interview!