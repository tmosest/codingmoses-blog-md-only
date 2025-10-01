---
title: LeetCode 1209. Remove All Adjacent Duplicates in String II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Remove All Adjacent Duplicates in String II  
### Java | Python | C++ – Complete LeetCode Solution & Interview‑Ready Blog

> **Keywords**: *LeetCode 1209*, *Remove All Adjacent Duplicates*, *k‑duplicate removal*, *Stack solution*, *Java*, *Python*, *C++*, *algorithm interview*, *coding interview*, *job interview*, *software engineer*

---

## 1. Problem Recap

> **LeetCode 1209 – Remove All Adjacent Duplicates in String II**  
> *Difficulty:* Medium  
> *Link:* <https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string-ii/>

Given a string `s` and an integer `k`, repeatedly remove any group of **exactly `k` identical consecutive characters**.  
Return the final string after no more removals are possible.  
It is guaranteed that the final answer is unique.

---

## 2. Why This Problem Rocks for Interviews

| Aspect | Why It Matters |
|--------|----------------|
| **Stack + Counter** | Demonstrates mastery of *LIFO* data structures and greedy elimination. |
| **Linear Scan** | Shows you can process the string in O(n) time. |
| **Edge‑case Handling** | Tests robustness with strings of length 1, `k` up to 10⁴, and mixed removal chains. |
| **Language‑agnostic** | You can solve it in Java, Python, or C++, proving flexibility. |
| **Space Trade‑off** | Encourages discussion on using O(n) vs O(k) space. |

---

## 3. Core Idea

1. **Scan left‑to‑right**.
2. Keep a **stack** where each entry stores a character and how many consecutive copies have been seen so far.
3. When the current character matches the stack’s top, increment its counter.
4. If the counter reaches `k`, **pop** that entry – the group disappears.
5. After the scan, rebuild the string from the remaining stack entries.

---

## 4. Code in Three Languages

> All three snippets are **identical in logic**, only syntax differs.  
> They are fully commented to guide an interview or a self‑study session.

### 4.1 Java

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class Solution {

    // Helper class to keep a character and its consecutive count
    private static class CharCount {
        char ch;
        int count;
        CharCount(char ch, int count) {
            this.ch = ch;
            this.count = count;
        }
    }

    public String removeDuplicates(String s, int k) {
        // Stack of CharCount objects
        Deque<CharCount> stack = new ArrayDeque<>();

        for (char c : s.toCharArray()) {
            if (!stack.isEmpty() && stack.peek().ch == c) {
                // Same char as top – increase its count
                stack.peek().count++;
            } else {
                // New char – push with count 1
                stack.push(new CharCount(c, 1));
            }

            // If we reached k consecutive chars, pop the group
            if (!stack.isEmpty() && stack.peek().count == k) {
                stack.pop();
            }
        }

        // Build the result from the remaining stack entries
        StringBuilder sb = new StringBuilder();
        // We need to reverse the stack order
        while (!stack.isEmpty()) {
            CharCount cc = stack.removeLast();
            for (int i = 0; i < cc.count; i++) sb.append(cc.ch);
        }
        return sb.toString();
    }
}
```

---

### 4.2 Python

```python
class Solution:
    def removeDuplicates(self, s: str, k: int) -> str:
        # stack will hold tuples (char, current consecutive count)
        stack = []

        for ch in s:
            if stack and stack[-1][0] == ch:
                # Increment count for the same char
                stack[-1][1] += 1
            else:
                # New character group
                stack.append([ch, 1])

            # If we hit k, pop the group
            if stack[-1][1] == k:
                stack.pop()

        # Rebuild string from stack
        return ''.join(ch * cnt for ch, cnt in stack)
```

---

### 4.3 C++

```cpp
#include <string>
#include <vector>
#include <iostream>

