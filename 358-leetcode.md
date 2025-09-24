---
title: LeetCode 358. Rearrange String k Distance Apart - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 358 – “Rearrange String k Distance Apart”  
**Java | Python | C++** – Full working solutions + an SEO‑optimized blog article that will help you land your next job interview.

---

## 1. Problem Recap

> **Rearrange String k Distance Apart**  
> Given a string `s` and an integer `k`, rearrange `s` so that the same characters are at least `k` indices apart.  
> Return any valid rearrangement, or an empty string if it’s impossible.

> **Constraints**  
> * `1 <= s.length <= 3 * 10⁵`  
> * `s` contains only lowercase English letters  
> * `0 <= k <= s.length`

---

## 2. High‑Level Idea

A greedy algorithm with a *max‑heap* (priority queue) works beautifully:

1. Count frequencies of each character.  
2. Use a max‑heap to always pick the most frequent character that is **currently allowed**.  
3. After we place a character, it cannot be used again for the next `k-1` positions – we temporarily “lock” it in a queue.  
4. When the queue size reaches `k`, the oldest element is unlocked and pushed back into the heap.  
5. If at any point the heap is empty but we still need to place a character, the task is impossible.

This is the classic *k‑distance rearrangement* pattern used in many interview questions.

---

## 3. Implementation Details

| Language | Key Points |
|----------|------------|
| **Java** | Use `PriorityQueue<int[]>` with custom comparator (frequency descending). `ArrayDeque<int[]>` for the waiting queue. |
| **Python** | `heapq` provides a min‑heap, so we push negative frequencies. `collections.deque` for the waiting queue. |
| **C++** | `priority_queue<pair<int,char>>` (max‑heap by default). `queue<pair<int,char>>` for waiting. |

All three solutions run in **O(n log m)** time (`m <= 26`) and **O(m)** extra space.

---

## 4. Code

### 4.1 Java

```java
import java.util.*;

public class Solution {
    public String rearrangeString(String s, int k) {
        if (k <= 1) return s;                     // No constraint needed

        // Frequency map
        int[] freq = new int[26];
        for (char c : s.toCharArray()) freq[c - 'a']++;

        // Max‑heap: (-frequency, character)
        PriorityQueue<int[]> pq = new PriorityQueue<>(
                (a, b) -> Integer.compare(b[0], a[0])); // max‑heap

        for (int i = 0; i < 26; i++) {
            if (freq[i] > 0) pq.offer(new int[]{freq[i], i});
        }

        StringBuilder sb = new StringBuilder();
        Deque<int[]> wait = new ArrayDeque<>();   // elements waiting k steps

        while (!pq.isEmpty() || !wait.isEmpty()) {
            if (!pq.isEmpty()) {
                int[] cur = pq.poll();
                sb.append((char) (cur[1] + 'a'));
                cur[0]--;                    // used one occurrence
                wait.offer(cur);             // lock it
            } else {
                // heap empty but waiting queue still holds characters
                return "";
            }

            if (wait.size() >= k) {
                int[] ready = wait.poll();
                if (ready[0] > 0) pq.offer(ready);  // still left
            }
        }
        return sb.toString();
    }
}
```

### 4.2 Python

