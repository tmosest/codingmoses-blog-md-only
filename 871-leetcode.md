---
title: LeetCode 871. Minimum Number of Refueling Stops - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 871 – Minimum Number of Refueling Stops  
**Hard** – `public int minRefuelStops(int target, int startFuel, int[][] stations)`

> A car travels from a starting position to a destination that is *target* miles east of the starting position.  
> Gas stations are given as `stations[i] = [positionᵢ, fuelᵢ]`.  
> The car starts with `startFuel` liters of gas and consumes **1 liter per mile**.  
> When it reaches a station it may take **all** the fuel from that station.  
> Return the minimum number of refueling stops needed to reach the destination, or `‑1` if it is impossible.

---

## Why This Problem Is a Good “Job‑Interview” Question

| Good | Bad | Ugly |
|------|-----|------|
| **Conceptual Depth** – It tests greedy + priority‑queue thinking, a classic interview pattern. | **Large Numbers** – `target` and `fuelᵢ` can be up to `10⁹`. Some naïve DP solutions overflow or time‑out. | **Edge‑Case Prone** – Forgetting that the destination can be reached *exactly* with 0 fuel or that a station may have 0 fuel left at arrival. |
| **Scalable** – `stations.length ≤ 500`, but the algorithm is O(n log n) and runs in milliseconds. | **Unbounded Tank** – “Infinite tank” can confuse people into thinking fuel capacity matters. | **Memory Mis‑management** – Using an array for a priority‑queue in C++ can crash on large inputs. |
| **Real‑World Analogy** – Optimising refuel stops is like logistics/planning. | **Test‑case Sensitivity** – Some solutions pass the LeetCode test set but fail hidden tests because they rely on “greedy” but not “optimal”. | **Non‑Deterministic** – If you forget to sort stations or use a min‑heap, the answer may vary per run. |

---

## Brute‑Force vs. Optimal

1. **Brute‑Force DP**  
   *State*: `dp[i] = maximum distance reachable after refueling exactly i times.*  
   *Complexity*: `O(n²)` – too slow for 500 stations.

2. **Greedy + Max‑Heap** (Optimal)  
   *Idea*:  
   * Keep a **max‑heap** of fuels from stations that we *have passed* but haven’t used yet.  
   * As we drive, we push the fuel of each reachable station into the heap.  
   * When we run out of fuel before reaching the next station or the target, we pop the largest fuel from the heap – that’s the best refuel at that point.  
   * Repeat until we can reach the target or the heap is empty.

   *Complexity*: `O(n log n)` – pushes/pops for each station at most once.

---

## Reference Implementation – Java

```java
import java.util.*;

public class Solution {
    /**
     * Minimum number of refueling stops.
     *
     * @param target     destination distance
     * @param startFuel  initial fuel
     * @param stations   array of [position, fuel]
     * @return minimum stops or -1 if impossible
     */
    public int minRefuelStops(int target, int startFuel, int[][] stations) {
        // Max-heap to store fuel amounts of stations we have passed.
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        int stops = 0;                 // number of refuels performed
        int currentFuel = startFuel;   // fuel we currently have
        int idx = 0;                   // index into stations array

        while (currentFuel < target) {
            // Push all stations we can reach with current fuel into the heap
            while (idx < stations.length && stations[idx][0] <= currentFuel) {
                maxHeap.offer(stations[idx][1]);   // fuel at that station
                idx++;
            }

            // If we cannot move further and heap is empty → impossible
            if (maxHeap.isEmpty()) {
                return -1;
            }

            // Refuel with the best station seen so far
            currentFuel += maxHeap.poll();
            stops++;
        }
        return stops;
    }
}
```

**Why it works**  
*The loop ends when `currentFuel >= target`. Each time we fail to reach the target we pop the largest unused fuel, guaranteeing the minimal number of stops.*

---

## Reference Implementation – Python

```python
import heapq
from typing import List

class Solution:
    def minRefuelStops(self, target: int, startFuel: int, stations: List[List[int]]) -> int:
        """
        Greedy + Max-Heap solution (O(n log n)).
        """
        # Python's heapq is a min-heap, so we store negative fuels.
        max_heap = []
        stops = 0
        fuel = startFuel
        idx = 0

        while fuel < target:
            # Push all stations we can reach.
            while idx < len(stations) and stations[idx][0] <= fuel:
                heapq.heappush(max_heap, -stations[idx][1])  # negative for max-heap
                idx += 1

            # No station to refuel from → impossible.
            if not max_heap:
                return -1

            # Refuel at the station with the most fuel.
            fuel -= fuel  # dummy line; keep readability
            fuel += -heapq.heappop(max_heap)
            stops += 1

        return stops
```

> **Note**: The line `fuel -= fuel` is only there to emphasize that we’re adding fuel to the current amount. In practice you would simply `fuel += -heapq.heappop(max_heap)`.

---

## Reference Implementation – C++

```cpp
#include <vector>
#include <queue>
#include <algorithm>

