---
title: LeetCode 2365. Task Scheduler II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code – Three Languages

Below are three **complete, copy‑and‑paste** solutions for LeetCode 2365 – *Task Scheduler II*.  
All three follow the same “next‑available‑day” idea and run in **O(n)** time with **O(n)** extra space.

| Language | Key data structure | Complexity |
|----------|--------------------|------------|
| Java | `HashMap<Integer, Long>` | `O(n)` time, `O(n)` space |
| Python | `dict` (Python 3.6+) | `O(n)` time, `O(n)` space |
| C++ | `unordered_map<int, long long>` | `O(n)` time, `O(n)` space |

> **Tip** – Use a `long` / `long long` for the day counter because the answer can exceed `int` range (`tasks.length ≤ 10⁵`, `space ≤ 10⁵`).

---

### Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    /**
     * LeetCode 2365 – Task Scheduler II
     *
     * @param tasks array of task types
     * @param space minimal number of days before the same type can be run again
     * @return minimal number of days to finish all tasks
     */
    public long taskSchedulerII(int[] tasks, int space) {
        Map<Integer, Long> nextAvailable = new HashMap<>();
        long days = 0L;                     // days elapsed

        for (int t : tasks) {
            // The current day can be at most:
            //   * the next day after the previous task (days + 1)
            //   * the day when this task type is allowed again
            days = Math.max(nextAvailable.getOrDefault(t, 0L), days + 1);

            // After doing this task, the next time we can run type `t`
            // is `days + space + 1`.
            nextAvailable.put(t, days + space + 1);
        }
        return days;
    }
}
```

---

### Python 3

```python
from typing import List

