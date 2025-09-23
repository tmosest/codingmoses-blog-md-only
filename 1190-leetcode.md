---
title: LeetCode 1190. Reverse Substrings Between Each Pair of Parentheses - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1190. **Reverse Substrings Between Each Pair of Parentheses**  
*Medium | LeetCode*  

> **Problem** – Given a string `s` containing only lowercase letters and parentheses, reverse the substrings inside each matching pair of parentheses, starting from the innermost pair.  
> Return the resulting string **without** any parentheses.  

> **Examples**  
> ```
> Input:  "(abcd)"
> Output: "dcba"
> 
> Input:  "(u(love)i)"
> Output: "iloveu"
> 
> Input:  "(ed(et(oc))el)"
> Output: "leetcode"
> ```  

> **Constraints**  
> • `1 <= s.length <= 2000`  
> • `s` only contains lowercase English letters and balanced parentheses.  

---

## 1.  Why This Problem Is a Gold‑mine for Interviews

* It forces you to think about **nested data structures** (a stack or recursion).  
* It tests your ability to **mutate strings efficiently** (in‑place vs. extra buffers).  
* Many interviewers ask this exact problem on a first round because the answer is clean, short, and covers a few core concepts.  

**SEO keywords:**  
- reverse parentheses  
- leetcode 1190 solution  
- java python c++ leetcode reverse parentheses  
- interview string manipulation  
- stack based leetcode problem  
- job interview string algorithm  

---

## 2.  The “Good” – A Classic Stack Solution (Python / Java / C++)

| Language | Approach | Time | Space |
|----------|----------|------|-------|
| Python  | 2‑pass: build `pair` array + walk with a direction flag (O(n)) | O(n) | O(n) |
| Java    | Iterative stack (O(n)) | O(n) | O(n) |
| C++     | Iterative stack (O(n)) | O(n) | O(n) |

Below we show two variants:

| Variant | What It Does | Complexity |
|---------|--------------|------------|
| **Iterative stack** | While iterating, push the index of every `'('`. When a `')'` is found, pop the last `'('` index, reverse the segment between them, and *skip* the parentheses. | **Time** O(n) – each character is processed a constant number of times. **Space** O(n) – stack + output buffer. |
| **Optimal “Jump”** | Pre‑compute matching pairs, then traverse the string once while “jumping” across matching parentheses and toggling direction. | **Time** O(n) – two linear passes. **Space** O(n) – pair array + stack. |

### 1.1  Iterative Stack Solution (Python)

```python
def reverseParentheses(s: str) -> str:
    stack = []          # stores indices of '('
    res   = []          # final characters
    cur   = 0           # current index in s

    while cur < len(s):
        ch = s[cur]
        if ch == '(':
            stack.append(cur)          # remember where the '(' is
        elif ch == ')':
            start = stack.pop()        # find its match
            # reverse the substring between start+1 and cur-1
            res.extend(reversed(s[start+1:cur]))
        else:
            res.append(ch)             # normal character
        cur += 1

    return ''.join(res)
```

> **Why it works** –  
> *The stack guarantees we always pair each `')'` with its **latest** unmatched `'('`.  
> By reversing the slice `[start+1:cur]` we undo the parentheses and reverse the inner substring at the exact moment we finish processing it.*  

### 1.2  Java Implementation

```java
import java.util.*;

public class Solution {
    public String reverseParentheses(String s) {
        StringBuilder sb = new StringBuilder();
        Deque<Integer> stack = new ArrayDeque<>();

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '(') {
                stack.push(i);
            } else if (c == ')') {
                int start = stack.pop();          // index of matching '('
                String segment = sb.substring(start + 1, sb.length());
                sb.delete(start + 1, sb.length());  // remove the processed part
                sb.insert(start + 1, new StringBuilder(segment).reverse());
            } else {
                sb.append(c);
            }
        }
        return sb.toString();
    }
}
```

> **Why it works** –  
> *The `StringBuilder` allows us to delete and insert in amortised O(1) per operation.  
> The stack keeps track of where a new “inner” substring begins, so when a `')'` is met we can isolate the segment, reverse it, and put it back.*

