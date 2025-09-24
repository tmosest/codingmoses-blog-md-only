---
title: LeetCode 3679. Minimum Discards to Balance Inventory - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📦 3679 – Minimum Discards to Balance Inventory  
**Medium | 10‑15 min solution | O(n) time, O(n + U) space**

---

### 1. Problem Recap

You are given:

* `arrivals` – an array of integers (`1 ≤ arrivals[i] ≤ 10⁵`) where each entry is the **type** of item that arrives on day `i` (1‑indexed).  
* `w` – the size of the sliding window (last `w` days).  
* `m` – the maximum allowed number of **kept** items of the same type in any window.

For every day `i` we look at the window  
`[max(1, i – w + 1) , i]`.  
If we keep the arrival on day `i` and it would cause the count of its type in this window to exceed `m`, we *must* discard it.  
We want the **minimum** number of discards needed so that *every* window satisfies the rule.

---

### 2. Key Insight

The only thing that matters is the *current* window.  
When we move from day `i` to `i+1`:

1. The day `i+1-w` leaves the window (if it exists).  
2. We decide whether to keep or discard the arrival on day `i+1`.

If we keep an item only when it does **not** violate the rule, we never waste a discard – this greedy strategy is optimal.  
So we just need a sliding window with a frequency counter.

---

### 3. Algorithm

```text
kept[i]   – boolean array, true if we keep the item on day i
cnt[type] – hash map (or array) that holds how many kept items of that type are currently inside the window
discard   – counter of discarded items

for i from 0 to n-1:          // 0‑based index = day i+1
    // 1. Remove the day that leaves the window
    idx = i - w
    if idx >= 0 and kept[idx] == true:
        cnt[arrivals[idx]] -= 1

    // 2. Try to keep the current arrival
    t = arrivals[i]
    if cnt[t] < m:
        kept[i] = true
        cnt[t] += 1
    else:
        discard += 1          // must discard
return discard
```

* **Time** – O(n) (each day does only a constant amount of work).  
* **Space** – O(n + U) where `U` is the number of distinct item types  
  (`kept` is `n`, `cnt` is `U`).

---

### 4. Code

Below you’ll find three fully‑working implementations – one each in **Java**, **Python**, and **C++**.

---

#### 4.1 Java (LeetCode‑style)

```java
import java.util.*;

public class Solution {
    public int minArrivalsToDiscard(int[] arrivals, int w, int m) {
        int n = arrivals.length;
        boolean[] kept = new boolean[n];
        Map<Integer, Integer> cnt = new HashMap<>();
        int discard = 0;

        for (int i = 0; i < n; i++) {
            int idx = i - w;
            if (idx >= 0 && kept[idx]) {
                int old = arrivals[idx];
                cnt.put(old, cnt.get(old) - 1);
            }

            int type = arrivals[i];
            int cur = cnt.getOrDefault(type, 0);
            if (cur < m) {
                kept[i] = true;
                cnt.put(type, cur + 1);
            } else {
                discard++;
            }
        }
        return discard;
    }
}
```

---

#### 4.2 Python

```python
class Solution:
    def minArrivalsToDiscard(self, arrivals: list[int], w: int, m: int) -> int:
        n = len(arrivals)
        kept = [False] * n
        freq = {}
        discard = 0

        for i in range(n):
            idx = i - w
            if idx >= 0 and kept[idx]:
                old = arrivals[idx]
                freq[old] -= 1

            t = arrivals[i]
            cur = freq.get(t, 0)
            if cur < m:
                kept[i] = True
                freq[t] = cur + 1
            else:
                discard += 1
        return discard
```

---

#### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minArrivalsToDiscard(vector<int>& arrivals, int w, int m) {
        int n = arrivals.size();
        vector<char> kept(n, 0);                     // 0 = discarded, 1 = kept
        unordered_map<int, int> cnt;                // type -> count in window
        int discard = 0;

        for (int i = 0; i < n; ++i) {
            int idx = i - w;
            if (idx >= 0 && kept[idx]) {
                int old = arrivals[idx];
                if (--cnt[old] == 0) cnt.erase(old);
            }

            int t = arrivals[i];
            if (cnt[t] < m) {
                kept[i] = 1;
                ++cnt[t];
            } else {
                ++discard;
            }
        }
        return discard;
    }
};
```

All three solutions run in **O(n)** time and use **O(n + U)** memory, perfectly matching the constraints (`n ≤ 10⁵`).

---

### 5. Blog‑Style Explanation

> **Title**: *Balancing Inventory in O(n): The 3679 Minimum Discards Problem Explained*

---

#### 5.1 Introduction

If you’re a software engineer chasing a data‑structures & algorithms role, you’ve likely seen LeetCode’s “Sliding Window” problems.  
The “Minimum Discards to Balance Inventory” (LeetCode 3679) is a classic example that blends greedy decisions with a sliding‑window counter.  

In this article we’ll:

* Understand the problem from a business perspective.
* Walk through the greedy algorithm step by step.
* Highlight pitfalls (the “bad” and “ugly” parts).
* Present clean, production‑ready code in Java, Python, and C++.
* Sprinkle some SEO‑friendly keywords to help recruiters find you.

---

#### 5.2 Real‑World Analogy

Imagine a warehouse that receives shipments every day.  
The manager can keep or discard each shipment on the same day.  
However, for *any* recent 7‑day period, no single product type can exceed a maximum of 5 kept items.  

If the 7‑day window already has 5 units of product “X” and a new shipment of “X” arrives, you **must** discard it—otherwise the rule is violated.  

You want to discard the *fewest* shipments possible, because each discarded shipment is a lost sale.

---

#### 5.3 The Greedy Insight

Why is it enough to “discard only when forced”?

* Suppose we *keep* an item that would cause the window count to exceed `m`.  
  That violates the rule, so such a state is illegal.  
* If we *discard* an item that could have been kept, we unnecessarily increase the discard count.  
  We could have kept it and still stayed legal.

Thus the only legal action at each day is:

```
if (current count < m) keep
else                     discard
```

This simple rule guarantees we never waste a discard, so it yields the *minimum* discards.

---

#### 5.4 Why a Sliding Window Is Needed (The “Good” Part)

The window size `w` can be as large as `10⁵`.  
Naïvely recomputing the count of every type in every window would cost `O(n·w)`, which is impossible.  

A *sliding window* lets us:

1. **Add** the new day’s item in O(1).  
2. **Remove** the item that leaves the window in O(1).  

We only keep a counter for the *currently kept* items, not the entire array.  
That reduces the time complexity from quadratic to linear.

---

#### 5.5 Common Pitfalls (The “Bad” & “Ugly” Parts)

| Problem | Bad/ Ugly Practice | Fix |
|---------|--------------------|-----|
| **Using a queue for the window** | A queue adds/removes O(1) but stores *all* days, making the solution memory‑heavy. | Use a boolean array (`kept`) instead of storing every day in a queue. |
| **Mutable counters in a dictionary** | Forgetting to erase zero‑entries can bloat the map and hurt constant factors. | Check for `0` and `erase` the key. |
| **Off‑by‑one index errors** | Using 1‑based logic on 0‑based arrays leads to subtle bugs. | Explicitly convert day `i` to 0‑based index and compute `idx = i - w`. |
| **Unnecessary array allocation** | Allocating an array of size `n` just to know if we kept an item is wasteful when `n` is huge. | Use a `boolean[]` (or `char`) – it’s minimal and fast. |

---

#### 5.4 Code Walk‑Through (Java)

```java
for (int i = 0; i < n; i++) {
    int idx = i - w;                       // day that leaves the window
    if (idx >= 0 && kept[idx]) {           // it was kept, so we remove its count
        int old = arrivals[idx];
        cnt.put(old, cnt.get(old) - 1);
    }

    int type = arrivals[i];                // current day’s item type
    int cur  = cnt.getOrDefault(type, 0);
    if (cur < m) {                         // safe to keep
        kept[i] = true;
        cnt.put(type, cur + 1);
    } else {                               // forced to discard
        discard++;
    }
}
```

* **Why is this O(n)?**  
  Each iteration does a constant number of hash‑map operations.  
* **Why does it work?**  
  Because the only possible violation is handled immediately.

---

#### 5.5 Complexity Summary

| Metric | Value |
|--------|-------|
| **Time** | **O(n)** (linear) |
| **Space** | **O(n + U)** (array + hash‑map) |
| **Why it matters** | Meets LeetCode’s hard limits (`n ≤ 10⁵`) and is fast enough for real‑time inventory systems. |

---

#### 5.6 SEO & Recruitment Hooks

* **Keywords**: “Sliding window algorithm”, “minimum discards”, “greedy algorithm”, “Java sliding window”, “Python data structures”, “C++ hash map”, “LeetCode 3679”, “inventory management problem”, “optimal discard strategy”.
* **Tagline**: “Want to ace LeetCode’s sliding‑window problems? Check out the 3679 Minimum Discards solution – Java, Python, C++ – O(n) time, optimal discards!”
* **Meta‑description** (for search engines):  
  *“Learn how to solve LeetCode 3679 – Minimum Discards to Balance Inventory – with a greedy sliding‑window approach. Get clean Java, Python, and C++ code, plus an in‑depth explanation that’s perfect for recruiters.”*

---

#### 5.7 Take‑away Checklist

✅ **Greedy is optimal** – keep only when you’re *not* forced to discard.  
✅ **Slide the window** – remove the day that exits (`i - w`).  
✅ **Frequency map** – track counts of kept types inside the window.  
✅ **Avoid off‑by‑one bugs** – convert day numbers correctly.  
✅ **Use minimal memory** – a boolean array for “kept” + a hash map for frequencies.

---

#### 5.8 Closing

Whether you’re tackling a coding interview, building a warehouse management micro‑service, or just sharpening your data‑structure skills, the 3679 problem is a great showcase of **greedy + sliding window**.  

Drop a ⭐️ on the LeetCode problem, share this article, and feel free to tweak the code for your own stack.  

**Happy coding! 🚀**  

---

### 6. Quick Reference

| Section | Link |
|---------|------|
| **Java solution** | `[Code]` |
| **Python solution** | `[Code]` |
| **C++ solution** | `[Code]` |
| **Full article** | *Copy‑paste into your blog or Medium post* |

---