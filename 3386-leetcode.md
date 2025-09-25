---
title: LeetCode 3386. Button with Longest Push Time - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 3386. Button with Longest Push Time – Multi‑Language LeetCode Solution

### ✅ Problem Recap  
You’re given a chronologically sorted array `events` where each element `[index, time]` tells you that the button with ID `index` was pressed at moment `time`.  
The *push time* for a button press is:

- **First press:** `time` (no previous press to subtract).  
- **Subsequent presses:** `time_current – time_previous`.

Your task: return the button ID that required the **longest** push time.  
If multiple buttons tie for the longest time, return the one with the **smallest ID**.

---

## 🧩 Algorithm Overview

1. **Initialize** the maximum push time with the time of the first event (`events[0][1]`) and store the first button ID as the current best answer.
2. **Iterate** through the array starting from the second event.
3. For each event, compute `duration = events[i][1] - events[i-1][1]`.
4. **Update** if:
   - `duration` is **strictly greater** than the current maximum, **or**
   - `duration` equals the current maximum **and** the current button ID is **smaller** than the stored answer.
5. After the loop, the stored answer is the desired button ID.

The algorithm runs in **O(n)** time and uses **O(1)** extra space.

---

## 📄 Code Implementations

### 1️⃣ Java

```java
public class Solution {
    public int buttonWithLongestTime(int[][] events) {
        // Edge case: at least one event is guaranteed by constraints
        int maxTime = events[0][1];
        int answer = events[0][0];

        for (int i = 1; i < events.length; i++) {
            int duration = events[i][1] - events[i - 1][1];
            if (duration > maxTime || (duration == maxTime && events[i][0] < answer)) {
                maxTime = duration;
                answer = events[i][0];
            }
        }
        return answer;
    }
}
```

### 2️⃣ Python

```python
class Solution:
    def buttonWithLongestTime(self, events: List[List[int]]) -> int:
        max_time = events[0][1]
        answer = events[0][0]

        for i in range(1, len(events)):
            duration = events[i][1] - events[i - 1][1]
            if duration > max_time or (duration == max_time and events[i][0] < answer):
                max_time = duration
                answer = events[i][0]

        return answer
```

### 3️⃣ C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int buttonWithLongestTime(vector<vector<int>>& events) {
        int max_time = events[0][1];
        int answer = events[0][0];

        for (size_t i = 1; i < events.size(); ++i) {
            int duration = events[i][1] - events[i - 1][1];
            if (duration > max_time || (duration == max_time && events[i][0] < answer)) {
                max_time = duration;
                answer = events[i][0];
            }
        }
        return answer;
    }
};
```

---

## 📈 Time & Space Complexity

| Language | Time Complexity | Space Complexity |
|----------|-----------------|------------------|
| Java     | **O(n)**        | **O(1)**         |
| Python   | **O(n)**        | **O(1)**         |
| C++      | **O(n)**        | **O(1)**         |

*`n` = number of events.*

---

## 🔍 Edge Cases & Pitfalls

| Scenario | What to Watch For |
|----------|-------------------|
| **Only one event** | The first event’s time itself is the longest; no difference calculation needed. |
| **Tie on duration** | Must return the *smallest* button ID. Check both `duration > max` **and** `duration == max && id < answer`. |
| **Large input** | All solutions run in linear time, comfortably fitting the 1 000‑event limit. |
| **Negative or zero times** | Constraints guarantee `timei ≥ 1`, so no negative durations. |

---

## 💡 “The Good, the Bad, and the Ugly”

### The Good  
- **Simplicity:** One pass, minimal variables, clear logic.  
- **Efficiency:** `O(n)` time, `O(1)` space – ideal for interview settings.  
- **Language‑agnostic:** The same concept maps cleanly to Java, Python, C++.

### The Bad  
- **Misunderstanding “first press time”:** Some solutions incorrectly treat the first event’s duration as zero.  
- **Off‑by‑one bugs:** Forgetting to start the loop at index 1 or using the wrong previous index leads to wrong durations.  

### The Ugly  
- **Over‑engineering:** Using hash maps or complex data structures for this linear scan is unnecessary and distracts interviewers.  
- **Ignoring tie‑break rule:** Returning the wrong button ID when multiple buttons share the longest duration gives a wrong answer that’s hard to spot without a thorough test suite.

---

## 📘 How to Explain This in an Interview

1. **Restate the problem** in your own words to confirm understanding.  
2. **Outline the algorithm** (first‑pass, maintain max and answer).  
3. **Write pseudocode** or a quick sketch on the whiteboard.  
4. **Mention edge cases** explicitly.  
5. **Discuss complexity** right after coding.  

If the interviewer asks “Can we do better?” emphasize that `O(n)` is optimal because every event must be inspected at least once.

---

## 🚀 Real‑World Relevance

This pattern—tracking maximum/minimum values while scanning a stream—is common in:

- **Sensor data processing** (detecting longest inactivity periods).  
- **Event logging** (identifying the most delayed events).  
- **Game analytics** (finding longest button press or interaction).  

Mastering this approach demonstrates strong array manipulation skills and a knack for efficient state tracking—both prized in software engineering roles.

---

## 📌 Quick Checklist for Your Interview Prep

- ✅ Understand the input format (sorted by time).  
- ✅ Handle the first event separately.  
- ✅ Update on strictly greater duration or tie with smaller ID.  
- ✅ Provide test cases for edge conditions.  
- ✅ Explain time & space complexity.  

---

## 📣 SEO‑Friendly Summary

Looking for a *Button with Longest Push Time* solution? Check out this **Java, Python, and C++** guide covering the algorithm, edge cases, and a complete interview‑ready explanation. Ideal for anyone preparing for coding interviews, LeetCode practice, or building efficient event‑processing systems.

**Keywords**: Button with Longest Push Time, LeetCode 3386, Java solution, Python solution, C++ solution, coding interview, algorithm, time complexity, space complexity, interview preparation, job interview tips.

---