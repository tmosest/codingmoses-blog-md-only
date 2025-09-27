---
title: LeetCode 3635. Earliest Finish Time for Land and Water Rides II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📚  Earliest Finish Time for Land and Water Rides II – The Full Solution
> **A practical interview problem that tests greedy intuition, array manipulation and time‑complexity skills**  
> **Languages:** Java | Python | C++  

---

### 1.  Problem Recap  

You’re given two lists of theme‑park attractions:

| **Category** | `startTime[i]` | `duration[i]` |
|--------------|----------------|---------------|
| Land rides   | `landStartTime[i]` | `landDuration[i]` |
| Water rides  | `waterStartTime[j]` | `waterDuration[j]` |

*You must ride **exactly one** land ride and **exactly one** water ride, in any order.*

A ride can start at its opening time or any later moment.  
After finishing one ride you can immediately board the other (if it’s already open) or wait for it to open.

**Goal:**  Return the *earliest possible finish time* for both rides.

> **Constraints**  
> 1 ≤ n, m ≤ 5 · 10⁴, 1 ≤ time, duration ≤ 10⁵  

---

### 2.  Intuition – Why a Greedy Scan Works

The only decisions that matter are:

1. Which ride you choose **first** (land or water).  
2. Which specific ride you pick from that category.

The *time* you finish the first ride is independent of the second ride’s choice, because you can always wait for the second ride to open.  
Thus, for a given **order** (Land → Water or Water → Land) you only need the *earliest* finish time of the first category:

* `minLandFinish  = min(landStart[i] + landDuration[i])`  
* `minWaterFinish = min(waterStart[j] + waterDuration[j])`

Once you know the earliest time you can finish the first ride, the finish time for every ride of the second category becomes:

```
finish = duration_of_second_ride
        + max( earliest_finish_of_first_category,
               opening_time_of_second_ride )
```

The `max` handles the wait‑time if the second ride isn’t open yet.

So we can:

1. Pre‑compute `minLandFinish` and `minWaterFinish` in **O(n+m)**.  
2. For every water ride compute the finish time for the order *Land→Water*.  
3. For every land ride compute the finish time for the order *Water→Land*.  
4. The answer is the minimum of all these candidates – still **O(n+m)**.

No sorting or binary search is required – pure linear scans.  
This greedy insight gives us an **O(n+m)** time, **O(1)** space solution.

---

### 3.  The Code – 3 Languages

#### 3.1 Java

```java
import java.util.*;

public class Solution {
    public int earliestFinishTime(
            int[] landStartTime, int[] landDuration,
            int[] waterStartTime, int[] waterDuration) {

        int minLandFinish = Integer.MAX_VALUE;
        for (int i = 0; i < landStartTime.length; i++) {
            minLandFinish = Math.min(minLandFinish,
                    landStartTime[i] + landDuration[i]);
        }

        int minWaterFinish = Integer.MAX_VALUE;
        for (int i = 0; i < waterStartTime.length; i++) {
            minWaterFinish = Math.min(minWaterFinish,
                    waterStartTime[i] + waterDuration[i]);
        }

        int answer = Integer.MAX_VALUE;

        /* Land first → Water second */
        for (int j = 0; j < waterStartTime.length; j++) {
            int finish = waterDuration[j] + Math.max(minLandFinish,
                                                     waterStartTime[j]);
            answer = Math.min(answer, finish);
        }

        /* Water first → Land second */
        for (int i = 0; i < landStartTime.length; i++) {
            int finish = landDuration[i] + Math.max(minWaterFinish,
                                                    landStartTime[i]);
            answer = Math.min(answer, finish);
        }

        return answer;
    }
}
```

---

#### 3.2 Python

```python
class Solution:
    def earliestFinishTime(
        self,
        landStartTime: List[int],
        landDuration: List[int],
        waterStartTime: List[int],
        waterDuration: List[int]
    ) -> int:
        min_land_finish = min(l + d for l, d in zip(landStartTime, landDuration))
        min_water_finish = min(w + d for w, d in zip(waterStartTime, waterDuration))

        ans = float('inf')

        # Land first → Water second
        for w, d in zip(waterStartTime, waterDuration):
            finish = d + max(min_land_finish, w)
            ans = min(ans, finish)

        # Water first → Land second
        for l, d in zip(landStartTime, landDuration):
            finish = d + max(min_water_finish, l)
            ans = min(ans, finish)

        return ans
```

---

#### 3.3 C++

```cpp
#include <vector>
#include <algorithm>
#include <climits>

class Solution {
public:
    int earliestFinishTime(
        std::vector<int>& landStartTime,
        std::vector<int>& landDuration,
        std::vector<int>& waterStartTime,
        std::vector<int>& waterDuration) {

        int minLandFinish = INT_MAX;
        for (size_t i = 0; i < landStartTime.size(); ++i)
            minLandFinish = std::min(minLandFinish,
                landStartTime[i] + landDuration[i]);

        int minWaterFinish = INT_MAX;
        for (size_t j = 0; j < waterStartTime.size(); ++j)
            minWaterFinish = std::min(minWaterFinish,
                waterStartTime[j] + waterDuration[j]);

        int ans = INT_MAX;

        // Land first → Water second
        for (size_t j = 0; j < waterStartTime.size(); ++j) {
            int finish = waterDuration[j] +
                         std::max(minLandFinish, waterStartTime[j]);
            ans = std::min(ans, finish);
        }

        // Water first → Land second
        for (size_t i = 0; i < landStartTime.size(); ++i) {
            int finish = landDuration[i] +
                         std::max(minWaterFinish, landStartTime[i]);
            ans = std::min(ans, finish);
        }

        return ans;
    }
};
```

