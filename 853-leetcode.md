---
title: LeetCode 853. Car Fleet - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚗 853. Car Fleet – “The Good, The Bad & The Ugly”  
*From algorithm design to interview‑ready code – all in one post!*

---

### TL;DR  
- **Problem**: Count how many “fleets” of cars reach a target without overtaking.  
- **Core idea**: Process cars from the one *closest to the target* backward, computing the time each would take to arrive. A stack of monotonically increasing arrival times gives the answer.  
- **Complexities**:  
  - Time: **O(n log n)** for sorting, then **O(n)**.  
  - Space: **O(n)** for the stack.  

---

## 1. The Problem in a Nutshell

You have `n` cars positioned on a one‑way road heading toward a destination at `target`.  
`position[i]` (0 ≤ `position[i]` < `target`) and `speed[i]` (positive).  

Cars can **catch up** but never **overtake**.  
When a faster car catches a slower one it joins that car’s “fleet” and the fleet moves at the slower speed.

**Return** the number of fleets that will reach the destination.

---

## 2. The Good – Why This Problem Is Fun

| Why it’s enjoyable | How it helps you |
|---------------------|------------------|
| **Pure math + logic** – no fancy data structures, just arithmetic and a stack. | Shows you can solve seemingly hard problems with simple tools. |
| **Common interview question** – appears on LeetCode, InterviewBit, and real interviews. | Master it to ace algorithm rounds. |
| **Illustrates monotonic stack** – a reusable pattern for many problems. | Recognizing patterns saves time in interviews. |

---

## 3. The Bad – What Traps You

| Issue | Why it hurts |
|-------|--------------|
| **Sorting order confusion** – some solutions sort ascending, others descending. | Mistakes lead to wrong answer or O(n²) time. |
| **Floating point precision** – using double division can introduce tiny errors. | May affect comparison of arrival times. |
| **Edge cases** – a car starting at the target or cars with same arrival times. | Overlooking them causes wrong fleet counts. |
| **Large input** – up to 10⁵ cars. | Naïve O(n²) simulation fails. |

---

## 4. The Ugly – Common Bad Practices

1. **Brute‑force simulation** (checking each pair of cars) – O(n²) time.  
2. **Ignoring sorting** – leads to mis‑counting because cars behind might never catch up if processed incorrectly.  
3. **Using recursion** for the stack – can overflow the call stack on large inputs.  
4. **Hard‑coding float precision** – `1e-9` or arbitrary epsilon may still fail on edge cases.

---

## 5. The Clean Solution – Monotonic Stack

1. **Compute arrival time** for each car:  
   ```time = (target - position) / speed```
2. **Sort cars by starting position descending** (closest to target first).  
3. **Iterate** through sorted cars, maintaining a stack of *arrival times* that are **non‑increasing**.  
   - If the current car’s arrival time is **greater** than the stack top, it will *not* catch up; push it as a new fleet.  
   - Otherwise, it merges into the fleet represented by the stack top; no new fleet is added.

Why this works?  
Cars processed later are *behind* earlier ones. If a later car would arrive *later*, it can’t catch up; otherwise, it will merge with the fleet ahead.

---

## 6. Code – 3 Languages

### 6.1 Java

```java
import java.util.*;

public class CarFleet {
    public int carFleet(int target, int[] position, int[] speed) {
        int n = position.length;
        // Create an array of car indices sorted by position descending
        Integer[] idx = new Integer[n];
        for (int i = 0; i < n; i++) idx[i] = i;
        Arrays.sort(idx, (a, b) -> Integer.compare(position[b], position[a]));

        Deque<Double> stack = new ArrayDeque<>();
        for (int i : idx) {
            double time = (double)(target - position[i]) / speed[i];
            if (stack.isEmpty() || time > stack.peek()) {
                stack.push(time);
            }
            // else: current car merges into the fleet represented by stack.peek()
        }
        return stack.size();
    }

    public static void main(String[] args) {
        CarFleet cf = new CarFleet();
        System.out.println(cf.carFleet(12, new int[]{10,8,0,5,3},
                                     new int[]{2,4,1,1,3})); // 3
    }
}
```

