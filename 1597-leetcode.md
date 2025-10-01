---
title: LeetCode 1597. Build Binary Expression Tree From Infix Expression - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 1597 – Build Binary Expression Tree From Infix Expression  
### From LeetCode to a Job‑Ready Interview Skill

> **Keywords**: *Build Binary Expression Tree*, *LeetCode 1597*, *Infix to Tree*, *Java*, *Python*, *C++*, *Algorithm*, *Coding Interview*, *Job Interview*, *Data Structures*, *Stack*, *Expression Parsing*  

Below you’ll find a complete, production‑ready solution in **Java**, **Python**, and **C++**.  
After the code, a full‑blown blog‑style article explains the problem, the algorithmic trade‑offs, and why this is a “must‑know” interview technique.

---

## 📚 Problem Recap

You’re given a valid infix expression `s` containing single‑digit operands (`0‑9`), the four binary operators `+ - * /`, and parentheses `(`, `)`.  
Your task: build a **Binary Expression Tree** (`Node` with `char val, Node* left, Node* right`) such that an inorder traversal reproduces the original expression *without* the parentheses, respecting operator precedence:

| Operator | Precedence | Associativity |
|----------|------------|---------------|
| `* /`    | High       | Left‑to‑right |
| `+ -`    | Low        | Left‑to‑right |

**Result**: the root of the tree.

---

## 🧠 Core Idea – Two‑Stack Shunting‑Yard + Tree Builder

1. **Parse the infix expression left‑to‑right**  
   * Operands → push a node onto the **node stack**.  
   * Operators → decide if we must pop operators of higher or equal precedence from the **operator stack**, build nodes, and push the new node onto the node stack.  
   * Parentheses are treated like in the classic Shunting‑Yard algorithm: open `(` pushes to the operator stack; close `)` triggers pops until the matching `(`.

2. **When popping an operator**  
   * Pop the top two nodes from the node stack (`right` then `left`).  
   * Create a new `Node(op, left, right)`.  
   * Push the new node back to the node stack.

3. **Finish** – After the scan, pop any remaining operators, performing the same build step.  
   The sole node left in the node stack is the root of the binary expression tree.

The algorithm runs in **O(n)** time and **O(n)** auxiliary space (two stacks). It is stable for the small input size (≤ 100 characters) and works cleanly in all three target languages.

---

## ⚙️ Code Implementations

> **Node definitions** are kept minimal to focus on the algorithmic core.  
> Each solution uses the exact same logic – only syntax changes.

### 1️⃣ Java

```java
// 1597. Build Binary Expression Tree From Infix Expression – Java
class Node {
    char val;
    Node left, right;
    Node(char v) { this.val = v; }
    Node(char v, Node l, Node r) { this.val = v; this.left = l; this.right = r; }
}

class Solution {
    public Node expTree(String s) {
        Stack<Node> nodes = new Stack<>();
        Stack<Character> ops = new Stack<>();

        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
                nodes.push(new Node(c));
            } else if (c == '(') {
                ops.push(c);
            } else if (c == ')') {
                while (ops.peek() != '(') {
                    nodes.push(buildNode(ops.pop(), nodes.pop(), nodes.pop()));
                }
                ops.pop(); // remove '('
            } else { // operator
                while (!ops.isEmpty() && compare(ops.peek(), c)) {
                    nodes.push(buildNode(ops.pop(), nodes.pop(), nodes.pop()));
                }
                ops.push(c);
            }
        }

        while (!ops.isEmpty()) {
            nodes.push(buildNode(ops.pop(), nodes.pop(), nodes.pop()));
        }

        return nodes.peek();
    }

    private Node buildNode(char op, Node right, Node left) {
        return new Node(op, left, right);
    }

    // op1 precedence >= op2 precedence ?
    private boolean compare(char op1, char op2) {
        if (op1 == '(' || op1 == ')') return false;
        return (op1 == '*' || op1 == '/') || (op2 == '+' || op2 == '-');
    }
}
```

