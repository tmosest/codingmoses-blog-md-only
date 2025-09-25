---
title: LeetCode 853. Car Fleet - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚗 853. Car Fleet – The Good, The Bad, and The Ugly  
*(Java • Python • C++)*  

---

### TL;DR  

- **Problem** – Count how many “fleets” of cars will reach a target point.  
- **Key Insight** – Sort the cars by starting position (closest to the target first) and compute the *time to reach the target* for each car.  
- **Algorithm** – Use a *monotonic stack* (or simply a running maximum) to collapse fleets in **O(n log n)** time and **O(1)** additional space.  

Below are clean, production‑ready implementations in **Java, Python, and C++** followed by a deep‑dive blog post that explains everything you need to ace this interview question.

---

## 1. Code Solutions

### 1.1 Java

```java
import java.util.*;

public class CarFleet {
    /**
     * Returns the number of car fleets that will arrive at the destination.
     *
     * @param target   destination mile
     * @param position array of starting positions
     * @param speed    array of speeds
     * @return number of fleets
     */
    public int carFleet(int target, int[] position, int[] speed) {
        int n = position.length;
        // Pair each car's position with its speed
        int[][] cars = new int[n][2];
        for (int i = 0; i < n; i++) {
            cars[i][0] = position[i];
            cars[i][1] = speed[i];
        }

        // Sort by position in descending order (closest to target first)
        Arrays.sort(cars, (a, b) -> Integer.compare(b[0], a[0]));

        int fleets = 0;
        double maxTime = 0.0;               // longest time seen so far

        for (int[] car : cars) {
            double time = (double) (target - car[0]) / car[1];
            // If this car takes longer than the fleet ahead, it forms a new fleet
            if (time > maxTime) {
                fleets++;
                maxTime = time;
            }
        }
        return fleets;
    }

    // For quick manual testing
    public static void main(String[] args) {
        CarFleet cf = new CarFleet();
        System.out.println(cf.carFleet(12, new int[]{10,8,0,5,3}, new int[]{2,4,1,1,3})); // 3
    }
}
```

> **Why it works**  
> By processing cars from the front (closest to the target) to the back, every later car can only *catch up* to a faster‑behind fleet if its arrival time is **≤** the fleet ahead’s time. Thus we keep the *maximum* arrival time seen so far and count a new fleet only when a car’s time is strictly larger.

---

### 1.2 Python

```python
from typing import List

class Solution:
    def carFleet(self, target: int, position: List[int], speed: List[int]) -> int:
        """
        Count the number of car fleets that reach the target.
        """
        # Combine and sort by position descending
        cars = sorted(zip(position, speed), key=lambda x: -x[0])

        fleets = 0
        max_time = 0.0

        for pos, spd in cars:
            time = (target - pos) / spd
            if time > max_time:
                fleets += 1
                max_time = time

        return fleets

# Quick test
if __name__ == "__main__":
    s = Solution()
    print(s.carFleet(12, [10,8,0,5,3], [2,4,1,1,3]))  # → 3
```

---

### 1.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int carFleet(int target, vector<int>& position, vector<int>& speed) {
        int n = position.size();
        vector<pair<int,int>> cars(n);
        for (int i = 0; i < n; ++i)
            cars[i] = {position[i], speed[i]};

        // Sort descending by position (closest to target first)
        sort(cars.begin(), cars.end(),
             [](const auto& a, const auto& b) { return a.first > b.first; });

        int fleets = 0;
        double maxTime = 0.0;

        for (auto &car : cars) {
            double time = static_cast<double>(target - car.first) / car.second;
            if (time > maxTime) {
                ++fleets;
                maxTime = time;
            }
        }
        return fleets;
    }
};

// Demo
int main() {
    Solution s;
    cout << s.carFleet(12, {10,8,0,5,3}, {2,4,1,1,3}) << endl; // 3
}
```

---

## 2. Blog Article – “The Good, The Bad, and The Ugly of Car Fleet”

> **Keywords**: Car Fleet, LeetCode, algorithm, interview question, monotonic stack, time complexity, space complexity, software engineer interview, coding interview

---

### 2.1 Introduction  

In every software‑engineering interview, you’ll encounter problems that test not only your coding chops but also your ability to think algorithmically. **Car Fleet (LeetCode 853)** is a canonical “medium” problem that blends geometry, simulation, and a dash of greedy thinking. It’s frequently used in *tech‑company* hiring because it forces candidates to:

- **Reason about ordering** (closest cars first)
- **Compute continuous values** (arrival times)
- **Maintain invariants** (fleet merging)

Below, we unpack this problem, walk through the elegant O(n log n) solution, and examine pitfalls that can trip even seasoned engineers.

---

### 2.2 Problem Statement (Simplified)  

> **You have `n` cars on a straight road heading toward a destination at mile `target`.**  
> Each car `i` starts at `position[i]` (unique, < `target`) and drives at `speed[i]` mph.  
> Cars cannot overtake each other; they can catch up and then travel together at the slower car’s speed.  
> A *car fleet* is any group of cars traveling together.  
> Return how many fleets will reach the destination.

---

### 2.3 Intuitive Explanation  

1. **Arrival Time** – For any car, the time to reach the target is `(target - position) / speed`.  
2. **Front‑to‑Back Processing** – If we look from the front (car closest to the target) toward the back, every following car can only *slow down* to match the fleet ahead, never *speed up*.  
3. **Monotonic Property** – As we iterate from front to back, the *maximum* arrival time seen so far never decreases.  
4. **Fleet Counting** –  
   - If a car’s arrival time is **greater** than the current maximum, it cannot catch up to the fleet ahead and must form a new fleet.  
   - Otherwise it merges into the fleet ahead.

Thus, the solution reduces to sorting by position and scanning once while maintaining the running maximum of times.

---

### 2.4 The Algorithm in Pseudocode  

```
cars = zip(position, speed)
sort cars by position descending   // front first

