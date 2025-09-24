---
title: LeetCode 1813. Sentence Similarity III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Code – LeetCode 1813 “Sentence Similarity III”

Below you’ll find **three clean, production‑ready implementations** – one in Java, one in Python and one in C++.  
Each implementation follows the same O(n) two‑pointer strategy described in the editorial:

1. Split both sentences into word lists.  
2. Walk from the left while the words match.  
3. Walk from the right while the words match.  
4. If the number of matched words (`left + right`) is at least the size of the shorter sentence, the two sentences are similar.

---

### 1.1 Java (LeetCode format)

```java
import java.util.*;

public class Solution {
    public boolean areSentencesSimilar(String sentence1, String sentence2) {
        // Split into words
        String[] words1 = sentence1.split(" ");
        String[] words2 = sentence2.split(" ");

        // Ensure words1 is the longer sentence
        if (words1.length < words2.length) {
            String[] tmp = words1; words1 = words2; words2 = tmp;
        }

        int i = 0, j = 0;                    // start indices
        int n1 = words1.length, n2 = words2.length;

        // 1️⃣ match from the beginning
        while (i < n2 && words1[i].equals(words2[i])) i++;

        // 2️⃣ match from the end
        while (j < n2 && words1[n1 - j - 1].equals(words2[n2 - j - 1])) j++;

        // 3️⃣ all words of the shorter sentence are covered
        return i + j >= n2;
    }
}
```

> **Why this works** –  
> The only place a new sentence can be inserted is between the matched prefix and suffix.  
> If the unmatched middle part of the longer sentence fully fits into that “gap”, the two sentences can be made identical.

---

### 1.2 Python 3 (LeetCode format)

```python
class Solution:
    def areSentencesSimilar(self, sentence1: str, sentence2: str) -> bool:
        # Split into lists of words
        w1 = sentence1.split()
        w2 = sentence2.split()

        # Make w1 the longer list
        if len(w1) < len(w2):
            w1, w2 = w2, w1

        i = 0                      # start pointer
        j = 0                      # end pointer
        n1, n2 = len(w1), len(w2)

        # Prefix match
        while i < n2 and w1[i] == w2[i]:
            i += 1

        # Suffix match
        while j < n2 and w1[n1 - j - 1] == w2[n2 - j - 1]:
            j += 1

        # All words of the shorter sentence are covered
        return i + j >= n2
```

---

