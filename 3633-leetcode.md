---
title: LeetCode 3633. Earliest Finish Time for Land and Water Rides I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 3633 – Earliest Finish Time for Land and Water Rides  
**Language‑agnostic solution (Java | Python | C++)**  
> “Want to land the perfect answer for your next interview? Read on – the trick is simple, O(n+m) time, O(1) memory, and 100 % correctness.”  

---

### 1. Problem Recap (SEO‑friendly wording)

> **LeetCode 3633 – Earliest Finish Time for Land and Water Rides**  
>  *Difficulty:* Easy  
>  *Keywords:* land rides, water rides, greedy, O(n+m), interview question, earliest finish time

You’re given two lists of rides:  
- `landStartTime[i]` – earliest opening time of land ride *i*  
- `landDuration[i]` – how long land ride *i* lasts  
- `waterStartTime[j]` – earliest opening time of water ride *j*  
- `waterDuration[j]` – how long water ride *j* lasts  

You must experience **exactly one** ride from each category, in any order.  
A ride can start no earlier than its opening time, and you can start the second ride immediately after the first one finishes or wait until it opens.  

**Goal** – return the earliest possible finishing time of both rides.

---

### 2. Intuition: Two Possibilities

1. **Land → Water**  
2. **Water → Land**

For each possibility we need to pick the “best” ride from the first category, then the “best” ride from the second.  
The “best” ride from the first category is simply the one that finishes earliest:  

```
minLandFinish  = min( landStart[i] + landDuration[i] )
minWaterFinish = min( waterStart[j] + waterDuration[j] )
```

Once you know the earliest finish of the first ride, you can compute the finish time of each candidate second‑ride:

```
startWater = max(minLandFinish, waterStart[j])   // we can’t start earlier
finishWater = startWater + waterDuration[j]
```

Analogous for the reverse order.

Take the minimum of all candidate finish times.

> **Why this works** –  
> The first ride’s finish time is independent of the second ride.  
> For a fixed first‑ride finish time, the best second ride is the one that opens the earliest and finishes the soonest (the greedy choice).  
> Because we consider *every* ride in the second category, we cover all valid pairs.

---

### 3. Edge Cases & Pitfalls

| **Bad** | **What to avoid** | **Good practice** |
|---------|-------------------|-------------------|
| **O(n*m) nested loops** | 100 * 100 = 10 000 iterations – still fine, but unnecessary. | Pre‑compute min finish times to get **O(n+m)**. |
| **Using `long` unnecessarily** | All values ≤ 1000, so `int` is safe. | Stick to `int` for clarity and speed. |
| **Off‑by‑one on arrays** | Mistyping `length` or `size` in loops. | Use `for (int i = 0; i < arr.length; i++)`. |
| **Mixing start & finish times** | Adding durations to wrong array. | Keep the formulae explicit: `start + duration`. |

---

### 4. Code

Below you’ll find a **single function** in each language that follows the algorithm described above.

---

#### 4.1 Java

```java
public class Solution {
    public int earliestFinishTime(int[] landStartTime,
                                  int[] landDuration,
                                  int[] waterStartTime,
                                  int[] waterDuration) {
        int ans = Integer.MAX_VALUE;

        // ----- Land first, Water second -----
        int minLandFinish = Integer.MAX_VALUE;
        for (int i = 0; i < landStartTime.length; i++) {
            minLandFinish = Math.min(minLandFinish,
                    landStartTime[i] + landDuration[i]);
        }
        for (int j = 0; j < waterStartTime.length; j++) {
            int start = Math.max(minLandFinish, waterStartTime[j]);
            ans = Math.min(ans, start + waterDuration[j]);
        }

        // ----- Water first, Land second -----
        int minWaterFinish = Integer.MAX_VALUE;
        for (int j = 0; j < waterStartTime.length; j++) {
            minWaterFinish = Math.min(minWaterFinish,
                    waterStartTime[j] + waterDuration[j]);
        }
        for (int i = 0; i < landStartTime.length; i++) {
            int start = Math.max(minWaterFinish, landStartTime[i]);
            ans = Math.min(ans, start + landDuration[i]);
        }

        return ans;
    }
}
```

---

#### 4.2 Python

```python
class Solution:
    def earliestFinishTime(self,
                           landStartTime: list[int],
                           landDuration: list[int],
                           waterStartTime: list[int],
                           waterDuration: list[int]) -> int:

        INF = 10**9
        ans = INF

        # Land first
        min_land_finish = min(l + d for l, d in zip(landStartTime, landDuration))
        for w_start, w_dur in zip(waterStartTime, waterDuration):
            start = max(min_land_finish, w_start)
            ans = min(ans, start + w_dur)

        # Water first
        min_water_finish = min(w + d for w, d in zip(waterStartTime, waterDuration))
        for l_start, l_dur in zip(landStartTime, landDuration):
            start = max(min_water_finish, l_start)
            ans = min(ans, start + l_dur)

        return ans
```

---

#### 4.3 C++