### 6.2 Python

```python
from typing import List
from collections import deque

class Solution:
    def carFleet(self, target: int, position: List[int], speed: List[int]) -> int:
        # Sort indices by position descending
        idx = sorted(range(len(position)), key=lambda i: -position[i])
        stack = deque()
        for i in idx:
            time = (target - position[i]) / speed[i]
            if not stack or time > stack[-1]:
                stack.append(time)
        return len(stack)

# Demo
if __name__ == "__main__":
    s = Solution()
    print(s.carFleet(12, [10,8,0,5,3], [2,4,1,1,3]))  # 3
```

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int carFleet(int target, vector<int>& position, vector<int>& speed) {
        int n = position.size();
        vector<int> idx(n);
        iota(idx.begin(), idx.end(), 0);
        sort(idx.begin(), idx.end(),
             [&](int a, int b){ return position[a] > position[b]; });

        vector<double> stack;
        for (int i : idx) {
            double time = double(target - position[i]) / speed[i];
            if (stack.empty() || time > stack.back()) {
                stack.push_back(time);
            }
            // else: merge into existing fleet
        }
        return stack.size();
    }
};

int main() {
    Solution sol;
    cout << sol.carFleet(12, {10,8,0,5,3}, {2,4,1,1,3}) << endl; // 3
}
```

---

## 7. SEO‑Optimized Blog Post

> **Title**: “Master LeetCode 853 – Car Fleet: The Good, The Bad, and The Ugly (Java, Python, C++)”

> **Meta Description**: “Learn how to crack LeetCode’s Car Fleet problem with a step‑by‑step solution, clean code in Java, Python, and C++, and interview tips. Get ready for your next coding interview!”

### 7.1 Introduction

Car Fleet (LeetCode #853) is a classic “monotonic stack” interview problem. It tests your ability to think about time, order, and group behavior in a concise O(n log n) solution. In this post, we’ll walk through:

- The intuition behind the algorithm  
- Why many naive solutions fail  
- The clean stack‑based solution  
- Full implementations in Java, Python, and C++  
- Interview‑ready tips and pitfalls

### 7.2 Problem Breakdown

Give a brief recap of input, output, constraints, and example explanations.

### 7.3 The Good: Intuition & Algorithm

Explain the concept of arrival times, sorting by position, and the stack logic in plain words. Use a diagram or simple ASCII art if possible.

### 7.4 The Bad: Common Pitfalls

Highlight the sorting direction, floating point issues, and edge cases. Provide a quick “cheat sheet” of what not to do.

### 7.5 The Ugly: Why Not Brute Force

Show a simple O(n²) brute‑force code snippet and explain its time complexity. Stress that interviewers expect optimal solutions.

### 7.6 Full Code

Insert the Java, Python, and C++ snippets from above. Add comments to highlight key steps.

### 7.7 Interview Tips

- Be ready to explain your stack intuition on the spot.  
- Mention time and space complexity.  
- Talk about handling large inputs and precision.

### 7.8 Conclusion

Wrap up with a summary of the key takeaways and a motivational push for readers to practice the problem until they can explain it confidently.

### 7.9 SEO & Social Sharing

- Use keywords: “LeetCode 853”, “Car Fleet solution”, “Java Car Fleet”, “Python Car Fleet”, “C++ Car Fleet”, “interview algorithm”, “monotonic stack”.
- Include a call‑to‑action: “Like, Share, and Subscribe for more algorithm insights!”

---

## 8. Final Checklist Before Your Interview

1. **Understand the problem** – read constraints, examples, edge cases.  
2. **Explain your solution** – start with the idea of arrival times and sorting.  
3. **Show the stack logic** – why it guarantees the correct fleet count.  
4. **Time & Space** – O(n log n) & O(n).  
5. **Write clean code** – use meaningful variable names, comment the stack steps.  
6. **Test** – run the provided examples and some edge cases (e.g., one car, all same speed, cars starting at target).  

Good luck – now you’re ready to shine with **Car Fleet**! 🚀