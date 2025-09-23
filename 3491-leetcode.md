---
title: LeetCode 3491. Phone Number Prefix - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# 📞 “Phone Number Prefix” – A Complete LeetCode Walk‑through (Java / Python / C++)

> **Goal**:  
> • Solve the LeetCode problem “Phone Number Prefix” (ID 3491).  
> • Deliver production‑ready code in **Java**, **Python**, and **C++**.  
> • Write a SEO‑optimized blog post that covers *the good, the bad, and the ugly* of the problem and showcases your interview‑ready mindset.

---

## 1. Problem Overview

**LeetCode 3491 – Phone Number Prefix**  
*Difficulty: Easy*

> **Input**: `String[] numbers` – an array of phone numbers (only digits, length ≤ 50).  
> **Output**: `boolean` – `true` if **no** phone number is a prefix of another; otherwise `false`.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `["1","2","4","3"]` | `true` | No number is a prefix of any other. |
| `["001","007","15","00153"]` | `false` | `"001"` is a prefix of `"00153"`. |

**Constraints**

| Constraint | Value |
|------------|-------|
| `2 <= numbers.length <= 50` | |
| `1 <= numbers[i].length <= 50` | |
| Only digits (`'0'`‑`'9'`) | |

---

## 2. Why It Matters in Interviews

* “Prefix” checks surface in real‑world problems: directory trees, auto‑complete, phone book, router tables, etc.  
* Demonstrates understanding of **string manipulation**, **sorting**, and optionally a **Trie** data structure.  
* The problem is short enough to be solved in 10‑15 min – perfect for a quick showcase in a screening call.

---

## 3. “The Good”

1. **Simplicity** – A single sorting step + linear scan.  
2. **Deterministic** – No randomization or heavy recursion.  
3. **Scales** – Even for the max constraints, the algorithm is instant.  
4. **Language‑agnostic** – The same idea works in any language.

---

## 4. “The Bad”

| Bad | Explanation | Remedy |
|-----|-------------|--------|
| **Requires O(N log N)** time | Sorting dominates – though trivial for N ≤ 50 | Acceptable; for huge datasets a Trie is O(total length). |
| **Space usage** | In-place sort uses minimal extra space, but some languages (Python) copy the list during `sort()`. | In‑place sorting or using a streaming Trie eliminates copying. |
| **Edge cases** | Empty strings or duplicate numbers (not allowed by constraints but possible in a real system). | Pre‑check or use a `Set` to deduplicate if needed. |

---

## 5. “The Ugly”

1. **Trie Implementation Bugs** – Forgetting to check node end‑of‑word flags or mis‑handling the root node.  
2. **Mis‑interpreting “prefix”** – A common pitfall is to think “substring” instead of “prefix.”  
3. **Ignoring Constraints** – Over‑engineering with heavy data structures when the problem is trivial.  

> *Tip*: Start with the simplest working solution; only go deeper if the interview asks for a more efficient data structure.

---

## 6. The Winning Solution (Sorting + `startsWith`)

The observation:  
If any number is a prefix of another, that pair will be adjacent after sorting lexicographically.  
Therefore, it’s enough to compare each element with the next one.

**Complexity**

| Operation | Time | Space |
|-----------|------|-------|
| Sort | `O(N log N)` | `O(1)` (in‑place) |
| Scan | `O(N)` | `O(1)` |
| Total | `O(N log N)` | `O(1)` |

---

## 7. Reference Implementations

### 7.1 Java (Java 17)

```java
import java.util.*;

class Solution {
    public boolean phonePrefix(String[] numbers) {
        // 1. Sort the array in place
        Arrays.sort(numbers);

        // 2. Compare each adjacent pair
        for (int i = 0; i < numbers.length - 1; i++) {
            String cur = numbers[i];
            String next = numbers[i + 1];
            if (next.startsWith(cur)) {
                return false;
            }
        }
        return true;
    }
}
```

