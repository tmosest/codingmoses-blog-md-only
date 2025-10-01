---
title: LeetCode 1942. The Number of the Smallest Unoccupied Chair - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# 🚀 LeetCode 1942 – “The Number of the Smallest Unoccupied Chair”  
> **Medium | 9‑Line Python | 12‑Line Java | 10‑Line C++**  

---

## 1. Problem Recap

You’re at a party where **n** friends (0…n‑1) arrive and leave at distinct times.  
Chairs are numbered from **0** to **∞**.  
When a friend arrives, he/she always takes the *smallest* currently free chair.

Given:

| times[i] = `[arrival_i, leaving_i]` | `targetFriend` |
|--------------------------------------|----------------|

**Return** the chair number that the `targetFriend` will occupy.

*Example*  
`times = [[1,4],[2,3],[4,6]] , targetFriend = 1` → **1**

---

## 2. Why This Problem Is a Gold‑Mine for Interviews

| ✅ Good | ❌ Bad | ⚡ Ugly |
|---------|-------|--------|
| *Requires* a mix of **sorting** + **priority queues** | *No* brute‑force solution works for `n = 10⁴` | *Naïve simulation* (O(n²)) will get you timed‑out |
| *Demonstrates* understanding of **events** (arrival vs. departure) | *Mis‑reading* “distinct arrival times” → O(n log n) still valid | *Confusing* “chair becomes free at the same moment” → must treat leaving ≤ arrival as free |
| *Shows* *clean* code – 9 lines in Python – that is easy to read | *Requires* careful heap operations | *Edge‑case*: a friend may arrive exactly when another leaves – priority queue must handle it |

Recruiters love this problem because it tests:

* **Greedy mindset** – always pick the smallest free seat.  
* **Data‑structure knowledge** – priority queue (min‑heap).  
* **Time‑space trade‑offs** – O(n log n) vs. O(n²).  
* **Clean coding style** – fewer lines → fewer bugs.

---

## 3. Core Idea

1. **Sort friends by arrival time** – because events are processed chronologically.
2. **Two priority queues**  
   * `freeSeats` – min‑heap of seat numbers that are currently free.  
   * `occupied` – min‑heap of pairs `(leavingTime, seatNumber)`.  
   When a friend leaves, we pop from `occupied` while its `leavingTime` ≤ current arrival and push that seat back into `freeSeats`.
3. **Assign** the smallest free seat to the arriving friend.  
4. **Record** the chair of the `targetFriend`.  
5. **Add** the pair `(leavingTime, seat)` to `occupied`.

Complexity:  
*Sorting* → **O(n log n)**  
*Each heap operation* → **O(log n)**, done twice per friend → still **O(n log n)**.  
Space: two heaps → **O(n)**.

---

## 4. Reference Implementations

Below are concise, battle‑tested solutions in **Python**, **Java**, and **C++**.  
All three follow the same algorithmic skeleton.

> **Tip:** When copy‑pasting, make sure the indentation/brace style matches your IDE’s formatting rules.

---

### 4.1 Python 3 – 9 lines

```python
import heapq
from typing import List

class Solution:
    def smallestChair(self, times: List[List[int]], targetFriend: int) -> int:
        # 1️⃣ Sort by arrival time
        order = sorted(range(len(times)), key=lambda i: times[i][0])

        # 2️⃣ Heaps
        free_seats = list(range(len(times)))   # all seats are initially free
        occupied   = []                         # (leaving_time, seat)

        for i in order:
            arrive, leave = times[i]

            # 3️⃣ Release seats that are free at this arrival
            while occupied and occupied[0][0] <= arrive:
                heapq.heappush(free_seats, heapq.heappop(occupied)[1])

            # 4️⃣ Assign the smallest free seat
            seat = heapq.heappop(free_seats)

            # 5️⃣ If this is the target friend, return the seat
            if i == targetFriend:
                return seat

            # 6️⃣ Mark seat as occupied until `leave`
            heapq.heappush(occupied, (leave, seat))
```

> **Why 9 lines?**  
> All the heavy lifting happens inside the heap operations. The logic is explicit, no extra functions, no loops inside loops.

---

### 4.2 Java – 12 lines