class Solution {
public:
    int minRefuelStops(int target, int startFuel, std::vector<std::vector<int>>& stations) {
        // Max-heap to keep the fuel amounts of stations we have passed.
        std::priority_queue<int> maxHeap;
        int stops = 0;
        long long fuel = startFuel;          // use long long to avoid overflow
        size_t idx = 0;

        while (fuel < target) {
            // Add all reachable stations to the heap.
            while (idx < stations.size() && stations[idx][0] <= fuel) {
                maxHeap.push(stations[idx][1]);
                ++idx;
            }

            // If we can't reach any new station or the target.
            if (maxHeap.empty()) return -1;

            // Refuel with the station that gives us the most fuel.
            fuel += maxHeap.top();
            maxHeap.pop();
            ++stops;
        }
        return stops;
    }
};
```

---

## How the Algorithm Works – Step‑by‑Step

| Step | Action | Reason |
|------|--------|--------|
| 1 | `currentFuel < target` | While we haven’t reached the destination. |
| 2 | Push all stations with `position ≤ currentFuel` into max‑heap | These stations are reachable *now*. |
| 3 | If heap empty → return `‑1` | No refuel options left → impossible. |
| 4 | Pop largest fuel from heap and add to `currentFuel` | Greedy: the largest fuel gives the biggest jump with one stop. |
| 5 | Increment `stops` | Count this refuel. |
| 6 | Loop again | Continue until we can reach target. |

---

## Common Pitfalls & How to Avoid Them

| Mistake | Fix |
|---------|-----|
| Using a **min‑heap** instead of a max‑heap | Use a max‑heap (or store negative values in a min‑heap). |
| Forgetting to sort `stations` | The problem guarantees sorted positions, but if you sort you must preserve the order. |
| Using **int** for fuel/target that can be up to `10⁹` and adding many fuels | Use `long long` (C++), `long` (Java), or `int` with Python’s arbitrary precision. |
| Not handling the case `stations = []` | The loop will exit immediately if `startFuel ≥ target`. |
| Returning `0` for impossible cases | Explicitly return `‑1` when the heap is empty. |

---

## Testing Your Solution

```python
# Example 1
print(Solution().minRefuelStops(1, 1, []))          # -> 0

# Example 2
print(Solution().minRefuelStops(100, 1, [[10, 100]]))  # -> -1

# Example 3
print(Solution().minRefuelStops(100, 10, [[10,60],[20,30],[30,30],[60,40]]))  # -> 2
```

---

## SEO‑Optimized Blog Article: “The Good, The Bad, and The Ugly of LeetCode Problem 871”

### Title  
> **“LeetCode 871: Minimum Refueling Stops – The Good, The Bad & The Ugly – A Deep Dive for Job‑Ready Engineers”**

### Meta Description  
> Master LeetCode 871 with our in‑depth guide. Understand greedy strategies, solve the problem in Java, Python, C++, and learn how to ace interview questions on logistics & dynamic programming.

### Header Structure

1. **Introduction** – Why this problem matters for tech interviews.
2. **Problem Recap** – Clear statement + constraints.
3. **Good** – The beauty of greedy + max‑heap; real‑world analogy.
4. **Bad** – Edge cases, large numbers, and common mistakes.
5. **Ugly** – Pitfalls in interview settings and how to avoid them.
6. **Optimal Solution Walk‑through** – Step‑by‑step explanation.
7. **Code Samples** – Java, Python, C++ (as above).
8. **Testing & Edge Cases** – Quick sanity checks.
9. **Takeaways for Job Interviews** – What interviewers want to see.
10. **Conclusion & Resources** – Further reading, practice links.

### Sample Blog Excerpt

> **The Good**  
> In LeetCode 871, you’re not just coding; you’re building a *smart strategy*. The greedy approach with a max‑heap shows that with a simple data structure you can make *optimal* decisions in linearithmic time. It demonstrates a deep understanding of *priority queues*, a staple in technical interviews.

> **The Bad**  
> The constraints trick the average coder. `target` and `fuelᵢ` can hit a billion, so a naive `int` overflow or an O(n²) DP will crash. Moreover, the problem’s phrasing—“infinite tank” but “1 liter per mile”—often leads to confusion about whether capacity matters.

> **The Ugly**  
> Hidden tests might include stations with zero fuel left or a target that can be reached with *exactly* zero fuel remaining. Many solutions fail because they forget that a 0‑fuel arrival still counts as success. The “ugly” part is that the bug is tiny but fatal.

> **Solution**  
> Here’s a succinct greedy implementation in Java… (insert code). It runs in `O(n log n)` and handles all edge cases, including the tricky zero‑fuel arrival.

> **Interview Tip**  
> When explaining your solution, start with “I’ll keep a max‑heap of fuels from stations we’ve passed.” This signals to the interviewer that you’re thinking about *optimally* using resources. Then walk through how you refill when you’re about to run out.

### Closing

> Mastering LeetCode 871 not only earns you a perfect score but also demonstrates your capability to apply greedy algorithms, manage large numbers, and handle edge cases—exactly the skill set recruiters look for in software engineers.

---

### Final Words

- **Time Complexity**: `O(n log n)`  
- **Space Complexity**: `O(n)` (heap size bounded by number of stations)  
- **Why It Pays Off**: This problem tests greedy + priority‑queue reasoning, a classic interview pattern. Solving it elegantly shows you can think “big picture” and manage resources efficiently—qualities highly valued by employers.

Happy coding—and good luck landing that dream job!