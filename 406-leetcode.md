---
title: LeetCode 406. Queue Reconstruction by Height - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Queue Reconstruction by Height – LeetCode 406  
*The Good, The Bad, and The Ugly – A Deep‑Dive + Full Java/Python/C++ Solutions*  

---

### 📌 TL;DR  
> **Problem:** Reconstruct a queue given `[height, k]` pairs where `k` is the number of people in front who are **taller or equal**.  
> **Solution:** Sort people **decreasing height** (ties → increasing `k`) and insert each person at index `k`.  
> **Complexity:** `O(n²)` time, `O(n)` space (simple greedy).  
> **Variants:** `O(n log n)` using a Binary Indexed Tree / Segment Tree.  

---

## 1. Introduction  

When searching for a front‑end job, recruiters love “LeetCode‑style” problems that showcase *clever* solutions.  
“Queue Reconstruction by Height” is a classic medium problem that is often asked in interviews.  

In this article, we’ll:

1. Understand the problem and its constraints.  
2. Walk through the greedy “sort‑and‑insert” algorithm (the *good* part).  
3. Discuss pitfalls, edge cases, and why some people think it’s the *bad* part.  
4. Explore the *ugly* – the complexity bottleneck and more advanced data‑structures.  
5. Provide ready‑to‑copy code in **Java**, **Python**, and **C++**.  
6. Offer tips on making the post SEO‑friendly (keywords, meta‑description, headings, etc.) to attract hiring managers.

---

## 2. Problem Statement  

Given an array `people` of `n` pairs `[hᵢ, kᵢ]`:

- `hᵢ` – height of the i‑th person.  
- `kᵢ` – number of people in front of the i‑th person whose height is **≥** `hᵢ`.  

Reconstruct the queue that satisfies all pairs.  
Return the queue as a 2‑D array `queue[j] = [hⱼ, kⱼ]` where `queue[0]` is the front.

**Constraints**

| Constraint | Value |
|------------|-------|
| `1 ≤ n ≤ 2000` | |
| `0 ≤ hᵢ ≤ 10⁶` | |
| `0 ≤ kᵢ < n` | |
| A valid reconstruction always exists. | |

---

## 3. Intuition – Why “Sort & Insert” Works  

1. **Fix the tallest people first.**  
   For the tallest person, the only way to satisfy `k` is to place him at position `k`.  
   Since nobody is taller, `k` counts only himself.

2. **Insert shorter people later.**  
   When inserting a shorter person, the already placed taller people will not affect `k` because they’re taller or equal.  
   By inserting at the exact index `k`, we guarantee that exactly `k` taller or equal persons are ahead.

3. **Sorting rule**  
   *Sort by descending height* → tallest first.  
   *Ties*: sort by ascending `k` so that people with the same height but smaller `k` are placed earlier, ensuring correctness.

---

## 4. Algorithm (Greedy)  

```text
sort people by:
    1. height descending
    2. k ascending (for equal heights)

queue = empty list
for each person (h, k) in sorted list:
    insert person at index k in queue

return queue
```

**Why `O(n²)`?**  
Inserting into an array at a random index costs `O(n)` due to shifting.  
Doing this `n` times yields `O(n²)`.

**Space:**  
We use an additional list for the output – `O(n)`.

---

## 5. Code – 3 Languages  

### 5.1 Java (LinkedList insertion)

```java
import java.util.*;

public class Solution {
    public int[][] reconstructQueue(int[][] people) {
        // 1. Sort by height descending, k ascending
        Arrays.sort(people, new Comparator<int[]>() {
            public int compare(int[] a, int[] b) {
                if (a[0] != b[0]) return b[0] - a[0];   // height descending
                return a[1] - b[1];                     // k ascending
            }
        });

        // 2. Insert at index k using LinkedList (O(1) per insertion)
        List<int[]> result = new LinkedList<>();
        for (int[] p : people) {
            result.add(p[1], p);
        }

        // 3. Convert to int[][]
        int[][] ans = new int[people.length][2];
        for (int i = 0; i < result.size(); i++) {
            ans[i] = result.get(i);
        }
        return ans;
    }
}
```

