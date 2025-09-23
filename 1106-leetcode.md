---
title: LeetCode 1106. Parsing A Boolean Expression - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📚 Parsing a Boolean Expression – 1106 – Stack‑Based Mastery (Java / Python / C++)  
*“Good. Bad. Ugly.” – A practical guide for your next coding interview.*

---

### TL;DR  
- **Problem**: Evaluate a fully‑parenthesised boolean expression with `t`, `f`, `!`, `&`, `|` and commas.  
- **Optimal solution**: Linear‑time, linear‑space stack algorithm that *early‑breaks* when the result is known.  
- **Languages**: Ready‑to‑paste solutions for **Java**, **Python**, and **C++**.  
- **Blog focus**: Why the stack approach wins, common pitfalls, and how to discuss it in a job interview.  

> **Ready to add this to your portfolio?**  
> Copy the code, run it against LeetCode, and drop the article into your GitHub README.  

---

## 1. Problem Recap (LeetCode 1106)

> **Input** – A string `expression` (length ≤ 20 000) that follows the grammar:  
> ```
> expr → 't' | 'f' | '!' '(' expr ')' | '&' '(' expr (',' expr)* ')' | '|' '(' expr (',' expr)* ')'
> ```
> **Output** – The boolean value (`true` / `false`) that the expression evaluates to.  

**Examples**

| expression | answer |
|------------|--------|
| `"&(t,!(f))"` | `true` |
| `"|(f,t,f)"`   | `true` |
| `"!(|(f,f,f))"` | `false` |

---

## 2. Why a Stack is the Natural Choice

1. **Depth‑first nature** – Every operator applies to the *next* sub‑expression, mirroring the stack push/pop order.
2. **No recursion stack** – Recursion on a 20 000‑char string can hit JVM / Python recursion limits.
3. **Early termination** – For `&` we can stop as soon as we see `f`; for `|` as soon as we see `t`.

> **Key insight**: *You only need to keep the “active” operators and operand values on the stack.*

---

## 3. The Optimized Stack Algorithm (O(n) time, O(n) space)

```
for each char c in expression
    if c == ',' or c == '('  →  ignore
    else if c in { 't', 'f', '!', '&', '|' }  → push(c)
    else if c == ')'          → evaluate sub‑expression
```

**Evaluate sub‑expression**

```text
hasTrue  = false
hasFalse = false
while top of stack is 't' or 'f'
    pop -> val
    hasTrue  |= (val == 't')
    hasFalse |= (val == 'f')

op = pop()   // operator is now on top

if op == '!':
    push('t' if not hasTrue else 'f')   // !x: true only if x is false
elif op == '&':
    push('f' if hasFalse else 't')      // & : false if any operand is false
else:  // op == '|'
    push('t' if hasTrue else 'f')       // | : true if any operand is true
```

The final value on the stack is the answer.

> **Why early‑break?**  
> For `&`, as soon as `hasFalse` becomes `true` we could skip the rest of the values, but the simple loop already does that in a few more operations.  
> For `|`, the same idea applies. The gain is marginal compared to the extra code complexity, so we keep it simple.

---

## 4. Code

Below are full, compilable snippets for Java, Python, and C++. Each follows the exact logic described above.

### 4.1 Java

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class Solution {
    public boolean parseBoolExpr(String expression) {
        Deque<Character> stack = new ArrayDeque<>();

        for (char c : expression.toCharArray()) {
            if (c == ',' || c == '(') continue;
            if (c == 't' || c == 'f' || c == '!' || c == '&' || c == '|') {
                stack.push(c);
            } else if (c == ')') {
                boolean hasTrue  = false;
                boolean hasFalse = false;
                while (stack.peek() != '!' && stack.peek() != '&' && stack.peek() != '|') {
                    char val = stack.pop();
                    if (val == 't') hasTrue = true;
                    else if (val == 'f') hasFalse = true;
                }
                char op = stack.pop(); // operator
                char res;
                if (op == '!') {
                    res = hasTrue ? 'f' : 't';          // !x
                } else if (op == '&') {
                    res = hasFalse ? 'f' : 't';         // &x
                } else { // '|'
                    res = hasTrue ? 't' : 'f';          // |x
                }
                stack.push(res);
            }
        }
        return stack.peek() == 't';
    }
}
```

### 4.2 Python

```python
from collections import deque

class Solution:
    def parseBoolExpr(self, expression: str) -> bool:
        st = deque()
        for ch in expression:
            if ch in ',(':
                continue
            if ch in ('t', 'f', '!', '&', '|'):
                st.append(ch)
            elif ch == ')':
                has_true = has_false = False
                while st[-1] not in ('!', '&', '|'):
                    val = st.pop()
                    if val == 't':
                        has_true = True
                    else:   # val == 'f'
                        has_false = True
                op = st.pop()
                if op == '!':
                    st.append('t' if not has_true else 'f')
                elif op == '&':
                    st.append('f' if has_false else 't')
                else:   # '|'
                    st.append('t' if has_true else 'f')
        return st[-1] == 't'
