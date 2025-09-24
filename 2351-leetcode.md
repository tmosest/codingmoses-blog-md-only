---
title: LeetCode 2351. First Letter to Appear Twice - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2351 – First Letter to Appear Twice  
**Java, Python & C++ Solutions + SEO‑Optimized Blog Post**  

> **LeetCode Problem 2351** – “First Letter to Appear Twice”  
> Difficulty: *Easy*  
> Tags: *String, Hash Table, Two Pointers*  

---

## 1️⃣ Problem Statement  

You’re given a string `s` of lowercase English letters.  
Return the *first* character that appears **twice** in the string.

> **Note**  
> A character `a` appears twice *before* another character `b` if the second
> occurrence of `a` occurs at a smaller index than the second occurrence of `b`.  
> `s` always contains at least one repeated letter.

**Examples**

| Input | Output | Reasoning |
|-------|--------|-----------|
| `"abccbaacz"` | `'c'` | `c`’s second occurrence (index 3) is before any other letter’s second occurrence. |
| `"abcdd"` | `'d'` | Only `d` repeats, so it is the answer. |

**Constraints**

- `2 ≤ s.length ≤ 100`
- `s` contains only lowercase English letters.
- There is at least one repeated letter.

---

## 2️⃣ Approach Overview  

The most straightforward and **O(n)** solution is to keep track of whether we have seen each letter before.  
Since there are only 26 lowercase letters, we can use:

1. **Boolean array** (size 26) – constant space.  
2. **HashSet** – also works but unnecessary overhead.

We iterate over the string once, mark each letter as seen the first time we encounter it, and return it immediately when we see it a second time.

### Why is this the *good* solution?

| Criterion | Why it’s Good |
|-----------|---------------|
| **Time Complexity** | `O(n)` – single pass over the string. |
| **Space Complexity** | `O(1)` – fixed-size array of 26 booleans. |
| **Simplicity** | Easy to read, no extra data structures. |
| **Deterministic** | No hash collisions, no randomization. |

### The *bad* side

- If you used a `HashSet`, you’d incur extra overhead and risk collisions in very large inputs (though not here).  
- Writing code that returns `'\0'` or `0` as a fallback might confuse future maintainers – but the problem guarantees a duplicate, so it’s fine.

### The *ugly* corner cases

- Empty string (not allowed by constraints).  
- All characters unique – also not allowed.  
- Non‑ASCII input – again, outside the problem’s scope.

---

## 3️⃣ Code Snippets  

Below are concise, ready‑to‑copy solutions in **Java, Python, and C++**.

---

### 3.1 Java

```java
// 2351. First Letter to Appear Twice
public class Solution {
    public char repeatedCharacter(String s) {
        // Boolean array for 26 lowercase letters
        boolean[] seen = new boolean[26];

        for (char c : s.toCharArray()) {
            if (seen[c - 'a']) {
                // First character that has been seen twice
                return c;
            }
            seen[c - 'a'] = true;
        }
        // Problem guarantees a duplicate, so this line is unreachable.
        return '\0';
    }
}
```

---

### 3.2 Python

```python
# 2351. First Letter to Appear Twice
class Solution:
    def repeatedCharacter(self, s: str) -> str:
        seen = [False] * 26          # 26 lowercase letters
        for ch in s:
            idx = ord(ch) - ord('a')
            if seen[idx]:
                return ch            # first repeated character
            seen[idx] = True
        # Unreachable due to problem guarantee
        return ''
```

---

### 3.3 C++

```cpp
// 2351. First Letter to Appear Twice
class Solution {
public:
    char repeatedCharacter(string s) {
        vector<bool> seen(26, false);
        for (char c : s) {
            int idx = c - 'a';
            if (seen[idx]) return c;
            seen[idx] = true;
        }
        // Unreachable
        return '\0';
    }
};
```

---

## 4️⃣ Blog Article: *The Good, the Bad, and the Ugly of LeetCode 2351*  

> **Title**: *Mastering LeetCode 2351 – First Letter to Appear Twice: A Clean, Efficient Java/Python/C++ Guide*  
> **Meta Description**: Solve LeetCode 2351 with a 3‑language solution. Learn the optimal O(n) strategy, pitfalls, and interview‑ready explanations.  

### 4.1 Why This Problem Rocks for Interviews  

- **Simplicity**: It tests basic string manipulation and frequency counting.  
- **Time/Space Trade‑off**: Forces you to choose between constant space and dynamic structures.  
- **Real‑World Analogy**: Detecting duplicates in log files, stream processing, or validating user input.  

### 4.2 The *Good* – Your Optimized Solution  

1. **O(n) Time** – One scan.  
2. **O(1) Space** – 26‑size boolean array.  
3. **Zero Hashing** – Avoids collisions and ensures determinism.  
4. **Readable Code** – Clear variable names, short loops.  

### 4.3 The *Bad* – Common Pitfalls  

| Mistake | Why It’s Bad |
|---------|--------------|
| Using a `HashSet` | Extra overhead, unnecessary for 26 letters. |
| Returning a magic value (`0`/`'\0'`) without comment | Future readers might wonder why that value is returned. |
| Ignoring the guarantee of a duplicate | Makes the code less robust in edge cases. |

### 4.4 The *Ugly* – Rare Edge Cases  

- **Unicode or uppercase letters**: The algorithm only works for ASCII lowercase.  
- **Very large input**: Not applicable because `s.length ≤ 100`.  
- **Multiple duplicates at the same index**: Impossible due to string nature.

### 4.5 Interview‑Ready Talking Points  

- **Explain the array indexing** (`c - 'a'`).  
- **Why we return immediately** on the second encounter.  
- **Why the solution is deterministic** – no hash collisions, no randomness.  
- **Mention time/space trade‑off** and why constant space is feasible.  

### 4.6 SEO & Job‑Hunting Tips  

- **Keywords**: “LeetCode 2351”, “First Letter to Appear Twice”, “Java string duplicate”, “Python interview questions”, “C++ coding challenge”, “algorithm interview tips”, “job interview coding”.  
- **Structure**: Use headings (`h1`, `h2`, `h3`) and bullet lists for readability.  
- **Add Code Blocks**: Markdown fenced code blocks with language specifiers for syntax highlighting.  
- **Show Complexity**: Include a “Time & Space Complexity” section to impress recruiters.  

---

## 5️⃣ Final Thoughts  

LeetCode 2351 is a micro‑challenge that packs the essentials of string handling, frequency counting, and efficient algorithm design.  
The optimal solution uses a fixed‑size boolean array, achieving linear time and constant space – a pattern you’ll see in many interview questions.  

With the Java, Python, and C++ implementations above, you’re ready to submit, share, and discuss this problem with confidence. Happy coding—and good luck landing that job!