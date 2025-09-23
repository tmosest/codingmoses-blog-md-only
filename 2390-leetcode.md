---
title: LeetCode 2390. Removing Stars From a String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap – “Removing Stars From a String”

**LeetCode ID:** 2390  
**Difficulty:** Medium  

> You’re given a string `s` that contains lowercase letters and the character `'*'`.  
> In one operation you can:
> 1. Pick a `'*'` in `s`.  
> 2. Remove the *closest* non‑star character to its **left**.  
> 3. Remove the `'*'` itself.  
>  
> Perform the operation until no `'*'` remains.  
> Return the final string.

The input guarantees that the operation can always be performed, and the resulting string is unique.

> **Example**  
> `s = "leet**cod*e"` → `lecoe`

The key observation is that every `'*'` deletes the character just before it.  
So we can scan the string once, keeping only the characters that survive the deletions.

---

## 2. Solution Overview

The most common pattern for this problem is to treat the string as a *stack*:

* Push a letter onto the stack.  
* When a `'*'` is encountered, pop the top element of the stack (the closest non‑star character) and ignore the star.

The stack can be simulated with an array and an integer pointer (`top`) for **O(1)** push/pop operations.

| Language | Time Complexity | Space Complexity | Key Implementation Detail |
|----------|-----------------|------------------|---------------------------|
| Java     | O(n) | O(n) | `char[]` + `top` index |
| Python   | O(n) | O(n) | `list` as stack |
| C++      | O(n) | O(n) | `vector<char>` or `string` + `top` index |

Because every character is processed exactly once, the algorithm runs in linear time, which is optimal.

---

## 3. Reference Code

Below are clean, idiomatic solutions for **Java**, **Python**, and **C++**.  
Each implementation follows the same logic: simulate a stack with an array/list and use an index pointer.

### 3.1 Java

```java
public class Solution {
    public String removeStars(String s) {
        // Use a char array to avoid the overhead of a real Stack
        char[] buffer = new char[s.length()];
        int top = 0;                // points to next free slot

        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '*') {
                // Delete the closest non-star to the left
                top--;               // discard the previous character
            } else {
                buffer[top++] = c;   // push current character
            }
        }

        // Convert the retained part of the buffer to a string
        return new String(buffer, 0, top);
    }
}
```

**Why it works:**  
When we hit `'*'`, the closest non‑star is already on top of the buffer.  
Decrementing `top` “pops” that character out.  
All other characters stay in the buffer.

---

### 3.2 Python

```python
class Solution:
    def removeStars(self, s: str) -> str:
        stack = []                 # Python list is a dynamic array

        for ch in s:
            if ch == '*':
                stack.pop()        # remove the previous character
            else:
                stack.append(ch)   # keep the letter

        return ''.join(stack)
```

**Tip:** `list.pop()` removes the last element in O(1) time.

---

### 3.3 C++

```cpp
class Solution {
public:
    string removeStars(string s) {
        string res;            // will hold the final string
        res.reserve(s.size()); // reserve memory to avoid reallocations

        for (char c : s) {
            if (c == '*') {
                if (!res.empty()) res.pop_back();  // delete nearest left char
            } else {
                res.push_back(c);                  // keep the character
            }
        }
        return res;
    }
};
```

**Why reserve?**  
`reserve` prevents multiple memory reallocations, keeping the algorithm truly linear.

---

## 4. Blog Article – “The Good, the Bad, and the Ugly of Removing Stars from a String”

### 4.1 Hook – Catch Your Interviewer’s Eye

If you’re eye‑balling a **software engineering interview** and a LeetCode‑style question pops up, you’re in good shape.  
“Removing Stars From a String” (LeetCode 2390) is deceptively simple yet a great showcase for:

* **Stack‑based thinking**  
* **Linear‑time string manipulation**  
* **Space‑optimal code**  

Let’s break down what makes this problem a *stellar* interview question, why you’ll love it (the Good), the pitfalls to avoid (the Bad), and the sneaky edge cases that can trip you up (the Ugly).

---

### 4.2 The Good – Why This Problem Is a Winner

| Feature | Why It Helps |
|---------|--------------|
| **O(n) Time** | Demonstrates you can solve the problem in linear time—critical for interviewers. |
| **O(n) Space** | Simple stack simulation shows you understand auxiliary space trade‑offs. |
| **Clear Input/Output** | No complicated data structures, just strings and stars. |
| **Single‑Pass Solution** | Shows you can process input in one sweep—valuable for streaming data problems. |
| **Scalable** | Works for `n = 10^5` comfortably, meeting LeetCode constraints. |

A concise stack‑simulation solution wins points for elegance and readability—exactly what recruiters look for.

---

### 4.3 The Bad – Common Pitfalls to Watch Out For

1. **Using a Real Stack (`Stack` in Java, `std::stack` in C++)**  
   *These wrappers add unnecessary overhead.*  
   Use an array or a vector and a pointer for O(1) push/pop.

2. **Not Handling Edge Cases**  
   *An empty string after all deletions.*  
   Your code should return an empty string (`""`) gracefully.

3. **Misinterpreting “Closest Non‑Star”**  
   *Confusing left/right or next star.*  
   Remember: the star always deletes the character **immediately to its left** (not the nearest star).

4. **Neglecting Space Optimization**  
   *Building a new string with `+` or `StringBuilder` inside a loop can create quadratic time.*  
   Pre‑allocate or use a char array.

---

### 4.4 The Ugly – The Subtle Traps

| Tricky Scenario | Why It Looks Ugly | Fix |
|-----------------|-------------------|-----|
| **Multiple consecutive stars** (`erase*****`) | Each `'*'` deletes the previous character, but you might think only the first one matters. | Simulate a stack; every `'*'` pops one element. |
| **Stars at the beginning** | Input guarantees operations are possible, but you may still encounter a star as the first character in some malformed test. | Skip stars that would decrement `top` below 0 (or rely on the guarantee). |
| **Large Input** (`10^5` characters) | A naïve `StringBuilder.append()` inside a loop with repeated `.toString()` can blow up memory. | Append once at the end or use a pre‑allocated array. |
| **Character Set** | The problem restricts to lowercase letters, but a generic solution might accidentally handle uppercase or digits. | Explicitly check `c == '*'` only. |

Recognizing these “ugly” details demonstrates depth in your understanding—an invaluable skill in technical interviews.

---

### 4.5 SEO‑Optimized Summary

If you’re preparing for a technical interview or looking to boost your online portfolio, mastering *“Removing Stars From a String”* shows recruiters you can:

* Implement **O(n)** algorithms  
* Use **stack** or **array simulation** efficiently  
* Write **clean Java/Python/C++ code** that passes large test cases  

Adding the problem’s tags to your résumé or LinkedIn can increase visibility:  
`#LeetCode #Algorithm #Stack #Java #Python #C++ #SoftwareEngineer #InterviewPrep`

---

### 4.6 Takeaway

- **Good**: Linear time, stack simulation, single pass.  
- **Bad**: Avoid heavy wrappers, handle edge cases, pre‑allocate.  
- **Ugly**: Consecutive stars, boundary conditions, large inputs.

Armed with this knowledge, you can confidently tackle the problem, impress interviewers, and even turn the solution into a talking point during your next job interview. Happy coding! 🚀

--- 

*Feel free to copy the code snippets into your favorite IDE, run the sample tests, and then explain the logic in your next mock interview.*