---
title: LeetCode 809. Expressive Words - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 809. Expressive Words – Full‑Stack Solution (Java / Python / C++)

Below you’ll find a clean, fully‑commented implementation for **Java**, **Python**, and **C++**.  
All three solve the same LeetCode problem:

> Given a string `s` and an array of query words, return how many of those words can be stretched into `s` by repeatedly extending any group of identical letters to **length ≥ 3**.

The solution uses a classic two‑pointer technique that runs in **O(|s| + ∑|word|)** time and **O(1)** extra space.

---

### Java

```java
// 809. Expressive Words
// Time   : O(|s| + Σ|word|)
// Space  : O(1)
public class Solution {
    public int expressiveWords(String s, String[] words) {
        int result = 0;
        for (String w : words) {
            if (isStretchable(s, w)) result++;
        }
        return result;
    }

    private boolean isStretchable(String s, String w) {
        // If w is longer than s it can never match
        if (w.length() > s.length()) return false;

        int i = 0, j = 0;          // i → s, j → w
        while (i < s.length() && j < w.length()) {
            if (s.charAt(i) != w.charAt(j)) return false;

            char cur = s.charAt(i);

            // Count run length in s
            int sCount = 0;
            while (i < s.length() && s.charAt(i) == cur) {
                sCount++; i++;
            }

            // Count run length in w
            int wCount = 0;
            while (j < w.length() && w.charAt(j) == cur) {
                wCount++; j++;
            }

            // w’s run must not exceed s’s run
            if (wCount > sCount) return false;

            // If the run is small in s, it must match exactly
            if (sCount < 3 && sCount != wCount) return false;
        }

        // Both strings must be fully consumed
        return i == s.length() && j == w.length();
    }
}
```

---

### Python

```python
# 809. Expressive Words
# Time:   O(len(s) + sum(len(w) for w in words))
# Space:  O(1)

class Solution:
    def expressiveWords(self, s: str, words: list[str]) -> int:
        return sum(self._stretchable(s, w) for w in words)

    def _stretchable(self, s: str, w: str) -> bool:
        if len(w) > len(s):
            return False

        i = j = 0
        while i < len(s) and j < len(w):
            if s[i] != w[j]:
                return False

            ch = s[i]
            s_cnt = 0
            while i < len(s) and s[i] == ch:
                s_cnt += 1
                i += 1

            w_cnt = 0
            while j < len(w) and w[j] == ch:
                w_cnt += 1
                j += 1

            if w_cnt > s_cnt:          # w has more letters in this group
                return False
            if s_cnt < 3 and s_cnt != w_cnt:   # can't stretch a short group
                return False

        return i == len(s) and j == len(w)
```

---

### C++

```cpp
// 809. Expressive Words
// Time   : O(|s| + Σ|word|)
// Space  : O(1)

class Solution {
public:
    int expressiveWords(string s, vector<string> &words) {
        int ans = 0;
        for (auto &w : words)
            if (isStretchable(s, w)) ++ans;
        return ans;
    }

private:
    bool isStretchable(const string &s, const string &w) {
        if (w.size() > s.size()) return false;

        size_t i = 0, j = 0;          // i -> s, j -> w
        while (i < s.size() && j < w.size()) {
            if (s[i] != w[j]) return false;

            char cur = s[i];
            size_t sCnt = 0;
            while (i < s.size() && s[i] == cur) {
                ++sCnt; ++i;
            }

            size_t wCnt = 0;
            while (j < w.size() && w[j] == cur) {
                ++wCnt; ++j;
            }

            if (wCnt > sCnt) return false;          // w longer than s
            if (sCnt < 3 && sCnt != wCnt) return false; // cannot stretch
        }
        return i == s.size() && j == w.size();
    }
};
```

---

## 📖 Blog Article – “Expressive Words: The Good, the Bad, and the Ugly”

> **SEO Keywords**: *Expressive Words Leetcode, Leetcode 809 solution, Java Python C++ algorithm interview, how to solve expressive words, job interview algorithm, stretchable words problem, algorithm interview questions*

---

### 1️⃣ Introduction

If you’re prepping for a software engineering interview, you’ll quickly encounter **LeetCode 809 – Expressive Words**. It’s deceptively simple but often trips candidates because it blends string manipulation with a “stretch” rule that’s easy to misinterpret.

In this article, we’ll break down the **good**, **bad**, and **ugly** aspects of the problem, walk through a robust solution in **Java**, **Python**, and **C++**, and highlight interview‑ready takeaways.

> *Why the title?*  
> “Good, the Bad, and the Ugly” is a classic reference from *The Godfather*. It signals we’re diving deep into what *works*, what *fails*, and what *is easy to get wrong*.

---

### 2️⃣ Problem Recap (Good)

You’re given:
- A string `s` (the “stretchy” string).
- An array `words[]` of candidate query words.

A **stretch** operation is:
1. Pick a group of identical characters in a word.
2. Add extra copies of that character so that the group’s length is **≥ 3**.

The goal: Count how many words in `words[]` can be stretched into `s`.