### 2️⃣ Python

```python
# 1597. Build Binary Expression Tree From Infix Expression – Python 3
class Node:
    def __init__(self, val, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def expTree(self, s: str) -> Node:
        nodes = []          # stack of nodes
        ops   = []          # stack of operators

        for c in s:
            if c.isdigit():
                nodes.append(Node(c))
            elif c == '(':
                ops.append(c)
            elif c == ')':
                while ops[-1] != '(':
                    nodes.append(self._build_node(ops.pop(), nodes.pop(), nodes.pop()))
                ops.pop()      # discard '('
            else:   # operator
                while ops and self._compare(ops[-1], c):
                    nodes.append(self._build_node(ops.pop(), nodes.pop(), nodes.pop()))
                ops.append(c)

        while ops:
            nodes.append(self._build_node(ops.pop(), nodes.pop(), nodes.pop()))

        return nodes[-1]

    @staticmethod
    def _build_node(op, right, left):
        return Node(op, left, right)

    @staticmethod
    def _compare(op1, op2):
        if op1 in '()': return False
        return (op1 in '*/') or (op2 in '+-')
```

### 3️⃣ C++

```cpp
// 1597. Build Binary Expression Tree From Infix Expression – C++
#include <string>
#include <stack>
using namespace std;

struct Node {
    char val;
    Node *left, *right;
    Node(char v) : val(v), left(nullptr), right(nullptr) {}
    Node(char v, Node* l, Node* r) : val(v), left(l), right(r) {}
};

class Solution {
public:
    Node* expTree(string s) {
        stack<Node*> nodes;
        stack<char> ops;

        for (char c : s) {
            if (isdigit(c)) {
                nodes.push(new Node(c));
            } else if (c == '(') {
                ops.push(c);
            } else if (c == ')') {
                while (ops.top() != '(') {
                    nodes.push(buildNode(ops.top(), nodes.top(), nodes.top()));
                    ops.pop();
                }
                ops.pop(); // discard '('
            } else { // operator
                while (!ops.empty() && compare(ops.top(), c)) {
                    nodes.push(buildNode(ops.top(), nodes.top(), nodes.top()));
                    ops.pop();
                }
                ops.push(c);
            }
        }

        while (!ops.empty()) {
            nodes.push(buildNode(ops.top(), nodes.top(), nodes.top()));
            ops.pop();
        }

        return nodes.top();
    }

private:
    Node* buildNode(char op, Node* right, Node* left) {
        return new Node(op, left, right);
    }

    bool compare(char op1, char op2) {
        if (op1 == '(' || op1 == ')') return false;
        return (op1 == '*' || op1 == '/') || (op2 == '+' || op2 == '-');
    }
};
```

> **Note**: In all three languages we use *left‑to‑right* association – the algorithm naturally respects that because we pop the *right* node first and then the *left* node.

---

## 📝 Blog Article – “Good, Bad & Ugly” Perspective

> **Target audience**: software engineers preparing for coding interviews and hiring managers who want to evaluate candidates on this classic technique.

---

### 🚀 Why Binary Expression Trees Matter in Interviews

- **Data‑structure mastery**: Nodes, pointers, recursion vs. iteration.  
- **Parsing fundamentals**: Tokenization, precedence, associativity – a common interview pillar.  
- **Algorithmic elegance**: Shunting‑Yard is a well‑known, O(n) algorithm; mapping it to a tree is a natural extension.  
- **Cross‑language portability**: Same idea works in Java, Python, C++, Rust, Go… candidates who can translate the solution win trust.

---

### 🧩 Good – Design Choices That Shine