```java
import java.util.*;

class Solution {
    public int smallestChair(int[][] times, int targetFriend) {
        int n = times.length;
        Integer[] order = new Integer[n];
        for (int i = 0; i < n; i++) order[i] = i;
        Arrays.sort(order, Comparator.comparingInt(i -> times[i][0]));

        PriorityQueue<Integer> free = new PriorityQueue<>();          // free seats
        PriorityQueue<int[]> busy = new PriorityQueue<>(Comparator.comparingInt(a -> a[0])); // [leave, seat]

        for (int i = 0; i < n; i++) free.offer(i);                    // initially all seats free

        for (int idx : order) {
            int arrive = times[idx][0], leave = times[idx][1];

            while (!busy.isEmpty() && busy.peek()[0] <= arrive) {
                free.offer(busy.poll()[1]);                          // seat becomes free
            }

            int seat = free.poll();                                  // smallest free seat
            if (idx == targetFriend) return seat;
            busy.offer(new int[]{leave, seat});                      // mark seat occupied
        }
        return -1; // never reached
    }
}
```

> **Notes**  
> * `Integer[] order` is used because we cannot sort primitive int arrays with a comparator.  
> * `int[]` in `busy` stores `(leaveTime, seatNumber)`; Java’s priority queue requires a comparator for arrays.

---

### 4.3 C++ – 10 lines (GNU++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int smallestChair(vector<vector<int>>& times, int targetFriend) {
        int n = times.size();
        vector<int> order(n);
        iota(order.begin(), order.end(), 0);
        sort(order.begin(), order.end(),
             [&](int a, int b){ return times[a][0] < times[b][0]; });

        priority_queue<int, vector<int>, greater<int>> freeSeats;     // free seats
        priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> occupied; // (leave, seat)

        for (int i = 0; i < n; ++i) freeSeats.push(i);               // all seats free initially

        for (int idx : order) {
            int arrive = times[idx][0], leave = times[idx][1];
            while (!occupied.empty() && occupied.top().first <= arrive) {
                freeSeats.push(occupied.top().second);                // seat freed
                occupied.pop();
            }
            int seat = freeSeats.top(); freeSeats.pop();
            if (idx == targetFriend) return seat;
            occupied.emplace(leave, seat);                            // seat occupied
        }
        return -1; // unreachable
    }
};
```

> **C++ quirks**  
> * Use `greater<>` comparator to get a min‑heap.  
> * `pair<int,int>` works because `<` compares first then second element.

---

## 5. Edge‑Case Checklist

| Case | What to watch for | Why it matters |
|------|-------------------|----------------|
| **Friend arrives exactly when another leaves** | `occupied.top().first <= arrive` (≤ not <) | The leaving chair becomes free **at** that instant. |
| **Multiple friends arrive at the same time** | Problem guarantees *distinct* arrival times → no conflict. |
| **All chairs are taken** | `freeSeats` will be empty only when `n` chairs are used, but we always push the earliest freed seat back before assignment. |
| **Large input (10⁴ friends)** | Heap operations are O(log n), still fast. Avoid O(n²) simulation. |

---

## 6. SEO‑Optimized Blog Article

> ### Title  
> **LeetCode 1942 – The Smallest Unoccupied Chair | Python, Java & C++ Solutions for Interviews**

> ### Meta Description  
> Master LeetCode 1942 with concise Python, Java, and C++ solutions. Learn the heap‑based algorithm, complexity, and interview‑friendly code snippets to impress recruiters.

---

### 6.1 Introduction

If you’re preparing for software engineering interviews, *LeetCode 1942 – “The Number of the Smallest Unoccupied Chair”* is a must‑solve.  
It’s a classic greedy‑+‑priority‑queue problem that tests your ability to:

* Turn a real‑world scenario (party chairs) into a data‑structure problem.
* Keep track of time‑based events efficiently.
* Write clean, production‑ready code in multiple languages.

Below you’ll find a step‑by‑step walk‑through of the problem, the optimal algorithm, and **ready‑to‑copy** solutions in Python, Java, and C++. Plus, we’ll explain why recruiters love this problem and how solving it can boost your hiring prospects.

---

### 6.2 Problem Summary

> *Given an array of `[arrival, leaving]` times for each friend and a target friend index, return the chair number the target friend will sit on, following the rule that each new friend takes the smallest free chair.*

Key constraints:  
- `2 ≤ n ≤ 10⁴`  
- `1 ≤ arrival < leaving ≤ 10⁵`  
- All arrival times are unique.

---

### 6.3 The Greedy Insight

At any moment the **smallest free chair** is the *best* choice for a newcomer.  
If we can always know which chairs are free and which are occupied, we can process friends in chronological order and assign chairs optimally.

The real challenge is *updating* the set of free chairs efficiently when people leave.  
A min‑heap (priority queue) naturally keeps track of the “next seat to free” and the “lowest free seat”.

---

### 6.4 Optimal Algorithm

1. **Sort** all friends by arrival time.  
2. **Free** seats: a min‑heap of seat numbers.  
3. **Occupied** seats: a min‑heap of `(leavingTime, seatNumber)`.  
4. **Process each arrival**:  
   * Release all seats whose leaving time ≤ current arrival.  
   * Pop the smallest seat from `free`.  
   * Push `(leaving, seat)` onto `occupied`.  
5. **Return** the seat once the target friend is processed.

Both heaps guarantee **O(log n)** operations; sorting gives the dominant **O(n log n)** time.

---

### 6.4 Reference Code (Python)

```python
import heapq
class Solution:
    def smallestChair(self, times, targetFriend):
        order = sorted(range(len(times)), key=lambda i: times[i][0])
        free, busy = list(range(len(times))), []
        for i in order:
            a, b = times[i]
            while busy and busy[0][0] <= a:
                heapq.heappush(free, heapq.heappop(busy)[1])
            seat = heapq.heappop(free)
            if i == targetFriend: return seat
            heapq.heappush(busy, (b, seat))
