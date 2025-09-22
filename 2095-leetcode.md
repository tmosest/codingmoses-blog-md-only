---
title: LeetCode 2095. Delete the Middle Node of a Linked List - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 LeetCode 2095 – Delete the Middle Node of a Linked List  
### Problem Statement (Short)

> **Given** a singly‑linked list, delete its **middle node** and return the new head.  
> The middle node is the ⌊ n/2 ⌋‑th node (0‑based indexing) where *n* is the list length.

### Why This Problem Matters

* Demonstrates mastery of pointer manipulation and the **fast/slow pointer** pattern.  
* Shows how to think about “mid‑point” problems without extra space.  
* A classic interview question for software‑engineering roles (Java, C++, Python, etc.).

---

## 📦 Solution in Three Languages

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.  
Each follows the same two‑pointer (tortoise & hare) strategy, achieving **O(n)** time and **O(1)** space.

> **Important Edge Cases**  
> • Empty list (`head == null`) → return `null`  
> • One‑node list → removing the only node results in an empty list → return `null`

### 1️⃣ Java

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteMiddle(ListNode head) {
        // Edge: 0 or 1 node -> list becomes empty
        if (head == null || head.next == null) return null;

        ListNode slow = head;          // moves 1 step
        ListNode fast = head.next;     // moves 2 steps
        ListNode prev = null;          // node before slow

        while (fast != null && fast.next != null) {
            prev = slow;
            slow = slow.next;
            fast = fast.next.next;
        }

        // prev is now the node just before the middle
        if (prev != null) prev.next = slow.next;
        return head;
    }
}
```

### 2️⃣ Python

```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def deleteMiddle(self, head: ListNode) -> ListNode:
        # Edge: 0 or 1 node -> list becomes empty
        if not head or not head.next:
            return None

        slow = head
        fast = head
        prev = None

        # fast moves twice as fast as slow
        while fast and fast.next:
            prev = slow
            slow = slow.next
            fast = fast.next.next

        # prev is the node just before the middle
        prev.next = slow.next
        return head
```

### 3️⃣ C++

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* deleteMiddle(ListNode* head) {
        if (!head || !head->next) return nullptr;   // 0 or 1 node

        ListNode* slow = head;
        ListNode* fast = head;
        ListNode* prev = nullptr;

        while (fast && fast->next) {
            prev = slow;
            slow = slow->next;
            fast = fast->next->next;
        }

        // prev now points to the node before middle
        prev->next = slow->next;
        return head;
    }
};
```

---

## 🔍 How the Two‑Pointer Approach Works

1. **Initialize** `slow` and `fast` at the head.  
2. Move `fast` two nodes per iteration, `slow` one node.  
3. When `fast` reaches the end (`null` or `null->next`), `slow` points to the middle node.  
4. Use a `prev` pointer (the node that was just before `slow`) to skip the middle node (`prev->next = slow->next`).  

Because we only traverse the list once, the algorithm is linear in time and constant in auxiliary space.

---

## 🧪 Quick Test Harness (Python)

```python
def build_linked_list(vals):
    dummy = ListNode()
    cur = dummy
    for v in vals:
        cur.next = ListNode(v)
        cur = cur.next
    return dummy.next

def to_list(head):
    out = []
    while head:
        out.append(head.val)
        head = head.next
    return out

# Example 1
head = build_linked_list([1,3,4,7,1,2,6])
print(to_list(Solution().deleteMiddle(head)))  # → [1,3,4,1,2,6]
```

Run similar tests for edge cases (`[2,1]`, `[5]`, `[]`).

---

## 📚 Blog Article: “The Good, the Bad, and the Ugly of Deleting the Middle Node”

> **Target Audience** – Junior‑to‑Mid level software engineers preparing for coding interviews.  
> **SEO Focus** – *Linked List*, *Middle Node Deletion*, *LeetCode 2095*, *Fast/Slow Pointers*, *Job Interview*, *Software Engineer*, *Algorithm Interview Questions*.

---

### 1️⃣ Introduction

When an interviewer gives you a **linked‑list** problem, they’re usually probing two things:

1. **Pointer arithmetic** – can you manipulate references without a stack?
2. **Algorithmic thinking** – can you find the optimal way to solve a problem?

Delete‑the‑Middle‑Node is a textbook problem that touches both. In this article we’ll dissect **what makes the problem “good”, “bad”, and “ugly”**, how to solve it in the most efficient way, and how to talk about it during an interview.

> **Quick TL;DR**: Use a two‑pointer technique (`slow`/`fast`) to find the middle in one pass. Skip the node with `prev->next = slow->next`. Edge cases: 0/1 node → return `null`.

---

### 2️⃣ The Good

| Aspect | Why It’s Good |
|--------|---------------|
| **Linear Time** | O(n) – we only scan once, no need for counting twice. |
| **Constant Space** | O(1) – we use only a handful of pointers. |
| **Single Pass** | Keeps memory usage low and runtime predictable. |
| **Widely Asked** | Appears in many interviews (Google, Amazon, Facebook). |
| **Good Teaching Tool** | Demonstrates the tortoise & hare algorithm. |

#### Interview‑Friendly Insight
When the interviewer asks *“How would you handle this with only one traversal?”*, you can immediately bring up the fast/slow pointer idea, show that you’re already thinking about the optimal solution.

---

### 3️⃣ The Bad

