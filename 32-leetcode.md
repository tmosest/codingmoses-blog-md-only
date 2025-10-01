---
title: LeetCode 32. Longest Valid Parentheses - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣ The Problem – “Longest Valid Parentheses” (Hard)

Given a string `s` that contains only the characters `(` and `)`, find the length of the longest substring that is a **valid (well‑formed) parentheses** expression.  
A valid expression is one where every opening parenthesis has a corresponding closing parenthesis and the pairs are properly nested.

> **Examples**  
> • `s = "(()"` → **Output:** `2` (the substring `"()"`)  
> • `s = ")()())"` → **Output:** `4` (the substring `"()()"`)  
> • `s = ""` → **Output:** `0`

| Constraint | Value |
|------------|-------|
| `0 <= s.length <= 3 * 10⁴` | ✔️ |
| `s[i] ∈ {'(', ')'}` | ✔️ |

---

## 2️⃣ Multiple Winning Solutions

Below you’ll find three fully‑worked solutions – one each in **Java, Python, and C++** – that solve the problem in **O(n) time** and **O(n) space** (the stack‑based solution) or **O(1) space** (the two‑pass scan).  
Pick the one that best matches your interview style or the language you’re most comfortable with.

### 2.1 🟦 Java – Stack Approach

```java
// Java 17
public class Solution {
    public int longestValidParentheses(String s) {
        // The stack holds indices; a sentinel -1 marks the base
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(-1);           // sentinel
        int maxLen = 0;

        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                stack.push(i);
            } else {
                // Pop the matching '('
                stack.pop();
                if (stack.isEmpty()) {
                    // No matching '(', push current index as new base
                    stack.push(i);
                } else {
                    // Current valid length is i - stack.peek()
                    maxLen = Math.max(maxLen, i - stack.peek());
                }
            }
        }
        return maxLen;
    }
}
```

### 2.2 🟨 Python – Two‑Pass Scan (O(1) Space)

```python
# Python 3.11
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        left = right = max_len = 0
        # Left to right: count '(' and ')'
        for c in s:
            if c == '(':
                left += 1
            else:   # c == ')'
                right += 1
            if left == right:
                max_len = max(max_len, 2 * right)
            elif right > left:
                left = right = 0

        # Right to left: reset counters
        left = right = 0
        for c in reversed(s):
            if c == '(':
                left += 1
            else:  # c == ')'
                right += 1
            if left == right:
                max_len = max(max_len, 2 * left)
            elif left > right:
                left = right = 0
        return max_len
```

### 2.3 🟥 C++ – Stack Approach

```cpp
// C++17
class Solution {
public:
    int longestValidParentheses(string s) {
        vector<int> stack;
        stack.push_back(-1);            // sentinel
        int maxLen = 0;

        for (int i = 0; i < (int)s.size(); ++i) {
            if (s[i] == '(') {
                stack.push_back(i);
            } else {
                stack.pop_back();
                if (stack.empty()) {
                    stack.push_back(i); // new base
                } else {
                    maxLen = max(maxLen, i - stack.back());
                }
            }
        }
        return maxLen;
    }
};
```

---

## 3️⃣ The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | O(n) – passes each char once. | None | N/A |
| **Space Complexity** | O(n) (stack) or O(1) (two‑pass). | O(n) for the stack if you don’t bother optimizing. | Recursion depth can blow the stack in naive solutions. |
| **Readability** | Stack solution is straightforward, but the two‑pass scan is clever. | The two‑pass logic can be confusing for interviewees. | Recursive DP (not shown) is harder to debug and harder to explain. |
| **Edge Cases** | Empty string, all opening or closing parentheses handled by sentinel. | Off‑by‑one errors if you forget sentinel. | Failing to reset counters when scanning right‑to‑left leads to wrong results. |
| **Memory Footprint** | Stack size ≈ number of unmatched '(' – worst‑case n. | Using a vector/Deque is fine in Java/C++; Python list works. | None of the shown solutions use excessive memory. |

---

## 4️⃣ SEO‑Optimized Blog Post

