---
title: LeetCode 1167. Minimum Cost to Connect Sticks - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Minimum Cost to Connect Sticks – LeetCode 1167  
> **Java + Python + C++ solutions, interview‑ready explanation, and an SEO‑optimized blog post**  

---

## 1. The Problem in a Nutshell

> **Given an array `sticks` of positive integers, repeatedly pick any two sticks, connect them, and pay a cost equal to the sum of their lengths. After connecting you obtain a new stick whose length is that sum. Continue until only one stick remains. Return the minimum total cost.**

The input size can be as large as `10⁴` sticks, each up to `10⁴` in length.

---

## 2. Why the Greedy + Min‑Heap Approach Works

The problem is a classic **Huffman‑coding** pattern.  
At every step you want to combine the *smallest* two sticks – any other choice will make the cost strictly larger.  
A min‑heap guarantees O(log n) extraction of the two smallest elements and O(log n) insertion of the new stick.

> **Key Insight**:  
> *Greedy + Min‑Heap = Optimal*.

---

## 3. Time & Space Complexity

| | Algorithm |
|---|---|
| **Time** | `O(n log n)` – building the heap + `n-1` extract‑and‑insert operations |
| **Space** | `O(n)` – heap stores all current sticks |

Edge case: when `sticks.length == 1` the answer is `0` – no work required.

---

## 4. The Code – Three Languages

> **Important** – all solutions use a **min‑heap** and run in the optimal `O(n log n)` time.  
> They also avoid integer‑overflow by using 64‑bit (`long` in Java, `long long` in C++, Python’s arbitrary‑size int).

### 4.1 Java

```java
import java.util.PriorityQueue;

public class Solution {
    public int connectSticks(int[] sticks) {
        // Use long to avoid overflow; final result fits into int.
        long total = 0;
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        for (int stick : sticks) pq.add(stick);

        while (pq.size() > 1) {
            long sum = (long) pq.poll() + pq.poll();
            total += sum;
            pq.add((int) sum);   // sum will never exceed 2^31-1 in given constraints
        }
        return (int) total;
    }
}
```

> **Why it’s clean**  
> - One priority queue  
> - No helper methods, concise loop  
> - Handles overflow safely

### 4.2 Python

```python
import heapq
from typing import List

class Solution:
    def connectSticks(self, sticks: List[int]) -> int:
        heapq.heapify(sticks)          # O(n)
        total = 0
        while len(sticks) > 1:
            a = heapq.heappop(sticks)  # smallest
            b = heapq.heappop(sticks)  # second smallest
            s = a + b
            total += s
            heapq.heappush(sticks, s)
        return total
```

> **Pythonic touches**  
> - `heapq.heapify` builds the heap in linear time  
> - List is mutated in place – no extra array

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int connectSticks(vector<int>& sticks) {
        // Min‑heap via priority_queue with greater comparator
        priority_queue<int, vector<int>, greater<int>> pq(sticks.begin(), sticks.end());
        long long total = 0;
        while (pq.size() > 1) {
            long long a = pq.top(); pq.pop();
            long long b = pq.top(); pq.pop();
            long long s = a + b;
            total += s;
            pq.push(static_cast<int>(s));
        }
        return static_cast<int>(total);
    }
};
```

> **C++ style**  
> - `greater<int>` gives a min‑heap  
> - `static_cast<int>` guarantees the returned type

---

## 5. Good, Bad, and Ugly

| | Good | Bad | Ugly |
|---|---|---|---|
| **Algorithm** | Greedy + Min‑Heap (optimal) | O(n²) naïve pairwise merging (quadratic) | Mixing heap + brute force, leading to messy code |
| **Implementation** | Clear, concise, uses language‑idiomatic heap | Nested loops, hard to read | In‑place modifications without comments, no error handling |
| **Edge‑Cases** | Handles single stick, large inputs, overflow | Fails on empty input, or on `int` overflow | Assumes `sticks` is non‑empty without checks |

### Why the “Ugly” version is a disaster

- It uses a two‑list trick (`vals` and `sums`) without explaining why it works.  
- `removeLowest` method is called `O(n)` inside a loop that runs `n-1` times → overall `O(n²)` performance.  
- No type safety – potential `ArrayIndexOutOfBounds` if list is emptied prematurely.  
- Hard to debug; no unit tests included.

---

## 6. SEO‑Friendly Blog Outline

| Section | Keyword Density | Purpose |
|---|---|---|
| **Title** | “Minimum Cost to Connect Sticks – LeetCode 1167 Solution in Java, Python, C++” | Rank on searches for the exact problem |
| **Meta Description** | 155‑char summary with “Minimum Cost to Connect Sticks, LeetCode 1167, greedy algorithm, min‑heap” | Click‑through from SERPs |
| **Header H1** | Same as title | Clear main heading |
| **Intro** | 3‑4 sentences containing “minimum cost to connect sticks”, “LeetCode 1167”, “Huffman coding” | Capture reader intent |
| **Problem Statement** | Use the official wording; add “constraints” | Show understanding |
| **Approach** | “Greedy + Min‑Heap”, “Huffman coding pattern” | Core explanation |
| **Complexity** | “O(n log n)”, “O(n)” | Technical depth |
| **Code** | Java, Python, C++ sections, each with syntax highlighting | Practical value |
| **Edge Cases** | “single stick”, “overflow” | Show robustness |
| **Good/Bad/Ugly** | Sub‑headings with bullet points | Learning takeaway |
| **Conclusion** | “Master LeetCode 1167 in minutes”, “Add to your portfolio” | Call‑to‑action |
| **Tags** | “LeetCode”, “minimum cost to connect sticks”, “greedy algorithm”, “heap” | Internal linking |

> **Tip**: Include internal links to your other blog posts on “Huffman Coding”, “Priority Queue in C++”, etc. This keeps readers on your site and boosts SEO.

---

## 7. Final Thoughts

- The **greedy + min‑heap** solution is the *only* one that scales to the maximum input limits.
- Keep your code **readable** – clear variable names, comments where the algorithm is non‑obvious, and consistent formatting.
- Test against edge cases: `[1]`, `[1, 2]`, `[10000] * 10000`.

> **Ready to ace your next coding interview?**  
> Add this clean, fast solution to your portfolio and share your experience on LinkedIn – recruiters love seeing real‑world LeetCode mastery!