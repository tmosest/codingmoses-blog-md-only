---
title: LeetCode 1942. The Number of the Smallest Unoccupied Chair - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ 1942. The Number of the Smallest Unoccupied Chair – Solution in **Java, Python & C++**

Below you’ll find a clean, production‑ready implementation of the classic LeetCode “Smallest Unoccupied Chair” problem in three languages.  
All solutions use **two priority queues (heaps)** – one for available chairs and one for occupied chairs sorted by departure time – giving an **O(n log n)** time and **O(n)** space complexity.

---

### Problem Recap

> You are at a party with **n** friends (0‑based indices).  
> There are infinitely many chairs numbered 0…∞.  
> When a friend arrives they take the **smallest** unoccupied chair.  
> When a friend leaves the chair becomes free immediately and can be taken by someone arriving at the same time.  
> `times[i] = [arrival_i, departure_i]` (arrival times are distinct).  
> Return the chair number that **targetFriend** will occupy.

---

## 1. Python 3 – 9 lines (two heaps)

```python
import heapq
from typing import List

class Solution:
    def smallestChair(self, times: List[List[int]], targetFriend: int) -> int:
        # 1️⃣ Sort friends by arrival time
        order = sorted(range(len(times)), key=lambda i: times[i][0])

        # 2️⃣ Heap of free chairs (initially 0…n-1), heap of occupied chairs (departure, seat)
        free, occupied = list(range(len(times))), []

        for idx in order:
            arrive, leave = times[idx]

            # 3️⃣ Release all chairs whose owner has left by the arrival time
            while occupied and occupied[0][0] <= arrive:
                heapq.heappush(free, heapq.heappop(occupied)[1])

            # 4️⃣ Grab the smallest free chair
            chair = heapq.heappop(free)

            if idx == targetFriend:               # 🎯 Found the answer
                return chair

            # 5️⃣ Mark chair as occupied until 'leave'
            heapq.heappush(occupied, (leave, chair))
```

**Why it works**  
- The `free` heap always contains all chairs that are currently empty.  
- The `occupied` heap is sorted by departure time; the top element is the chair that will free the soonest.  
- By repeatedly popping from `occupied` when its departure time ≤ current arrival, we keep `free` up‑to‑date.  
- The smallest free chair is always at the top of `free`, so we can assign it in O(log n).

---

## 2. Java – `PriorityQueue` version

```java
import java.util.*;

class Solution {
    public int smallestChair(int[][] times, int targetFriend) {
        int n = times.length;

        // 1️⃣ Sort friend indices by arrival time
        Integer[] order = new Integer[n];
        for (int i = 0; i < n; i++) order[i] = i;
        Arrays.sort(order, Comparator.comparingInt(i -> times[i][0]));

        // 2️⃣ Min‑heap of free chairs
        PriorityQueue<Integer> free = new PriorityQueue<>();
        for (int i = 0; i < n; i++) free.offer(i);

        // 3️⃣ Min‑heap of occupied chairs: pair(departure, seat)
        PriorityQueue<int[]> occupied = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));

        for (int idx : order) {
            int arrive = times[idx][0];
            int leave  = times[idx][1];

            // 4️⃣ Release chairs whose owner left
            while (!occupied.isEmpty() && occupied.peek()[0] <= arrive) {
                free.offer(occupied.poll()[1]);
            }

            // 5️⃣ Take the smallest free chair
            int chair = free.poll();

            if (idx == targetFriend) return chair;

            // 6️⃣ Mark chair as occupied until 'leave'
            occupied.offer(new int[]{leave, chair});
        }
        return -1;   // unreachable – input guarantees targetFriend exists
    }
}
```

---

## 3. C++ – `priority_queue` + `vector`

```cpp
#include <vector>
#include <queue>
#include <algorithm>

class Solution {
public:
    int smallestChair(std::vector<std::vector<int>>& times, int targetFriend) {
        int n = times.size();

        // 1️⃣ Order indices by arrival time
        std::vector<int> order(n);
        std::iota(order.begin(), order.end(), 0);
        std::sort(order.begin(), order.end(),
                  [&](int a, int b){ return times[a][0] < times[b][0]; });

        // 2️⃣ Min‑heap of free chairs
        std::priority_queue<int, std::vector<int>, std::greater<int>> free;
        for (int i = 0; i < n; ++i) free.push(i);

        // 3️⃣ Min‑heap of occupied chairs: pair(departure, seat)
        std::priority_queue<
            std::pair<int,int>, 
            std::vector<std::pair<int,int>>, 
            std::greater<std::pair<int,int>>
        > occupied;

        for (int idx : order) {
            int arrive = times[idx][0];
            int leave  = times[idx][1];

            // 4️⃣ Free all chairs whose owner left by now
            while (!occupied.empty() && occupied.top().first <= arrive) {
                free.push(occupied.top().second);
                occupied.pop();
            }

            // 5️⃣ Grab smallest free chair
            int chair = free.top(); free.pop();

            if (idx == targetFriend) return chair;

            // 6️⃣ Mark chair occupied until 'leave'
            occupied.emplace(leave, chair);
        }
        return -1;   // should never happen
    }
};
```

