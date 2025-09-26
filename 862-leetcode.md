---
title: LeetCode 862. Shortest Subarray with Sum at Least K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **LeetCode 862 – Shortest Subarray with Sum at Least K**

**Key Idea**  
The sum of a subarray \([i+1, j]\) can be computed quickly as  
`pref[j] – pref[i]`, where `pref` is a prefix‑sum array.  
To find the *shortest* such subarray, we maintain a **monotonic deque** of prefix‑sum indices:

* **Front** – gives the smallest prefix that might form a valid subarray ending at the current index.  
* **Back** – we discard indices whose prefix sum is not smaller than the current one, because they can’t produce a shorter subarray later.

---

### Algorithm
```
1. Build prefix array pref[0…n] (pref[0] = 0).
2. minLen ← ∞
3. For j = 0 … n:
     • While deque not empty and pref[j] - pref[dq.front()] ≥ k:
           minLen ← min(minLen, j - dq.front())
           dq.pop_front()                // front no longer useful
     • While deque not empty and pref[j] ≤ pref[dq.back()]:
           dq.pop_back()                 // maintain increasing prefix sums
     • dq.push_back(j)
4. Return (minLen == ∞ ? -1 : minLen)
```

---

### Why It Works

* `pref[j] - pref[dq.front()] ≥ k` → subarray `[dq.front()+1 … j]` has sum ≥ k.  
  Removing it from the front can only make future candidates longer, so it’s safe to pop.  
* Popping from the back keeps only the *minimal* prefix sums at earlier indices, ensuring any later subarray will be at least as short if it starts after one of those indices.

---

### Complexity
* **Time:** `O(n)` – each index enters and leaves the deque at most once.  
* **Space:** `O(n)` – prefix array + deque.

---

**Bottom line:** Use a prefix sum array together with a monotonic deque to greedily check and update the shortest subarray, achieving linear time and space.