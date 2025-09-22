---
title: LeetCode 2914. Minimum Number of Changes to Make Binary String Beautiful - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code – LeetCode 2914 “Minimum Number of Changes to Make Binary String Beautiful”

The problem boils down to **pair‑wise** corrections.  
For a binary string of even length `s = s0 s1 … s(n-1)` we can partition it into
substrings of length 2:  
`(s0,s1) | (s2,s3) | …`.  
Each pair must become all‑zero or all‑one.  
Changing one character in a pair fixes the whole pair, so the optimal strategy
is:

```
answer = number of adjacent pairs (si,si+1) where si != si+1
```

That is the minimal number of flips required.

Below are clean, O(N) solutions in **Java**, **Python** and **C++**.

---

### Java

```java
// LeetCode 2914 – Minimum Number of Changes to Make Binary String Beautiful
class Solution {
    public int minChanges(String s) {
        int changes = 0;
        // iterate by steps of 2 – look at each pair
        for (int i = 0; i < s.length(); i += 2) {
            // if the two characters differ we need one flip
            if (s.charAt(i) != s.charAt(i + 1)) {
                changes++;
            }
        }
        return changes;
    }
}
```

---

### Python

```python
# LeetCode 2914 – Minimum Number of Changes to Make Binary String Beautiful
class Solution:
    def minChanges(self, s: str) -> int:
        changes = 0
        for i in range(0, len(s), 2):
            if s[i] != s[i + 1]:
                changes += 1
        return changes
```

---

### C++

```cpp
// LeetCode 2914 – Minimum Number of Changes to Make Binary String Beautiful
class Solution {
public:
    int minChanges(string s) {
        int changes = 0;
        for (int i = 0; i < (int)s.size(); i += 2) {
            if (s[i] != s[i + 1]) ++changes;
        }
        return changes;
    }
};
```

All three solutions run in **O(n)** time, use **O(1)** additional space, and
conform to LeetCode’s expected signature.

---

## 2.  Blog Article – “LeetCode 2914: The Beautiful Binary String – Good, Bad, & Ugly”

> **SEO Keywords**: *LeetCode 2914, minimum changes, beautiful binary string, coding interview, algorithm interview, Java solution, Python solution, C++ solution, greedy algorithm, pair‑wise correction, interview prep*

---

### 🎯 What is the Problem?

On LeetCode’s “Minimum Number of Changes to Make Binary String Beautiful”  
(Problem 2914) you’re given an even‑length binary string `s`.  
A string is **beautiful** if you can split it into substrings, each:

* Even length (so the split will always be in pairs of two characters)
* All‑zero or all‑one.

You may flip any character.  
Return the *minimum* flips needed to make the whole string beautiful.

---

### 🧠 Intuition – The Pair‑Wise Greedy

Because every substring must have even length, the natural split is into *adjacent pairs*:

```
s0 s1 | s2 s3 | … | s(n-2) s(n-1)
```

If a pair already contains two equal bits (00 or 11), we’re good.  
If not (01 or 10), we *must* flip **exactly one** of the two bits to make the pair beautiful.  
The flips in different pairs do not interfere with each other, so the optimal
strategy is simply:

> Count how many pairs have mismatched bits; that count is the answer.

No DP, no back‑tracking, no greedy “look‑ahead” needed.  
That’s why this problem has a **pure greedy** solution.

---

### 📈 Complexity Analysis

|   | Time | Space |
|---|------|-------|
| Solution | **O(n)** – one pass over the string | **O(1)** – only a counter |
| Where `n = s.length()` |

With `n ≤ 10⁵`, this runs in microseconds in all major languages.

---

### 🛠️ Implementation – “Good” Code

Below is the *clean, production‑ready* implementation in three languages.

*Java*

```java
class Solution {
    public int minChanges(String s) {
        int changes = 0;
        for (int i = 0; i < s.length(); i += 2) {
            if (s.charAt(i) != s.charAt(i + 1)) changes++;
        }
        return changes;
    }
}
```

*Python*

```python
class Solution:
    def minChanges(self, s: str) -> int:
        changes = 0
        for i in range(0, len(s), 2):
            if s[i] != s[i+1]:
                changes += 1
        return changes
```

*C++*

```cpp
class Solution {
public:
    int minChanges(string s) {
        int changes = 0;
        for (int i = 0; i < (int)s.size(); i += 2)
            if (s[i] != s[i+1]) ++changes;
        return changes;
    }
};
```

Each version:

* Handles the even‑length guarantee safely
* Uses a simple `for` loop that steps by two
* Performs a single `if` check per pair

---

### ⚠️ “Bad” Pitfalls to Avoid

| Pitfall | Why it’s bad | Fix |
|---|---|---|
| **Iterating over every character** (`i++` instead of `i+=2`) | You double‑count pairs and add unnecessary comparisons. | Use `i += 2` so each pair is inspected only once. |
| **Flipping the wrong bit** (e.g., always change the first bit) | No effect on the answer, but can mislead a future maintainer. | A single `if` check is enough; no need to decide *which* bit to flip. |
| **Assuming the string can be odd‑length** | LeetCode guarantees even length, but an extra check could cost O(1) extra time with no benefit. | Trust the problem constraints; avoid extra validation. |
| **Using a mutable string or array unnecessarily** | In Java/C++ this could allocate new memory each loop. | Work with `charAt` / array indexing; keep memory footprint constant. |

---

### 🤡 “Ugly” Anti‑Patterns

| Anti‑Pattern | Why it’s ugly | Example |
|---|---|---|
| **Recursion with heavy stack usage** | A naive recursive solution could hit stack overflow for `n = 10⁵`. | `int minChangesRec(String s, int idx)` that calls itself for every pair. |
| **Using regex or string splits** | Splitting the string into pairs and then checking each pair is overkill. | `s.split("(..)")` or `Pattern.compile("(?<=..)(?=..)"` etc. |
| **Copying the string on every iteration** | `s = s.replace(...)` inside a loop creates new strings each time. | Keep the original string untouched; only count mismatches. |
| **Hard‑coding indices** | `if (s[0] != s[1]) ...` then repeating for all pairs manually. | Use a loop to generalize. |

---

### 🚀 Take‑aways for Interviewers & Interviewees

1. **Recognize the pattern** – “Even length → split into pairs” immediately suggests a linear scan.
2. **Greedy correctness** – Each pair is independent; a local fix is globally optimal.
3. **Time/Space** – O(n)/O(1) is more than enough; no need for fancy data structures.
4. **Edge Cases** – The string is always even; the algorithm naturally handles “all zeros” or “all ones” with zero changes.
5. **Testing** – Cover:
   * All pairs equal → 0 changes
   * All pairs different → n/2 changes
   * Alternating 0101… → n/2 changes
   * Random mixes → compute manually

---

### 📚 Further Reading

* **Greedy Algorithms** – Understanding when local choices lead to a global optimum.
* **Dynamic Programming** – For more complex partitioning problems; here DP would be overkill.
* **LeetCode Discuss – Problem 2914** – Many contributors share variations and alternative proofs.

---

### 🎯 Final Verdict

*Good*: A one‑pass, pair‑wise counter is the optimal solution.  
*Bad*: Over‑engineering, unnecessary loops, or mutable copies waste time and space.  
*Ugly*: Recursion, regex, or hard‑coded indices make the code hard to read and maintain.

With the code snippets above, you can confidently submit to LeetCode, ace the interview, and showcase your ability to spot the elegant greedy solution. Happy coding!