---
title: LeetCode 683. K Empty Slots - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 683 – **K Empty Slots**  
> **Hard** – 2 × 10⁴ ≤ n, k ≤ 2 × 10⁴  

> **Goal:**  
> Find the **earliest day** on which two bulbs that are **k + 1** positions apart are both ON and all bulbs between them are OFF.

---

## 1. Quick Solution Idea  
1. **Keep track of bulbs that are already ON** in a *balanced binary search tree* (`TreeSet` / `set` / `SortedSet`).  
2. When a new bulb at position `p` is turned ON:  
   * Look at the **closest ON bulb on the left** (`lower(p)`).  
   * Look at the **closest ON bulb on the right** (`higher(p)`).  
3. If either of the two neighbors is exactly `k + 1` positions away → return the current day.  
4. If the loop finishes → return `-1`.

> **Why it works?**  
> The only way a pair of ON bulbs can have *exactly* `k` OFF bulbs between them is if there is no other ON bulb in that interval.  
> The closest ON bulb on each side is the *only* candidate to satisfy the condition.

> **Complexities**  
> *Time*: **O(n log n)** – each insert & lookup is `log n`.  
> *Space*: **O(n)** – we store every bulb once.

---

## 2. Code Implementations  

### 2.1 Java 17  
```java
import java.util.TreeSet;

public class Solution {
    public int kEmptySlots(int[] bulbs, int k) {
        TreeSet<Integer> on = new TreeSet<>();
        for (int day = 1; day <= bulbs.length; day++) {
            int pos = bulbs[day - 1];
            on.add(pos);

            Integer left = on.lower(pos);
            Integer right = on.higher(pos);

            if (left != null && pos - left - 1 == k) return day;
            if (right != null && right - pos - 1 == k) return day;
        }
        return -1; // never found
    }
}
```

### 2.2 Python 3 (bisect)  
```python
from bisect import bisect_left

class Solution:
    def kEmptySlots(self, bulbs: list[int], k: int) -> int:
        on = []          # sorted list of ON positions
        for day, pos in enumerate(bulbs, 1):
            i = bisect_left(on, pos)
            on.insert(i, pos)

            # left neighbour
            if i > 0 and pos - on[i-1] - 1 == k:
                return day
            # right neighbour
            if i+1 < len(on) and on[i+1] - pos - 1 == k:
                return day
        return -1
```

### 2.3 C++17 (`std::set`)  
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int kEmptySlots(vector<int>& bulbs, int k) {
        set<int> on;
        for (int day = 1; day <= bulbs.size(); ++day) {
            int pos = bulbs[day-1];
            on.insert(pos);

            auto it = on.lower_bound(pos);

            // left neighbour
            if (it != on.begin()) {
                int left = *prev(it);
                if (pos - left - 1 == k) return day;
            }

            // right neighbour
            auto nxt = next(it);
            if (nxt != on.end()) {
                int right = *nxt;
                if (right - pos - 1 == k) return day;
            }
        }
        return -1;
    }
};
```

---

## 3. Blog Article – “The Good, the Bad, and the Ugly of LeetCode 683”

> **Keywords**: *LeetCode 683*, *K Empty Slots*, *hard interview problem*, *Java TreeSet*, *Python bisect*, *C++ set*, *binary search tree*, *job interview algorithm*  

---

### 3.1 Introduction  
In every software‑engineering interview, the interviewer likes to mix a classic *array problem* with a *data‑structure twist*. LeetCode 683 “K Empty Slots” is one such problem. It forces you to think about *dynamic updates* and *nearest‑neighbor queries* – skills that show up in real‑world systems like cache eviction, scheduling, and time‑series analytics.

> **Why this problem matters**  
> • Demonstrates your understanding of **ordered sets** and **search‑space reduction**.  
> • Tests your ability to **optimize** an O(n²) brute force into O(n log n).  
> • Exposes you to the subtlety of **boundary conditions** (k = 0, n = 1, etc.).

---

### 3.2 The Naïve (Bad) Approach  
A textbook O(n²) solution is to, for each day, check every pair of ON bulbs and count OFF bulbs between them.  
```text
for day in 1..n:
    for i in 1..day:
        for j in i+1..day:
            if bulbs[i] and bulbs[j] are on:
                if count_off_between(i, j) == k:
                    return day
```
- **Time**: O(n³) – too slow for n = 20 000.  
- **Space**: O(1).  

> *Why it fails*: The double loop already blows up the runtime. Modern interviewers expect you to improve this.

---

### 3.3 The Efficient (Good) Approach  
Using a **balanced BST** (Java `TreeSet`, C++ `std::set`, Python `bisect`) lets us:

1. Insert each bulb position in **O(log n)**.  
2. Find the *nearest* ON bulb to the left/right in **O(log n)**.  
3. Compare distances.

> *Result*: **O(n log n)** time, **O(n)** space.  
> This is the fastest simple solution you can write without resorting to more exotic data structures (segment tree, binary indexed tree, or union‑find with lazy updates).  

---

### 3.4 Edge Cases & Pitfalls (The Ugly)  

| Scenario | What to Watch For | Fix |
|----------|-------------------|-----|
| `k == 0` | You’re looking for adjacent ON bulbs. | The code still works because `pos - left - 1` will be `0`. |
| `n == 1` | No pair exists → always `-1`. | The loop exits with `-1`. |
| Duplicate bulb numbers | The problem guarantees a permutation, so this won’t happen, but in a real system you’d need to guard against it. | Use a `Set` to detect duplicates early. |
| Very large `k` (e.g., `k > n`) | No such pair can exist. | Optional pre‑check `if k >= n: return -1`. |
| Off‑by‑one errors | Remember that positions in `bulbs` are **1‑indexed**, but our loop indices are **0‑based**. | Use `int pos = bulbs[day - 1];` and `for (int day = 1; day <= bulbs.length; day++)`. |

---

### 3.5 What Interviewers Actually Look For  

1. **Correctness**: Return the *earliest* day, not just any day.  
2. **Complexity**: Explain why O(n log n) is acceptable for n = 20 000.  
3. **Data‑structure choice**: Justify why a BST is a natural fit (nearest neighbor queries).  
4. **Code readability**: Use meaningful variable names (`on`, `left`, `right`) and comments.  

---

### 3.6 Takeaway for Your Next Interview  

- Master the idea of *“nearest neighbour in a sorted collection”*; it pops up in scheduling, networking, and even game‑development.  
- Practice implementing the same logic in **Java, Python, and C++** – you’ll be ready for language‑agnostic questions.  
- Remember to discuss **edge cases**; interviewers love to see you think about them.  

---

## 4. Final Thoughts  

LeetCode 683 is more than just a “bulb‑turning” puzzle. It’s a micro‑cosm of real‑world algorithmic problems: you have a dynamic set of items and need to answer range queries quickly. By mastering this problem, you’ll demonstrate both theoretical understanding and practical coding chops—exactly what hiring managers look for.

Good luck on your job hunt! 🚀