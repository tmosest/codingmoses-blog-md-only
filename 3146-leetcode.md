---
title: LeetCode 3146. Permutation Difference between Two Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Overview

**Leetcode #3146 – “Permutation Difference between Two Strings”**

* **Input**  
  * `s` – a string of unique lowercase letters (length ≤ 26).  
  * `t` – a permutation of `s`.  
* **Goal**  
  Compute  
  \[
  \sum_{c \in s} \bigl|\,\text{index}_s(c) - \text{index}_t(c)\bigr|
  \]
  where `index_s(c)` is the 0‑based position of character `c` in `s` and similarly for `t`.

The task is straightforward but it is a classic “hash‑map + array” interview problem that appears in many coding‑interview prep sets.

---

## 2. Solution Idea

* Build a mapping from character → position for `s`.  
* Iterate over `t`, lookup each character’s original index from the map, and add the absolute difference to a running sum.  
* Time complexity: **O(n)**, where *n* = `s.length()` (≤ 26).  
* Space complexity: **O(n)** for the hash map (or a 26‑element array).

Because the alphabet is only lowercase letters, an array of size 26 is a perfectly fine alternative that avoids the overhead of a hash table.

---

## 3. Reference Implementations

### 3.1 Java

```java
// Java 17
import java.util.*;

public class Solution {
    public int findPermutationDifference(String s, String t) {
        // Map character to its index in s
        int[] pos = new int[26];
        for (int i = 0; i < s.length(); i++) {
            pos[s.charAt(i) - 'a'] = i;
        }

        int diff = 0;
        for (int i = 0; i < t.length(); i++) {
            diff += Math.abs(pos[t.charAt(i) - 'a'] - i);
        }
        return diff;
    }

    // Simple main to demo
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.findPermutationDifference("abc", "bac"));   // 2
        System.out.println(sol.findPermutationDifference("abcde", "edbac")); // 12
    }
}
```

*Why arrays?*  
The input constraints guarantee at most 26 distinct characters, so an array indexed by `'a'..'z'` is O(1) per lookup and uses constant memory.

---

### 3.2 Python

```python
# Python 3.11
class Solution:
    def findPermutationDifference(self, s: str, t: str) -> int:
        # char → index in s
        pos = {c: i for i, c in enumerate(s)}
        return sum(abs(pos[c] - i) for i, c in enumerate(t))

# Demo
if __name__ == "__main__":
    sol = Solution()
    print(sol.findPermutationDifference("abc", "bac"))   # 2
    print(sol.findPermutationDifference("abcde", "edbac")) # 12
```

Python’s dictionary provides a clean, readable solution; the `sum` generator expression keeps the code compact.

---

### 3.3 C++

```cpp
// C++17
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findPermutationDifference(string s, string t) {
        vector<int> pos(26, -1);
        for (int i = 0; i < (int)s.size(); ++i)
            pos[s[i] - 'a'] = i;

        int diff = 0;
        for (int i = 0; i < (int)t.size(); ++i)
            diff += abs(pos[t[i] - 'a'] - i);

        return diff;
    }
};

int main() {
    Solution sol;
    cout << sol.findPermutationDifference("abc", "bac") << '\n';   // 2
    cout << sol.findPermutationDifference("abcde", "edbac") << '\n'; // 12
}
```

Using a `std::vector<int>` gives the same constant‑time lookup as the Java array. The code stays simple and efficient.

---

## 4. Blog Article: “Permutation Difference – The Good, The Bad, The Ugly”

### Title  
**Mastering Leetcode 3146 – “Permutation Difference Between Two Strings”**  
*A deep dive for interview‑ready developers (Java / Python / C++)*

---

### Meta Description  
Want to ace the *Permutation Difference* interview question? This article explains the problem, walks through optimal Java, Python, and C++ solutions, discusses edge cases, and offers SEO‑friendly tips for landing your next software‑engineering job.

---

### 1. The Good

| What | Why It’s Good |
|------|---------------|
| **Clear Problem Statement** | Every character is unique → no ambiguity about “occurrence”. |
| **Small Input Size** | Length ≤ 26 → allows constant‑time lookups with an array. |
| **Linear Complexity** | O(n) time & O(n) space, optimal for any string length. |
| **Language‑agnostic Approach** | Same idea works in Java, Python, C++, and even JavaScript. |

