---
title: LeetCode 2030. Smallest K-Length Subsequence With Occurrences of a Letter - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap  
**LeetCode 2030 – Smallest K‑Length Subsequence With Occurrences of a Letter**  
Given  

* `s` – a lowercase string  
* `k` – length of the subsequence to build  
* `letter` – a target character that must appear at least `repetition` times  
* `repetition` – minimum number of `letter` in the result  

Return the *lexicographically smallest* subsequence of `s` that satisfies the constraints.

> **Constraints**  
> * `1 ≤ repetition ≤ k ≤ |s| ≤ 5·10⁴`  
> * `letter` appears in `s` at least `repetition` times

The classic example:

```
s = "leetcode", k = 4, letter = 'e', repetition = 2
output → "ecde"
```

---

## 2. Core Idea

This is a textbook **monotonic‑stack + greedy** problem, very similar to *Remove Duplicate Letters* (LeetCode 316).  
We scan `s` from left to right, maintaining a stack that represents the current prefix of the answer.

While looking at a new character `c` we can *pop* the stack’s top `t` if:

1. **Lexicographic improvement** – `t > c`.  
2. **Enough room left** – after popping we still have enough characters (including `c`) to reach length `k`.  
3. **Letter constraint** – we cannot drop a `letter` that would leave us with fewer than `repetition` copies.

The “letter‑constraint” is the only twist compared to the classic problem.

After the loop we will have at most `k` characters in the stack; the final answer is the stack’s content in order.

---

## 3. Correctness Sketch

*Let `S` be the original string, `R` the stack after the scan, and `Ans = R` (top → bottom).*

1. **Feasibility** – We never pop a character if the remaining pool of characters (not yet processed) + current stack size < `k`.  
   Hence `|Ans| ≤ k`.  
   When the scan ends, the stack size is exactly `k` because we *always push* when the stack is shorter than `k` (except when we cannot due to the letter‑constraint).

2. **Letter count** – We keep two counters:
   * `remLetter` – how many `letter` still lie in the unprocessed suffix.
   * `usedLetter` – how many `letter` already in the stack.
   A pop that would drop a `letter` is only allowed if `usedLetter – 1 + remLetter ≥ repetition`.  
   Consequently, the final subsequence contains at least `repetition` copies of `letter`.

3. **Lexicographic minimality** –  
   Whenever a smaller character can replace a larger one *without violating constraints*, we do so (rule 1 & 2).  
   This greedy choice is locally optimal and, because all future decisions are independent of earlier ones, it yields a globally minimal string.

Thus, the algorithm is correct.

---

## 4. Complexity Analysis

* Time: **O(|s|)** – each character is pushed and popped at most once.  
* Space: **O(k)** – stack holds at most `k` characters.

---

## 5. Reference Implementation

Below are clean, self‑contained implementations in **Python, Java, and C++**.  
All three use the same greedy/stack logic described above.

> **Tip** – The code is written for a coding interview; keep it short, comment only where necessary, and avoid extra containers.

---

### 5.1 Python 3

```python
def smallestSubsequence(s: str, k: int, letter: str, repetition: int) -> str:
    # How many `letter` are still left in the suffix?
    rem_letter = s.count(letter)

    stack = []
    used_letter = 0          # letters already in stack

    for i, c in enumerate(s):
        # 1. pop while we can improve lexicographically
        while stack and stack[-1] > c:
            # Is it safe to pop?
            #   * after pop we still have enough chars to reach length k
            #   * if top is `letter`, we must still satisfy repetition
            top = stack[-1]
            if len(stack) - 1 + (len(s) - i) < k:
                break
            if top == letter and used_letter - 1 + rem_letter < repetition:
                break
            stack.pop()
            if top == letter:
                used_letter -= 1

        # 2. push if we still need more characters
        if len(stack) < k:
            if c == letter:
                stack.append(c)
                used_letter += 1
            elif k - len(stack) > repetition - used_letter:
                stack.append(c)

        if c == letter:
            rem_letter -= 1

    return ''.join(stack)
```

---

### 5.2 Java 17

```java
class Solution {
    public String smallestSubsequence(String s, int k, char letter, int repetition) {
        // Count how many `letter` are still remaining in the suffix
        int remLetter = 0;
        for (char c : s.toCharArray())
            if (c == letter) remLetter++;

        Deque<Character> stack = new ArrayDeque<>();
        int usedLetter = 0;          // letters already in stack

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);

            // pop while we can improve lexicographically
            while (!stack.isEmpty() && stack.peekLast() > c) {
                char top = stack.peekLast();
                // check if popping keeps feasibility
                if (stack.size() - 1 + (s.length() - i) < k)
                    break;
                if (top == letter && usedLetter - 1 + remLetter < repetition)
                    break;
                stack.pollLast();
                if (top == letter) usedLetter--;
            }

            // push if we still need more characters
            if (stack.size() < k) {
                if (c == letter) {
                    stack.addLast(c);
                    usedLetter++;
                } else if (k - stack.size() > repetition - usedLetter) {
                    stack.addLast(c);
                }
            }

            if (c == letter) remLetter--;
        }

        // Build result
        StringBuilder sb = new StringBuilder(k);
        for (char c : stack) sb.append(c);
        return sb.toString();
    }
}
```

---

