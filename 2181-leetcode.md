---
title: LeetCode 2181. Merge Nodes in Between Zeros - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 2181 – Merge Nodes in Between Zeros  
*Linked‑List, O(n) – One Pass, O(1) Space*  

---

### 1️⃣ Problem Overview  
You’re given a singly‑linked list that starts and ends with a node of value **0**.  
Between every pair of zeros there is a non‑empty block of positive numbers.  
For each block you must **sum** all its values and replace the block with a **single node** that contains that sum.  
All zero nodes are removed in the final list.

```
Input : 0 → 3 → 1 → 0 → 4 → 5 → 2 → 0
Output: 4 → 11
```

> **Constraints**  
> • 3 ≤ n ≤ 2·10⁵  
> • 0 ≤ Node.val ≤ 1000  
> • No two consecutive zeros  
> • Head and tail are always 0

---

## 💡 Core Insight  

*You only need one scan of the list.*  
Walk through the list, keep a running sum until you hit the next zero.  
When a zero is reached:  

1. Write the accumulated sum into the node *just before* the zero.  
2. Skip the zero and any following nodes that belong to the next block.  

Using a **dummy head** (or just starting at `head.next`) makes the pointer updates trivial and eliminates the need for edge‑case checks.

---

## 📄 Reference Implementations

### A. Java – Leetcode‑style

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeNodes(ListNode head) {
        // head is the first zero, we start with the node after it
        ListNode curr = head.next;   // node where we will write the sum
        ListNode runner = curr;      // pointer that moves until the next zero

        while (runner != null) {
            int sum = 0;

            // accumulate until we hit a zero
            while (runner.val != 0) {
                sum += runner.val;
                runner = runner.next;
            }

            // replace current node's value with the sum
            curr.val = sum;

            // skip the zero and advance curr to the start of next block
            runner = runner.next;   // move past the zero
            curr.next = runner;     // connect to the next block (or null)
            curr = curr.next;       // move curr forward
        }

        // head.next is the new head of the merged list
        return head.next;
    }
}
```

---

### B. Python – Leetcode‑style

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

class Solution:
    def mergeNodes(self, head: ListNode) -> ListNode:
        # head is zero, start with the node after it
        curr = head.next          # node that will receive the sum
        runner = curr

        while runner:
            total = 0
            while runner.val != 0:
                total += runner.val
                runner = runner.next

            curr.val = total          # write the sum
            runner = runner.next      # skip the zero
            curr.next = runner
            curr = curr.next

        return head.next
```

---

