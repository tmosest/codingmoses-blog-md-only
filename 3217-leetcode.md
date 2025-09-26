---
title: LeetCode 3217. Delete Nodes From Linked List Present in Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap

> **LeetCode #3217 – Delete Nodes From Linked List Present in Array**  
> Given an array `nums` and the head of a singly‑linked list, remove **all** nodes whose values appear in `nums`.  
> Return the head of the modified list.

**Constraints**

| Constraint | Value |
|------------|-------|
| `1 ≤ nums.length ≤ 10⁵` | |
| `1 ≤ nums[i] ≤ 10⁵` | |
| All `nums` elements are unique | |
| Linked‑list size `1 … 10⁵` | |
| `1 ≤ node.val ≤ 10⁵` | |
| At least one node is **not** removed | |

The problem is a classic interview question that tests:
* hash‑set usage
* linked‑list manipulation
* time/space trade‑offs

Below you’ll find clean, production‑ready solutions in **Java, Python, and C++** – all using a single traversal of the list and an O(1) lookup set.  
After the code you’ll find a *blog‑style guide* that explains the “good, the bad, and the ugly” of this problem and is SEO‑optimized to help you land your next coding interview.

---

## 2.  Reference Solutions

> ⚙️ All three implementations use the same idea:
> 1. Build a hash‑set (`unordered_set` / `HashSet` / `set`) from `nums`.  
> 2. Walk the linked list with a dummy head.  
> 3. Skip nodes whose value is in the set, otherwise keep them.  
> 4. Return `dummy.next`.

### 2.1 Java

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
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public ListNode deleteNodes(int[] nums, ListNode head) {
        // 1. Build the set of values to delete
        Set<Integer> toDelete = new HashSet<>(nums.length);
        for (int v : nums) toDelete.add(v);

        // 2. Dummy head simplifies edge cases
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode prev = dummy, curr = head;

        // 3. Walk the list once
        while (curr != null) {
            if (toDelete.contains(curr.val)) {
                // skip this node
                prev.next = curr.next;
            } else {
                prev = curr;                // keep it
            }
            curr = curr.next;
        }
        return dummy.next;
    }
}
```

---

### 2.2 Python

```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def deleteNodes(self, nums, head):
        # 1. Build the set of values to delete
        to_delete = set(nums)

        # 2. Dummy head to handle deletions at the front
        dummy = ListNode(0)
        dummy.next = head
        prev, curr = dummy, head

        # 3. Single pass
        while curr:
            if curr.val in to_delete:
                prev.next = curr.next          # skip
            else:
                prev = curr                    # keep
            curr = curr.next

        return dummy.next
```

---

### 2.3 C++

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(nullptr) {}
 * };
 */
#include <unordered_set>

class Solution {
public:
    ListNode* deleteNodes(vector<int>& nums, ListNode* head) {
        // 1. Hash‑set for O(1) lookups
        unordered_set<int> toDelete(nums.begin(), nums.end());

        // 2. Dummy node to avoid special cases
        ListNode dummy(0);
        dummy.next = head;
        ListNode *prev = &dummy, *curr = head;

        // 3. One pass
        while (curr) {
            if (toDelete.count(curr->val)) {
                prev->next = curr->next;          // skip
            } else {
                prev = curr;                      // keep
            }
            curr = curr->next;
        }
        return dummy.next;
    }
};
```

---

## 3.  Blog Article – *The Good, The Bad, and The Ugly of Deleting Nodes from a Linked List*

> **SEO Keywords**: delete nodes from linked list, linked list interview questions, leetcode 3217, Java linked list, Python linked list, C++ linked list, interview tips, hash set, time complexity, space complexity, job interview coding

---

### 3.1 Introduction

Linked lists are the **foundation** of many interview questions.  
One of the most common twists: “remove all nodes that contain a value from a given array.”  
You’ve probably seen it on LeetCode (Problem 3217) or in a technical interview.  
It looks simple, but getting the corner cases right and delivering an optimal solution can be tricky.

Let’s break down **the good**, **the bad**, and **the ugly** of this problem.

---

### 3.2 The Good: Why It’s a Great Interview Test

| Why | How it tests you |
|-----|------------------|
| **Data structure manipulation** | You must understand pointers (or references) and how to safely change `next` links. |
| **Hash‑set usage** | You must convert an array into a set for O(1) look‑ups. |
| **Single‑pass requirement** | You should realize you can’t afford to traverse the list multiple times. |
| **Edge‑case handling** | Removing the head, all nodes, or no nodes – all must be handled gracefully. |
| **Scalability** | The input size can be up to 100 k – so O(n) time and O(1) extra space is the sweet spot. |

For interviewers, it’s a **compact “do you understand pointers + hash‑set?”** question that yields a quick quality signal.

