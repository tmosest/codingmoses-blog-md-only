---
title: LeetCode 25. Reverse Nodes in k-Group - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Reverse Nodes in K‑Group – A Complete Guide (Java | Python | C++)

> **Keywords:** Reverse Nodes in K-Group, LeetCode 25, Linked List, Interview, Java, Python, C++, O(1) space, Recursion, Iteration

---

### 1️⃣ Introduction

Linked lists are a staple of technical interviews.  
**Reverse Nodes in K‑Group** (LeetCode 25) is a classic “medium‑hard” problem that tests your ability to:

* Manipulate pointers carefully  
* Handle edge cases (less than *k* nodes left)  
* Optimize for O(1) extra space  

The solution is often a turning point between a solid candidate and a “just passed” one. This article walks you through the **good**, the **bad**, and the **ugly** parts of the problem, and provides clean, production‑ready code in **Java, Python, and C++**.

---

### 2️⃣ Problem Statement (Simplified)

> Given a singly linked list, reverse the nodes **k** at a time and return the modified list.  
> *If the number of nodes is not a multiple of k, the remaining nodes stay in original order.*

```text
Input : 1 -> 2 -> 3 -> 4 -> 5, k = 2
Output: 2 -> 1 -> 4 -> 3 -> 5
```

Constraints:

* 1 ≤ k ≤ n ≤ 5000  
* 0 ≤ Node.val ≤ 1000  

---

### 3️⃣ Analysis & Strategy

| Good | Bad | Ugly |
|------|-----|------|
| **Good** – The problem reduces to *“reverse every sub‑segment of size k”*. | **Bad** – Off‑by‑one errors in pointer updates are common. | **Ugly** – Recursive depth can blow up if k = 1, making O(n) stack usage. |

The core operation is **in‑place reversal** of a sub‑segment.  
We have two popular patterns:

1. **Recursive** – clean and intuitive, but O(n) stack space.  
2. **Iterative with dummy head** – O(1) extra memory, slightly more verbose but perfect for interviewers who care about space.

Both patterns share a helper that finds the *k‑th node* from a given start and a routine that reverses pointers between two nodes.

---

### 4️⃣ Solution Walk‑through

#### 4.1 Helper: Find k‑th Node

```text
start -> 1 -> 2 -> 3 -> 4 -> 5
k = 3
```

Starting from `start`, move `k` steps.  
If we reach `null` before taking `k` steps, the segment is too short.

#### 4.2 Reversing a Segment

We reverse pointers between `head` (inclusive) and `tail` (exclusive).  
Typical three‑pointer approach:

```
prev = null
cur  = head
while cur != tail:
    nxt   = cur.next
    cur.next = prev
    prev = cur
    cur = nxt
return prev   // new head of reversed segment
```

#### 4.3 Iterative Algorithm (O(1) space)

1. Create a dummy node pointing to `head`.  
2. `groupPrev` = dummy.  
3. While true:  
   * Find `k`‑th node after `groupPrev`. If none → break.  
   * `groupNext` = `kth.next`.  
   * Reverse from `groupPrev.next` up to `groupNext`.  
   * Reconnect:  
     * `groupPrev.next` = `kth` (new head)  
     * `oldHead.next` = `groupNext`  
     * Move `groupPrev` to `oldHead` (which becomes the tail of the reversed segment).  
4. Return `dummy.next`.

---

### 5️⃣ Complexity

| Metric | Recursion | Iteration |
|--------|-----------|-----------|
| Time   | O(n)      | O(n)      |
| Space  | O(n/k) (stack) | O(1)      |

---

### 6️⃣ Code

#### 6.1 Java (Iterative)

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
public class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null || k == 1) return head;

        // Dummy node to handle head changes
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode groupPrev = dummy;

        while (true) {
            // Find the k-th node
            ListNode kth = getKthNode(groupPrev, k);
            if (kth == null) break;
            ListNode groupNext = kth.next;

            // Reverse this group
            ListNode prev = kth.next;
            ListNode cur  = groupPrev.next;
            while (cur != groupNext) {
                ListNode tmp = cur.next;
                cur.next = prev;
                prev = cur;
                cur = tmp;
            }

            // Reconnect
            ListNode tmp = groupPrev.next;   // old head becomes tail
            groupPrev.next = kth;            // new head
            groupPrev = tmp;                 // move to next group
        }

        return dummy.next;
    }

    // Returns the k-th node from start, or null if not enough nodes
    private ListNode getKthNode(ListNode start, int k) {
        while (start != null && k > 0) {
            start = start.next;
            k--;
        }
        return start;
    }
}
```

#### 6.2 Python (Iterative)

```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        if not head or k == 1:
            return head

        dummy = ListNode(0, head)
        group_prev = dummy

        while True:
            kth = self.get_kth_node(group_prev, k)
            if not kth:
                break
            group_next = kth.next

            # Reverse group
            prev, cur = kth.next, group_prev.next
            while cur != group_next:
                nxt = cur.next
                cur.next = prev
                prev = cur
                cur = nxt

            # Reconnect
            tmp = group_prev.next
            group_prev.next = kth
            group_prev = tmp

        return dummy.next

    def get_kth_node(self, start: ListNode, k: int) -> ListNode:
        while start and k:
            start = start.next
            k -= 1
        return start
