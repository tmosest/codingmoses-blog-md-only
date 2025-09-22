---
title: LeetCode 2130. Maximum Twin Sum of a Linked List - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 LeetCode 2130 – *Maximum Twin Sum of a Linked List*  
> **Target language stack:** **Java, Python, C++**  
> **Time Complexity:** `O(n)`  
> **Space Complexity:** `O(1)` (in‑place)

---

### 1️⃣ Problem Recap  

> *You are given a singly‑linked list with an **even** number of nodes.  
>  The “twin” of node `i` is node `n‑1‑i`.  
>  The twin sum is `value(i) + value(n‑1‑i)`.  
>  Return the **maximum twin sum**.*

Examples  

| Input | Output | Explanation |
|-------|--------|-------------|
| `[5,4,2,1]` | `6` | 5+1 = 6, 4+2 = 6 → max = 6 |
| `[4,2,2,3]` | `7` | 4+3 = 7, 2+2 = 4 → max = 7 |
| `[1,100000]` | `100001` | only one pair |

> **Constraints**  
> * `2 <= n <= 10⁵` (n is even)  
> * `1 <= node.val <= 10⁵`

---

### 2️⃣ Algorithm Overview

1. **Find the middle of the list**  
   *Two‑pointer (slow/fast)*: `slow` moves one step, `fast` two steps.  
   When `fast` reaches the end, `slow` is at node `n/2`.

2. **Reverse the second half**  
   Reverse the list starting at `slow`.  
   Now each twin pair is in adjacent nodes: first half node ↔ reversed second half node.

3. **Traverse both halves simultaneously**  
   Walk from the original head and the head of the reversed second half, summing node values and keeping the maximum.

4. **(Optional) Restore the list** – not required for the LeetCode answer but good practice for interviewers.

The trick is to do all steps *in‑place* so that memory usage stays `O(1)`.

---

### 3️⃣ Reference Code

> **Java**  
> **Python**  
> **C++**  

> All three implementations share the same logic – just different syntax.

```markdown
## 🔧 Java 17

```java
/**
 * Definition for singly-linked list.
 */
class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; }
}

public class Solution {
    public int pairSum(ListNode head) {
        // 1️⃣ Find middle
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        // 2️⃣ Reverse second half
        ListNode prev = null, curr = slow;
        while (curr != null) {
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }

        // 3️⃣ Compute twin sums
        int maxSum = 0;
        ListNode p1 = head, p2 = prev;
        while (p2 != null) {      // p2 has n/2 nodes
            maxSum = Math.max(maxSum, p1.val + p2.val);
            p1 = p1.next;
            p2 = p2.next;
        }
        return maxSum;
    }
}
```

```markdown
## 🔧 Python 3

```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def pairSum(self, head: ListNode) -> int:
        # 1️⃣ Find middle
        slow = fast = head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next

        # 2️⃣ Reverse second half
        prev = None
        curr = slow
        while curr:
            nxt = curr.next
            curr.next = prev
            prev = curr
            curr = nxt

        # 3️⃣ Compute twin sums
        max_sum = 0
        p1, p2 = head, prev
        while p2:                 # p2 has n/2 nodes
            max_sum = max(max_sum, p1.val + p2.val)
            p1 = p1.next
            p2 = p2.next
        return max_sum
```

```markdown
## 🔧 C++17

```cpp
/** 
 * Definition for singly-linked list.
 */
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(nullptr) {}
};

class Solution {
public:
    int pairSum(ListNode* head) {
        // 1️⃣ Find middle
        ListNode *slow = head, *fast = head;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
        }

        // 2️⃣ Reverse second half
        ListNode *prev = nullptr, *curr = slow;
        while (curr) {
            ListNode *nxt = curr->next;
            curr->next = prev;
            prev = curr;
            curr = nxt;
        }

        // 3️⃣ Compute twin sums
        int maxSum = 0;
        ListNode *p1 = head, *p2 = prev;
        while (p2) {            // p2 has n/2 nodes
            maxSum = std::max(maxSum, p1->val + p2->val);
            p1 = p1->next;
            p2 = p2->next;
        }
        return maxSum;
    }
};
```

---

### 4️⃣ The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time** | O(n) – single pass to find middle + reverse + compute | None | None |
| **Space** | O(1) – in‑place reversal | Slight temporary stack usage if recursion is chosen | Recursion depth of n → stack overflow |
| **Readability** | Clear separation: find middle → reverse → compute | Complex pointer gymnastics can be confusing | Not reversing (using an array) uses O(n) space – “the easiest but not the best” |
| **Edge Cases** | Handles n = 2 (single twin pair) | None | Wrong middle when list length is not even (but problem guarantees even) |
| **Interview Tactics** | Show awareness of two‑pointer technique | Ask for alternative (stack, array) – discuss trade‑offs | Be ready to restore original list if asked |

---

### 5️⃣ Why This Solution Rocks for Interviews

1. **Shows two‑pointer mastery** – a classic interview pattern.  
2. **In‑place reversal** demonstrates careful pointer manipulation and memory awareness.  
3. **Single pass (after reverse)** shows algorithmic efficiency.  
4. **Clean code** – easy to understand, testable, and scalable.  

---

### 6️⃣ Testing & Edge‑Case Checklist

| Test | Input | Expected | Why |
|------|-------|----------|-----|
| Even length, large | `[1,2,3,4,5,6]` | `11` | 1+6, 2+5, 3+4 → max 11 |
| Minimal length | `[2,3]` | `5` | only one twin pair |
| All same values | `[4,4,4,4]` | `8` | 4+4 pairs |
| High values | `[100000,1,1,100000]` | `200000` | 100000+100000 |
| Random order | `[7,8,2,9,3,6]` | `15` | 7+6, 8+3, 2+9 |

Use these as unit tests in your IDE or online editor.

---

### 7️⃣ SEO‑Optimized Takeaway

- **Title** – “Maximum Twin Sum of a Linked List – LeetCode 2130 | Java / Python / C++ Solutions”
- **Meta Description** – “Learn how to solve LeetCode 2130 (Maximum Twin Sum of a Linked List) in Java, Python, and C++. Master two‑pointer, in‑place reversal, and interview‑friendly code!”
- **Keywords** – LeetCode 2130, Maximum Twin Sum, Linked List problem, two‑pointer, reverse linked list, interview question, Java solution, Python solution, C++ solution.
- **Headers** – Use `H1` for title, `H2` for sections, `H3` for sub‑topics.
- **Internal Links** – Link to other LeetCode problems (e.g., “Reverse Linked List”, “Two Sum”) to improve crawlability.
- **Call‑to‑Action** – “Download the code, practice, and share your own solutions on GitHub to showcase your coding chops!”

---

### 8️⃣ Final Thoughts

- **Keep the code tidy** – separate helper functions if needed.  
- **Explain your approach** – talk about the time/space trade‑offs.  
- **Show awareness of pitfalls** – e.g., not handling odd length (even though the problem guarantees it).  
- **Be ready to tweak** – if asked to preserve the list, you can reverse the second half back before returning.

Good luck on your next interview! 🚀  

Happy coding!