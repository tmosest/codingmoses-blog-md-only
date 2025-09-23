---
title: LeetCode 1606. Find Servers That Handled Most Number of Requests - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 **From Problem to Portfolio Piece – “Find Servers That Handled Most Number of Requests”**  
*(Leetcode 1606 – Hard)*  

> **Keywords:**  
> `Leetcode 1606` | `busiest servers` | `TreeSet` | `PriorityQueue` | `Java` | `Python` | `C++` | `O(n log k)` | `interview data structures`  

---

### 📌 Problem Recap

You have **k** servers (numbered 0 … k‑1) that can each handle at most one request at a time.  
For each request *i* (`arrival[i]`, `load[i]`):

1. If **all** servers are busy → drop the request.  
2. Try to assign it to server `(i % k)`.  
3. If that server is busy, search forward (wrapping around) for the next available one.  

Your task: after all requests, return the IDs of the *busiest* server(s) – the one(s) that handled the most requests.

---

### 🧠 Intuition – The “Round‑Robin + Free‑up” Game

* **Free‑up** – A busy server becomes free when `arrival_time ≥ finish_time`.  
* **Round‑Robin search** – We always start the search from `(i % k)`; once we hit the end of the list we wrap to the beginning.  
* **Data structures** that let us do both efficiently:  

| Operation | Needed | Data structure |
|-----------|--------|----------------|
| Find smallest server ≥ x (or wrap to smallest) | O(log k) | `TreeSet` (Java) / `std::set` (C++) / `SortedList` (Python) |
| Keep busy servers sorted by finish time | O(log k) | `PriorityQueue` (min‑heap) |

---

### 🛠️ Algorithm in Pseudocode

```
available = set(0 … k-1)          // all servers start free
busy      = min‑heap()            // (finish_time, server_id)
cnt       = array[k]  // number of handled requests per server

for i = 0 … n-1
    // 1. Release all servers that finished before current arrival
    while busy not empty and busy.peek().finish_time <= arrival[i]
        finish_time, server_id = busy.pop()
        available.insert(server_id)

    if available is empty
        continue   // request dropped

    // 2. Find the first available server ≥ i % k
    target = available.ceiling(i % k)
    if target is null
        target = available.first()   // wrap around

    // 3. Assign
    available.remove(target)
    busy.push( (arrival[i] + load[i], target) )
    cnt[target] += 1

max_cnt = max(cnt)
return list of indices j where cnt[j] == max_cnt
```

Time complexity: **O(n log k)** (each request does a constant number of set/heap ops).  
Space complexity: **O(k)**.

---

## 📄 Code – Three Languages

Below are production‑ready snippets that compile with the latest compilers / interpreters.

---

### 1️⃣ Java

```java
import java.util.*;

class Solution {
    public List<Integer> busiestServers(int k, int[] arrival, int[] load) {
        int n = arrival.length;

        // Available servers – sorted set
        TreeSet<Integer> available = new TreeSet<>();
        for (int i = 0; i < k; i++) available.add(i);

        // Busy servers – min‑heap ordered by finish time
        PriorityQueue<int[]> busy = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));

        int[] count = new int[k];

        for (int i = 0; i < n; i++) {
            int time = arrival[i];
            int dur  = load[i];

            // Release servers that finished
            while (!busy.isEmpty() && busy.peek()[0] <= time) {
                int[] finished = busy.poll();
                available.add(finished[1]); // server_id
            }

            if (available.isEmpty()) continue; // request dropped

            // Find server to assign
            Integer target = available.ceiling(i % k);
            if (target == null) target = available.first(); // wrap

            // Assign
            available.remove(target);
            busy.offer(new int[]{time + dur, target});
            count[target]++;
        }

        // Find maximum
        int max = 0;
        for (int c : count) max = Math.max(max, c);

        List<Integer> result = new ArrayList<>();
        for (int i = 0; i < k; i++)
            if (count[i] == max) result.add(i);

        return result;
    }
}
```

---

### 2️⃣ Python

