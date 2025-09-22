---
title: LeetCode 295. Find Median from Data Stream - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 295. Find Median from Data Stream  
### Full‑stack Solution (Java | Python | C++) + SEO‑Optimised Blog Post  

---

### TL;DR
- **Problem** – Build a class `MedianFinder` that supports:
  - `void addNum(int num)` – insert a number from a data stream  
  - `double findMedian()` – return the current median  
- **Complexity** – `addNum` : **O(log n)**, `findMedian` : **O(1)**  
- **Approach** – Two heaps (a max‑heap for the lower half, a min‑heap for the upper half)  
- **Why it matters** – This is a classic interview problem that shows you know *priority queues*, *balanced trees*, and *amortised analysis*.  

---

## 1. Code – One Implementation per Language

> The same logic is applied in all three languages:  
> *Maintain two heaps: `low` (max‑heap) and `high` (min‑heap).*  
> *Keep the size difference ≤ 1 and `low.peek() ≤ high.peek()`.  
>  Median is either the single top element (odd size) or the average of both tops (even size).*

### 1.1 Java (LeetCode‑style)

```java
import java.util.PriorityQueue;
import java.util.Comparator;

public class MedianFinder {
    // Max‑heap for the lower half
    private final PriorityQueue<Integer> low  = new PriorityQueue<>(Comparator.reverseOrder());
    // Min‑heap for the upper half
    private final PriorityQueue<Integer> high = new PriorityQueue<>();

    public MedianFinder() {}

    public void addNum(int num) {
        // 1️⃣ Insert into the appropriate heap
        if (low.isEmpty() || num <= low.peek()) {
            low.offer(num);
        } else {
            high.offer(num);
        }

        // 2️⃣ Re‑balance so that |size(low) – size(high)| ≤ 1
        if (low.size() > high.size() + 1) {
            high.offer(low.poll());
        } else if (high.size() > low.size() + 1) {
            low.offer(high.poll());
        }
    }

    public double findMedian() {
        // 3️⃣ Compute median in O(1)
        int total = low.size() + high.size();
        if (total % 2 == 1) {
            // Odd number of elements – larger heap holds the median
            return low.size() > high.size() ? low.peek() : high.peek();
        } else {
            // Even – average of both tops
            return (low.peek() + high.peek()) / 2.0;
        }
    }
}
```

### 1.2 Python

```python
import heapq

class MedianFinder:
    def __init__(self):
        # max‑heap (invert sign) and min‑heap
        self.low  = []          # max‑heap for lower half
        self.high = []          # min‑heap for upper half

    def addNum(self, num: int) -> None:
        # 1️⃣ Push into the correct heap
        if not self.low or num <= -self.low[0]:
            heapq.heappush(self.low, -num)          # invert sign → max‑heap
        else:
            heapq.heappush(self.high, num)

        # 2️⃣ Re‑balance
        if len(self.low) > len(self.high) + 1:
            heapq.heappush(self.high, -heapq.heappop(self.low))
        elif len(self.high) > len(self.low) + 1:
            heapq.heappush(self.low, -heapq.heappop(self.high))

    def findMedian(self) -> float:
        # 3️⃣ O(1) median
        if len(self.low) == len(self.high):
            return (-self.low[0] + self.high[0]) / 2.0
        return float(-self.low[0]) if len(self.low) > len(self.high) else float(self.high[0])
```

### 1.3 C++

```cpp
#include <queue>
#include <vector>

class MedianFinder {
private:
    // max‑heap (lower half)
    std::priority_queue<int> low;
    // min‑heap (upper half)
    std::priority_queue<int, std::vector<int>, std::greater<int>> high;

public:
    MedianFinder() = default;

    void addNum(int num) {
        // 1️⃣ Insert into the proper heap
        if (low.empty() || num <= low.top()) low.push(num);
        else high.push(num);

        // 2️⃣ Re‑balance to keep |low.size() - high.size()| <= 1
        if (low.size() > high.size() + 1) {
            high.push(low.top()); low.pop();
        } else if (high.size() > low.size() + 1) {
            low.push(high.top()); high.pop();
        }
    }

    double findMedian() {
        // 3️⃣ O(1) median computation
        if (low.size() == high.size())
            return (low.top() + high.top()) / 2.0;
        return low.size() > high.size() ? low.top() : high.top();
    }
};
```

