---
title: LeetCode 3217. Delete Nodes From Linked List Present in Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Delete Nodes From Linked List Present in Array – A Full‑Stack Walkthrough  
*(Java | Python | C++)*  

> **Why you should care**  
> The “Delete Nodes From Linked List Present in Array” problem is a frequent interview question on LeetCode, HackerRank, and even in real‑world tech interviews. Mastering it shows you can:  
> • Handle two data‑structures at once (array + linked list)  
> • Use hash‑based lookup for O(1) search  
> • Write clean, production‑ready code in multiple languages  

Below you’ll find a **step‑by‑step solution**, an **SEO‑optimized blog post** that explains the *good*, *bad*, and *ugly* of typical solutions, and **fully‑working code** in **Java, Python, and C++**.

---

# 📄 Problem Statement

Given an array `nums` (unique integers) and the head of a singly‑linked list `head`, **remove every node whose value is present in `nums`** and return the head of the modified list.

*Constraints*  

- `1 ≤ nums.length ≤ 10⁵`  
- `1 ≤ nums[i] ≤ 10⁵`  
- All elements in `nums` are unique.  
- `1 ≤ number of nodes ≤ 10⁵`  
- `1 ≤ Node.val ≤ 10⁵`  
- There is at least one node whose value is **not** in `nums`.

---

# 🔍 The Good, The Bad, The Ugly

| Aspect | Good (Typical Interview‑Ready) | Bad (Common Pitfalls) | Ugly (Avoid) |
|--------|--------------------------------|-----------------------|--------------|
| **Data structure for lookup** | `HashSet<Integer>` (Java), `unordered_set<int>` (C++), `set` (Python) – O(1) average lookup | Using an array of size 10⁵ → wasted memory if numbers are sparse | Using a linked list to search for each node → O(n²) |
| **Edge cases** | Empty list after deletion (return `null`), or head itself removed | Forgetting to detach the tail (`next = nullptr`) | Modifying the original list without creating a new dummy head (risk of memory leak) |
| **Complexity** | `O(n + m)` time, `O(n)` extra space | `O(n*m)` time if naive | O(1) extra space but still O(n²) time |
| **Code readability** | Clear variable names, comments, separate helper functions | Too many nested `if` statements | Over‑optimising prematurely (bitset hacks) |

---

# 📚 Approach (Hash‑Set + Dummy Head)

1. **Build a hash set** from `nums`.  
   - Enables constant‑time membership checks.
2. **Create a dummy head** (`new ListNode(0)`).
3. **Traverse the original list** using a pointer `curr`.  
   - If `curr.val` is **not** in the set, attach it to the result list (`tail.next = curr`).
   - Move `tail` forward.
4. **Terminate** the new list (`tail.next = null`) to avoid cycles.
5. Return `dummy.next`.

> **Why a dummy head?**  
> It simplifies edge cases (e.g., the first node is removed) by guaranteeing a non‑null previous node.

---

# 🏁 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Building the set | `O(n)` | `O(n)` |
| Traversing the list | `O(m)` | `O(1)` (besides output) |
| Total | **`O(n + m)`** | **`O(n)`** |

`n` – length of `nums`, `m` – length of the linked list.

---

# 📦 Code Implementations

Below are complete, ready‑to‑copy solutions in **Java**, **Python**, and **C++**. Each file contains a minimal `ListNode` definition and a `Solution` class (or function) that can be pasted into your IDE or online judge.

---

## Java

```java
import java.util.HashSet;
import java.util.Set;

/**
 * Definition for singly-linked list.
 */
class ListNode {
    int val;
    ListNode next;
    ListNode() {}
    ListNode(int val) { this.val = val; }
    ListNode(int val, ListNode next) { this.val = val; this.next = next; }
}

public class Solution {
    public ListNode deleteNodes(int[] nums, ListNode head) {
        // 1. Build the hash set
        Set<Integer> toDelete = new HashSet<>();
        for (int num : nums) {
            toDelete.add(num);
        }

        // 2. Dummy head to simplify deletions
        ListNode dummy = new ListNode(0);
        ListNode tail = dummy;

        // 3. Traverse the list
        while (head != null) {
            if (!toDelete.contains(head.val)) {
                tail.next = head;   // keep the node
                tail = tail.next;
            }
            head = head.next;
        }

        // 4. Terminate the new list
        tail.next = null;
        return dummy.next;
    }
}
```

**Test snippet**

```java
public static void main(String[] args) {
    // Build list: 1->2->3->4->5
    ListNode head = new ListNode(1, new ListNode(2,
                      new ListNode(3, new ListNode(4,
                      new ListNode(5)))));
    int[] nums = {1, 2, 3};

    Solution sol = new Solution();
    ListNode res = sol.deleteNodes(nums, head);

    // Print result
    while (res != null) {
        System.out.print(res.val + " ");
        res = res.next;
    } // Expected: 4 5
}
```

---

## Python

```python
from typing import List, Optional

class ListNode:
    def __init__(self, val: int = 0, next: Optional['ListNode'] = None):
        self.val = val
        self.next = next

class Solution:
    def deleteNodes(self, nums: List[int], head: Optional[ListNode]) -> Optional[ListNode]:
        to_delete = set(nums)           # O(n) time, O(n) space
        dummy = ListNode(0)
        tail = dummy

        while head:
            if head.val not in to_delete:  # keep node
                tail.next = head
                tail = tail.next
            head = head.next

        tail.next = None
        return dummy.next
```

**Test snippet**

```python
def build_list(vals):
    dummy = ListNode(0)
    cur = dummy
    for v in vals:
        cur.next = ListNode(v)
        cur = cur.next
    return dummy.next

def print_list(node):
    while node:
        print(node.val, end=' ')
        node = node.next
    print()

head = build_list([1,2,3,4,5])
nums = [1,2,3]
sol = Solution()
res = sol.deleteNodes(nums, head)
print_list(res)   # Output: 4 5
```

