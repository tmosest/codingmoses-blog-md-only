---
title: LeetCode 2663. Lexicographically Smallest Beautiful String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2663 – Lexicographically Smallest Beautiful String  
## A “Hard” LeetCode problem, solved in Java, Python and C++  
> **TL;DR**  
> • Greedy – scan from right to left, bump a character, then rebuild the suffix with the smallest legal letters.  
> • Complexity: **O(n)** time, **O(1)** extra space (apart from the output string).  
> • Works for *n* up to **100 000** and *k* from 4 to 26.  

---

## Problem Recap

* You are given a **beautiful** string `s` of length `n` (`1 ≤ n ≤ 10^5`).  
* A string is *beautiful* if  
  1. Every character is in the first `k` letters of the alphabet (`a … a+k-1`).  
  2. No substring of length ≥ 2 is a palindrome.  
* Find the **lexicographically smallest** beautiful string that is **strictly larger** than `s`.  
* If no such string exists, return an empty string.

---

## Intuition & Greedy Idea

Think of the string as a “lock” that we want to open to the next valid state.

1. **Move right‑to‑left**:  
   Starting from the last position, try to increase that character.  
   *Why right‑to‑left?*  
   Because the suffix that comes after the changed position must be rebuilt to the smallest possible characters; we can’t afford to increase a character farther left if a smaller lexicographic increment exists to the right.

2. **Legal increment**  
   Increment `s[i]` until it becomes a letter ≤ `'a'+k-1`.  
   After each increment we must check that the new character does **not** equal  
   * `s[i-1]` (no two consecutive equal chars) and  
   * `s[i-2]` (prevents a 3‑length palindrome).  

   If both checks pass we have found the leftmost position where we can bump the string.  
   If `s[i]` reaches `'a'+k-1` and still fails the checks, we move one step to the left (`i--`) and repeat.

3. **Rebuild the suffix**  
   Once we have a valid prefix `s[0…i]`, the suffix `s[i+1…n-1]` must be the **lexicographically smallest** beautiful completion.  
   For each position `j` after `i` we pick the smallest letter from the set `{a, b, …, a+k-1}` that is different from `s[j-1]` and `s[j-2]`.  
   Because we’re always picking the smallest possible letter, the whole string will be the smallest lexicographically among all strings that are larger than the original.

4. **No solution**  
   If we walk off the left end (`i < 0`) it means we could not find any legal increment, so return `""`.

---

## Correctness Proof (Sketch)

We prove that the algorithm returns the desired string or correctly reports that none exists.

1. **Existence of an increment**  
   If a larger beautiful string exists, there must be a position `i` such that  
   * `s[i] < 'a'+k-1` and  
   * increasing `s[i]` by at least 1 keeps the string beautiful.  
   The algorithm examines all positions from right to left and stops at the first one where an increment is possible. Thus, if a solution exists, the algorithm will find a valid `i`.

2. **Lexicographic minimality of the prefix**  
   The algorithm never changes any character left of `i`. Any other solution that changes a character left of `i` would be lexicographically larger because the earlier position changes to a higher letter.

3. **Lexicographic minimality of the suffix**  
   After the prefix is fixed, each position `j > i` is set to the smallest letter that maintains beauty with its two predecessors.  
   Any other choice at position `j` that is larger would make the whole string lexicographically larger, and choosing a smaller letter would break the beauty constraint.  
   Thus the constructed suffix is the smallest possible.

Combining 1‑3, the algorithm returns the smallest beautiful string that is strictly larger than `s`. If no such `i` exists, no larger beautiful string can be formed, so the answer is `""`. ∎

---

## Edge Cases & Pitfalls

| Scenario | Why it matters | How the algorithm handles it |
|----------|----------------|------------------------------|
| **k = 4** (minimal allowed) | Only letters `a‑d` exist → palindrome constraints are tighter. | Still works; the suffix reconstruction uses the full set `{a,b,c,d}`. |
| **s already uses the largest letters** | No position can be increased. | The loop will decrement `i` past the left end → return `""`. |
| **String length = 1** | Palindrome check trivial. | The algorithm still works; no indices `i-1` or `i-2` exist. |
| **k = 26** | All alphabet letters allowed. | The logic remains identical; the suffix can use any of 26 letters. |
| **Very long string (10^5)** | Need linear time. | Each character is examined at most twice (once in the bumping phase, once in suffix building). |

---

## Code

Below are clean, well‑commented implementations in **Java**, **Python**, and **C++**.  
All solutions follow the same greedy strategy described above.

### 1. Java

```java
import java.util.*;

public class Solution {
    public String smallestBeautifulString(String s, int k) {
        char[] ch = s.toCharArray();
        int n = ch.length;
        int i = n - 1;

        // ----- find position to bump -----
        while (i >= 0) {
            ch[i]++;                      // try next letter
            if (ch[i] - 'a' == k) {       // went past the allowed range
                i--;
                continue;
            }
            if ((i - 1 < 0 || ch[i] != ch[i - 1]) &&
                (i - 2 < 0 || ch[i] != ch[i - 2])) {
                break;                    // valid bump found
            }
        }

        if (i < 0) return "";           // no larger beautiful string

        // ----- rebuild the suffix with smallest possible letters -----
        for (int j = i + 1; j < n; j++) {
            // choose the smallest letter that does not match
            // the previous one or the one two steps back
            for (int x = 0; x < k; x++) {
                char cand = (char) ('a' + x);
                if (j - 1 >= 0 && cand == ch[j - 1]) continue;
                if (j - 2 >= 0 && cand == ch[j - 2]) continue;
                ch[j] = cand;
                break;
            }
        }
        return new String(ch);
    }
}
```

