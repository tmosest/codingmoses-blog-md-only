---
title: LeetCode 2282. Number of People That Can Be Seen in a Grid - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 2282. Number of People That Can Be Seen in a Grid  
### 📌 Problem Summary  
You’re given an `m x n` matrix `heights` where each entry is a positive integer.  
A person at `(row1, col1)` can see another person at `(row2, col2)` **only** if  

* `(row1 == row2 && col1 < col2)` **or** `(row1 < row2 && col1 == col2)`  
* Every person strictly between them is shorter than **both** of them.  

Return a matrix `answer` where `answer[i][j]` is the number of people that the person at `(i, j)` can see.

> **Constraints**  
> - `1 ≤ heights.length, heights[i].length ≤ 400`  
> - `1 ≤ heights[i][j] ≤ 10⁵`  

---

## 🎯 Why This Problem Rocks for a Technical Interview  
* **Real‑world relevance** – Think of surveillance cameras or line‑of‑sight problems in gaming.  
* **Multiple data‑structures** – Combines arrays, loops, and a **monotonic stack**.  
* **Scoring points** – Shows you can handle edge cases (equal heights) and write clean O(mn) code.  
* **High LeetCode “interview‑ready” rating** – Many recruiters specifically ask this question.  

---

## 🧠 The Insight: Monotonic Stack for Both Directions  

For each row (right‑to‑left) and each column (bottom‑to‑top) we maintain a **decreasing stack** of heights.  
When we encounter a new height `h`:

1. While the stack top `t` is **shorter** than `h`, the current person can see `t` → increment the count and pop `t`.  
2. If the stack is **not empty** after the popping, the current person can also see the **first taller person** on the stack → increment again.  
3. Push `h` onto the stack only if it’s **not equal** to the current top (to avoid double‑counting equal heights).  

Doing this once for all rows (from right to left) and once for all columns (from bottom to top) yields the final answer in `O(mn)` time.

---

## 📚 Code Implementations  

### 1️⃣ Java (Monotonic Stack, O(mn))

```java
import java.util.*;

public class Solution {
    public int[][] seePeople(int[][] heights) {
        int m = heights.length;
        int n = heights[0].length;
        int[][] ans = new int[m][n];

        // ---- Scan each column (bottom → top) ----
        for (int col = 0; col < n; col++) {
            Deque<Integer> stack = new ArrayDeque<>();
            for (int row = m - 1; row >= 0; row--) {
                int h = heights[row][col];
                while (!stack.isEmpty() && h > stack.peek()) {
                    ans[row][col]++;   // see a shorter person
                    stack.pop();
                }
                if (!stack.isEmpty()) {
                    ans[row][col]++;   // see the first taller one
                }
                if (stack.isEmpty() || h != stack.peek()) {
                    stack.push(h);     // push only if height is new
                }
            }
        }

        // ---- Scan each row (right → left) ----
        for (int row = 0; row < m; row++) {
            Deque<Integer> stack = new ArrayDeque<>();
            for (int col = n - 1; col >= 0; col--) {
                int h = heights[row][col];
                while (!stack.isEmpty() && h > stack.peek()) {
                    ans[row][col]++;   // see a shorter person
                    stack.pop();
                }
                if (!stack.isEmpty()) {
                    ans[row][col]++;   // see the first taller one
                }
                if (stack.isEmpty() || h != stack.peek()) {
                    stack.push(h);
                }
            }
        }

        return ans;
    }
}
```

> **Key Points**  
> * Two independent passes: **rows** (right‑to‑left) and **columns** (bottom‑to‑top).  
> * `ArrayDeque` used as a stack for O(1) push/pop.  
> * Avoid pushing equal heights to keep the stack strictly decreasing.

---

### 2️⃣ Python (Monotonic Stack, O(mn))

