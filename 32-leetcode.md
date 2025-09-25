---
title: LeetCode 32. Longest Valid Parentheses - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap  
**Longest Valid Parentheses** – LeetCode #32  
*Hard*

> **Input:** A string `s` consisting only of the characters `'('` and `')'`.  
> **Output:** The length of the longest substring that forms a *valid* (well‑formed) sequence of parentheses.

> **Examples**  
> ```text
> "(()"     → 2   // "()"
> ")()())"  → 4   // "()()"
> ""        → 0
> ```

> **Constraints**  
> * `0 ≤ s.length ≤ 3·10⁴`  
> * `s[i]` is `'('` or `')'`

---

## 2.  Solution Overview  

| Approach | Time | Space | Pros | Cons |
|----------|------|-------|------|------|
| Brute‑force (check all substrings) | O(n³) | O(1) | Very simple | Too slow for n=30 000 |
| Stack (indices) | O(n) | O(n) | Linear, easy to implement | Requires extra memory |
| Dynamic Programming | O(n) | O(n) | Linear, no stack | Slightly more complex logic |
| Two‑pass scan (left → right & right → left) | O(n) | O(1) | Constant space | Needs careful counting logic |

The two‑pass scan is the *most* efficient in terms of memory, but all linear‑time solutions are acceptable for interview purposes.

Below we provide the canonical **stack** implementation (clear, idiomatic, and works for all edge cases) in **Java, Python, and C++**. The dynamic‑programming version is also shown as an optional alternative.

---

## 3.  Stack‑Based Implementation  

### 3.1  Idea  
Use a stack to keep indices of **unmatched** parentheses.  
* Push `-1` onto the stack as a dummy base.  
* For each `(` – push its index.  
* For each `)` – pop one element.  
  * If the stack becomes empty → push current index (new base).  
  * Else → current length = `i - stack.peek()`. Keep the maximum.

This works because the stack always holds the index of the last unmatched `(` or the last unmatched `)` that started a new candidate segment.

### 3.2  Code  

#### Java

```java
import java.util.Stack;

public class Solution {
    public int longestValidParentheses(String s) {
        // Edge case
        if (s == null || s.length() == 0) return 0;

        Stack<Integer> stack = new Stack<>();
        stack.push(-1);          // base for the first valid substring
        int maxLen = 0;

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '(') {
                stack.push(i);
            } else { // c == ')'
                if (!stack.isEmpty()) stack.pop();

                if (stack.isEmpty()) {
                    stack.push(i);          // new base
                } else {
                    maxLen = Math.max(maxLen, i - stack.peek());
                }
            }
        }
        return maxLen;
    }
}
```

#### Python

```python
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        if not s:
            return 0

        stack = [-1]          # base index
        max_len = 0

        for i, ch in enumerate(s):
            if ch == '(':
                stack.append(i)
            else:  # ch == ')'
                stack.pop()            # remove matching '(' if exists
                if not stack:
                    stack.append(i)   # new base
                else:
                    max_len = max(max_len, i - stack[-1])
        return max_len
```

#### C++

```cpp
class Solution {
public:
    int longestValidParentheses(string s) {
        if (s.empty()) return 0;
        stack<int> st;
        st.push(-1);           // base index
        int maxLen = 0;

        for (int i = 0; i < (int)s.size(); ++i) {
            if (s[i] == '(') {
                st.push(i);
            } else { // ')'
                if (!st.empty()) st.pop();
                if (st.empty()) {
                    st.push(i);           // new base
                } else {
                    maxLen = max(maxLen, i - st.top());
                }
            }
        }
        return maxLen;
    }
};
```

---

## 4.  Alternative: Dynamic Programming  

When you want a *pure* DP solution, consider `dp[i]` = longest valid substring ending at index `i`.

