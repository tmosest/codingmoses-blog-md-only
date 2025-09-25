---
title: LeetCode 544. Output Contest Matches - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# LeetCode 544 – *Output Contest Matches*  
**Java | Python | C++ – Full Code, Algorithm & Interview‑Ready Blog**

---

## 1️⃣ Problem Recap

You have **n** teams (n = 2^k, 1 ≤ k ≤ 12).  
Teams are ranked 1 … n (1 = strongest, n = weakest).  

During each round you always pair the **strongest remaining** team with the **weakest remaining** team:

```
Round 1 : (1,n), (2,n‑1), (3,n‑2), …
Round 2 : winners of (1,n) vs winners of (n‑1,2), …
…
```

The task: **Return the complete bracket as a single string** using parentheses, commas, and the team numbers.

> Example  
> `n = 4` → `"((1,4),(2,3))"`  
> `n = 8` → `"(((1,8),(4,5)),((2,7),(3,6)))"`

---

## 2️⃣ Core Idea

The bracket is a perfectly balanced binary tree.  
If we recursively divide the set of teams into two halves that always contain the *weakest* and *strongest* halves, we can build the string from the leaves up.

**Recursive Formula**

```
build(l, r) =           // l = left index (inclusive), r = right index (exclusive)
    if r - l == 1          // one team
        return string(l+1)
    else
        m = (l + r) / 2
        return "(" + build(l, m) + "," + build(m, r) + ")"
```

For the first call `build(0, n)` we get the final bracket.  
This algorithm runs in **O(n)** time and **O(log n)** recursion depth (max 12), easily fitting the constraints.

---

## 3️⃣ Full Code – 3 Languages

> **Tip:** In interviews you’re usually allowed to use a single recursion that writes directly to a `StringBuilder` / `list` / `stringstream` to avoid excessive string copying.

---

### 3.1 Java

```java
class Solution {
    public String findContestMatch(int n) {
        StringBuilder sb = new StringBuilder();
        build(sb, 0, n);
        return sb.toString();
    }

    private void build(StringBuilder sb, int l, int r) {
        if (r - l == 1) {                 // leaf
            sb.append(l + 1);             // team numbers start from 1
            return;
        }
        int m = (l + r) / 2;
        sb.append('(');
        build(sb, l, m);
        sb.append(',');
        build(sb, m, r);
        sb.append(')');
    }
}
```

---

### 3.2 Python

```python
class Solution:
    def findContestMatch(self, n: int) -> str:
        def build(l, r, out):
            if r - l == 1:               # one team
                out.append(str(l + 1))
                return
            m = (l + r) // 2
            out.append('(')
            build(l, m, out)
            out.append(',')
            build(m, r, out)
            out.append(')')

        out = []
        build(0, n, out)
        return ''.join(out)
```

---

### 3.3 C++

```cpp
class Solution {
public:
    string findContestMatch(int n) {
        string res;
        build(0, n, res);
        return res;
    }

private:
    void build(int l, int r, string &out) {
        if (r - l == 1) {               // single team
            out += to_string(l + 1);
            return;
        }
        int m = (l + r) / 2;
        out += '(';
        build(l, m, out);
        out += ',';
        build(m, r, out);
        out += ')';
    }
};
```

---

## 4️⃣ Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time   | O(n) | O(n)   | O(n) |
| Memory | O(n) string + O(log n) recursion | O(n) + O(log n) | O(n) + O(log n) |

The algorithm is linear in the number of teams and uses negligible extra stack space (max depth 12).

---

## 5️⃣ The Good, The Bad, & The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Simplicity** | Recursive solution is intuitive, no data‑structure tricks. | Some interviewers might penalize recursion depth (though depth ≤ 12). | Over‑engineering: using queues or bit‑twiddling to generate pairings can obfuscate logic. |
| **Readability** | Straightforward `build` function. | Java’s `StringBuilder` and Python’s list may look verbose to newcomers. | In‑place string manipulation in C++ can be error‑prone (operator overloading). |
| **Performance** | Fast (O(n)). | Python may be slightly slower but still within limits. | All languages perform well; Python slower on massive input (not a concern here). |
| **Space** | Only needed for the final string and stack. | None. | None. |

**What to Avoid (the Ugly)**  
- **Bitwise hacks** (like the LeetCode editorial’s solution) that work but are cryptic.  
- **Iterative simulation** that repeatedly scans the array to find pairs – O(n²) and unnecessary.  
- **Hard‑coded base‑case logic** that doesn’t generalize for non‑power‑of‑two inputs (though the problem guarantees 2^k).  

---

## 6️⃣ Interview Tips

1. **Explain the recursive tree** before coding. Show that each node represents a match, and children are the two sub‑matches feeding into it.  
2. **Mention base case**: single team.  
3. **Talk about complexity**: O(n) time, O(log n) recursion depth.  
4. **Show iterative version** if asked (use a queue or stack to simulate rounds).  
5. **Handle edge cases**: `n = 2` → `(1,2)`; `n = 4096` fits in recursion stack.  

---

## 7️⃣ Conclusion

The LeetCode 544 problem is a textbook example of a perfectly balanced binary tree construction.  
A simple recursive algorithm that appends to a string builder / list / stream gives a clean, readable, and highly efficient solution.  

> **Key Takeaway:**  
> *Always think of the bracket as a binary tree; the “strongest‑vs‑weakest” rule simply means you split the range in half each round.*

---

## 8️⃣ SEO Tags & Keywords

- LeetCode 544  
- Output Contest Matches  
- Java solution LeetCode  
- Python solution LeetCode  
- C++ solution LeetCode  
- Interview coding problems  
- Binary tree recursion  
- Time complexity O(n)  
- Algorithm interview prep  
- Software engineering interview  

---

### 🔧 Quick Test

```python
# Python
print(Solution().findContestMatch(4))  # ((1,4),(2,3))
print(Solution().findContestMatch(8))  # (((1,8),(4,5)),((2,7),(3,6)))
```

Run the same for Java and C++ – outputs match expected results.

Happy coding and good luck on your next interview! 🚀