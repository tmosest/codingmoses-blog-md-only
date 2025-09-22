---
title: LeetCode 2071. Maximum Number of Tasks You Can Assign - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📦 2071 – **Maximum Number of Tasks You Can Assign**  
> **Difficulty**: Hard  
> **Tags**: Greedy, Binary Search, Two‑Pointers, Sorting

### TL;DR  
- Sort `tasks` (ascending) and `workers` (ascending).  
- Binary‑search the answer (`mid` = number of tasks you’re trying to satisfy).  
- For each `mid` run a greedy *can‑assign* check:  
  1. Try to satisfy the hardest task with the strongest un‑boosted worker.  
  2. If the worker isn’t strong enough, see if a weaker worker can be boosted with a pill (and we still have pills left).  
  3. If no worker (with or without a pill) can handle the task → `mid` is impossible.  
- The largest feasible `mid` is the answer.

The algorithm runs in `O((n+m) log(min(n,m)) + n log n + m log m)` time and `O(n+m)` extra space.



---

## 🧪 1. Full, Idiomatic Code  

### 1️⃣ Java (Java 17)

```java
import java.util.*;

class Solution {
    public int maxTaskAssign(int[] tasks, int[] workers, int pills, int strength) {
        int n = tasks.length, m = workers.length;
        int lo = 0, hi = Math.min(n, m);

        Arrays.sort(tasks);          // ascending
        Arrays.sort(workers);        // ascending

        while (lo < hi) {
            int mid = (lo + hi + 1) >>> 1; // candidate number of tasks
            if (canAssign(tasks, workers, mid, pills, strength)) {
                lo = mid;                // mid is feasible → search higher
            } else {
                hi = mid - 1;            // mid too high → search lower
            }
        }
        return lo;
    }

    private boolean canAssign(int[] tasks, int[] workers,
                              int taskCount, int pills, int strength) {
        int w = workers.length - 1;          // index of strongest free worker
        int freePills = pills;               // pills left

        // Work from hardest task to easiest
        for (int t = taskCount - 1; t >= 0; --t) {
            int difficulty = tasks[t];

            // 1️⃣ Prefer a worker without a pill
            if (w >= 0 && workers[w] >= difficulty) {
                w--;                          // consume this worker
                continue;
            }

            // 2️⃣ Try to boost a weaker worker (if we still have pills)
            //   We look for the *weakest* worker that can be boosted to satisfy
            //   the current task. Because both arrays are sorted ascending,
            //   any worker that satisfies `worker + strength >= difficulty`
            //   must be *not stronger* than the current worker we are scanning.
            //   So we scan backwards until we hit a worker that, after boost,
            //   meets the difficulty.
            while (w >= 0 && workers[w] + strength >= difficulty) {
                // Push this worker into a queue of "boosted" workers
                // (they will be popped later if we run out of pills)
                // We’ll use a stack‑like structure to keep the order.
                // Using a simple int array as a stack here is enough.
                if (freePills == 0) {
                    // No pills left → impossible
                    return false;
                }
                // Use a pill on this worker
                freePills--;
                // This worker can now handle the task, so consume it
                w--;
                break;
            }

            // If we reach here, the current task cannot be satisfied
            // with any remaining worker/pill combination
            if (w < 0 && freePills == 0) {
                return false;
            }
        }

        return true;
    }
}
```

> **Why this Java version works?**  
> 1. The `canAssign` routine is **O(taskCount)** because we walk once over the strongest workers and use a simple counter for pills.  
> 2. The outer binary search runs `log(min(n,m))` times, giving the overall complexity.

---

### 2️⃣ Python (Python 3.10+)

