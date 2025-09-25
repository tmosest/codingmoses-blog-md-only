---
title: LeetCode 316. Remove Duplicate Letters - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 316 – *Remove Duplicate Letters*  
> **The Good, The Bad, and The Ugly** – How I cracked this interview‑style problem and what you can learn from it.

> *Keywords: Remove Duplicate Letters, LeetCode 316, smallest subsequence, stack algorithm, interview coding, Java, Python, C++.*

---

### 1️⃣ Problem Recap (LeetCode 316)

> **Given** a string `s` consisting of lowercase English letters.  
> **Goal**: Remove duplicate letters so that every letter appears **once** and **only once**.  
> The resulting string must be the *lexicographically smallest* among all possible results.

| Example | Input | Output |
|---------|-------|--------|
| 1 | `"bcabc"` | `"abc"` |
| 2 | `"cbacdcbc"` | `"acdb"` |

**Constraints**

* `1 ≤ s.length ≤ 10⁴`
* `s` contains only lowercase letters

---

### 2️⃣ Intuition & “The Good”

| Why this problem is interesting |
|--------------------------------|
| It is a classic *greedy‑plus‑stack* problem. |
| It tests your ability to keep a *lexicographically* optimal structure while respecting order constraints. |
| A clear solution uses **O(1)** additional space (only 26 letters) and **O(n)** time – perfect for interview scenarios. |

**Why we love the stack solution**

1. **Directly mimics the idea of “keep the smallest possible prefix.”**  
   Each time we see a new character, we decide whether it should replace a larger character that will appear again later.
2. **O(1) amortised operations** – `push`, `pop`, and membership checks are constant time.
3. **Intuitive visualisation** – Think of the stack as the “current best subsequence” growing from left to right.

---

### 3️⃣ “The Bad” – What could go wrong?

| Pitfalls | Fix |
|----------|-----|
| Forgetting to remember the *last occurrence* of each character → you might pop a character that never re‑appears, breaking the result. | Pre‑compute a map `lastIdx[26]`. |
| Using a `Set` of visited characters incorrectly → you might skip a needed character. | Use a `boolean[] visited` (size 26) for O(1) look‑ups. |
| Mis‑ordering the comparison (`s[i] < stack[-1]` vs. `>`) → result not lexicographically minimal. | Double‑check the comparison direction. |
| Neglecting the *order constraint*: you can’t reorder letters arbitrarily. | The stack preserves the relative order of the final subsequence. |

---

### 4️⃣ “The Ugly” – Edge Cases & Common Mistakes

| Edge Case | Why it matters | Typical bug |
|-----------|----------------|-------------|
| Input has **only one unique letter** (e.g., `"aaaa"`). | The algorithm must return `"a"` not an empty string. | Forgetting to push the first letter. |
| **All letters are already distinct** (e.g., `"abcd"`). | The algorithm should return the same string. | Incorrectly popping even when `lastIdx` equals current index. |
| **Letter appears only once** but is *not* the smallest possible prefix (e.g., `"zab"`). | You cannot drop it – it is mandatory. | Over‑aggressive popping. |
| **Large strings** (up to 10⁴). | You must stay within O(n) time and O(1) space. | Using a dynamic `HashMap` instead of a fixed 26‑element array can hurt performance. |

---

## 📚 The Solution (Java, Python & C++)

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++** that follow the stack‑greedy approach described above.

---

### Java (Java 17+)

```java
import java.util.*;

public class Solution {
    public String removeDuplicateLetters(String s) {
        // 1. Record last index of each char
        int[] lastIdx = new int[26];
        for (int i = 0; i < s.length(); i++) {
            lastIdx[s.charAt(i) - 'a'] = i;
        }

        // 2. Greedy stack + visited flag
        boolean[] visited = new boolean[26];
        Deque<Character> stack = new ArrayDeque<>();

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            int idx = c - 'a';
            if (visited[idx]) continue;          // already in stack

            // Pop while we can make string smaller and the popped char will appear later
            while (!stack.isEmpty() && c < stack.peekLast() &&
                   i < lastIdx[stack.peekLast() - 'a']) {
                visited[stack.pop() - 'a'] = false;
            }

            stack.addLast(c);
            visited[idx] = true;
        }

        // Build result
        StringBuilder sb = new StringBuilder();
        for (char ch : stack) sb.append(ch);
        return sb.toString();
    }

    // Simple driver for quick testing
    public static void main(String[] args) {
        var sol = new Solution();
        System.out.println(sol.removeDuplicateLetters("bcabc"));     // abc
        System.out.println(sol.removeDuplicateLetters("cbacdcbc"));  // acdb
    }
}
```

---

### Python 3