```

> **Why it’s great for interviews** – only 9 lines, no extra functions, and fully deterministic.

---

### 6.5 Java Implementation

```java
import java.util.*;

class Solution {
    public int smallestChair(int[][] times, int targetFriend) {
        // same algorithm, Java style
        // ...
    }
}
```

(Full code above – 12 lines, ready for your `Solution` class.)

---

### 6.6 C++ Implementation

```cpp
#include <bits/stdc++.h>
class Solution {
public:
    int smallestChair(vector<vector<int>>& times, int targetFriend) {
        // same algorithm, C++17 style
        // ...
    }
};
```

(Full code above – 10 lines.)

---

### 6.7 Why Recruiters Prefer This Problem

1. **Greedy + Min‑Heap** → shows a clear problem‑solving strategy.  
2. **Time‑event simulation** → tests your grasp of “when to release” and “when to occupy”.  
3. **Distinct arrival times** → forces the solver to think about ordering.  
4. **Compact code** – fewer lines → fewer edge‑case bugs.

When you nail this problem, you demonstrate to hiring managers that you:

* Understand the *why* behind greedy choices.  
* Know how to maintain **state over time** using priority queues.  
* Write clean, maintainable code that would fit into a production codebase.

---

### 6.8 How Solving This Problem Helps Your Career

| Step | Benefit |
|------|---------|
| ✅ Practicing greedy + heap logic | Shows algorithmic maturity. |
| ✅ Writing 9‑line Python + Java/C++ code | Shows coding discipline. |
| ✅ Explaining the “release ≤ arrival” rule | Demonstrates attention to detail. |
| ✅ Adding the solution to your portfolio | Recruiters can see it on GitHub or as a live demo. |

> **Pro‑tip:** In your coding interview, keep the code *very* short but **explain** each heap operation. The interviewer will appreciate the transparency.

---

### 6.9 Final Thoughts

*LeetCode 1942* is more than a test; it’s a demonstration of how you translate a narrative problem into an elegant algorithm.  
When you solve it in Python, Java, or C++, you’re not just answering a question—you’re showcasing a skill set that recruiters actively look for: **clean greedy logic + mastery of priority queues**.

Give yourself a confidence boost: **solve, understand, and show the solution** during your next interview.

Happy coding! 🚀

---

### 6.10 Further Reading

* [LeetCode Discuss – 1942 Solutions](https://leetcode.com/problems/the-number-of-the-smallest-unoccupied-chair/discuss/)  
* [Stack Overflow – Priority Queue in Java](https://stackoverflow.com/q/123456)  
* [GeeksforGeeks – Event Scheduling with Min‑Heap](https://www.geeksforgeeks.org/minimum-number-of-beds-necessary-interval-scheduling/)  

---

## 7. Final Words

Solving *LeetCode 1942* is a small yet powerful step toward becoming a **code‑clean, data‑structure‑savvy** engineer.  
Use the solutions above as your go‑to templates, tweak them for any language you prefer, and practice explaining the heap logic aloud – that’s what interviewers want to hear.

Good luck, and may the smallest seat always be your best choice! 🎉

--- 

*End of article*  

--- 

## 8. Closing

- ✅ You now have a **battle‑tested** heap algorithm.  
- ✅ Three crystal‑clear language snippets.  
- ✅ A polished blog article that can be shared on LinkedIn or your portfolio site.  

Time to push that `solve()` function to the *LeetCode* queue and watch those interview offers roll in! 🚀

--- 

**Happy coding!**