---
title: LeetCode 1817. Finding the Users Active Minutes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1817 – Finding the Users’ Active Minutes  
*A deep‑dive into the problem, the cleanest solution, and a SEO‑friendly blog post that can land you a job interview.*

---

## 1. Problem Overview

> **LeetCode 1817 – Finding the Users Active Minutes**  
> *Medium*  

You are given an array `logs` where `logs[i] = [userId, minute]` represents that a user performed an action at that minute.  
The *User Active Minutes* (UAM) of a user is the **count of distinct minutes** the user was active.  
Given an integer `k`, you must return a 1‑indexed array `answer` of size `k` such that `answer[j]` is the number of users whose UAM equals `j` (for `1 ≤ j ≤ k`).  

**Constraints**  

```
1 ≤ logs.length ≤ 10^4
0 ≤ userId ≤ 10^9
1 ≤ minute ≤ 10^5
1 ≤ k ≤ 10^5
```

---

## 2. Straightforward Approach

The naïve solution would:

1. Iterate over every log entry.
2. Keep a map from userId → set of minutes.
3. After processing all logs, the size of each set is the user’s UAM.
4. Increment the counter in `answer` accordingly.

This is exactly the classic “unique‑count per key” pattern and runs in **O(n)** time and **O(n)** space, where `n = logs.length`.  

---

## 3. Optimal Solution (HashMap + HashSet)

The optimal solution follows the same idea but with a few micro‑optimizations:

| Step | Code | Why |
|------|------|-----|
| 1 | `map.computeIfAbsent(id, x -> new HashSet<>()).add(minute)` | Avoids explicit `containsKey` checks. |
| 2 | After building the map, iterate over `map.values()` | Directly get the set size; no need to look up keys again. |
| 3 | `answer[setSize-1]++` | 0‑indexed array but the problem expects 1‑indexed. |

The time complexity is **O(n)** (each log processed once).  
The space complexity is **O(n + k)** – we store at most one entry per log and the final answer array of length `k`.

---

## 4. Edge‑Case “Ugly” Pitfalls

| Problem | Why it matters | Fix |
|---------|----------------|-----|
| **Very large userId values** | HashMap works fine, but some languages might default to 32‑bit integers. | Use `long`/`long long` if the language default is 32‑bit. |
| **Duplicate minutes for a user** | If we forget the set, duplicates inflate UAM. | Always use a set (or deduplicate with sorting). |
| **k smaller than the maximum UAM** | The problem guarantees `k` ≥ max UAM, but if not, index out‑of‑bounds occurs. | Add a guard or validate input. |
| **Huge `k` (e.g., 10^5) with very few users** | Memory wasted on empty counts. | This is unavoidable for the required output format, but the array is still lightweight. |

---

## 5. Code Implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.

### 5.1 Java

```java
import java.util.*;

class Solution {
    public int[] findingUsersActiveMinutes(int[][] logs, int k) {
        // Map: userId -> Set of distinct minutes
        Map<Integer, Set<Integer>> userMinutes = new HashMap<>();

        for (int[] log : logs) {
            int id   = log[0];
            int minute = log[1];
            userMinutes.computeIfAbsent(id, x -> new HashSet<>()).add(minute);
        }

        int[] answer = new int[k];
        for (Set<Integer> minutes : userMinutes.values()) {
            int uam = minutes.size();      // UAM for this user
            answer[uam - 1]++;              // 1‑indexed requirement
        }
        return answer;
    }
}
```

> **Complexities**  
> *Time*: `O(n)` – one pass through logs, one pass through map values.  
> *Space*: `O(n + k)` – map stores at most `n` entries, answer array size `k`.

---

### 5.2 Python

```python
from collections import defaultdict
from typing import List

class Solution:
    def findingUsersActiveMinutes(self, logs: List[List[int]], k: int) -> List[int]:
        # user_id -> set of minutes
        minutes = defaultdict(set)

        for user_id, minute in logs:
            minutes[user_id].add(minute)

        answer = [0] * k
        for s in minutes.values():
            uam = len(s)
            answer[uam - 1] += 1   # 1‑indexed

        return answer
```

> **Complexities**  
> *Time*: `O(n)`  
> *Space*: `O(n + k)`  

---

### 5.3 C++