```python
class Solution:
    def removeDuplicateLetters(self, s: str) -> str:
        # 1. Last index of each letter
        last_idx = {c: i for i, c in enumerate(s)}

        stack = []          # stack of chars
        visited = set()     # characters already on stack

        for i, ch in enumerate(s):
            if ch in visited:
                continue

            # Pop while we can keep the string smaller and the popped char will appear later
            while stack and ch < stack[-1] and i < last_idx[stack[-1]]:
                visited.remove(stack.pop())

            stack.append(ch)
            visited.add(ch)

        return "".join(stack)

# Quick test
if __name__ == "__main__":
    sol = Solution()
    print(sol.removeDuplicateLetters("bcabc"))     # abc
    print(sol.removeDuplicateLetters("cbacdcbc"))  # acdb
```

---

### C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string removeDuplicateLetters(string s) {
        // 1. Last position of each character
        vector<int> lastIdx(26, -1);
        for (int i = 0; i < (int)s.size(); ++i)
            lastIdx[s[i] - 'a'] = i;

        // 2. Stack + visited flag
        vector<bool> visited(26, false);
        vector<char> stack;            // acts as a stack

        for (int i = 0; i < (int)s.size(); ++i) {
            char c = s[i];
            int idx = c - 'a';
            if (visited[idx]) continue;            // already chosen

            while (!stack.empty() && c < stack.back() &&
                   i < lastIdx[stack.back() - 'a']) {
                visited[stack.back() - 'a'] = false;
                stack.pop_back();
            }

            stack.push_back(c);
            visited[idx] = true;
        }

        return string(stack.begin(), stack.end());
    }
};

// Minimal test harness
int main() {
    Solution sol;
    cout << sol.removeDuplicateLetters("bcabc") << '\n';     // abc
    cout << sol.removeDuplicateLetters("cbacdcbc") << '\n';  // acdb
}
```

---

> All three implementations run in **O(n)** time, use only **O(1)** extra space (fixed 26‑size arrays / booleans), and produce the lexicographically minimal string.

---

## 🎯 Interview‑Ready Checklist

| Checklist | ✅ |
|-----------|---|
| 1. Compute `lastIdx` for all 26 letters | ✔️ |
| 2. Use a *boolean[]* or *bitmask* for visited status | ✔️ |
| 3. Pop from stack only when the popped char appears later | ✔️ |
| 4. Preserve relative order via the stack | ✔️ |
| 5. Build result with `StringBuilder`/`StringBuilder`/`vector<char>` | ✔️ |
| 6. Keep code readable (no inline magic numbers) | ✔️ |
| 7. Write a short driver to sanity‑check on sample inputs | ✔️ |

---

## 📖 Blog Article (SEO‑Optimised)

Below is a fully‑fledged blog post you can drop into a Medium, Dev.to, or personal site. It’s formatted with Markdown headings, lists, and code snippets that will rank well for the keywords above.

---

```markdown
# The Good, The Bad, and The Ugly of LeetCode 316 – *Remove Duplicate Letters*

> **SEO‑Friendly Summary**: Master the *Remove Duplicate Letters* problem (LeetCode 316) with a stack‑based greedy algorithm. See Java, Python, and C++ solutions that are interview‑ready. Ideal for aspiring software engineers prepping for coding interviews.

## 1️⃣ Problem Snapshot
LeetCode 316 asks you to produce the *lexicographically smallest* subsequence of distinct letters from a given string. The string only contains lowercase letters (`a`–`z`), so the alphabet size is a constant 26.

**Key constraints**: `1 ≤ len(s) ≤ 10⁴`.  
**Desired complexity**: `O(n)` time, `O(1)` extra space.

## 2️⃣ The Good – Stack = Optimal Prefix

- **Greedy intuition**:  
  While building the answer left‑to‑right, always keep the smallest possible prefix.
- **Stack behaviour**:  
  The stack represents the *current best subsequence*.  
  When a new character `c` arrives:
  - If `c` is already in the stack → skip.
  - Else, pop from the stack all larger characters that will appear again later.
  - Push `c` onto the stack.
- **Why this works**:  
  Any larger character that will appear later can be safely removed because we can always re‑insert it later while keeping order.  
  The stack keeps the relative order of the final subsequence intact.

## 3️⃣ The Bad – Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Forgetting the *last index* of each character | Pre‑compute `lastIdx[26]`. |
| Using a dynamic `HashSet` incorrectly | Use a `boolean[] visited`. |
| Wrong comparison (`>` vs. `<`) | Ensure `c < stack.top()` when popping. |
| Over‑popping a character that won’t re‑appear | Check `i < lastIdx[top]`. |

## 4️⃣ The Ugly – Edge Cases

| Edge case | Explanation | Typical mistake |
|-----------|-------------|-----------------|
| `"aaaa"` | Must return `"a"`. | Skipping the first push. |
| `"abcd"` | Already distinct → return as‑is. | Popping when the last index equals current index. |
| `"zab"` | `z` is mandatory. | Removing it due to aggressive popping. |
| Very long strings | Must stay linear. | Using `HashMap` instead of fixed array can slow you down. |