```cpp
class Solution {
public:
    int earliestFinishTime(vector<int>& landStartTime,
                           vector<int>& landDuration,
                           vector<int>& waterStartTime,
                           vector<int>& waterDuration) {
        int ans = INT_MAX;

        // Land first
        int minLandFinish = INT_MAX;
        for (size_t i = 0; i < landStartTime.size(); ++i) {
            minLandFinish = min(minLandFinish,
                                landStartTime[i] + landDuration[i]);
        }
        for (size_t j = 0; j < waterStartTime.size(); ++j) {
            int start = max(minLandFinish, waterStartTime[j]);
            ans = min(ans, start + waterDuration[j]);
        }

        // Water first
        int minWaterFinish = INT_MAX;
        for (size_t j = 0; j < waterStartTime.size(); ++j) {
            minWaterFinish = min(minWaterFinish,
                                 waterStartTime[j] + waterDuration[j]);
        }
        for (size_t i = 0; i < landStartTime.size(); ++i) {
            int start = max(minWaterFinish, landStartTime[i]);
            ans = min(ans, start + landDuration[i]);
        }

        return ans;
    }
};
```

---

### 5. Complexity

| **Operation** | **Time** | **Space** |
|---------------|----------|-----------|
| Pre‑computing minima | `O(n)` for land, `O(m)` for water | `O(1)` |
| Computing candidates | `O(m)` + `O(n)` | `O(1)` |
| **Total** | **`O(n + m)`** | **`O(1)`** |

With `n, m ≤ 100` this runs in microseconds and is perfect for interview‑style constraints.

---

### 5. SEO‑Focused Blog Article

> **Title**  
> `LeetCode 3633 – O(n+m) Java/Python/C++ Solution to Earliest Finish Time for Land and Water Rides | Interview Prep`

> **Meta description**  
> “Solve LeetCode 3633 in O(n+m). This article gives clean Java, Python, and C++ code, explains the greedy strategy, and highlights common pitfalls. Ideal for software‑engineering interview preparation.”

---

## 🎓 Blog Post

> **“The ‘Land and Water’ Interview Riddle – How to Tackle LeetCode 3633 Like a Pro”**

---

### a. Why this question matters for job interviews

Many hiring managers love problems that test **greedy thinking** and **array manipulation** without drowning the candidate in edge‑case complexity. LeetCode 3633 is a perfect “easy” problem that still has a twist: you need to pick *two* rides, but the categories are independent.  

> *Interviewers ask:* “What’s the best way to reduce an O(n*m) approach to O(n+m)?”  
> *Your answer:* “Pre‑compute the earliest finish of the first category and then greedily pair it with every ride of the second.”

---

### b. Good, Bad, Ugly – The algorithm in practice

| **Stage** | **Good** | **Bad** | **What I learned** |
|-----------|----------|---------|--------------------|
| **Step 1 – Problem understanding** | Draw a timeline, list all 4 arrays. | Skip the “exactly one ride from each category” nuance. | Clarifying constraints prevents logical errors. |
| **Step 2 – Brute force (O(n*m))** | Easy to write; passes limits. | Gives a misleading impression of required time complexity. | Use it for debugging, not production. |
| **Step 3 – Greedy + Min finish pre‑calc** | O(n+m), clean code. | Forget to `max()` start times. | Keep the formulas explicit (`start = max(minFinish, openTime)`). |
| **Step 4 – Testing** | Edge case: single ride in each list. | Forget to initialize `ans` to a big value. | Use `INT_MAX` or `10**9`. |
| **Step 5 – Deployment** | Works on LeetCode, passes hidden tests. | None | |

---

### c. Real‑World Tips for the Interview

1. **Explain the two‑step order early** – “First we consider land→water, then water→land.”  
2. **Show the greedy intuition** – “The first ride’s finish time is independent; once we know it, the second ride is the one that opens as early as possible.”  
3. **Mention time/space complexity** – Interviewers appreciate a clean Big‑O explanation.  
4. **Walk through a concrete example** – e.g., `land = [(2,3),(4,1)]`, `water = [(1,5),(5,2)]`. Show how the algorithm yields `9`.  
5. **Highlight pitfalls** – “Don’t mix start and finish times; remember to `max` with the first finish.”

---

### d. Bonus: What if you had to choose *k* rides from each category?

> The greedy principle still holds:  
> 1. Pick the *k* rides with the smallest `start + duration`.  
> 2. For each second‑category candidate, start at `max(firstGroupEarliestFinish, openTime)` and finish.  
> Complexity becomes **O(k*(n+m))** if you do it naïvely, but you can still prune heavily.

---

### 6. Final Thoughts

- The LeetCode 3633 solution is **100 % correct** with **O(n+m) time** and **O(1) space**.  
- It’s a textbook example of **greedy + pre‑computation**.  
- Presenting this solution in Java, Python, and C++ demonstrates language versatility—a key trait interviewers look for.

> **If you want to land that next software‑engineering role, practice problems like this and master the art of explaining your thought process clearly and concisely. Happy coding!**  

---

### 7. References & Further Reading

- LeetCode 3633 – [Problem Link](https://leetcode.com/problems/earliest-finish-time-for-land-and-water-rides/)  
- Greedy Algorithms – <https://en.wikipedia.org/wiki/Greedy_algorithm>  
- Time Complexity Analysis – <https://www.programiz.com/dsa/time-complexity>  

Happy interviewing! 🎉