All three snippets are ready‑to‑paste for a **LeetCode 295** submission or for a technical interview.  
Feel free to add unit tests or integrate them into your own coding‑platform scaffolding.

---

## 2. Why This Blog Is Job‑Interview‑Ready

The goal isn’t just to solve LeetCode 295; it’s to demonstrate *design* and *optimization* skills that hiring managers crave.  
Below we break the solution into three sections – **The Good**, **The Bad**, **The Ugly** – so you can articulate the trade‑offs when you explain the code in an interview.

---

## 2.1 The Good – Why This Solution Rocks

| ✅ Feature | Why It’s Great |
|------------|----------------|
| **O(log n) insertion** | Uses a binary heap → *logarithmic* cost, even for 5 × 10⁴ operations. |
| **O(1) median query** | Simply peek at the tops of the two heaps – no traversal, no copying. |
| **Space‑optimal** | Stores each element once: **O(n)** total. |
| **Simplicity & Clarity** | Two heaps + rebalancing logic is easy to code, test, and explain. |
| **Scalable** | Works for *any* integer range – no assumptions about data distribution. |
| **Interview‑ready** | Shows mastery of **priority queues** and **amortised analysis** – a must‑know for senior data‑structure roles. |

---

## 2.2 The Bad – Common Pitfalls & Edge Cases

| ⚠️ Pitfall | Fix / Insight |
|------------|---------------|
| **Choosing the wrong heap type** | In Java: use `Comparator.reverseOrder()` for the max‑heap; in Python: invert sign; in C++: `std::greater<int>` for the min‑heap. |
| **Off‑by‑one on size difference** | Always check `|low.size() – high.size()| ≤ 1`. Forgetting this breaks the median logic for even/odd counts. |
| **Failing to balance after insertion** | Even if you push into the wrong heap, you must re‑balance. A single `heap.pop()` / `push()` moves the offending element. |
| **Integer overflow on median calculation** | In Java, cast to `long` before adding: `((long)low.peek() + high.peek()) / 2.0`. |
| **Using a single heap (cloning, polling)** | Cloning a heap for every `findMedian()` leads to **O(n)** time – *never* do this in an interview. |

---

## 2.3 The Ugly – When Things Go Wrong

| 👹 Ugly Scenario | Why It Happens |
|------------------|----------------|
| **Unbalanced Heaps on Insert** | If you naively push every number into one heap, the other stays empty → median wrong. |
| **Neglecting Order Constraint** | `low.peek()` must be ≤ `high.peek()`; otherwise the median will be from the wrong half. |
| **Wrong Median Calculation on Even Size** | Returning `low.peek()` or `high.peek()` alone for an even number of elements is incorrect. |
| **Using Recursion or Merge Sort** | Some solutions (e.g., merge‑sort after each insert) are *O(n log n)* or *O(n)* for median query – unacceptable for large streams. |
| **Assuming Sorted Input** | If the data stream is already sorted, a single array can give median in O(1), but you *must* handle the general case. |

---

## 3. Bonus: Tiny Optimization for Small Integer Ranges

If you know the stream’s values are guaranteed to lie in `[0, 100]`, you can ditch the priority queues completely:

| Language | Concept | Complexity |
|----------|---------|------------|
| Java | `int[] freq = new int[101];` | `addNum` : **O(1)**, `findMedian` : **O(100)** (constant) |
| Python | `collections.Counter` + prefix sum | Same as Java |
| C++ | `std::array<int,101>` | Same as Java |

This variant is *nice for micro‑optimisation*, but most interviewers will still expect the two‑heap solution. Mention it only if the interview explicitly asks for a range‑restricted solution.

---

## 4. SEO‑Optimised Blog Post: “The Good, the Bad, and the Ugly of LeetCode 295 – MedianFinder”

> **Target keywords:** *LeetCode 295*, *MedianFinder*, *find median from data stream*, *two heaps*, *Java interview*, *Python interview*, *C++ interview*, *O(log n) priority queue*, *data structures interview*, *technical interview questions*.

