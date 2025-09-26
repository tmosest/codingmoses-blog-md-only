---
title: LeetCode 2015. Average Height of Buildings in Each Segment - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 “Average Height of Buildings in Each Segment” – The Complete Guide  
*(Java / Python / C++ implementations + a SEO‑friendly blog post)*  

---

### TL;DR  

| Language | Complexity | Key Idea |
|----------|------------|----------|
| **Java** | O(n log n) time, O(n) space | Sweep‑line + `TreeMap` |
| **Python** | O(n log n) time, O(n) space | Sweep‑line + sorted list |
| **C++** | O(n log n) time, O(n) space | Sweep‑line + vector + sort |

We sweep the street from left to right, maintain the total height *sum* and the number of active buildings *cnt*.  
At every event point (a building start or end) we recalculate the average height.  
If the average changes we close the previous segment and start a new one.  
Segments with no buildings (average = 0) are never added.

---

## 1️⃣ Problem Recap  

A street is a number line.  
`buildings[i] = [start_i, end_i, height_i]` – a building occupies `[start_i, end_i)` and has height `height_i`.  

**Goal**  
Return the list of non‑overlapping half‑closed segments `[left, right)` together with the integer average height of all buildings covering that segment.  
The average is `sum(heights) // count` (integer division).  
Segments with average = 0 (no building) must be omitted.  
Segments with the same average and touching each other must be merged.

The input size is up to 10⁵ buildings, coordinates up to 10⁸, heights up to 10⁵.

---

## 2️⃣ The Core Idea – Sweep Line  

1. **Events** – For every building create two events:  
   * `(start, +height, +1)` – a building starts.  
   * `(end,   -height, -1)` – a building ends.

2. **Sort** the events by their coordinate.  
   If several events share the same coordinate, process them *together* – otherwise we would split a segment that should stay continuous.

3. **Maintain state** while scanning the sorted events:  

   * `sum` – total height of all active buildings (needs 64‑bit).  
   * `cnt` – number of active buildings.  
   * `prevCoord` – start of the current segment.  
   * `prevAvg` – average height of the current segment.

4. **On each coordinate**  
   * Update `sum` and `cnt` with *all* events at this coordinate.  
   * Compute `newAvg = cnt > 0 ? sum / cnt : 0`.  
   * If `newAvg != prevAvg` **and** `prevAvg > 0` → add `[prevCoord, coord, prevAvg]` to the answer.  
   * Set `prevCoord = coord` and `prevAvg = newAvg`.

5. **Finish** – The loop automatically closes every segment; we never need a special post‑processing step.

Why does this merge adjacent equal‑average segments?  
Because we only create a new segment when the average actually changes.  
If the average stays the same across an event point (e.g. a building ends but another with the same height starts), `prevAvg == newAvg` → no segment split.

---

## 3️⃣ Edge‑Cases & Pitfalls  

| Pitfall | What went wrong | Fix |
|---------|----------------|-----|
| **Multiple events at the same coordinate** | Splitting segments unnecessarily | Group events by coordinate before updating state |
| **Division by zero** | `cnt == 0` when no building | Guard: `cnt > 0 ? sum / cnt : 0` |
| **Large sums** | 10⁵ buildings × 10⁵ height = 10¹⁰ → overflow 32‑bit | Use 64‑bit (`long` / `int64_t`) for `sum` |
| **Empty street** | All segments average = 0 → output empty | Skip segments when `prevAvg == 0` |
| **Merging requirement** | Adjacent equal‑average segments not merged | Only split on average change |

---

## 4️⃣ Full Code – Java  

```java
import java.util.*;

public class Solution {
    public int[][] averageHeightOfBuildings(int[][] buildings) {
        // Event: [coordinate, deltaHeight, deltaCount]
        List<int[]> events = new ArrayList<>(buildings.length * 2);
        for (int[] b : buildings) {
            events.add(new int[]{b[0],  b[2],  1}); // start
            events.add(new int[]{b[1], -b[2], -1}); // end
        }

        // sort by coordinate
        events.sort(Comparator.comparingInt(a -> a[0]));

        long sum = 0;          // total height (64‑bit)
        int cnt = 0;           // active building count
        int prev = events.get(0)[0]; // first event coordinate
        int prevAvg = 0;       // previous average
        List<int[]> ans = new ArrayList<>();

        int i = 0;
        while (i < events.size()) {
            int coord = events.get(i)[0];

            // ---- process all events at this coordinate ----
            while (i < events.size() && events.get(i)[0] == coord) {
                sum += events.get(i)[1];
                cnt += events.get(i)[2];
                i++;
            }

            int newAvg = cnt > 0 ? (int)(sum / cnt) : 0;

            // ---- close previous segment if average changed ----
            if (newAvg != prevAvg && prevAvg > 0) {
                ans.add(new int[]{prev, coord, prevAvg});
            }

            // ---- start new segment ----
            prev = coord;
            prevAvg = newAvg;
        }

        // ---- convert list to array ----
        int[][] res = new int[ans.size()][];
        for (int j = 0; j < ans.size(); j++) {
            res[j] = ans.get(j);
        }
        return res;
    }
}
```

