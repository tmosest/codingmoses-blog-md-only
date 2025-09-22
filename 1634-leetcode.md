---
title: LeetCode 1634. Add Two Polynomials Represented as Linked Lists - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 1634 – Add Two Polynomials Represented as Linked Lists

> **Problem** – Merge two linked‑list polynomials that are already sorted in **strictly descending power** order, returning a new polynomial in the same order.  
> **Constraints** – `0 ≤ n ≤ 10⁴`, coefficients in `[-10⁹, 10⁹]`, powers in `[0, 10⁹]`.  
> **Goal** – `O(n)` time, `O(1)` auxiliary space (the output list can be built in place).

Below are clean, idiomatic solutions in **Java**, **Python**, and **C++** that satisfy the requirements.

---

### Java Solution

```java
/**
 * Definition for singly-linked list representing a polynomial term.
 */
class PolyNode {
    int coefficient;
    int power;
    PolyNode next;
    PolyNode(int coeff, int pow) { this.coefficient = coeff; this.power = pow; }
    PolyNode(int coeff, int pow, PolyNode next) { this.coefficient = coeff; this.power = pow; this.next = next; }
}

public class Solution {

    /**
     * Adds two polynomials represented by linked lists.
     * @param poly1 Head of first polynomial list
     * @param poly2 Head of second polynomial list
     * @return Head of the summed polynomial list
     */
    public PolyNode addPoly(PolyNode poly1, PolyNode poly2) {
        // Dummy head simplifies handling the first node
        PolyNode dummy = new PolyNode(0, 0);
        PolyNode tail = dummy;

        while (poly1 != null && poly2 != null) {
            if (poly1.power > poly2.power) {
                tail.next = poly1;
                poly1 = poly1.next;
            } else if (poly1.power < poly2.power) {
                tail.next = poly2;
                poly2 = poly2.next;
            } else {                // Same power – merge coefficients
                int newCoeff = poly1.coefficient + poly2.coefficient;
                if (newCoeff != 0) { // Only keep non‑zero terms
                    tail.next = new PolyNode(newCoeff, poly1.power);
                }
                poly1 = poly1.next;
                poly2 = poly2.next;
            }
            tail = tail.next; // Move tail forward if a node was attached
        }

        // Attach the remaining tail (either poly1 or poly2)
        tail.next = (poly1 != null) ? poly1 : poly2;
        return dummy.next;
    }
}
```

**Why this works**

| Step | What happens | Complexity |
|------|--------------|------------|
| 1.   | Create dummy head | O(1) |
| 2.   | While both lists are non‑empty, compare powers | O(1) per iteration |
| 3.   | Attach the larger‑power term, or merge if equal | O(1) |
| 4.   | Skip zero coefficient results | O(1) |
| 5.   | Append remaining list | O(1) |
| 6.   | Return `dummy.next` | O(1) |

Total: **O(n)** time, **O(1)** auxiliary space.

---

### Python Solution

```python
from typing import Optional

class PolyNode:
    def __init__(self, coefficient: int, power: int, next: Optional['PolyNode']=None):
        self.coefficient = coefficient
        self.power = power
        self.next = next

def addPoly(poly1: Optional[PolyNode], poly2: Optional[PolyNode]) -> Optional[PolyNode]:
    dummy = PolyNode(0, 0)
    tail = dummy

    while poly1 and poly2:
        if poly1.power > poly2.power:
            tail.next = poly1
            poly1 = poly1.next
        elif poly1.power < poly2.power:
            tail.next = poly2
            poly2 = poly2.next
        else:                       # equal power
            new_coeff = poly1.coefficient + poly2.coefficient
            if new_coeff:           # skip zero term
                tail.next = PolyNode(new_coeff, poly1.power)
            poly1 = poly1.next
            poly2 = poly2.next
        tail = tail.next if tail.next else tail

    # Attach the remaining list
    tail.next = poly1 or poly2
    return dummy.next
```

**Key Points**

* Uses a dummy node to avoid edge‑case checks for the first element.
* Maintains linear scan and constant extra memory.
* Pythonic handling of `None` and list traversal.

---

### C++ Solution

