---
title: LeetCode 224. Basic Calculator - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧮 224. Basic Calculator – Hard – Java / Python / C++ Solutions + SEO‑Optimized Blog Post

Below you’ll find **three complete implementations** of LeetCode 224 – *Basic Calculator* – one each in Java, Python and C++.  
After the code you’ll find a detailed, SEO‑friendly blog article that covers the good, the bad, and the ugly of this classic interview problem.

---

### 1. Java – Stack based (Iterative)

```java
import java.util.Stack;

public class Solution {
    public int calculate(String s) {
        Stack<Integer> stack = new Stack<>();
        int result = 0;   // running total
        int num = 0;      // current number being built
        int sign = 1;     // +1 or -1

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);

            if (Character.isDigit(c)) {
                // Build the current number (handles multi‑digit)
                num = num * 10 + (c - '0');
            } else if (c == '+') {
                result += sign * num;
                num = 0;
                sign = 1;
            } else if (c == '-') {
                result += sign * num;
                num = 0;
                sign = -1;
            } else if (c == '(') {
                // Push the current state onto the stack
                stack.push(result);
                stack.push(sign);
                // Reset for the sub‑expression
                result = 0;
                sign = 1;
            } else if (c == ')') {
                result += sign * num;   // finish the inner number
                num = 0;
                int prevSign = stack.pop();   // sign before '('
                int prevResult = stack.pop(); // result before '('
                result = prevResult + prevSign * result;
            } else if (c == ' ') {
                // skip whitespace
                continue;
            }
        }

        // Add any remaining number
        if (num != 0) {
            result += sign * num;
        }
        return result;
    }
}
```

> **Why this works:**  
> The stack stores the *context* before every opening parenthesis: the cumulative result up to that point and the sign that applies to the entire sub‑expression inside the parentheses. When we hit a closing parenthesis, we pop those two values and combine them with the result of the sub‑expression.

---

### 2. Python – Stack based (Iterative)

```python
class Solution:
    def calculate(self, s: str) -> int:
        stack = []
        result = 0
        num = 0
        sign = 1  # +1 or -1

        for ch in s:
            if ch.isdigit():
                num = num * 10 + int(ch)
            elif ch == '+':
                result += sign * num
                num = 0
                sign = 1
            elif ch == '-':
                result += sign * num
                num = 0
                sign = -1
            elif ch == '(':
                stack.append(result)
                stack.append(sign)
                result = 0
                sign = 1
            elif ch == ')':
                result += sign * num
                num = 0
                prev_sign = stack.pop()
                prev_result = stack.pop()
                result = prev_result + prev_sign * result
            # ignore spaces

        if num:
            result += sign * num
        return result
```

---

### 3. C++ – Stack based (Iterative)

```cpp
#include <string>
#include <stack>
using namespace std;

class Solution {
public:
    int calculate(string s) {
        stack<int> st;      // stores previous result and sign
        int result = 0;     // running total
        int num = 0;        // current number
        int sign = 1;       // +1 or -1

        for (char c : s) {
            if (isdigit(c)) {
                num = num * 10 + (c - '0');
            } else if (c == '+') {
                result += sign * num;
                num = 0;
                sign = 1;
            } else if (c == '-') {
                result += sign * num;
                num = 0;
                sign = -1;
            } else if (c == '(') {
                st.push(result);
                st.push(sign);
                result = 0;
                sign = 1;
            } else if (c == ')') {
                result += sign * num;
                num = 0;
                int prevSign = st.top(); st.pop();
                int prevResult = st.top(); st.pop();
                result = prevResult + prevSign * result;
            }
            // ignore spaces
        }

        if (num != 0) result += sign * num;
        return result;
    }
};
```

---

## 🎓 Blog Post – “The Good, The Bad, and The Ugly of LeetCode 224: Basic Calculator”

### 1. Introduction

> **LeetCode 224 – Basic Calculator (Hard)**  
> The problem asks you to evaluate a simple arithmetic expression that contains non‑negative integers, `+`, `-`, parentheses, and spaces.  
> **Why it matters:**  
> •  It’s a classic *stack* interview question that tests your ability to handle operator precedence and nested structures.  
> •  Many tech‑companies (Google, Amazon, Microsoft, Meta, etc.) use variants of this problem to gauge candidates’ data‑structure knowledge and mental math speed.  
> •  Writing a clean, efficient solution demonstrates mastery of iterative logic, recursion, and error handling—qualities highly prized on your résumé.

### 2. Problem Statement (SEO Keywords)

- **Basic Calculator** – LeetCode 224
- **Hard** – coding interview challenge
- **Stack based solution**
- **Java, Python, C++** – language‑specific code
- **Arithmetic expression evaluation** – no `eval()`

