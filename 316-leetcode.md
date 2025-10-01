---
title: LeetCode 316. Remove Duplicate Letters - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Remove Duplicate Letters – LeetCode 316  
> **The Good, The Bad, & The Ugly (with a Job‑Interview Twist)**  

---

## 1. What is the problem?

> **Input:** A string `s` of lowercase English letters  
> **Task:** Return the *smallest* lexicographical string that contains every distinct character of `s` **exactly once**.

> **Why does it matter?**  
> - It tests greedy reasoning, stack usage, and careful handling of duplicates.  
> - It is a *canonical* LeetCode problem that often shows up in algorithm interviews for roles that require strong data‑structure knowledge (software engineer, backend developer, etc.).

> **Examples**  
> ```
> s = "bcabc"   → "abc"
> s = "cbacdcbc" → "acdb"
> ```

---

## 2. Intuition – What “smallest lexicographical order” really means

When you have to keep the order of the characters but can drop some duplicates, you’re essentially looking for the *lexicographically smallest* permutation of the distinct letters that respects the original ordering.

Think of it like building a “stack” of characters:

1. **Push** the next character onto the stack.  
2. **Pop** the top of the stack if:  
   * the current character is smaller than the top, **and**  
   * the top will appear again later in the string.  

Why?  
- If the top is larger, placing a smaller character first gives a better (smaller) string.  
- If the top never appears again, we *must* keep it – otherwise we would lose that unique letter.

---

## 3. Approach – Greedy + Monotonic Stack

1. **Record last occurrence** of every character (so we know if a character will re‑appear).  
2. **Traverse the string** once, maintaining:  
   * `stack` – the current candidate answer.  
   * `inStack[26]` – a boolean array indicating whether a character is already in the stack (to avoid duplicates).  
3. For each character `c` at position `i`:  
   * If `c` is already in the stack, skip.  
   * While the stack is non‑empty, the top `t` is greater than `c`, and `i` is **before** the last index of `t`:  
     * Pop `t` from stack.  
     * Mark `t` as not in stack.  
   * Push `c` onto the stack and mark it as present.  
4. **Return** the concatenation of the stack.  

This runs in **O(n)** time and **O(1)** additional space (the stack may contain at most 26 characters for lowercase letters).

---

## 4. Code – One Solution in Three Languages

### 4.1 Python

```python
class Solution:
    def removeDuplicateLetters(self, s: str) -> str:
        last = {c: i for i, c in enumerate(s)}   # last index of each char

        stack, seen = [], set()
        for i, ch in enumerate(s):
            if ch in seen:
                continue
            while stack and ch < stack[-1] and i < last[stack[-1]]:
                seen.remove(stack.pop())
            stack.append(ch)
            seen.add(ch)
        return ''.join(stack)
```

### 4.2 Java

```java
import java.util.*;

class Solution {
    public String removeDuplicateLetters(String s) {
        int[] last = new int[26];
        for (int i = 0; i < s.length(); i++) {
            last[s.charAt(i) - 'a'] = i;
        }

        boolean[] inStack = new boolean[26];
        Deque<Character> stack = new ArrayDeque<>();

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            int idx = c - 'a';
            if (inStack[idx]) continue;

            while (!stack.isEmpty() && c < stack.peekLast()
                    && i < last[stack.peekLast() - 'a']) {
                char removed = stack.pollLast();
                inStack[removed - 'a'] = false;
            }
            stack.offerLast(c);
            inStack[idx] = true;
        }

        StringBuilder sb = new StringBuilder();
        for (char ch : stack) sb.append(ch);
        return sb.toString();
    }
}
```

### 4.3 C++

```cpp
class Solution {
public:
    string removeDuplicateLetters(string s) {
        vector<int> last(26, -1);
        for (int i = 0; i < (int)s.size(); ++i)
            last[s[i] - 'a'] = i;

        vector<bool> inStack(26, false);
        string st;
        for (int i = 0; i < (int)s.size(); ++i) {
            char c = s[i];
            int idx = c - 'a';
            if (inStack[idx]) continue;

            while (!st.empty() && c < st.back() && i < last[st.back() - 'a']) {
                inStack[st.back() - 'a'] = false;
                st.pop_back();
            }
            st.push_back(c);
            inStack[idx] = true;
        }
        return st;
    }
};
```

> **Why not a recursive or DP approach?**  
> Those would blow up the time complexity (and are not even feasible for the interview). The greedy‑stack solution is the *canonical* one and keeps interviewers impressed.

---