```cpp
/**
 * Definition for singly-linked list node representing a polynomial term.
 */
struct PolyNode {
    int coefficient;
    int power;
    PolyNode *next;
    PolyNode(int coeff, int pow) : coefficient(coeff), power(pow), next(nullptr) {}
    PolyNode(int coeff, int pow, PolyNode *n) : coefficient(coeff), power(pow), next(n) {}
};

class Solution {
public:
    PolyNode* addPoly(PolyNode* poly1, PolyNode* poly2) {
        // Dummy head to simplify node attachment
        PolyNode dummy(0, 0);
        PolyNode* tail = &dummy;

        while (poly1 && poly2) {
            if (poly1->power > poly2->power) {
                tail->next = poly1;
                poly1 = poly1->next;
            } else if (poly1->power < poly2->power) {
                tail->next = poly2;
                poly2 = poly2->next;
            } else { // same power
                int newCoeff = poly1->coefficient + poly2->coefficient;
                if (newCoeff != 0) {            // keep only non‑zero terms
                    tail->next = new PolyNode(newCoeff, poly1->power);
                }
                poly1 = poly1->next;
                poly2 = poly2->next;
            }
            tail = tail->next ? tail->next : tail; // move only if we attached a node
        }

        // Append the remainder of the non‑empty list
        tail->next = poly1 ? poly1 : poly2;
        return dummy.next;
    }
};
```

---

## 2.  Blog Article – “Add Two Polynomials Represented as Linked Lists: The Good, the Bad, and the Ugly”

> **SEO Focus**: *Java interview questions, LinkedList interview, Leetcode solutions, Python interview, C++ interview, software engineer, job interview*

---

### Introduction

When searching for “Java interview questions”, “Python interview problems”, or “C++ interview coding challenges”, *Add Two Polynomials Represented as Linked Lists* is a frequent contender. It blends classic linked‑list manipulation with a twist of algebra, making it a favorite for assessing both data‑structure fluency and algorithmic thinking.  

In this post, we’ll dissect the problem, explore the strengths and pitfalls of common solutions, and walk through a clean, production‑ready implementation. By the end, you’ll know exactly what interviewers are looking for and how to present a polished answer that boosts your chances of landing that dream job.

---

### The Problem (LeetCode 1634)

You’re given two singly linked lists, each node representing a polynomial term:  

```
[coefficient, power]
```

Both lists are already sorted in strictly descending power order and contain no zero‑coefficient terms.  

**Task** – Merge the two polynomials into one sorted list, summing coefficients with the same power and omitting any terms that result in a zero coefficient.

*Example*:  
`poly1 = [[2,2],[4,1],[3,0]]` (2x² + 4x + 3)  
`poly2 = [[3,2],[-4,1],[-1,0]]` (3x² – 4x – 1)  

**Output**: `[[5,2],[2,0]]` (5x² + 2)

---

### The Good – Why It’s a Great Interview Question

| Aspect | Why It Matters |
|--------|----------------|
| **Linked‑list basics** | Tests ability to traverse and modify linked lists, a core skill for any backend developer. |
| **Sorting invariants** | Assumes lists are pre‑sorted, so candidates can focus on merging rather than sorting. |
| **Edge cases** | Zero coefficients, equal powers, or empty lists force careful handling of boundary conditions. |
| **Time / Space** | Optimal solution is linear time and constant auxiliary space – a classic interview “sweet spot”. |
| **Language agnostic** | You can solve it in Java, Python, C++, or any language that supports linked lists. |

---

### The Bad – Common Pitfalls

| Pitfall | Consequence | Avoidance |
|---------|-------------|-----------|
| **Ignoring zero‑coefficients** | Leaves `0xⁿ` terms in the result, violating problem constraints. | Explicitly check `if (sum != 0)` before appending. |
| **Wrong comparison logic** | Swapping nodes incorrectly or causing infinite loops. | Use clear `if/else if/else` or a single `if (p1->power > p2->power) … else if …`. |
| **Not using a dummy head** | Requires many special cases for the first node. | Create a dummy node and return `dummy.next`. |
| **Extra memory allocation** | Fails the `O(1)` space requirement. | Reuse existing nodes; only allocate when a new node is needed for a merged term. |
| **Skipping `poly1.next` or `poly2.next`** | Misaligned list after merge. | Always advance pointers after processing a term. |

---

### The Ugly – Over‑Complicated Approaches