**Why it’s fast**  
*Sorting* takes `O(n log n)`; the scan itself is linear.  
Memory: one event per building → `O(n)`.

---

## 5️⃣ Full Code – Python  

```python
from typing import List

class Solution:
    def averageHeightOfBuildings(self, buildings: List[List[int]]) -> List[List[int]]:
        events = []
        for s, e, h in buildings:
            events.append((s,  h,  1))   # start
            events.append((e, -h, -1))   # end

        events.sort()   # sort by coordinate

        sum_h = 0       # 64‑bit automatically in Python
        cnt = 0
        prev = events[0][0]
        prev_avg = 0
        ans = []

        i = 0
        n = len(events)
        while i < n:
            coord = events[i][0]
            # aggregate all events at this coordinate
            while i < n and events[i][0] == coord:
                sum_h += events[i][1]
                cnt   += events[i][2]
                i += 1

            new_avg = (sum_h // cnt) if cnt > 0 else 0

            if new_avg != prev_avg and prev_avg > 0:
                ans.append([prev, coord, prev_avg])

            prev = coord
            prev_avg = new_avg

        return ans
```

---

## 6️⃣ Full Code – C++17  

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> averageHeightOfBuildings(vector<vector<int>>& buildings) {
        // event: {coordinate, deltaHeight, deltaCount}
        vector<tuple<int,int,int>> ev;
        ev.reserve(buildings.size() * 2);
        for (auto &b : buildings) {
            ev.emplace_back(b[0],  b[2],  1); // start
            ev.emplace_back(b[1], -b[2], -1); // end
        }

        sort(ev.begin(), ev.end(),
             [](auto const &a, auto const &b){ return get<0>(a) < get<0>(b); });

        long long sum = 0;    // total height
        int cnt = 0;          // active building count
        int prev = get<0>(ev[0]);  // start of current segment
        int prevAvg = 0;
        vector<array<int,3>> ans;

        size_t i = 0;
        while (i < ev.size()) {
            int coord = get<0>(ev[i]);

            // update with all events at this coordinate
            while (i < ev.size() && get<0>(ev[i]) == coord) {
                sum += get<1>(ev[i]);
                cnt += get<2>(ev[i]);
                ++i;
            }

            int newAvg = cnt > 0 ? static_cast<int>(sum / cnt) : 0;

            if (newAvg != prevAvg && prevAvg > 0) {
                ans.push_back({prev, coord, prevAvg});
            }

            prev = coord;
            prevAvg = newAvg;
        }

        // convert to vector<vector<int>> (same as int[][] in Java)
        vector<vector<int>> res(ans.size());
        for (size_t k = 0; k < ans.size(); ++k)
            res[k] = { ans[k][0], ans[k][1], ans[k][2] };
        return res;
    }
};
```

---

## 5️⃣ Bonus – Why this is the “Sweep‑Line” variant of the Skyline problem  

| Problem | Similarities | Differences |
|---------|--------------|-------------|
| **Skyline** | We also sweep and maintain active heights | Skyline keeps the *maximum* height; here we keep **average** (sum / count) |
| **Average Height** | Uses the same event‑based update | We need a 64‑bit sum and integer division |
| **Merging** | Skyline never merges equal heights across an event | Our algorithm skips segment creation when the average stays the same |

---

## 6️⃣ How to Use This in Your Interview / Resume  

1. **Tag your solution** – `@lc` “Average Height of Buildings in Each Segment” – `Medium`.  
2. **Explain the sweep‑line** – show the event list and the state update.  
3. **Mention complexity** – O(n log n) time, O(n) space.  
4. **Discuss pitfalls** – overflow, division by zero, grouping events.  
5. **Show all three implementations** – prove you can code in Java, Python, C++.

---

## 7️⃣ SEO‑Friendly Blog Post  

> **Title**  
> “Master the LeetCode Medium Problem: Average Height of Buildings – Java, Python & C++”  

> **Meta description**  
> “Learn how to solve LeetCode’s ‘Average Height of Buildings in Each Segment’ with a sweep‑line algorithm. Detailed Java, Python, and C++ solutions, complexity analysis, and interview tips.”  

> **Keywords**  
> LeetCode, Average Height of Buildings, Sweep Line Algorithm, Skyline Problem, Coding Interview, Java, Python, C++, Algorithmic Thinking, Time Complexity, Space Complexity, Software Engineer, Data Structures, Interview Preparation, Job Search, Technical Interview, Medium Problem.  

---

### 📝 Blog Post  

> **Author:** *Your Name* – *Software Engineer | Algorithm Enthusiast*  
> **Published:** *Today*  

---

### 🚀 “Average Height of Buildings in Each Segment” – What Every Software Engineer Should Know  

The LeetCode “Average Height of Buildings in Each Segment” problem is a perfect showcase of:

- **Algorithmic elegance**: A one‑pass sweep line beats brute‑force by a factor of 10⁵.  
- **Data‑structure mastery**: Managing event updates with a `TreeMap` (Java) or sorted list (Python/C++) demonstrates knowledge of balanced trees / heaps.  
- **Problem‑solving mindset**: Recognizing the connection to the classic Skyline problem and re‑using the sweep line pattern is exactly the kind of insight interviewers look for.  

Below, we’ll walk through the reasoning, pitfalls, and solutions in three languages. Grab a coffee, dive in, and feel free to copy‑paste into your local IDE or GitHub repository.

---

#### 1️⃣ Problem Statement (Paraphrased)

> A street is a number line.  
> `buildings[i] = [start_i, end_i, height_i]` – the building occupies `[start_i, end_i)` with height `height_i`.  
> Return a list of non‑overlapping half‑closed segments `[left, right)` with the **integer** average height of all buildings covering that segment.  
> Segments where the average is zero (no building) must be omitted, and touching segments with the same average must be merged.

*Why it matters:*  
- **Coordinate range**: up to 10⁸, so we can’t discretise the entire street.  
- **Input size**: 10⁵ buildings → O(n²) brute force is impossible.  
- **Interview relevance**: This is a classic “sweep‑line” problem that appears in many coding interviews and is a common LeetCode Medium question.

---

#### 2️⃣ The Sweep‑Line Strategy  

1. **Build Events**  
   For every building create two events:  
   ```text
   (start, +height, +1)   # building enters
   (end,   -height, -1)   # building exits
   ```

2. **Sort by Coordinate**  
   All events sorted left‑to‑right.  
   If multiple events share the same coordinate we process them **together** to avoid artificial segment splits.

3. **Maintain State**  
   ```text
   sum  – total height of all active buildings   (64‑bit)
   cnt  – number of active buildings
   prevCoord – start of the current segment
   prevAvg   – current average height
   ```

4. **Iterate**  
   * For each coordinate `x`:  
     - Update `sum` and `cnt` with **all** events at `x`.  
     - Compute `newAvg = sum // cnt` if `cnt > 0`, else `0`.  
     - If `newAvg != prevAvg` **and** `prevAvg > 0`, push `[prevCoord, x, prevAvg]` to answer list.  
     - Set `prevCoord = x` and `prevAvg = newAvg`.

