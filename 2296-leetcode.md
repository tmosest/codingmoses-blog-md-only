---
title: LeetCode 2296. Design a Text Editor - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code – LeetCode 2296 “Design a Text Editor”

Below are **three complete, self‑contained solutions** – one each in **Java, Python, and C++** – that satisfy the LeetCode constraints and run in **O(k)** time per operation.

> **Why this solution?**  
> * It uses the simplest data‑structure that gives *O(k)* time: two `Stack` objects (or a `StringBuilder` with a cursor index).  
> * No linked‑list traversal, no heavy‑weight libraries – perfect for an interview or production code.  
> * All corner‑cases (moving beyond the text, deleting more than available, empty cursor) are handled cleanly.

---

### 1.1 Java – `StringBuilder` + cursor index

```java
import java.util.*;

class TextEditor {
    private StringBuilder text;   // the current text
    private int cursor;           // cursor position (0 … text.length())

    public TextEditor() {
        this.text   = new StringBuilder();
        this.cursor = 0;
    }

    /** Append `text` at the cursor, then move cursor to the right end of it. */
    public void addText(String newText) {
        text.insert(cursor, newText);   // O(newText.length())
        cursor += newText.length();
    }

    /** Delete up to `k` chars to the left of the cursor.
     *  Return the number of characters actually removed. */
    public int deleteText(int k) {
        int remove = Math.min(k, cursor);      // can't delete beyond start
        if (remove > 0) {
            text.delete(cursor - remove, cursor);
            cursor -= remove;
        }
        return remove;
    }

    /** Move cursor left up to `k` positions.
     *  Return the last min(10, cursor) characters to the left of the cursor. */
    public String cursorLeft(int k) {
        cursor = Math.max(0, cursor - k);
        int start = Math.max(0, cursor - 10);
        return text.substring(start, cursor);
    }

    /** Move cursor right up to `k` positions.
     *  Return the last min(10, cursor) characters to the left of the cursor. */
    public String cursorRight(int k) {
        cursor = Math.min(text.length(), cursor + k);
        int start = Math.max(0, cursor - 10);
        return text.substring(start, cursor);
    }
}
```

---

### 1.2 Python – two `deque` stacks

```python
from collections import deque
from typing import List

class TextEditor:
    def __init__(self):
        # left stack holds characters to the left of the cursor
        # right stack holds characters to the right of the cursor
        self.left  = deque()
        self.right = deque()

    def addText(self, text: str) -> None:
        for ch in text:
            self.left.append(ch)          # O(1) each

    def deleteText(self, k: int) -> int:
        removed = 0
        while removed < k and self.left:
            self.left.pop()
            removed += 1
        return removed

    def cursorLeft(self, k: int) -> str:
        moved = 0
        while moved < k and self.left:
            self.right.appendleft(self.left.pop())
            moved += 1
        # last min(10, cursor) chars to the left of cursor
        return ''.join(list(self.left)[-10:])

    def cursorRight(self, k: int) -> str:
        moved = 0
        while moved < k and self.right:
            self.left.append(self.right.popleft())
            moved += 1
        return ''.join(list(self.left)[-10:])

# Usage example
# editor = TextEditor()
# editor.addText("leetcode")
```

*Why two stacks?*  
They let us **push/pop in O(1)**, giving overall **O(k)** for each operation, even for the largest `k=40` and millions of calls.

---

### 1.3 C++ – `string` + cursor index

```cpp
#include <string>
#include <algorithm>

class TextEditor {
    std::string text;   // current text
    size_t cursor;      // 0 … text.size()

public:
    TextEditor() : text(""), cursor(0) {}

    void addText(const std::string &s) {
        text.insert(cursor, s);
        cursor += s.size();
    }

    int deleteText(int k) {
        int remove = std::min(k, static_cast<int>(cursor));
        if (remove > 0) {
            text.erase(cursor - remove, remove);
            cursor -= remove;
        }
        return remove;
    }

    std::string cursorLeft(int k) {
        cursor = std::max<size_t>(0, cursor - k);
        size_t start = (cursor >= 10) ? cursor - 10 : 0;
        return text.substr(start, cursor - start);
    }

    std::string cursorRight(int k) {
        cursor = std::min<size_t>(text.size(), cursor + k);
        size_t start = (cursor >= 10) ? cursor - 10 : 0;
        return text.substr(start, cursor - start);
    }
};
```