---

### 4.  Blog Article – “The Good, the Bad, and the Ugly”

> **SEO‑Optimized Title:**  
> *Earliest Finish Time for Land and Water Rides II – A Practical Interview Problem (Java, Python, C++)*  

---

#### 4.1 Introduction  

Interviewers love problems that test greedy reasoning, array traversal and clean code.  
“Earliest Finish Time for Land and Water Rides II” is a canonical medium‑difficulty LeetCode challenge that does exactly that.  

In this post we walk through:

1.  Problem understanding & constraints  
2.  The greedy intuition that turns a seemingly complex combinatorial task into an **O(n+m)** scan  
3.  A full solution in Java, Python and C++  
4.  Common pitfalls (the “Bad”) and how to avoid them  
5.  Extensions you can discuss to show depth (the “Ugly”)  

By the end you’ll have a *ready‑to‑paste* reference for each language and a talking point that demonstrates your ability to simplify under tight time limits.

---

#### 4.2 Problem Recap (Good)  

- **What you must pick:** exactly one land and one water ride.  
- **Order doesn’t matter** – you can choose either first.  
- **Large inputs**: 50 000 rides per category → we need linear time.  

---

#### 4.3 Why the Naïve Approach Fails (Bad)  

A natural first attempt is:

```text
for every land ride
    for every water ride
        try both orders
```

That would be **O(n·m)**, far too slow for 5 × 10⁴ rides per category.  
Interviewers will quickly spot the quadratic blow‑up and expect you to find a better strategy.

---

#### 4.4 The Greedy Insight (Good)  

- **Key insight:** the *finish time of the first category* is independent of which ride you’ll take later.  
- Compute the *earliest finish* of the first category once (`minLandFinish`, `minWaterFinish`).  
- For the second category, the finish time becomes a simple formula using `max` for the wait.  
- Scan all rides of the second category → update global minimum.  

All this is **one pass** per array – no sorting, no heap, no binary search.

> **Why it’s great for interviews**  
> *Linear scans* show you can identify the real bottleneck.  
> *Greedy reasoning* demonstrates you’re not brute‑forcing but rather exploiting problem structure.  
> *Clean code* (variable names, separate loops for each order) shows maintainability.

---

#### 4.5 Edge Cases & Defensive Coding (Bad)  

- **Single ride** per category: our formula still works because `min()` over one element is fine.  
- **Large integer sums**: `startTime + duration` can reach 200 000, still inside 32‑bit signed int.  
- **Answer initialization**: use `Integer.MAX_VALUE` (Java), `INT_MAX` (C++), or `float('inf')` (Python).  

---

#### 4.6 “Ugly” – Things Interviewers Might Ask  

1. **Can we solve it in-place?**  
   *Yes – the above solution uses only constant extra space.*  

2. **What if the arrays were unsorted?**  
   *No impact – we’re only doing linear scans.*  

3. **What about more than two categories?**  
   *The same idea generalises: keep the minimal finish time of the first category, iterate over the rest.*  

4. **Proof of optimality** – you can give a brief argument:  
   *Given an order, picking the first ride with the smallest finish time can’t delay the second ride, because waiting is allowed.*  

5. **Space‑optimised alternative** – use streaming (Java 8 streams, Python generators).  

Interviewers may test your ability to **prove** why the greedy choice is optimal; be ready to articulate that the `max` expression exactly models the waiting time and that choosing a later finish for the first ride can never help the second ride.

---

#### 4.7 Take‑Away Checklist  

- ☐ Understand the two‑step decision (order + specific ride).  
- ☐ Pre‑compute `minLandFinish` & `minWaterFinish`.  
- ☐ Scan the other category and compute finish time using `max`.  
- ☐ Keep the global minimum.  
- ☐ Write clean, self‑documenting code (clear variable names, comments).  
- ☐ Time‑complexity: **O(n+m)**, Space‑complexity: **O(1)**.  

---

#### 4.8 How This Helps You Get Hired  

* Interviewers want candidates who can:  
  - Reduce a combinatorial explosion to a linear scan.  
  - Reason about “waiting” as a `max` operation.  
  - Produce clean, language‑agnostic code.  

* Show this solution on your GitHub, include the discussion above, and tag your profile with `Java`, `Python`, `C++`, `LeetCode`, `Greedy`, `Array`.  

> **Keywords**: earliest finish time, land rides, water rides, greedy algorithm, interview problem, LeetCode medium, Java coding interview, Python interview, C++ interview.

---

#### 4.9 TL;DR  

```
1️⃣ Compute earliest finish of the first category
2️⃣ For every ride of the second category:
    finish = duration + max( earliest_finish_of_first, opening_time )
3️⃣ Repeat for both orders and take the global minimum
```

> **Time:** O(n+m)  
> **Space:** O(1)  

Happy coding, and may your next interview feel like a smooth ride! 🎢

---



### 5.  Quick Test – Sample Inputs  

| Input | Expected |
|-------|----------|
| `landStartTime=[1,2,4]`<br>`landDuration=[5,4,2]`<br>`waterStartTime=[3,5,1]`<br>`waterDuration=[6,3,4]` | `9` |
| `landStartTime=[5]`<br>`landDuration=[5]`<br>`waterStartTime=[1]`<br>`waterDuration=[1]` | `6` |

Run any of the snippets above in your favourite IDE to verify.

---

### 6.  Final Words  

This problem is a great showcase for:

-  *Linear‑time greedy algorithms*  
-  *Clear, maintainable code*  
-  *Cross‑language fluency*  

Add the reference implementation to your personal interview cheat‑sheet and you’ll have a solid talking point for any technical interview. Good luck! 🚀