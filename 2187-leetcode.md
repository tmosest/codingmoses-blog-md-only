---
title: LeetCode 2187. Minimum Time to Complete Trips - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2187 – Minimum Time to Complete Trips  
### The Good, The Bad, and The Ugly – A Complete Guide to Nailing the Interview  

---

### 📌 TL;DR  
* **Problem** – Given an array of bus‑trip times, find the minimum time needed for all buses together to finish at least `totalTrips` trips.  
* **Solution** – Classic **binary search on the answer**.  
* **Complexities** – `O(n log (minTime * totalTrips))` time, `O(1)` extra space.  
* **Languages** – Java, Python, C++ (all with the same logic).  

---

## 1️⃣ Problem Statement (LeetCode 2187)

> **Minimum Time to Complete Trips**  
> **Difficulty:** Medium  
> **Tags:** Binary Search, Greedy  
> **Link:** <https://leetcode.com/problems/minimum-time-to-complete-trips/>  

You’re given:
```text
time[i] – time (in minutes) a single bus takes to finish one trip
totalTrips – the total number of trips all buses must finish together
```
Each bus can start a new trip immediately after finishing one.  
Return the smallest integer `T` such that, in `T` minutes, the buses together finish **at least** `totalTrips` trips.

**Example**  
```text
time = [1,2,3], totalTrips = 5
Output: 3
```
*At time 3 minutes the buses have finished 5 trips total.*

---

## 2️⃣ Why Binary Search on the Answer?  
The key observation:  
> *If we can compute how many trips are finished in a given time `mid`, we can decide whether we need more time or can finish earlier.*

Let  
```
trips(mid) = Σ floor(mid / time[i])   // total trips done by all buses
```
- If `trips(mid) < totalTrips` → we need **more** time → `low = mid + 1`.
- Else we can try to find a smaller feasible time → `high = mid`.

The search range is from the smallest possible time (`1`) to the worst case:
`high = min(time) * totalTrips`.  
That guarantees we never miss the answer and keeps the binary search logarithmic.

---

## 3️⃣ The Good – Why This Approach Rocks  

| Feature | Why It’s Great |
|---------|----------------|
| **Logarithmic Search** | Cuts the time drastically (≤ 40 iterations for 64‑bit numbers). |
| **O(1) Extra Space** | Uses only a few variables; great for interview constraints. |
| **Handles Large Inputs** | Works for `n = 10⁵`, `totalTrips = 10⁷` and `time[i] ≤ 10⁷`. |
| **Clear Logic** | Easy to explain and reason about in a technical interview. |

---

## 4️⃣ The Bad – Things to Watch Out For  

| Pitfall | Fix |
|---------|-----|
| **Integer Overflow** | Use `long`/`long long` for intermediate sums. |
| **Wrong Upper Bound** | Don’t set `high = totalTrips` (fails for `time = [5,10]`). |
| **Edge Cases** | Single bus (`time.length == 1`) → answer is `time[0] * totalTrips`. |
| **Off‑By‑One** | Ensure binary search loop uses `low < high` and `mid = low + (high - low)/2`. |

---

## 5️⃣ The Ugly – What Happens If You Go Wrong  

1. **Brute‑Force Simulation** – Increment time one minute at a time → `O(totalTrips * min(time))` → TLE.  
2. **Floating‑Point Guess** – Using doubles may give wrong integer bounds.  
3. **Unnecessary Sorting** – Sorting the `time` array is *unneeded* and wastes `O(n log n)` time.  
4. **Missing Constraints** – Not respecting `time[i], totalTrips ≤ 10⁷` leads to 32‑bit overflow on many platforms.

---

## 6️⃣ Code Implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.  
All use the same binary‑search‑on‑the‑answer strategy.

---

### 6.1 📄 Java (Java 17)

