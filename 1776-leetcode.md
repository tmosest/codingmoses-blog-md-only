---
title: LeetCode 1776. Car Fleet II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚗 1776. Car Fleet II – Master the Hard LeetCode Problem (Java | Python | C++)  

> **Ready to land that tech‑interview?**  
> This post walks you through the most efficient solution for LeetCode 1776 “Car Fleet II” using a monotonic stack. We’ll show you clean code in **Java, Python, and C++**, explain why it’s O(n), and highlight the *good, the bad, and the ugly* of this classic algorithmic challenge.  

---

## 📄 Problem Recap

> **Cars on a One‑Lane Road**  
> There are `n` cars traveling in the same direction.  
> Each car `i` starts at `position[i]` with `speed[i]`.  
> Positions are strictly increasing (`position[i] < position[i+1]`).  
> When a faster car catches a slower one, they merge into a single “fleet” that travels at the slower car’s speed.  
>  
> **Return** an array `answer` where `answer[i]` is the time (in seconds) when car `i` first collides with the next car, or `-1` if it never collides.

> **Constraints**  
> - `1 ≤ n ≤ 10⁵`  
> - `1 ≤ position[i], speed[i] ≤ 10⁶`  

> **Typical Test Case**  
> ```text
> Input : cars = [[1,2],[2,1],[4,3],[7,2]]
> Output: [1.00000,-1.00000,3.00000,-1.00000]
> ```

---

## 🧠 The Core Idea – Monotonic Stack

The key observation:

1. **Only faster cars can catch up to slower ones ahead.**  
2. When a car catches up, it inherits the speed of the slower car (the one it collides with).  
3. Therefore, from the rightmost car to the left, we maintain a **monotonic stack** of candidates that the current car can potentially collide with.  
   *The stack stores indices of cars that are strictly slower than the car below them in the stack.*

For each car (traversing from right to left):

- Pop cars from the stack that:
  - Are faster than the current car (no collision), **or**
  - Would collide **after** the popped car has already collided with someone else (hence their “effective” speed changes).

- The first car left on the stack that satisfies the collision condition is the one that the current car will collide with first.  
- If no such car remains, the answer is `-1`.

This algorithm runs in **O(n)** time because each car is pushed and popped at most once.

---

## 💻 Code Snippets

Below are clean, production‑ready implementations in **Java, Python, and C++**. Each follows the same monotonic‑stack logic.

---

### 1. Java (Clean, O(n) Monotonic Stack)

```java
import java.util.*;

public class Solution {
    public double[] getCollisionTimes(int[][] cars) {
        int n = cars.length;
        double[] ans = new double[n];
        Arrays.fill(ans, -1.0);

        Deque<Integer> stack = new ArrayDeque<>();

        for (int i = n - 1; i >= 0; i--) {
            int posI = cars[i][0], spdI = cars[i][1];

            while (!stack.isEmpty()) {
                int j = stack.peekLast();
                int posJ = cars[j][0], spdJ = cars[j][1];

                // If current car is slower or equal, it will never catch j
                if (spdI <= spdJ) break;

                double t = (double) (posJ - posI) / (spdI - spdJ);

                // If j never collides, or collision happens before j's own collision
                if (ans[j] == -1.0 || t <= ans[j]) {
                    ans[i] = t;
                    break;
                } else {
                    // j will change speed before i catches it → discard j
                    stack.pollLast();
                }
            }
            stack.offerLast(i);
        }
        return ans;
    }
}
```

---

### 2. Python (3.9+)

```python
from typing import List

class Solution:
    def getCollisionTimes(self, cars: List[List[int]]) -> List[float]:
        n = len(cars)
        ans = [-1.0] * n
        stack: List[int] = []          # indices of candidate cars

        for i in range(n - 1, -1, -1):
            pos_i, spd_i = cars[i]
            while stack:
                j = stack[-1]
                pos_j, spd_j = cars[j]

                if spd_i <= spd_j:          # cannot catch up
                    break

                t = (pos_j - pos_i) / (spd_i - spd_j)

                if ans[j] == -1.0 or t <= ans[j]:
                    ans[i] = t
                    break
                else:
                    stack.pop()            # j changes speed before i catches it

            stack.append(i)
        return ans
```