class Solution {
public:
    std::string removeDuplicates(std::string s, int k) {
        // stack of pairs: character and its consecutive count
        std::vector<std::pair<char, int>> stack;

        for (char c : s) {
            if (!stack.empty() && stack.back().first == c) {
                stack.back().second++;          // same char – increment count
            } else {
                stack.emplace_back(c, 1);       // new char group
            }

            // Remove group if count reaches k
            if (!stack.empty() && stack.back().second == k) {
                stack.pop_back();
            }
        }

        // Build result
        std::string res;
        for (auto &p : stack) {
            res.append(p.second, p.first);
        }
        return res;
    }
};
```

---

## 5. Step‑by‑Step Walkthrough (Example)

> **Input**: `s = "deeedbbcccbdaa"`, `k = 3`

| Step | Current Char | Stack Top | Action | Stack (top → bottom) | Result so far |
|------|--------------|-----------|--------|-----------------------|---------------|
| 1 | d | - | Push (d,1) | d | - |
| 2 | e | d | Push (e,1) | e, d | - |
| 3 | e | e | Increment → (e,2) | e(2), d | - |
| 4 | e | e | Increment → (e,3) → POP | d | - |
| 5 | d | d | Increment → (d,2) | d(2) | - |
| 6 | b | d | Push (b,1) | b, d(2) | - |
| 7 | b | b | Increment → (b,2) | b(2), d(2) | - |
| 8 | c | b | Push (c,1) | c, b(2), d(2) | - |
| 9 | c | c | Increment → (c,2) | c(2), b(2), d(2) | - |
|10 | c | c | Increment → (c,3) → POP | b(2), d(2) | - |
|11 | b | b | Increment → (b,3) → POP | d(2) | - |
|12 | d | d | Increment → (d,3) → POP | - | - |
|13 | a | - | Push (a,1) | a | - |
|14 | a | a | Increment → (a,2) | a(2) | - |

Remaining stack: `a(2)`.  
Final string: **"aa"**.

---

## 6. Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | O(n) – single pass over the string | O(n) – single pass | O(n) – single pass |
| **Space** | O(n) – stack stores at most all characters | O(n) – stack of pairs | O(n) – vector of pairs |
| **Why O(n) space?** | In worst case, no group ever reaches size `k` (e.g., all distinct letters). | Same reason. | Same reason. |

---

## 7. Variants & Edge Cases

| Edge Case | What to watch out for |
|-----------|------------------------|
| `k = 1` | Every character forms a group; answer is the empty string. |
| `s` all same char | Multiple passes? No – one pass removes all in one go. |
| `k` larger than string length | No removal ever occurs. |
| Large `k` (10⁴) | Use `int` counters; no overflow. |
| Empty string | Return empty string immediately. |

---

## 8. Interview Tips

1. **Ask clarifying questions first** (e.g., “What happens if `k` equals 1?”).  
2. **Explain the stack design** before diving into code.  
3. Mention that you can **replace the stack with a two‑pointer approach** for optimal space if the interviewer demands it.  
4. Discuss **time–space trade‑offs** – could you keep only counts? What if you only need the length?  
5. Talk about **testing** – write unit tests for the edge cases listed above.  

---

## 8. How This Solves Your Job Search

- **Showcase** your ability to write clean, **interview‑grade** code in *Java*, *Python*, or *C++*.
- **Understand** why LeetCode 1209 is a *must‑solve* for front‑end/back‑end roles.
- **Prepare** for follow‑up questions: *“Can you implement this in place?”* or *“What about streaming input?”*
- **Demonstrate** strong fundamentals in data structures (Stack, Vector, Deque).

> **Tip:** Keep the code on your GitHub or LeetCode profile. When applying, add a small comment like: “Solved LeetCode 1209 in Java, Python & C++ – stack + counter approach, O(n) time, O(n) space.”

---

## 9. Takeaway

- The **stack‑with‑counter** solution is elegant, linear, and easy to explain.  
- It showcases a *greedy* strategy that is frequently requested in **software‑engineering interviews**.  
- By mastering this problem in three languages, you prove your versatility and coding fluency—exactly what recruiters look for.

> **Ready to land your next tech interview?**  
> 1️⃣ Clone this repository.  
> 2️⃣ Add the solutions to your coding portfolio.  
> 3️⃣ Start practicing the edge‑case tests above.  
> 4️⃣ Share your results on LinkedIn – tag it with `#LeetCode1209`.

Happy coding! 🚀