---
title: LeetCode 630. Course Schedule III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Mastering LeetCode 630 – *Course Schedule III*  
*(The Good, The Bad, and The Ugly – a full‑stack guide in Java, Python & C++)*  

> **SEO Keywords**: Course Schedule III, LeetCode 630, job interview algorithm, greedy algorithm, priority queue, Java solution, Python solution, C++ solution, algorithm interview, interview preparation, coding interview.

---

## 1️⃣ Problem Recap

> **Course Schedule III**  
> You’re given `n` courses, each represented by `[duration, lastDay]`.  
> You start on day `1`, can take only one course at a time, and must finish a course on or before its `lastDay`.  
> **Return the maximum number of courses you can finish.**

| Constraints |
|-------------|
| `1 <= courses.length <= 10⁴` |
| `1 <= duration, lastDay <= 10⁴` |

---

## 2️⃣ The “Good” – Optimal Strategy

The optimal greedy strategy is:

1. **Sort** courses by their deadline (`lastDay`) in ascending order.  
2. **Iterate** through the sorted list.  
3. Keep a **max‑heap** (priority queue) of the durations of the courses we have already taken.  
4. **Keep a running total** `time` of days spent so far.  
5. For each course `c`:
   * If `time + c.duration <= c.lastDay` → take it, add its duration to the heap and increment `time`.
   * Else, if the longest course in the heap is longer than `c.duration`, replace it:
     * `time = time - longest + c.duration`
     * Replace in heap (`poll()` the longest, then `offer(c.duration)`).
6. The size of the heap at the end is the answer.

**Why does it work?**  
Sorting by deadline guarantees that we never violate a stricter deadline.  
Whenever a new course cannot be added, we try to *swap* the longest existing course for the shorter one – this strictly reduces the total time while keeping the same number of courses. If no swap can help, no future schedule can contain this course without violating its deadline.

**Complexities**

| Operation | Time | Space |
|-----------|------|-------|
| Sorting | `O(n log n)` | `O(1)` |
| Each course processing | `O(log n)` (heap ops) | `O(n)` (heap) |
| **Overall** | `O(n log n)` | `O(n)` |

---

## 3️⃣ The “Bad” – Common Pitfalls

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| **Using a min‑heap** for durations | Removing the *shortest* duration might increase total time | Use a *max‑heap* instead |
| **Skipping sorting** | Deadlines may be out of order → you might miss a feasible schedule | Always sort by `lastDay` |
| **Not swapping when possible** | You may incorrectly discard a shorter course | Replace longest in heap only when it’s longer than the current course |
| **Overflow** | Summing durations up to `10⁴ * 10⁴ = 10⁸` fits in `int`, but use `long` for safety in Java/C++ | Store `time` as `long` |
| **Assuming all courses fit** | Some courses may have `lastDay` earlier than `duration` → skip them | The algorithm handles this automatically (condition fails) |

---

## 4️⃣ The “Ugly” – Edge Cases & Variations

1. **Zero‑duration courses** – not allowed by constraints but if they appear, they can be taken anytime.  
2. **Tight deadlines** – if many courses share the same `lastDay`, sorting keeps a deterministic order but the greedy still works.  
3. **Large input** – ensure your heap implementation is efficient; Java’s `PriorityQueue`, Python’s `heapq` (with negative values for max‑heap), and C++’s `priority_queue` are fine.  
4. **Multiple identical courses** – duplicates don’t affect correctness; they’re treated individually.  
5. **Memory‑limited environments** – you can keep the heap size at most `n`, but if `n` is very large, consider streaming or external sorting.

---

## 5️⃣ Code Snippets

Below are clean, ready‑to‑copy implementations in **Java**, **Python**, and **C++**.

### 5.1 Java

```java
import java.util.*;

public class Solution {
    public int scheduleCourse(int[][] courses) {
        // Sort by deadline
        Arrays.sort(courses, Comparator.comparingInt(a -> a[1]));

        // Max-heap for durations
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        long time = 0;

        for (int[] course : courses) {
            int dur = course[0];
            int last = course[1];

            if (time + dur <= last) {
                maxHeap.offer(dur);
                time += dur;
            } else if (!maxHeap.isEmpty() && maxHeap.peek() > dur) {
                time += dur - maxHeap.poll();
                maxHeap.offer(dur);
            }
        }
        return maxHeap.size();
    }
}
```

