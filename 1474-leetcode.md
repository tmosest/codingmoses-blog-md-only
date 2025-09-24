---
title: LeetCode 1474. Delete N Nodes After M Nodes of a Linked List - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 **LeetCode 1474 – Delete N Nodes After M Nodes of a Linked List**  
> *A clean, in‑place O(n) solution in **Java**, **Python**, and **C++** + a full‑blown interview‑ready blog post.*

---

### Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Why It Matters](#why-it-matters)  
3. [Naïve Ideas (The Bad & Ugly)](#naïve-ideas)  
4. [The Optimal Solution (The Good)](#optimal-solution)  
5. [Code Walk‑through](#code-walkthrough)  
   * [Java](#java)  
   * [Python](#python)  
   * [C++](#c-plus-plus)  
6. [Complexity Analysis](#complexity)  
7. [Edge Cases & Common Pitfalls](#edge-cases)  
8. [Follow‑Up: In‑Place vs. Extra Space](#follow-up)  
9. [Interview Tips & Tricks](#interview-tips)  
10. [SEO Checklist & Keywords](#seo-checklist)  
11. [Conclusion & Final Thoughts](#conclusion)  

---

## <a name="problem-overview"></a>1️⃣ Problem Overview  

> **Delete N Nodes After M Nodes of a Linked List**  
> **LeetCode #1474** – *Easy*  

You’re given a **singly‑linked list** and two integers `m` and `n`.  
Starting from the head, keep the first `m` nodes, delete the next `n` nodes, then repeat until the end of the list.  
Return the head of the modified list.

**Example**  
```
Input: head = [1,2,3,4,5,6,7,8,9,10,11,12,13], m = 2, n = 3
Output: [1,2,6,7,11,12]
```

**Constraints**  
- `1 <= nodes <= 10^4`  
- `1 <= val <= 10^6`  
- `1 <= m, n <= 1000`

---

## <a name="why-it-matters"></a>2️⃣ Why It Matters  

- **Linked List Mastery** – Most technical interviews ask you to manipulate pointers directly.  
- **Space‑Optimal** – The problem explicitly asks for an **in‑place** solution (O(1) extra space).  
- **Time Efficiency** – A single pass through the list (O(n) time) is the ideal solution.  

Mastering this problem demonstrates that you understand:
- Pointer traversal  
- Edge‑case handling  
- Clean code structure (dummy head, loops vs recursion)  

---

## <a name="naïve-ideas"></a>3️⃣ Naïve Ideas (The Bad & Ugly)

| Idea | Why It’s Bad | Ugly Touch |
|------|--------------|------------|
| **Recursion** | Each call uses stack space → O(n) space | `deleteNodes(head)` calls itself inside the delete loop, blowing up stack on large lists |
| **Two‑pass** | First pass to count, second pass to delete → still O(n) but wasteful | Extra loop, unnecessary complexity |
| **Array Conversion** | Convert to array → O(n) extra memory | Loses linked‑list properties, not “in‑place” |

> **Takeaway** – Keep it linear, pointer‑only, and single‑pass.

---

## <a name="optimal-solution"></a>4️⃣ The Optimal Solution (The Good)

1. **Use a dummy head** – simplifies edge cases when the head itself is removed.  
2. **Maintain two pointers**:  
   - `prev` – the last node that remains after a deletion round.  
   - `curr` – the node we’re currently inspecting.  
3. **Loop**  
   - Advance `prev` and `curr` `m` steps (keep the nodes).  
   - Advance `curr` `n` steps (delete the nodes).  
   - Link `prev->next = curr`.  
4. **Stop** when `curr` becomes `NULL`.

All steps are **O(1) per node**, so the overall time is **O(n)** and space **O(1)**.

---

## <a name="code-walkthrough"></a>5️⃣ Code Walk‑through  

Below are clean, production‑ready implementations in the three major interview languages.

### <a name="java"></a>Java

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
    public ListNode deleteNodes(ListNode head, int m, int n) {
        // Dummy node to handle deletions at the head
        ListNode dummy = new ListNode(0);
        dummy.next = head;

        ListNode prev = dummy;   // last node that remains
        ListNode curr = head;    // current node in the list

        while (curr != null) {
            // 1️⃣ Keep m nodes
            for (int i = 0; i < m && curr != null; i++) {
                prev = curr;
                curr = curr.next;
            }

            // 2️⃣ Delete n nodes
            for (int i = 0; i < n && curr != null; i++) {
                curr = curr.next;
            }

            // 3️⃣ Connect the kept part to the next kept part
            prev.next = curr;
        }

        return dummy.next;
    }
}
```

### <a name="python"></a>Python

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def deleteNodes(head: ListNode, m: int, n: int) -> ListNode:
    dummy = ListNode(0, head)          # dummy node
    prev, curr = dummy, head

    while curr:
        # Keep m nodes
        for _ in range(m):
            if curr is None:
                break
            prev, curr = curr, curr.next

        # Delete n nodes
        for _ in range(n):
            if curr is None:
                break
            curr = curr.next

        # Link
        prev.next = curr

    return dummy.next
```

### <a name="c-plus-plus"></a>C++

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(nullptr) {}
 * };
 */
class Solution {
public:
    ListNode* deleteNodes(ListNode* head, int m, int n) {
        ListNode dummy(0);
        dummy.next = head;
        ListNode *prev = &dummy, *curr = head;

        while (curr) {
            // Keep m nodes
            for (int i = 0; i < m && curr; ++i) {
                prev = curr;
                curr = curr->next;
            }

            // Delete n nodes
            for (int i = 0; i < n && curr; ++i) {
                curr = curr->next;
            }

            // Connect
            prev->next = curr;
        }

        return dummy.next;
    }
};
```

---

## <a name="complexity"></a>6️⃣ Complexity Analysis  

| Metric | Java / Python / C++ | Explanation |
|--------|---------------------|-------------|
| **Time** | **O(n)** | Each node is visited a constant number of times (at most `m + n` but `m + n ≤ 2000`). |
| **Space** | **O(1)** | Only a few pointers + dummy head; no auxiliary arrays or recursion stacks. |

> ✅ The optimal solution meets the “in‑place” requirement perfectly.

---

## <a name="edge-cases"></a>7️⃣ Edge Cases & Common Pitfalls  

| Case | What Happens | Fix / Defensive Code |
|------|--------------|----------------------|
| **n == 0** | No deletion, just return the original list | The inner `for` loop simply skips, code still works |
| **m == 0** (invalid by constraints) | Would lose entire list | `for (int i=0; i<m; ++i)` does nothing → `prev` stays dummy, leading to empty list |
| **List shorter than m** | Keep everything | Outer `while (curr)` ends early; no deletion occurs |
| **List shorter than n after keeping** | Delete to the end | Inner `for` loop checks `curr != nullptr` before advancing |
| **Deletion touches the head** | Handled by dummy head | `prev` starts at dummy, so `prev.next = curr` can skip the original head |

> **Tip** – Always test with `m = 1`, `n = 1`, `m = n = 0` (if the judge ever loosens constraints).

---

## <a name="follow-up"></a>8️⃣ Follow‑Up: In‑Place vs. Extra Space  

**Question:** *Can we solve this with **O(n)** extra memory (e.g., by building an array or copying nodes)?*  
**Answer:** Absolutely – just convert to an array or use a recursive helper, but you lose the “in‑place” edge that the question explicitly wants.  
**Why stay in‑place?**  
- Interviews often test whether you can *reuse* the existing structure without allocating new nodes.  
- In real‑world code, you’d want to avoid a memory copy for performance and predictability.

---

## <a name="interview-tips"></a>9️⃣ Interview Tips & Tricks  

| Question | Your Talking Point | Code‑Ready Answer |
|----------|--------------------|--------------------|
| “What if `n` is 0?” | “We simply keep every `m` nodes; the delete loop is skipped.” | Show that the `for (int i = 0; i < n; ++i)` block runs zero times. |
| “Can you do it recursively?” | “Yes, but recursion uses stack space. For a list of 10 000 nodes, that’s a stack overflow.” | Offer the iterative solution first, then discuss recursion only as a theoretical alternative. |
| “Why the dummy head?” | “It removes special handling for when the head itself is deleted.” | Mention that it also guarantees `prev->next` is always valid. |
| “Complexity?” | “Linear time, constant space.” | Walk through the two nested loops: each node is processed a constant number of times. |

**STAR Method** – *Situation, Task, Action, Result*  
- **Situation**: “I was asked to delete every `n` nodes after keeping `m` nodes.”  
- **Task**: “Traverse the list once and modify it in place.”  
- **Action**: “Used a dummy node, two pointers, and two simple loops.”  
- **Result**: “O(n) time, O(1) space, no extra allocations.”

---

## <a name="seo-checklist"></a>🔍 SEO Checklist & Keywords  

| Category | Keyword | Why it matters |
|----------|---------|----------------|
| **Problem Title** | “LeetCode 1474 Delete N Nodes After M Nodes” | Searchers often type the exact problem number. |
| **Interview Focus** | “Linked List interview question”, “in‑place linked list deletion” | These are the phrases recruiters type. |
| **Languages** | “Java solution”, “Python solution”, “C++ solution” | Enables each language to appear in separate search results. |
| **Complexity** | “O(n) time linked list solution”, “constant space” | Recruiters look for efficient code. |
| **Extras** | “Java Python C++ solutions”, “algorithm interview” | Broadens the reach. |

> **Google‑style Meta‑Tags**  
> ```html
> <title>LeetCode 1474 – Delete N Nodes After M Nodes (Java, Python, C++)</title>
> <meta name="description" content="Clean, in‑place O(n) solution to LeetCode 1474 – Delete N Nodes After M Nodes of a Linked List. Java, Python, and C++ code + interview guide.">
> ```

---

## <a name="conclusion"></a>10️⃣ Conclusion & Final Thoughts  

| ✅ | **What you learned** |
|----|---------------------|
| 🔧 | Mastered pointer manipulation in a singly‑linked list. |
| 📦 | Delivered an **in‑place** algorithm (O(1) space). |
| ⏱️ | Achieved a single‑pass O(n) time complexity. |
| 🗣️ | Communicated the solution clearly in **Java**, **Python**, and **C++** – the three languages recruiters love. |
| 💡 | Avoided recursion and two‑pass traps – the “bad” approaches. |

> **Final tip** – During the interview, *talk through the dummy head idea first*. Most interviewers love to see you think about edge cases before writing code.

Happy coding and good luck crushing your next interview! 🚀

---

### 📌 Quick Reference Code (All Languages)

```text
Java:
  dummy → prev → curr
  for m steps: prev = curr; curr = curr.next;
  for n steps: curr = curr.next;
  prev.next = curr;

Python / C++ (same logic)

```

Feel free to copy, paste, and run these snippets on your own test harnesses or online judges. Happy hacking!