---

## C++

```cpp
#include <bits/stdc++.h>
using namespace std;

// Definition for singly‑linked list.
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(nullptr) {}
};

class Solution {
public:
    ListNode* deleteNodes(vector<int>& nums, ListNode* head) {
        unordered_set<int> toDelete(nums.begin(), nums.end());

        ListNode dummy(0);
        ListNode* tail = &dummy;

        while (head) {
            if (toDelete.find(head->val) == toDelete.end()) {
                tail->next = head;
                tail = tail->next;
            }
            head = head->next;
        }
        tail->next = nullptr;   // cut off possible old tail
        return dummy.next;
    }
};
```

**Test snippet**

```cpp
int main() {
    // Build list: 1->2->3->4->5
    ListNode* head = new ListNode(1);
    ListNode* cur = head;
    for (int v = 2; v <= 5; ++v) {
        cur->next = new ListNode(v);
        cur = cur->next;
    }

    vector<int> nums = {1, 2, 3};
    Solution sol;
    ListNode* res = sol.deleteNodes(nums, head);

    // Print result
    for (ListNode* p = res; p; p = p->next)
        cout << p->val << " ";
    // Expected: 4 5
    return 0;
}
```

---

# 📣 SEO‑Optimized Blog Post

---

## Title  
**“Delete Nodes From Linked List Present in Array – Java, Python, C++ Solutions + Interview Tips”**

> **Keywords**: LeetCode, interview problem, delete nodes, linked list, hash set, C++ solution, Java solution, Python solution, O(n) algorithm, data structures interview, technical interview prep

---

## Introduction  

Interviews at Google, Amazon, Microsoft, and even startups love problems that force you to think about **data‑structure interaction**. One such favorite is *Delete Nodes From Linked List Present in Array*. On LeetCode it’s a Medium‑level problem that has a clear optimal solution: **hash‑set lookup + dummy head traversal**.

In this article you’ll discover:  

1. Why the problem is interview‑worthy.  
2. The most elegant algorithm (O(n+m) time).  
3. How to avoid common mistakes.  
4. Complete, copy‑pasteable code in three languages.  
5. Bonus interview questions that “spin off” from the same idea.

Let’s dive in!

---

## 1. Why This Problem Stands Out  

| Company | What they test | Typical Follow‑Up |
|---------|----------------|-------------------|
| Google | Linked lists & set operations | “What if the list had cycles?” |
| Amazon | Space optimization | “Can you do it in O(1) extra space?” |
| Facebook | Edge‑case handling | “What if the array is huge but sparse?” |

The problem demonstrates a clear mapping: **“lookup” → **hash‑set**. Interviewers ask you to justify your choice of data structure and to explain the trade‑offs.

---

## 2. The Algorithm – O(n+m)  

1. **Hash‑Set** – store all values that must be removed.  
2. **Dummy head** – eliminates special handling when the first node is deleted.  
3. **Single pass** – iterate once over the list, append only the nodes that are *not* in the set.  
4. **Terminate** – set `next` of the last node to `null` to avoid dangling pointers.  

This approach gives you an **optimal time complexity** of `O(n + m)` and an **extra space** of `O(n)` for the set.  

---

## 3. Language‑Specific Tips  

| Language | Tip | Why |
|----------|-----|-----|
| **Java** | Use `HashSet<Integer>` – it’s part of the JDK and keeps your code clean. | Constant‑time lookup and no extra boilerplate. |
| **Python** | Convert the array to a `set` before iterating. | Python’s `in` operator on sets is O(1) on average. |
| **C++** | `unordered_set<int>` is the STL counterpart of `HashSet`. | Faster than `set<int>` (balanced tree) and uses hash tables under the hood. |

---

## 4. Common Mistakes to Avoid  

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| Using a large array for lookup | Wastes memory if numbers are sparse. | Switch to a hash‑based structure. |
| Not detaching the tail | May produce a cycle in the resulting list. | Explicitly set `tail->next = nullptr`. |
| Forgetting the dummy head | Need extra code to handle deletions at the front. | Always start with `ListNode dummy(0)`. |

---

## 5. “Spin‑Off” Interview Questions  

1. **Remove Nodes In‑Place** – can you do it without allocating any new nodes?  
2. **Sorted List Variant** – `nums` is sorted; does that change your algorithm?  
3. **Multi‑Threaded Deletion** – imagine the list is being read by another thread while you delete nodes.  
4. **Large Numbers** – if `Node.val` could be up to `10⁹`, would an array‑based lookup still work?  

These are natural follow‑ups that test deeper understanding and edge‑case handling.

---

## 6. Takeaway Checklist  

- [ ] Convert `nums` into a `HashSet / unordered_set / set`.  
- [ ] Use a **dummy head** to simplify the algorithm.  
- [ ] Do a single pass over the list, appending nodes not in the set.  
- [ ] Terminate the list with `next = null`.  
- [ ] Handle the case where the entire list is removed (`return null`).  

> **Pro tip**: If you’re on LeetCode, paste the solution into the online editor, run the sample tests, then submit. The same code works on most online judges.

---

## 🎓 Final Words  

Mastering this problem proves you understand **hash‑based lookup**, **linked‑list traversal**, and **edge‑case handling**. Your interviewers will see that you can write clean, efficient code that scales to 10⁵ elements – a critical skill in any performance‑critical codebase.

Happy coding, and good luck on your next interview! 🎉

---

**Author**: *Your Name* – Software Engineer & Interview Coach.  
Follow me on Twitter, LinkedIn, and GitHub for more interview prep and open‑source projects.  

---