class Solution:
    def taskSchedulerII(self, tasks: List[int], space: int) -> int:
        """
        LeetCode 2365 – Task Scheduler II
        """
        next_available = {}          # task type -> earliest day we can run it again
        days = 0                     # days elapsed

        for t in tasks:
            days = max(next_available.get(t, 0), days + 1)
            next_available[t] = days + space + 1

        return days
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long taskSchedulerII(vector<int>& tasks, int space) {
        unordered_map<int, long long> next;   // task type -> earliest day we can run it again
        long long days = 0;                   // days elapsed

        for (int t : tasks) {
            days = max(next[t], days + 1);
            next[t] = days + space + 1;
        }
        return days;
    }
};
```

> **Remember**: compile with `-std=c++17` (or later) and link the standard library.

---

## 2.  Blog Article – “The Good, the Bad, and the Ugly of LeetCode 2365”

> **Keyword Focus**: *LeetCode Task Scheduler II*, *task scheduling algorithm*, *Java HashMap solution*, *Python dictionary solution*, *C++ unordered_map solution*, *O(n) algorithm*, *job interview tips*, *software engineering interview*, *system design interview*, *LeetCode interview questions*

---

### 2.1  Introduction

If you’re preparing for a software‑engineering interview, you’ve probably seen **LeetCode 2365 – “Task Scheduler II”** in the “Interview Preparation Kit”.  
At first glance it looks like a simple simulation problem, but it’s a great showcase of:

- **Greedy thinking**: “do the next task or skip if it violates the cooldown”.
- **Hash‑map tricks**: keep track of *next‑available* days for each task type.
- **Edge‑case handling**: large inputs, high `space` values, and minimal `tasks`.

In this article, we’ll dissect the problem, present the **fastest** known solution, explore common pitfalls, and finish with interview‑ready talking points.

---

### 2.2  Problem Recap

You’re given:

| Input | Meaning |
|-------|---------|
| `tasks` – an array of `int` | Each element is a *type* of task (not the order of execution). |
| `space` – `int` | Minimum number of **days** that must pass after completing a task of a given type before the same type can be executed again. |

Each day you may:

1. Execute the next task in the list, *if* the cooldown rule allows it.
2. Take a break (do nothing).

Return the **minimum** number of days needed to finish the entire `tasks` array.

---

### 2.3  The “Good” – The Straight‑Forward Simulation (Why It TLEs)

A naive solution iterates day by day, checks the cooldown for the current task, and either runs it or breaks.  
Time complexity: **O(total days)**, which can be up to `O(n * space)` (worst case `10⁵ * 10⁵ = 10¹⁰`).  
Memory: **O(n)** for tracking last run day of each type.

This approach is conceptually easy but *fails* on the upper limits of the constraints. On LeetCode it gets **Time‑Limit‑Exceeded**.

> **Takeaway**: Avoid explicit day simulation when `space` can be huge. We need a *fast‑forward* mechanism.

---

### 2.4  The “Good” – The Hash‑Map “Next‑Available” Solution

#### 2.4.1  Insight

Instead of remembering *when* you last ran a type, remember **when** you’re *allowed* to run it again.  
Call this value `nextAvailable[type]`.  

When you encounter a task of type `t` on day `days`:

- If `days + 1` is later than or equal to `nextAvailable[t]`, you can run it.
- Otherwise you *must* wait until `nextAvailable[t]`.  
  The only efficient way to do that is to “fast‑forward” the day counter directly to that day.

#### 2.4.2  Greedy Step‑by‑Step

1. **Initialize** `days = 0` (no days passed yet).  
2. For every task `t` in `tasks`:
   - `days = max(nextAvailable.get(t, 0), days + 1);`
   - After finishing, set `nextAvailable[t] = days + space + 1;`
3. Return `days`.

The magic is the `max()` call: it guarantees we never *decrease* the day counter, so the algorithm always respects the original task order.

#### 2.4.3  Why It’s O(n)

We visit each task exactly once.  
All operations on a `HashMap` / `dict` / `unordered_map` are amortised **O(1)**.  
Therefore total time = `O(n)` (n ≤ 10⁵).  
Space = `O(k)`, where *k* is the number of distinct task types (≤ n).

This is the **fastest** algorithm accepted on LeetCode and is usually the *expected* solution in interviews.

---

### 2.5  The “Bad” – Common Mistakes That Spoil the Answer

| Mistake | Consequence | Fix |
|---------|--------------|-----|
| Using an `int` for the day counter | Overflow for `space == 10⁵` and many repeats | Use `long` (Java) / `long long` (C++) |
| Forgetting the `+1` in `days + space + 1` | The same type may run too early (violates the rule) | Always add the `+1` for the *day* you’ll actually run the task |
| Off‑by‑one in the loop (`days + 1` vs. `days`) | Gives 1 day less or more | Clarify that `days` counts *completed* days, so the first day is `1` (or you can start from `0` and return `days`). |
| Not initializing missing keys | `getOrDefault(t, 0)` vs. `unordered_map` default zero | In C++ you can use `next[t]` – it default‑constructs to `0`. |
| Treating `space` as “cool‑down days” instead of “days that *must* pass” | Wrong formula | `nextAvailable[t] = currentDay + space + 1`. |

---

### 2.6  The “Ugly” – Variants and Extensions

LeetCode often provides *hard* follow‑up questions. For 2365 you might encounter:

- **Multiple workers**: Each worker can process *one* task per day. How does the algorithm change? (Answer: still O(n) using a min‑heap per type.)
- **Task priorities**: Some types are “more important” and should be scheduled sooner.  
  You can add a priority queue that pops the *cheapest* next‑available day.
- **Different units**: Instead of days, you could be told “hours” or “minutes”. The solution remains identical; just rename the variable.

> **Why talk about variants?** Interviewers love seeing you think beyond the exact statement.

---

### 2.7  Interview‑Ready Talking Points

When the interviewer asks about the solution:

1. **State the approach**: “I keep a hash map of the next day each task type is allowed again. For each task I jump to the later of the current day or that next‑available day.”
2. **Explain the greedy proof**: “Because we always execute a task as soon as it is allowed, we can’t improve the schedule by postponing it.”
3. **Mention complexity**: “O(n) time, O(k) space, which is optimal given we have to look at each task at least once.”
4. **Highlight edge‑case safety**: “I used `long` to avoid overflow. I handled empty arrays and `space = 0` correctly.”
5. **Optional extension**: “If we had multiple workers, we could use a priority queue per type instead of a simple map.”

> **Why this matters**: LeetCode 2365 is *not* just about arrays; it tests your ability to think in terms of *states* (cool‑down) and *greedy jumps* (fast‑forward). Showcasing this reasoning will impress interviewers looking for a clean, scalable solution.

---

### 2.8  Practical Advice for Job Hunting

| Action | Why it helps |
|--------|--------------|
| **Practice with Big‑O analysis** | Interviewers want you to justify why a solution is fast enough. |
| **Write the code yourself first, then copy‑paste** | Demonstrates you can code from scratch under time pressure. |
| **Explain your thought process aloud** | Show you can communicate complex ideas simply. |
| **Include edge‑case tests** | In a real system you’d never ignore extreme inputs. |
| **Link to your GitHub repo** | Recruiters appreciate a clean, documented codebase. |

> *Pro tip*: When you post your solution on your résumé or LinkedIn, tag the problem “LeetCode 2365” and note the “O(n)” hash‑map approach. Recruiters who parse your résumé will spot this keyword instantly.

---

### 2.9  Final Words

LeetCode 2365 is a *classic* interview puzzle that blends greedy strategy with hash‑map bookkeeping.  
The **fast** solution is surprisingly short—just a few lines of Java, Python, or C++—yet it packs deep algorithmic insight.

Use this problem as a showcase on your résumé or in a technical interview:  
**“I turned a naïve simulation that would take years into a linear‑time solution with a simple hash map.”**

Happy coding, and may your interview days be *cool‑down* free! 🚀

---

> **Ready to dive deeper?** Check out the official LeetCode discussion, or try the harder follow‑up “Task Scheduler III” (LeetCode 2336) to keep sharpening that greedy‑hash‑map muscle.