### 5.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string smallestSubsequence(string s, int k, char letter, int repetition) {
        int remLetter = count(s.begin(), s.end(), letter);
        vector<char> st;          // stack
        int usedLetter = 0;

        for (int i = 0; i < (int)s.size(); ++i) {
            char c = s[i];

            // pop while improving lexicographic order
            while (!st.empty() && st.back() > c) {
                char top = st.back();
                // ensure we can still fill k after popping
                if ((int)st.size() - 1 + (int)s.size() - i < k) break;
                // ensure letter count stays >= repetition
                if (top == letter && usedLetter - 1 + remLetter < repetition) break;

                st.pop_back();
                if (top == letter) usedLetter--;
            }

            // push if we still need more characters
            if ((int)st.size() < k) {
                if (c == letter) {
                    st.push_back(c);
                    usedLetter++;
                } else if (k - st.size() > repetition - usedLetter) {
                    st.push_back(c);
                }
            }

            if (c == letter) remLetter--;
        }

        return string(st.begin(), st.end());
    }
};
```

---

## 6. Blog Article: “The Good, the Bad, and the Ugly of Greedy Subsequence Problems”

> **SEO Keywords**: *LeetCode 2030*, *smallest subsequence*, *greedy algorithm*, *monotonic stack*, *Java interview*, *Python interview*, *C++ interview*, *job interview coding problems*, *lexicographically smallest string*

---

### 6.1 Title  
**“The Good, the Bad, and the Ugly of Greedy Subsequence Problems – A Deep Dive into LeetCode 2030”**

### 6.2 Hook  
You’ve seen the classic *“Remove Duplicate Letters”* problem. Now imagine you have an extra rule: *you must keep a certain letter at least `r` times*. That’s LeetCode 2030 – a deceptively simple statement that hides a subtle twist. In this post I’ll walk you through the greedy stack solution that will make you look sharp in your next coding interview, and I’ll point out the pitfalls that keep many candidates stuck.

### 6.3 Problem Recap (for readers)  
- Input: string `s`, length `k`, letter `letter`, required repetitions `r`  
- Goal: smallest lexicographic subsequence of length `k` that contains at least `r` copies of `letter`  
- Constraints: `|s| ≤ 5·10⁴`

### 6.4 The Good: Why Greedy + Stack Works  
1. **Linear time, linear space** – Interviewers love algorithms that scale.  
2. **Elegant data structure** – A simple stack (`Deque`, `ArrayDeque`, `vector`) captures the prefix.  
3. **Clear intuition** – “Keep a character if it helps, pop if you can safely replace it with a smaller one”.  
4. **Language‑agnostic** – The same logic translates directly into Python, Java, or C++ – perfect for diverse interview stacks.

### 6.5 The Bad: Common Mistakes  
| Mistake | Why it fails | How to avoid it |
|---------|--------------|-----------------|
| **Dropping `letter` too early** | Without the repetition check you can pop a `letter` and end up with < `r` copies. | Keep two counters: `remLetter` (in suffix) and `usedLetter` (in stack). Pop only if `usedLetter-1 + remLetter ≥ r`. |
| **Pushing when stack < k but letter constraint violated** | You might fill the stack to `k` but run out of `letter` in the suffix. | When pushing a non‑`letter`, make sure `k - stackSize > r - usedLetter`. |
| **Over‑complicating the feasibility test** | Some solutions check `stack.size() + remainingChars < k` only after popping, which can break early. | Compute the condition *before* the pop: `stackSize-1 + remainingChars >= k`. |

### 6.6 The Ugly: Subtle Edge Cases  
1. **All remaining characters are `letter`** – The algorithm must refuse to pop a `letter` even if a smaller character is coming up.  
2. **The last character of `s` is the required `letter`** – If you push it too late, you might not be able to reach length `k`.  
3. **`k` equals the string length** – The stack will never pop; you just need to copy the string while respecting the repetition rule.

Being aware of these edge cases saves candidates from wrong “optimal” answers that still violate the repetition constraint.

### 6.7 Code Walk‑through  
*Insert a mini‑slide show of the Python/Java/C++ snippets above, with brief comments.*  
Explain line‑by‑line how the loop pushes and pops, why the counters work, and how the final string is built.

### 6.8 Interview Take‑aways  
- **Explain your counters** – Interviewers appreciate when you state why you need `remLetter` and `usedLetter`.  
- **Draw the stack** – On paper, sketch a few steps (e.g., `s = "abca"`, `k=3`, `letter='a'`, `r=1`).  
- **Show the feasibility check** – “We can only pop if we still have enough characters left.”  
- **Tie back to *Remove Duplicate Letters*** – Emphasize how the only difference is the letter‑constraint.

### 6.9 Conclusion  
LeetCode 2030 is a masterclass in **“greedy with a constraint”**. The monotonic stack solution is short, fast, and works for any language you prefer in the interview (Python, Java, C++). The trick is remembering the extra guard that protects the required letter. Master this, and you’ll be ready for the next “subsequence” problem that comes your way.

### 6.10 Call‑to‑Action  
> If you found this article useful, drop a comment with the hardest greedy problem you’ve tackled, and share the post on LinkedIn to help your network prepare for the next big interview.

---

## 7. Final Thoughts

LeetCode 2030 is a *single‑rule twist* on a well‑known greedy pattern. Master the stack‑based approach, keep the two counters in mind, and you’ll win the “smallest subsequence” title in minutes. Happy coding, and good luck with that job interview!