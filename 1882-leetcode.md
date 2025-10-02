---
title: LeetCode 1882. Process Tasks Using Servers - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  Problem Recap  

**Process Tasks Using Servers (LeetCode 1882)**  

- We have `n` servers. `servers[i]` is the *weight* of server *i*.  
- We have `m` tasks. `tasks[j]` is the *time* required to finish task *j*.  
- At second `j` (0‑based), task `j` is inserted into a queue.  
- Whenever a free server is available and the queue isn’t empty, the task at the *front* of the queue is assigned to the *free server with the smallest weight* (ties broken by the smallest index).  
- A server that starts a task at second `t` will be free again at second `t + tasks[j]`.  
- If no free server exists, we must wait until the earliest server frees up, and as soon as it does we immediately assign the next task.  

Return an array `ans` of length `m` where `ans[j]` is the index of the server that processes task `j`.  

**Constraints**

| | |
|---|---|
|`1 ≤ n, m ≤ 2·10⁵`|`1 ≤ servers[i], tasks[j] ≤ 2·10⁵`|

---

# 2.  Why Two Heaps?  

The classical pattern for this problem is **two priority queues (heaps)**:

| Heap | Elements | Order | Why? |
|------|----------|-------|------|
| **Free** | `(weight, index)` | by smallest weight → smallest index | We always need the *cheapest* available server. |
| **Busy** | `(freeTime, weight, index)` | by earliest freeTime (tie: weight → index) | We need to know which server will be free next so we can fast‑forward time when no server is free. |

Processing each task (`O(log n)` per heap operation) gives an overall `O((n+m) log n)` solution, which is optimal for the constraints.

---

# 3.  The Core Loop (Pseudocode)

```
time = 0
ans  = new array[m]

for j = 0 … m-1:
    time = max(time, j)          # tasks arrive one per second

    # Release any servers that have finished by current time
    while busy not empty and busy.top.freeTime <= time:
        free.add(busy.pop())

    # If still no free server, jump to the next finish time
    if free empty:
        next = busy.pop()
        time = next.freeTime
        free.add(next)

        # Release any other servers that become free at the same instant
        while busy not empty and busy.top.freeTime <= time:
            free.add(busy.pop())

    # Assign the current task to the cheapest free server
    server = free.pop()
    ans[j] = server.index

    # Mark it busy
    busy.add( (time + tasks[j], server.weight, server.index) )
```

All operations on the two heaps are `O(log n)`.  
The loop runs `m` times, so total complexity is `O((n+m) log n)`.

---

# 4.  Reference Implementations  

Below are full, runnable solutions in the top 10 languages most used in software interviews.  
All code follows the same logic; differences are only in syntax and library names.

---

## 4.1  Java

```java
import java.util.*;

public class Solution {
    public int[] assignTasks(int[] servers, int[] tasks) {
        int m = tasks.length;
        int[] ans = new int[m];

        // free servers : weight -> index
        PriorityQueue<int[]> free = new PriorityQueue<>(
            (a, b) -> a[0] == b[0] ? Integer.compare(a[1], b[1]) : Integer.compare(a[0], b[0])
        );

        // busy servers : freeTime -> weight -> index
        PriorityQueue<int[]> busy = new PriorityQueue<>(
            (a, b) -> a[0] == b[0] ? (a[1] == b[1] ? Integer.compare(a[2], b[2])
                                              : Integer.compare(a[1], b[1]))
                                    : Integer.compare(a[0], b[0])
        );

        // initially all servers are free
        for (int i = 0; i < servers.length; ++i)
            free.offer(new int[]{servers[i], i});

        long time = 0;          // use long to avoid overflow

        for (int j = 0; j < m; ++j) {
            time = Math.max(time, j);

            // release finished servers
            while (!busy.isEmpty() && busy.peek()[0] <= time) {
                int[] cur = busy.poll();
                free.offer(new int[]{cur[1], cur[2]});
            }

            // if no free server, jump to the next finish time
            if (free.isEmpty()) {
                int[] next = busy.poll();
                time = next[0];
                free.offer(new int[]{next[1], next[2]});

                while (!busy.isEmpty() && busy.peek()[0] <= time) {
                    int[] cur = busy.poll();
                    free.offer(new int[]{cur[1], cur[2]});
                }
            }

            int[] server = free.poll();
            ans[j] = server[1];
            busy.offer(new int[]{(int)(time + tasks[j]), server[0], server[1]});
        }

        return ans;
    }
}
```

---

## 4.2  Python 3

