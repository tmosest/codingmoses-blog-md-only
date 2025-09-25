---
title: LeetCode 3635. Earliest Finish Time for Land and Water Rides II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  Solving LeetCode 3635 – Earliest Finish Time for Land and Water Rides II

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.  
All three share the same O(n+m) time / O(1) auxiliary‑space algorithm described in the blog article that follows.

---

## 1.1 Java

```java
import java.util.*;

public class Solution {

    /**
     * Returns the earliest possible time a tourist can finish one land ride
     * and one water ride in either order.
     *
     * @param landStartTime  earliest start times for land rides
     * @param landDuration   durations of land rides
     * @param waterStartTime earliest start times for water rides
     * @param waterDuration  durations of water rides
     * @return minimum finish time
     */
    public int earliestFinishTime(int[] landStartTime, int[] landDuration,
                                  int[] waterStartTime, int[] waterDuration) {

        int INF = Integer.MAX_VALUE;
        int answer = INF;

        /* ----------------------------------------------------------
           1. Land first, water second
        ---------------------------------------------------------- */
        // the earliest moment a land ride can finish (any land ride)
        int minLandFinish = INF;
        for (int i = 0; i < landStartTime.length; i++) {
            minLandFinish = Math.min(minLandFinish,
                    landStartTime[i] + landDuration[i]);
        }

        // for each water ride, the finish time = duration + max(minLandFinish, waterStart)
        for (int j = 0; j < waterStartTime.length; j++) {
            int finish = waterDuration[j] + Math.max(minLandFinish, waterStartTime[j]);
            answer = Math.min(answer, finish);
        }

        /* ----------------------------------------------------------
           2. Water first, land second
        ---------------------------------------------------------- */
        // the earliest moment a water ride can finish (any water ride)
        int minWaterFinish = INF;
        for (int j = 0; j < waterStartTime.length; j++) {
            minWaterFinish = Math.min(minWaterFinish,
                    waterStartTime[j] + waterDuration[j]);
        }

        // for each land ride, the finish time = duration + max(minWaterFinish, landStart)
        for (int i = 0; i < landStartTime.length; i++) {
            int finish = landDuration[i] + Math.max(minWaterFinish, landStartTime[i]);
            answer = Math.min(answer, finish);
        }

        return answer;
    }

    // ------------------------------------------------------------
    // Demo / Self‑test
    // ------------------------------------------------------------
    public static void main(String[] args) {
        Solution sol = new Solution();

        int[] landStart = {2, 8};
        int[] landDur   = {4, 1};
        int[] waterStart = {6};
        int[] waterDur   = {3};

        System.out.println(sol.earliestFinishTime(landStart, landDur, waterStart, waterDur));
        // Expected: 9
    }
}
```

---

## 1.2 Python

```python
from typing import List

class Solution:
    def earliestFinishTime(
        self,
        landStartTime: List[int],
        landDuration: List[int],
        waterStartTime: List[int],
        waterDuration: List[int],
    ) -> int:
        INF = 10**18
        answer = INF

        # 1) Land first, water second
        min_land_finish = min(l + d for l, d in zip(landStartTime, landDuration))
        for w_start, w_dur in zip(waterStartTime, waterDuration):
            finish = w_dur + max(min_land_finish, w_start)
            answer = min(answer, finish)

        # 2) Water first, land second
        min_water_finish = min(w + d for w, d in zip(waterStartTime, waterDuration))
        for l_start, l_dur in zip(landStartTime, landDuration):
            finish = l_dur + max(min_water_finish, l_start)
            answer = min(answer, finish)

        return answer


# ----------------------------------------------------------------
# Demo / Self‑test
# ----------------------------------------------------------------
if __name__ == "__main__":
    sol = Solution()
    print(
        sol.earliestFinishTime(
            landStartTime=[2, 8],
            landDuration=[4, 1],
            waterStartTime=[6],
            waterDuration=[3],
        )
    )  # 9
```

---