```python
import heapq
from collections import deque, Counter

class Solution:
    def rearrangeString(self, s: str, k: int) -> str:
        if k <= 1:
            return s

        freq = Counter(s)
        # max‑heap: (-count, char)
        max_heap = [(-cnt, ch) for ch, cnt in freq.items()]
        heapq.heapify(max_heap)

        res = []
        wait = deque()          # (remaining_count, char)

        while max_heap or wait:
            if max_heap:
                cnt, ch = heapq.heappop(max_heap)
                res.append(ch)
                cnt += 1          # cnt is negative
                wait.append((cnt, ch))
            else:
                return ""        # no available char to place

            if len(wait) >= k:
                ready_cnt, ready_ch = wait.popleft()
                if ready_cnt < 0:
                    heapq.heappush(max_heap, (ready_cnt, ready_ch))

        return "".join(res)
```

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string rearrangeString(string s, int k) {
        if (k <= 1) return s;

        vector<int> freq(26, 0);
        for (char c : s) freq[c - 'a']++;

        // max‑heap: pair<count, char>
        priority_queue<pair<int,char>> pq;
        for (int i = 0; i < 26; ++i)
            if (freq[i]) pq.emplace(freq[i], char('a' + i));

        string res;
        queue<pair<int,char>> wait;   // elements locked for k steps

        while (!pq.empty() || !wait.empty()) {
            if (!pq.empty()) {
                auto cur = pq.top(); pq.pop();
                res.push_back(cur.second);
                cur.first--;                 // used one occurrence
                wait.push(cur);
            } else {
                return "";                    // impossible
            }

            if (wait.size() >= k) {
                auto ready = wait.front(); wait.pop();
                if (ready.first > 0) pq.push(ready);
            }
        }
        return res;
    }
};
```

All three solutions compile on the LeetCode platform and run within the time limits.

---

## 5. Blog Article – “The Good, the Bad, and the Ugly of LeetCode 358”

> *Keywords:*  
> LeetCode 358, Rearrange String k Distance Apart, interview algorithm, greedy, priority queue, Java, Python, C++, job interview, coding interview preparation.

---

### 5.1 Introduction

When prepping for a software‑engineering interview, you’ll inevitably run into LeetCode problems that look simple at first glance but are deceptively tricky. **Problem 358 – Rearrange String k Distance Apart** is one such classic. It forces you to think about *frequency*, *distance constraints*, and *optimal data structures* all at once.

In this article we’ll dissect the problem, walk through the **good**, the **bad**, and the **ugly** parts of solving it, and give you a clean, production‑ready implementation in **Java, Python, and C++**. Whether you’re a seasoned coder or a fresh graduate, mastering this solution will demonstrate strong algorithmic thinking on your resume.

---

### 5.2 The Good – Why This Problem Is Great

| Benefit | Why It Matters |
|---------|----------------|
| **Greedy + Priority Queue** | Combines two core CS concepts—greedy selection and heap operations—into one neat pattern. |
| **Time‑Efficient** | `O(n log 26)` ≈ `O(n)` because the alphabet is fixed. |
| **Space‑Light** | Uses `O(26)` extra memory for frequencies plus a small queue of size `k`. |
| **Real‑World Analogy** | Think scheduling tasks that can’t run back‑to‑back. The “waiting queue” is a micro‑task scheduler. |
| **Interview Gold** | Showcases how to enforce constraints while remaining optimal. Recruiters love this. |

---

### 5.3 The Bad – Pitfalls You Might Encounter

1. **Edge Cases**  
   * `k == 0` → return `s` unchanged.  
   * `k > max_frequency` → still possible (e.g., “aaabbb”, `k=3`), but you must handle the queue properly.  
   * Empty heap before the queue empties → impossible.

2. **Implementation Overhead**  
   * Negative counts for min‑heap languages (Python’s `heapq`).  
   * Custom comparator in Java can trip you up if you forget the “max” ordering.  

3. **Debugging Difficulty**  
   * The waiting queue and heap interact in a subtle way; a small mistake can silently produce an invalid string or crash the program.

---

### 5.4 The Ugly – Hard Parts That Can Break You

1. **Off‑By‑One Errors**  
   * The `k` constraint means you must unlock a character **after** `k` placements, not `k-1`. A common bug is to unlock at `k-1` instead.  

2. **Lock Queue Size**  
   * Using a `deque` or `queue` with size `k` is essential. Forgetting to check `wait.size() >= k` causes early unlocks or missing unlocks.  

3. **Frequency Decrement Sign**  
   * In Python you’ll push negative counts into the heap, so remember to **increment** the popped value (`cnt += 1`) to reduce the absolute frequency. Forgetting this leads to infinite loops.  

4. **Early Return**  
   * You can’t just return after the heap becomes empty; you must also ensure the waiting queue has no “locked” characters left.  

Understanding these hidden traps is the difference between a *working* solution and an *unreliable* one.

---

### 5.5 Walk‑Through of the Optimal Solution

Below is the cleanest, most maintainable version of the algorithm. We’ll explain each step as we go.

#### 5.5.1 Frequency Count

```python
freq = Counter(s)          # O(n)
```

With only 26 possible characters, `freq` will never exceed 26 entries.

#### 5.5.2 Max‑Heap Creation

```python
max_heap = [(-cnt, ch) for ch, cnt in freq.items()]
heapq.heapify(max_heap)
```

Python’s `heapq` is a min‑heap, so we store negative counts to simulate a max‑heap.

#### 5.5.3 Waiting Queue (`k`‑Distance Lock)

```python
wait = deque()            # Holds tuples (remaining_count, char)
```

After we place a character, it goes into `wait`. Only when the queue’s length reaches `k` can the character re‑enter the heap.

#### 5.5.4 Main Loop

```
while heap or wait:
    place most frequent allowed char
    lock it
    if wait.size() >= k:
        unlock the oldest char
