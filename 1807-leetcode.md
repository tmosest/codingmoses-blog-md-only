---
title: LeetCode 1807. Evaluate the Bracket Pairs of a String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  Solution Code – 3 Languages  

Below you’ll find clean, production‑ready implementations for **Java**, **Python** and **C++**.  
Each one follows the same idea:

1. Build a hash‑map (`key → value`) from the `knowledge` list.  
2. Scan the string `s` once.  
   * When we see `'('`, we collect the characters until `')'`, look up the key in the map, and append the mapped value (or `"?"` if it doesn’t exist).  
   * All other characters are copied verbatim.  
3. Use an efficient string builder (`StringBuilder`, `vector<string>` + `join`, `std::string` + `reserve`) to keep the runtime linear.

---

### 🔧  Java

```java
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class Solution {
    public String evaluate(String s, List<List<String>> knowledge) {
        // 1. Build the lookup map
        Map<String, String> map = new HashMap<>();
        for (List<String> pair : knowledge) {
            map.put(pair.get(0), pair.get(1));
        }

        // 2. Scan the string
        StringBuilder ans = new StringBuilder(s.length());
        int i = 0;
        while (i < s.length()) {
            char c = s.charAt(i);
            if (c == '(') {
                // Extract key
                int j = i + 1;
                StringBuilder keyBuilder = new StringBuilder();
                while (j < s.length() && s.charAt(j) != ')') {
                    keyBuilder.append(s.charAt(j));
                    j++;
                }
                String key = keyBuilder.toString();
                ans.append(map.getOrDefault(key, "?"));
                i = j + 1;          // skip past ')'
            } else {
                ans.append(c);
                i++;
            }
        }
        return ans.toString();
    }
}
```

> **Why this is fast**  
> * O(n + m) time, where `n = |s|` and `m` is the total length of all keys in `knowledge`.  
> * O(m) extra space for the map.

---

### 🐍  Python

```python
from typing import List

class Solution:
    def evaluate(self, s: str, knowledge: List[List[str]]) -> str:
        # 1. Build the map
        lookup = {k: v for k, v in knowledge}

        # 2. Scan once
        res = []
        i = 0
        while i < len(s):
            if s[i] == '(':
                j = i + 1
                while s[j] != ')':
                    j += 1
                key = s[i+1:j]
                res.append(lookup.get(key, '?'))
                i = j + 1
            else:
                res.append(s[i])
                i += 1
        return ''.join(res)
```

> **Python‑specific tricks**  
> * `list` + `''.join()` is faster than string concatenation.  
> * Dictionary comprehension keeps the code tidy.

---

