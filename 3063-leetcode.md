---
title: LeetCode 3063. Linked List Frequency - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📄  Linked List Frequency – LeetCode 3063  
### A 3‑Way Deep Dive: Java | Python | C++ + SEO‑Optimized Blog

---

### Table of Contents
1. [Problem Statement](#problem-statement)
2. [Quick Solution Sketch](#quick-solution-sketch)
3. [Good, Bad, & Ugly](#good-bad-ugly)
4. [Algorithm & Complexity](#algorithm-complexity)
5. [Full Code](#full-code)
   - Java
   - Python
   - C++
6. [Edge‑Case Checklist](#edge-case-checklist)
7. [Alternative Approaches](#alternative-approaches)
8. [Testing & Sample Runs](#testing)
9. [Take‑away & Interview Tips](#take-away)
10. [SEO & Job‑Search Friendly Closing](#seo)

---

## Problem Statement <a name="problem-statement"></a>

> **LeetCode 3063 – Linked List Frequency**  
> You are given the head of a singly linked list that contains *k* distinct integers.  
> Return a new singly linked list of length *k* where each node contains the frequency of the corresponding distinct value from the original list.  
> The order of frequencies in the output list does **not** matter.

> **Constraints**  
> * 1 ≤ n ≤ 10⁵ (number of nodes)  
> * 1 ≤ Node.val ≤ 10⁵

> **Example**  
> Input: `1 → 1 → 2 → 1 → 2 → 3`  
> Output: `3 → 2 → 1` (any permutation of the frequencies is valid)

---

## Quick Solution Sketch <a name="quick-solution-sketch"></a>

1. **Traverse the input list once** and count occurrences of each value using a hash map (`value → count`).
2. **Build a new list** by iterating over the map’s values and creating a node for each frequency.
3. Return the head of the new list.

This is an **O(n)** time, **O(k)** space solution – perfect for interview practice and job‑hunt coding challenges.

---

## Good, Bad & Ugly <a name="good-bad-ugly"></a>

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| Data structure | `HashMap` for O(1) counting | Using `List` + linear search → O(n²) | Nested loops for counting + building the result |
| List creation | Dummy node + tail pointer | Re‑assigning head in a loop | Constantly traversing to the tail for each new node |
| Order handling | Any order is fine, no sorting required | Misunderstanding “any order” → sorting unnecessarily | Using sorting to produce a fixed order when it’s not required |
| Edge cases | Single node, all unique, all identical | Forgetting to handle `null` head | Assuming map values come in sorted order |
| Space | O(k) for frequencies | Extra O(n) for temporary arrays | Copying the whole list twice unnecessarily |

> **The key takeaway** – keep it simple: hash the values, build once, return.

---

## Algorithm & Complexity <a name="algorithm-complexity"></a>

```text
1. Initialize empty hash map M.
2. For each node in the input list:
       M[node.val] += 1
3. Create dummy head of result list.
4. For each frequency f in M.values():
       append new node with value f to result list
5. Return dummy.next
```

*Time*: **O(n)** – one pass for counting, one pass over distinct values (≤ n).  
*Space*: **O(k)** – the map stores one entry per distinct value.  
*Extra*: Constant extra space for dummy node and loop variables.

---

## Full Code <a name="full-code"></a>

### Java

```java
import java.util.HashMap;
import java.util.Map;

class ListNode {
    int val;
    ListNode next;
    ListNode() {}
    ListNode(int val) { this.val = val; }
    ListNode(int val, ListNode next) { this.val = val; this.next = next; }
}

public class Solution {
    public ListNode frequenciesOfElements(ListNode head) {
        // 1️⃣ Count frequencies
        Map<Integer, Integer> freq = new HashMap<>();
        for (ListNode cur = head; cur != null; cur = cur.next) {
            freq.put(cur.val, freq.getOrDefault(cur.val, 0) + 1);
        }

        // 2️⃣ Build result list
        ListNode dummy = new ListNode();   // dummy node simplifies tail handling
        ListNode tail = dummy;
        for (int count : freq.values()) {
            tail.next = new ListNode(count);
            tail = tail.next;
        }
        return dummy.next;   // skip dummy
    }
}
```

> **Why a dummy node?**  
> It eliminates special‑case handling for the first node and keeps tail updates simple.

---

### Python

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def frequenciesOfElements(self, head: ListNode) -> ListNode:
        # 1️⃣ Count frequencies
        freq = {}
        cur = head
        while cur:
            freq[cur.val] = freq.get(cur.val, 0) + 1
            cur = cur.next

        # 2️⃣ Build result list
        dummy = ListNode()
        tail = dummy
        for count in freq.values():
            tail.next = ListNode(count)
            tail = tail.next
        return dummy.next
```

> Python’s `dict` is a built‑in hash map.  
> Using a `while` loop keeps the logic clear and avoids recursion depth issues.

---

### C++

```cpp
/** Definition for singly-linked list. */
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(nullptr) {}
};

class Solution {
public:
    ListNode* frequenciesOfElements(ListNode* head) {
        // 1️⃣ Count frequencies
        unordered_map<int, int> freq;
        for (ListNode* cur = head; cur; cur = cur->next) {
            ++freq[cur->val];
        }

        // 2️⃣ Build result list
        ListNode* dummy = new ListNode(0);
        ListNode* tail = dummy;
        for (const auto &p : freq) {
            tail->next = new ListNode(p.second);
            tail = tail->next;
        }
        ListNode* res = dummy->next;
        delete dummy;          // free dummy node
        return res;
    }
};
```

> C++ uses `unordered_map` (hash map) and manual memory management.  
> A dummy node is again used to simplify tail insertion.

---

## Edge‑Case Checklist <a name="edge-case-checklist"></a>

| Edge Case | What to watch out for | How the code handles it |
|-----------|-----------------------|------------------------|
| **Single node** | Map gets one entry | Works → one node in result |
| **All distinct** | Map size = n | Result length = n |
| **All identical** | Map size = 1 | Result length = 1 |
| **Large values (1e5)** | Hash map capacity | Still O(1) lookup |
| **Null head** | Should never happen (constraints) | Not needed, but Java/Python/C++ gracefully return null |

---

## Alternative Approaches <a name="alternative-approaches"></a>

| Approach | Pros | Cons |
|----------|------|------|
| **Sort the input list first** then count consecutive values | No hash map, deterministic order | Requires O(n log n) time, extra space for sorting |
| **Two‑pass with array bucket** (`bucket[i] = count`) | O(1) counting for bounded values | Needs array of size 1e5 → high memory for sparse data |
| **Recursion** to count frequencies | Elegant but depth‑limit risk | Not efficient for 10⁵ nodes |

> **Bottom line:** The hash‑map one‑pass approach is the gold standard for interviews.

---

## Testing & Sample Runs <a name="testing"></a>

```python
# Python example
def build_list(arr):
    dummy = ListNode()
    cur = dummy
    for v in arr:
        cur.next = ListNode(v)
        cur = cur.next
    return dummy.next

def to_list(node):
    res = []
    while node:
        res.append(node.val)
        node = node.next
    return res

head = build_list([1,1,2,1,2,3])
print(to_list(Solution().frequenciesOfElements(head)))  # [3, 2, 1] (order may vary)
```

Similar tests can be written in Java and C++.

---

## Take‑away & Interview Tips <a name="take-away"></a>

1. **Read the problem carefully** – “any order” is a key hint to avoid sorting.
2. **Explain your approach first** – hash map → O(n) time, O(k) space.
3. **Write clean, reusable code** – dummy node + tail pointer pattern is a staple for linked‑list problems.
4. **Edge‑case discussion** – show awareness of distinct vs identical values, large input sizes.
5. **Time/Space complexity** – always state it and confirm it matches constraints.
6. **Ask clarifying questions** – if you’re unsure about “any order” or “distinct elements”.

---

## SEO & Job‑Search Friendly Closing <a name="seo"></a>

If you’re aiming to land that **software engineering interview** or get noticed on **LinkedIn** for a **LeetCode 3063** solution, this post has you covered:

- **Keywords**: Linked List Frequency, LeetCode 3063, Java Linked List, Python Linked List, C++ Linked List, Interview Coding, O(n) Solution, HashMap, Dummy Node.
- **Readability**: Clear headings, code blocks, and an “Good, Bad, Ugly” section that showcases problem‑solving mindset.
- **Value**: Detailed walkthrough, edge‑case checklist, and alternative approaches – perfect for a portfolio or personal blog.

Share this article, add it to your résumé, or cite it during your next coding interview to demonstrate mastery over linked‑list fundamentals and algorithmic thinking. 🚀

---