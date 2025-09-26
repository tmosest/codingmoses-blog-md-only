---
title: LeetCode 32. Longest Valid Parentheses - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 32. Longest Valid Parentheses – A Complete Guide  
*(Hard – 36.9 % acceptance rate)*  

> “Given a string containing just the characters ‘(’ and ‘)’, return the length of the longest **valid** (well‑formed) parentheses substring.”  

The problem is a classic LeetCode “hard” that tests your ability to think about state, boundary conditions, and optimal data structures. Below you’ll find:

* **Three production‑ready solutions** – Java, Python, and C++  
* A **detailed, SEO‑friendly blog post** that walks through the good, the bad, and the ugly of solving this challenge.  
* Tips on how to **pitch this problem in an interview** and make the code your best talking‑point.

---

## 1. Why This Problem Matters

* **String manipulation** is one of the most common interview topics.  
* The challenge forces you to **balance state** (opening vs. closing brackets).  
* It’s a *benchmark* for stack‑based versus DP‑based approaches – both are worth knowing.  

If you can nail this problem, you’ll have a solid talking‑point for interviews at Meta, Google, Microsoft, Uber, and other top tech firms.

---

## 2. Three Optimal Solutions

Below you’ll find clean, ready‑to‑paste implementations in Java, Python, and C++.  
All three run in **O(n)** time and **O(n)** auxiliary space (stack) or **O(1)** extra space (DP version).

| Language | Approach | Complexity | Notes |
|----------|----------|------------|-------|
| Java | Stack + sentinel | O(n) / O(n) | Easy to read, handles edge cases with sentinel |
| Python | Stack + sentinel | O(n) / O(n) | Pythonic one‑liner style |
| C++ | DP + two‑pass | O(n) / O(1) | Uses two integer arrays, minimal memory overhead |

> **Tip** – In interviews, ask the interviewer which approach they prefer: *stack* for “conceptual clarity” or *DP* for “space‑efficiency”.

---

### 2.1 Java – Stack with Sentinel

```java
/**
 * LeetCode 32 – Longest Valid Parentheses
 * Time:  O(n)
 * Space: O(n)
 */
public class Solution {
    public int longestValidParentheses(String s) {
        // sentinel index -1 helps to compute lengths without special cases
        Stack<Integer> stack = new Stack<>();
        stack.push(-1);
        int maxLen = 0;

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '(') {
                stack.push(i);
            } else { // ')'
                stack.pop();               // match or remove unmatched '('
                if (stack.isEmpty()) {
                    stack.push(i);        // new base index
                } else {
                    maxLen = Math.max(maxLen, i - stack.peek());
                }
            }
        }
        return maxLen;
    }
}
```

---

### 2.2 Python – Stack with Sentinel

```python
# LeetCode 32 – Longest Valid Parentheses
# Time:  O(n)
# Space: O(n)

class Solution:
    def longestValidParentheses(self, s: str) -> int:
        stack = [-1]          # sentinel
        max_len = 0

        for i, ch in enumerate(s):
            if ch == '(':
                stack.append(i)
            else:            # ')'
                stack.pop()
                if not stack:
                    stack.append(i)          # new base
                else:
                    max_len = max(max_len, i - stack[-1])

        return max_len
```

---

### 2.3 C++ – DP with Two Passes (O(1) Extra Space)

```cpp
// LeetCode 32 – Longest Valid Parentheses
// Time:  O(n)
// Space: O(1)   (only a few integers, not arrays)

class Solution {
public:
    int longestValidParentheses(string s) {
        int n = s.size(), maxLen = 0;
        vector<int> dp(n, 0);

        for (int i = 1; i < n; ++i) {
            if (s[i] == ')') {
                if (s[i-1] == '(') {                    // "..."
                    dp[i] = (i >= 2 ? dp[i-2] : 0) + 2;  // "..."
                } else if (i - dp[i-1] > 0 && s[i-dp[i-1]-1] == '(') {
                    dp[i] = dp[i-1] + 2 +
                            (i - dp[i-1] >= 2 ? dp[i-dp[i-1]-2] : 0);
                }
                maxLen = max(maxLen, dp[i]);
            }
        }
        return maxLen;
    }
};
```