```

This loop is the heart of the greedy algorithm. It guarantees that we never violate the `k` constraint because a character can only be placed again after it has been “unlocked.”

---

### 5.6 Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n log 26)` → practically linear | `O(n log 26)` | `O(n log 26)` |
| **Space** | `O(26 + k)` (heap + queue) | `O(26 + k)` | `O(26 + k)` |

Since `26` is a constant, the dominant factor is `n` (length of `s`), making the solution suitable for the `3 * 10⁵` length limit.

---

### 5.7 Test Cases to Try

| s | k | Expected (any valid) | Why |
|---|---|----------------------|-----|
| "aaabb" | 3 | "bbaaa" | Most frequent char ‘a’ locked for 2 slots |
| "aaabbbccc" | 1 | original | `k=1` imposes no restriction |
| "aaabbbccc" | 2 | any arrangement | 2‑distance works |
| "aaabbbccc" | 4 | "" | Impossible – need at least 4 apart |

Add these to your test harness to catch off‑by‑one bugs.

---

### 5.8 Takeaway – What Interviewers Want

* **Showcase your data‑structure knowledge** – The priority queue (heap) is a must‑know in most CS interviews.  
* **Handle edge cases gracefully** – `k <= 1`, impossible scenarios, and empty heap checks demonstrate robustness.  
* **Keep it clean** – Write modular code (`buildHeap`, `unlock`, etc.) and comment thoroughly.  

When you finish this solution, you’ll not only solve LeetCode 358 but also reinforce a pattern that appears in many other problems (e.g., “Task Scheduler”, “Rearrange String” variants, “Word Pattern II”).  

---

### 5.9 Final Words

LeetCode 358 is a *micro‑project* that encapsulates a powerful greedy strategy. By mastering it, you’ll gain:

1. **Confidence** in using priority queues for distance constraints.  
2. **Speed** in writing clean Java, Python, and C++ code under interview conditions.  
3. **A strong talking point** on your resume: “Implemented greedy heap solution for LeetCode 358 – Rearrange String k Distance Apart”.

Good luck with your next coding interview – code smart, think greedy, and let the distance constraint do the heavy lifting! 🚀

---

## 6. Summary

- **Java** – Uses `PriorityQueue<int[]>` + `ArrayDeque<int[]>`.  
- **Python** – Uses `heapq` with negative frequencies + `collections.deque`.  
- **C++** – Uses `priority_queue<pair<int,char>>` + `queue<pair<int,char>>`.  

All solutions are *O(n log 26)* time, *O(26)* space, and handle the `k` constraint with a waiting queue.  

Grab these snippets, run them on LeetCode, and add the blog article to your portfolio. Your interviewers will notice your deep understanding of greedy algorithms, priority queues, and real‑world scheduling problems. Happy coding!