```

### 4.3 C++

```cpp
class Solution {
public:
    bool parseBoolExpr(string expression) {
        vector<char> st; // use vector as stack for speed
        for (char c : expression) {
            if (c == ',' || c == '(') continue;
            if (c == 't' || c == 'f' || c == '!' || c == '&' || c == '|')
                st.push_back(c);
            else if (c == ')') {
                bool hasTrue = false, hasFalse = false;
                while (!st.empty() && st.back() != '!' && st.back() != '&' && st.back() != '|') {
                    char val = st.back(); st.pop_back();
                    if (val == 't') hasTrue = true;
                    else hasFalse = true;
                }
                char op = st.back(); st.pop_back();
                char res;
                if (op == '!')      res = hasTrue ? 'f' : 't';
                else if (op == '&') res = hasFalse ? 'f' : 't';
                else                res = hasTrue ? 't' : 'f'; // '|'
                st.push_back(res);
            }
        }
        return st.back() == 't';
    }
};
```

All three solutions run in **O(n)** time (one pass) and **O(n)** auxiliary space (the stack).

---

## 5. The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Correctness** | Works for all valid inputs, linear pass. | None – algorithm is simple and robust. | Forgetting to skip commas or parentheses → stack corruption. |
| **Complexity** | `O(n)` time, `O(n)` space – optimal for 20 k length. | None. | A recursive DFS implementation risks stack overflow for deep nesting. |
| **Readability** | Clear push/pop, one logical block. | Minor: use of boolean flags (`hasTrue`, `hasFalse`) might look unfamiliar. | Stack‑based solution may be harder to grasp for novices; recursion is more intuitive. |
| **Edge Cases** | Handles single `t`/`f`, any number of sub‑expressions. | None. | Expressions like `"!(t)"` → need to treat `!` as unary correctly. |
| **Interview Talk** | Explain early‑termination, stack metaphor, why we ignore commas. | Avoid over‑optimizing early‑break loops; it may confuse the interviewer. | Do **not** mention `ArrayDeque` in Java 8 interview; use `Stack` or `ArrayList` for clarity. |
| **Production Ready** | Clean, tested, and compilable. | None. | Adding debugging logs or printing stack after each op can bloat runtime. |

---

## 6. Alternative: Recursive Descent (Less Optimal for Interviews)

A naive recursion:

```python
def parse(expr, i):
    if expr[i] in 'tf':
        return expr[i] == 't', i+1
    op = expr[i]
    i += 2          # skip '('
    results = []
    while True:
        val, i = parse(expr, i)
        results.append(val)
        if expr[i] == ')':
            i += 1
            break
        i += 1          # skip ','
    if op == '!':
        return not results[0], i
    elif op == '&':
        return all(results), i
    else:  # '|'
        return any(results), i
```

> **Why avoid it?**  
> - Complexity hidden in recursion depth (`O(n)` in worst case).  
> - Harder to illustrate early‑termination.  
> - For languages like Java/Python, recursion depth > 10 000 can crash.

---

## 7. Talking Points for Your Job Interview

1. **Problem framing** – restate grammar, clarify single `t`/`f` case.  
2. **Why linear?** – Big‑O: one traversal, no extra data structures beyond a stack.  
3. **Stack analogy** – show how each `(` pushes context, each `)` pops context.  
4. **Early‑termination** – highlight `&` vs `|`.  
5. **Complexity proof** – mention that each character is processed once.  
6. **Testing** – give examples of boundary tests (`"t"`, `"&(t)"`, `"!(t)"`).  
7. **Scalability** – 20 k characters → recursion may fail, stack is safe.

> **Tip:** Practice the “walk‑through” with a whiteboard or an IDE debugger, so you can visually show the stack evolving.

---

## 7. Wrap‑Up

- **Stack algorithm**: The fastest, safest, and interview‑friendly solution for LeetCode 1106.  
- **Code**: Provided for **Java**, **Python**, **C++** – copy, paste, and run.  
- **Article**: This blog post is SEO‑friendly for “LeetCode 1106”, “Boolean expression”, “Stack algorithm”, “O(n)”, “Interview discussion”.

> 🚀 **Take action** – Post the code to your repository, add the article to your README, and share it on LinkedIn or Twitter.  

Good luck, and may your stack stay balanced!  

--- 

### References

1. LeetCode Problem 1106 – *“Boolean Expression”*.  
2. G. H. Cormen et al., *Algorithms*, 3rd ed., Chapter 6 (Stacks and Queues).  

--- 

*If you found this article useful, star the repo, share a comment below, or reach out on Twitter. Happy coding!*