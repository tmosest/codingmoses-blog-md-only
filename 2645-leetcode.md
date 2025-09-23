---
title: LeetCode 2645. Minimum Additions to Make Valid String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2645 – Minimum Additions to Make Valid String  
**Java | Python | C++** – Full working solutions + a short “good‑bad‑ugly” blog post that’s SEO‑friendly and will help you land your next tech interview.

---

### 📌 Problem Recap  
* **Goal:** Insert the fewest letters (`a`, `b`, or `c`) into a string `word` so that the resulting string can be split into one or more consecutive `"abc"` blocks.  
* **Input:** `1 ≤ word.length ≤ 50`, `word` only contains `a`, `b`, `c`.  
* **Output:** Minimum number of insertions required.  

---

## 🛠️ 3 Language‑Specific Solutions  

> All solutions run in **O(n)** time, **O(1)** extra space.

---

### Java 17

```java
class Solution {
    public int addMinimum(String word) {
        int k = 0;          // number of “abc” blocks needed
        char prev = 'z';    // sentinel larger than any of a,b,c

        for (int i = 0; i < word.length(); i++) {
            char cur = word.charAt(i);
            if (cur <= prev) {      // start a new block
                k++;
            }
            prev = cur;             // keep the last seen character
        }
        return k * 3 - word.length();   // total chars in blocks minus existing ones
    }
}
```

**Why it works**  
The string `"abc"` is strictly increasing (`a < b < c`).  
Whenever the current character is **not** larger than the previous one, we have to start a new `"abc"` block.  
`k` is the total number of blocks; each block contributes 3 characters, so `k*3` is the length of the final valid string.  
Subtract the original length to get the insertions needed.

---

### Python 3.10+

```python
class Solution:
    def addMinimum(self, word: str) -> int:
        k, prev = 0, 'z'
        for ch in word:
            if ch <= prev:       # new block required
                k += 1
            prev = ch
        return k * 3 - len(word)
```

The Python version is a 1‑liner inside the loop – identical logic to Java.

---

### C++17

```cpp
class Solution {
public:
    int addMinimum(string word) {
        int k = 0;              // blocks
        char prev = 'z';        // sentinel

        for (char ch : word) {
            if (ch <= prev) {   // start new block
                ++k;
            }
            prev = ch;
        }
        return k * 3 - static_cast<int>(word.size());
    }
};
```

---

## 📖 Blog Article: *The Good, the Bad, and the Ugly of LeetCode 2645*  

> **Keywords:** LeetCode 2645, Minimum Additions to Make Valid String, Java solution, Python solution, C++ solution, greedy algorithm, interview preparation, job interview, algorithm design.  

---

### Meta Description  
Master LeetCode 2645 – “Minimum Additions to Make Valid String” – learn the greedy trick, see Java/Python/C++ solutions, and understand the “good‑bad‑ugly” nuances to ace your next coding interview.

---

### 1️⃣ The Good: Why This Problem Is a Golden Interview Question  
- **Simplicity + Depth** – At first glance it looks like a toy problem, but the underlying greedy logic is a micro‑cosm of classic subsequence problems.  
- **Scalability** – The solution runs in linear time, which is a must‑have for interviewees.  
- **Language‑agnostic** – You can solve it in any language (Java, Python, C++), making it perfect for showing language‑specific strengths.  

### 2️⃣ The Bad: Common Pitfalls That Cost Interviewers  
| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Misinterpreting “increasing”** | Thinking `b <= a` means “new block” instead of `b > a`. | Remember the strict order `a < b < c`. |
| **Using a stack or queue** | Over‑engineering and using O(n) space unnecessarily. | Keep only a single `prev` character and a counter `k`. |
| **Forgetting the sentinel** | Comparing first char with an undefined previous char leads to a bug. | Initialize `prev` to a character larger than `c` (`'z'` works). |

### 3️⃣ The Ugly: Alternative Approaches That Make Things Messy  
- **Two‑Pointer simulation**: Iterating over a virtual `"abc"` cycle and incrementing an insertion counter each time the current character mismatches. It works but introduces extra logic (`(j + 1) % 3`) that’s easy to get wrong.  
- **Dynamic Programming**: Building a DP table for all prefixes is overkill for `n ≤ 50`. It’s a textbook example of *algorithmic bloat*.  
- **Regex + Replacement**: Some online solutions use regex to remove patterns like `abc`. While fast in practice, it’s fragile and not self‑explanatory.

### 4️⃣ How to Use This Problem in Your Job Hunt  
1. **Show the greedy insight** – interviewers love to see you recognize patterns.  
2. **Highlight the O(n) time and O(1) space** – these metrics are frequently asked.  
3. **Demonstrate language versatility** – present the solution in Java, Python, and C++ (as we did above).  
4. **Explain the pitfalls** – being able to point out what *not* to do indicates deep understanding.

---

## 🚀 Takeaway

- **Greedy Insight:** Start a new `"abc"` block whenever the current character is not larger than the previous one.  
- **Final Formula:** `insertions = blocks * 3 - word.length()`.  
- **Implementation:** A simple loop with two variables—`k` (blocks) and `prev` (last seen character).  

With this clean, 5‑line solution in any of the top languages, you’ll not only pass LeetCode 2645 but also impress interviewers by turning a seemingly trivial problem into a showcase of algorithmic maturity. Happy coding—and good luck landing that next job!