---

### 3.3 The Bad: Common Pitfalls

| Pitfall | Why it breaks |
|---------|---------------|
| **Not using a dummy head** | If the first node is deleted you’ll lose the reference to the new head. |
| **Modifying `next` incorrectly** | Assigning `curr = curr.next` *after* deleting can skip a node or create a cycle. |
| **Wrong data‑type for the set** | Using a list or array for look‑ups gives O(n) per node → O(n²) time. |
| **Assuming input array is sorted** | Many candidates mistakenly try binary search; the array may be unsorted. |
| **Not handling “no deletions”** | Some solutions return `null` when the list is unchanged. |
| **Wrong return type** | Returning the dummy node instead of `dummy.next` gives an empty list. |

Avoiding these pitfalls shows you understand linked‑list memory layout and set theory.

---

### 3.4 The Ugly: “What If” Variants

| Variant | Challenge |
|---------|-----------|
| **Delete nodes by *value* instead of *index*** | You must convert values into a set, not positions. |
| **Large node values** | The array may contain values up to 10⁵, but you must still keep O(1) lookup. |
| **Multithreaded deletion** | (Rarely asked) you’d need to lock the list or use a concurrent data structure. |
| **Linked list with random pointers** | If it were a *doubly* linked list or had `prev`, you’d need to update both sides. |
| **Immutable list** | Some languages expose immutable lists; you’d have to build a new list. |

In a real interview, the “ugly” variants often come as **curve‑ball follow‑ups** (“What if the array is huge? What if the list is a double‑linked list?”).  

Show you can **adapt** the core idea (hash‑set + single pass) to new constraints.

---

### 3.5 Optimal Solution Walk‑through

```text
1. Build a hash‑set from the array.
2. Create a dummy node pointing to the original head.
3. Iterate with two pointers: prev (starting at dummy) and curr.
4. If curr.val is in the set: prev.next = curr.next  // skip
   Else: prev = curr                                 // keep
5. Move curr = curr.next.
6. Return dummy.next.
```

*Time*: **O(n + m)** → O(n) because `m` = `nums.length`  
*Space*: **O(m)** for the set, **O(1)** additional pointers.

With `m ≤ 10⁵`, a hash‑set is the only realistic way to stay within the time limits.  
All languages’ standard libraries give you an **unordered** or **hash** structure that does exactly that.

---

### 3.6 Edge‑Case Checklist

| Edge case | What to check |
|-----------|---------------|
| Head is deleted | Dummy head keeps the new head. |
| Tail is deleted | `prev.next` correctly becomes `null`. |
| All nodes deleted except one | Loop continues until `curr` is `null`. |
| No node is deleted | `prev` always moves forward, `dummy.next` remains the original head. |
| Empty array (won’t happen due to constraints) | You can guard against it, but the spec guarantees at least one value. |

Testing against a small driver like:

```python
# build list: 1->2->3->4
head = ListNode(1, ListNode(2, ListNode(3, ListNode(4))))
nums = [2, 4]
result = Solution().deleteNodes(nums, head)
# result should be 1->3
```

ensures you’re ready for the “no‑deletion” corner case.

---

### 3.6 Interview‑Ready Tips

| Tip | Reason |
|-----|--------|
| **Explain your plan before coding** | Shows you can think on the fly. |
| **Talk through pointer updates** | Helps avoid accidental cycles. |
| **Ask clarifying questions** | “Is the array sorted?” “Do we need to preserve order?” |
| **Mention time/space trade‑offs** | Impress the interviewer that you’re aware of algorithmic constraints. |
| **Write a small test harness** | In the interview you can run a quick mental test or even a quick Python REPL to prove your logic. |
| **Use a dummy head** | It’s a safe‑guard you’re never going to forget. |

---

### 3.7 How This Article Helps You Land a Job

* The **code samples** give you a quick copy‑paste reference.  
* The **blog sections** highlight common interview traps, so you can brag about *not* falling for them.  
* The **SEO tags** help recruiters find this article when searching for “linked list interview questions.”  

**Next Step**: Paste any of the reference solutions into your IDE, run the sample tests, and feel confident explaining the algorithm in your next interview.

---

### 3.8 Closing Thought

Linked lists will stay a staple of coding interviews for years.  
Mastering this “delete nodes” problem not only gives you a **winning algorithm** but also demonstrates:

1. **Pointers are not just a theoretical concept** – they’re a real‑world tool.
2. **Hash‑sets are your fastest lookup friend** when time matters.
3. **Single‑pass solutions are the gold standard** for interview‑size constraints.

Show up, bring a dummy head, and you’ll walk away with more than just a correct answer—you’ll walk away with a signal that you *really understand* data‑structures.  

Good luck, and happy coding!