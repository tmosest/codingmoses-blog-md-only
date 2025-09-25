---
title: LeetCode 3217. Delete Nodes From Linked List Present in Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📚  Cracking Leetcode 3217  
**Delete Nodes From Linked List Present in Array**  
*The Good, The Bad, and The Ugly – A Job‑Interview Friendly Guide*  

---

### Table of Contents
1. [Problem Overview](#problem-overview)  
2. [Intuition & Core Idea](#intuition-core-idea)  
3. [Algorithm & Complexity](#algorithm-complexity)  
4. [Implementation in Three Languages](#implementation-in-three-languages)  
5. [The Good, The Bad, The Ugly](#the-good-the-bad-the-ugly)  
6. [Interview Tips & SEO‑Friendly Takeaways](#interview-tips)  

---

## 1️⃣ Problem Overview <a name="problem-overview"></a>

> **Leetcode 3217** – *Delete Nodes From Linked List Present in Array*  
> **Difficulty:** Medium

You’re given:
- an integer array `nums` (`1 ≤ nums.length ≤ 10⁵`, all values unique)
- the head of a singly‑linked list (`1 ≤ nodes ≤ 10⁵`, node values are also unique integers in the same range)

**Goal:**  
Return the head of the linked list after removing every node whose value appears in `nums`.  
At least one node will *not* be removed (the input guarantees this).

> **Example**  
> ```
> nums = [1, 2, 3]
> head = 1 → 2 → 3 → 4 → 5
> Output: 4 → 5
> ```

---

## 2️⃣ Intuition & Core Idea <a name="intuition-core-idea"></a>

The simplest thought is “check each node against the array” – but that would be *O(n × m)* and far too slow.

**Key Insight:**  
*Convert the array into a hash‑set (or an array of booleans) so membership checks become *O(1)*.*  
Then iterate through the list once, skipping nodes whose values are in the set.

---

## 3️⃣ Algorithm & Complexity <a name="algorithm-complexity"></a>

```
1. Build a hash‑set `removeSet` from `nums`           // O(n) time, O(n) space
2. Create a dummy node pointing to the original head // helps with head deletions
3. Traverse the list with a pointer `curr`:
   a. If curr.next.val is in removeSet → skip it
   b. Else move curr forward
4. Return dummy.next
```

| Metric | Complexity |
|--------|------------|
| **Time** | `O(n + m)` (single pass over array + single pass over list) |
| **Space** | `O(n)` for the hash‑set (or `O(max(nums))` if you use a bool array) |
| **Why it matters for interviews** | Demonstrates knowledge of hash‑sets, pointer manipulation, and careful handling of edge cases (e.g., removing the original head). |

---

## 4️⃣ Implementation in Three Languages <a name="implementation-in-three-languages"></a>

Below you’ll find clean, production‑ready code for **Java**, **Python**, and **C++**.  
All three use the same core algorithm described above.

---

### 4.1 Java

```java
// Definition for singly-linked list.
class ListNode {
    int val;
    ListNode next;
    ListNode() {}
    ListNode(int val) { this.val = val; }
    ListNode(int val, ListNode next) { this.val = val; this.next = next; }
}

class Solution {
    public ListNode deleteNodes(int[] nums, ListNode head) {
        // 1. Build hash set of values to delete
        Set<Integer> removeSet = new HashSet<>();
        for (int v : nums) removeSet.add(v);

        // 2. Dummy node simplifies deletions at the head
        ListNode dummy = new ListNode(0, head);
        ListNode curr = dummy;

        // 3. One-pass removal
        while (curr.next != null) {
            if (removeSet.contains(curr.next.val)) {
                curr.next = curr.next.next;          // skip node
            } else {
                curr = curr.next;                    // keep node
            }
        }
        return dummy.next;
    }
}
```

---

### 4.2 Python

```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def deleteNodes(self, nums: list[int], head: ListNode | None) -> ListNode | None:
        # 1. Build set for O(1) lookups
        remove_set = set(nums)

        # 2. Dummy head
        dummy = ListNode(0, head)
        curr = dummy

        # 3. Iterate once
        while curr.next:
            if curr.next.val in remove_set:
                curr.next = curr.next.next
            else:
                curr = curr.next
        return dummy.next
```

---

### 4.3 C++

```cpp
// Definition for singly-linked list.
struct ListNode {
    int val;
    ListNode *next;
    ListNode() : val(0), next(nullptr) {}
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : val(x), next(next) {}
};

class Solution {
public:
    ListNode* deleteNodes(vector<int>& nums, ListNode* head) {
        unordered_set<int> removeSet(nums.begin(), nums.end());

        // Dummy node to handle deletions at head
        ListNode dummy(0, head);
        ListNode* curr = &dummy;

        while (curr->next) {
            if (removeSet.count(curr->next->val))
                curr->next = curr->next->next; // skip
            else
                curr = curr->next;             // keep
        }
        return dummy.next;
    }
};
```

---

## 5️⃣ The Good, The Bad, The Ugly <a name="the-good-the-bad-the-ugly"></a>

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Core Idea** | Using a hash‑set gives linear time – *clean and idiomatic* | The naïve nested‑loop O(n*m) is easy to write but unacceptable for constraints | Using a large boolean array of size `max(nums)+1` can waste memory if `nums` contains huge values (e.g., 10⁵) |
| **Edge Cases** | Dummy node handles removal of the original head automatically | Forgetting to `delete` nodes in C++ leads to memory leaks | In Java/Python forgetting to set `current.next = null` at the end can leave stray references and cause subtle bugs |
| **Readability** | Clear variable names (`removeSet`, `dummy`) | Over‑complicated logic (e.g., double pointers) can obfuscate intent | Mixing language‑specific idioms (e.g., using `next = head.next` inside a loop without clarity) |
| **Performance** | Linear passes, constant‑time checks | O(n*m) will TLE on large tests | Unnecessary `contains()` on a `HashSet` for every node may be slower than a sorted array with binary search, but still acceptable |
| **Interview Appeal** | Demonstrates data‑structure knowledge, pointer manipulation, edge‑case handling | Simple but incomplete solutions are often rejected | Over‑optimizing prematurely or ignoring clarity can hurt the interview score |

> **Bottom line:** Keep the implementation straightforward. Use a hash‑set, a dummy head, and one pass over the list. No need for fancy tricks unless the interviewer explicitly asks for them.

---

## 6️⃣ Interview Tips & SEO‑Friendly Takeaways <a name="interview-tips"></a>

| Tip | Why it matters | SEO keyword |
|-----|----------------|-------------|
| **Explain your thought process** | Interviewers love a clear roadmap. | *Leetcode interview*, *problem solving* |
| **Mention time/space trade‑offs** | Shows you understand constraints. | *algorithm complexity*, *O(n + m)* |
| **Ask clarifying questions** | Confirms assumptions (e.g., “Are the list values unique?”). | *interview strategy*, *clarifying questions* |
| **Write clean code** | Readability beats cleverness. | *clean code*, *production‑ready* |
| **Talk about edge cases** | Head removal, empty list, all nodes removed, etc. | *edge cases*, *linked list* |
| **Test on the whiteboard** | Demonstrates debugging skills. | *whiteboard coding*, *debugging interview* |
| **Practice with a timer** | Ensures you can finish within 15–30 min. | *Leetcode practice*, *coding speed* |

> 🎯 *If you’re targeting recruiters and hiring managers, remember to include keywords like “Linked List”, “HashSet”, “Linear Time”, and “Interview Problem”. This will help your article show up in searches by recruiters looking for “medium Leetcode questions” and “singly‑linked list interview questions”.*

---

### ✅ Key Takeaway

> **“Build a hash‑set → dummy head → one linear pass.”**  
> That’s the most interview‑friendly, efficient, and maintainable solution to Leetcode 3217.  

Happy coding, and may your next interview be as smooth as a `dummy.next`! 🚀