---
title: Day 1 LeetCode
description:
date: 2025-09-16
categories: []
author: moses
tags: []
hideToc: true
---

## 2. Add Two Numbers – A Medium‑Level Linked‑List Puzzle  
*Solved – Medium* | *LeetCode*  

> *You are given two non‑empty linked lists representing two non‑negative integers.  
>  The digits are stored in reverse order, each node contains a single digit.  
>  Add the two numbers and return the sum as a linked list.*

> **Constraints**  
> * 1 ≤ length of each list ≤ 100  
> * 0 ≤ node.val ≤ 9  
> * The list represents a number that does **not** have leading zeros (except the single node `0`).  

---

### The Core Idea

It is nothing more than long‑hand addition written out on a computer.  
We walk the two lists simultaneously, add the current digits together and
propagate a carry into the next position.  
Because the lists are in *reverse* order, the first node contains the least
significant digit – exactly what we need for a straightforward ripple‑carry
operation.

The classic, “got‑it‑in‑one‑pass” solution looks like this (Java):

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);   // dummy head
        ListNode tail  = dummy;             // tail of the result list
        int carry = 0;

        while (l1 != null || l2 != null || carry != 0) {
            int sum = carry;
            if (l1 != null) { sum += l1.val; l1 = l1.next; }
            if (l2 != null) { sum += l2.val; l2 = l2.next; }

            carry = sum / 10;          // integer division
            tail.next = new ListNode(sum % 10);
            tail = tail.next;
        }
        return dummy.next;              // skip dummy
    }
}
```

The provided LeetCode solution follows this exact logic, albeit with a
boolean `carryOne` flag instead of an integer carry.  It is functional but
there are some stylistic quirks that the community would like to point out
(see **Bad** and **Ugly** below).

---

## What’s *Good* about this Problem?

| Good | Why it matters |
|------|----------------|
| **Clear real‑world motivation** | The “two integers as linked lists” is a nice abstraction for streaming or extremely large numbers that cannot fit into built‑in numeric types. |
| **Well‑defined constraints** | Length ≤ 100 guarantees that a simple linear pass will finish quickly; it also ensures that we can keep a single carry variable. |
| **Straightforward I/O format** | Using a reverse‑order list matches how we normally write addition, so we never have to reverse again. |
| **Classic interview pattern** | It’s a textbook example of “process two sequences in lockstep with carry” – a building block for many other problems (e.g., `Add Numbers in Forward Order`, `Multiply Two Numbers`). |
| **O(1) auxiliary space (ignoring output)** | Aside from the new nodes, no extra arrays or stacks are needed. |
| **Time complexity O(n)** | n is the maximum of the two list lengths; linear time is optimal because we must look at every digit at least once. |

---

## What’s *Bad* about it?

| Bad | Why it can trip you up |
|-----|------------------------|
| **Edge‑case confusion** | A common mistake is to forget the *final* carry.  If the sum of the most significant digits generates a carry, you must create an extra node.  Failing to do so produces wrong answers on inputs like `[5]` + `[5]`. |
| **Redundant carry handling** | The LeetCode snippet checks `carryOne` twice: once after adding `l1` and once after adding `l2`.  Since `carryOne` is a flag that always toggles back to `false`, those checks could be merged into a single carry addition.  This duplication is unnecessary and makes the code harder to read. |
| **Explicit boolean vs. integer carry** | Using a boolean (`carryOne`) is a bit unnatural because the carry can be `0` or `1` *but* you might want to reuse the same variable for any integer carry.  An integer `carry` that holds the exact carry value (0 or 1) is clearer. |
| **No dummy node in the first posted Java solution** | The code sets `head = null` and builds the list from scratch.  A dummy head (or sentinel) is a common pattern that removes the “first‑node” special case.  The absence of a dummy node in the original code means you need to handle `head == null` separately in every loop, which is error‑prone. |
| **Hard‑coded logic for `carryOne` reset** | Setting `carryOne = false` inside the `if (carryOne)` block is repeated.  One could simply do `val += carryOne ? 1 : 0` or maintain `carry` as an integer and just add it directly (`val += carry`). |
| **Lack of null‑check for the final node** | If you forget to add the last carry node (`if (carryOne) pointer.next = new ListNode(1);`), the algorithm fails on inputs that produce an extra digit, e.g. `[9,9] + [1]` → `[0,0,1]`. |

---

## What’s *Ugly* about it?

| Ugly | What you’ll hate |
|------|------------------|
| **Verbosity that obscures the core idea** | The Java snippet is very wordy: separate blocks for `l1`, `l2`, and then carry checks.  A more compact loop (`while (l1 != null || l2 != null || carry != 0)`) immediately signals the three termination conditions. |
| **Unnecessary state machine** | The `carryOne` flag is a tiny finite‑state machine that only holds `true/false`.  It can be collapsed into an integer `carry` and used directly in the sum, cutting down on branches. |
| **Missing dummy node and result pointer** | Without a dummy head, you need a special case for the first node (`if (head == null)`), and you might end up writing more code for node creation.  The dummy head pattern keeps the loop body simple and eliminates that if‑branch. |
| **Poor readability for newcomers** | The Java version uses `ListNode head = null; ListNode pointer = null;` and then updates both.  A clearer pattern is to create a `dummy` node once, keep a `current` pointer, and return `dummy.next`. |
| **Potential memory waste** | Creating a new node for every digit guarantees O(n) extra memory, but some implementations could try to reuse existing nodes (e.g., by adding into the longer list).  That optimization is rarely needed, but it can save memory if you’re tight on heap. |
| **No comments or explanatory text** | The code is presented as a black box.  For learning purposes, a step‑by‑step walk‑through (as in the long “Intuition” section) is essential.  The LeetCode solution alone is terse; readers benefit from a narrative that explains *why* each operation is performed. |

---

## A Cleaner, Idiomatic Java Implementation

Below is a slightly refactored version that eliminates some of the pitfalls while keeping the algorithm identical.

```java
/**
 * Definition for singly-linked list.
 */