---

## 🎯 Why These Solutions Win Interviews

| Aspect | Python | Java | C++ |
|--------|--------|------|-----|
| **Readability** | 9 lines + comments | Compact, type‑safe | Concise, explicit data structures |
| **Time Complexity** | O(n log n) | O(n log n) | O(n log n) |
| **Space Complexity** | O(n) | O(n) | O(n) |
| **Key Insight** | Two priority queues: free & occupied | Two `PriorityQueue`s | Two `priority_queue`s with custom comparator |
| **Edge Cases Handled** | Releases at same timestamp, target friend at first or last | Same | Same |

---

## 🔎 SEO‑Optimized Blog Article Outline

> **Title**: “LeetCode 1942 – The Smallest Unoccupied Chair: A Deep Dive into Two‑Heap Solutions (Java, Python & C++)”

---

### 1️⃣ Introduction  
- Hook: “Imagine a party where every new guest must sit on the *smallest* empty chair. How do you keep track of the ever‑shifting chair lineup?”  
- State the problem, link to LeetCode, mention constraints (n ≤ 10⁴).  
- SEO keywords: *LeetCode 1942*, *Smallest Unoccupied Chair*, *Interview Question*, *Two Heaps*, *Priority Queue*, *Java*, *Python*, *C++*.

### 2️⃣ Problem Breakdown  
- Visual diagram of arrivals & departures.  
- Clarify “chair becomes free instantly” and “arrival times are distinct”.  
- Why a naive simulation (array of chairs) is O(n²) and impractical.

### 3️⃣ Naïve vs. Optimal Approaches  
| Approach | Complexity | Drawbacks | When to Use |
|----------|------------|-----------|-------------|
| Brute Force Array | O(n²) | Memory heavy, time out | Small n only |
| Sorted Events + TreeSet | O(n log n) | Slightly more code | When you’re comfortable with set operations |
| Two Heaps (recommended) | O(n log n) | Simpler logic | **Interview‑ready** |

### 4️⃣ The Two‑Heap Strategy (The Good)  
- **Free Chair Heap**: always holds the smallest available chair.  
- **Occupied Chair Heap**: sorted by departure time.  
- Release logic: pop from occupied while departure ≤ current arrival → push chair back into free.  
- Assignment: pop the top of free, push into occupied with its departure.  
- Stop early when the target friend is seated.  

#### Code Walk‑through (Python)  
- Step‑by‑step explanation with inline comments (the 9‑line solution).  
- Highlight the `while` loop that frees chairs.  
- Show how early exit saves work.

### 5️⃣ Translating to Java & C++ (The Ugly)  
- Discuss language‑specific quirks:  
  - Java `PriorityQueue` is a min‑heap by default? (No, it’s a min‑heap but needs comparator).  
  - C++ `priority_queue` is max‑by‑default → use `greater<...>` comparator.  
- Show how to store pairs (departure, chair) safely.  
- Common pitfalls: forgetting to `pop` after `peek`, off‑by‑one errors in indices.

### 6️⃣ Edge‑Case Checklist (The Bad)  
| Edge Case | What to Watch For |
|-----------|-------------------|
| Target friend arrives last | Ensure loop processes all arrivals. |
| Multiple departures at same timestamp | Release all before assigning. |
| Arrival time equals previous departure | Release first (<= arrival) so the chair can be reused. |
| Very large n (10⁴) | Two heaps keep memory O(n). |

### 7️⃣ Performance Tuning Tips  
- Use `heapq`’s `heappush`/`heappop` (Python) – no need for manual `push/pop`.  
- In Java, pre‑populate free heap with `IntStream.range(0, n).forEach(free::offer)` for readability.  
- In C++, use `std::vector<int>` for free heap if you want to avoid `push/pop` overhead for the first n elements.

### 8️⃣ Variations & Extensions  
- What if arrival times weren’t distinct? Add a tie‑break rule (e.g., friend ID).  
- How would you handle a finite number of chairs? Use a counter for “no available chair” case.  
- Use a segment tree or binary indexed tree for range minimum queries if you want to practice different data structures.

### 9️⃣ Conclusion & Final Takeaway  
- Summarize why the two‑heap solution is the *sweet spot* for LeetCode 1942.  
- Encourage readers to practice by implementing the three versions, test against random inputs (`unittest` / JUnit / Google Test).  
- Invite comments: “What other interview problems are you struggling with?”

### 🔗 Additional Resources  
- Link to LeetCode solution discussion threads.  
- GitHub repo with all three implementations.  
- Video walkthrough on YouTube for visual learners.

---

## 🚀 Final Thought

With these **three concise, efficient implementations** and the **structured interview‑ready explanation**, you’ll be able to confidently tackle LeetCode 1942 and impress interviewers with both speed and clarity. Happy coding! 🚀

--- 

> *Feel free to drop questions in the comments – let’s make every new guest feel right at home!*