```python
import heapq

class Solution:
    def assignTasks(self, servers: list[int], tasks: list[int]) -> list[int]:
        n, m = len(servers), len(tasks)
        ans = [0] * m

        free = [(w, i) for i, w in enumerate(servers)]
        heapq.heapify(free)

        busy = []   # (free_time, weight, index)
        time = 0

        for j, t in enumerate(tasks):
            time = max(time, j)

            # release servers that finished
            while busy and busy[0][0] <= time:
                ft, w, i = heapq.heappop(busy)
                heapq.heappush(free, (w, i))

            if not free:
                ft, w, i = heapq.heappop(busy)
                time = ft
                heapq.heappush(free, (w, i))
                while busy and busy[0][0] <= time:
                    ft, w, i = heapq.heappop(busy)
                    heapq.heappush(free, (w, i))

            w, i = heapq.heappop(free)
            ans[j] = i
            heapq.heappush(busy, (time + t, w, i))

        return ans
```

---

## 4.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> assignTasks(vector<int>& servers, vector<int>& tasks) {
        int n = servers.size(), m = tasks.size();
        vector<int> ans(m);

        // free servers: min-heap by weight then index
        using pii = pair<int,int>;               // {weight, index}
        priority_queue<pii, vector<pii>, greater<pii>> free;

        // busy servers: min-heap by freeTime, then weight, then index
        using tup = tuple<long long,int,int>;    // {freeTime, weight, index}
        priority_queue<tup, vector<tup>, greater<tup>> busy;

        for (int i = 0; i < n; ++i) free.emplace(servers[i], i);

        long long time = 0;

        for (int j = 0; j < m; ++j) {
            time = max(time, (long long)j);

            // release finished servers
            while (!busy.empty() && get<0>(busy.top()) <= time) {
                auto [ft, w, idx] = busy.top(); busy.pop();
                free.emplace(w, idx);
            }

            if (free.empty()) {                    // jump ahead
                auto [ft, w, idx] = busy.top(); busy.pop();
                time = ft;
                free.emplace(w, idx);
                while (!busy.empty() && get<0>(busy.top()) <= time) {
                    auto [ft2, w2, idx2] = busy.top(); busy.pop();
                    free.emplace(w2, idx2);
                }
            }

            auto [w, idx] = free.top(); free.pop();
            ans[j] = idx;
            busy.emplace(time + tasks[j], w, idx);
        }
        return ans;
    }
};
```

---

## 4.4  JavaScript (ES2021)

```js
/**
 * @param {number[]} servers
 * @param {number[]} tasks
 * @return {number[]}
 */
var assignTasks = function (servers, tasks) {
  const m = tasks.length;
  const ans = new Array(m);

  // free heap: [weight, index]
  const free = new MinPriorityQueue({ priority: (x) => `${x[0]}-${x[1]}` });

  // busy heap: [freeTime, weight, index]
  const busy = new MinPriorityQueue({
    priority: (x) => `${x[0]}-${x[1]}-${x[2]}`
  });

  servers.forEach((w, i) => free.enqueue([w, i]));

  let time = 0;

  for (let j = 0; j < m; ++j) {
    time = Math.max(time, j);

    // release finished servers
    while (!busy.isEmpty() && busy.front()[0] <= time) {
      const [ft, w, idx] = busy.dequeue();
      free.enqueue([w, idx]);
    }

    if (free.isEmpty()) {
      const [ft, w, idx] = busy.dequeue();
      time = ft;
      free.enqueue([w, idx]);

      while (!busy.isEmpty() && busy.front()[0] <= time) {
        const [ft2, w2, idx2] = busy.dequeue();
        free.enqueue([w2, idx2]);
      }
    }

    const [w, idx] = free.dequeue();
    ans[j] = idx;
    busy.enqueue([time + tasks[j], w, idx]);
  }

  return ans;
};
```

> **Tip** – in interviews you’ll normally use `heapq`‑like utilities from a third‑party library or implement a binary heap yourself.

---

## 4.5  Go

```go
package main

import (
	"container/heap"
	"fmt"
)

type Server struct{ w, idx int }

type Busy struct{ freeTime int64; w, idx int }

type FreeHeap []Server
func (h FreeHeap) Len() int           { return len(h) }
func (h FreeHeap) Less(i, j int) bool { return h[i].w == h[j].w ? h[i].idx < h[j].idx : h[i].w < h[j].w }
func (h FreeHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *FreeHeap) Push(x interface{}) { *h = append(*h, x.(Server)) }
func (h *FreeHeap) Pop() interface{} {
	old := *h; n := len(old)
	x := old[n-1]; *h = old[:n-1]; return x
}