### 1.3  C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string reverseParentheses(string s) {
        string result;
        vector<int> stack;
        int n = s.size();

        for (int i = 0; i < n; ++i) {
            if (s[i] == '(') {
                stack.push_back(result.size());
            } else if (s[i] == ')') {
                int start = stack.back(); stack.pop_back();
                reverse(result.begin() + start + 1, result.end());
            } else {
                result.push_back(s[i]);
            }
        }
        return result;
    }
};
```

> **Why it works** –  
> *The `reverse` call on the `string::iterator` range performs the inner‑to‑outer reversal seamlessly.  
> By not storing the parentheses in `result`, we guarantee the final string contains none.*

---

## 2.  The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Readability** | The stack solution is 40‑line code, easy to read in interview. | A naive approach that builds a new string for every reversal can be *O(n²)*. | Using recursion without a global index can be confusing for beginners. |
| **Performance** | O(n) time, O(n) memory – optimal for the given limits. | The `reverse` operation inside the loop could be avoided by the “jump” method, but it costs little for n ≤ 2000. | In‑place reversal of the entire string is possible but adds complexity with two pointers. |
| **Language‑specific pitfalls** | In Java, `StringBuilder.reverse()` mutates the builder – remember to clone if you need the original. | In C++, `reverse` requires careful iterator ranges; off‑by‑one errors are common. | In Python, string slices create new objects – keep an eye on the constant factor. |

---

## 3.  Optimal “Jump” Solution (O(n)) – 2 Passes

This method is a favorite among LeetCode’s “hard” problems because it uses **no explicit reversal** inside the loop.

```python
def reverseParentheses(s: str) -> str:
    n = len(s)
    pair = [0] * n
    stack = []

    # 1st pass – build pair index map
    for i, ch in enumerate(s):
        if ch == '(':
            stack.append(i)
        else:                     # ch == ')'
            j = stack.pop()
            pair[i] = j
            pair[j] = i

    res = []
    i = 0
    step = 1
    while i < n:
        if s[i] in "()":
            i = pair[i]
            step = -step
        else:
            res.append(s[i])
        i += step
    return ''.join(res)
```

**C++**

```cpp
string reverseParentheses(string s) {
    int n = s.size();
    vector<int> pair(n);
    stack<int> st;
    for (int i = 0; i < n; ++i) {
        if (s[i] == '(') st.push(i);
        else if (s[i] == ')') {
            int j = st.top(); st.pop();
            pair[i] = j;
            pair[j] = i;
        }
    }

    string res;
    for (int i = 0, d = 1; i < n; i += d) {
        if (s[i] == '(' || s[i] == ')') {
            i = pair[i];
            d = -d;
        } else {
            res.push_back(s[i]);
        }
    }
    return res;
}
```

**Java**

```java
class Solution {
    public String reverseParentheses(String s) {
        int n = s.length();
        int[] pair = new int[n];
        Stack<Integer> st = new Stack<>();

        for (int i = 0; i < n; i++) {
            if (s.charAt(i) == '(') st.push(i);
            else if (s.charAt(i) == ')') {
                int j = st.pop();
                pair[i] = j;
                pair[j] = i;
            }
        }

        StringBuilder sb = new StringBuilder();
        for (int i = 0, d = 1; i < n; i += d) {
            char c = s.charAt(i);
            if (c == '(' || c == ')') {
                i = pair[i];
                d = -d;
            } else sb.append(c);
        }
        return sb.toString();
    }
}
```

---

## 4.  Complexity Recap

| Variant | Time | Space |
|---------|------|-------|
| Stack/Builder (first solution) | **O(n)** | **O(n)** |
| “Jump” two‑pass (optimal) | **O(n)** | **O(n)** |

Both run comfortably under LeetCode’s limits (`n ≤ 2000`). For larger inputs, the jump method is slightly faster because it does *one* linear traversal after the pre‑processing pass.

---

## 5.  How to Use This Solution in Your Next Job Interview

1. **Explain the intuition first.**  
   *“Because parentheses are nested, we can think of them as a stack or recursion.”*  
2. **Show the code, but don’t just hand it over.**  
   *Walk through the stack usage and why we delete the parentheses.*  
3. **Mention the trade‑offs.**  
   *Stack vs. recursion vs. two‑pass jump – each has its own readability cost.*  
4. **Ask a follow‑up question.**  
   *“What if the string were not balanced? How would you detect that?”*  

A strong candidate will finish in **under 5 minutes** and then extend the discussion to more generic parsing problems.

---

## 6.  Final Thought

The reverse‑parentheses problem is a **gateway** into the world of *linear‑time string algorithms*. Master it, and you’ll feel confident tackling a wide array of interview questions involving **balanced delimiters, substring reversal, and two‑pointer tricks**.

Good luck, and happy coding! 🚀

--- 

**Prepared by**  
*Your friendly AI coding tutor*  

--- 
**Keywords for SEO**: `reverse parentheses stack solution, optimal jump algorithm, interview string manipulation, stack based algorithm, LeetCode string problem, job interview coding practice`.