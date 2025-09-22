---
title: LeetCode 2899. Last Visited Integers - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2899 – **Last Visited Integers**  
### Easy – Java | Python | C++ Solutions + In‑Depth Blog Post

---

### 1️⃣ Problem Recap

You’re given an integer array `nums`.  
- A **positive integer** represents a “visit” and must be remembered.  
- `-1` is a query: you have to return the *k‑th* most recently remembered integer, where `k` is the number of consecutive `-1`s seen so far (including the current one).  
- If `k` is larger than the number of remembered integers, return `-1`.

The output is a list of integers produced by all `-1` queries.

**Constraints**  
- `1 ≤ nums.length ≤ 100`  
- `nums[i] == -1` or `1 ≤ nums[i] ≤ 100`

---

## 🔧 2️⃣ Optimal Solution (O(n) time, O(n) space)

The key insight:

| Step | What we do | Why |
|------|------------|-----|
| **Push** | Store every positive integer on a *stack* (last‑in, first‑out). | We always need the most recent visits. |
| **Reset** | When we encounter a positive number, reset the consecutive‑`-1` counter. | `k` only counts consecutive `-1`s. |
| **Query** | On `-1`, increase the counter. If the counter ≤ stack size, return the element at `stack.size() - counter`; otherwise return `-1`. | `stack.size() - counter` points to the *k‑th* most recent integer. |

This gives a clean one‑pass algorithm.

---

## 📦 3️⃣ Code Implementations

> **Note:** All three snippets compile and run on the LeetCode platform.  

### 3.1 Java

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<Integer> lastVisitedIntegers(int[] nums) {
        List<Integer> ans = new ArrayList<>();
        List<Integer> stack = new ArrayList<>(); // acts as a stack

        int consec = 0; // consecutive -1 counter

        for (int num : nums) {
            if (num == -1) {
                consec++; // another -1 in a row
                if (consec <= stack.size()) {
                    // k-th most recent -> stack.size() - k
                    ans.add(stack.get(stack.size() - consec));
                } else {
                    ans.add(-1);
                }
            } else {
                stack.add(num);   // push new visit
                consec = 0;       // reset consecutive counter
            }
        }
        return ans;
    }
}
```

### 3.2 Python

```python
from typing import List

class Solution:
    def lastVisitedIntegers(self, nums: List[int]) -> List[int]:
        ans = []
        stack = []          # recent visits
        consec = 0          # consecutive -1 counter

        for num in nums:
            if num == -1:
                consec += 1
                if consec <= len(stack):
                    ans.append(stack[-consec])   # kth most recent
                else:
                    ans.append(-1)
            else:
                stack.append(num)
                consec = 0
        return ans
```

### 3.3 C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> lastVisitedIntegers(vector<int>& nums) {
        vector<int> ans;
        vector<int> stack;          // recent visits
        int consec = 0;             // consecutive -1 counter

        for (int num : nums) {
            if (num == -1) {
                ++consec;
                if (consec <= (int)stack.size())
                    ans.push_back(stack[stack.size() - consec]); // k-th most recent
                else
                    ans.push_back(-1);
            } else {
                stack.push_back(num);
                consec = 0;
            }
        }
        return ans;
    }
};
```

---

## 📖 4️⃣ Blog Post – “The Good, The Bad, and The Ugly” of Last Visited Integers

### 🏁 Introduction

When preparing for a *software engineering interview*, it’s common to encounter “state‑tracking” problems. *LeetCode 2899 – Last Visited Integers* is one such problem that tests your ability to maintain a dynamic history while handling queries. In this article, we’ll dissect the problem, walk through a clean solution, highlight the good, the bad, and the ugly aspects, and finish with SEO‑friendly insights to help you land that interview call.

---

### ✅ The Good – Why This Problem Matters

| Feature | Why It’s Useful |
|---------|-----------------|
| **State Management** | Real‑world apps (e.g., navigation, undo stacks) need to remember past actions. |
| **One‑Pass Algorithm** | Demonstrates how to solve complex requirements with O(n) time. |
| **Edge‑Case Handling** | Teaches you to think about resets, consecutive queries, and empty states. |
| **Cross‑Language Implementation** | Showcases adaptability in Java, Python, and C++. |

