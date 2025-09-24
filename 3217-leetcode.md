---
title: LeetCode 3217. Delete Nodes From Linked List Present in Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. 3‑Way Code for LeetCode 3217  
**Delete Nodes From Linked List Present in Array**  
> **Difficulty** – Medium  
> **Tags** – Linked List, Hash Table, Two Pointers  

| Language | File name | Complexity |
|----------|-----------|------------|
| Java     | `Solution.java` | **O(n + m)** time, **O(m)** extra space |
| Python   | `solution.py` | **O(n + m)** time, **O(m)** extra space |
| C++      | `solution.cpp` | **O(n + m)** time, **O(m)** extra space |

> **n** – number of nodes in the linked list  
> **m** – size of the `nums` array

> All three solutions use the same idea:  
> 1. Put every value of `nums` into a hash set.  
> 2. Walk through the list once, skipping any node whose value is in the set.  
> 3. Use a dummy head to avoid edge‑case headaches.

---

### 1.1 Java

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
    public ListNode modifiedList(int[] nums, ListNode head) {
        // 1. Build a hash set from the array
        Set<Integer> toRemove = new HashSet<>();
        for (int num : nums) toRemove.add(num);

        // 2. Dummy node to simplify deletions at the head
        ListNode dummy = new ListNode(0, head);
        ListNode prev = dummy, cur = head;

        // 3. Walk through the list
        while (cur != null) {
            if (toRemove.contains(cur.val)) {
                // skip this node
                prev.next = cur.next;
            } else {
                prev = cur;
            }
            cur = cur.next;
        }

        return dummy.next;   // new head of the modified list
    }
}
```

---

### 1.2 Python

```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def modifiedList(self, nums: List[int], head: ListNode) -> ListNode:
        # 1. Set for O(1) lookups
        to_remove = set(nums)

        # 2. Dummy node
        dummy = ListNode(0, head)
        prev, cur = dummy, head

        # 3. Traverse once
        while cur:
            if cur.val in to_remove:
                prev.next = cur.next          # remove cur
            else:
                prev = cur
            cur = cur.next

        return dummy.next
```

---

### 1.3 C++

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
    ListNode* modifiedList(vector<int>& nums, ListNode* head) {
        unordered_set<int> toRemove(nums.begin(), nums.end());

        ListNode dummy(0, head);
        ListNode *prev = &dummy, *cur = head;

        while (cur) {
            if (toRemove.count(cur->val))
                prev->next = cur->next;   // skip cur
            else
                prev = cur;
            cur = cur->next;
        }
        return dummy.next;
    }
};
```

---

## 2. Blog Article – “Delete Nodes From Linked List Present in Array: The Good, The Bad, The Ugly”

> *SEO Keywords:*  
> Delete Nodes From Linked List Present in Array, LeetCode 3217, Linked List Removal, Hash Set, Interview Coding, Java Linked List, Python Linked List, C++ Linked List, Data Structures Interview

---

### 2.1 Problem Recap

You are given:
- An array `nums` of **unique** integers.
- A singly‑linked list `head`.

**Goal:** Remove **all** nodes from the linked list whose value appears in `nums`, and return the new head.  
Constraints: `1 <= nums.length, list.length <= 10^5`.  

> In interview terms: *“Can you prune a linked list in linear time given a set of target values?”*

---

### 2.2 The “Good” – Clean, Efficient, Production‑Ready

| What we do | Why it’s good |
|------------|---------------|
| **Hash set for `nums`** | O(1) average lookup, overall `O(m)` extra space. |
| **Single pass over the list** | `O(n)` time, no nested loops. |
| **Dummy head node** | Eliminates corner cases (deleting the first node, empty list after deletions). |
| **Iterative solution** | No risk of stack overflow, easier to debug. |

**Result:** Linear time, linear extra memory, clear code. This is the “canonical” solution most interviewers expect.

---