| Pitfall | Explanation |
|--------|-------------|
| **Wrong Midpoint** | Some solutions assume the *second* middle in even‑length lists (⌈n/2⌉). The problem explicitly wants ⌊n/2⌋. |
| **Null Dereference** | Forgetting to check `head` or `head->next` leads to segmentation faults (C++), `NoneType` errors (Python), or null‑pointer exceptions (Java). |
| **Two‑Pointer Mis‑setup** | If you move `fast` ahead by one before the loop, the `prev` will be `null` for lists of length 2. |
| **Testing Insufficiency** | Many candidates only test on even‑length lists; odd‑length lists behave differently. |

> **Key Takeaway**: Always handle the *empty* and *single‑node* list before running pointers.

---

### 3️⃣ The Ugly

> Some candidates go for a **two‑phase approach**:
> 1. Count the nodes (`O(n)`), find `mid = n/2`.  
> 2. Traverse again to `mid-1` and skip.

This is technically correct but **sub‑optimal** and harder to explain under time pressure. It uses two passes and feels “lazy” to interviewers who love elegance.

> **Another ugly** – building a new list and copying values. That uses O(n) space, defeats the purpose of a “pure” linked‑list exercise, and often indicates the candidate doesn’t understand reference manipulation.

---

### 4️⃣ The Core Idea: Fast & Slow Pointers

```text
     slow   → 1  → 2  → 3  → 4  → 5  → 6
     fast   → 1  → 2  → 3  → 4  → 5  → 6
      after 1st step
     slow   → 2
     fast   → 3
      after 2nd step
     slow   → 3
     fast   → 5
      after 3rd step
     slow   → 4   (middle)
     fast   → None
```

* `fast` moves twice as fast as `slow`.  
* When `fast` is at the end, `slow` is exactly at ⌊n/2⌋.  
* Keep a `prev` pointer to bypass the middle node.

> **Tip**: In languages like C++/Java, remember to **initialize `prev = nullptr`** and set it **inside the loop** (`prev = slow`). This avoids an extra “check” after the loop.

---

### 5️⃣ How to Discuss During an Interview

1. **Clarify the definition of “middle”**  
   > “You want the node at index `floor(n/2)`?” – Confirm.

2. **Ask about constraints**  
   > “Any upper bound on the list size?” – This will let you decide between O(n) vs O(log n) approaches.

3. **Explain the two‑pointer solution**  
   * Draw the pointers on a whiteboard.  
   * Show that the algorithm runs in a single pass.  
   * Discuss why we need a `prev` pointer to unlink.

4. **Mention edge cases**  
   * Empty list → `null`  
   * One node → `null`

5. **Optional** – show the length‑based solution if the interviewer asks for a “different” approach.  
   * Complexity: O(n) time, O(1) space (same as two‑pointer).  
   * Simpler to reason but requires a second traversal.

6. **Time & Space Complexity**  
   * `O(n)` time, `O(1)` space.  
   * You can always ask: *“Is an O(n²) solution acceptable? I think not, because we can do it in one pass.”*

---

### 6️⃣ Variations to Impress

| Variation | What to Highlight |
|-----------|-------------------|
| **Return the deleted node** | Useful for problems that ask “delete middle and return its value”. |
| **Handle Doubly Linked Lists** | Slight modification: just remove `prev->next` and set `prev->next->prev = prev`. |
| **Circular Lists** | Adapt pointers to cycle detection (`fast = fast->next->next`). |
| **Find Median in Sorted List** | Merge this logic with a binary‑search‑style “median” approach. |

Mentioning these variations shows you’re not just memorizing, but truly understanding the pattern.

---

### 7️⃣ Interview‑Ready Tips

| Question | How to Answer |
|----------|---------------|
| “Can we solve this without extra space?” | Yes, using two pointers. |
| “What about an empty or single‑node list?” | Return `null`. |
| “Why two pointers instead of counting?” | One pass, O(1) space. |
| “What if the list is huge and you can’t store all nodes?” | Still fine – the algorithm only keeps references. |
| “What’s the worst‑case runtime?” | Linear; no nested loops. |

> **STAR Format**  
> *S – Situation (linked list problem)*  
> *T – Task (delete middle)*  
> *A – Action (two‑pointer algorithm)*  
> *R – Result (O(n) time, O(1) space)*

---

### 8️⃣ Adding This to Your Résumé

```text
- Solved LeetCode 2095 “Delete the Middle Node of a Linked List” using the fast/slow pointer pattern (O(n) time, O(1) space).
- Demonstrated efficient pointer manipulation and optimal single‑pass algorithms.
```

Feel free to link to the GitHub repo where the code lives, or add the solution to a personal portfolio. Employers love seeing real code in multiple languages.

---

### 9️⃣ Call to Action

If you’ve ever faced a linked‑list question in an interview, you know how intimidating it can feel.  
But with the right approach, it’s **the kind of problem that showcases your problem‑solving chops**.

**Want to practice?**  
Try variations:  
* Delete the middle *node* in a **doubly linked list**.  
* Find the *median value* in a sorted linked list.  
* Delete *k‑th node from the end* (LeetCode 19).

---

## 🚀 Takeaway for Your Job Hunt

* **Showcase the two‑pointer technique** in your interview notes; it’s a go‑to pattern for linked‑list questions.  
* **Explain edge cases** upfront – interviewers love when candidates handle them gracefully.  
* **Mention the interview‑specific angle** – e.g., “Would you like me to write a test harness?”  
* **Link to your GitHub** with the Java/Python/C++ solutions in the résumé or portfolio.

With these implementations, explanations, and interview strategies in your toolbox, you’re ready to turn the *Delete the Middle Node* problem into a confident interview win—and a headline‑friendly blog post that recruiters will love to read!