---

### 📌 Title  
**Master LeetCode 295: MedianFinder – A Deep Dive into the Two‑Heap Solution (Java / Python / C++)**  

---

### 📝 Meta Description  
Learn how to implement `MedianFinder` for LeetCode 295 – Find Median from Data Stream – in Java, Python, and C++.  
Explore the good, the bad, and the ugly of common approaches, and get interview‑ready with O(log n) insert and O(1) query.  

---

### 🔥 Body  

#### 1. Introduction  
The **“Find Median from Data Stream”** problem (LeetCode 295) is a staple for data‑structure interviews.  
It forces you to design a *dynamic* data structure that keeps track of the middle element of an ever‑growing set.  
In this article, we’ll walk through the optimal **two‑heap** algorithm, illustrate how to code it in **Java, Python, and C++**, and highlight trade‑offs you can discuss during interviews.

#### 2. Why MedianFinder Matters  
- **Real‑world relevance:** Streaming analytics, online recommendation engines, and stock‑price dashboards all need to compute median on the fly.  
- **Interview signal:** Demonstrates control over **priority queues**, **heap balancing**, and **amortised analysis**.  

#### 3. The Good – A Proven O(log n) Strategy  
- **Binary heaps** provide *logarithmic* insertion, essential for streams of 10⁵+ numbers.  
- **Rebalancing** keeps heap sizes close, ensuring median logic holds for both odd and even counts.  
- **O(1) median query** – just peek at heap tops.  

#### 4. The Bad – Common Mistakes to Avoid  
- Using a single heap or naïve sorting leads to O(n) query times.  
- Forgetting to maintain the order constraint (`low.peek() ≤ high.peek()`).  
- Integer overflow in Java when adding two heap tops.  

#### 5. The Ugly – What Interviewers Don’t Want  
- Cloning or polling heaps inside `findMedian()`.  
- Unbalanced heaps that break the median for odd/even lengths.  
- Re‑implementing merge‑sort on every insert – an *O(n log n)* disaster.  

#### 6. Implementation Details  
- Java: `PriorityQueue<Integer>` with `Comparator.reverseOrder()` for the max‑heap.  
- Python: `heapq` with sign inversion to simulate a max‑heap.  
- C++: `std::priority_queue` for max‑heap and a min‑heap with `std::greater<int>`.  

Each snippet is accompanied by clear comments so you can copy‑paste into LeetCode or your IDE.

#### 7. Bonus: When the Input is Restricted to 0–100  
If the problem guarantees small integer ranges, a frequency array gives *O(1) insert* and *O(1)* median in practice.  
But interviewers will usually expect the general two‑heap solution – mention the optimization only if asked.

#### 8. Takeaway – Why You Should Nail This Question  
- **Design skill:** Show you can build a *dynamic* data structure.  
- **Algorithmic depth:** `O(log n)` insert, `O(1)` query – a classic efficiency benchmark.  
- **Coding confidence:** Ready‑to‑run code in Java, Python, and C++ – perfect for remote coding tests.  

---

### 📣 Call‑to‑Action  
> *“Want to crack more LeetCode problems? Subscribe for deeper dives into interview‑style data‑structure solutions.”*  

---

### 🚀 Endnote  

Mastering LeetCode 295 equips you with a powerful mental model: *two heaps + rebalancing*.  
You can discuss the **trade‑offs** (good/bad/ugly) in any data‑structure interview, impress hiring managers with your design thinking, and walk away with a **robust, production‑ready** solution in three major languages.

Happy coding, and best of luck on your next interview! 🚀

---

## 5. Closing Thoughts

Whether you’re prepping for a **Google**, **Amazon**, or **Microsoft** interview, this blog and code snippets give you:

1. A *ready‑to‑run* solution in the language of your choice.  
2. A clear framework to explain *why* the algorithm works (Good).  
3. A checklist of common mistakes to avoid (Bad).  
4. A quick‑look at edge‑case pitfalls (Ugly).  

Use the “Good‑Bad‑Ugly” narrative to guide your interview story, and you’ll leave recruiters convinced that you’re a true **data‑structure engineer**.

Happy interviewing! 💪

---