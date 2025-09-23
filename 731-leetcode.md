---
title: LeetCode 731. My Calendar II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 731 – My Calendar II  
**Solutions in Java, Python & C++ + an SEO‑friendly interview‑blog**

---

## 📚 Problem Overview (LeetCode 731)

> *You need to implement a calendar that can book events as long as no **three** events overlap at the same time.  
> Events are half‑open intervals \([start, end)\). Return `true` if the event can be added, otherwise `false`.*

> **Constraints**  
> * `0 ≤ start < end ≤ 10^9`  
> * ≤ 1000 `book()` calls

---

## 🔧 Key Idea

Maintain **two collections**:

1. **All booked events** – to detect any *new* overlap.
2. **Double‑booked intervals** – intervals that are already covered by two events.  
   If a new event overlaps any of these, a **triple booking** would occur → reject.

The classic “two‑list” solution runs in **O(N)** per `book()` (N = number of existing events).  
A more efficient approach uses a *sweep‑line* (`TreeMap` / `std::map`) and runs in **O(log N)** per call.

Below are three idiomatic implementations (Java, Python, C++) that use the *two‑list* strategy for clarity.

---

## ⚙️ Java Implementation

```java
import java.util.ArrayList;
import java.util.List;

class Event {
    int start, end;
    Event(int s, int e) { start = s; end = e; }
}

class MyCalendarTwo {

    private final List<Event> bookings = new ArrayList<>();
    private final List<Event> overlaps = new ArrayList<>();

    public MyCalendarTwo() { }

    public boolean book(int start, int end) {
        // 1) Check triple‑booking
        for (Event e : overlaps) {
            if (isOverlap(e, start, end)) return false;
        }

        // 2) Record new double‑bookings
        for (Event e : bookings) {
            if (isOverlap(e, start, end)) {
                overlaps.add(new Event(
                        Math.max(e.start, start),
                        Math.min(e.end, end)
                ));
            }
        }

        // 3) Persist the new event
        bookings.add(new Event(start, end));
        return true;
    }

    private boolean isOverlap(Event e, int s, int t) {
        return Math.max(e.start, s) < Math.min(e.end, t);
    }
}
```

> **Complexities**  
> • **Time:** `O(n)` per `book()` (worst‑case linear scan).  
> • **Space:** `O(n)` for the two lists.

---

## 🐍 Python Implementation

```python
class Event:
    __slots__ = ("s", "e")
    def __init__(self, s, e):
        self.s, self.e = s, e

class MyCalendarTwo:
    def __init__(self):
        self.bookings = []   # all events
        self.overlaps = []   # double‑booked intervals

    def book(self, start: int, end: int) -> bool:
        # 1) Triple‑booking guard
        for e in self.overlaps:
            if max(e.s, start) < min(e.e, end):
                return False

        # 2) Build new overlaps
        for e in self.bookings:
            if max(e.s, start) < min(e.e, end):
                self.overlaps.append(Event(
                    max(e.s, start),
                    min(e.e, end)
                ))

        # 3) Persist the new event
        self.bookings.append(Event(start, end))
        return True
```

> **Complexities**  
> • **Time:** `O(n)` per call.  
> • **Space:** `O(n)`.

---

## 🏗️ C++ Implementation

```cpp
#include <vector>
using namespace std;

struct Event {
    int s, e;
    Event(int ss, int ee) : s(ss), e(ee) {}
};

class MyCalendarTwo {
    vector<Event> bookings;
    vector<Event> overlaps;

    bool isOverlap(const Event &e, int s, int t) const {
        return max(e.s, s) < min(e.e, t);
    }
public:
    MyCalendarTwo() = default;

    bool book(int start, int end) {
        // 1) Triple‑booking guard
        for (const auto& e : overlaps)
            if (isOverlap(e, start, end)) return false;

        // 2) Record new overlaps
        for (const auto& e : bookings)
            if (isOverlap(e, start, end))
                overlaps.emplace_back(
                    max(e.s, start),
                    min(e.e, end)
                );

        // 3) Persist the new event
        bookings.emplace_back(start, end);
        return true;
    }
};
```

> **Complexities**  
> • **Time:** `O(n)` per `book()`.  
> • **Space:** `O(n)`.

---

## 📝 Blog Article – “The Good, The Bad, and The Ugly of My Calendar II”

> **Title:** *LeetCode 731 – My Calendar II: A Deep Dive into Interval Scheduling, Triple‑Booking, and Your Next Coding Interview*

> **Meta Description:** Master LeetCode 731 with Java, Python, and C++ solutions. Learn the trade‑offs of two‑list vs. sweep‑line, optimize your algorithm, and ace your software‑engineering interview.

---

### 1️⃣ Why This Problem Rocks

- **Real‑world relevance:** Scheduling, booking systems, and resource allocation.
- **Algorithmic depth:** Interval overlap detection, event counting, data structure choice.
- **Interview popularity:** Frequently asked on tech‑company interviews (Google, Amazon, Microsoft).

---

### 2️⃣ The Good – What Works

| Aspect | Explanation |
|--------|-------------|
| **Simplicity** | The two‑list solution is intuitive: one list for all bookings, another for overlaps. No external libraries required. |
| **Deterministic Complexity** | With ≤ 1000 bookings, an `O(n)` scan per call is fast enough. |
| **Clear Edge Cases** | Half‑open intervals avoid pitfalls like `[10,20]` & `[20,30]` being considered overlapping. |