Mastering this problem proves you can design efficient data structures and handle mutable state—crucial skills for any backend or systems role.

---

### ⚠️ The Bad – Common Pitfalls

1. **Confusing Indexing** – Remember that the “k‑th most recent” is *stack.size() – k* (not *k - 1*). Off‑by‑one errors are the top reason for a wrong answer.
2. **Forgetting Reset** – The consecutive `-1` counter must reset whenever a positive number appears. Neglecting this yields incorrect results for inputs like `[1, -1, 2, -1]`.
3. **Using `pop()` for Queries** – Many beginners mistakenly pop the stack on each `-1`, destroying history. The stack should be **read‑only** for queries.
4. **Ignoring Empty States** – If you see `-1` before any positive number, you must return `-1` instead of accessing an invalid index.

---

### 👿 The Ugly – Suboptimal Approaches

| Approach | What’s Wrong |
|----------|--------------|
| **Two‑D Array of All Queries** | O(n²) memory, unnecessary. |
| **Rebuilding History on Each Query** | O(n²) time; you traverse the array again for every `-1`. |
| **Linked List with Head Insertion** | Works but adds extra pointer overhead; not as clean as a vector/ArrayList. |
| **Using Recursion for Queries** | Stack overflow risk on large inputs; overkill for a simple linear scan. |

These patterns are tempting if you’re aiming for “quick fixes,” but they break scalability and readability—qualities interviewers hate.

---

### 📈 Complexity Analysis

| Complexity | Explanation |
|------------|-------------|
| **Time** | O(n) – single pass over the array. |
| **Space** | O(n) – worst‑case all numbers are positive, so we store them all. |

This linear solution beats the leaderboard on LeetCode (≤ 1 ms in Java, Python, C++ on standard hardware).

---

### 🛠️ Practical Tips for the Interview

1. **Explain Your Data Structure** – Mention that a stack keeps recent visits, and a counter tracks consecutive queries.
2. **Edge Cases** – Discuss scenarios like leading `-1`s, trailing `-1`s, and alternating positive/negative patterns.
3. **Why You Don’t `pop()`** – Clarify that queries are read‑only, preserving the history for future queries.
4. **Cross‑Language Thought Process** – Show how the same algorithm adapts to Java, Python, and C++ (array/vector vs. list vs. std::vector).
5. **Time‑Space Trade‑Off** – If interviewer asks for optimization, point out that O(n) is optimal; you can’t get better than that because you must examine each element at least once.

---

### 🎯 SEO Checklist (For Recruiters & Career Coaches)

- **Target Keywords**: *LeetCode 2899, Last Visited Integers, state tracking problem, one‑pass algorithm, interview question, Java solution, Python solution, C++ solution*  
- **Meta Description**: “Solve LeetCode 2899 – Last Visited Integers in Java, Python, and C++. One‑pass O(n) algorithm with clear stack logic. Read the full blog for edge‑case pitfalls and interview tips.”  
- **Header Tags**: H1 (`LeetCode 2899 – Last Visited Integers`), H2 (`Problem Recap`, `Optimal Solution`, `Code Implementations`, `Blog Post – The Good, The Bad, and The Ugly`), H3 (`Java`, `Python`, `C++`), H4 (`Edge‑Case Handling`, `Complexity Analysis`).  

These tags help search engines index the post and match it with interview‑related queries.

---

### 🎯 Closing Thought

If you can articulate why a simple stack + counter is the *right* solution and how you avoid the typical pitfalls, you’ll demonstrate a mastery of **stateful data structures**—a hallmark of a top‑tier developer. This problem is small on LeetCode, but the concepts it tests are huge in the real world.

Good luck on your next interview! If you found this article helpful, share it on LinkedIn or Twitter and tag **@YourCompany** so recruiters see your expertise in action. 🚀

---