```java
int[] dp = new int[s.length()];
int max = 0;
for (int i = 1; i < s.length(); i++) {
    if (s.charAt(i) == ')') {
        if (s.charAt(i-1) == '(') {
            dp[i] = 2 + (i >= 2 ? dp[i-2] : 0);
        } else if (i - dp[i-1] > 0 && s.charAt(i-dp[i-1]-1) == '(') {
            dp[i] = dp[i-1] + 2 + (i - dp[i-1] - 2 >= 0 ? dp[i-dp[i-1]-2] : 0);
        }
        max = Math.max(max, dp[i]);
    }
}
return max;
```

Both stack and DP run in `O(n)` time with `O(n)` space (stack) or `O(n)` auxiliary memory (DP). The two‑pass scan can drop space to `O(1)`.

---

## 5.  Common Pitfalls & Edge Cases  

| Pitfall | Fix |
|---------|-----|
| Forgetting the dummy `-1` in stack | Push `-1` initially to compute lengths correctly. |
| Using `StringBuilder` or character arrays incorrectly | Use `charAt(i)` for constant‑time access. |
| Mis‑handling empty string | Early return `0`. |
| Off‑by‑one in DP transition | Double‑check indices `i-2`, `i-dp[i-1]-2`. |
| Using recursion or DFS – stack overflow | Stick to iterative O(n) algorithms. |

---

## 6.  Interview‑Ready Tips  

1. **Explain the intuition first.** Talk about “matching pairs” and “unmatched bases”.
2. **Show a simple example** on the whiteboard (e.g., `")()())"`).
3. **Mention time & space trade‑offs.** Stack: O(n) space; two‑pass: O(1).
4. **Be ready to discuss the DP approach** if the interviewer wants a pure DP solution.
5. **Edge‑case tests**: empty string, all `'('`, all `')'`, alternating pattern, long strings.

---

## 7.  Blog Article: *Longest Valid Parentheses – The Good, The Bad, and The Ugly*

> **SEO Keywords:**  
> *Longest Valid Parentheses, LeetCode 32, coding interview, stack algorithm, dynamic programming, parentheses problem, interview prep, Java, Python, C++ solutions, job interview coding, algorithm explanation, time complexity, space complexity, interview tips*

### 7.1  Introduction  

If you’re hunting for a software engineering role, one of the first questions you’ll encounter is **“Find the longest valid parentheses substring”**. It’s deceptively simple to write a brute‑force solution, but it quickly turns into a nightmare for interviewers who want *efficient*, *clever*, and *well‑documented* code.

In this article we’ll dissect the problem, explore the most efficient solutions, highlight common pitfalls, and share interview‑specific insights that will impress recruiters and get you the job offer.

> **TL;DR**: Use a stack (or two‑pass scan) to solve the problem in O(n) time and O(n) (or O(1)) space. Avoid the O(n³) brute‑force approach.

---

### 7.2  The Problem (Re‑Stated)

Given a string `s` of length up to 30 000 that contains only `'('` and `')'`, return the length of the longest contiguous substring that is a *valid* parentheses sequence (every opening has a matching closing, and the order is correct).

> **Examples**  
> - `"(()"` → `2` (`"()"`)  
> - `")()())"` → `4` (`"()()"`)  
> - `""` → `0`

---

### 7.3  The Good: What Works

| Algorithm | Why it’s Good |
|-----------|---------------|
| **Stack** | Intuitive, linear, handles all edge cases. Keeps track of unmatched indices; easy to explain on paper. |
| **Two‑pass scan** | O(n) time, O(1) space. Perfect for interviewers who want you to show space‑optimization. |
| **Dynamic Programming** | Shows you can think beyond data structures; demonstrates understanding of sub‑problems. |

#### Stack Walk‑Through

1. Push `-1` as a base.
2. For each `(` push its index.
3. For each `)` pop once.
   - If stack empty → push current index (new base).
   - Else → compute `currentLen = i - stack.peek()`.

The stack always contains the indices of unmatched parentheses or the index before the start of the current valid substring. That subtlety is the key to constant‑time length computation.

#### Two‑Pass Scan

