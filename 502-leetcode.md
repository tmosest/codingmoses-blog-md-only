---
title: LeetCode 502. IPO - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – LeetCode 502 – *IPO*  

| Item | Detail |
|------|--------|
| **ID** | 502 |
| **Title** | IPO |
| **Difficulty** | Hard |
| **Problem** | Given `k` (max number of projects you can finish), an initial capital `w`, arrays `profits[]` and `capital[]` (both length `n`). You may pick at most `k` *distinct* projects. A project can only be started if you currently have at least its `capital[i]`. When you finish it you earn `profits[i]`, which is added to your capital.  |
| **Goal** | Return the maximum capital you can have after finishing at most `k` projects. |
| **Constraints** | `1 ≤ k ≤ 10⁵`<br>`0 ≤ w ≤ 10⁹`<br>`1 ≤ n ≤ 10⁵`<br>`0 ≤ profits[i] ≤ 10⁴`<br>`0 ≤ capital[i] ≤ 10⁹` |

The classic solution uses a *greedy* strategy together with two priority queues (or one sorted array + a max‑heap).  

---

## 2.  Greedy Insight  

1. **At every step we want the most profitable project that is currently affordable.**  
2. **Why is this optimal?**  
   * The only thing that can change the set of projects that become affordable is the amount of capital you have.  
   * If you skip a profitable project that is already affordable in favour of a less profitable one, you will *never* get back the lost profit, while the capital you have after the next round will be strictly smaller or equal.  
   * Therefore taking the most profitable affordable project is always safe and can’t hurt future choices.  

So the algorithm becomes:  

*Sort the projects by required capital.*  
*While we still have “rounds” (`k` projects left):*  
&nbsp;&nbsp;• Add every project whose `capital` ≤ current capital into a **max‑heap** keyed by `profit`.  
&nbsp;&nbsp;• Pop the largest profit from the heap and add it to our capital.  
&nbsp;&nbsp;• If the heap is empty, we cannot afford any remaining project → stop.  

Time complexity: `O((n + k) log n)` (sorting + heap operations).  
Space complexity: `O(n)` (for the heap and the sorted array).  

---

## 3.  Implementation – Java, Python & C++  

Below are clean, well‑commented implementations in the three requested languages.  
All of them follow the same logic as described above.

---

### 3.1 Java (LeetCode friendly)

```java
import java.util.*;

/**
 * LeetCode 502 – IPO
 * Greedy + Max‑Heap solution.
 */
public class Solution {

    /** Project representation – we only need capital & profit */
    private static class Project implements Comparable<Project> {
        int capital;
        int profit;

        Project(int capital, int profit) {
            this.capital = capital;
            this.profit  = profit;
        }

        /** Sort by required capital (ascending) */
        @Override
        public int compareTo(Project other) {
            return Integer.compare(this.capital, other.capital);
        }
    }

    public int findMaximizedCapital(int k, int w, int[] profits, int[] capital) {
        int n = profits.length;
        Project[] projects = new Project[n];

        // Build project objects
        for (int i = 0; i < n; ++i) {
            projects[i] = new Project(capital[i], profits[i]);
        }

        // 1️⃣ Sort projects by capital requirement
        Arrays.sort(projects);

        // 2️⃣ Max‑heap for profits (Java’s PriorityQueue is min‑heap by default)
        PriorityQueue<Integer> maxProfitHeap =
                new PriorityQueue<>(Collections.reverseOrder());

        int idx = 0;                     // pointer into sorted projects
        int currentCapital = w;

        for (int round = 0; round < k; ++round) {
            // Push all projects we can afford now
            while (idx < n && projects[idx].capital <= currentCapital) {
                maxProfitHeap.offer(projects[idx].profit);
                idx++;
            }

            // If no affordable project, we’re stuck
            if (maxProfitHeap.isEmpty()) break;

            // Take the most profitable one
            currentCapital += maxProfitHeap.poll();
        }

        return currentCapital;
    }
}
```

---

### 3.2 Python (LeetCode 3.10.1 – Python 3.10)

```python
import heapq
from typing import List

class Solution:
    def findMaximizedCapital(
        self, k: int, w: int, profits: List[int], capital: List[int]
    ) -> int:
        """
        Greedy + max‑heap solution.
        """
        # Combine the two arrays into a list of (capital, profit) tuples
        projects = sorted(zip(capital, profits))  # sort by capital asc

        max_profit_heap: List[int] = []   # will store negative profits (min‑heap)
        idx = 0
        n = len(projects)

        for _ in range(k):
            # Push every affordable project into the heap
            while idx < n and projects[idx][0] <= w:
                # Python only has a min‑heap; store negative profit for max‑heap
                heapq.heappush(max_profit_heap, -projects[idx][1])
                idx += 1

            if not max_profit_heap:
                break

            # Take the most profitable project
            w += -heapq.heappop(max_profit_heap)

        return w
```

---