### C. C++ – Leetcode‑style

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
    ListNode* mergeNodes(ListNode* head) {
        // head is the first zero, start from the following node
        ListNode* curr = head->next;   // node to write the sum
        ListNode* runner = curr;

        while (runner) {
            int sum = 0;
            while (runner->val != 0) {
                sum += runner->val;
                runner = runner->next;
            }

            curr->val = sum;          // write the sum
            runner = runner->next;    // skip the zero
            curr->next = runner;
            curr = curr->next;
        }
        return head->next;            // new head
    }
};
```

---

## 📚  The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | O(n) – one pass, optimal | – | – |
| **Space Complexity** | O(1) – in‑place modifications | – | – |
| **Edge‑case Handling** | Dummy node or `head.next` eliminates boundary checks | Forgetting to skip the zero can create an infinite loop | Using extra memory (e.g., stack) when not needed |
| **Readability** | Clear variable names (`curr`, `runner`, `sum`) | Verbose pointer gymnastics can confuse | Using recursion on a linked list may overflow stack |
| **Maintainability** | Minimal state, straightforward updates | None | Hard‑to‑read nested loops with many breaks |

---

## 🔧 Common Pitfalls & How to Avoid Them

1. **Missing the final zero**  
   *If you stop before the tail zero, you’ll miss the last block.*  
   ✅ **Solution** – Always check `runner != null` in the outer loop and finish the inner loop when `runner->val == 0`.

2. **Overwriting nodes before finishing a block**  
   *Updating `curr->val` too early will lose data for the next sum.*  
   ✅ **Solution** – Compute `sum` first, then write it after the inner loop finishes.

3. **Null‑pointer dereference**  
   *Running `runner->val` when `runner` is `nullptr`.*  
   ✅ **Solution** – Guard the outer loop with `while (runner != nullptr)`.

4. **Not updating `curr->next` correctly**  
   *Skipping too many or too few nodes.*  
   ✅ **Solution** – After computing the sum, do `runner = runner->next` (skip the zero) and then `curr->next = runner`.

---

## 📈 Why This Problem Rocks for Interviews

* **Linked‑List Mastery** – Every senior developer needs to be comfortable manipulating pointers.
* **One‑pass, O(1) solution** – Demonstrates algorithmic efficiency.
* **Edge‑Case Awareness** – The “no two consecutive zeros” and “head/tail are zeros” rules are easy to miss.
* **Talk‑through** – You can discuss time/space trade‑offs and justify your dummy‑node approach.

---

## 🛠️ How to Show This on Your Resume

```text
- Implemented O(n) solutions for “Merge Nodes in Between Zeros” (Leetcode #2181), a linked‑list algorithmic challenge.
- Optimized space complexity to O(1) by performing in‑place node updates and eliminating auxiliary data structures.
- Demonstrated robust pointer manipulation skills in Java, Python, and C++ across multiple platforms.
```

> Highlight *time/space*, *in‑place*, *pointer gymnastics*, *single‑pass*—keywords recruiters in tech companies love.

---

## 🎤 Interview Prep Checklist

1. **Explain the problem** (use the description and constraints).  
2. **Sketch the algorithm** on a whiteboard or paper:  
   *Traverse → Sum → Write → Skip zero → Repeat.*  
3. **State complexity** and why it’s optimal.  
4. **Run through edge‑cases** (single block, long list, etc.).  
5. **Show code** in at least one language, then mention you can port it.  
6. **Answer “What if we had more zeros?”** – Use the same approach, it will still be O(n).

---

## 📌 SEO‑Friendly Takeaway

> If recruiters are scanning for “Linked‑List Interview Questions”, “Leetcode 2181”, or “Merge Nodes in Between Zeros” solutions in Java, Python, or C++, this article is a perfect fit.  
> Use the keywords **Linked List, Merge Nodes, Leetcode 2181, Java, Python, C++, One Pass, O(n), O(1) Space** in blog titles, meta descriptions, and headings to improve discoverability.

---

## 📌 Quick‑Start Test Harness (Python)

```python
def build_list(nums):
    dummy = ListNode(0)
    tail = dummy
    for n in nums:
        tail.next = ListNode(n)
        tail = tail.next
    return dummy.next

def print_list(head):
    vals = []
    while head:
        vals.append(str(head.val))
        head = head.next
    print(" → ".join(vals))

# Example
head = build_list([0,3,1,0,4,5,2,0])
print("Before:", end=" ")
print_list(head)

merged = Solution().mergeNodes(head)
print("After :", end=" ")
print_list(merged)
```

---

## 📚 Further Reading

| Problem | What it Teaches | Link |
|---------|----------------|------|
| Leetcode 234 – **Palindrome Linked List** | Two‑pointer + reverse middle | https://leetcode.com/problems/palindrome-linked-list/ |
| Leetcode 2 – **Add Two Numbers** | In‑place sum with carry | https://leetcode.com/problems/add-two-numbers/ |
| Leetcode 443 – **String Compression** | In‑place array manipulation | https://leetcode.com/problems/string-compression/ |

---

### 🚀 Final Thought  
Mastering **Merge Nodes in Between Zeros** showcases your ability to think in terms of **pointers**, **time‑space optimization**, and **robust edge‑case handling**.  
These are the exact qualities that hiring managers in software engineering teams are looking for.  
Add the code snippets above to your portfolio, run them on Leetcode or your own IDE, and be ready to discuss the trade‑offs in your next interview. Good luck, and may the code be with you!