---

### 3. C++ (GNU++17)

```cpp
class Solution {
public:
    vector<double> getCollisionTimes(vector<vector<int>>& cars) {
        int n = cars.size();
        vector<double> ans(n, -1.0);
        vector<int> st;                 // stack of indices

        for (int i = n - 1; i >= 0; --i) {
            int pos_i = cars[i][0], spd_i = cars[i][1];
            while (!st.empty()) {
                int j = st.back();
                int pos_j = cars[j][0], spd_j = cars[j][1];

                if (spd_i <= spd_j) break;      // cannot catch

                double t = double(pos_j - pos_i) / (spd_i - spd_j);

                if (ans[j] < 0 || t <= ans[j]) {
                    ans[i] = t;
                    break;
                } else {
                    st.pop_back();              // j will change speed first
                }
            }
            st.push_back(i);
        }
        return ans;
    }
};
```

---

## 📈 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Main loop (traversing `n` cars) | `O(n)` | `O(n)` (stack + answer array) |
| Each car pushed/popped once | | |

*Overall:* **Time O(n), Space O(n)** – optimal for `n ≤ 10⁵`.

---

## ⚡️ Good, Bad, & Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Concept** | Elegant monotonic stack; linear time | Requires careful handling of “future collisions” | Hard to reason initially |
| **Implementation** | Compact; easy to debug | Must maintain correct `while` conditions | Off‑by‑one errors, division by zero if speeds equal |
| **Readability** | Clear variable names (`ans`, `st`) | Stack logic may confuse beginners | Over‑engineering (e.g., storing extra data) |
| **Edge Cases** | Handles all `-1` cases | Need to check `spd_i <= spd_j` to avoid division by zero | Forgetting to compare against `ans[j]` leads to wrong results |

> **Tip:** Always walk through the stack with a small example. Visualizing the stack states helps prevent bugs.

---

## 🎯 Interview‑Ready Tips

1. **State the observation first.** Explain that a faster car can only catch a slower one and that after catching, the fleet moves at the slower speed.  
2. **Describe the monotonic stack.** Emphasize that the stack holds “potential colliders” and why we traverse from right to left.  
3. **Walk through a test case.** Show how the stack evolves step by step.  
4. **Complexity discussion.** Highlight `O(n)` time & `O(n)` space, and why no `O(n²)` nested loops are needed.  
5. **Edge handling.** Mention the check `spd_i <= spd_j` and the comparison with `ans[j]`.  

---

## 📌 SEO‑Optimized Meta Data

```html
<title>LeetCode 1776 Car Fleet II – Java, Python & C++ Solutions | Interview Preparation</title>
<meta name="description" content="Master the LeetCode hard problem Car Fleet II. Get clean Java, Python, and C++ solutions with a monotonic stack approach. Learn the algorithm, complexity, and interview tips.">
<meta name="keywords" content="LeetCode 1776, Car Fleet II, Java solution, Python solution, C++ solution, monotonic stack, algorithm interview, coding interview, car fleet problem">
```

---

## 🎉 Conclusion

Car Fleet II is a textbook example of **using a monotonic stack to transform an apparently quadratic problem into a linear one**. The trick lies in realizing that after a collision, the speed “locks” to the slower car, and that any faster car behind must first clear all faster cars before considering collisions.

With the code snippets above and the insights in this post, you’re ready to:

- **Solve the problem efficiently** in your chosen language.  
- **Explain the algorithm** convincingly to an interviewer.  
- **Showcase your coding‑cleanliness** by using clear, bug‑free implementations.

Good luck, and may the fleets be ever in your favor! 🚀