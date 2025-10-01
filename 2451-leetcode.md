---
title: LeetCode 2451. Odd String Difference - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Code Solutions

Below are three self‑contained, ready‑to‑run implementations that solve LeetCode #2451 – **Odd String Difference** – in **Java**, **Python** and **C++**.  
All three use the same linear‑time, constant‑space strategy: compute a *difference array* for every word, group words by that array, and return the word that appears in a group of size 1.

| Language | Key Idea | Complexity |
|----------|----------|------------|
| **Java** | `HashMap<String, List<String>>` – the key is the encoded difference array. | O(n·k) time, O(n·k) space |
| **Python** | `defaultdict(list)` – the key is a `tuple` of differences. | O(n·k) time, O(n·k) space |
| **C++** | `unordered_map<string, vector<string>>` – the key is the encoded difference array. | O(n·k) time, O(n·k) space |

> **n** – number of words (≤ 100)  
> **k** – length of each word (≤ 20)

---

### Java 17

```java
import java.util.*;

public class Solution {
    public String oddString(String[] words) {
        // Map: encoded difference array -> list of words that produce it
        Map<String, List<String>> map = new HashMap<>();

        for (String w : words) {
            char[] ch = w.toCharArray();
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < ch.length - 1; i++) {
                sb.append(ch[i + 1] - ch[i]).append('#'); // '#' separates values
            }
            String key = sb.toString();

            map.computeIfAbsent(key, k -> new ArrayList<>()).add(w);
        }

        // The odd word is the only one in a group of size 1
        for (List<String> list : map.values()) {
            if (list.size() == 1) {
                return list.get(0);
            }
        }
        return "";   // never reached with valid input
    }

    // ---- Quick test harness ----
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.oddString(new String[]{"adc","wzy","abc"})); // abc
        System.out.println(sol.oddString(new String[]{"aaa","bob","ccc","ddd"})); // bob
    }
}
```

---

### Python 3

```python
from collections import defaultdict
from typing import List

class Solution:
    def oddString(self, words: List[str]) -> str:
        # key: tuple of differences, value: list of words
        groups = defaultdict(list)

        for w in words:
            diffs = tuple(ord(w[i+1]) - ord(w[i]) for i in range(len(w)-1))
            groups[diffs].append(w)

        # find the group with a single element
        for lst in groups.values():
            if len(lst) == 1:
                return lst[0]
        return ""  # impossible with valid constraints

# ---- Quick test harness ----
if __name__ == "__main__":
    sol = Solution()
    print(sol.oddString(["adc", "wzy", "abc"]))          # abc
    print(sol.oddString(["aaa", "bob", "ccc", "ddd"]))   # bob
```

---

### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string oddString(vector<string>& words) {
        unordered_map<string, vector<string>> mp;

        for (const string &w : words) {
            string key;
            for (size_t i = 0; i + 1 < w.size(); ++i) {
                key += to_string(w[i+1] - w[i]) + '#';   // '#' as separator
            }
            mp[key].push_back(w);
        }

        for (auto &p : mp) {
            if (p.second.size() == 1)
                return p.second[0];
        }
        return "";  // unreachable for valid input
    }
};