#### Code‑Snippets (One‑liner style)

*Java*  
```java
int diff = 0;
int[] pos = new int[26];
for (int i = 0; i < s.length(); i++) pos[s.charAt(i)-'a'] = i;
for (int i = 0; i < t.length(); i++) diff += Math.abs(pos[t.charAt(i)-'a'] - i);
```

*Python*  
```python
pos = {c:i for i,c in enumerate(s)}
diff = sum(abs(pos[c]-i) for i,c in enumerate(t))
```

*C++*  
```cpp
vector<int> pos(26,-1);
for (int i=0;i<s.size();++i) pos[s[i]-'a']=i;
int diff=0;
for (int i=0;i<t.size();++i) diff+=abs(pos[t[i]-'a']-i);
```

---

### 2. The Bad

| Issue | Impact | Mitigation |
|-------|--------|------------|
| **Using `HashMap` in Java** | Slight overhead; not necessary given the alphabet constraint. | Replace with `int[]` of size 26. |
| **Assuming Input Is Valid** | If `t` isn’t a permutation, map look‑ups could return `null` (Java) or undefined. | Add input validation or rely on problem guarantees in interviews. |
| **Complexity Misunderstanding** | Some candidates think O(n²) due to nested loops or repeated searches. | Emphasize that the optimal solution is linear. |

---

### 3. The Ugly

*Why some solutions go wrong:*

1. **Brute Force (O(n²))** – looping through `s` for each char in `t`.  
   - **Why bad?** For n=26 it’s still fine, but in interviews, a quadratic solution screams lack of insight.  
2. **Wrong Data Structures** – using `HashSet` instead of a map, losing the positional data.  
3. **Off‑by‑One Errors** – mixing 1‑based vs. 0‑based indices.  
   - **Debug tip:** Print both positions during the loop; you’ll immediately spot mismatches.

---

### 4. Interview‑Ready Tips

| Tip | How It Helps |
|-----|--------------|
| **Mention Constraints Early** | You’ll naturally steer to the array solution. |
| **Explain Complexity** | “We can’t afford O(n²) because we’re guaranteed at most 26 chars.” |
| **Show Edge‑Case Awareness** | “If the strings are identical, the answer is 0; if they are reversed, the answer is …” |
| **Time‑Space Trade‑off** | “Using an array gives constant‑time lookups; a HashMap would add overhead but still meet O(n) time.” |
| **Mention Language‑Specific Tricks** | Python’s dict comprehension, C++ `std::array`, Java’s `int[]`. |

---

### 5. SEO & Job‑Search Value

*Keywords to Sprinkle:*  
- Leetcode 3146  
- Permutation Difference problem  
- Java interview coding challenge  
- Python algorithmic problem  
- C++ string manipulation interview  
- Job interview preparation  
- Software engineer interview tips  

*Why these matter:*  
- Recruiters search for “Leetcode 3146” or “Permutation Difference” when looking for candidates who have solved similar problems.  
- Your article will surface in search results for *“how to solve permutation difference in Java”* or *“Python solution for Leetcode 3146”*.  
- By covering multiple languages, you demonstrate versatility – a key trait for tech roles.

---

### 6. Takeaway

The *Permutation Difference* problem is a textbook example of mapping indices and summing absolute differences. A single linear pass with an array suffices. Mastering this solution shows you can:

* Read and parse constraints quickly.  
* Translate a problem statement into an optimal data‑structure choice.  
* Write clean, language‑specific code that would impress interviewers.

Now go implement, practice, and nail that next coding interview!

---

## 7. Quick‑Start Summary

| Language | Code Snippet | Complexity |
|----------|--------------|------------|
| Java | `int[] pos = new int[26]; … sum(abs(...))` | O(n) time, O(1) space |
| Python | `pos = {c:i for i,c in enumerate(s)}; sum(abs(...))` | O(n) time, O(n) space |
| C++ | `vector<int> pos(26,-1); … sum(abs(...))` | O(n) time, O(1) space |

Happy coding!