### 2.3 The “Bad” – What to Avoid

| Bad practice | Consequence |
|--------------|-------------|
| **Scanning the list for every value in `nums`** | `O(n * m)` time – disastrous for 10^5 nodes & 10^5 values. |
| **Recursively removing nodes** | Recursion depth = list length → stack overflow for long lists. |
| **Using a vector to check existence** | Linear search (`O(m)`) inside the loop → `O(n*m)` time. |
| **Ignoring the dummy head** | You’ll need many special‑case checks (if head is removed, if list becomes empty). |
| **Modifying the list while iterating with `for-each` on a copy** | Memory overhead + complexity. |

These patterns are typical “gotchas” that interviewers will flag.

---

### 2.4 The “Ugly” – Misleading Solutions

Sometimes, people attempt to be clever but end up with fragile code:

```java
// Ugly example: using removeIf on a LinkedList
public ListNode modifiedList(int[] nums, ListNode head) {
    List<Integer> list = new ArrayList<>();
    // build a Java LinkedList from ListNode ...
    list.removeIf(val -> contains(nums, val)); // O(n*m)
    // convert back ...
}
```

Why it’s ugly:
- **Two conversions** (list → linked list → list).  
- **High time complexity** due to repeated `contains`.  
- **Hard to read** – you’re hiding the true algorithm inside library calls.  

> *Rule of thumb:* If you can’t describe the algorithm in plain English in a couple of sentences, it’s probably overcomplicated.

---

### 2.5 Edge Cases Worth Testing

| Case | Why it matters |
|------|----------------|
| `head` has *only* nodes that need to be removed | Result must not be `null`. |
| `nums` contains a value not present in the list | No nodes should be removed. |
| Large values (up to 10^5) | Hash set handles them; no overflow. |
| List is very long (10^5 nodes) | Recursion will fail; iterative required. |
| Empty `nums` (not allowed by constraints) | Just return the original list. |

Write unit tests that cover all these scenarios.

---

### 2.6 Why This Matters for Your Interview

- **Data‑structure mastery**: Demonstrates knowledge of linked lists, hash sets, and memory management.  
- **Time/space trade‑offs**: Shows you can reason about asymptotic performance.  
- **Coding style**: Clean, commented, and concise code is a plus.  
- **Problem‑solving mindset**: Ability to spot and avoid the “bad” and “ugly” patterns.

> *Tip:* In an interview, start by explaining your plan (“I’ll hash the array and sweep through the list”), then write the code, and finally discuss complexity.

---

### 2.7 Takeaway

- **Use a hash set** – O(1) membership tests.  
- **Traverse once** – linear time.  
- **Dummy node** – eliminates edge‑case headaches.  
- **Avoid nested loops or recursion** for large data.  
- **Test thoroughly** – edge cases often trip up novices.

With these guidelines, you can confidently solve **LeetCode 3217** and ace the linked‑list portion of any coding interview.

---

### 2.8 Quick Reference Cheat Sheet

| Step | Java | Python | C++ |
|------|------|--------|-----|
| Build set | `Set<Integer> s = new HashSet<>(Arrays.asList(nums));` | `s = set(nums)` | `unordered_set<int> s(nums.begin(), nums.end());` |
| Dummy head | `ListNode dummy = new ListNode(0, head);` | `dummy = ListNode(0, head)` | `ListNode dummy(0, head);` |
| Traverse | `while(cur){ if(s.contains(cur.val)) prev.next=cur.next; else prev=cur; cur=cur.next; }` | `while(cur): if cur.val in s: prev.next = cur.next else: prev = cur; cur = cur.next` | `while(cur){ if(s.count(cur->val)) prev->next=cur->next; else prev=cur; cur=cur->next; }` |
| Return | `return dummy.next;` | `return dummy.next` | `return dummy.next;` |

Happy coding, and may your job interview be as smooth as a well‑trimmed linked list!