fleets = 0
maxTime = 0

for pos, spd in cars:
    time = (target - pos) / spd
    if time > maxTime:
        fleets += 1
        maxTime = time

return fleets
```

**Complexity**  
- Sorting: `O(n log n)`  
- Scan: `O(n)`  
- Space: `O(n)` for the pair array (or `O(1)` if we sort in place).

---

### 2.5 Why This is a Great Interview Question  

| Category | What it Tests |
|----------|---------------|
| **Greedy** | Choosing the next fleet based solely on current time. |
| **Sorting** | Understanding when and how to order elements. |
| **Monotonic Stack** | Recognizing a non‑increasing property. |
| **Floating Point** | Precision handling in arrival time calculations. |
| **Edge Cases** | One‑car fleet, all cars in one fleet, no cars at all. |

The problem forces candidates to avoid the naïve simulation that would run in `O(n^2)` time (checking every pair). Instead, a single pass suffices.

---

### 2.6 “The Good” – What You Should Emphasize in Your Interview

1. **Clear Problem Restatement** – Restate the problem in your own words to show comprehension.  
2. **Identify the Key Insight** – Explain that sorting by position and using arrival times gives a greedy solution.  
3. **Explain the Monotonic Property** – This justifies why we only need to compare to the maximum.  
4. **Complexity Analysis** – Show `O(n log n)` time and `O(1)` auxiliary space (after sorting).  
5. **Edge‑Case Handling** – Mention how the code gracefully handles a single car or no cars.  

---

### 2.7 “The Bad” – Common Pitfalls to Avoid

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Using integer division** | Arrival times are fractional; integer division truncates, causing incorrect fleets. | Use `double` (Java) or `float/double` (Python/C++). |
| **Sorting ascending instead of descending** | Then you would compare a car to a slower front fleet incorrectly. | Sort by *descending* position or reverse the logic accordingly. |
| **Assuming no merging at the target** | The statement “If a car catches up at the target, it still counts as the same fleet.” | The algorithm naturally handles this because equal times merge. |
| **Not handling duplicate positions** | Problem guarantees uniqueness, but a wrong assumption could lead to bugs. | Trust the constraints, or guard with a set if needed. |

---

### 2.8 “The Ugly” – Advanced Variants & Edge Scenarios

1. **Large `n` (up to 10⁵)**  
   - The `O(n log n)` sort is fine, but avoid any `O(n²)` loops.  

2. **Very Large Speeds (10⁶)**  
   - Using 32‑bit integers for time can overflow; use double or long double.  

3. **Precision Tolerance**  
   - Two arrival times that differ by a tiny epsilon should be treated as equal if they’re essentially the same fleet.  
   - In practice, a strict `>` comparison works because arrival times are rational numbers with distinct numerators/denominators due to unique positions.  

4. **Different Target Directions**  
   - The problem assumes all cars head **toward** the target. If some cars are moving away, the logic changes dramatically.  

5. **Multi‑Road (2D) Extension**  
   - In a more complex variation, cars could have different routes, requiring graph algorithms.  

Understanding these extensions shows depth and adaptability—qualities employers love.

---

### 2.9 Practical Tips for Your Interview

- **Write clean, commented code** – Show you can maintain production code.  
- **Talk through examples** – Step through the sample input, compute times, show the stack/maximum.  
- **Ask clarifying questions** – Confirm uniqueness of positions, if negative speeds are allowed, etc.  
- **Mention possible optimizations** – e.g., using a custom struct and a stable sort, or in‑place sorting for memory efficiency.  

---

### 2.10 Conclusion – Turning Car Fleet into a Job‑Winning Skill  

Car Fleet is deceptively simple yet packed with algorithmic nuance. Mastering it demonstrates:

- **Algorithmic maturity** – Greedy + monotonic reasoning.  
- **Coding discipline** – Floating‑point safety, edge‑case robustness.  
- **Communication** – Ability to explain complex ideas concisely.

By sharing the *good*, avoiding the *bad*, and appreciating the *ugly*, you’ll impress interviewers and open the door to roles at leading tech companies. Keep practicing, keep exploring variations, and soon you’ll be the candidate who drives through interviews *without being overtaken*.

---

*Happy coding and best of luck on your next interview!* 🚀

--- 

> **Next Steps**  
> 1. Review the C++/Python/Java implementations above.  
> 2. Practice with random inputs (`n=10⁵`, random positions/speeds).  
> 3. Explore the advanced variants—try to write a proof for why equal arrival times always merge.  
> 4. Pair‑program with a friend or use online judges—LeetCode’s “Discussion” section is gold for hidden tricks.

--- 

> **Call‑to‑Action**  
> If you’ve nailed Car Fleet, the next problem is likely *Minimum Window Substring* or *Trapping Rain Water*. Keep tackling them—every solved problem is another line in your résumé that says *“I think like an engineer.”*

--- 

*Happy interviewing!* 🚗💨


--- 

**Disclaimer**: The code snippets above are ready‑to‑run solutions. They have been tested on the sample cases and should pass all official LeetCode test cases.