*Why this is production‑ready?*  
* `Arrays.sort` is highly optimized.  
* Uses only the minimal number of variables.  
* No additional data structures – low memory footprint.

---

### 7.2 Python 3

```python
from typing import List

class Solution:
    def phonePrefix(self, numbers: List[str]) -> bool:
        numbers.sort()                     # O(N log N) in place
        for i in range(len(numbers) - 1):
            if numbers[i + 1].startswith(numbers[i]):
                return False
        return True
```

*Python‑specific notes*:  
* The `sort()` method sorts in place.  
* `startswith` is a highly optimized C routine.

---

### 7.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool phonePrefix(vector<string>& numbers) {
        sort(numbers.begin(), numbers.end());           // O(N log N)
        for (size_t i = 0; i + 1 < numbers.size(); ++i) {
            const string &cur  = numbers[i];
            const string &next = numbers[i + 1];
            if (next.compare(0, cur.size(), cur) == 0)   // prefix check
                return false;
        }
        return true;
    }
};
```

*Why `compare`?*  
* It avoids creating a substring and performs a direct memcmp, which is fast.

---

## 8. Optional Alternative – Trie (Java)

If you’re asked to demonstrate a more advanced data structure, a Trie is a perfect fit.

```java
class TrieNode {
    TrieNode[] next = new TrieNode[10];
    boolean isEnd = false;
}

class Solution {
    public boolean phonePrefix(String[] numbers) {
        TrieNode root = new TrieNode();
        for (String num : numbers) {
            TrieNode cur = root;
            for (char c : num.toCharArray()) {
                int idx = c - '0';
                if (cur.next[idx] == null) {
                    cur.next[idx] = new TrieNode();
                }
                cur = cur.next[idx];
                if (cur.isEnd) return false;          // existing number is a prefix
            }
            if (cur.next[0] != null || cur.next[1] != null ||
                cur.next[2] != null || cur.next[3] != null ||
                cur.next[4] != null || cur.next[5] != null ||
                cur.next[6] != null || cur.next[7] != null ||
                cur.next[8] != null || cur.next[9] != null)
                return false;                        // new number is a prefix of existing
            cur.isEnd = true;
        }
        return true;
    }
}
```

*Trade‑off*: O(total length) time and O(total length) space vs. the sorting solution’s `O(N log N)` time and `O(1)` space.  
For `N <= 50` and length `<= 50`, the sorting approach is usually preferable.

---

## 9. How to Showcase This on Your Resume / Portfolio

1. **Problem Summary**  
   * “Solved LeetCode 3491 – Phone Number Prefix (Easy) using a two‑step sorting + linear scan algorithm.”

2. **Technical Highlights**  
   * “Optimized solution: `O(N log N)` time, `O(1)` auxiliary space.”  
   * “Implemented in Java, Python, and C++ demonstrating language versatility.”

3. **Key Takeaways**  
   * “Illustrated understanding of string prefixes, sorting properties, and algorithmic time‑space trade‑offs.”  
   * “Prepared for follow‑up interview questions on Tries, complexity analysis, and edge‑case handling.”

---

## 10. SEO‑Optimized Blog Post

> **Title**: *“Cracking LeetCode 3491 – Phone Number Prefix: Java, Python, & C++ Solutions + Interview Tips”*  
> **Meta Description**: *Master LeetCode’s Phone Number Prefix problem with clear, production‑ready code in Java, Python, and C++. Learn the algorithm, complexity, and interview‑prep strategies.*  

---

### 10.1 Introduction

In today’s competitive tech landscape, *LeetCode* problems are a staple of interview preparation. One such problem, **“Phone Number Prefix”** (ID 3491), tests your grasp of string manipulation and algorithmic thinking. In this article, we walk through the **best solution**, discuss **performance trade‑offs**, provide **clean code** in **Java**, **Python**, and **C++**, and give you practical **interview tips** to impress hiring managers.

---

### 10.2 Problem Recap

*Given an array of numeric strings, return `true` if no string is a prefix of another.*  
- **Examples**  
  - `["1","2","4","3"]` → `true`  
  - `["001","007","15","00153"]` → `false`  

- **Constraints**  
  - 2 ≤ `numbers.length` ≤ 50  
  - 1 ≤ `numbers[i].length` ≤ 50  
  - Only digits `'0'`–`'9'`

---

### 10.3 The “Good, Bad, Ugly” Walk‑through

#### The Good
- **Straightforward**: Sort + compare adjacent pairs.  
- **Fast**: Even for the largest input, runs in milliseconds.  
- **Memory‑efficient**: O(1) extra space.

#### The Bad
- **Sorting cost**: O(N log N) time – negligible here, but a concern for huge lists.  
- **Edge‑case awareness**: Need to handle duplicates or empty strings if the spec changes.

#### The Ugly
- **Common pitfalls**: Using `substring` instead of `startsWith`, mis‑handling the root in Trie implementations, or overlooking that the sorted array guarantees adjacent prefix relationships.

---

### 10.4 The Winning Code

#### Java

```java
import java.util.*;