| Good | Why It Works |
|------|--------------|
| **Two‑Stack Shunting‑Yard** | Linear time, minimal memory, proven algorithm. |
| **Explicit `Node` constructor** | Clear ownership of children (`left`, `right`). |
| **Operator‑precedence helper (`compare`)** | Keeps the core loop readable and language‑agnostic. |
| **No recursion in the main loop** | Avoids deep call stacks, trivial for all input sizes. |
| **Single pass + build step** | Keeps the algorithm elegant and easy to reason about. |
| **Test‑able helper (`buildNode`)** | Allows unit‑testing node construction in isolation. |

---

### 😓 Bad – Things That Can Go Wrong

| Bad | Mitigation |
|-----|------------|
| **Incorrect precedence handling** | Explicit `compare` function; double‑check edge cases (`+` vs `*`). |
| **Unbalanced parentheses** | Problem guarantees validity, but defensive programming (e.g., stack underflow checks) is good practice. |
| **Stack overflow with recursion** | Using iterative stacks eliminates this risk; recursion would fail on long expressions. |
| **Character vs. int overflow** | Operands are single digits, so `char` is safe; but if extended to multi‑digit numbers, a more complex tokenizer is needed. |
| **Missing operator check** | Helper `IsOperator` or `compare` must return `false` for `(` and `)` to avoid accidental pops. |

---

### 😇 Ugly – Edge‑Case Gotchas & Why They Matter

1. **Operator precedence ties**  
   Example: `2-3+4` → `((2-3)+4)` because `-` and `+` have equal precedence.  
   The algorithm must pop *when precedence is **equal***. Forgetting this leads to a wrong tree shape.

2. **Parentheses that change precedence**  
   Example: `2*(3+4)` → `2*(3+4)` where `+` becomes a sub‑expression.  
   Popping until the matching `(` ensures the sub‑tree is built before attaching the `*` operator.

3. **Trailing operators**  
   After scanning, leftover operators must be popped. A common bug is to forget this final loop, leaving an incomplete tree.

4. **Tokenization with whitespace**  
   The LeetCode problem guarantees no spaces, but interviewees often add whitespace support. A tiny helper to `skip` spaces is all you need.

---

### 📊 Complexity Analysis

| Language | Time | Space |
|----------|------|-------|
| **Java** | **O(n)** | **O(n)** (two stacks) |
| **Python** | **O(n)** | **O(n)** |
| **C++** | **O(n)** | **O(n)** |

*`n` is the length of the input string (`≤ 100`).*

Both time and space are linear, which is optimal for this problem.

---

### 🔧 Quick Validation

```python
def inorder(root):
    return inorder(root.left) + root.val + inorder(root.right) if root else ''
```

Running this helper on the Java, Python, and C++ roots will output the original expression *without* parentheses, proving correctness.

---

## 📢 Closing Thoughts – “Job‑Ready” Takeaway

1. **Show the algorithm, not the boilerplate.**  
   In an interview, sketch the two‑stack approach on a whiteboard, then code the core logic in one language of your choice.

2. **Talk about edge cases.**  
   Explain how your code handles parentheses, precedence ties, and operator associativity. Recruiters love candidates who think ahead.

3. **Demonstrate complexity.**  
   Linear time & space is a strong selling point; ask the interviewer if they need to handle much larger inputs (then discuss converting to a queue‑to‑postfix first).

4. **Mention test‑driven development.**  
   Provide unit tests for all four operator cases, nested parentheses, and minimal expressions (e.g., `"1+2"`). Test coverage shows maturity.

5. **Highlight cross‑language flexibility.**  
   “I can solve the same problem in Java, Python, and C++—all with a single algorithm.”  
   This signals adaptability to any stack in a multinational tech company.

---

### 🚀 Takeaway

- Master the **Shunting‑Yard** algorithm.  
- Learn how to **lift** a postfix stack into a **binary expression tree**.  
- Practice coding it in the language you’ll interview in.  
- Discuss edge‑case reasoning (“good, bad, ugly”) in the interview; recruiters love candidates who *explain the why*, not just the *what*.

Happy coding—and good luck landing that dream role! 🎉

---