> **Why LinkedList?**  
> Inserting into a `LinkedList` at a known index is `O(1)` once you have the node.  
> But because we use `List.add(index, element)`, Java still walks to the index (`O(n)`) – the same asymptotic cost.  
> The code is concise and mirrors the typical LeetCode solution.

---

### 5.2 Python (list insertion)

```python
class Solution:
    def reconstructQueue(self, people: List[List[int]]) -> List[List[int]]:
        # Sort: height descending, k ascending
        people.sort(key=lambda x: (-x[0], x[1]))

        res = []
        for h, k in people:
            res.insert(k, [h, k])   # O(n) per insert
        return res
```

---

### 5.3 C++ (vector insertion)

```cpp
class Solution {
public:
    vector<vector<int>> reconstructQueue(vector<vector<int>>& people) {
        // 1. Sort by height descending, k ascending
        sort(people.begin(), people.end(),
            [](const vector<int>& a, const vector<int>& b) {
                if (a[0] != b[0]) return a[0] > b[0]; // height descending
                return a[1] < b[1];                  // k ascending
            });

        // 2. Insert at index k
        vector<vector<int>> res;
        for (auto &p : people) {
            res.insert(res.begin() + p[1], p); // O(n) per insert
        }
        return res;
    }
};
```

---

## 6. The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | Simple `O(n²)` – acceptable for `n ≤ 2000`. | Not optimal for very large `n`. | For `n` up to 10⁵, `O(n²)` would time‑out. |
| **Space** | `O(n)` – trivial. | None. | None. |
| **Implementation Simplicity** | One sort + single loop. | Requires careful sorting comparator. | Need to implement segment tree / BIT for `O(n log n)`. |
| **Readability** | Clear, “greedy” nature. | Sorting rule may confuse beginners. | Segment tree code is verbose. |
| **Edge Cases** | Handles ties correctly (height same). | None. | None. |
| **Real‑world Applicability** | Good for interview demos. | Not scalable for huge datasets. | Requires data‑structure knowledge. |

---

## 7. Optimizing to O(n log n) – The “Segment Tree” Variant  

When `n` is large, we can maintain a **Binary Indexed Tree (Fenwick)** or **Segment Tree** to find the `k`‑th empty position in `O(log n)`.  
The idea:

1. Sort as before (height descending, k ascending).  
2. Initially, all positions are free (`1`).  
3. For each person, find the index of the `(k+1)`‑th free slot.  
4. Mark that slot as occupied.  

This reduces the overall complexity to `O(n log n)`.

> *Why the article?*  
> It demonstrates a deeper data‑structure skill set – often sought by hiring managers.

---

## 8. SEO & Interview‑Ready Blog Tips  

| Element | Recommendation |
|---------|----------------|
| **Title** | “Queue Reconstruction by Height (LeetCode 406) – Java / Python / C++ Solution” |
| **Meta Description** | “Learn the greedy approach to LeetCode 406 – Queue Reconstruction by Height. Full code in Java, Python, and C++. Understand time complexity, edge cases, and advanced optimizations.” |
| **Headings** | H1 for title, H2 for sections, H3 for sub‑sections. |
| **Keywords** | `Queue Reconstruction by Height`, `LeetCode 406`, `Java solution`, `Python solution`, `C++ solution`, `greedy algorithm`, `segment tree`, `binary indexed tree`. |
| **Code Formatting** | Triple backticks with language identifiers. |
| **Call‑to‑Action** | “If you’re preparing for coding interviews, add this problem to your practice list.” |
| **Images** | Optional diagram showing the sorting and insertion process. |
| **Internal Links** | Link to other algorithm blogs (“Sorting tricks”, “Binary Indexed Tree”). |
| **External Links** | Official LeetCode problem page and top solutions (as in the prompt). |

---

## 9. Conclusion  

- **Greedy Sort & Insert** is the canonical solution for *Queue Reconstruction by Height*.  
- Works in `O(n²)` – fine for interview constraints.  
- For large datasets, upgrade to an `O(n log n)` solution using BIT/Segment Tree.  
- The provided Java, Python, and C++ snippets cover the interview‑friendly version and can be copied into your IDE or LeetCode workspace.  

Happy coding! 🚀  
*(If you found this article helpful, consider sharing or leaving a comment. Happy interviewing!)*

---