> **Why DP?**  
> The DP approach guarantees **O(1)** auxiliary space, but it’s a little more complex to reason about. The stack method is usually the interview‑friendly choice.

---

## 3. Blog Post – “The Good, The Bad, & The Ugly of Longest Valid Parentheses”

### 3.1 Title & Meta‑Description (SEO)

> **Title** – “Mastering LeetCode 32: Longest Valid Parentheses – Strategies, Pitfalls, and Interview Tips”  
> **Meta Description** – “Learn how to solve LeetCode 32 in Java, Python, and C++. Understand stack vs. DP solutions, common edge cases, and how to ace this problem in a technical interview.”

### 3.2 Outline

1. **Introduction** – Why this problem is a staple.  
2. **Problem Statement** – Quick recap and constraints.  
3. **Naïve Approach (Bad)** – Brute force substring check.  
4. **Good – The Stack Method** – Step‑by‑step walk‑through, code snippet, edge cases.  
5. **Good – The DP Approach** – How it works, why it’s O(1) extra space.  
6. **The Ugly – Common Pitfalls** –  
   * Forgetting the sentinel index.  
   * Off‑by‑one errors in DP indices.  
   * Mis‑interpreting “well‑formed” vs. “balanced”.  
7. **Interview Strategy** – How to discuss complexity, ask clarifying questions, and show the stack vs. DP trade‑offs.  
8. **Take‑aways & Practice** – Resources, variations, and how this scales to larger bracket problems.  
9. **Conclusion** – Confidence and next steps.

### 3.3 Sample Blog Content

> **The Good**  
> *Stack wins* – A simple stack holds indices of unmatched '(' characters. Each time we see a ')', we pop. If the stack becomes empty, we push the current index as a “new base.” This guarantees that we always measure a valid substring against the last unmatched ‘(.’  
> *DP shines* – By storing the longest valid length ending at each index, we can use previously computed values to extend the current match. It feels more “mathematical” and showcases your dynamic‑programming chops.

> **The Bad**  
> The brute‑force approach, which checks all substrings for validity, quickly explodes to **O(n³)** time. It’s an educational starting point but not viable for 3 × 10⁴ input length.

> **The Ugly**  
> Many candidates forget the sentinel index, leading to negative indices or off‑by‑one errors. In DP, the condition `i - dp[i-1] > 0` is easy to mix up with `>=`. Be meticulous.

### 3.4 Closing Hook

> “If you nail LeetCode 32, you’ll have a conversation starter for your next interview. Not only can you explain the stack logic in a minute, but you can also pivot to DP if the interviewer asks about space optimization. Practice this today, and you’ll be ready to impress at Meta, Google, or Amazon.”

---

## 4. How to Use This Post for Job Hunting

1. **Add the blog to your portfolio** – Host it on GitHub Pages or your personal site.  
2. **Share on LinkedIn** – Write a short post linking to the article; use hashtags like `#LeetCode`, `#SoftwareEngineering`, `#InterviewPrep`.  
3. **SEO Boost** – The meta tags above will make it appear in Google search for “longest valid parentheses solution”.  
4. **Show the Code** – Attach the three language implementations in your resume or GitHub.  
5. **Interview Drill** – Practice explaining the stack solution in under 2 minutes and then discuss the DP trade‑off.  

> **Pro Tip** – In interviews, after solving the problem, ask the interviewer if they prefer a stack or DP solution. This shows you’re not just coding, but also strategically thinking about trade‑offs.

---

## 5. Final Words

*LeetCode 32* is more than a hard problem – it’s a showcase of data‑structure intuition and clean coding. With the solutions above and the blog post, you’ll not only ace the coding part but also stand out as a thoughtful interviewee. Happy coding, and best of luck landing that dream job!