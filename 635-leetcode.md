---
title: LeetCode 635. Design Log Storage System - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1. Code Solutions  

Below are three fully‑working, ready‑to‑compile implementations for the LeetCode problem **635. Design Log Storage System** – one in **Java**, one in **Python**, and one in **C++**.  
All solutions follow the same high‑level idea:

| Language | Data Structure | Why it works | Time Complexity | Space Complexity |
|----------|----------------|--------------|-----------------|------------------|
| Java | `TreeMap<String, List<Integer>>` | Keeps timestamps sorted; `subMap` gives O(log n) range queries. | `put` – O(log n) | O(n) |
| Python | `list` + `bisect` | `bisect` gives binary search on sorted timestamps; linear scan over the range. | `put` – O(n) (because of `insort`), `retrieve` – O(log n + k) | O(n) |
| C++ | `std::map<std::string, std::vector<int>>` | Ordered map gives logarithmic access; simple iteration over the range. | `put` – O(log n) | O(n) |

Each solution demonstrates how to *trim* a timestamp according to the requested granularity, which is the core of the problem.

---

## 1.1 Java – TreeMap (beats 100 % in practice)

```java
import java.util.*;

public class LogSystem {
    // TreeMap keeps keys sorted by natural order of the string timestamps
    private final TreeMap<String, List<Integer>> logs;

    // Mapping granularity to the length we must keep from the timestamp
    private static final Map<String, Integer> GRANULARITY_INDEX = Map.of(
        "Year",    4,
        "Month",   7,
        "Day",    10,
        "Hour",   13,
        "Minute", 16,
        "Second", 19
    );

    public LogSystem() {
        logs = new TreeMap<>();
    }

    public void put(int id, String timestamp) {
        logs.computeIfAbsent(timestamp, k -> new ArrayList<>()).add(id);
    }

    public List<Integer> retrieve(String start, String end, String granularity) {
        int idx = GRANULARITY_INDEX.get(granularity);

        // Trim both ends to the granularity
        String startKey = start.substring(0, idx);
        String endKey   = end.substring(0, idx);

        // subMap gives us all entries in [startKey, endKey] inclusive
        NavigableMap<String, List<Integer>> sub = logs.subMap(startKey, true, endKey, true);

        List<Integer> result = new ArrayList<>();
        for (List<Integer> ids : sub.values()) {
            result.addAll(ids);
        }
        return result;
    }
}
```

---

## 1.2 Python – List + Bisect

```python
import bisect
from collections import defaultdict
from typing import List

class LogSystem:
    GRANULARITY_INDEX = {
        "Year":    4,
        "Month":   7,
        "Day":    10,
        "Hour":   13,
        "Minute": 16,
        "Second": 19,
    }

    def __init__(self):
        # keep timestamps sorted
        self.times = []          # list of timestamp strings
        self.ids   = []          # parallel list of log IDs
        self.idx   = defaultdict(list)   # map timestamp -> list of IDs

    def put(self, id: int, timestamp: str) -> None:
        # Insert keeping times sorted
        pos = bisect.bisect_left(self.times, timestamp)
        self.times.insert(pos, timestamp)
        self.ids.insert(pos, id)
        self.idx[timestamp].append(id)

    def retrieve(self, start: str, end: str, granularity: str) -> List[int]:
        i = self.GRANULARITY_INDEX[granularity]
        start_key = start[:i]
        end_key   = end[:i]

        # find the slice of indices that fall into the range
        lo = bisect.bisect_left(self.times, start_key)
        hi = bisect.bisect_right(self.times, end_key)

        result = []
        for pos in range(lo, hi):
            result.append(self.ids[pos])
        return result
```

---

## 1.3 C++ – std::map

```cpp
#include <bits/stdc++.h>
using namespace std;

class LogSystem {
public:
    // ordered map – key = timestamp string, value = list of IDs
    map<string, vector<int>> logs;
    const unordered_map<string, int> granIndex = {
        {"Year",    4},
        {"Month",   7},
        {"Day",    10},
        {"Hour",   13},
        {"Minute", 16},
        {"Second", 19}
    };

    LogSystem() {}

    void put(int id, const string& timestamp) {
        logs[timestamp].push_back(id);
    }

    vector<int> retrieve(const string& start, const string& end, const string& granularity) {
        int idx = granIndex.at(granularity);
        string s = start.substr(0, idx);
        string e = end.substr(0, idx);

        vector<int> res;
        for (auto it = logs.lower_bound(s); it != logs.end() && it->first <= e; ++it) {
            res.insert(res.end(), it->second.begin(), it->second.end());
        }
        return res;
    }
};
```

---

# 2. Blog Article – “Design Log Storage System: The Good, The Bad, and The Ugly”

> **SEO Keywords**: Leetcode 635, Design Log Storage System, job interview, coding interview, Java TreeMap, Python bisect, C++ map, log system design, granularity, time complexity, space complexity.

---

## 2.1 The Problem in Plain English

You’re building a tiny **log‑storage** service. Every log entry comes with a unique **ID** and a **timestamp** in the format

```
YYYY:MM:DD:HH:MM:SS
```

For example: `"2017:01:01:23:59:59"`.  
You’ll receive at most **500** `put` (insert) and `retrieve` (query) calls.

A **query** asks: *give me all IDs whose timestamps lie between `start` and `end` (inclusive), but only consider the time up to a certain **granularity*** (e.g. ignore seconds if the granularity is `"Minute"`).

The challenge is to support fast queries while keeping the implementation clean.

---