// ---- Quick test harness ----
int main() {
    Solution sol;
    vector<string> a = {"adc","wzy","abc"};
    cout << sol.oddString(a) << endl;        // abc
    vector<string> b = {"aaa","bob","ccc","ddd"};
    cout << sol.oddString(b) << endl;        // bob
}
```

---

## 2. Blog Article – “Odd String Difference: The Good, the Bad, and the Ugly”

> **Keywords**: LeetCode, Odd String Difference, coding interview, Java solution, Python solution, C++ solution, hashmap, difference array, string manipulation, job interview, interview prep, algorithm analysis, job seeker

### Introduction

When recruiters ask you to “find the odd string out,” they’re actually probing your ability to spot patterns, use hash tables efficiently, and think in constant‑time. The **Odd String Difference** problem on LeetCode (Problem 2451) is a classic interview staple that blends **string manipulation** with **hash‑based grouping**. In this post we’ll walk through:

* The problem statement in plain English  
* Why the “difference array” idea is elegant  
* Three polished solutions (Java, Python, C++)  
* What’s good, what’s bad, and what’s ugly about common approaches  
* SEO‑friendly take‑aways that can boost your interview résumé

Let’s dive in.

---

### Problem Recap

> **Given**: `words` – an array of `n` equal‑length lowercase strings  
> **Task**: For each word, compute a *difference array*  
> \[
> diff[i][j] = words[i][j+1] - words[i][j],\;\;0\le j < len-1
> \]  
> All words share the same difference array **except one**.  
> **Return** the unique word.

The alphabet is 0‑indexed (`'a' = 0`, `'b' = 1`, …).  
Examples are provided in the problem statement (see the LeetCode link).

---

### The Good: Why the HashMap Trick Works

1. **Linear Time**  
   Computing a difference array takes `O(k)` per word, where `k ≤ 20`.  
   Building the map is `O(n·k)` – fast enough for `n ≤ 100`.

2. **Constant Space per Word**  
   The map holds only `n` keys. Each key is a *representation* of a `k‑1` integer array.

3. **Simplicity**  
   Once you encode the difference array into a string (Java/C++) or a tuple (Python), grouping is trivial:  
   `map[key].add(word)`.

4. **Deterministic Answer**  
   The problem guarantees one odd word. Therefore, the group whose list has length 1 is the answer.

---

### The Bad: Why Brute‑Force Fails

A naive approach might compare every pair of words to see which has a different difference array. That would be `O(n²·k)`, which still passes the tiny constraints but is conceptually overkill:

* **Complexity**: `n` can be 100 – 10 000 pairwise comparisons, unnecessary overhead.  
* **Code**: Extra loops, more room for bugs.  
* **Interview perception**: Recruiters love O(1) or O(n) solutions. A quadratic solution signals lack of optimization.

---

### The Ugly: Edge‑Case Pitfalls

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| **Using a mutable `List<Integer>` as a map key** (Java) | HashMap fails because `List` mutability changes the hash value. | Convert to an immutable representation (`String` or `List.copyOf`). |
| **Using raw strings without separators** (Java/C++) | Two different difference arrays like `[1, 12]` and `[11, 2]` produce the same key `"112"`. | Append a delimiter (`'#'`) or use `Arrays.toString()` which inserts commas. |
| **Neglecting the negative differences** | Python tuple works, but C++ key string must preserve sign. | Convert each difference to a string including sign (`to_string(diff)`). |
| **Assuming the odd word is the first in the array** | Wrong answer if the odd word is later. | Always search for group size 1, not index. |
| **Missing the `k‑1` length** | For length 2 strings, difference array length 1. | Loop up to `len - 1`. |

---

### Code Walk‑through

#### Java (HashMap)

1. **Encode** each difference array into a string with `#` as a separator.  
2. **Group** words by this key.  
3. **Return** the only word in a group of size 1.

```java
String key = sb.toString();   // e.g., "3#-1#"
map.computeIfAbsent(key, k -> new ArrayList<>()).add(w);
```

#### Python (Tuple)

```python
diffs = tuple(ord(w[i+1]) - ord(w[i]) for i in range(len(w)-1))
groups[diffs].append(w)
```

Tuples are hashable and automatically distinguish between arrays of different lengths or values.

#### C++ (String Key)

```cpp
key += to_string(w[i+1] - w[i]) + '#';   // ensure uniqueness
mp[key].push_back(w);
```

A custom hash for `vector<int>` is also possible but unnecessary here.

---

### Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| HashMap/Dict + encoding | **O(n·k)** | **O(n·k)** |
| Brute‑force pairwise | O(n²·k) | O(1) |

Given the constraints, the HashMap solution is both fast and clear.

---

### How to Nail This in an Interview

1. **Explain the idea**: “Compute the difference array; words with the same array belong together.”  
2. **Show the encoding trick**: “I’ll turn the array into a string so I can use it as a key.”  
3. **Mention edge‑case handling**: “I separate numbers with ‘#’ to avoid collisions.”  
4. **State the final step**: “The group that holds only one word is the answer.”  

If the candidate can articulate this flow, they demonstrate a solid grasp of **hash tables**, **O(1) operations**, and **robust coding**.

---

### Final Thought: Make Your Résumé Shine

Include a short snippet in your portfolio:

> “Implemented the LeetCode ‘Odd String Difference’ problem using a hashmap to group words by their difference arrays – linear time, constant space per word.”  

This line tells recruiters you:

* Can **translate a pattern** into a **hash‑based data structure**  
* Understand the **trade‑offs** of different solutions  
* Deliver **concise, bug‑free code**

And it justifies that extra `#` you used in your string key.

Good luck – and remember: every “odd string” is an opportunity to showcase your algorithmic mindset.

---

### Take‑away Cheat Sheet

| Language | Key Type | Encode |
|----------|----------|--------|
| Java | `String` (with separator) | `StringBuilder` + `toString()` |
| Python | `tuple` | `tuple([...])` |
| C++ | `string` (with sign) | `to_string(diff) + '#'` |

Happy coding, and may your odd string always be the one you’re looking for! 

--- 

> **Follow us** for more interview‑ready LeetCode walkthroughs.  

--- 

> **Contact**:  
> Email: `dev@example.com`  
> LinkedIn: `linkedin.com/in/yourprofile`  

*(Feel free to adapt the code snippets for your preferred language and test environment.)*