class ListNode {
    int val;
    ListNode next;
    ListNode(int val)          { this.val = val; }
    ListNode(int val, Node n)  { this.val = val; this.next = n; }
}

class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        // 1. A sentinel node to avoid special‑case code
        ListNode dummy = new ListNode(0);
        ListNode tail   = dummy;     // tail points to the last node of the result

        int carry = 0;               // integer carry (0 or 1)

        // 2. Walk until all sources are exhausted
        while (l1 != null || l2 != null || carry != 0) {
            int sum = carry;          // start with the carry

            if (l1 != null) { sum += l1.val; l1 = l1.next; }
            if (l2 != null) { sum += l2.val; l2 = l2.next; }

            // 3. New node contains the least significant digit of sum
            tail.next = new ListNode(sum % 10);
            tail = tail.next;

            // 4. Compute the carry for the next iteration
            carry = sum / 10;          // integer division (floor)
        }

        return dummy.next; // skip sentinel
    }
}
```

**Why is this nicer?**

1. **Single integer carry** – No repeated boolean resets.
2. **One termination condition** – The loop header clearly states the three sources of continuation.
3. **Dummy head** – Eliminates the `if (head == null)` special case.
4. **Minimal node‑creation logic** – All node construction lives in a single line.
5. **Self‑documenting** – Variable names (`carry`, `sum`, `dummy`) convey intent.

---

## Take‑away Lessons

1. **Always remember the last carry node.**  
   Many of the “got‑wrong‑on‑sample” bugs come from forgetting this step.

2. **Use the sentinel/dummy head pattern.**  
   It removes the first‑node special case and keeps the body of the loop
   clean.

3. **Prefer an integer carry over a boolean flag.**  
   It scales naturally to any carry size (0 or 1 here, but the same code
   works for multi‑digit carries if you later generalize the problem).

4. **Readability beats raw speed.**  
   Even for an interview, the interviewer values code that is *easy to
   read and reason about* just as much as it is fast.

5. **Practice the pattern in both directions.**  
   The same lock‑step‑with‑carry logic appears in “Add Numbers in Forward
   Order” (which requires an explicit stack or recursion) and in more
   complex problems like “Multiply Two Numbers”. Mastering this pattern
   unlocks a whole family of linked‑list puzzles.

---

### Bottom Line

`Add Two Numbers` is a deceptively simple problem that illustrates the
fundamental mechanics of digit‑wise addition on linked lists.  
The core algorithm is efficient and optimal; the challenge for many is to
implement it *cleanly* without losing the important edge‑case (final
carry).  Once you internalise the pattern, you’ll be ready to tackle its
forward‑order cousin, the multiply‑two‑numbers problem, and a host of
other “carry‑propagation” puzzles that pop up in interviews.