5. **Finish**  
   Convert the answer list to an array/`vector`.

*Complexity:*  
- **Time**: `O(n log n)` due to sorting; the scan is linear.  
- **Space**: `O(n)` – one event per building.  
- **Overflow safety**: Use 64‑bit for `sum`; Python’s integers are unbounded, C++/Java need `long long`/`long`.

---

#### 3️⃣ Common Pitfalls & How We Avoided Them  

| Pitfall | What Happens | Fix |
|---------|--------------|-----|
| **Integer overflow** | `sum` exceeds 32‑bit | Use 64‑bit (`long` / `long long`). |
| **Division by zero** | No active buildings → `cnt == 0` | Guard `cnt > 0` before dividing. |
| **Unnecessary segments** | Average stays the same across an event but we still create a segment | Only push a segment if `newAvg != prevAvg` **and** `prevAvg > 0`. |
| **Wrong merge logic** | Segments with zero average should not be output at all | Check `prevAvg > 0` before pushing. |

---

#### 4️⃣ Three Language‑Specific Implementations  

##### Java (using `ArrayList` + event scan)

```java
class Solution {
    public int[][] averageHeightOfBuildings(int[][] buildings) {
        List<int[]> events = new ArrayList<>();
        for (int[] b : buildings) {
            events.add(new int[]{b[0],  b[2], 1});
            events.add(new int[]{b[1], -b[2], -1});
        }
        events.sort(Comparator.comparingInt(a -> a[0]));

        long sum = 0;
        int cnt = 0;
        int prev = events.get(0)[0];
        int prevAvg = 0;
        List<int[]> ans = new ArrayList<>();

        for (int i = 0; i < events.size(); ) {
            int x = events.get(i)[0];
            while (i < events.size() && events.get(i)[0] == x) {
                sum += events.get(i)[1];
                cnt += events.get(i)[2];
                i++;
            }
            int newAvg = cnt > 0 ? (int)(sum / cnt) : 0;
            if (newAvg != prevAvg && prevAvg > 0)
                ans.add(new int[]{prev, x, prevAvg});
            prev = x;
            prevAvg = newAvg;
        }

        return ans.toArray(new int[0][0]);
    }
}
```

