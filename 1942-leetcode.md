---
title: LeetCode 1942. The Number of the Smallest Unoccupied Chair - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 1942 – “The Number of the Smallest Unoccupied Chair”
> *How to win an interview question in under 5 minutes.*

> **Keywords:** LeetCode 1942, smallest unoccupied chair, priority queue, heap, Java, Python, C++, algorithm, interview, software engineer, coding interview, problem solving.

---

## 1️⃣ Problem Overview

At a party there are *infinite* chairs numbered 0…∞.  
When a friend arrives, they always sit on the **smallest un‑occupied** chair.  
When a friend leaves, that chair becomes free immediately, and a new arrival at the *same* time can use it.

You are given `times[i] = [arrival_i, leave_i]` for `n` friends (`1 ≤ arrival_i < leave_i ≤ 10⁵`).  
All arrival times are distinct.  
`targetFriend` is the index of the friend whose chair you must return.

> **Return** the chair number that `targetFriend` will occupy.

---

## 2️⃣ Intuition

The natural data‑structures for this problem are **priority queues** (heaps):

| Purpose | What we store | How it works |
|---------|---------------|--------------|
| `availableSeats` | chair numbers | always gives the smallest free chair |
| `occupiedSeats` | `(leaveTime, chair)` | always gives the next chair to be freed |

We process friends **in order of arrival time**.  
Before seating a friend we “free” every chair whose `leaveTime ≤ currentArrival`.  
Then we seat the friend on the smallest free chair.

This is the classic “two‑heap” solution that runs in **O(n log n)** time and **O(n)** space.

---

## 3️⃣ Solution Code

### 3.1 Python (9‑line clean implementation)

```python
import heapq
from typing import List

class Solution:
    def smallestChair(self, times: List[List[int]], targetFriend: int) -> int:
        # Order friends by arrival time
        order = sorted(range(len(times)), key=lambda i: times[i][0])

        # All chairs 0…n-1 are initially free
        available = list(range(len(times)))
        heapq.heapify(available)

        # Min‑heap of (leaveTime, chair)
        occupied = []

        for i in order:
            arrival, leave = times[i]

            # Free all chairs that become available before this arrival
            while occupied and occupied[0][0] <= arrival:
                _, chair = heapq.heappop(occupied)
                heapq.heappush(available, chair)

            # Seat the friend
            chair = heapq.heappop(available)
            if i == targetFriend:
                return chair

            # Mark chair as occupied until 'leave'
            heapq.heappush(occupied, (leave, chair))
```

### 3.2 Java (PriorityQueue version)

```java
import java.util.*;

class Solution {
    public int smallestChair(int[][] times, int targetFriend) {
        int n = times.length;
        // Order friends by arrival time
        Integer[] order = new Integer[n];
        for (int i = 0; i < n; i++) order[i] = i;
        Arrays.sort(order, Comparator.comparingInt(i -> times[i][0]));

        // Available chairs – min‑heap
        PriorityQueue<Integer> free = new PriorityQueue<>();
        for (int i = 0; i < n; i++) free.offer(i);

        // Occupied chairs – (leaveTime, chair)
        PriorityQueue<int[]> occupied = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));

        for (int idx : order) {
            int arrive = times[idx][0];
            int leave  = times[idx][1];

            // Release chairs whose leaveTime <= arrive
            while (!occupied.isEmpty() && occupied.peek()[0] <= arrive) {
                free.offer(occupied.poll()[1]);
            }

            int chair = free.poll();              // smallest free chair
            if (idx == targetFriend) return chair;

            occupied.offer(new int[]{leave, chair});
        }
        return -1; // unreachable for valid input
    }
}
```

### 3.3 C++ (priority_queue version)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int smallestChair(vector<vector<int>>& times, int targetFriend) {
        int n = times.size();

        // Order friends by arrival time
        vector<int> order(n);
        iota(order.begin(), order.end(), 0);
        sort(order.begin(), order.end(),
             [&](int a, int b){ return times[a][0] < times[b][0]; });

        // Available chairs – min-heap
        priority_queue<int, vector<int>, greater<int>> free;
        for (int i = 0; i < n; ++i) free.push(i);

        // Occupied chairs – (leaveTime, chair)
        priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> occupied;

        for (int idx : order) {
            int arrive = times[idx][0];
            int leave  = times[idx][1];

            // Free chairs whose leaveTime <= arrive
            while (!occupied.empty() && occupied.top().first <= arrive) {
                free.push(occupied.top().second);
                occupied.pop();
            }

            int chair = free.top(); free.pop();
            if (idx == targetFriend) return chair;

            occupied.emplace(leave, chair);
        }
        return -1; // never reached
    }
};
```

All three solutions run in **O(n log n)** time and use **O(n)** extra memory.

---

## 4️⃣ The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time complexity** | Optimal for the constraints (`n ≤ 10⁴`) | Still `O(n log n)` even if we only care about a single friend | There are slower O(n²) greedy approaches that will TLE |
| **Space complexity** | Linear (`O(n)`) | Still needs two heaps | Avoids storing all arrival times – we only keep `n` chairs |
| **Corner case** | Handles simultaneous leave & arrive correctly because we free first | Forgetting to free before seating may give wrong chair | If we used a single heap for both available & occupied, we would break the rule |
| **Readability** | Short, clear code | Requires understanding of heaps and tuple handling | Overly complex if you mix data structures together |
| **Language support** | Python `heapq`, Java `PriorityQueue`, C++ `priority_queue` | All three have built‑in min‑heap support | No built‑in “min‑heap” in some older languages (C needs custom comparator) |

---

## 5️⃣ Why This Approach Rocks for Interviews

1. **Clear algorithmic idea** – “free before seat” is easy to explain.  
2. **Scales** – `O(n log n)` is acceptable for 10⁴ elements; interviewers will be impressed.  
3. **Data‑structure showcase** – you demonstrate knowledge of priority queues, heaps, and custom comparators.  
4. **Edge‑case coverage** – simultaneous events are a common pitfall; you handle them explicitly.  
5. **Language agnostic** – you can swap Java ↔️ C++ ↔️ Python without changing the core logic.

---

## 6️⃣ Variants & Extensions

- **Multiple parties** – run the algorithm for each test case.  
- **Dynamic arrival times** – keep an event queue (arrival & leave events) sorted by time.  
- **Fixed number of chairs** – replace the “available seats” heap with a simple integer counter.  
- **Random chair assignment** – switch to a max‑heap to give the largest free chair.

---

## 7️⃣ Take‑away & How to Land That Job

1. **Practice the 2‑heap pattern** – you’ll use it for seating, parking, scheduling, and more.  
2. **Explain the “free‑before‑seat” step** – interviewers love clear reasoning.  
3. **Write clean, commented code** – show maintainability.  
4. **Show the big‑O analysis** – interviewers ask for it; you’ll impress.  
5. **Mention real‑world analogies** – “parking lot, hotel rooms, CPU cores” – makes the problem relatable.

**Ready to ace your next interview?**  
Upload this solution to LeetCode, add a README explaining the approach, and share the link in your résumé or GitHub. Recruiters love candidates who can solve real problems and explain them!

---

## 8️⃣ Final Checklist

- ✅ Code compiles in Python 3.8+, Java 17, and C++17.  
- ✅ Handles all edge cases (simultaneous leave & arrival).  
- ✅ Meets time/space constraints.  
- ✅ Blog article is SEO‑friendly and covers interview‑relevant insights.

Good luck, and happy coding! 🚀