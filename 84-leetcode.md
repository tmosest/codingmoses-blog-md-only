---
title: LeetCode 84. Largest Rectangle in Histogram - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1.  Code Solutions

Below you’ll find three production‑ready implementations that solve **LeetCode 84 – Largest Rectangle in Histogram**.  
All three use the classic *monotonic stack* technique, giving **O(n)** time and **O(n)** auxiliary space.

> **Problem** – Given an array `heights` of bar heights (width = 1), return the area of the largest rectangle that can be formed inside the histogram.

| Language | Function Signature | Complexity |
|----------|---------------------|------------|
| **Java** | `public int largestRectangleArea(int[] heights)` | Time: `O(n)`<br>Space: `O(n)` |
| **Python** | `def largest_rectangle_area(heights: List[int]) -> int` | Time: `O(n)`<br>Space: `O(n)` |
| **C++** | `int largestRectangleArea(vector<int>& heights)` | Time: `O(n)`<br>Space: `O(n)` |

---

### 1.1 Java

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class Solution {
    /**
     * Returns the area of the largest rectangle in a histogram.
     *
     * @param heights array of bar heights
     * @return maximum rectangle area
     */
    public int largestRectangleArea(int[] heights) {
        // Use a sentinel -1 to simplify width calculation
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(-1);
        int maxArea = 0;

        for (int i = 0; i < heights.length; i++) {
            // Pop until the stack's top is lower than current height
            while (stack.peek() != -1 && heights[i] <= heights[stack.peek()]) {
                int h = heights[stack.pop()];
                int w = i - stack.peek() - 1;
                maxArea = Math.max(maxArea, h * w);
            }
            stack.push(i);
        }

        // Clear the stack and process remaining bars
        while (stack.peek() != -1) {
            int h = heights[stack.pop()];
            int w = heights.length - stack.peek() - 1;
            maxArea = Math.max(maxArea, h * w);
        }

        return maxArea;
    }
}
```

---

### 1.2 Python

```python
from typing import List

class Solution:
    def largest_rectangle_area(self, heights: List[int]) -> int:
        """
        Compute the largest rectangle area in a histogram.
        :param heights: List[int] – bar heights
        :return: int – maximum area
        """
        stack = [-1]           # sentinel
        max_area = 0

        for i, h in enumerate(heights):
            while stack[-1] != -1 and h <= heights[stack[-1]]:
                height = heights[stack.pop()]
                width  = i - stack[-1] - 1
                max_area = max(max_area, height * width)
            stack.append(i)

        # Process remaining bars
        while stack[-1] != -1:
            height = heights[stack.pop()]
            width  = len(heights) - stack[-1] - 1
            max_area = max(max_area, height * width)

        return max_area
```

---

### 1.3 C++

```cpp
#include <vector>
#include <stack>
#include <algorithm>