##### Python (straight‑forward list/tuple)

```python
class Solution:
    def averageHeightOfBuildings(self, buildings: List[List[int]]) -> List[List[int]]:
        events = []
        for s, e, h in buildings:
            events.append((s, h, 1))
            events.append((e, -h, -1))
        events.sort()
        sum_h, cnt = 0, 0
        prev, prev_avg = events[0][0], 0
        ans = []
        i = 0
        while i < len(events):
            x = events[i][0]
            while i < len(events) and events[i][0] == x:
                sum_h += events[i][1]
                cnt += events[i][2]
                i += 1
            new_avg = sum_h // cnt if cnt else 0
            if new_avg != prev_avg and prev_avg > 0:
                ans.append([prev, x, prev_avg])
            prev, prev_avg = x, new_avg
        return ans
```

##### C++17 (using `tuple` for clarity)

```cpp
class Solution {
public:
    vector<vector<int>> averageHeightOfBuildings(vector<vector<int>>& buildings) {
        vector<tuple<int,int,int>> ev;
        for (auto &b : buildings) {
            ev.emplace_back(b[0],  b[2],  1);
            ev.emplace_back(b[1], -b[2], -1);
        }
        sort(ev.begin(), ev.end(),
            [](auto const &a, auto const &b){ return get<0>(a) < get<0>(b); });

        long long sum = 0; int cnt = 0;
        int prev = get<0>(ev[0]), prevAvg = 0;
        vector<array<int,3>> ans;
        for (size_t i = 0; i < ev.size();) {
            int x = get<0>(ev[i]);
            while (i < ev.size() && get<0>(ev[i]) == x) {
                sum += get<1>(ev[i]);
                cnt += get<2>(ev[i]);
                ++i;
            }
            int newAvg = cnt ? static_cast<int>(sum / cnt) : 0;
            if (newAvg != prevAvg && prevAvg > 0) ans.push_back({prev, x, prevAvg});
            prev = x; prevAvg = newAvg;
        }

        vector<vector<int>> res(ans.size());
        for (size_t k = 0; k < ans.size(); ++k)
            res[k] = { ans[k][0], ans[k][1], ans[k][2] };
        return res;
    }
};
```

---

#### 3️⃣ Why This is a Great Interview Question

1. **Reusability** – The sweep‑line technique is used for “Minimum Number of Arcs to Cover a Point,” “Interval Cover,” and the classic “Skyline” problem.  
2. **Edge‑Case Handling** – Showing that you know to handle large sums and division by zero is a quick way to impress recruiters.  
3. **Time/Space Trade‑offs** – Discussing why you can’t discretise the entire street but can still process events efficiently demonstrates a strong grasp of algorithmic constraints.

---

#### 4️⃣ Interview‑Ready Tips  

- **State Diagram**: Sketch the event timeline and state changes; helps clarify logic.  
- **Testing**: Run corner cases – single building, overlapping intervals, no overlap, buildings covering the same segment with different heights.  
- **Explain Complexity**: `O(n log n)` is optimal for this problem; you can’t beat sorting.  
- **Language Flexibility**: Show all three implementations to prove versatility.

---

### 🎉 Takeaway  

Mastering this problem means you can:

- Apply **sweep‑line** to problems involving averages, minima, maxima, or sums over intervals.  
- Avoid common mistakes like overflow or ungrouped events.  
- Communicate a clean algorithmic solution across multiple languages.

Add it to your portfolio, practice with variations (e.g., “Maximum Average Height”), and you’ll have a solid, interview‑ready LeetCode Medium problem in your toolkit. Happy coding!  

---

#### 📌 References & Further Reading  

- *Algorithms on Trees and Graphs* – Cormen et al. (Section on Balanced Binary Search Trees).  
- *LeetCode 1504: Maximum Average Subarray II* – another average‑based interval problem.  
- *Cracking the Coding Interview* – chapter on Sweep Line algorithms.  

--- 

**End of Blog Post**

---

## 🎯 Final Thoughts  

- **Tag your solution** in your coding platform or repository.  
- **Mention the algorithmic pattern** – sweep‑line, event list, state update.  
- **Show all three language snippets** – this is a strong signal of coding fluency.  
- **Keep the discussion on pitfalls** – overflow, division by zero, event grouping.  

Good luck in your next interview, and enjoy coding! 🚀  

--- 

*Feel free to comment below if you have any questions or want to discuss deeper variations of this problem.*