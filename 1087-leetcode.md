---
title: LeetCode 1087. Brace Expansion - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1087 – Brace Expansion  
**LeetCode ID**: 1087 | **Difficulty**: Medium  
**Tags**: String, Recursion, Backtracking, Parsing

---

### Quick Problem Summary  
Given a string that contains letters and *choice groups* denoted by `{}` (e.g. `{a,b,c}`), generate **every** word that can be produced by picking one letter from each group.  
Return the resulting words **sorted lexicographically**.

**Examples**

| Input | Output |
|-------|--------|
| `"{a,b}c{d,e}f"` | `["acdf","acef","bcdf","bcef"]` |
| `"abcd"` | `["abcd"]` |

---

## Why This Problem Matters in Interviews  

1. **String Parsing** – The candidate must be comfortable scanning a string and grouping characters.  
2. **Backtracking / Recursion** – Generating all combinations is a classic combinatorial explosion problem.  
3. **Lexicographic Ordering** – Shows awareness of sorting and the need to return results in a specified order.  
4. **Time / Space Analysis** – Interviewers expect a discussion of O(∏k) complexity, where k is the size of each group.  

Demonstrating a clean, readable solution in *multiple languages* shows versatility – perfect for a job‑search portfolio!

---

## The Idea – Build & Expand

1. **Parse the input** into a list of *options* for each position.  
   * A single letter → `[letter]`.  
   * `{a,b,c}` → `['a', 'b', 'c']`.  

2. **Recursively build words**.  
   * At depth *i*, choose one character from `options[i]` and append it to the current prefix.  
   * When the prefix length equals the number of groups, add the completed word to the answer list.

3. **Sort** the final list before returning.

The recursion depth equals the number of groups (≤ 50), so stack usage is trivial.

---

## Complexity Analysis  

| Metric | Explanation | Result |
|--------|-------------|--------|
| **Time** | For every group we iterate over its choices; total words = ∏ |options[i]|. | **O(∏ |options[i]|)** |
| **Space** | Recursion stack + result list. | **O(∏ |options[i]|)** (worst‑case) |

---

## Code Solutions

Below you’ll find *clean, commented* solutions in **Java**, **Python**, and **C++**.

> **Tip:** The code is intentionally minimalistic so you can paste it into your IDE or LeetCode sandbox without modification.

---

### 1. Java (Backtracking + Sorting)

```java
import java.util.*;

public class Solution {
    public String[] expand(String s) {
        // 1️⃣ Parse the string into a list of option lists
        List<List<Character>> groups = parse(s);

        // 2️⃣ Recursively generate words
        List<String> result = new ArrayList<>();
        backtrack(groups, 0, new StringBuilder(), result);

        // 3️⃣ Sort lexicographically
        Collections.sort(result);

        return result.toArray(new String[0]);
    }

    private List<List<Character>> parse(String s) {
        List<List<Character>> groups = new ArrayList<>();
        for (int i = 0; i < s.length(); ) {
            List<Character> opts = new ArrayList<>();
            if (s.charAt(i) == '{') {          // start of a group
                i++;                            // skip '{'
                while (s.charAt(i) != '}') {
                    char c = s.charAt(i);
                    if (c != ',') opts.add(c);
                    i++;
                }
                i++;                            // skip '}'
            } else {                           // single letter
                opts.add(s.charAt(i));
                i++;
            }
            groups.add(opts);
        }
        return groups;
    }

    private void backtrack(List<List<Character>> groups, int depth,
                           StringBuilder prefix, List<String> out) {
        if (depth == groups.size()) {
            out.add(prefix.toString());
            return;
        }

        for (char c : groups.get(depth)) {
            prefix.append(c);
            backtrack(groups, depth + 1, prefix, out);
            prefix.deleteCharAt(prefix.length() - 1); // backtrack
        }
    }
}
```

---

### 2. Python (Recursive DFS)

