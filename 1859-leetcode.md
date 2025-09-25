---
title: LeetCode 1859. Sorting the Sentence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 LeetCode 1859 – “Sorting the Sentence”  
**Easy | 1–9 words | O(n) time, O(n) space**

> **Problem**  
> A shuffled sentence is formed by appending the 1‑indexed word position to each word and then permuting the words.  
> Given a shuffled sentence `s` (no leading/trailing spaces, words separated by a single space), rebuild the original sentence.

> **Example**  
> ```
> Input : "is2 sentence4 This1 a3"
> Output: "This is a sentence"
> ```

> **Why is this a great interview question?**  
> * Simple parsing logic  
> * Demonstrates string manipulation and indexing  
> * No need for advanced data structures

---

## 📄 Solution Overview

1. **Split** the string into words (O(n) characters).  
2. For each word, **extract** the last character → the position (1‑based).  
3. **Place** the word (without the digit) into an array at `index‑1`.  
4. **Join** the array back into a sentence.

> The constraint `1 ≤ wordCount ≤ 9` guarantees the last character is always a single digit.  
> If the problem allowed more words, we’d need to parse all trailing digits.

---

## 🧪 Code Implementations

> All implementations have identical logic; only syntax differs.

### 1. Java

```java
// Java 17
import java.util.*;

public class Solution {
    public String sortSentence(String s) {
        String[] words = s.split(" ");
        String[] res = new String[words.length];

        for (String w : words) {
            int pos = w.charAt(w.length() - 1) - '1';  // 0‑based
            res[pos] = w.substring(0, w.length() - 1);
        }

        return String.join(" ", res);
    }
}
```

> **Complexity**  
> *Time* – O(n) (each word visited once)  
> *Space* – O(n) (result array)

---

### 2. Python

```python
# Python 3
class Solution:
    def sortSentence(self, s: str) -> str:
        words = s.split()
        res = [None] * len(words)

        for w in words:
            pos = int(w[-1]) - 1            # 0‑based index
            res[pos] = w[:-1]               # strip the digit

        return ' '.join(res)
```

> **Complexity** – same as Java

---

### 3. C++

```cpp
// C++17
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string sortSentence(string s) {
        vector<string> words;
        stringstream ss(s);
        string w;
        while (ss >> w) words.push_back(w);

        vector<string> res(words.size());
        for (const string &w : words) {
            int pos = w.back() - '1';        // 0‑based
            res[pos] = w.substr(0, w.size() - 1);
        }
        string ans;
        for (size_t i = 0; i < res.size(); ++i) {
            if (i) ans += ' ';
            ans += res[i];
        }
        return ans;
    }
};
```

> **Complexity** – O(n) time, O(n) space

---

## 🚀 How to Use These Solutions in Your Interview

1. **Explain the algorithm** before diving into code.  
2. Mention the **time/space complexity** and why they’re optimal.  
3. Highlight that the trick is to treat the last digit as an index.  
4. Be ready to discuss edge cases (e.g., single‑word sentences, mixed case, etc.).

---

## 🧩 The Good, The Bad, and The Ugly

| Category | What’s Great | Potential Pitfalls | Ugly (Edge Cases) |
|----------|--------------|--------------------|-------------------|
| **Good** | • O(n) time & space, no heavy data structures.<br>• Works for any alphabet (lower/upper). | – | – |
| **Bad** | • None. The problem is deliberately easy to surface basic string skills. | **Mis‑parsing the digit** – forgetting to convert `char` to `int`. | – |
| **Ugly** | – | • If the problem were extended to **10+ words** (multi‑digit positions), the current logic would fail. <br>• Need a more robust parsing routine that extracts all trailing digits, not just the last char. | In the real world, sentences could contain punctuation or numbers in words, requiring a cleaner regex solution. |

---

## 📚 Bonus: Extending to Variable‑Length Indices

If the sentence could have **10+ words**, the index may consist of multiple digits. Here’s a quick adaptation for Python:

```python
import re

def sort_sentence_variable(s):
    words = s.split()
    res = [None] * len(words)
    for w in words:
        m = re.search(r'(\d+)$', w)
        pos = int(m.group(1)) - 1
        res[pos] = w[:m.start()]
    return ' '.join(res)
```

This uses a regex to capture all trailing digits.

---

## 🎯 SEO‑Optimized Blog Title & Meta Description

**Title:**  
> *Sorting the Sentence (LeetCode 1859) – Java, Python, & C++ Solutions + Interview Tips*

**Meta Description:**  
> Master LeetCode’s “Sorting the Sentence” problem with clear Java, Python, and C++ code. Learn the optimal algorithm, complexity analysis, and interview‑ready explanation. Perfect for coding interview prep.

---

## 📈 Wrap‑Up

Sorting the Sentence is a classic “string manipulation + indexing” interview puzzle. The optimal solution is trivial once you realize the last digit is an explicit 1‑based position.  
Implementations in Java, Python, and C++ are concise, efficient, and ready to drop into your LeetCode session or coding interview.

Happy coding—and may your sentences always be in order! 🚀