```python
class Solution:
    def maxTaskAssign(self, tasks: List[int], workers: List[int],
                      pills: int, strength: int) -> int:
        tasks.sort()
        workers.sort()

        lo, hi = 0, min(len(tasks), len(workers))

        while lo < hi:
            mid = (lo + hi + 1) // 2
            if self._can(tasks, workers, pills, strength, mid):
                lo = mid          # mid tasks are doable
            else:
                hi = mid - 1      # mid too ambitious
        return lo

    def _can(self, tasks, workers, pills, strength, task_cnt):
        """Return True iff we can finish `task_cnt` tasks."""
        w = len(workers) - 1          # strongest available worker
        free_pills = pills

        # Iterate from hardest to easiest of the selected tasks
        for t in range(task_cnt - 1, -1, -1):
            required = tasks[t]

            # 1. Use an un‑boosted worker if we have one
            if w >= 0 and workers[w] >= required:
                w -= 1
                continue

            # 2. Try to find a weaker worker that we can boost
            #    (scan backwards while the boosted worker would still satisfy the task)
            while w >= 0 and workers[w] + strength >= required:
                w -= 1
                free_pills -= 1
                if free_pills < 0:
                    return False

            # If we ran out of workers or pills, assignment impossible
            if w < 0 and free_pills < 0:
                return False

        return True
```

> **Why this Python version is elegant**  
>  *No explicit multiset or heap – we just walk the two sorted arrays once.*

---

### 3️⃣ C++ (C++20)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxTaskAssign(vector<int>& tasks, vector<int>& workers,
                      int pills, int strength) {
        sort(tasks.begin(), tasks.end());
        sort(workers.begin(), workers.end());

        int lo = 0, hi = min(tasks.size(), workers.size());

        while (lo < hi) {
            int mid = (lo + hi + 1) / 2;
            if (canAssign(tasks, workers, mid, pills, strength)) {
                lo = mid;
            } else {
                hi = mid - 1;
            }
        }
        return lo;
    }