## 5. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Build `last` map | **O(n)** | **O(1)** (26 entries) |
| Main loop | **O(n)** | **O(26)** for stack + `seen` set |
| Final string | **O(1)** | — |

**Total:**  
- **Time:** `O(n)`  
- **Space:** `O(n)` in the worst case (but effectively `O(1)` for 26 unique lowercase letters).  

---

## 6. Edge Cases & Common Pitfalls

| Edge Case | What to watch for | Why it’s “bad” |
|-----------|-------------------|----------------|
| All letters already unique (`"abcde"`) | Stack never pops | Makes the algorithm look “unnecessary” if not explained |
| Repeating letters at the end (`"cbabc")` | Need to keep the last `'c'` even if smaller | Forgetting the “last occurrence” map leads to a wrong answer |
| Long string (`10^5` chars) | Only 26 distinct chars | Without `last` lookup, the stack could grow to `n` → O(n²) if you naïvely re‑scan for each pop |

---

## 7. The Good, The Bad & The Ugly – What Interviewers Look For

| Aspect | What Interviewers Praise | What Interviewers Criticize | What Interviewers *Don't* like |
|--------|--------------------------|-----------------------------|--------------------------------|
| **Good – The Greedy Proof** | You clearly state the “why” behind each pop. | If you jump into code without explaining the greedy rationale, you lose points. | Skipping the proof (just “I wrote a stack”) is a red flag. |
| **Good – The Monotonic Stack** | You mention that the stack is *monotonic* in decreasing order, which is the essence of the solution. | Interviewers expect you to know why a monotonic stack works, not just that it does. | Using an `ArrayList` and popping from the front (O(n)) is a *bad* choice. |
| **Bad – The Duplicate Check** | Using a `boolean[26]` (or a `Set`) prevents duplicates efficiently. | Over‑engineering (e.g., using a `HashSet` in C++ where a bool array is simpler) shows lack of optimization awareness. | Using `ArrayList.contains()` on the stack every time makes it O(n²). |
| **Bad – The Last‑Index Map** | A single pass to populate `last` is neat and O(n). | Forgetting to fill the map or using an unordered map in C++ with large overhead can mislead interviewers. | Not checking the *last* index leads to incorrect outputs. |
| **Ugly – The Code‑Style Choices** | Clear variable names (`last`, `stack`, `seen`). | Mixing loops, not using a proper stack data‑structure (e.g., using an array and manual indices). | Writing the whole algorithm inside a `main()` method and then “just copy‑paste” it into LeetCode. |

---

## 8. Why This Matters for *Your* Next Job

- **Stack & Greedy**: Many backend services involve stream processing (e.g., building logs, caching pipelines).  
- **Interview Visibility**: Recruiters often search “LeetCode 316 Java” or “Remove Duplicate Letters interview.”  
- **Problem‑solving mindset**: You show that you can think *greedily* and prove correctness in one pass.  

**SEO Keywords to Use**  
- Remove Duplicate Letters  
- LeetCode 316  
- Java Remove Duplicate Letters  
- Python LeetCode 316  
- C++ LeetCode 316  
- Stack interview problem  
- Lexicographical order algorithm  
- Algorithm interview questions  
- Software engineering interview prep  

These terms are the ones that recruiters will type when scanning candidates.

---

## 9. Take‑away Checklist

| ✅ | Item |
|---|------|
| ✅ | Understand the “smallest lexicographical order” requirement. |
| ✅ | Record the last occurrence of every character. |
| ✅ | Use a monotonic stack with a `seen` flag to avoid duplicates. |
| ✅ | Write clean, one‑pass code in Python, Java, and C++. |
| ✅ | Be ready to explain why each pop is safe (the top appears again later). |
| ✅ | Discuss complexity and prove correctness in the interview. |
| ✅ | Prepare to adapt the solution for **any alphabet size** (just change the array sizes). |

---

## 10. Final Word

Removing duplicate letters is more than a string‑manipulation exercise. It’s a *greedy* puzzle that demands:

1. **Precise reasoning** – why does the stack pop?  
2. **Data‑structure mastery** – use a stack, not a list.  
3. **Clean code** – time‑efficiency, memory‑efficiency, readability.

Showcasing this problem in your portfolio or during an interview signals that you’re comfortable with both theory and practical implementation—a quality that hiring managers crave in any software engineering role.

Good luck, and *happy coding!*  

--- 

> **If you liked this deep dive, hit the up‑vote button, subscribe to our channel for more interview‑ready solutions, and add this article to your LinkedIn profile.**  
> *Subscribe here:* https://www.youtube.com/channel/UC9RMNwYTL3SXCP6ShLWVFww?sub_confirmation=1  