## 5️⃣ Code Snippets

### Java

```java
import java.util.*;

public class Solution {
    public String removeDuplicateLetters(String s) {
        int[] lastIdx = new int[26];
        for (int i = 0; i < s.length(); i++) lastIdx[s.charAt(i) - 'a'] = i;

        boolean[] visited = new boolean[26];
        Deque<Character> stack = new ArrayDeque<>();

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            int idx = c - 'a';
            if (visited[idx]) continue;

            while (!stack.isEmpty() && c < stack.peekLast() &&
                   i < lastIdx[stack.peekLast() - 'a']) {
                visited[stack.pop() - 'a'] = false;
            }

            stack.addLast(c);
            visited[idx] = true;
        }

        StringBuilder sb = new StringBuilder();
        for (char ch : stack) sb.append(ch);
        return sb.toString();
    }
}
```

### Python

```python
class Solution:
    def removeDuplicateLetters(self, s: str) -> str:
        last_idx = {c: i for i, c in enumerate(s)}
        stack, visited = [], set()

        for i, ch in enumerate(s):
            if ch in visited: continue
            while stack and ch < stack[-1] and i < last_idx[stack[-1]]:
                visited.remove(stack.pop())
            stack.append(ch)
            visited.add(ch)
        return ''.join(stack)
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string removeDuplicateLetters(string s) {
        vector<int> lastIdx(26, -1);
        for (int i = 0; i < (int)s.size(); ++i)
            lastIdx[s[i] - 'a'] = i;

        vector<bool> visited(26, false);
        vector<char> st;        // acts as a stack

        for (int i = 0; i < (int)s.size(); ++i) {
            char c = s[i];
            int idx = c - 'a';
            if (visited[idx]) continue;

            while (!st.empty() && c < st.back() &&
                   i < lastIdx[st.back() - 'a']) {
                visited[st.back() - 'a'] = false;
                st.pop_back();
            }

            st.push_back(c);
            visited[idx] = true;
        }

        return string(st.begin(), st.end());
    }
};
```

## 6️⃣ Why This Matters for Your Next Job Interview

| Skill | How the problem tests it |
|-------|--------------------------|
| **Greedy reasoning** | You must decide locally (pop or keep) to guarantee global optimality. |
| **Stack manipulation** | Many interviews probe your comfort with LIFO data structures. |
| **Space‑time trade‑offs** | Knowing that you can solve in `O(n)`/`O(1)` is a big win. |
| **Edge‑case awareness** | Interviewers love to throw corner cases (e.g., `"aaaa"`, `"zab"`) to see if you anticipate them. |

### Quick Preparation Tip
Run through the above code on a whiteboard, explaining each line. Talk about:
- Why `lastIdx` matters.
- Why `visited` prevents duplicates.
- The invariant: "All letters in the stack are unique and appear only once in the answer so far."

> **Bonus**: Try to *extend* the solution for uppercase/lowercase or arbitrary alphabets to show flexibility.

## 7️⃣ Final Thoughts

LeetCode 316 is a **small but powerful** test of your algorithmic thinking. By mastering the stack‑based greedy approach, you’ll not only nail this problem but also demonstrate a toolkit of skills that interviewers look for.

Happy coding, and good luck on your next interview!

---  
*Author: [Your Name], aspiring software engineer & algorithm enthusiast. For more deep dives, check out [link to your GitHub] or follow me on Twitter @yourhandle.*
```

```

---

### How to Use the Post

1. **Publish**: Copy the Markdown into your platform of choice.  
2. **Image & Syntax Highlighting**: Most Markdown editors will auto‑highlight the code fences.  
3. **SEO**: Ensure the first paragraph contains the keyword phrase *Remove Duplicate Letters LeetCode 316*.  
4. **Link Building**: Add internal links to related posts (e.g., *Stack vs Queue*, *Greedy Algorithms*).  
5. **Social**: Share on Twitter, LinkedIn, or Reddit for increased visibility.

---

**Enjoy** mastering *Remove Duplicate Letters* and good luck in your upcoming interviews!
```

---

### Closing Thought

Mastering LeetCode 316 demonstrates solid algorithmic fundamentals and can set you apart in coding interviews. Keep the checklist handy, practice edge cases, and you’ll be confident stepping into your next coding interview.

Happy coding! 🚀
```

---

**Final Note**:  
- The article uses precise keyword phrases (`Remove Duplicate Letters`, `LeetCode 316`, `stack-based greedy algorithm`) that are likely to surface in search results.  
- The code snippets are language‑specific, well‑commented, and ready for direct copy‑paste into interview coding platforms.

--- 

All set! 🎉
