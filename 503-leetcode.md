---
title: LeetCode 503. Next Greater Element II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 503. Next Greater Element II – “The Good, The Bad, and The Ugly”  
### (A Job‑Interview Friendly, SEO‑Optimized Guide)

> **Keywords**: Next Greater Element II, LeetCode 503, Circular Array, Monotonic Stack, Java, Python, C++, Algorithm, Data Structures, Interview Prep, Job Interview  

---

## 📌 TL;DR

- **Problem**: For each element in a *circular* integer array, find the next greater element. If none exists, return `-1`.  
- **Best Solution**: **Monotonic stack** + **two‑pass simulation** (time `O(n)`, space `O(n)`).  
- **Why It Matters**: Mastering this pattern shows you know *stack tricks*, *circular logic*, and *optimal algorithms*—all highly valued in coding interviews.

---

## 📝 Problem Statement (LeetCode 503)

> **Given** a circular integer array `nums` (the element after the last is the first).  
> **Return** an array where each position contains the **next greater number** of the corresponding element.  
> If no greater element exists, put `-1`.

> **Example**  
> `nums = [1, 2, 1] → [2, -1, 2]`  

---

## 📚 The “Good”: Optimal Algorithm

### 1️⃣ Monotonic Stack

A stack that keeps indices in **decreasing** order of their values.  
While iterating, we pop smaller or equal elements—those can never be the next greater for any future index.

### 2️⃣ Two‑Pass Simulation

Because the array is circular, we iterate *twice* over the indices (or use modulo arithmetic).  
- First pass: fill the stack for the last part of the array.
- Second pass: finish the first part using the same stack logic.

### 3️⃣ Complexity

| Metric | Java / Python / C++ |
|--------|---------------------|
| Time   | **O(n)**            |
| Space  | **O(n)** (stack + result) |

---

## ❌ The “Bad”: Brute‑Force Approach

```java
int[] res = new int[n];
for (int i = 0; i < n; ++i) {
    int next = -1;
    for (int j = 1; j <= n; ++j) {             // scan all positions
        int idx = (i + j) % n;
        if (nums[idx] > nums[i]) {
            next = nums[idx];
            break;
        }
    }
    res[i] = next;
}
```

- **Time**: `O(n²)` – unacceptable for `n = 10⁴`.  
- **Space**: `O(1)` – but the huge time cost outweighs it.

---

## ⚡ The “Ugly”: Common Pitfalls

| Pitfall | What Happens | Fix |
|---------|--------------|-----|
| **Using `Stack<Integer>` (Java)** | `Stack` is synchronized; overhead. | Use `ArrayDeque<Integer>` or `IntStack` for performance. |
| **Not handling duplicates** | Wrong comparison (`<=` vs `<`). | Use `<=` to skip equal elements; otherwise you might miss the correct next greater. |
| **Modulo misuse** | `i % n` on negative indices. | Iterate from `2*n - 1` down to `0` and always use `i % n` to map back. |
| **Space leak** | Forget to clear stack before reuse. | Clear or reinitialize for each test case. |

---

## 🧩 Code Solutions

Below are clean, production‑ready implementations for **Java, Python, and C++**.

---

### Java (Optimized with `ArrayDeque`)

```java
import java.util.ArrayDeque;

public class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];
        ArrayDeque<Integer> stack = new ArrayDeque<>();

        for (int i = 2 * n - 1; i >= 0; --i) {
            int idx = i % n;
            while (!stack.isEmpty() && nums[stack.peek()] <= nums[idx]) {
                stack.pop();
            }
            res[idx] = stack.isEmpty() ? -1 : nums[stack.peek()];
            stack.push(idx);
        }
        return res;
    }
}
```

---

### Python (Using `list` as a Stack)

```python
from typing import List

class Solution:
    def nextGreaterElements(self, nums: List[int]) -> List[int]:
        n = len(nums)
        res = [-1] * n
        stack: List[int] = []

        for i in range(2 * n - 1, -1, -1):
            idx = i % n
            while stack and nums[stack[-1]] <= nums[idx]:
                stack.pop()
            if stack:
                res[idx] = nums[stack[-1]]
            stack.append(idx)
        return res
```

---

### C++ (Using `std::vector` & `std::stack`)

```cpp
#include <vector>
#include <stack>

class Solution {
public:
    std::vector<int> nextGreaterElements(std::vector<int>& nums) {
        int n = nums.size();
        std::vector<int> res(n, -1);
        std::stack<int> st; // indices

        for (int i = 2 * n - 1; i >= 0; --i) {
            int idx = i % n;
            while (!st.empty() && nums[st.top()] <= nums[idx]) st.pop();
            if (!st.empty()) res[idx] = nums[st.top()];
            st.push(idx);
        }
        return res;
    }
};
```

---

## 🚀 Running the Code

### Java

```bash
javac Solution.java
java Solution
```

### Python

```bash
python -c "print(__import__('collections').deque())"
```

### C++

```bash
g++ -std=c++17 solution.cpp -o solution
./solution
```

> Replace the `main` function with test cases as needed.

---

## 🎯 Why This Matters in Interviews

1. **Algorithmic Thinking** – Shows you can reduce `O(n²)` to `O(n)` by leveraging data structure properties.  
2. **Understanding Circular Logic** – Many interview problems hide a “wrap‑around” twist.  
3. **Stack Mastery** – Monotonic stacks are a staple in daily coding interviews.  
4. **Time & Space Trade‑offs** – You’ll impress interviewers by explaining complexity clearly.

---

## 📈 SEO‑Ready Meta Description

> “Learn how to solve LeetCode 503 – Next Greater Element II – with Java, Python, and C++ solutions. Master monotonic stack, circular array logic, and optimize your interview preparation.”

---

## 📌 Checklist Before Your Next Interview

- [ ] Understand **circular array** indexing (`(i + 1) % n`).  
- [ ] Know the **monotonic stack** invariant.  
- [ ] Write the solution **in two passes** or using modulo.  
- [ ] Explain **time/space complexity** clearly.  
- [ ] Discuss common pitfalls (duplicates, stack implementation).  

---

## 📚 Further Reading

- LeetCode Problem 503: [Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/)
- Monotonic Stack Pattern:  
  - GeeksforGeeks: “Monotonic Stack”  
  - InterviewBit: “Next Greater Element”  
- Circular Array Techniques:  
  - “Array Wrapping Tricks” – Medium article  
  - “Two‑Pointer Circular Arrays” – HackerRank  

---

### Final Thought

Mastering “Next Greater Element II” is more than solving a single LeetCode problem.  
It’s about showing that you can **recognize a pattern** (circular + next greater), **apply the right data structure** (monotonic stack), and **optimize time and space**.  
These skills translate directly to real‑world coding challenges and make you a standout candidate in any technical interview. Good luck! 🚀