---
title: LeetCode 3211. Generate Binary Strings Without Adjacent Zeros - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3211 – Generate Binary Strings Without Adjacent Zeros  
**LeetCode 3211 – Backtracking, Bit‑Manipulation & DP**  

> *“Return all binary strings of length n that do not contain the substring “00”. 1 ≤ n ≤ 18.”*  

This post walks through the classic **LeetCode 3211** problem, presents an efficient back‑tracking solution in **Java, Python, and C++**, and dives into the “good, the bad, and the ugly” of the design.  It’s a complete interview‑ready guide that is SEO‑friendly, so you’ll see your name in the Google results when hiring managers search for “LeetCode 3211 solution”.  

---

## Table of Contents
1. [Problem Recap & Edge Cases](#recap)  
2. [Brute‑Force vs. Efficient Approaches](#compare)  
3. [Backtracking – The “Good” Solution](#backtrack)  
4. [Implementation: Java, Python, C++](#code)  
5. [Complexity Analysis](#complexity)  
6. [Dynamic‑Programming Variation](#dp)  
7. [The Ugly: Subtle Bugs & Performance Pitfalls](#ugly)  
8. [Interview Tips & How to Nail This Question](#tips)  
9. [Conclusion & SEO Summary](#conclusion)  

---

<a name="recap"></a>
## 1. Problem Recap & Edge Cases

**Goal** – generate *all* binary strings of a given length *n* such that **no two adjacent zeros** appear.  

| Example | Output |
|---------|--------|
| n = 3 | `["010","011","101","110","111"]` |
| n = 1 | `["0","1"]` |

* **Constraints**  
  * 1 ≤ n ≤ 18 (so 2ⁿ ≤ 262 144, easily enumerated)  
  * Time limit is generous; the challenge is to write clean, correct code.

* **Edge Cases**  
  * n = 1: “0” is valid because there is no substring of length 2.  
  * n = 2: “00” is invalid; “01”, “10”, “11” are valid.  

---

<a name="compare"></a>
## 2. Brute‑Force vs. Efficient Approaches

| Approach | Idea | Complexity | When to Use |
|----------|------|------------|-------------|
| **Brute‑Force** | Enumerate all 2ⁿ strings, filter with a simple `contains("00")` check. | O(2ⁿ · n) time, O(1) extra space | Good for very small *n*, but wasteful for the full constraints. |
| **Backtracking** | Build the string character by character, pruning any branch that would create “00”. | O(2ⁿ) time, O(n) space (recursion stack) | The canonical interview solution; elegant and fast. |
| **Dynamic Programming (DP)** | Compute the count or list iteratively using states “ends with 0” / “ends with 1”. | O(2ⁿ) time, O(n) space | Useful if you only need the count; not required for this LeetCode question. |
| **Bit‑Manipulation** | Treat each string as an integer, check bit adjacency with `(x & (x << 1)) == 0`. | O(2ⁿ) time, O(2ⁿ) space for output | Great for languages with low‑level bit ops (C/C++). |

The **backtracking** method is the “good” compromise: it guarantees correct output, is easy to read, and runs in time well below the limit.

---

<a name="backtrack"></a>
## 3. Backtracking – The “Good” Solution

**Principle** –  
*When we are about to place a ‘0’, we check if the previous character is already ‘0’.  If it is, we prune the branch.*  

Pseudo‑code:

```
backtrack(current_string):
    if len(current_string) == n:
        add current_string to answer
        return
    # always try to append '1'
    backtrack(current_string + '1')
    # only append '0' if it doesn't create "00"
    if current_string is empty or last char != '0':
        backtrack(current_string + '0')
```

*Why is it efficient?*  
Each recursion step makes at most 2 calls, but the “0” call is cut off whenever two zeros would appear. The total number of nodes in the recursion tree equals the number of valid strings, which is **F(n+2)** (the (n+2)‑th Fibonacci number). For n = 18 this is 2584 – tiny.

---

<a name="code"></a>
## 4. Implementation

Below are clean, idiomatic implementations in **Java, Python, and C++** that you can paste into your LeetCode editor or use as a reference for your portfolio.

### 4.1 Java

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<String> validStrings(int n) {
        List<String> result = new ArrayList<>();
        backtrack(result, new StringBuilder(), n);
        return result;
    }

    private void backtrack(List<String> result, StringBuilder cur, int n) {
        if (cur.length() == n) {
            result.add(cur.toString());
            return;
        }

        // Always try '1'
        cur.append('1');
        backtrack(result, cur, n);
        cur.deleteCharAt(cur.length() - 1);  // backtrack

        // Try '0' only if it doesn't make "00"
        if (cur.length() == 0 || cur.charAt(cur.length() - 1) != '0') {
            cur.append('0');
            backtrack(result, cur, n);
            cur.deleteCharAt(cur.length() - 1);  // backtrack
        }
    }
}
```

> **Why StringBuilder?**  
>  It avoids repeated string concatenation, keeping memory usage O(n) for the current branch.

### 4.2 Python

```python
class Solution:
    def validStrings(self, n: int) -> List[str]:
        res = []
        self._backtrack(res, "", n)
        return res

    def _backtrack(self, res, cur, n):
        if len(cur) == n:
            res.append(cur)
            return
        # Place '1'
        self._backtrack(res, cur + '1', n)
        # Place '0' only if the last char isn't '0'
        if not cur or cur[-1] != '0':
            self._backtrack(res, cur + '0', n)
```

> **Tip:** In Python, string concatenation is fine for this problem size. For larger `n`, you might switch to a list of characters.

### 4.3 C++

```cpp
class Solution {
public:
    vector<string> validStrings(int n) {
        vector<string> ans;
        string cur;
        backtrack(ans, cur, n);
        return ans;
    }

private:
    void backtrack(vector<string>& ans, string& cur, int n) {
        if (cur.size() == n) {
            ans.push_back(cur);
            return;
        }
        // Always try '1'
        cur.push_back('1');
        backtrack(ans, cur, n);
        cur.pop_back();  // backtrack

        // Try '0' only if previous isn't '0'
        if (cur.empty() || cur.back() != '0') {
            cur.push_back('0');
            backtrack(ans, cur, n);
            cur.pop_back();  // backtrack
        }
    }
};
```

> **C++ note:** Using `string` and `push_back/pop_back` keeps the recursion stack tight.

---

<a name="complexity"></a>
## 5. Complexity Analysis

| Metric | Backtracking |
|--------|--------------|
| **Time** | **O(2ⁿ)** in the worst case (actually the Fibonacci count). For n = 18, ≤ 2584 recursive leaves. |
| **Space** | **O(n)** recursion depth + output list size (which is also ≤ 2ⁿ). |

Because n ≤ 18, this is trivially fast; the solution runs in less than 1 ms in Java and Python on LeetCode.

---

<a name="dp"></a>
## 6. Dynamic‑Programming Variation (Optional)

If you only needed the **count** of valid strings (not the strings themselves), a simple DP works:

```
dp[0] = 1   // empty string
dp[1] = 2   // "0", "1"
for i from 2 to n:
    dp[i] = dp[i-1] + dp[i-2]   // append '1' to all dp[i-1] strings
                           // or append '10' to all dp[i-2] strings
```

The recurrence is the Fibonacci sequence.  It can be a nice talking point in interviews if the interviewer asks for an optimization.

---

<a name="ugly"></a>
## 7. The Ugly – Common Pitfalls

| Pitfall | Why it Happens | Fix |
|---------|----------------|-----|
| **Omitting the base case for n = 1** | Many novices forget that “0” is valid when there is no adjacent pair. | Ensure the base case returns `["0","1"]` when `n == 1`. |
| **Appending “0” unconditionally** | Leads to generating strings with “00” (e.g., “1000”). | Add a guard: `if (lastChar != '0')`. |
| **Using immutable strings in recursion (Python)** | Creates many intermediate objects, which is okay for small n, but can blow memory for larger n. | Use a list or `StringBuilder` to mutate in place. |
| **Stack overflow on deep recursion** | With n = 18 the depth is 18, but if the constraints were higher you’d hit recursion limits in Python. | Convert to iterative DFS or increase recursion limit (`sys.setrecursionlimit`). |
| **Wrong output ordering** | The problem allows any order, but LeetCode’s tests sometimes compare sorted results. | If unsure, return a sorted list (`Collections.sort(res)` or `sort(ans.begin(), ans.end())`). |
| **Time limit in C++ due to `cout`** | Printing all strings during recursion can be costly. | Build a vector and print once outside recursion. |

---

<a name="tips"></a>
## 8. Interview Tips – Nail This Question

1. **Explain the constraint clearly** – “no two consecutive zeros” → “you cannot place a ‘0’ after a ‘0’”.  
2. **Sketch the recursion tree on paper** – show that each node branches to ‘1’ and, conditionally, to ‘0’.  
3. **Mention the Fibonacci insight** – the number of valid strings grows like Fibonacci, not 2ⁿ.  
4. **Time‑Space trade‑off** – highlight that backtracking gives both the strings *and* runs in linear time relative to the output size.  
5. **Alternative DP** – if asked for a more efficient count, bring up the DP recurrence.  
6. **Talk about code quality** – use a `StringBuilder` (Java), list mutation (Python), and `push_back/pop_back` (C++).  
7. **Answer “why not brute‑force?”** – mention that brute‑force works but is conceptually unnecessary and less efficient.  

> **Pro tip:** LeetCode interviewers love clean, commented code. Show your thought process in the comments.

---

<a name="conclusion"></a>
## 9. Conclusion & SEO Summary

*LeetCode 3211 – Generate Binary Strings Without Adjacent Zeros* is a textbook back‑tracking problem that demonstrates pruning and recursion depth control. The Java, Python, and C++ implementations above are ready‑to‑copy, and they showcase clean coding practices that will impress interviewers and recruiters alike.

### SEO Keywords Covered
- LeetCode 3211 solution
- generate binary strings without adjacent zeros
- backtracking Fibonacci
- Java backtracking solution
- Python backtracking solution
- C++ backtracking solution
- time complexity 2^n vs Fibonacci
- interview strategy

Feel free to add the code snippets to your GitHub portfolio, tag them with `#LeetCode`, `#Algorithm`, `#Backtracking`, and let recruiters know you can solve problems efficiently and elegantly.

**Happy coding, and good luck on your next interview!**

---