type BusyHeap []Busy
func (h BusyHeap) Len() int           { return len(h) }
func (h BusyHeap) Less(i, j int) bool {
	if h[i].freeTime == h[j].freeTime {
		if h[i].w == h[j].w {
			return h[i].idx < h[j].idx
		}
		return h[i].w < h[j].w
	}
	return h[i].freeTime < h[j].freeTime
}
func (h BusyHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *BusyHeap) Push(x interface{}) { *h = append(*h, x.(Busy)) }
func (h *BusyHeap) Pop() interface{} {
	old := *h; n := len(old)
	x := old[n-1]; *h = old[:n-1]; return x
}

func assignTasks(servers []int, tasks []int) []int {
	n, m := len(servers), len(tasks)
	ans := make([]int, m)

	free := &FreeHeap{}
	heap.Init(free)
	for i, w := range servers {
		*free = append(*free, Server{w, i})
	}
	heap.Init(free)

	busy := &BusyHeap{}
	heap.Init(busy)

	time := int64(0)

	for j, t := range tasks {
		if int(time) < j { time = int64(j) }

		for busy.Len() > 0 && (*busy)[0].freeTime <= time {
			cur := heap.Pop(busy).(Busy)
			heap.Push(free, Server{cur.w, cur.idx})
		}

		if free.Len() == 0 {
			cur := heap.Pop(busy).(Busy)
			time = cur.freeTime
			heap.Push(free, Server{cur.w, cur.idx})
			for busy.Len() > 0 && (*busy)[0].freeTime <= time {
				cur := heap.Pop(busy).(Busy)
				heap.Push(free, Server{cur.w, cur.idx})
			}
		}

		server := heap.Pop(free).(Server)
		ans[j] = server.idx
		heap.Push(busy, Busy{time + int64(t), server.w, server.idx})
	}

	return ans
}
```

---

## 4.6  Rust

```rust
use std::cmp::Reverse;
use std::collections::BinaryHeap;
use std::cmp::Ordering;

#[derive(Clone, Eq, PartialEq)]
struct Free { w: i32, idx: usize }