1. Scan left → right, count `(` and `)`; whenever `)` > `(`, reset counters.
2. Scan right → left, similar logic; swap roles of `(` and `)`.

This trick removes the need for a stack entirely, making the solution truly space‑efficient.

---

### 7.4  The Bad: Common Mistakes

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| Using a *sentinel* wrong (e.g., `0` instead of `-1`) | Off‑by‑one length errors | Start with `-1` to represent “no character”. |
| Forgetting to handle `s.empty()` | NullPointer or IndexOutOfBounds | Early return `0`. |
| Mis‑computing DP indices | Wrong substring length | Always guard against negative indices (`i >= 2 ? dp[i-2] : 0`). |
| Trying to use recursion for `n=30k` | Stack overflow | Use iterative methods. |

---

### 7.5  The Ugly: Over‑Optimization and Complexity

Sometimes candidates go overboard with micro‑optimizations or obscure data structures:

- **Bit‑masking**: Trying to pack indices into bits gives you nothing because you still need to know *which* indices are unmatched.
- **Complex custom data structures**: A fancy deque that removes the need for the sentinel adds cognitive load for the interviewer.
- **Early termination heuristics**: “Break when length > X” is wrong; you don’t know X until you compute.

**Takeaway**: In interviews, *clarity* trumps *trickiness*. A clean stack or two‑pass solution shows you understand the problem deeply and can code it cleanly.

---

### 7.6  Interview‑Ready Checklist

1. **State the problem in your own words.**  
   *“We need the longest well‑formed parentheses substring.”*

2. **Outline naive approach (optional).**  
   *“Brute force O(n³) – check every substring.”*  
   *“Not acceptable for 30k.”*

3. **Propose efficient solution** – pick stack or two‑pass.  
   *Explain the sentinel trick and length calculation.*

4. **Write the code on the board.**  
   Keep it short, but include comments.

5. **Discuss complexity.**  
   *Time:* `O(n)`  
   *Space:* `O(n)` (stack) or `O(1)` (two‑pass)

6. **Edge Cases**  
   *Empty string, all opening, all closing, alternating patterns.*

7. **Optional DP version** – show you can adapt to other styles.

8. **Ask the interviewer**  
   *“Do you prefer a space‑optimized version?”*

---

### 7.7  Final Thought

The **Longest Valid Parentheses** problem is a classic test of your ability to translate a real‑world constraint (matching parentheses) into a data‑structure or algorithmic strategy. Mastering the stack approach not only gives you a solid answer for this problem but also reinforces a pattern that shows up in many interview questions: *“Track unmatched elements”*.

By presenting a clean, well‑commented solution and being prepared to discuss time/space trade‑offs, you’ll demonstrate both coding proficiency and problem‑solving insight – exactly what recruiters are looking for.

> **Good luck!**  
> You’ve got the algorithm, now just code it, explain it, and let your enthusiasm shine.

---

## 8.  Bonus: Quick One‑Liner Solutions (Python)  

```python
# 2-pass scan (O(n), O(1) space)
def longestValidParentheses(s):
    left = right = max_len = 0
    for c in s:
        if c == '(':
            left += 1
        else:
            right += 1
            if left == right:
                max_len = max(max_len, 2 * right)
            elif right > left:
                left = right = 0
    left = right = 0
    for c in reversed(s):
        if c == ')':
            left += 1
        else:
            right += 1
            if left == right:
                max_len = max(max_len, 2 * left)
            elif right > left:
                left = right = 0
    return max_len
```

Feel free to drop this into your interview notes – it’s a handy trick to have on hand.

---

### 9.  TL;DR

| Language | Stack Implementation |
|----------|----------------------|
| **Java** | See section 3.2 |
| **Python** | See section 3.3 |
| **C++** | See section 3.4 |

> The stack solution runs in **O(n)** time and uses **O(n)** extra space (or **O(1)** if you prefer the two‑pass trick).  
> Good luck landing that dream job!