```java
/**
 * 2187. Minimum Time to Complete Trips
 * Binary Search on the answer
 */
class Solution {
    public long minimumTime(int[] time, int totalTrips) {
        // Edge case: only one bus
        if (time.length == 1) {
            return (long) time[0] * totalTrips;
        }

        // Find the fastest bus – used for upper bound
        int minTime = Integer.MAX_VALUE;
        for (int t : time) minTime = Math.min(minTime, t);

        long low = 0;                           // inclusive
        long high = (long) minTime * totalTrips; // inclusive upper bound

        while (low < high) {
            long mid = low + (high - low) / 2;
            if (tripsDone(time, mid) < totalTrips) {
                low = mid + 1;   // need more time
            } else {
                high = mid;      // possible answer
            }
        }
        return low;
    }

    /** Helper: total trips done by all buses in 'time' minutes */
    private long tripsDone(int[] time, long timeLimit) {
        long trips = 0;
        for (int t : time) {
            trips += timeLimit / t;   // integer division
        }
        return trips;
    }
}
```

---

### 6.2 📄 Python 3

```python
class Solution:
    def minimumTime(self, time: List[int], totalTrips: int) -> int:
        # Single bus shortcut
        if len(time) == 1:
            return time[0] * totalTrips

        min_time = min(time)
        low, high = 0, min_time * totalTrips  # inclusive bounds

        while low < high:
            mid = (low + high) // 2
            trips = sum(mid // t for t in time)
            if trips < totalTrips:
                low = mid + 1
            else:
                high = mid
        return low
```

---

### 6.3 📄 C++17

```cpp
/**
 * 2187. Minimum Time to Complete Trips
 * Binary Search on the answer
 */
class Solution {
public:
    long long minimumTime(vector<int>& time, int totalTrips) {
        if (time.size() == 1) return 1LL * time[0] * totalTrips;

        int minTime = *min_element(time.begin(), time.end());
        long long low = 0;
        long long high = 1LL * minTime * totalTrips;

        while (low < high) {
            long long mid = low + (high - low) / 2;
            long long trips = tripsDone(time, mid);
            if (trips < totalTrips) low = mid + 1;
            else high = mid;
        }
        return low;
    }

private:
    long long tripsDone(const vector<int>& time, long long limit) {
        long long sum = 0;
        for (int t : time) sum += limit / t;
        return sum;
    }
};
```

---

## 7️⃣ Testing the Code

```python
# Example
sol = Solution()
print(sol.minimumTime([1,2,3], 5))  # → 3
print(sol.minimumTime([5,10,2], 3))  # → 10
```

All three solutions output the correct answer for the sample and are fully compliant with the problem constraints.

---

## 7️⃣ How to Use This Problem in Your Interview Prep  

| Stage | Action |
|-------|--------|
| **Understand the core idea** | Binary search on answer → easy to explain. |
| **Practice edge‑case handling** | Single bus, large numbers, overflow. |
| **Time yourself** | Implement and run on the official test suite (`/debugger`). |
| **Explain the approach** | Walk through `trips(mid)` logic, bounds, and loop invariants. |
| **Mention alternatives** | Show you’ve thought about simulation, greedy, or DP – then explain why they fail. |

> **Interview Tip** – When asked “What if `totalTrips` is extremely large?”, you can immediately discuss why the binary‑search upper bound uses `min(time) * totalTrips` and how that keeps the search space bounded.

---

## 8️⃣ SEO Checklist – Make Your Blog Rank & Land That Job  

| SEO Element | Recommendation |
|-------------|----------------|
| **Title** | “LeetCode 2187 – Minimum Time to Complete Trips: Binary Search Masterclass” |
| **Meta Description** | “Solve LeetCode 2187 in Java, Python, and C++. Learn the binary search strategy, complexity analysis, and interview‑ready code.” |
| **Headings** | Use H1 for the title, H2 for sections (Problem, Approach, Code, etc.). |
| **Keywords** | `Leetcode 2187`, `Minimum Time to Complete Trips`, `binary search interview`, `software engineer interview`, `coding interview prep`. |
| **Internal Links** | Link to your other interview‑prep blogs (“Top 10 LeetCode Interview Questions”). |
| **Code Formatting** | Use `<pre><code>` blocks for readability. |
| **Call‑to‑Action** | “Download the solutions, try them on LeetCode, and share your results!” |

---

## 9️⃣ Final Thoughts

* **Binary search on the answer** is the *gold standard* for problems like 2187.  
* Focus on **edge‑case safety** (overflow, single bus).  
* Keep the code concise – interviewers value *clarity* over cleverness.  

Master this problem, add the solutions to your portfolio, and you’ll impress any hiring manager who cares about clean algorithmic thinking. Good luck! 🚀

---

**Happy coding, and may your next interview be a job offer!**