---

### 3️⃣ The Bad – Where It Can Slip

| Issue | Detail |
|-------|--------|
| **Linear Scan** | As bookings grow, each `book()` becomes slower (`O(n)`). For huge datasets (millions of events), this is unacceptable. |
| **Duplicate Overlaps** | The naive approach may store the same double‑booked segment multiple times (e.g., if three distinct pairs share the same overlap). It’s still correct, but wasteful. |
| **Hard to Reason About** | Debugging nested loops can be confusing for interview candidates under time pressure. |

---

### 4️⃣ The Ugly – Gotchas & Misconceptions

1. **Triple‑Booking Mis‑identification**  
   *Many candidates incorrectly only check `bookings` for overlap, assuming two events will never overlap twice. This misses the *double* overlap list and accepts a triple booking.*

2. **Using Sets of Intervals**  
   *A common but buggy pattern: store intervals in a `HashSet` and test for overlap by iterating. It ignores the “already double‑booked” state.*

3. **Sweep‑Line Implementation Errors**  
   *The `TreeMap`/`std::map` solution requires careful increment/decrement logic. Off‑by‑one errors on the half‑open boundary are frequent. A single missing `+1` can make your algorithm wrong.*

---

### 4️⃣ Sweep‑Line (TreeMap / std::map) – The Elegant Alternative

```java
// Java sketch (O(log n) per call)
class MyCalendarTwo {
    private final TreeMap<Integer, Integer> map = new TreeMap<>();
    public boolean book(int start, int end) {
        map.put(start, map.getOrDefault(start, 0) + 1);
        map.put(end, map.getOrDefault(end, 0) - 1);

        int concurrent = 0;
        for (int v : map.values()) {
            concurrent += v;
            if (concurrent > 2) {           // triple booking detected
                // rollback
                map.put(start, map.get(start) - 1);
                if (map.get(start) == 0) map.remove(start);
                map.put(end, map.get(end) + 1);
                if (map.get(end) == 0) map.remove(end);
                return false;
            }
        }
        return true;
    }
}
```

*Benefits:* `O(log n)` per call, no duplicate overlaps, perfect for interviewers who care about asymptotic optimality.

---

### 5️⃣ Time & Space Trade‑offs

| Approach | Time per `book()` | Space | When to Use |
|----------|------------------|-------|-------------|
| Two‑List | `O(n)` | `O(n)` | ≤ 1000 calls, clean code |
| Sweep‑Line | `O(log n)` | `O(n)` | Large data sets, interviewers favor asymptotic bounds |

---

### 6️⃣ Test‑Case Cheat Sheet

| Test | Expected Result |
|------|-----------------|
| `book(10,20)` → `true` | First event |
| `book(50,60)` → `true` | No overlap |
| `book(10,40)` → `true` | Overlaps one existing → double booking |
| `book(5,15)` → `false` | Would create a triple booking with `[10,20]` & `[10,40]` |
| `book(25,55)` → `true` | Overlaps only `[50,60]` → double booking |

> *Tip:* Always include edge cases where intervals touch but don’t overlap (`[20,30]` after `[10,20]`).

---

### 7️⃣ Interview‑Ready Checklist

1. **Explain your algorithm** – two‑list or sweep‑line, whichever you coded.  
2. **State time & space** – interviewers love seeing the complexity upfront.  
3. **Mention edge cases** – half‑open intervals, back‑to‑back events.  
4. **Show a dry run** – walk through a small example on the whiteboard.  
5. **Be prepared for variations** – what if the limit was 10^5 bookings? Use sweep‑line.

---

### 8️⃣ Final Thought – *Job‑Interview Takeaway*

> *“A clean, well‑commented solution that covers edge cases, explains complexity, and can be easily ported between Java, Python, and C++ demonstrates a candidate’s algorithmic thinking and coding discipline—exactly what recruiters look for.”*

> If you can walk through this problem, explain why you chose a particular data structure, and discuss trade‑offs, you’ll impress interviewers at any major tech company.

---

## 📹 Bonus: Video Walkthrough

*(In the actual blog you’d embed a YouTube link or short GIF)*

> “Watch me solve LeetCode 731 in 5 minutes. I’ll show the two‑list approach, a sweep‑line variant, and highlight common pitfalls. Check out the full video here: <https://youtu.be/YourVideoID>.”

---

### 📈 SEO Checklist

- **Primary keywords:** *LeetCode 731*, *My Calendar II*, *MyCalendarTwo*, *interval scheduling*, *triple booking*  
- **Secondary keywords:** *Java solution*, *Python solution*, *C++ solution*, *interview algorithm*, *software engineer interview*, *coding interview questions*, *job interview preparation*  
- **Headings (H1, H2, H3)** with keyword sprinkling.  
- **Alt‑text for code snippets**: *Java MyCalendarTwo implementation*  
- **Internal links:** “Read our sweep‑line solution” → link to the next article.  
- **Meta tags**: Title, description, keywords list.

---

### 🚀 Wrap‑Up

- **Three clean solutions** in the most popular interview languages.  
- **A comprehensive, SEO‑optimized article** that covers the problem’s relevance, design choices, and interview tips.  
- **All ready to paste** into a résumé, LinkedIn post, or interview prep kit.

Good luck landing that job—your mastery of *My Calendar II* will set you apart!