```python
from typing import List

class Solution:
    def expand(self, s: str) -> List[str]:
        groups = self._parse(s)
        res: List[str] = []
        self._dfs(groups, 0, [], res)
        return sorted(res)

    def _parse(self, s: str) -> List[List[str]]:
        groups: List[List[str]] = []
        i = 0
        while i < len(s):
            if s[i] == '{':
                i += 1
                opts: List[str] = []
                while s[i] != '}':
                    if s[i] != ',':
                        opts.append(s[i])
                    i += 1
                groups.append(opts)
                i += 1  # skip '}'
            else:
                groups.append([s[i]])
                i += 1
        return groups

    def _dfs(self, groups: List[List[str]], depth: int,
             cur: List[str], res: List[str]) -> None:
        if depth == len(groups):
            res.append(''.join(cur))
            return
        for ch in groups[depth]:
            cur.append(ch)
            self._dfs(groups, depth + 1, cur, res)
            cur.pop()          # backtrack
```

---

### 3. C++ (DFS + STL)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<string> expand(string s) {
        vector<vector<char>> groups = parse(s);
        vector<string> res;
        string cur;
        dfs(groups, 0, cur, res);
        sort(res.begin(), res.end());
        return res;
    }

private:
    vector<vector<char>> parse(const string &s) {
        vector<vector<char>> groups;
        for (size_t i = 0; i < s.size(); ) {
            vector<char> opts;
            if (s[i] == '{') {            // group
                ++i;
                while (s[i] != '}') {
                    if (s[i] != ',') opts.push_back(s[i]);
                    ++i;
                }
                ++i;                       // skip '}'
            } else {                       // single char
                opts.push_back(s[i]);
                ++i;
            }
            groups.push_back(opts);
        }
        return groups;
    }

    void dfs(const vector<vector<char>> &groups, int depth,
             string &cur, vector<string> &res) {
        if (depth == groups.size()) {
            res.push_back(cur);
            return;
        }
        for (char c : groups[depth]) {
            cur.push_back(c);
            dfs(groups, depth + 1, cur, res);
            cur.pop_back();               // backtrack
        }
    }
};
```

---

## Blog Article – “Brace Expansion: The Good, The Bad, and The Ugly”

### Title  
**Brace Expansion (LeetCode 1087): The Good, The Bad, and The Ugly – Mastering a Classic Interview Problem**

### Meta‑Description (for SEO)  
“Learn how to solve LeetCode 1087 – Brace Expansion – in Java, Python, and C++. Dive into the algorithm, complexity, and real‑world interview tips. Boost your coding interview résumé now!”

### Outline

| Section | Key Points |
|---------|------------|
| **Intro** | Why brace expansion shows your parsing + recursion skills. |
| **Problem Breakdown** | Definition, examples, edge cases. |
| **Good – What Makes It Easy** | Small input size, no nested groups, clear constraints. |
| **Bad – Common Pitfalls** | Forgetting to sort, mis‑parsing commas, off‑by‑one errors. |
| **Ugly – Harder Variants** | Nested braces, variable group sizes, huge output set. |
| **Algorithmic Insight** | Two‑phase approach: parse → DFS. |
| **Complexity Discussion** | Product of group sizes, O(∏k) time & space. |
| **Language‑Specific Code** | Java, Python, C++ snippets with comments. |
| **Interview Tips** | How to explain your solution, discuss edge cases, time/space trade‑offs. |
| **Takeaway** | Why mastering this problem boosts your interview confidence. |

---

### The Good – Why Brace Expansion is a Nice Interview Exercise

- **No Nested Braces** – Keeps parsing linear (`O(n)`), no recursion for parsing.  
- **Fixed Alphabet** – Only lowercase letters, so no Unicode surprises.  
- **Small String Length (≤ 50)** – Guarantees the recursion depth stays tiny.  
- **Deterministic Output** – After sorting, you always know exactly what to return.  

These constraints let you focus on two core skills: string scanning and backtracking.

---

### The Bad – Where Interviewers Push You

| Pitfall | What to Watch For | Quick Fix |
|---------|------------------|-----------|
| **Comma Handling** | Forget to skip commas inside braces | Use `if (c != ',')` when building options |
| **Off‑by‑One** | Mis‑index when moving past `}` | Increment index *after* reading `}` |
| **Sorting** | Returning unsorted list | `Collections.sort(result)` in Java, `sorted(res)` in Python, `sort(res.begin(), res.end())` in C++ |
| **Empty String** | Unexpected behavior when s is empty (not allowed but good practice) | Handle gracefully, return `[]` or `[""]` as per spec |

These are common bugs that make your solution fail in the system tests.

---

### The Ugly – Extensions that Kill You

- **Nested Braces** – Requires a stack‑based parser.  
- **Large Output** – Product of group sizes can explode (e.g., `{a,b,c,d,e,f,g}` repeated 10 times).  
- **Wildcard Characters** – If you had to treat `*` or `?` as options, you'd need more complex matching.  

Understanding these variations helps you explain how your solution would scale and what changes would be necessary.

---

### The Algorithm – Parse + DFS

1. **Parsing**  
   * Scan the string once.  
   * Build a vector/array of lists: each list contains the possible characters for that position.  

2. **Depth‑First Search**  
   * Recursively build a prefix.  
   * At depth *i*, iterate over `options[i]`, append, recurse to *i + 1*.  
   * When the prefix length equals the number of groups, push it to the answer list.  

3. **Sort & Return**  
   * After DFS completes, sort the answer list and convert to the required data structure.

Why DFS? Because the output set is exactly the Cartesian product of all option lists, and DFS naturally explores every combination in `O(∏k)`.

---

### Complexity – Why It Matters

- **Time**: Each word takes `O(L)` to build where `L` is the number of groups, but you generate all words. Hence `O(∏ |options[i]|)` time.  
- **Space**: The recursion stack is `O(L)`, but the answer list stores all words → `O(∏ |options[i]|)` memory.  

You can explain that for typical interview constraints this is fine, but if the number of groups or group size grows, you’d hit exponential blow‑up.

---

### Language‑Specific Implementation (Code Snippets)

Include the three solutions shown above, each annotated to highlight parsing and backtracking sections. Encourage readers to try rewriting them in other languages (Go, Rust, etc.) – a great exercise for your portfolio.

---

### Interview Strategy – How to Nail the Explanation

1. **Explain the Two Phases** – “First I parse into options; then I DFS to build all combinations.”  
2. **Show the Recursion Tree** – Sketch a small example on the whiteboard.  
3. **Address Edge Cases** – Show what happens when there’s only one group, or when the output is large.  
4. **Talk About Sorting** – Highlight why sorted output is required.  
5. **Complexity Talk** – Mention the product of sizes; compare to worst‑case.  
6. **Optimization Discussion** – If the interviewer asks, explain that you could generate words *on‑the‑fly* and stream them if memory was a constraint.  

By following this roadmap you’ll answer every question a recruiter might throw at you.

---

### Takeaway – Why You Should Add Brace Expansion to Your Resume

- **Demonstrates Parsing Proficiency** – Systematically scans input.  
- **Shows Recursive Backtracking Mastery** – A staple of many coding interviews.  
- **Language Versatility** – Solved cleanly in Java, Python, and C++.  
- **Clear Complexity Argument** – You can discuss time/space trade‑offs confidently.  

Add the code to your GitHub profile, write a quick blog post, or even create a YouTube walkthrough. The more you can articulate *why* your solution works, the more impressive you’ll appear to hiring managers.

---

### Final Words

Brace Expansion is the perfect *“micro‑problem”* that lets you shine in a coding interview. Solve it once, and you’ll be ready for the harder variants, better at explaining recursion, and comfortable switching between Java, Python, and C++.  

Happy coding, and good luck on your next interview! 🚀

--- 

> **Ready to drop this into your portfolio?**  
> Copy one of the language snippets above, add a short README that explains the algorithm, and commit it to a public GitHub repo. Searchers find it via the SEO meta‑description – you’ll get the clicks you need!