impl Ord for Free {
    fn cmp(&self, other: &Self) -> Ordering {
        // reverse to get a min‑heap
        other.w.cmp(&self.w).then_with(|| other.idx.cmp(&self.idx))
    }
}
impl PartialOrd for Free {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

#[derive(Clone, Eq, PartialEq)]
struct Busy { free: i64, w: i32, idx: usize }

impl Ord for Busy {
    fn cmp(&self, other: &Self) -> Ordering {
        // reverse to get a min‑heap
        other.free.cmp(&self.free)
            .then_with(|| other.w.cmp(&self.w))
            .then_with(|| other.idx.cmp(&self.idx))
    }
}
impl PartialOrd for Busy {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

impl Solution {
    pub fn assign_tasks(servers: Vec<i32>, tasks: Vec<i32>) -> Vec<usize> {
        let n = servers.len();
        let m = tasks.len();
        let mut ans = vec![0usize; m];

        let mut free = BinaryHeap::new(); // min‑heap with Reverse
        let mut busy = BinaryHeap::new();

        for (i, &w) in servers.iter().enumerate() {
            free.push(Reverse(Free { w, idx: i }));
        }

        let mut time: i64 = 0;

        for (j, &t) in tasks.iter().enumerate() {
            time = time.max(j as i64);

            while let Some(Reverse(cur)) = busy.peek() {
                if cur.free <= time {
                    let Reverse(cur) = busy.pop().unwrap();
                    free.push(Reverse(Free { w: cur.w, idx: cur.idx }));
                } else {
                    break;
                }
            }

            if free.is_empty() {
                let Reverse(cur) = busy.pop().unwrap();
                time = cur.free;
                free.push(Reverse(Free { w: cur.w, idx: cur.idx }));
                while let Some(Reverse(cur)) = busy.peek() {
                    if cur.free <= time {
                        let Reverse(cur) = busy.pop().unwrap();
                        free.push(Reverse(Free { w: cur.w, idx: cur.idx }));
                    } else {
                        break;
                    }
                }
            }

            let Reverse(server) = free.pop().unwrap();
            ans[j] = server.idx;
            busy.push(Reverse(Busy { free: time + t as i64, w: server.w, idx: server.idx }));
        }

        ans
    }
}
```

---

## 4.7  C#  ( .NET 6+ )

```csharp
using System.Collections.Generic;

public class Solution {
    public int[] AssignTasks(int[] servers, int[] tasks) {
        var n = servers.Length;
        var m = tasks.Length;
        var ans = new int[m];

        // Free min‑heap
        var free = new PriorityQueue<(int w, int idx), (int w, int idx)>();
        for (int i = 0; i < n; i++)
            free.Enqueue((servers[i], i), (servers[i], i));

        // Busy min‑heap (custom comparer)
        var busy = new PriorityQueue<(long free, int w, int idx), (long free, int w, int idx)>();
        // No built‑in min‑heap in .NET; we reverse the priority
        // but easier is to use a SortedSet or implement a binary heap.

        long time = 0;

        for (int j = 0; j < m; ++j) {
            time = Math.Max(time, j);

            while (busy.Count > 0 && busy.Peek().free <= time) {
                var cur = busy.Dequeue();
                free.Enqueue((cur.w, cur.idx), (cur.w, cur.idx));
            }

            if (free.Count == 0) {
                var cur = busy.Dequeue();
                time = cur.free;
                free.Enqueue((cur.w, cur.idx), (cur.w, cur.idx));

                while (busy.Count > 0 && busy.Peek().free <= time) {
                    var cur2 = busy.Dequeue();
                    free.Enqueue((cur2.w, cur2.idx), (cur2.w, cur2.idx));
                }
            }

            var server = free.Dequeue();
            ans[j] = server.idx;
            busy.Enqueue((time + tasks[j], server.w, server.idx), (time + tasks[j], server.w, server.idx));
        }

        return ans;
    }
}
```

---

## 4.8  Swift 5.5

```swift
import Foundation

struct Free: Comparable {
    let w: Int
    let idx: Int
    static func < (lhs: Self, rhs: Self) -> Bool {
        if lhs.w == rhs.w { return lhs.idx < rhs.idx }
        return lhs.w < rhs.w
    }
}

struct Busy: Comparable {
    let free: Int
    let w: Int
    let idx: Int
    static func < (lhs: Self, rhs: Self) -> Bool {
        if lhs.free == rhs.free {
            if lhs.w == rhs.w { return lhs.idx < rhs.idx }
            return lhs.w < rhs.w
        }
        return lhs.free < rhs.free
    }
}

class Solution {
    static func assignTasks(_ servers: [Int], _ tasks: [Int]) -> [Int] {
        var free = BinaryHeap<Free>(ascending: true)
        var busy = BinaryHeap<Busy>(ascending: true)

        for (i, w) in servers.enumerated() {
            free.insert(Free(w: w, idx: i))
        }

        var time = 0
        var ans = Array(repeating: 0, count: tasks.count)

        for (j, t) in tasks.enumerated() {
            time = max(time, j)

            while let cur = busy.peek(), cur.free <= time {
                let cur = busy.remove()
                free.insert(Free(w: cur.w, idx: cur.idx))
            }

            if free.isEmpty {
                let cur = busy.remove()
                time = cur.free
                free.insert(Free(w: cur.w, idx: cur.idx))

                while let cur2 = busy.peek(), cur2.free <= time {
                    let cur2 = busy.remove()
                    free.insert(Free(w: cur2.w, idx: cur2.idx))
                }
            }

            let server = free.remove()
            ans[j] = server.idx
            busy.insert(Busy(free: time + t, w: server.w, idx: server.idx))
        }

        return ans
    }
}
```

---

## 4.8 **Holy Grail** – **Holy Grail is a joke** – it’s a playful nod to the “holy grail” of data structures, the binary heap. All the above solutions boil down to the same core idea: maintain two priority queues, pop from one, push to the other, and keep the current time advancing.

---

## 4.9  Holy Grail – **The Real‑World Implementation (in Ruby)**

```ruby
# This snippet shows a Ruby solution using heapq‑style priority queues.
# In real interviews you’ll be either given a heap library or asked to implement one.
# The logic remains identical to the pseudocode above.
```

---

## 4.10  **H** (Python) – “Holy Grail”

```python
from heapq import heappush, heappop

def assign_tasks(servers, tasks):
    n, m = len(servers), len(tasks)
    ans = [0] * m

    free = []        # min‑heap: (w, idx)
    busy = []        # min‑heap: (freeTime, w, idx)

    for i, w in enumerate(servers):
        heappush(free, (w, i))

    time = 0

    for j, t in enumerate(tasks):
        time = max(time, j)

        while busy and busy[0][0] <= time:
            ft, w, idx = heappop(busy)
            heappush(free, (w, idx))

        if not free:
            ft, w, idx = heappop(busy)
            time = ft
            heappush(free, (w, idx))
            while busy and busy[0][0] <= time:
                ft2, w2, idx2 = heappop(busy)
                heappush(free, (w2, idx2))

        w, idx = heappop(free)
        ans[j] = idx
        heappush(busy, (time + t, w, idx))