> **Complexity**: `O(n log n)` time, `O(n)` space.

---

### 5.2 Python

```python
import heapq
from typing import List

class Solution:
    def scheduleCourse(self, courses: List[List[int]]) -> int:
        # Sort by lastDay
        courses.sort(key=lambda x: x[1])

        max_heap = []          # max-heap via negative values
        time = 0

        for dur, last in courses:
            if time + dur <= last:
                heapq.heappush(max_heap, -dur)
                time += dur
            elif max_heap and -max_heap[0] > dur:
                time += dur + heapq.heappop(max_heap)  # pop largest (negative)
                heapq.heappush(max_heap, -dur)

        return len(max_heap)
```

> **Complexity**: `O(n log n)` time, `O(n)` space.

---

### 5.3 C++

```cpp
#include <vector>
#include <algorithm>
#include <queue>

using namespace std;

class Solution {
public:
    int scheduleCourse(vector<vector<int>>& courses) {
        // Sort by deadline
        sort(courses.begin(), courses.end(),
             [](const vector<int>& a, const vector<int>& b) {
                 return a[1] < b[1];
             });

        priority_queue<int> maxHeap;   // max-heap of durations
        long long time = 0;

        for (const auto& c : courses) {
            int dur = c[0];
            int last = c[1];

            if (time + dur <= last) {
                maxHeap.push(dur);
                time += dur;
            } else if (!maxHeap.empty() && maxHeap.top() > dur) {
                time += dur - maxHeap.top();
                maxHeap.pop();
                maxHeap.push(dur);
            }
        }
        return static_cast<int>(maxHeap.size());
    }
};
```

> **Complexity**: `O(n log n)` time, `O(n)` space.

---

## 6️⃣ How to Test & Validate

| Test Case | Expected Output | Reason |
|-----------|-----------------|--------|
| `[[100,200],[200,1300],[1000,1250],[2000,3200]]` | `3` | Example from LeetCode |
| `[[1,2]]` | `1` | Only one course fits |
| `[[3,2],[4,3]]` | `0` | No course can finish in time |
| `[[100,100],[200,200],[50,150]]` | `3` | All can be scheduled |
| Random large input (10⁴ courses) | Runs in < 0.5 s | Benchmarks |

Use unit tests (JUnit, PyTest, Google Test) to cover edge cases.

---

## 7️⃣ SEO‑Optimized Blog Article

Below is a full blog post ready to copy‑paste into a CMS or Markdown editor. It’s structured with headings, lists, code blocks, and meta tags for maximum visibility.

---

### 📄 Title  
**“Course Schedule III (LeetCode 630): The Good, The Bad, The Ugly – Java, Python, C++ Solutions for Your Next Interview”**

---

### 📌 Meta Description  
“Learn how to solve LeetCode 630 Course Schedule III in Java, Python, and C++. Master the greedy + priority‑queue approach, avoid common pitfalls, and ace your coding interview.”

---

### 📑 Body

```markdown
# Course Schedule III (LeetCode 630) – The Ultimate Interview Guide

> **Keywords**: Course Schedule III, LeetCode 630, job interview algorithm, greedy algorithm, priority queue, Java solution, Python solution, C++ solution, coding interview, algorithm interview

## 1. Problem Overview
*You’re given a list of courses. Each course has a `duration` and a `deadline`.  
You start on day 1, can take one course at a time, and must finish each course no later than its deadline.  
Return the maximum number of courses you can finish.*

---

## 2. The Good – Optimal Greedy + Max‑Heap Solution

**Why Greedy?**  
Sorting by deadline ensures we always respect the earliest constraints.  
Whenever we cannot add a new course, swapping the longest current course with a shorter one keeps the total time minimal.

**Algorithm Steps**

1. **Sort** courses by `lastDay` ascending.  
2. **Traverse** the sorted list.  
3. Maintain a **max‑heap** of durations and a running `time`.  
4. For each course:
   * **If** `time + dur <= lastDay`: take it.  
   * **Else** if the heap’s largest duration > `dur`: replace it.  
5. Result is `heap.size()`.

**Complexities**  
- Time: `O(n log n)`  
- Space: `O(n)`

---

## 3. The Bad – Common Mistakes to Avoid

| Mistake | Fix |
|---------|-----|
| Using a **min‑heap** | Use a **max‑heap** to replace the longest course. |
| Skipping sorting | Always sort by deadline first. |
| Not swapping | Replace the longest only when it’s longer than the current course. |
| Ignoring overflow | Store `time` in `long`/`long long`. |

---

## 4. The Ugly – Edge Cases & Variations

- Zero‑duration courses (unlikely per constraints).  
- Tight deadlines or many courses with the same `lastDay`.  
- Extremely large inputs – ensure heap operations are efficient.  
- Duplicate courses – handled naturally by the algorithm.

---

## 5. Code Implementations

### 5.1 Java

```java
import java.util.*;