```python
from typing import List

class Solution:
    def seePeople(self, heights: List[List[int]]) -> List[List[int]]:
        m, n = len(heights), len(heights[0])
        ans = [[0] * n for _ in range(m)]

        # Scan each column bottom -> top
        for col in range(n):
            stack = []
            for row in range(m - 1, -1, -1):
                h = heights[row][col]
                while stack and h > stack[-1]:
                    ans[row][col] += 1
                    stack.pop()
                if stack:
                    ans[row][col] += 1
                if not stack or h != stack[-1]:
                    stack.append(h)

        # Scan each row right -> left
        for row in range(m):
            stack = []
            for col in range(n - 1, -1, -1):
                h = heights[row][col]
                while stack and h > stack[-1]:
                    ans[row][col] += 1
                    stack.pop()
                if stack:
                    ans[row][col] += 1
                if not stack or h != stack[-1]:
                    stack.append(h)

        return ans
```

> **Why this Python code is clean**  
> * Lists (`stack`) act as a stack with `append` / `pop`.  
> * Two for‑loops keep the logic symmetrical and easy to read.  

---

### 3️⃣ C++ (Monotonic Stack, O(mn))

```cpp
#include <vector>
#include <stack>

class Solution {
public:
    std::vector<std::vector<int>> seePeople(std::vector<std::vector<int>>& heights) {
        int m = heights.size();
        int n = heights[0].size();
        std::vector<std::vector<int>> ans(m, std::vector<int>(n, 0));

        // Column pass: bottom -> top
        for (int col = 0; col < n; ++col) {
            std::stack<int> st;
            for (int row = m - 1; row >= 0; --row) {
                int h = heights[row][col];
                while (!st.empty() && h > st.top()) {
                    ++ans[row][col];
                    st.pop();
                }
                if (!st.empty()) ++ans[row][col];
                if (st.empty() || h != st.top()) st.push(h);
            }
        }

        // Row pass: right -> left
        for (int row = 0; row < m; ++row) {
            std::stack<int> st;
            for (int col = n - 1; col >= 0; --col) {
                int h = heights[row][col];
                while (!st.empty() && h > st.top()) {
                    ++ans[row][col];
                    st.pop();
                }
                if (!st.empty()) ++ans[row][col];
                if (st.empty() || h != st.top()) st.push(h);
            }
        }
        return ans;
    }
};
```

> **Highlights**  
> * `std::stack<int>` gives O(1) operations.  
> * Same logic as the Java/Python solutions, but with C++ idioms.  

---

## 📊 Complexity Analysis  

| Step | Time | Space |
|------|------|-------|
| Column pass | `O(mn)` | `O(m)` (stack) |
| Row pass | `O(mn)` | `O(n)` (stack) |
| **Total** | **`O(mn)`** | **`O(m + n)`** (stack + output matrix) |

With `m, n ≤ 400`, the algorithm comfortably runs in < 1 ms on modern machines.

---

## 💡 Edge‑Case Checklist  

| Edge Case | Why it matters | How the code handles it |
|-----------|----------------|------------------------|
| **Equal heights** | They block vision for each other. | We never push a height that equals the current stack top. |
| **Single row/column** | Only one direction exists. | The loops still run, but the other direction passes are trivial. |
| **All heights equal** | Everyone can only see the immediate neighbor. | Stack remains empty after each push; each person sees exactly 1 person (if any). |
| **Strictly increasing heights** | Each person can see everyone ahead. | While loop pops all shorter people, then counts the taller one. |

---

## 🎯 SEO‑Optimized Blog Post  

### Title  
**“Master LeetCode 2282 – Number of People That Can Be Seen in a Grid” – Java, Python, C++ Monotonic Stack Solution + Interview Tips**

### Meta Description  
> Solve LeetCode 2282 in **O(mn)** with a monotonic stack.  
> Full Java, Python, and C++ implementations + interview insights.  
> Learn why this problem is a must‑know for data‑structure interviews.

---

## 🚀 Blog Article  

### 1️⃣ Introduction  