Why is this *good*?
- **Clear constraints**: `1 ≤ s.length, words.length ≤ 100`. So a linear scan is fine.
- **Deterministic**: No randomness or backtracking; the greedy approach works.

---

### 3️⃣ Where the Trouble Lies (Bad)

1. **Misreading the stretch rule**  
   You *cannot* stretch a group in `s` that is already smaller than 3. Only groups **≥ 3** can be the target of stretching.  
   *Common pitfall*: Thinking you can shrink `s`’s group to match `w`, which is false.

2. **Off‑by‑one bugs in run‑length counting**  
   While counting contiguous identical letters, forgetting to increment both pointers leads to infinite loops or mis‑aligned checks.

3. **Edge‑case mismatch**  
   - `s` is shorter than `w`.  
   - `s` contains extra groups that `w` doesn’t have.  
   - `w` has a group longer than `s`’s equivalent group.

The “bad” part is that a single off‑by‑one or misunderstood rule can kill your solution.

---

### 4️⃣ The Classic Two‑Pointer Fix (Ugly → Beautiful)

The “ugly” (complicated) solution often involves nested loops, building frequency maps, or performing multiple passes. The cleanest way is the **two‑pointer, run‑length** approach:

1. Traverse `s` and `w` simultaneously.
2. When the current characters match, count how many times each appears consecutively (run lengths).
3. Apply the stretch rules:
   - `w`’s run length cannot exceed `s`’s run length.
   - If `s`’s run length is **< 3**, it must equal `w`’s run length exactly.
4. If a mismatch occurs, abort.

The beauty lies in **O(1)** extra memory and a single pass over each word.

---

### 5️⃣ Code Walk‑through (Java)

```java
public class Solution {
    public int expressiveWords(String s, String[] words) {
        int cnt = 0;
        for (String w : words) if (isStretchable(s, w)) cnt++;
        return cnt;
    }

    private boolean isStretchable(String s, String w) {
        if (w.length() > s.length()) return false;
        int i = 0, j = 0;
        while (i < s.length() && j < w.length()) {
            if (s.charAt(i) != w.charAt(j)) return false;
            char cur = s.charAt(i);
            int sCnt = 0, wCnt = 0;
            while (i < s.length() && s.charAt(i) == cur) { sCnt++; i++; }
            while (j < w.length() && w.charAt(j) == cur) { wCnt++; j++; }
            if (wCnt > sCnt) return false;
            if (sCnt < 3 && sCnt != wCnt) return false;
        }
        return i == s.length() && j == w.length();
    }
}
```

> **Why this is “good”**  
> *Clear variables*, *single exit points*, *no magic numbers*.

---

### 6️⃣ Python & C++ – Quick Comparisons

- **Python**: Uses `while` loops and `len()` for bounds.
- **C++**: Utilizes `size_t` for indices, `string` and `vector<string>`.

All share the same logical flow, making it easy to translate between languages.

---

### 7️⃣ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| `expressiveWords` | **O(|s| + Σ|w|)** | **O(1)** |
| `isStretchable` | **O(|s| + |w|)** | **O(1)** |

With the given limits (≤ 100 characters), performance is trivial, but the algorithm scales linearly if the strings grow.

---

### 8️⃣ What to Show on Your Resume

- **Problem**: LeetCode 809 – Expressive Words
- **Skills**: String manipulation, run‑length encoding, greedy algorithms, two‑pointer technique
- **Languages**: Java, Python, C++
- **Result**: Clean, O(n) solution with full test coverage

Mention that you **avoided** the common pitfalls of off‑by‑one errors and misinterpreting stretch conditions, demonstrating attention to detail – a key interview trait.

---

### 9️⃣ Testing Checklist

| Test | Description |
|------|-------------|
| `s = "heeellooo", words = ["hello","hi","helo"]` | Expected output: 1 |
| `s = "zzzzzyyyyy", words = ["zzyy","zy","zyy"]` | Expected output: 3 |
| `s = "abcd", words = ["abc","abcd","ab"]` | All return 0 |
| `s = "aabbcc", words = ["aabbcc","aabbccc"]` | Stretch not allowed on groups < 3 |
| `s = "aaa", words = ["aa"]` | Should return 0 (group too short) |

Add edge cases for single‑letter groups, empty arrays, and maximum length strings.

---

### 10️⃣ Final Takeaway (The Ugly Becomes Beautiful)

> **“If you’re stuck, remember that the problem is essentially a run‑length comparison. The two‑pointer method eliminates complexity and protects against subtle bugs.”**

During interviews, the interviewer will ask you to explain the rule “≥ 3”. When you can articulate it clearly and present the two‑pointer solution, you’ll not only solve the problem but also showcase **algorithmic elegance**.

---

### 🎯 Conclusion

LeetCode 809 is more than a string puzzle—it’s a **test of precision**. By mastering the two‑pointer approach in **Java, Python, and C++**, you’re ready to impress hiring managers and demonstrate the **interview‑ready** mindset they’re after.

Happy coding, and good luck landing that next job interview! 🚀

---

*End of article.*

--- 

> *Feel free to tweak the “Testing Checklist” and add more edge‑cases. The key is demonstrating thoroughness, clarity, and a polished algorithm.*