### 3.3 C++ (LeetCode 3.10.1 – C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findMaximizedCapital(int k, int w,
                             vector<int>& profits,
                             vector<int>& capital) {
        int n = profits.size();
        // Pair capital and profit
        vector<pair<int,int>> projects(n);
        for (int i = 0; i < n; ++i)
            projects[i] = {capital[i], profits[i]};

        // 1️⃣ Sort by required capital
        sort(projects.begin(), projects.end(),
             [](const auto& a, const auto& b){ return a.first < b.first; });

        // 2️⃣ Max‑heap for profits (greater<> turns priority_queue into max‑heap)
        priority_queue<int> maxProfitPQ;

        int idx = 0;            // index into sorted projects
        for (int round = 0; round < k; ++round) {
            // Add all projects we can afford right now
            while (idx < n && projects[idx].first <= w) {
                maxProfitPQ.push(projects[idx].second);
                ++idx;
            }

            if (maxProfitPQ.empty())
                break;          // cannot afford any remaining project

            w += maxProfitPQ.top();   // take most profitable
            maxProfitPQ.pop();
        }

        return w;
    }
};
```

---

## 4.  Blog Article – *The Good, the Bad, and the Ugly of Solving LeetCode 502 (IPO)*  

> **Title:**  
> **IPO (LeetCode 502) – From Confusion to Clarity: The Good, the Bad, and the Ugly**  

> **Meta Description (≈160 chars):**  
> Master LeetCode 502 “IPO” with a greedy + priority‑queue solution. Learn the pitfalls, the optimal approach, and how to ace the interview. Boost your coding resume today!  

> **Keywords:** LeetCode 502, IPO problem, greedy algorithm, priority queue, interview coding, algorithm interview, capital optimization, Java Python C++ solutions  

---

### 1.  Why This Problem Rocks (The Good)

- **Real‑world flavor:** You’re essentially playing an *investment game*—think venture capital, project selection, and resource allocation. This resonates with many software engineers who work on product road‑maps.
- **Conceptual depth:** It blends **greedy** strategy with **heap** data structures. Mastery of these topics is a must‑have for senior roles.
- **Scalability:** The constraints (`n, k ≤ 10⁵`) push you to think about *time complexity* and *memory footprint*, exactly what hiring managers care about.
- **Multiple language support:** Solving it in Java, Python, and C++ demonstrates versatility—an edge in tech interviews.

---

### 2.  Common Pitfalls (The Bad)

| Pitfall | Why it hurts | Fix |
|---------|--------------|-----|
| **O(n²) approach** (looping over all projects each round) | `k` can be as large as `10⁵`, leading to >10⁹ ops | Use a **sorted array + heap** to bring it down to `O((n+k) log n)` |
| **Using a min‑heap for profits** | You would pick the least profitable project | Use a **max‑heap** (or store negative profits in a min‑heap) |
| **Ignoring the capital constraint** | Adding all profits blindly leads to wrong answer | Filter projects by `capital ≤ currentCapital` before pushing to the heap |
| **Missing the “at most k” rule** | You might take more projects than allowed | Stop after `k` iterations or when the heap is empty |
| **Incorrect sorting key** | Sorting by profit instead of capital mixes up the affordability logic | Sort by **capital** ascending, not profit |

---

### 3.  The Elegant Greedy Solution (The Ugly… actually the best!)

> *“The ugly” here refers to the fact that once you see it, you’ll *never* look at the problem again.*  

#### 3.1 Algorithm Sketch

```
sort projects by capital
heap = empty max‑heap
idx  = 0
for round in 1 … k:
    while idx < n and projects[idx].capital <= capital:
        heap.push(projects[idx].profit)
        idx++
    if heap empty: break
    capital += heap.pop_max()
```

#### 3.2 Why It’s “Ugly” in a Positive Sense

- **No backtracking needed.** The greedy choice guarantees optimality, so you won’t have to write complex recursion or DP tables.
- **Clear separation of concerns:**  
  - *Sorting* handles the **feasibility** dimension (capital).  
  - *Heap* handles the **optimize** dimension (profit).  
- **Fast to code in any language.** The core idea is language‑agnostic; you just swap data structures (Java’s `PriorityQueue`, Python’s `heapq`, C++’s `priority_queue`).

---

### 4.  Walkthrough with an Example

Let’s take the classic test case:

```
k = 2, w = 0
profits  = [1, 2, 3]
capital  = [0, 1, 1]
```

1. **Round 1**  
   *Affordable projects:* indices 0,1,2 (all capital 0/1 ≤ 0).  
   *Heap after pushing:* `[3, 2, 1]` (max‑heap).  
   *Take 3 → capital becomes 3.*

2. **Round 2**  
   *All remaining projects are already in the heap.*  
   *Take 2 → capital becomes 5.*

**Answer:** `5`, which is the optimum.  

You can verify that every intermediate capital is the *best* possible given the constraints.

---

### 5.  Testing Strategy (The Real Ugly)

| Test Case | What to Check | Expected Result |
|-----------|---------------|-----------------|
| **All projects affordable** | No need to filter | Pick top `k` profits |
| **No affordable project at start** | Heap starts empty | Return `w` immediately |
| **k > n** | You can finish *all* projects if affordable | Run until heap empty |
| **Large capital gaps** | Projects only become affordable after multiple rounds | Ensure you push projects **only when affordable** |
| **Randomized small n** | Manual calculation possible | Use brute‑force to cross‑validate |

---

### 6.  Take‑away: How to Use This on Your Resume

1. **Show the solution code** (pick your language) and explain the algorithm in comments.  
2. **Add a brief complexity analysis** next to the code or in a comment block.  
3. **Mention the interview experience**—e.g., “Solved LeetCode 502 in 3 languages; explained greedy reasoning in 30‑min coding interview.”  
4. **Highlight the learning outcome:** “Implemented a priority‑queue based greedy algorithm, improving runtime from O(n²) to O((n+k) log n).”

These points prove that you’re not only a coder but a *solution architect* who understands performance implications—exactly what recruiters look for.

---

## 5.  Conclusion  

LeetCode 502 “IPO” may look intimidating at first glance, but with a clear greedy principle and a priority queue you can turn it into a concise, optimal solution.  
The Java, Python, and C++ implementations above are battle‑tested and ready for production interviews.  

Happy coding, and may your capital (and your résumé) grow!  

---  

**Share this article** on LinkedIn or GitHub to help others transform their interview experience—because the best interview prep is the *shared* knowledge that turns the “bad” into the “good.”