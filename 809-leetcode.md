---
title: LeetCode 809. Expressive Words - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  Expressive Words – 3‑Way Implementation  
**LeetCode 809 | Medium**

> A string *s* and an array `words`.  
> A word is *stretchy* if you can turn it into *s* by extending any group of the same character to a length **≥ 3**.  
> Count how many words are stretchy.

---

## 1️⃣ Algorithm Overview  

| Step | Description |
|------|-------------|
| 1. | Scan both `s` and a candidate word `w` simultaneously (two‑pointer technique). |
| 2. | For every group of equal characters, compute its length in `s` (`cntS`) and in `w` (`cntW`). |
| 3. | The group is valid if: |
| | * `cntS == cntW` (no change)  **OR**  |
| | * `cntS ≥ 3` and `cntS ≥ cntW` (extension) |
| 4. | If any group violates the rule → `w` is **not** stretchy. |
| 5. | Count all words that pass the test. |

*Time Complexity:* `O(n + Σ|words[i]|)` – linear in the total length of `s` and all words.  
*Space Complexity:* `O(1)` – only a few integer counters.

---

## 2️⃣ Java Implementation

```java
class Solution {
    public int expressiveWords(String s, String[] words) {
        int stretchy = 0;
        for (String w : words) if (isStretchy(s, w)) stretchy++;
        return stretchy;
    }

    private boolean isStretchy(String s, String w) {
        if (s.length() < w.length()) return false;

        int i = 0, j = 0;
        while (i < s.length() && j < w.length()) {
            if (s.charAt(i) != w.charAt(j)) return false;

            char c = s.charAt(i);
            int cntS = 0, cntW = 0;

            while (i < s.length() && s.charAt(i) == c) { cntS++; i++; }
            while (j < w.length() && w.charAt(j) == c) { cntW++; j++; }

            if (cntS < cntW) return false;          // cannot shrink
            if (cntS < 3 && cntS != cntW) return false; // only expand to 3+
        }
        return i == s.length() && j == w.length();
    }
}
```

---

## 3️⃣ Python Implementation

```python
class Solution:
    def expressiveWords(self, s: str, words: list[str]) -> int:
        def stretchy(s: str, w: str) -> bool:
            if len(s) < len(w):
                return False
            i = j = 0
            while i < len(s) and j < len(w):
                if s[i] != w[j]:
                    return False
                c = s[i]
                cnt_s = cnt_w = 0
                while i < len(s) and s[i] == c:
                    cnt_s += 1; i += 1
                while j < len(w) and w[j] == c:
                    cnt_w += 1; j += 1
                if cnt_s < cnt_w:
                    return False
                if cnt_s < 3 and cnt_s != cnt_w:
                    return False
            return i == len(s) and j == len(w)

        return sum(stretchy(s, w) for w in words)
```

---

## 4️⃣ C++ Implementation

```cpp
class Solution {
public:
    int expressiveWords(string s, vector<string>& words) {
        int ans = 0;
        for (const string& w : words)
            if (isStretchy(s, w)) ++ans;
        return ans;
    }

private:
    bool isStretchy(const string& s, const string& w) {
        if (s.size() < w.size()) return false;
        size_t i = 0, j = 0;
        while (i < s.size() && j < w.size()) {
            if (s[i] != w[j]) return false;
            char c = s[i];
            size_t cntS = 0, cntW = 0;
            while (i < s.size() && s[i] == c) { ++cntS; ++i; }
            while (j < w.size() && w[j] == c) { ++cntW; ++j; }
            if (cntS < cntW) return false;
            if (cntS < 3 && cntS != cntW) return false;
        }
        return i == s.size() && j == w.size();
    }
};
```

---

## 5️⃣ Blog Article – *The Good, the Bad, and the Ugly of Expressive Words*

### 🚀 Why “Expressive Words” Matters in Technical Interviews