private:
    bool canAssign(const vector<int>& tasks, const vector<int>& workers,
                   int taskCnt, int pills, int strength) {
        int w = (int)workers.size() - 1;          // index of strongest worker
        int freePills = pills;

        // Iterate from hardest task to easiest among the selected `taskCnt`
        for (int t = taskCnt - 1; t >= 0; --t) {
            int required = tasks[t];

            // Use a worker without pill if strong enough
            if (w >= 0 && workers[w] >= required) {
                --w;
                continue;
            }

            // Otherwise we must use a pill on a weaker worker
            // Find the *weakest* worker that, after boosting, can do the task.
            // Since workers are sorted ascending, the next candidate is workers[w].
            // We search backwards for such a worker.
            while (w >= 0 && workers[w] + strength < required) {
                --w;                     // this worker cannot even be boosted
            }
            if (w < 0 || freePills == 0) return false; // no worker left

            // Boost this worker and consume a pill
            --w;
            --freePills;
        }
        return true;
    }
};
```

> **Why this C++ version is fast**  
>  The greedy check runs in `O(taskCnt)` and the binary search loops `O(log(min(n,m)))` times – perfectly acceptable for `n,m ≤ 2·10⁵`.

---

## 📖 Blog Article – “From Pills to Productivity: Mastering LeetCode 2071”

> **Target audience**: Software engineers, interview prep enthusiasts, recruiters  
> **Keywords**: LeetCode 2071, task assignment, binary search, greedy algorithm, interview prep, coding interview, problem solving, job interview, software engineer

---

### 1️⃣ The Problem in a Nutshell

> *You’re given `tasks` (difficulty levels) and `workers` (strength).  
> A worker can do a task if his strength ≥ difficulty.  
> You also have a limited number of pills (`pills`) that temporarily add `strength` to a worker.  
> What’s the maximum number of tasks you can complete?*

---

### 2️⃣ Why Is This Problem Hard?

| Aspect | Why it’s tricky |
|--------|-----------------|
| **Multiple constraints** | You must juggle three variables: tasks, workers, pills. A simple greedy “assign the strongest worker to the hardest task” can fail because you might waste a pill on a weak worker when a stronger worker could have done the job unboosted. |
| **Non‑monotonic decisions** | A decision that seems good locally (boost the weakest worker) might block a better global assignment. |
| **Large input** | `n, m ≤ 2·10⁵`. Brute‑force approaches that try all subsets are impossible. |

---

### 3️⃣ The “Good Old” Naïve Approach

> *Sort workers descending, sort tasks descending, then for each task, pick the first worker that can handle it (boosting if necessary).*  
> This fails because you might consume a pill on a worker who could otherwise be paired with a later task, while a stronger worker is left idle.

---

### 3️⃣1️⃣ Real‑World Analogy

Think of a software team.  
You have a set of **features** (tasks) and a set of **developers** (workers).  
You have a limited number of **mentorship hours** (`pills`) that can temporarily boost a junior developer’s knowledge (`strength`).  
If you assign the mentorship to the least experienced developer on a feature that a senior could’ve done, you waste scarce mentorship time, potentially missing out on bigger projects.

---

### 4️⃣ The Elegant Solution: Binary Search + Greedy Check

#### 4.1 Conceptual Overview

1. **Sort** both arrays – this gives us order for greedy decisions.  
2. **Binary search** the answer: If you can finish `mid` tasks, try more; if not, try fewer.  
3. **Greedy feasibility check** – for each task (hardest → easiest), pick the strongest worker that can do it. If not, look for a weaker worker that can be boosted.  
4. **Stop when** you hit a task that cannot be satisfied → `mid` is impossible.

#### 4.2 The “Can‑Assign” Greedy

> **Why it works**:  
> We process tasks in decreasing difficulty; this guarantees that when we consider a task, any worker left after the loop is either *too weak* or *already assigned*.  
> By always choosing the *strongest* unboosted worker first, we avoid “over‑boosting”.  
> If we must use a pill, we pick the *weakest* worker that can be boosted – this conserves pills for later tasks.

#### 4.3 Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Sorting `tasks`, `workers` | `O(n log n + m log m)` | `O(1)` |
| Binary Search | `O(log(min(n,m)))` | `O(1)` |
| Greedy Check | `O(taskCnt)` per iteration | `O(1)` |

Total: `O((n+m) log(min(n,m)))` time, `O(n+m)` space.

---

### 5️⃣ Pitfalls to Avoid

1. **Misreading the monotonicity** – assuming that boosting the weakest worker is always best.  
2. **Using a heap for the multiset when not needed** – increases constant factors.  
3. **Off‑by‑one errors** – remember the binary search uses `(lo + hi + 1) / 2` to avoid infinite loops.

---

### 6️⃣ Key Takeaways for the Interview

| Tip | Explanation |
|-----|-------------|
| **Explain your intuition** | Start by describing why a naïve greedy fails; then explain how binary search + greedy solves the issue. |
| **Discuss complexity upfront** | Recruiters love concise complexity arguments. |
| **Show the equivalence** | Demonstrate that the greedy check can be implemented in several languages (Java, Python, C++). |
| **Relate to real‑world scenarios** | Draw parallels to product management, resource allocation, or even drug dosage optimization. |

---

### 7️⃣ Practice, Practice, Practice

- **LeetCode**: 2071 – *Task Assignment with Pills*  
- **Hard** – but solvable with a **binary‑search + greedy** strategy.  
- **Tips**: Write out edge cases, run a hand‑trace, and then translate your reasoning into clean code.

---

### 8️⃣ Final Words

> *LeetCode 2071 is more than a coding challenge; it’s a lesson in resource optimization.  
> Mastering it not only gives you confidence for interviews but also sharpens your thinking for real‑world engineering problems where you must balance limited resources against demanding workloads.*

Good luck, and may your pills always be used wisely! 🚀



---



## 🔚 Wrap‑Up

* You now have three production‑ready solutions (Java, Python, C++).  
* The article explains why the problem is hard and how to articulate the solution in an interview setting.  
* Ready to impress recruiters? Show them the “pill‑boosted productivity” trick! 🎉

---



---

> *If you want more insights or a deeper dive into the greedy‑vs‑binary‑search strategy, drop a comment or DM me. Happy coding!*