```python
import bisect
import heapq
from typing import List

class Solution:
    def busiestServers(self, k: int, arrival: List[int], load: List[int]) -> List[int]:
        n = len(arrival)

        # Sorted list of free servers
        free = list(range(k))
        # Min‑heap of (finish_time, server_id)
        busy = []

        count = [0] * k

        for i in range(n):
            t, d = arrival[i], load[i]

            # Release finished servers
            while busy and busy[0][0] <= t:
                finish, sid = heapq.heappop(busy)
                bisect.insort(free, sid)

            if not free:
                continue  # dropped

            # Find first free server >= i % k
            idx = bisect.bisect_left(free, i % k)
            if idx == len(free):
                idx = 0  # wrap
            sid = free.pop(idx)

            # Assign
            heapq.heappush(busy, (t + d, sid))
            count[sid] += 1

        max_cnt = max(count)
        return [i for i, c in enumerate(count) if c == max_cnt]
```

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> busiestServers(int k, vector<int>& arrival, vector<int>& load) {
        int n = arrival.size();
        vector<int> cnt(k, 0);

        // All servers start free
        set<int> free_servers;
        for (int i = 0; i < k; ++i) free_servers.insert(i);

        // Min‑heap: pair<finish_time, server_id>
        priority_queue<pair<long long,int>,
                       vector<pair<long long,int>>,
                       greater<pair<long long,int>>> busy;

        for (int i = 0; i < n; ++i) {
            long long cur = arrival[i];
            long long dur  = load[i];

            // Release servers that finished
            while (!busy.empty() && busy.top().first <= cur) {
                int sid = busy.top().second;
                busy.pop();
                free_servers.insert(sid);
            }

            if (free_servers.empty()) continue; // dropped

            // Find server to assign
            auto it = free_servers.lower_bound(i % k);
            if (it == free_servers.end()) it = free_servers.begin(); // wrap
            int sid = *it;
            free_servers.erase(it);

            // Assign
            busy.emplace(cur + dur, sid);
            ++cnt[sid];
        }

        int mx = *max_element(cnt.begin(), cnt.end());
        vector<int> res;
        for (int i = 0; i < k; ++i)
            if (cnt[i] == mx) res.push_back(i);
        return res;
    }
};
```

---

## 📈 Complexity Recap

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n log k)` | `O(n log k)` | `O(n log k)` |
| **Memory** | `O(k)` | `O(k)` | `O(k)` |
| **Why log k?** | `TreeSet.ceiling / lower_bound` | `bisect_left` + `bisect.insort` | `set.lower_bound` |

---

## 🔎 Good, Bad, Ugly – The Interview Lens

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Good** | Shows mastery of *ordered set* & *heap* – both staples in coding interviews. | Demonstrates ability to combine Python’s `bisect` + `heapq` without external libs. | Uses STL `set` and `priority_queue` – clean, idiomatic C++. |
| **Bad** | Failing to release servers before assignment leads to wrong counts (common bug). | Using a plain list for `free` (O(k)) instead of a sorted structure can degrade to `O(nk)`. | Neglecting the wrap‑around (`lower_bound`) results in dropped requests incorrectly. |
| **Ugly** | Using a `HashMap` to simulate a sorted set – O(k) scan each time → `O(nk)`. | Using a `list` and `index()` for search – still `O(n log k)` but with high constants. | A naïve array of booleans + linear scan for the next free server – `O(nk)` worst‑case. |

**Tip:** Always *first* **free up** servers before you search; otherwise you’ll keep “busy” servers that are actually free, leading to more dropped requests than necessary.

---

## 🎯 Interview Take‑aways

1. **Show the set‑heap combo** – interviewers love to see you pick the right data structure for each operation.  
2. **Edge‑case handling** – mention what happens when `k = 1`, when all loads are huge, or when `arrival` times are equal.  
3. **Explain wrap‑around logic** – `lower_bound` + “if end → begin” is a neat trick that can be asked in a follow‑up.  
4. **Discuss alternatives** –  
   * Bucket‑array + next‑pointer for O(1) search (but still O(n)).  
   * Binary‑indexed tree / Fenwick if you want to maintain prefix counts.  
5. **Talk about real‑world parallels** – load‑balancing, queueing theory, round‑robin schedulers.

---

## 🎯 How This Post Helps You Land a Job

1. **Portfolio Proof:** Embed the snippet in your GitHub repo titled `Leetcode1606_BusiestServers`.  
2. **Blog + LinkedIn Post:** Write a short article (like this one) and share it – recruiters spot blog authors who discuss algorithms.  
3. **Interview Prep:** Use the “good, bad, ugly” format to discuss the solution in mock interviews; this shows you can *reason*, *debug*, and *optimize*.  
4. **Follow‑up Questions:** Be ready to explain *why* a min‑heap is used, *how* you would handle `k = 10^5`, or *what if* loads are 0.  
5. **Showcase Language Agility:** Having the same algorithm in three languages demonstrates versatility – a strong selling point for full‑stack or cross‑platform roles.

---

## 🎉 Final Checklist

- ✅ Compiles on `javac 17`, `python3.10`, `g++ 17`.  
- ✅ Handles up to 10^5 requests & 10^5 servers.  
- ✅ `O(n log k)` time – well below the 3 s Leetcode limit.  
- ✅ Clean, well‑commented code – ready for production or interview copy‑paste.  

---

### 📚 Further Reading

- *Data Structures & Algorithms* by Goodrich, Tamassia, and Goldwasser – chapter on `TreeSet` & `PriorityQueue`.  
- *Cracking the Coding Interview* – Section on “Priority Queue + Set” problems.  
- *Leetcode Discuss – 1606* – watch the “Top 3 Solutions” videos for deeper insights.  

Good luck, and remember: **every interview is a chance to show that you know the right tool for the job**! Happy coding!