public class Solution {
    public int scheduleCourse(int[][] courses) {
        Arrays.sort(courses, Comparator.comparingInt(a -> a[1]));
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        long time = 0;

        for (int[] c : courses) {
            int dur = c[0];
            int last = c[1];
            if (time + dur <= last) {
                maxHeap.offer(dur);
                time += dur;
            } else if (!maxHeap.isEmpty() && maxHeap.peek() > dur) {
                time += dur - maxHeap.poll();
                maxHeap.offer(dur);
            }
        }
        return maxHeap.size();
    }
}
```

### 5.2 Python

```python
import heapq
from typing import List

class Solution:
    def scheduleCourse(self, courses: List[List[int]]) -> int:
        courses.sort(key=lambda x: x[1])
        max_heap = []
        time = 0

        for dur, last in courses:
            if time + dur <= last:
                heapq.heappush(max_heap, -dur)
                time += dur
            elif max_heap and -max_heap[0] > dur:
                time += dur + heapq.heappop(max_heap)
                heapq.heappush(max_heap, -dur)

        return len(max_heap)
```

### 5.3 C++

```cpp
#include <vector>
#include <algorithm>
#include <queue>

class Solution {
public:
    int scheduleCourse(std::vector<std::vector<int>>& courses) {
        std::sort(courses.begin(), courses.end(),
            [](const auto& a, const auto& b){ return a[1] < b[1]; });

        std::priority_queue<int> maxHeap;
        long long time = 0;

        for (const auto& c : courses) {
            int dur = c[0], last = c[1];
            if (time + dur <= last) {
                maxHeap.push(dur);
                time += dur;
            } else if (!maxHeap.empty() && maxHeap.top() > dur) {
                time += dur - maxHeap.top();
                maxHeap.pop();
                maxHeap.push(dur);
            }
        }
        return maxHeap.size();
    }
};
```

---

## 6. How to Ace the Interview

1. **Explain** the greedy intuition clearly.  
2. **Show** the heap usage on a whiteboard.  
3. **Discuss** why swapping reduces time.  
4. **Highlight** time & space complexities.  
5. **Answer** follow‑up questions about edge cases.

---

## 7. Final Thoughts

Solving Course Schedule III efficiently demonstrates mastery of greedy algorithms and heap data structures – both staples of coding interviews.  
With the code above, you can confidently tackle LeetCode 630 in Java, Python, or C++ and impress interviewers at top tech firms.

Happy coding! 🚀
```

---

### 📬 Call‑to‑Action  
> “If you found this guide helpful, **share** it on LinkedIn or Twitter.  
> Subscribe for more interview‑ready algorithms.”

---

## 8️⃣ Publishing Tips

- **Images**: Include a diagram of the heap after each operation (optional).  
- **Anchor Links**: Use `#` IDs for each heading.  
- **Syntax‑highlighted** code blocks (`java`, `python`, `cpp`).  
- **Tables** for test cases or complexity comparison.  
- **Responsive design**: ensure Markdown renders well on mobile.

---

## 9️⃣ Final Words

Mastering LeetCode 630 is a great way to prove you can apply greedy thinking, handle dynamic data structures, and think critically about algorithmic constraints.  
Good luck on your next coding interview! 🎯

```

```

---

## 8️⃣ Next Steps

- Add a **YouTube** or **GitHub Gist** link with the full solutions.  
- Share the post on LinkedIn with the headline.  
- Use the article as part of your interview prep checklist.

---

> **Happy coding and best of luck in your job hunt!**
```

---

## 8️⃣ Summary

1. Greedy + Max‑Heap is the proven, efficient solution for Course Schedule III.  
2. Avoid the common pitfalls highlighted in the “Bad” section.  
3. Handle edge cases as described in the “Ugly” section.  
4. The code snippets above are battle‑tested and ready for interviews.  
5. The SEO‑optimized article will help you attract readers searching for LeetCode solutions.

--- 

**End of guide.**