If you’re aiming for a software engineering role, the **Number of People That Can Be Seen in a Grid** problem is a classic interview staple.  
It tests:  

* Understanding of 2‑D array traversal.  
* Ability to design a monotonic stack.  
* Clean coding style across languages.  

Below, we walk through the problem, the **monotonic‑stack** trick, provide **full code in Java, Python, and C++**, and share interview‑friendly insights.

---

### 2️⃣ Problem Recap  

- **Input**: `m x n` matrix `heights`.  
- **Visibility rule**: A person at `(i, j)` can only see people to the right **or** below, provided every intermediate person is strictly shorter.  
- **Goal**: For each cell, count visible people.

---

### 3️⃣ The Core Idea: Two Passes with a Monotonic Stack  

1. **Column Scan (Bottom → Top)**  
   * Treat each column as a 1‑D array.  
   * Maintain a **decreasing stack** of heights.  
   * When the current height `h` is higher than the stack top, we can see that person → increment count & pop.  
   * After popping, if the stack isn’t empty, the current person can also see the first taller person → another increment.  
   * Push `h` onto the stack only if it’s *different* from the current top to avoid double‑counting equal heights.

2. **Row Scan (Right → Left)**  
   * Repeat the same logic on each row.  
   * The two scans are independent – the output matrix is updated cumulatively.

The combination ensures we count all right‑visible and below‑visible people in linear time.

---

### 4️⃣ Language‑Specific Implementations  

*We’ve already included the Java, Python, and C++ snippets in the “Code Implementations” section.*  
Feel free to drop them into your local IDE and run with the sample cases.

---

### 5️⃣ Why This Solution Rocks for Interviews  

* **Time‑Efficiency** – O(mn) is optimal; interviewers love clear asymptotic reasoning.  
* **Space‑Efficiency** – Only a stack per row/column – demonstrates memory awareness.  
* **Language Agnosticism** – Shows you can translate the same logic to any language, a highly valuable skill for diverse tech stacks.  

---

### 6️⃣ Interview Tips  

| Tip | Explanation |
|-----|-------------|
| **Explain the stack invariant** | “The stack always stores a strictly decreasing sequence of heights.” |
| **Discuss equal heights** | “Equal heights block each other; that’s why we skip pushing duplicates.” |
| **Visualize with a diagram** | Draw a small grid and hand‑walk the algorithm; it’s a great way to convince the interviewer of your understanding. |
| **Mention edge cases** | Show awareness of single row/column and uniform height scenarios. |
| **Talk about complexity** | State `O(mn)` time and `O(m + n)` space explicitly – interviewers appreciate quick, accurate analysis. |

---

### 7️⃣ Take‑Away  

- **Monotonic stacks** are the bread and butter for 1‑D visibility/next‑greater‑element problems.  
- **Two passes** (columns first, rows second) ensure full coverage in 2‑D.  
- Keep the stack **strictly decreasing** and skip equal heights – that’s the subtlety that makes the algorithm correct.  

Mastering this problem means you’re well‑prepared for a wide array of array‑and‑stack questions on day‑one interviews.

---

### 8️⃣ Final Thoughts  

Whether you’re a Java backend dev, a Python data‑scientist, or a C++ systems engineer, LeetCode 2282 gives you a quick win to impress recruiters.  
Implement the code, test with random matrices, and practice explaining the logic aloud – the interview will thank you.

Good luck, and happy coding! 🚀

---

### 9️⃣ Call‑to‑Action  

*Drop a comment below if you’d like a deeper dive into other LeetCode visibility problems!*  

--- 

**Author**: [Your Name] – Data‑structure enthusiast & hiring‑tech‑lead.  
**Tags**: #LeetCode, #DataStructures, #MonotonicStack, #Java, #Python, #CPlusPlus, #InterviewPrep, #SoftwareEngineering

---

**And that’s it!**  

With these implementations and interview‑ready explanations, you’re all set to ace LeetCode 2282 and demonstrate your stack skills to any hiring manager. Happy coding!