## 1.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int earliestFinishTime(vector<int>& landStartTime,
                           vector<int>& landDuration,
                           vector<int>& waterStartTime,
                           vector<int>& waterDuration) {
        const long long INF = 4e18;          // large enough
        long long answer = INF;

        /* 1. Land first, water second */
        long long minLandFinish = INF;
        for (size_t i = 0; i < landStartTime.size(); ++i)
            minLandFinish = min(minLandFinish,
                                (long long)landStartTime[i] + landDuration[i]);

        for (size_t j = 0; j < waterStartTime.size(); ++j) {
            long long finish =
                waterDuration[j] + max(minLandFinish, (long long)waterStartTime[j]);
            answer = min(answer, finish);
        }

        /* 2. Water first, land second */
        long long minWaterFinish = INF;
        for (size_t j = 0; j < waterStartTime.size(); ++j)
            minWaterFinish = min(minWaterFinish,
                                 (long long)waterStartTime[j] + waterDuration[j]);

        for (size_t i = 0; i < landStartTime.size(); ++i) {
            long long finish =
                landDuration[i] + max(minWaterFinish, (long long)landStartTime[i]);
            answer = min(answer, finish);
        }

        return (int)answer;
    }
};

/* ----------------------------------------------------------------
   Demo / Self‑test
   ---------------------------------------------------------------- */