class Solution {
    public boolean phonePrefix(String[] numbers) {
        Arrays.sort(numbers);
        for (int i = 0; i < numbers.length - 1; i++) {
            if (numbers[i + 1].startsWith(numbers[i])) return false;
        }
        return true;
    }
}
```

#### Python

```python
class Solution:
    def phonePrefix(self, numbers: List[str]) -> bool:
        numbers.sort()
        for i in range(len(numbers) - 1):
            if numbers[i + 1].startswith(numbers[i]):
                return False
        return True
```

#### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool phonePrefix(vector<string>& numbers) {
        sort(numbers.begin(), numbers.end());
        for (size_t i = 0; i + 1 < numbers.size(); ++i) {
            if (numbers[i + 1].compare(0, numbers[i].size(), numbers[i]) == 0)
                return false;
        }
        return true;
    }
};
```

---

### 10.5 Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Sorting | `O(N log N)` | `O(1)` |
| Scan | `O(N)` | `O(1)` |
| **Total** | **`O(N log N)`** | **`O(1)`** |

With `N ≤ 50`, this solution is effectively instantaneous.

---

### 10.6 When to Use a Trie

If the interview specifically asks for a *Trie* (or you’re dealing with millions of phone numbers), a Trie offers:

- **O(total length)** time – linear in the sum of all string lengths.  
- **Space** proportional to the total number of characters.  

The trade‑off is added code complexity. For this LeetCode problem, the sorting approach remains the simplest win.

---

### 10.7 Interview‑Ready Tips

1. **Explain the key insight**: “After sorting, any prefix pair will be adjacent.”  
2. **Discuss complexity** up front.  
3. **Show code clarity** – use language‑idiomatic functions (`startsWith`, `compare`).  
4. **Address edge cases**: duplicates, empty strings (if the spec expands).  
5. **Mention alternative solutions** (Trie) and why you chose the simplest.

---

### 10.8 Final Thoughts

Solving “Phone Number Prefix” demonstrates:

- Mastery of **string operations**  
- Efficient use of **sorting**  
- Ability to write **clean, production‑ready code** in multiple languages  

These skills translate directly to real‑world tasks—think routing tables, user authentication, or database index design. Keep practicing, add this solution to your portfolio, and you’ll be ready to tackle similar interview puzzles with confidence.

---

**Happy coding!** 🚀  

--- 

### 11. SEO Keywords (for meta tags & headings)

- LeetCode Phone Number Prefix  
- Phone number prefix problem solution  
- Java LeetCode 3491  
- Python LeetCode 3491  
- C++ LeetCode 3491  
- interview algorithm for phone numbers  
- prefix check algorithm  
- sorting and startsWith  
- Trie interview question  
- coding interview preparation  
- algorithm time complexity  
- efficient string comparison  

Use these in your article titles, headings, and throughout the post to attract job seekers and hiring managers looking for concrete interview examples.