## 2.2 The Naïve (but “Good Enough”) Approach

**Idea**  
*Keep everything in a simple list*.

```text
logs = []  // [(timestamp, id), ...]
```

*When you query, just scan the whole list, trim each timestamp to the granularity, and check if it falls inside the range.*

```text
for (timestamp, id) in logs:
    if trim(timestamp) in [start, end]:
        add id to answer
```

**Pros**

- Easy to write, no external libraries.
- Works because the input size is tiny (≤ 500).

**Cons**

- **O(n)** time per query – fine for 500, but terrible for larger inputs.
- No built‑in guarantee that timestamps are sorted, which can cause subtle bugs if you forget to keep the order.

> **Interview Tip** – When the interviewer says “you can use a vector/list, it will be fine”, it’s usually a red flag: the interviewer wants you to think of an *ordered* structure.

---

## 2.3 The “Ugly” Problem: Trimming Timestamps

The *trimming* operation is the heart of this problem.

| Granularity | Number of characters to keep |
|-------------|------------------------------|
| Year | 4 (`YYYY`) |
| Month | 7 (`YYYY:MM`) |
| Day | 10 (`YYYY:MM:DD`) |
| Hour | 13 (`YYYY:MM:DD:HH`) |
| Minute | 16 (`YYYY:MM:DD:HH:MM`) |
| Second | 19 (`YYYY:MM:DD:HH:MM:SS`) |

So `trim("2017:01:01:23:59:59", "Minute")` → `"2017:01:01:23:59"`.

If you don’t do this correctly, you’ll return *wrong* results or miss logs that should be included.

---

## 2.4 The Efficient Solution – Using an **Ordered Map**  

The trick is that timestamps are **lexicographically sortable**.  
If we keep them in a data structure that maintains order, we can cut the query time dramatically.

### 2.4.1 Why a Tree / BST Works

1. **Sorted order**  
   Every timestamp can be compared as a string. The natural order of the string is the same as the chronological order because all components are zero‑padded.

2. **Range queries**  
   In a *balanced BST* (Java’s `TreeMap`, C++’s `std::map`, Python’s `sortedcontainers` or even a plain `list` with `bisect`) we can locate the *first* element ≥ `start` in *O(log n)* time.

3. **Inclusive boundaries**  
   The library functions (`subMap` in Java, `lower_bound`/`upper_bound` in C++/Python) let us fetch *exactly* the keys we care about.

### 2.4.2 Full Java Implementation (TreeMap)

```java
TreeMap<String, List<Integer>> logs;
```

*Insertion* – `put`  
`computeIfAbsent` + `add` – **O(log n)**

*Query* – `retrieve`  

1. Compute the slice index for the requested granularity.  
2. Truncate `start` and `end`.  
3. Use `subMap(startKey, true, endKey, true)` to get the exact slice in **O(log n)**.  
4. Flatten the lists of IDs.

Result: **O(log n + k)** per query (`k` = number of matching logs).

---

## 2.5 Python Special: `bisect` on a Sorted List

Python’s `bisect` module lets us do binary search on a **plain list**.  
The trick is to keep **two parallel lists**:

```text
times = sorted timestamps
ids   = parallel list of IDs
```

*Inserting* – `bisect.insort` – *O(n)* (shifts elements), but still fine for 500 operations.

*Querying* – `bisect_left/right` to find the slice of indices that belong to the truncated range, then just collect the IDs.

The overall time per query is `O(log n + k)` – fast enough for LeetCode and nice to show in an interview.

---

## 2.6 Edge‑Case Checklist

| Edge Case | Why it matters | Fix |
|-----------|----------------|-----|
| **Same timestamp inserted twice** | Our map values are *lists* – we preserve order. | `computeIfAbsent` (Java), `log[timestamp].push_back(id)` (C++). |
| **Granularity = “Second”** | No trimming – full timestamp used. | `index = 19`. |
| **Granularity = “Year”** | Truncate to 4 chars. | `index = 4`. |
| **Start > End** | The query should return empty. | `subMap`/`lower_bound` will produce an empty range. |
| **No logs in range** | Must return an empty list, not `null`. | Iterate over empty slice – returns empty vector/list. |

---

## 2.7 Common Interview Follow‑ups

| Question | What you’re really being asked |
|----------|---------------------------------|
| “What if we had 1 000 000 logs?” | Test your ability to reason about *log‑n* vs *n* performance. |
| “Can you do the query in O(1)?” | Trick question – you can’t skip the binary search if you need to keep order. |
| “How would you support deletion?” | In the BST solution you’d pop the ID from the vector or erase the key if the vector becomes empty. |
| “What if timestamps weren’t zero‑padded?” | You’d need a custom comparator or parse into a `datetime` object. |
| “Could you pre‑bucket logs by granularity?” | Yes, you can keep six arrays (`year`, `month`, …) and binary‑search on the appropriate one. It saves trimming but uses more space. |

---

## 2.8 Final Take‑away

1. **Trimming** – A simple string slice is enough because the format is fixed.  
2. **Ordered Map** – The core of the efficient solution.  
3. **Complexities** – With `TreeMap`/`std::map`, queries run in *O(log n + k)*, insertions in *O(log n)*.  
4. **Coding Interview** – Show the interviewer that you understand why order matters; ask clarifying questions about constraints and possible extensions.  

If you can write a clean Java `TreeMap` solution (like the one above) and explain the trimming logic, you’ll nail this LeetCode interview question and impress any hiring manager who loves good data‑structure design.

Happy coding – and good luck on the next interview!