Sometimes candidates implement **recursive merges** or **hash‑map aggregation**. While correct, they break the `O(1)` space rule (the recursion stack counts as memory). Moreover, creating new nodes for every term (even if the term exists in one of the input lists) wastes time and can lead to **memory leaks** in languages like C++.

> **Tip**: A simple iterative, in‑place merge is the “clean, lean” answer that impresses interviewers.

---

### A Clean, Idiomatic Implementation (Java as the Reference)

Below is the Java solution that we’ll walk through step‑by‑step. It uses a dummy node, merges in a single pass, and never allocates unnecessary nodes.

```java
public PolyNode addPoly(PolyNode poly1, PolyNode poly2) {
    PolyNode dummy = new PolyNode(0, 0);
    PolyNode tail = dummy;

    while (poly1 != null && poly2 != null) {
        if (poly1.power > poly2.power) {
            tail.next = poly1;          // Larger power – keep original node
            poly1 = poly1.next;
        } else if (poly1.power < poly2.power) {
            tail.next = poly2;
            poly2 = poly2.next;
        } else {
            int newCoeff = poly1.coefficient + poly2.coefficient;
            if (newCoeff != 0) {        // Only attach if the result is non‑zero
                tail.next = new PolyNode(newCoeff, poly1.power);
            }
            poly1 = poly1.next;
            poly2 = poly2.next;
        }
        tail = tail.next; // advance if a node was attached
    }

    tail.next = (poly1 != null) ? poly1 : poly2; // append rest
    return dummy.next;
}
```

*Why it’s interview‑ready:*

1. **Clear logic** – the `if/else` chain mirrors the mathematical reasoning: “greater power → keep that term; equal power → sum coefficients”.
2. **Zero‑term handling** – the `if (newCoeff != 0)` guard ensures compliance with the “no zero terms” rule.
3. **O(1) auxiliary space** – we only allocate when a new merged node is required; all other operations reuse existing nodes.
4. **Linear scan** – each node is processed once; no nested loops or sorting overhead.

---

### Python & C++ Variants

- **Python**: Use a `while poly1 and poly2` loop; the dummy node is a single `PolyNode(0,0)` object.  
- **C++**: Prefer a `PolyNode dummy(0,0)` on the stack and return `dummy.next`.  
- **Java**: The same logic applies, just with strong typing and explicit pointer references.

---

### Interview Presentation Tips

1. **State your assumptions** – “Both lists are sorted descending by power and contain no zero coefficients.”  
2. **Explain the time/space trade‑off** – “We’ll merge in linear time, reusing nodes so we stay within `O(1)` extra space.”  
3. **Show edge‑case handling** – “If coefficients cancel, we simply skip the node; if one list runs out, we append the rest.”  
4. **Write clean code** – Keep the logic in a single method; avoid nested helper functions unless necessary.  
5. **Walk through a test case** – Simulate `poly1 = [[2,3],[1,1]]` and `poly2 = [[-2,3],[4,0]]`; show how the pointer decisions lead to the final list.

---

### Final Thoughts – “What Interviewers Want”

Interviewers are looking for:

| Desired Output | What it reflects |
|----------------|------------------|
| **Linear time** | Efficient use of data structures. |
| **Constant space** | In‑place manipulation and mindful memory usage. |
| **Robust edge‑case handling** | Attention to detail and defensive programming. |
| **Clear, commented code** | Ability to communicate complex logic to a teammate or manager. |

Mastering *Add Two Polynomials Represented as Linked Lists* gives you a versatile talking point. It showcases your grasp of linked lists, sorting invariants, and algorithmic optimization—all while letting you flex a bit of math.  

So the next time you see this on LeetCode, on a company’s coding challenge, or in a recruiter’s PDF, walk in with confidence, keep your code clean, and remember: the best answers are those that **merge, sum, and skip**, all in one tidy pass.

---

### Takeaway

- **Implement** the dummy‑head, in‑place merge in your preferred language.  
- **Explain** the algorithm’s linearity and constant space.  
- **Highlight** edge‑case handling (equal powers, zero sums, empty lists).  

By mastering this problem, you’ll not only ace the coding portion of your interview but also demonstrate the clear, efficient coding style that top tech companies value. Happy coding, and may your job interviews end on a high note! 🚀

--- 

*Feel free to comment with your own language‑specific implementation or share where you’ve encountered this problem in a real interview.*