> **Title:** “Master the LeetCode ‘Longest Valid Parentheses’ – Java, Python & C++ Solutions + Interview Secrets”

---

### 4.1 Introduction (Why This Problem Matters)

> The **Longest Valid Parentheses** problem is a staple on LeetCode’s *Hard* list and a frequent interview question at top tech companies like Google, Microsoft, Amazon, and Meta. Solving it showcases your **string manipulation**, **dynamic programming**, and **stack** knowledge—skills recruiters look for when hiring senior software engineers.

---

### 4.2 Problem Recap (Keep It Short & Sweet)

- Input: String `s` of only `(` and `)`.  
- Output: Integer, length of the longest well‑formed parentheses substring.  
- Constraints: `0 <= s.length <= 30,000`.

---

### 4.3 Common Pitfalls (What to Avoid)

1. **Miscounting parentheses** – Always pair `(` with a matching `)`.  
2. **Missing the sentinel** – Without `-1`, `stack.isEmpty()` can throw an exception.  
3. **Wrong direction** – Some solutions only scan left‑to‑right; they miss cases like `"(()"`.

---

### 4.4 Solution Breakdown

#### 4.4.1 Stack Solution (O(n) time, O(n) space)

- **Idea:** Treat indices like a stack.  
- **Why it works:** The top of the stack always stores the index of the last unmatched parenthesis or a sentinel. When a closing parenthesis matches, the distance between the current index and the new stack top is the length of a valid substring.

#### 4.4.2 Two‑Pass Scan (O(n) time, O(1) space)

- **Idea:** Count `(` and `)` while sweeping left→right and then right→left.  
- **Why it works:**  
  - **Left→Right:** Count pairs until `right > left` → reset.  
  - **Right→Left:** Count pairs until `left > right` → reset.  
  - The maximum of both passes gives the answer.

---

### 4.5 Code Examples

> **Java** – Stack  
> ```java
> public int longestValidParentheses(String s) { … }
> ```
>  
> **Python** – Two‑Pass  
> ```python
> class Solution:
>     def longestValidParentheses(self, s: str) -> int: …
> ```
>  
> **C++** – Stack  
> ```cpp
> class Solution {
> public:
>     int longestValidParentheses(string s) { … }
> };
> ```

(Full code blocks are provided above.)

---

### 4.6 Time & Space Complexity

| Language | Time | Space |
|----------|------|-------|
| Java | **O(n)** | **O(n)** |
| Python | **O(n)** | **O(1)** |
| C++ | **O(n)** | **O(n)** |

---

### 4.7 How to Explain It in an Interview

1. **Start with intuition:** “We’ll walk from left to right, keeping track of indices of unmatched parentheses.”  
2. **Show the stack idea:** “When we see a `(`, push its index; on a `)`, pop and compute the length.”  
3. **Edge cases:** “If the stack becomes empty, we push the current index as a new base.”  
4. **Finish with complexity:** “Linear time, linear space.”

---

### 4.8 Advanced Tips for Job Seekers

- **Mention real‑world analogies:** “Just like parsing code or matching tags in HTML, this problem ensures you can handle nested structures.”  
- **Show the O(1) space trick:** “If a recruiter asks for memory optimization, bring up the two‑pass approach.”  
- **Practice variant problems:** `Valid Parentheses`, `Regular Expression Matching`, and `Longest Palindromic Substring` reinforce the same patterns.

---

### 4.9 Takeaway

Mastering **Longest Valid Parentheses** proves you can:

- Handle **stack** data structures efficiently.  
- Optimize for **time** and **space** trade‑offs.  
- Think about **edge cases** and **sentinel values**—critical in production code.

---

### 4.10 Ready for the Next Interview?

Download the code snippets, run your own test cases, and practice explaining each step aloud. A polished explanation, backed by solid code, will turn interviewers into hiring managers. Good luck!

---

> **Keywords:** Longest Valid Parentheses, LeetCode Hard, Java Stack, Python Two‑Pass, C++ Solution, Interview Prep, Coding Interview, Software Engineer Interview, Data Structures, Algorithm Analysis, O(n) time, O(1) space, Tech Companies, Job Interview Tips.