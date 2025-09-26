---
title: LeetCode 1839. Longest Substring Of All Vowels in Order - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1839 – “Longest Beautiful Substring”  
### Good, Bad & Ugly: A Deep‑Dive into Vowel‑Order Substrings (Java / Python / C++)

> **TL;DR** – Scan the string once with a *greedy two‑pointer* strategy.  
> Time: **O(N)**, Space: **O(1)**.  
> Three ready‑to‑paste solutions (Java, Python, C++) are provided.

---

### 1️⃣ Problem Recap

> **Input** – A string `word` that contains only the five vowels: `a, e, i, o, u`.  
> **Goal** – Return the length of the longest contiguous substring that  
> 1. Contains **all** five vowels **at least once**.  
> 2. Appears in **strict alphabetical order** (`a…e…i…o…u`).  

If no such substring exists, return `0`.

> **Examples**  
> *`aeiaaioaaaaeiiiiouuuooaauuaeiu`* → **13** (`aaaaeiiiiouuu`)  
> *`aeeeiiiioooauuuaeiou`* → **5** (`aeiou`)  
> *`a`* → **0**

---

### 2️⃣ The “Bad” Brute‑Force Approach

The most intuitive solution is to generate *every* substring, check the two conditions, and keep the longest.  
```text
for i in [0..n-1]:
    for j in [i+1..n]:
        sub = word[i:j]
        if isBeautiful(sub): best = max(best, len(sub))
```
**Why it’s bad**

| Issue | Impact |
|-------|--------|
| O(N²) substrings | Too slow for `n = 5·10⁵` |
| O(N) per check | Still O(N³) overall |
| High memory overhead | Substring allocation |

Even with optimisations it won’t pass LeetCode’s time limits.

---

### 3️⃣ The “Good” Optimal Strategy

#### Core Insight  
While iterating, we can *reset* whenever the vowel order breaks.  
A substring is valid **only** if it moves forward in the vowel sequence.  
Hence a single linear scan suffices.

#### Sliding‑Window + Greedy

| Variable | Meaning |
|----------|---------|
| `left` | Start index of the current candidate substring |
| `stage` | How many distinct vowels we have seen in order (1…5) |
| `ans` | Longest beautiful substring length found so far |

**Algorithm**

```
ans = 0
left = 0
stage = 1                 // we have seen at least one 'a' at position 0

for i from 1 to n-1:
    if word[i] < word[i-1]:          // order broken → new window
        stage = 1
        left = i
    else if word[i] > word[i-1]:     // we stepped to the next vowel
        stage += 1

    if stage == 5:                   // we have seen a-e-i-o-u
        ans = max(ans, i - left + 1)

return ans
```

**Why it works**

- **Order check**: If the current vowel is *less* than the previous one, the substring can’t continue; we must start fresh at `i`.
- **Progression**: If it is *greater*, we move to the next vowel stage.
- **Repetition**: Equal characters (`'a'` → `'a'`) do not change `stage`; they simply lengthen the current window.
- **Full set**: When `stage` reaches 5 we know the window contains all five vowels in order, so we compare its length.

---

### 4️⃣ Code Implementations

> **Tip** – All solutions run in *O(N)* time, *O(1)* extra space, and handle the maximum input size effortlessly.

#### 4.1 Java (Readable, Constant‑Space)

```java
public class Solution {
    public int longestBeautifulSubstring(String word) {
        int ans = 0, left = 0, stage = 1; // stage 1 means we already have an 'a'

        for (int i = 1; i < word.length(); i++) {
            char prev = word.charAt(i - 1);
            char curr = word.charAt(i);

            if (curr < prev) {          // order broken
                stage = 1;
                left = i;
            } else if (curr > prev) {   // next vowel
                stage++;
            }
            if (stage == 5) {
                ans = Math.max(ans, i - left + 1);
            }
        }
        return ans;
    }
}
```

#### 4.2 Python (Elegant & Fast)

```python
class Solution:
    def longestBeautifulSubstring(self, word: str) -> int:
        ans = left = 0
        stage = 1  # 1 -> 'a', 2 -> 'e', ... 5 -> 'u'

        for i in range(1, len(word)):
            prev, cur = word[i-1], word[i]
            if cur < prev:          # order break
                stage, left = 1, i
            elif cur > prev:        # move to next vowel
                stage += 1

            if stage == 5:          # all vowels seen
                ans = max(ans, i - left + 1)

        return ans
```