### ⚙️  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string evaluate(string s, vector<vector<string>>& knowledge) {
        // 1. Map building
        unordered_map<string, string> mp;
        mp.reserve(knowledge.size() * 2);          // avoid rehash
        for (auto& p : knowledge) mp[p[0]] = p[1];

        // 2. One-pass scan
        string ans;
        ans.reserve(s.size());                     // speed up
        for (size_t i = 0; i < s.size(); ++i) {
            if (s[i] == '(') {
                string key;
                ++i;                                // skip '('
                while (i < s.size() && s[i] != ')') {
                    key.push_back(s[i++]);
                }
                ans += mp.count(key) ? mp[key] : "?";
            } else {
                ans.push_back(s[i]);
            }
        }
        return ans;
    }
};
```

> **C++ highlights**  
> * `reserve` reduces reallocations.  
> * Using `unordered_map` gives average O(1) look‑up.

---

## 2️⃣  Blog Post – *The Good, The Bad, and The Ugly of LeetCode 1807*

### 📄 Problem Recap

> **LeetCode 1807 – Evaluate the Bracket Pairs of a String**  
> You’re given a string `s` that contains bracketed keys, e.g. `"(name)is(age)yearsold"`.  
> A 2‑D array `knowledge` contains key/value pairs.  
> **Goal** – replace each `"(key)"` with its value or `?` if the key isn’t known.  
> There are no nested brackets.

### 🧠 Why It’s a Medium‑Difficulty Interview Question

* **String parsing** – not as trivial as a regex because of time limits.  
* **Hash‑map usage** – demonstrates understanding of O(1) look‑ups.  
* **Edge‑case handling** – missing keys, large inputs (`1e5` length).  
* **Efficiency mindset** – you can’t afford an `O(n²)` solution.

### 🎯  The “Good” – The Simple, Clean Approach

1. **Build a hash‑map** – `O(m)` where `m` is the number of knowledge pairs.  
2. **Single scan** – walk through `s` once, building the answer on the fly.  
3. **O(n + m) time** and **O(m) space** – optimal for the constraints.

This is exactly what the official editorial and the majority of top solutions do. It’s elegant, readable, and passes all tests in milliseconds.

### ⚡  The “Bad” – What to Avoid

| Mistake | Why It’s Bad | Typical Consequence |
|---------|--------------|---------------------|
| **Regex** (`re.sub`) | Although it looks neat, the regex engine can be slow on `1e5` length strings and may hit Python’s recursion limit on nested groups. | Time‑limit exceeded (TLE). |
| **Stack for every '('** | You’ll end up pushing the entire key onto the stack, and you still need a hash‑map lookup. | O(n) extra space, no performance benefit. |
| **Repeated string concatenation** | In Java or Python, building a new string each time leads to O(n²) time. | TLE or high memory usage. |
| **Ignoring the “?” rule** | Some solutions forget to replace unknown keys, returning the raw `"(key)"`. | Wrong answer (WA). |

### 🌪️  The “Ugly” – Over‑Engineering and Pitfalls

1. **Dynamic programming** – There is no sub‑problem recurrence here; DP would just add overhead.  
2. **Custom trie for keys** – Unnecessary; a hash‑map is simpler and faster.  
3. **Heavy use of iterators or streams** – Makes the code harder to read and may hurt performance in tight loops.  
4. **Hard‑coding string length** – Over‑optimizing can backfire when input sizes change.  

**Bottom line:** Stick to the straightforward hash‑map + linear scan. That’s what interviewers expect and what passes all test cases.

### 📈 Complexity Breakdown

| Step | Time | Space |
|------|------|-------|
| Build map | `O(m)` | `O(m)` |
| Scan `s` | `O(n)` | `O(1)` (apart from the result string) |
| **Total** | `O(n + m)` | `O(m)` |

With `n, m ≤ 100 000`, this easily fits within 1 second time limits and a few megabytes of memory.

### 🔍  Real‑World Tips for Interviews

* **Read the constraints first** – they hint at the needed time complexity.  
* **Mention your approach upfront** – “I’ll use a hash‑map to store the key/value pairs, then scan the string once.”  
* **Talk about edge cases** – “If the key isn’t found, we output ‘?’.”  
* **Explain why you avoid regex** – “Although regex is powerful, it can be slow for large inputs and may exceed recursion limits.”  
* **Show your code in the language of choice** – provide clean, commented snippets.  

### 🚀  How This Helps Your Job Hunt

* **Showcase language versatility** – You can solve the same problem in Java, Python, and C++.  
* **Demonstrate algorithmic understanding** – The hash‑map + single pass is a classic interview pattern.  
* **SEO‑friendly title** – *LeetCode 1807 – Evaluate the Bracket Pairs of a String – Java, Python, C++ Solutions (HashMap Approach)*  
* **Share on LinkedIn/Twitter** – Use relevant tags (#LeetCode, #CodingInterview, #HashMap, #Java, #Python, #CPlusPlus) to attract recruiters.  
* **Create a blog post** – Include the problem statement, your solution, and the “good/bad/ugly” analysis. Recruiters love readable, explanatory content.

### 📚  Final Takeaway

The optimal solution is *simple* and *fast*: a hash‑map plus a linear scan.  
Avoid regex, nested stacks, and needless complexity.  
With clean code and a clear explanation, you’ll impress interviewers and get noticed by hiring managers searching for LeetCode expertise.

Happy coding! 🚀

---

> **Author** – [Your Name]  
> *Full code snippets are attached above. Feel free to copy, adapt, and post!*  
> **Contact** – email@domain.com | LinkedIn: /in/yourprofile | Twitter: @yourhandle

---

### 💬 Want to ask anything else?  
Drop a comment or DM me – I’m here to help you ace the next interview!