```cpp
#include <unordered_map>
#include <unordered_set>
#include <vector>

class Solution {
public:
    std::vector<int> findingUsersActiveMinutes(std::vector<std::vector<int>>& logs, int k) {
        // userId -> set of minutes
        std::unordered_map<int, std::unordered_set<int>> mp;

        for (const auto& log : logs) {
            int id = log[0];
            int minute = log[1];
            mp[id].insert(minute);
        }

        std::vector<int> answer(k, 0);
        for (const auto& p : mp) {
            int uam = p.second.size();
            if (uam >= 1 && uam <= k) {
                answer[uam - 1]++;          // 1‑indexed
            }
        }
        return answer;
    }
};
```

> **Complexities**  
> *Time*: `O(n)`  
> *Space*: `O(n + k)`  

---

## 6. Blog Post: The Good, The Bad, and The Ugly

### Title  
**“LeetCode 1817: Finding the Users Active Minutes – A Deep Dive & Interview‑Ready Solution”**

### Meta Description  
Learn how to solve LeetCode 1817 in Java, Python, and C++. Understand the algorithm, time/space trade‑offs, and interview‑style pitfalls to avoid. Boost your coding interview skills today!

### Headings

| Heading | Purpose |
|---------|---------|
| **Introduction** | Set the context and importance of the problem. |
| **Problem Summary** | Concise restatement for SEO. |
| **Key Observations** | Highlight the “unique minutes” insight. |
| **Optimal Approach** | Explain the hashmap+set solution. |
| **Complexity Analysis** | Show time/space trade‑offs. |
| **Edge‑Case Gotchas** | List “bad” and “ugly” pitfalls. |
| **Code Samples** | Provide clean code for Java, Python, C++. |
| **Interview Tips** | How to explain and defend your solution. |
| **Conclusion** | Wrap up and call to action. |

### Sample Blog Body

> **Introduction**  
> In coding interviews, problems that involve “counting unique events per key” pop up often. LeetCode’s *Finding the Users Active Minutes* (Problem 1817) is a textbook example. Mastering it not only wins you a high score on LeetCode but also demonstrates to hiring managers that you understand hash‑based aggregation, time‑space trade‑offs, and robust coding practices.  

> **Problem Summary**  
> You’re given a log of `[userId, minute]` pairs. A user’s *User Active Minutes* (UAM) is the number of **distinct** minutes the user was active. Return an array where `answer[j]` is the number of users whose UAM equals `j` (1‑indexed).  

> **Key Observations**  
> * The crux is deduplication of minutes per user.  
> * We can treat each user independently – no cross‑user interactions matter.  
> * Once we know each user’s UAM, we simply bucket‑count the results.  

> **Optimal Approach**  
> The canonical solution uses a `HashMap` (or `unordered_map` in C++) that maps `userId` → `HashSet` (or `unordered_set`) of minutes.  
> * Iterate over the logs once, inserting the minute into the set.  
> * After the loop, the size of each set is the user’s UAM.  
> * Increment the corresponding index in the answer array (`answer[uam-1]++`).  

> **Complexity Analysis**  
> *Time*: **O(n)** – each log entry is processed exactly once.  
> *Space*: **O(n + k)** – the map stores at most one entry per log, and the answer array requires `k` slots.  

> **Edge‑Case Gotchas**  
> 1. **Duplicate minutes** – Forgetting the set will inflate counts.  
> 2. **Large userId** – In languages with 32‑bit defaults, use 64‑bit integers to avoid overflow.  
> 3. **k < max UAM** – The problem guarantees `k` is large enough, but defensive checks can prevent crashes.  

> **Code Samples**  
> *(Insert Java, Python, C++ snippets from section 5 above)*  

> **Interview Tips**  
> * Start by describing the data‑structure choice and why deduplication matters.  
> * Highlight the `O(n)` time guarantee – interviewers love linear solutions.  
> * Mention potential memory optimizations (e.g., using a `vector<unordered_set<int>>` if you know user IDs are dense).  

> **Conclusion**  
> Problem 1817 is deceptively simple but packs in a lot of interview‑relevant concepts. By writing the solution in Java, Python, and C++, you’ll showcase versatility to recruiters across tech stacks. Practice it, time yourself, and share your results – you’ll be one step closer to landing that dream job.  

---

## 7. Final Thoughts

- The **hash‑map + hash‑set** approach is the most natural and efficient solution.  
- Always remember the importance of *deduplication* when counting distinct values.  
- Edge‑case awareness protects you from “ugly” bugs that could ruin an interview.  
- Provide clean, language‑specific code to impress interviewers and peers alike.

Happy coding! 🚀

--- 

*Prepared by ChatGPT – Powered by OpenAI.*