```

#### 6.3 C++ (Iterative)

```cpp
/* Definition for singly-linked list. */
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(nullptr) {}
};

class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        if (!head || k == 1) return head;
        ListNode dummy(0);
        dummy.next = head;
        ListNode* groupPrev = &dummy;

        while (true) {
            ListNode* kth = getKthNode(groupPrev, k);
            if (!kth) break;
            ListNode* groupNext = kth->next;

            // Reverse
            ListNode* prev = groupNext;
            ListNode* cur  = groupPrev->next;
            while (cur != groupNext) {
                ListNode* nxt = cur->next;
                cur->next = prev;
                prev = cur;
                cur = nxt;
            }

            // Reconnect
            ListNode* tmp = groupPrev->next;   // old head
            groupPrev->next = kth;            // new head
            groupPrev = tmp;                  // move to next group
        }
        return dummy.next;
    }

private:
    ListNode* getKthNode(ListNode* start, int k) {
        while (start && k) {
            start = start->next;
            --k;
        }
        return start;
    }
};
```

---

### 7️⃣ Common Pitfalls & How to Avoid Them

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Off‑by‑one** when finding kth node | Moving k times from `groupPrev` may return `null` even if segment is valid. | Use a *dummy* and **strict** loop (`while (k-- > 0)`) to ensure correct count. |
| **Dangling pointer** after reversal | Forgetting to set `groupPrev.next` before the loop. | Always re‑connect the group after reversal; use temporary variables. |
| **Recursion depth** | k = 1 ⇒ each call processes one node → n calls. | Prefer iterative solution if interviewers mention O(1) space. |
| **Modifying the input list outside the function** | Accidentally returning a node that still points to a reversed segment. | Return `dummy.next` (or the original head if no reversal). |
| **Null pointer dereference** | Calling `kth.next` when `kth` is `null`. | Check `kth == null` **before** using it. |

---

### 8️⃣ Interview‑Friendly Tips

1. **Start with a dummy node** – It eliminates special handling for the head.  
2. **Write a helper for `getKthNode`** – Keeps the main loop clean.  
3. **Use a three‑pointer reversal** – Demonstrates mastery over pointer manipulation.  
4. **Explain your reconnection logic** – Interviewers love seeing you articulate why `groupPrev` moves to the tail of the reversed segment.  
5. **Show complexity** – Mention O(1) space and why the iterative version is preferable for large lists.  

---

### 9️⃣ Variants & Extensions

| Variant | What changes | How to adapt |
|---------|--------------|--------------|
| **Reverse in reverse order (k = n)** | Whole list reversed | Same algorithm with k = list length |
| **Batch reversal with variable k** | k may change per segment | Store k per segment or pass an array |
| **Doubly linked list** | You can use `.prev` to simplify reversal | Swap `.next` and `.prev` pointers in the loop |

---

### 10️⃣ Conclusion

*Reverse Nodes in K‑Group* is not just a pointer exercise – it’s a **diagnostic** of how you think about data structures, edge cases, and memory management.  
By mastering the iterative O(1) space solution (shown above) and being ready to discuss the recursive alternative, you’ll demonstrate both **clean code** and **engineering awareness** – exactly what hiring managers look for.

**Next Up:** Other LeetCode Linked‑List challenges like *“Remove Nth Node From End of List”* (25) and *“Merge k Sorted Lists”* (23) build on the same fundamentals. Keep practicing!

---

### 🔗 References

* LeetCode Problem 25: <https://leetcode.com/problems/reverse-nodes-in-k-group/>
* YouTube tutorial by the OP (good, bad, ugly): <https://youtu.be/your_video_link_here>
* Official Java/Python/C++ definitions – <https://leetcode.com/problems/reverse-nodes-in-k-group/solutions/>

---

**Happy coding and good luck on your next interview!**