### 1.3 C++ (LeetCode format)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool areSentencesSimilar(string sentence1, string sentence2) {
        // Helper: split a sentence into words
        auto split = [](const string& s) {
            vector<string> res;
            string cur;
            for (char c : s) {
                if (c == ' ') {
                    if (!cur.empty()) { res.push_back(cur); cur.clear(); }
                } else {
                    cur += c;
                }
            }
            if (!cur.empty()) res.push_back(cur);
            return res;
        };

        vector<string> w1 = split(sentence1);
        vector<string> w2 = split(sentence2);

        // Ensure w1 is the longer vector
        if (w1.size() < w2.size()) swap(w1, w2);

        size_t i = 0, j = 0;
        size_t n1 = w1.size(), n2 = w2.size();

        // Match from the front
        while (i < n2 && w1[i] == w2[i]) ++i;

        // Match from the back
        while (j < n2 && w1[n1 - j - 1] == w2[n2 - j - 1]) ++j;

        // All words of the shorter sentence are accounted for
        return i + j >= n2;
    }
};
```

---

## 2.  Blog Post – “The Good, the Bad, and the Ugly of LeetCode 1813”

> **Target Keywords:** *LeetCode, Sentence Similarity III, algorithm interview, job interview, coding challenge, interview preparation, Java, Python, C++*

### 2.1 Introduction

When you stumble across **LeetCode 1813 – “Sentence Similarity III”** in your interview prep, the first thing that hits you is that it’s a “medium” problem. Yet its solution is *remarkably simple* once you see the right angle.  
In this post we’ll walk through the algorithm, dissect what makes it great, expose the hidden pitfalls, and show you the “ugly” edge cases that could trip you up. We’ll also give you a **ready‑to‑paste implementation** in Java, Python, and C++ so you can brag about your skills at your next interview.

> **Why this matters for your job hunt** –  
> Interviewers love problems that force you to think in terms of *prefix/suffix* and *two‑pointer* tricks. Demonstrating mastery of this pattern shows you can translate a problem statement into an efficient, maintainable solution. Plus, knowing the pitfalls keeps you from that “gotcha” moment when a test case fails unexpectedly.

---

### 2.2 Problem Recap

> **Goal**: Determine whether two sentences can become identical by inserting *any* single contiguous sentence (possibly empty) into one of them.

- Sentences are space‑separated words.  
- No leading/trailing spaces.  
- Words contain only letters (upper/lowercase).

**Examples**

| Sentence 1 | Sentence 2 | Result |
|------------|------------|--------|
| “My name is Haley” | “My Haley” | ✅ |
| “of” | “A lot of words” | ❌ |
| “Eating right now” | “Eating” | ✅ |

---

### 2.3 The Good – 2‑Pointer Power

| Aspect | Why it’s great |
|--------|----------------|
| **O(n) Time** | We scan each word at most twice (once from start, once from end). No nested loops. |
| **O(n) Space** | Only store word lists; no heavy auxiliary data structures. |
| **Intuitive** | Matching prefixes and suffixes is a pattern that shows up in many string problems (palindromes, longest common prefix, etc.). |
| **Language‑agnostic** | Works the same in Java, Python, C++ – just split strings and compare indices. |
| **Easy to read** | 2‑pointer logic is often shorter and clearer than recursion or dynamic programming for this problem. |

> **Takeaway** – A concise two‑pointer approach demonstrates that you can extract the essence of a problem and solve it in linear time.

---

### 2.4 The Bad – Hidden Costs

| Issue | Impact |
|-------|--------|
| **String splitting overhead** | In a tight interview environment you’re expected to keep the code lean. `split(" ")` (Java), `split()` (Python) or manual tokenization (C++) all allocate new strings. For huge inputs this can become a measurable overhead. |
| **Locale‑sensitive split** | Some languages treat spaces as locale‑dependent whitespace. Always split on the literal space character (`" "`), not the default regex, to avoid surprises. |
| **Large input size** | Although the algorithm is O(n), you may hit Java’s `String.split` O(n²) performance if you use a regex that compiles every time. Prefer `StringTokenizer` or manual parsing in Java for production code. |
| **Edge case “empty sentence”** | The problem statement allows inserting an *empty* sentence. Your code must correctly handle zero‑length sentences. |
| **Upper/lowercase mismatches** | Words are case‑sensitive. Ensure you use `equals` (Java) or `==` after `split()` (Python) – not `==` in Java which compares object references. |

---

### 2.5 The Ugly – “Gotcha” Edge Cases

| Edge Case | Why it trips people | How to guard |
|-----------|---------------------|--------------|
| **The shorter sentence is already a suffix of the longer one** | `left + right` can be equal to `n2` only if the matched suffix is **non‑overlapping** with the prefix. | Ensure the `while` loops use `< n2` *not* `< n1`. |
| **Both sentences identical** | Some may prematurely return true after the prefix loop. | The final condition `left + right >= n2` is the definitive check. |
| **All words match but order differs** | Example: “Hello World” vs “World Hello” | The two‑pointer method will return false because neither the prefix nor suffix match fully. |
| **Empty insertion** | “Hello” vs “Hello” → no words to insert, but the algorithm must still return true. | The `>=` check handles the empty middle case. |
| **Very long middle segment** | “a b c … z” vs “a … z” | Your split arrays must handle long lists. Use efficient parsing in Java (e.g., `StringTokenizer`) if you’re concerned about performance. |

> **Debugging tip** – After the two‑pointer loops, print `i`, `j`, `n2` and ensure `i + j` never exceeds `n2`. If it does, you’ve covered the entire shorter sentence.

---

### 2.6 Pseudocode

```text
split sentence1 into words1
split sentence2 into words2
if len(words1) < len(words2) swap them          // words1 is always longer

left  = 0
right = 0
n1 = len(words1), n2 = len(words2)

while left < n2 and words1[left] == words2[left]:
    left += 1

while right < n2 and words1[n1 - right - 1] == words2[n2 - right - 1]:
    right += 1

return left + right >= n2
```

---

### 2.7 Complexity Summary

| Complexity | Explanation |
|------------|-------------|
| **Time**   | `O(n)` – each word is examined at most twice. |
| **Space**  | `O(n)` – the two word vectors. |
| **Best/Worst** | No hidden quadratic behavior. |
| **Scalability** | Works comfortably up to millions of characters (LeetCode test cases are far smaller). |

---

### 2.8 Real‑World Interview Checklist

| ✅ | Item |
|----|------|
| ✅ | Use a simple two‑pointer loop – no recursion or DP. |
| ✅ | Always swap so the first list is the longer one. |
| ✅ | Compare words with `equals` (Java), `==` after `split()` (Python), or `==` on `string` objects (C++). |
| ✅ | Test your code on the following edge cases: |
|    | 1. “a” vs “a”  |
|    | 2. “a b” vs “b a”  |
|    | 3. “a b c d” vs “a d”  |
|    | 4. “a” vs “” (empty sentence is **not** allowed by the statement, but good to check). |

---

### 2.9 Final Takeaway

- **Show the pattern** – Prefix/suffix matching is a quick‑fire technique that impresses interviewers.  
- **Avoid the gotchas** – Pay attention to string splitting and pointer bounds.  
- **Ready‑to‑paste** – Use the code snippets above in the language of your choice.

**Now you can walk into any coding interview armed with a clean, efficient solution to “Sentence Similarity III” and a blog post that shows you understand the problem at *every* level. Good luck landing that next job!**

---