---

## 2.  Blog Article – “Design a Text Editor (LeetCode 2296): The Good, The Bad, and The Ugly”

> **Meta‑Description**  
> Master LeetCode 2296 “Design a Text Editor”. Explore three clean implementations in Java, Python, and C++, understand the trade‑offs, and learn how to ace interview questions on string manipulation and cursor simulation.

---

### 2.1 Introduction

Have you ever stared at the LeetCode “Design a Text Editor” problem and wondered why it’s rated “Hard”?  
The core difficulty is that every operation – add, delete, move left, move right – must be **O(k)**, and the data structure must keep the cursor in sync with the text.  

In this post we’ll:

- Break down the problem statement and constraints.
- Show three **idiomatic** solutions (Java, Python, C++).
- Discuss the *good*, *bad*, and *ugly* parts of each approach.
- Highlight why the stack‑based method is the gold‑standard for interviewers.
- Give you a cheat‑sheet to impress recruiters.

Let’s dive in.

---

### 2.2 Problem Statement (Simplified)

| Operation | What it does | Return value |
|-----------|--------------|--------------|
| `addText(string text)` | Insert at cursor, cursor moves right. | `void` |
| `deleteText(int k)` | Delete up to `k` chars left of cursor. | `int` – number deleted |
| `cursorLeft(int k)` | Move cursor left `k` steps. | `string` – last min(10, cursor) chars |
| `cursorRight(int k)` | Move cursor right `k` steps. | `string` – last min(10, cursor) chars |

*Constraints*: `text.length`, `k ≤ 40`; up to 2 × 10⁴ calls; only lowercase letters.

---

### 2.3 The “Good” – Two‑Stack (or Deque) Solution

**Why it’s great**

| Reason | Details |
|--------|---------|
| **O(k) time** | Each operation touches at most `k` characters. |
| **Constant‑space per char** | Two deques store characters directly, no pointer gymnastics. |
| **Simple code** | No custom linked‑list nodes, no edge‑case juggling beyond stack pops. |
| **Language‑agnostic** | Works in Java (`ArrayDeque`), Python (`collections.deque`), C++ (`std::deque`). |
| **Perfect for interviews** | Interviewers love stack‑based simulation; easy to explain and test. |

**Core idea**

- **Left stack** holds characters **left** of the cursor.  
- **Right stack** holds characters **right** of the cursor.  
- **Insertion** → push onto left stack.  
- **Deletion** → pop from left stack.  
- **Move left** → pop from left → push onto right.  
- **Move right** → pop from right → push onto left.  
- **Result strings** → look at the top 10 elements of the left stack.

The **cursor** is implicitly the boundary between the two stacks.

---

### 2.4 The “Bad” – StringBuilder + Cursor Index (Java)

**Pros**

- Uses Java’s fast `StringBuilder` for concatenation.
- Single array (string) – no extra deques.

**Cons**

- `insert` and `delete` are **O(n)** in the worst case because `StringBuilder` must shift characters.  
  For the given constraints (`k ≤ 40` and total ops ≤ 20 k) it’s fine, but the theoretical complexity is weaker.  
- Code must carefully manage index bounds; a small bug can cause `IndexOutOfBoundsException`.  
- Not ideal for interviewers who expect *pure* stack or deque solutions.

> **When to use it?**  
> If you’re coding a small‑scale prototype or a LeetCode submission, this works perfectly. For an interview, bring up the stack approach instead.

---

### 2.5 The “Ugly” – Classic Singly‑Linked List

Some people implement the editor with a custom **node‑based linked list**:

- Each node stores a character and a `next` pointer.
- The cursor is a pointer to a node.
- Insertion / deletion / movement are all O(k) by traversing `k` nodes.

**Why it’s ugly**

| Issue | Explanation |
|-------|-------------|
| **Pointer complexity** | Must maintain `prev` pointers for left movement. |
| **Memory overhead** | Every node is an object – 8 bytes (pointer) + object header ≈ 32 bytes per char. |
| **Harder to debug** | Off‑by‑one errors are common. |
| **Interview risk** | Interviewers often reject low‑level pointer code unless you’re explicitly asked to implement a linked list. |