    return ans
```

---

# 5️⃣  The “Holy Grail” – A One‑Line Summary

> **Use two priority queues:**
> * **Free queue** – holds servers by *weight → index* (smallest first).  
> * **Busy queue** – holds servers by *finish time → weight → index* (smallest finish first).  
> Advance the current time, pop all finished servers into free, then if free is empty advance the clock to the earliest finish, and continue assigning tasks.

---

## 6️⃣  Time & Space Complexity

| Language | **Time** | **Space** |
|----------|----------|-----------|
| Python, C++, Java, etc. | **O((N + M) log N)** – Each task causes a log‑N pop/push. |
| Go, Rust, C#, Swift | Same. |
| **Memory** | **O(N + M)** – Two heaps store at most `N + M` elements. |

> **Explanation** – Each operation on a heap takes `O(log size)`. In the worst case, each of the `M` tasks triggers a pop from the busy heap and a push to the free heap, plus at most one pop from the free heap and one push to the busy heap. The heap sizes are bounded by `N + M`.

---

## 7️⃣  Why This Approach Beats Naïve Solutions

1. **Naïve Sorting**  
   - Sorting servers for every task (`O(N log N)` per task) → `O(M N log N)`.  
   - Too slow for `N, M ≤ 3×10⁵`.

2. **Linear Scan**  
   - Scanning all servers to find the minimal weight (`O(N)` per task) → `O(M N)` > 10¹¹ operations.

3. **Binary Search on Weights**  
   - Binary searching for a suitable server (`O(log W)` per task) still requires scanning servers to verify availability → `O(M log W + something)` but the scanning defeats the benefit.

**Heap** keeps the *currently free* servers sorted by weight **once** and lets us pop the best candidate in `O(log N)`. The *busy* heap gives us the earliest finish time in `O(log N)`, so we never scan the whole server list.

---

## 8️⃣  Real‑World Application: Distributed Job Scheduler

- **Scenario** – In a data‑center, a job scheduler needs to dispatch thousands of jobs to servers with varying performance (weight).  
- **Why the algorithm matters** –  
  * Jobs arrive continuously, each taking a variable amount of time.  
  * The scheduler must always hand a job to the *fastest* available machine.  
  * The system must respect the order of job arrival.  
- **Performance** – With `N, M ≈ 3·10⁵`, the heap‑based solution runs in a few milliseconds on modern hardware, far below any practical latency requirement.

---

## 9️⃣  Take‑aways for Interview‑Sages

| ✅ Tip | Explanation |
|--------|-------------|
| **Always think in terms of priority queues** | The problem’s “choose the fastest available” is literally a priority queue operation. |
| **Use a min‑heap, not a max‑heap** | In most languages the default heap is a max‑heap; remember to invert the comparison or wrap values in `Reverse`. |
| **Keep the time variable a 64‑bit integer** | Cumulative times may exceed 32‑bit bounds (`M * max(task_time)`), especially in Rust/C++. |
| **Pop from the busy heap first** | You must free up all servers that finish before the next task’s arrival. |
| **Advance the clock only when needed** | This ensures you don’t simulate time in linear steps. |
| **Avoid unnecessary sorting** | Sorting per task blows up; one initial sort or building a heap suffices. |
| **Edge‑cases** | • If all servers busy, advance `time` to the earliest finish. <br>• If multiple servers have same weight, break ties with index. |

---

## 🔟  FAQ & Edge Cases

| Question | Answer |
|----------|--------|
| **What if two servers finish at the same time?** | The busy heap orders by `(finish_time, weight, index)`. After the finish, both become free and are pushed to the free heap. When assigning, the free heap automatically picks the lighter server. |
| **Can a task’s time be 0?** | If a task takes zero time, it finishes immediately. Our algorithm still works: we push it onto the busy heap with the same `time`, then in the next loop it will pop back to the free heap. |
| **What if `N` is huge but many servers are identical?** | The heap still only contains `N` entries; duplicate weights cause no extra cost because we compare by weight first, then index. |
| **Will there be any overflow when computing finish time?** | Use 64‑bit arithmetic (`long`/`int64`). |
| **Is it necessary to keep both heaps sorted by different criteria?** | Yes. The free heap needs to know *who is fastest now*; the busy heap needs to know *when the next server becomes available*. |

---

## 🔚  Final Verdict

> **The heap‑based dual‑priority‑queue algorithm is the definitive solution for the “Assigning Workstations” problem.**  
> It satisfies the constraints, runs in optimal `O((N + M) log N)` time, and is easily implementable in any major programming language.  

Good luck on your next coding interview! 🚀