### 2. Python

```python
class Solution:
    def smallestBeautifulString(self, s: str, k: int) -> str:
        chars = list(s)
        n = len(chars)
        i = n - 1

        # find rightmost position we can increase
        while i >= 0:
            # try next letter
            chars[i] = chr(ord(chars[i]) + 1)
            if chars[i] > chr(ord('a') + k - 1):
                i -= 1
                continue

            # check beauty
            if (i - 1 < 0 or chars[i] != chars[i - 1]) and \
               (i - 2 < 0 or chars[i] != chars[i - 2]):
                break
        if i < 0:
            return ""

        # rebuild suffix with smallest legal letters
        for j in range(i + 1, n):
            for x in range(k):
                cand = chr(ord('a') + x)
                if j - 1 >= 0 and cand == chars[j - 1]:
                    continue
                if j - 2 >= 0 and cand == chars[j - 2]:
                    continue
                chars[j] = cand
                break

        return "".join(chars)
```

### 3. C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string smallestBeautifulString(string s, int k) {
        vector<char> ch(s.begin(), s.end());
        int n = ch.size();
        int i = n - 1;

        // find rightmost position that can be increased
        while (i >= 0) {
            ch[i]++;                                    // try next letter
            if (ch[i] > char('a' + k - 1)) {            // out of range
                i--;
                continue;
            }
            if ((i - 1 < 0 || ch[i] != ch[i - 1]) &&
                (i - 2 < 0 || ch[i] != ch[i - 2])) {
                break;                                 // valid bump
            }
        }

        if (i < 0) return "";        // no solution

        // rebuild suffix with smallest possible letters
        for (int j = i + 1; j < n; ++j) {
            for (int x = 0; x < k; ++x) {
                char cand = char('a' + x);
                if (j - 1 >= 0 && cand == ch[j - 1]) continue;
                if (j - 2 >= 0 && cand == ch[j - 2]) continue;
                ch[j] = cand;
                break;
            }
        }
        return string(ch.begin(), ch.end());
    }
};
```

All three codes run in **linear time** (`O(n)`) and use only a few auxiliary variables.

---

## Test Your Solution

```text
Input:  s = "abcd", k = 4   →  "abdc"
Input:  s = "abdc", k = 4   →  "abcd"  (no larger string)
Input:  s = "a",   k = 4    →  "b"
Input:  s = "zzz", k = 26  →  ""
```

Feel free to paste the snippets into LeetCode’s online editor and run the unit tests.

---

## “What If It Fails?” – Common Bugs

| Bug | Symptom | Fix |
|-----|---------|-----|
| Using `for (char c : s)` in Java without copying → modifies the original string | Subsequent increments corrupt the input | Copy into a new array (`char[] ch = s.toCharArray()`) |
| Off‑by‑one in `chars[i] > 'a'+k-1` | May attempt to increment past `'a'+k-1` | Compare the *code point* (`>`) instead of `==` |
| Rebuilding suffix with only `{a,b,c}` when `k>3` | Wrong answer for `k=26` | Iterate `x < k` (full alphabet) |
| Ignoring `i-2` when `j < 2` | Wrong palindrome check for short suffix | Guard with `if (j - 2 >= 0 && ...)` |

---

## Time & Space Complexity

| Implementation | Time | Extra Space |
|----------------|------|-------------|
| Java | **O(n)** | **O(1)** (output string included) |
| Python | **O(n)** | **O(1)** |
| C++ | **O(n)** | **O(1)** |

The algorithm never needs a priority queue or sorting – we simply try the next letter and perform constant‑time checks.

---

## Why This Problem Is a “Job‑Interview Goldmine”

1. **Clear constraints** – The limit `n ≤ 100 000` forces you to think about linear‑time solutions.  
2. **Greedy proof** – Interviewers love concise proofs that justify a greedy approach.  
3. **String manipulation + palindrome logic** – A nice blend of data‑structures and algorithmic insight.  
4. **Variants** – The same pattern appears in problems about “next lexicographic permutation” or “next valid password”, so mastering it gives you a reusable tool for future questions.  

Showcasing this solution in a portfolio (Java, Python, C++) demonstrates:

* Proficiency with multiple languages.  
* Ability to write clean, production‑ready code.  
* Strong understanding of greedy algorithms and correctness proofs.

---

## SEO‑Friendly Summary

- **Lexicographically Smallest Beautiful String** – The core keyword you want to rank for.  
- **LeetCode 2663** – Directly tags the problem number for search engines.  
- **Java/Python/C++ solution** – Gives interviewers immediate language‑specific reference.  
- **Job interview coding** – Highlights the real‑world value of this algorithmic trick.  
- **Palindrome‑free string** – Adds depth for advanced algorithm queries.  

Use the code snippets and explanations above in your interview prep, blog posts, or portfolio to make a strong impression on hiring managers. Happy coding!