class Solution {
public:
    int largestRectangleArea(std::vector<int>& heights) {
        std::stack<int> st;          // indices
        st.push(-1);                // sentinel
        int maxArea = 0;

        for (int i = 0; i < heights.size(); ++i) {
            while (st.top() != -1 && heights[i] <= heights[st.top()]) {
                int h = heights[st.top()];
                st.pop();
                int w = i - st.top() - 1;
                maxArea = std::max(maxArea, h * w);
            }
            st.push(i);
        }

        // Remaining bars
        while (st.top() != -1) {
            int h = heights[st.top()];
            st.pop();
            int w = heights.size() - st.top() - 1;
            maxArea = std::max(maxArea, h * w);
        }

        return maxArea;
    }
};
```

---

## 2.  Blog Post: “Largest Rectangle in Histogram – The Good, the Bad & the Ugly”

> **Meta‑Description**: Master LeetCode 84 in seconds. Learn the stack‑based algorithm, pitfalls, edge cases, and interview‑friendly code in Java, Python, & C++. Boost your data‑structure skills & land your next job!

---

### 2.1 H1 – “Largest Rectangle in Histogram: Mastering a Classic Hard Problem”

> Every senior engineer’s résumé mentions **Monotonic Stacks**. This article walks you through the *good*, *bad*, and *ugly* of the Largest Rectangle problem, plus clean, interview‑ready code in Java, Python, and C++.

---

### 2.2 H2 – Problem Recap & Constraints

- **Input**: `heights[0…n‑1]` – bar heights, width = 1  
- **Output**: area of the largest rectangle
- **Constraints**:  
  - `1 <= n <= 10⁵`  
  - `0 <= heights[i] <= 10⁴`

These limits rule out quadratic solutions; you need **O(n)** time.

---

### 2.3 H2 – The Good: Why a Monotonic Stack?

1. **Linear Time** – Each bar is pushed/popped at most once.  
2. **Elegant Geometry** – As you scan from left to right, the stack keeps the indices of bars in *strictly increasing* height order.  
3. **Simple Width Computation** – When popping a bar `h`, the next index on the stack (`stack.top()`) gives the left boundary; current index `i` is the right boundary.  
4. **Clear Proof of Correctness** – The stack invariant guarantees that every possible rectangle is considered exactly once.

---

### 2.4 H2 – The Bad: Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Off‑by‑One errors** | Mis‑counting width (`i - stack.peek() - 1` vs `i - stack.peek()`) | Use a sentinel `-1` to avoid negative widths |
| **Empty stack check** | Forgetting to handle the sentinel when popping | Initialize stack with `-1` or check `stack.empty()` |
| **Large Input** | Using recursion or naïve O(n²) loops | Stick to iterative stack approach |
| **Overflow** | Multiplying height * width for 10⁵ bars | Use `long` in languages where int overflow is possible (C++, Java) |

---

### 2.5 H2 – The Ugly: Edge Cases & Performance Traps

| Edge Case | Explanation | Strategy |
|-----------|-------------|----------|
| **All zeros** | Height = 0 → area = 0 | Stack will pop all immediately; max stays 0 |
| **Monotonically increasing** | Stack grows to size n | Still O(n) because no pops until the end |
| **Monotonically decreasing** | Stack shrinks each step | Still O(n) due to single pass |
| **Large equal heights** | Consecutive equal bars | The `<=` condition ensures you pop *all* equal heights to avoid missed rectangles |
| **Memory limits** | n = 100 000 → stack size ≈ n | Acceptable in all languages (≈ 0.8 MB) |

---

### 2.6 H2 – The Algorithm (Step‑by‑Step)

1. **Initialize** a stack with sentinel `-1`.  
2. **Iterate** over each index `i`:
   - While current height `h` ≤ height at stack's top:
     - Pop index `idx`.
     - Height = `heights[idx]`.
     - Width = `i - stack.top() - 1`.
     - Update `maxArea`.
   - Push `i` onto the stack.
3. **Clean up** remaining indices after the loop (similar pop logic with `n` as right boundary).
4. **Return** `maxArea`.

---

### 2.7 H2 – Code Snippets (All Languages)

> *Java*  
> ```java
> public int largestRectangleArea(int[] heights) { … }
> ```

> *Python*  
> ```python
> def largest_rectangle_area(self, heights: List[int]) -> int: …
> ```

> *C++*  
> ```cpp
> int largestRectangleArea(vector<int>& heights) { … }
> ```

*(Full code above – see Section 1.)*

---

### 2.8 H2 – Time & Space Complexity

- **Time**: `O(n)` – every index is pushed/popped once.  
- **Space**: `O(n)` – stack can grow to `n` in worst case.  
- **Constant factors** are small: a few integer operations per iteration.

---

### 2.9 H2 – Interview Tips

1. **Explain the stack invariant**: “The stack holds indices of increasing heights; this guarantees we know the left boundary for any popped bar.”  
2. **Walk through a small example**: `[2,1,5,6,2,3]` – show pushes/pops and area calculations.  
3. **Mention edge cases** you tested – all zeros, increasing, decreasing.  
4. **Ask clarifying questions**: e.g., “Are heights guaranteed non‑negative?” – shows interviewers you care about constraints.  
5. **Mention optimizations**: avoid extra loops; use sentinel to simplify width calculation.

---

### 2.10 H2 – Takeaways

- **Monotonic stacks** are a powerful tool for 1‑D histogram problems.  
- Avoid common off‑by‑one bugs with a sentinel.  
- Even in a “Hard” problem, a single‑pass stack gives the optimal solution.  
- The same pattern applies to other LeetCode challenges: *Largest Rectangle in a Grid*, *Largest Rectangle of 1s*, etc.

---

### 2.11 H2 – Call to Action

> **Ready to ace your next interview?**  
> - Practice this solution on LeetCode, HackerRank, and your own projects.  
> - Share your solutions on GitHub with the tag `#largest-rectangle-histogram`.  
> - Subscribe for more interview‑prep walkthroughs and video explanations.  

> **Let’s code, interview, and land that dream job together!**

---

### 2.12 H2 – References & Further Reading

1. LeetCode: [Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)
2. YouTube: [Video Explanation – Niits38862Jun 27, 2024](https://www.youtube.com/watch?v=...)
3. Blog Post: *Monotonic Stack – A Deep Dive* (https://example.com/monotonic-stack)
4. GitHub Repo: `https://github.com/yourhandle/leetcode-84` – Full implementations & tests

---

> **SEO Keywords**: “Largest Rectangle in Histogram”, “LeetCode 84 solution”, “monotonic stack algorithm”, “O(n) histogram rectangle”, “Java Python C++ interview”, “data structure interview prep”, “get a job coding interview”, “how to solve hard problems”

---

Happy coding! 🚀