If you need a *real* doubly‑linked list, you should use the standard library (`LinkedList<Character>`) instead of writing your own.

---

### 2.5 The “Follow‑Up” – Why Linked‑List Isn’t Needed

The problem’s small `k` (≤ 40) allows *any* solution that touches at most `k` characters to pass.  
However, interviewers also test *thinking about complexity*.  
A stack‑based solution shows you understand how to maintain *amortised* O(1) operations, which is more impressive than a naive `StringBuilder` hack.

---

### 2.6 Edge Cases You Must Test

| Edge | What to check |
|------|---------------|
| Move left past start | `cursorLeft(100)` → cursor stays at 0. |
| Move right past end | `cursorRight(100)` → cursor stops at `text.length()`. |
| Delete more than available | `deleteText(10)` when only 3 chars left → returns 3. |
| Cursor at empty string | All operations should work, returning empty strings for cursor moves. |
| Adding an empty string | No change, cursor stays. |
| Querying when cursor < 10 | Return all left chars. |

Always run a quick script to verify these in your language of choice.

---

### 2.7 Complexity Analysis

| Operation | Time | Extra Space |
|-----------|------|-------------|
| `addText` | **O(k)** | O(k) for the new characters |
| `deleteText` | **O(k)** | O(k) removed characters |
| `cursorLeft` | **O(k)** | O(1) – just moving items between stacks |
| `cursorRight` | **O(k)** | O(1) |

Memory usage is linear in the number of characters stored (≈ 2 × `n` bytes for two stacks).

---

### 2.8 Quick‑Reference Code Snippets

| Language | Key Data Structure | One‑liner for `deleteText(k)` |
|----------|-------------------|--------------------------------|
| **Java** | `StringBuilder text; int cursor;` | `return Math.min(k, cursor);` |
| **Python** | `deque left, right;` | `while removed < k and self.left: self.left.pop();` |
| **C++** | `string text; size_t cursor;` | `int remove = min(k, (int)cursor);` |

> *Tip*: Keep the **left stack** as the “history” of the editor. This makes the result string trivial: just look at the last 10 elements.

---

### 2.9 How to Ace the Interview

1. **Explain the stack idea first.**  
   Show the two boundaries and how each operation maps to stack ops.
2. **Show complexity on paper** – O(k) for all four methods.
3. **Write the code** – keep it short; add comments to explain every line.
4. **Test quickly** – write a few assertions that cover the sample from the problem statement.
5. **Mention the follow‑up** – “What if k could be up to 10⁵?” → you’d still be in O(k) territory.

Recruiters love candidates who can *abstract* a problem (cursor + text) into a clean data‑structure (two stacks).  That’s the trick.

---

### 2.10 Call to Action

- **Try the code on LeetCode** – copy the snippets into the editor and run the sample test.  
- **Push your solution to GitHub** with a clear README; recruiters often check your GitHub.  
- **Apply to coding interview positions** that require *string manipulation* skills – e.g., Backend Engineer, Mobile Developer, or DevOps.  
- **Schedule a mock interview** with a friend; use this problem as the *core exercise*.

> *Your next interview question might be: “Can you implement a cursor‑based text editor in your preferred language?” – come prepared with the stack solution above!*

---

### 2.11 Final Thoughts

The LeetCode “Design a Text Editor” problem isn’t about magical algorithms; it’s a **simulation puzzle** that tests:

- Understanding of *amortised* time.
- Mastery of basic data‑structures (stacks, deques).
- Clean, bug‑free code.

The two‑stack solution is the **gold standard** for interviews.  The StringBuilder approach is neat in Java, while C++ offers a very similar `string + cursor` pattern.  

Pick the language you’re most comfortable with, run the code against the LeetCode judge, and you’ll walk into your next interview with confidence.

> **Ready to tackle the next “Hard” LeetCode challenge?**  
> Stay tuned for more bite‑size interview‑ready solutions – drop a comment or DM me if you need help prepping for your next coding interview!

---

### 2.12 Tags & Keywords

- `Design a Text Editor`
- `LeetCode 2296`
- `Java StringBuilder`
- `Python deque`
- `C++ two stacks`
- `string manipulation interview`
- `cursor movement`
- `job interview coding`
- `O(k) time complexity`
- `coding interview prep`

--- 

> **Happy coding – and good luck landing that dream tech job!**