> Input: `s = "1 + 1"` → Output: `2`  
> Input: `s = "(1+(4+5+2)-3)+(6+8)"` → Output: `23`

### 3. Constraints

| Constraint | Reason |
|------------|--------|
| `1 <= s.length <= 3 * 10^5` | Handles large expressions |
| `s` contains only digits, `+`, `-`, `(`, `)`, and space | Simple grammar |
| No unary `+` | Clarifies that `+1` is invalid |
| Unary `-` allowed | Handles expressions like `-1` or `-(2+3)` |
| No consecutive operators | Guarantees a valid syntax |
| 32‑bit signed integer fits | No overflow concerns |

### 4. The Good – What Makes This Problem Elegant

- **Deterministic Grammar:** No ambiguous operator precedence beyond parentheses and binary `+`/`-`.  
- **Stack Suitability:** Each parenthesis pair naturally maps to a push/pop, keeping the algorithm O(n).  
- **Single Pass:** We can solve the problem in one linear scan, which is a big win for interviewers.  
- **Scalable:** Works for huge inputs (`3*10^5` chars) without recursion depth issues.  
- **Language Agnostic:** The stack logic is identical in Java, Python, C++.

### 5. The Bad – Common Pitfalls and Mistakes

| Pitfall | How to Avoid |
|---------|--------------|
| **Forgot to add the last number** | After the loop, add `sign * num` if `num` is non‑zero. |
| **Mis‑ordering stack push/pop** | Push *result* first, then *sign*. When popping, reverse the order. |
| **Assuming single‑digit numbers** | Build numbers by `num = num*10 + (c - '0')`. |
| **Using recursion for `()`** | Recursion depth may hit stack overflow for deeply nested parentheses. |
| **Treating spaces incorrectly** | Skip `' '` characters, don't treat them as operators. |

### 6. The Ugly – Edge Cases That Trip Candidates

1. **Unary minus at the start**: `"-1+2"`  
   - The algorithm must treat the leading `-` as a sign for the first number.
2. **Negative parentheses**: `"- (1+2)"`  
   - The space after `-` is harmless, but the algorithm must still push the correct sign onto the stack.
3. **Multiple nested parentheses**: `"((1+2)+(3+4))"`  
   - Ensure the stack never underflows; each `(` must have a matching `)`.
4. **Large numbers**: `"123456789+987654321"`  
   - Use `int` (32‑bit) but be careful that intermediate sums stay within bounds (they do, as per constraints).

### 7. Algorithm Walkthrough (Stack‑Based)

1. **Initialize**: `result = 0`, `num = 0`, `sign = 1`, empty stack.
2. **Scan each character**:
   - **Digit** → build `num`.
   - **`+` / `-`** → apply `sign * num` to `result`, reset `num`, set new `sign`.
   - **`(`** → push current `result` and `sign`, reset them (`result = 0`, `sign = 1`).
   - **`)`** → finish the inner number, multiply by the sign popped from stack, then add to the outer result.
3. **After loop**: Add any remaining number to `result`.

This algorithm runs in **O(n)** time and uses **O(n)** stack space in the worst case (fully nested parentheses). The constant factor is very small—only a handful of integer variables and stack operations.

### 8. Code Samples

*Full source code for Java, Python, and C++ is provided above.*  
All three share the same logic; just the syntax changes.

### 9. Complexity Analysis

| Language | Time | Space |
|----------|------|-------|
| Java | `O(n)` | `O(n)` |
| Python | `O(n)` | `O(n)` |
| C++ | `O(n)` | `O(n)` |

> **Why is this acceptable?**  
> Interviewers look for linear algorithms for hard problems. Recursion would have added extra call overhead and risked stack overflow.

### 10. How to Shine on Your Résumé

- **Add a “Coding Interview” badge** next to the Basic Calculator problem on your GitHub or personal blog.  
- **Highlight “O(n) stack solution”** in your project README or LinkedIn posts.  
- **Mention multi‑language implementations** (Java, Python, C++) to show adaptability.  
- **Provide a time‑measure snippet** to show that your solution runs under 1 ms for `3*10^5` characters on typical hardware.

### 10. Final Takeaway

> The “Basic Calculator” problem is a *beautiful* example of how a complex parsing problem can be distilled into simple stack operations.  
> By mastering the stack‑based approach and anticipating edge cases, you not only nail this LeetCode question but also demonstrate a depth of understanding that recruiters look for in senior software engineers.  

**Good luck on your next interview!** 🚀

--- 

**Meta Note**: The above blog content can be used verbatim on personal blogs, Medium, dev.to, or even in LinkedIn posts. It’s packed with SEO‑friendly headings, keyword‑rich paragraphs, and practical code snippets—perfect for boosting your visibility and attracting recruiters.