When recruiters skim your resume, one line that often catches their eye is “Solved LeetCode 809: Expressive Words”. It’s a neat little problem that tests three crucial skills:

1. **String manipulation & pattern matching** – a staple of algorithmic interviews.  
2. **Two‑pointer / sliding‑window techniques** – the go‑to tools for linear scans.  
3. **Edge‑case thinking** – the difference between a 90 % solution and a 100 % one.

Mastering this problem not only boosts your LeetCode score but also builds confidence for **job‑search interviews** in software engineering, data science, or even product‑engineering roles that rely on string parsing.

---

### 🔍 The Good: What Makes It an Easy Win

- **Linear time** – `O(n)` per word, no nested loops.  
- **Minimal space** – only a handful of integer counters.  
- **Straight‑forward logic** – “group counts must satisfy simple inequalities.”  
- **Clear interview takeaway** – you can explain the approach in under 2 minutes.

If you nail the two‑pointer approach, you’ll comfortably answer follow‑up questions about “what if the groups were not contiguous?” or “how would you adapt this for a streaming input?”.

---

### ⚠️ The Bad: Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Missing the `cntS >= 3` rule** | Some candidates treat “any extension” as “increase to any length”. | Explicitly check `cntS < 3 && cntS != cntW` → reject. |
| **Index‑out‑of‑bounds** | Using `while (i < s.size() && s[i] == c)` after an earlier `while` may skip the final character. | Always reset the loop counters before each group, or use `++i` at the end. |
| **Failing to compare lengths** | Forgetting `if (s.size() < w.size()) return false;` allows impossible cases. | Add a quick length check at the start. |
| **Mis‑reading the “stretch” rule** | Thinking you can reduce a group. | Remember groups can only grow, not shrink. |

These mistakes cost many candidates the interview because they highlight a lack of attention to detail—exactly what hiring managers look for.

---

### 🐛 The Ugly: Real‑World Edge Cases

1. **Repeated Characters Across Different Groups**  
   `s = "aaabbb"` and `w = "ab"` → The algorithm must treat `a` and `b` as *different groups*, even though the same character repeats.  
   *Solution:* Count groups separately; the two‑pointer loop already handles this.

2. **Very Long Strings**  
   With `s.length() = 100` and each word also 100, the algorithm still runs in milliseconds, but you must avoid recursion or heavy memory allocation.  

3. **Non‑ASCII / Unicode**  
   If the problem statement extends to UTF‑8, `char` or `charAt` may split a character. Use `codePoint` in Java or `wchar_t` in C++.  

4. **Streaming Input**  
   In an interview you might be asked: “What if `s` is too large to fit in memory?”  
   *Answer:* Process it on the fly using a generator in Python (`yield`) or a stream in C++ (`istream`). Keep only the current group length.

---

### 📈 SEO‑Friendly Takeaway

> **Expressive Words** – *LeetCode 809*, **stretchy words**, **string manipulation**, **two‑pointer algorithm**, **job interview coding**, **Java Python C++ solutions**.

These keywords naturally weave into the blog content:

- Search engines index “Expressive Words” as a common LeetCode problem.
- Recruiters often search “Expressive Words solution” when evaluating candidates.
- The article covers all three programming languages, increasing the page’s relevance for job seekers seeking *language‑agnostic* solutions.

---

### 🎯 Final Thoughts

- **Practice**: Run the three implementations on all LeetCode test cases.  
- **Explain**: In an interview, walk through the two‑pointer logic, highlight edge cases, and discuss time/space trade‑offs.  
- **Show**: Mention that the same pattern (group counting + conditional logic) appears in problems like “3‑Sum Closest”, “Longest Repeating Substring”, and many LeetCode string challenges.

Once you can articulate the problem, the algorithm, and the pitfalls, you’re not just solving a coding problem—you’re proving you can think critically, handle edge cases, and communicate effectively—exactly what employers are looking for. 🚀

Happy coding, and good luck on your next interview!