int main() {
    Solution sol;
    vector<int> landStart = {2, 8};
    vector<int> landDur   = {4, 1};
    vector<int> waterStart = {6};
    vector<int> waterDur   = {3};

    cout << sol.earliestFinishTime(landStart, landDur, waterStart, waterDur)
         << endl;   // 9
    return 0;
}
```

---

# 2.  Blog Article – “The Good, the Bad, and the Ugly of Solving LeetCode 3635”

> **Target Audience**: Front‑end / back‑end developers, system‑design interviewees, or anyone preparing for coding interviews who wants a deeper understanding of greedy algorithms, complexity analysis, and clean‑code practices.  
> **SEO Keywords**: *LeetCode 3635, Earliest Finish Time for Land and Water Rides II, greedy algorithm, interview preparation, Java solution, Python solution, C++ solution, time complexity, space complexity, coding interview tips*.

---

## 2.1 Problem Recap (in your own words)

You’re given two lists:

| Category | Input | Description |
|----------|-------|-------------|
| **Land** | `landStartTime[i]` | The earliest hour a land ride *i* can start. |
|          | `landDuration[i]` | How long that ride lasts. |
| **Water**| `waterStartTime[j]` | The earliest hour a water ride *j* can start. |
|          | `waterDuration[j]` | How long that ride lasts. |

A tourist must complete **exactly one land ride and exactly one water ride**. The rides may be taken in *any* order. The goal is to compute the **smallest possible finish hour**.

---

## 2.2 The Good – Why the Greedy Solution Works

1. **Observing Independence**  
   *The land ride and the water ride are independent events.*  
   If you decide to take a land ride first, all you need to know about the second ride is **when the first one is done**. The *exact identity* of the first ride (i.e., which land or water ride you picked) is irrelevant because any later ride can start only after the previous one finishes.

2. **Early‑Finish Minima**  
   Let  

   * `M_land = min_i (landStart[i] + landDuration[i])`  
   * `M_water = min_j (waterStart[j] + waterDuration[j])`  

   These are the earliest moments at which *any* land / water ride can finish.  
   They are the bottlenecks when the other type of ride starts.

3. **Formulating Finish Times**  
   - **Land first, Water second**:  
     For a specific water ride `j`, you can start it no earlier than `max(M_land, waterStart[j])`.  
     Add its duration → finish time = `waterDuration[j] + max(M_land, waterStart[j])`.

   - **Water first, Land second**:  
     Symmetrically, for a specific land ride `i`, finish time = `landDuration[i] + max(M_water, landStart[i])`.

4. **Take the Minimum**  
   Scan both lists once, compute the two sets of finish times, and keep the smallest.  
   This is a classic **greedy** technique: at each decision point you use the *earliest possible finish* of the preceding ride, which guarantees optimality because later rides cannot be made earlier by delaying the previous ride.

5. **Why This Is Optimal**  
   Suppose you pick a land ride that does *not* finish at `M_land`. You are forcing the next water ride to start later than necessary. The greedy rule simply says: *Always finish the first ride as early as possible.* Any other choice only pushes the second ride’s start further back.

---

## 2.3 The Bad – Common Pitfalls and “Nice” Edge Cases

| Pitfall | Why It Happens | Fix / Mitigation |
|---------|----------------|------------------|
| **Integer overflow** | Adding two `int` values (`start + duration`) can exceed 32‑bit range in C++/Java when the input values are near `INT_MAX`. | Use `long long`/`long` in C++/Java or `int` → `int` cast after the full calculation, or clamp to `long long` throughout. |
| **Zero‑length rides** | A ride can have `duration = 0`. The algorithm still works, but naive `max(minFinish, start)` logic could inadvertently pick the wrong start if you forget the `max`. | Always use `max` when computing the start time for the second ride. |
| **Empty lists** | The LeetCode constraints guarantee at least one land *and* one water ride, but a defensive implementation should guard against empty inputs. | Add early returns or assertions. |
| **Large input arrays** | Reading the entire array twice in Python with `zip` can create temporary tuples if not careful. | Use generator expressions (`(l + d for l, d in zip(...))`) to keep memory usage O(1). |

---

## 2.4 The Ugly – When a “Brute Force” Approach Breaks

A naïve solution would consider *every* ordered pair `(land, water)` and simulate both orders:

```text
O((n*m) + (n*m)) = O(n*m)
```

For the problem’s constraints (`n, m ≤ 2·10^5`), this approach will hit a **time‑limit exceeded** error on most judge systems. Worse, the memory footprint can grow if you pre‑store all pair‑wise start times.

### Why It’s Ugly

* **Quadratic blow‑up** – Scales poorly; even a small increase in input size can push runtime past the 2‑second limit on many judges.
* **Hard to reason** – With thousands of pairs, you lose the elegant “one‑pass” reasoning that greedy gives you.
* **Code maintenance** – More loops → more bugs (index errors, off‑by‑ones) and harder to unit‑test.

---

## 2.5 Implementation‑Level Tips

| Tip | What It Means | Why It Matters |
|-----|---------------|----------------|
| **Use `long` / `long long` during intermediate sums** | Prevent overflow before casting back to `int` | Ensures correctness for edge inputs |
| **Avoid creating auxiliary arrays** | Compute minima and finishes in one pass | Keeps memory constant |
| **Keep the public API small** | One method per language | Easier to read, test, and reuse |
| **Document with Javadoc / docstrings** | Explain parameters & return value | Future maintainers save time |
| **Write a self‑test harness** | `main()` or `if __name__ == "__main__"` | Guarantees the logic is correct before submission |

---

## 2.6 Variations You Might Encounter

1. **More than two categories** (e.g., “rides, shows, and dining”).  
   Extend the greedy idea: pick the minimal finish among *any* category, then compute finish times for the remaining categories. Complexity stays linear because you only need the global earliest finish of each category.

2. **Multiple rides per category** – The problem already handles this, but if you had *k* rides per category you would simply iterate `k` times.

3. **Constraints on order** (e.g., land must come before water).  
   Then you only need the first part of the algorithm, simplifying it further.

---

## 2.7 Take‑away: Why This Problem Is a “Must‑Know” for Interviews

* **Greedy Principle**: “Always finish the first ride as early as possible.”  
  This rule is intuitive but powerful. It showcases your ability to reason about *local* optimality leading to a *global* optimum.

* **Time‑Space Mastery**:  
  Linear time and constant extra space demonstrate that you can solve problems within strict limits—a common interview requirement.

* **Cross‑Language Confidence**:  
  The solution is language‑agnostic. Having a clean Java, Python, and C++ implementation shows that you can port algorithms between stacks, a plus for full‑stack or system‑design interviews.

* **Debugging Discipline**:  
  The “self‑test” scaffolding ensures you validate your logic before the actual submission, reducing the risk of hidden bugs under the time pressure of an interview.

---

## 2.8 Final Word

The LeetCode 3635 challenge is not just about *coding*; it’s about **thinking clearly**, choosing the right algorithmic tool, and delivering **readable, testable code**.  

- **Good**: The greedy solution is elegant and optimal.  
- **Bad**: Forgetting integer overflow or misapplying the `max` function can silently break your solution.  
- **Ugly**: Brute‑force pairwise simulation leads to TLE and is a maintenance nightmare.

Armed with this understanding and the ready‑to‑copy code snippets above, you’re fully equipped to tackle LeetCode 3635 during your next coding interview—ready to impress recruiters with both speed and clean engineering. Happy coding!