#### 4.3 C++ (Fast & Memory‑Efficient)

```cpp
class Solution {
public:
    int longestBeautifulSubstring(string word) {
        int ans = 0, left = 0, stage = 1; // stage counts vowels in order

        for (int i = 1; i < word.size(); ++i) {
            char prev = word[i-1];
            char cur  = word[i];

            if (cur < prev) {          // reset
                stage = 1;
                left = i;
            } else if (cur > prev) {   // move to next vowel
                ++stage;
            }

            if (stage == 5) {
                ans = max(ans, i - left + 1);
            }
        }
        return ans;
    }
};
```

---

### 5️⃣ Edge Cases & Gotchas

| Case | Why it matters | Fix |
|------|----------------|-----|
| **Single vowel** (e.g., `"a"` or `"uuu"`) | No beautiful substring | Return `0` automatically (loop never hits `stage == 5`) |
| **Re‑started window in the middle** | Order reset must also reset `stage` | `stage = 1` on reset |
| **Duplicate vowels** | They don’t progress `stage` | Keep `stage` unchanged when `cur == prev` |
| **Large input** | Memory safety | Use indices, not substring objects |

---

### 6️⃣ Complexity Summary

| Language | Time | Space |
|----------|------|-------|
| Java | **O(N)** | **O(1)** |
| Python | **O(N)** | **O(1)** |
| C++ | **O(N)** | **O(1)** |

`N` = `word.length()` (≤ 5·10⁵)

---

### 7️⃣ Common Interview Pitfalls

1. **Assuming “contains all vowels” → “contains each at least once”**  
   Be explicit that every vowel must appear and the order matters.

2. **Using `set` or `HashMap` for each window**  
   That would be O(N²) in the worst case. The greedy scan is the intended solution.

3. **Over‑optimising with regex**  
   Regular expressions can be elegant but are hard to justify in an interview.

4. **Neglecting the reset case**  
   Forgetting to reset `stage` when the order breaks leads to wrong answers.

---

### 8️⃣ Take‑aways for Your Next Coding Interview

- **Start with a brute‑force plan** – it clarifies the constraints.
- **Look for linear‑time scans** – most string problems with order constraints boil down to a single pass.
- **State machines matter** – `stage` is essentially a small finite‑state machine tracking progress through the vowel sequence.
- **Code readability is key** – even a one‑liner in Python should have clear comments for interviewers.
- **Time‑space trade‑offs** – here we purposely kept memory minimal because the input size is huge.

---

### 9️⃣ Further Resources

| Link | What It Offers |
|------|----------------|
| [LeetCode 1839](https://leetcode.com/problems/longest-substring-of-all-vowels-in-order/) | Problem page with test cases |
| [Java Solution – Constant Space](https://leetcode.com/problems/longest-substring-of-all-vowels-in-order/solutions/1175715/java-readable-code-constant-space-by-him-eip2/) | Original solution by Himanshu Chhikara |
| [Python Version – Greedy Sliding Window](https://leetcode.com/problems/longest-substring-of-all-vowels-in-order/solutions/6798204/sliding-window-easy-by-vivek011299-jv7q/) | Clean Python code |
| [C++ Version – O(N) Two‑Pointer](https://leetcode.com/problems/longest-substring-of-all-vowels-in-order/solutions/6622943/longest-beautiful-substring-c-solution-t/) | Fast and concise |

---

## 📌 Final Verdict

The “beautiful substring” problem is a perfect example of how a *simple greedy scan* turns an O(N²) nightmare into an O(N) breeze.  

Copy one of the three code snippets, submit it, and you’ll get **AC** on LeetCode 1839 in milliseconds.  

Good luck – and may your interview be as orderly as a “beautiful” vowel sequence! 🚀

--- 

> **Author** – *Stack‑Overflow & LeetCode enthusiast, constantly turning puzzling problems into clean, efficient code.*  
> **Contact** – GitHub: `@codingwizard` | LinkedIn: `linkedin.com/in/codingwizard`  

--- 

> *If you liked this post, star the repo, share with friends, and drop a comment if you found it helpful!*
--- 

```diff
# End of blog
```
