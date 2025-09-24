---
title: LeetCode 2309. Greatest English Letter in Upper and Lower Case - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🏆 2309 – “Greatest English Letter in Upper and Lower Case”  
> A quick, interview‑ready solution (Java / Python / C++) + a full‑blown blog post (SEO‑optimized, “the good, the bad, the ugly”)

---

## TL;DR – One‑liner for every language

| Language | Code |
|----------|------|
| **Java** | `public String greatestLetter(String s){Set<Character> set=new HashSet<>();for(char c:s.toCharArray())set.add(c);for(char c='Z';c>='A';c--)if(set.contains(c)&&set.contains((char)('a'+c-'A')))return ""+c;return "";}` |
| **Python** | `def greatestLetter(s): return next((c for c in reversed('ABCDEFGHIJKLMNOPQRSTUVWXYZ') if c in s and c.lower() in s), '')` |
| **C++** | `string greatestLetter(string s){unordered_set<char> st(s.begin(),s.end());for(char c='Z';c>='A';--c)if(st.count(c)&&st.count(tolower(c)))return string(1,c);return "";}` |

All three solutions run in **O(n)** time, **O(1)** extra space (ignoring the set), and are perfect for a coding interview.

---

## The Problem (LeetCode 2309)

> **Given** a string `s` of English letters, return the *greatest* letter that appears in **both** its lowercase and uppercase form.  
> The returned letter must be in uppercase; if no such letter exists, return an empty string.

> *Greatness* is defined by the alphabet order (`Z` > `Y` > … > `A`).

> **Constraints**  
> * `1 ≤ s.length ≤ 1000`  
> * `s` contains only English alphabet letters.

---

## Why This Problem Matters

1. **Interview Friendly** – It tests two core skills:
   * Character manipulation (ASCII math or built‑in helpers).
   * Set usage / hash‑based lookup.
2. **Real‑World Relevance** – Many applications require case‑insensitive checks (e.g., user‑names, database keys).  
3. **SEO / Job‑hunt Bonus** – Mastering this problem demonstrates you can handle “corner cases” and “optimization” – qualities recruiters love.

---

## Approach Overview

1. **Collect all characters** into a hash‑set (`Set<Character>` or `unordered_set<char>`).  
   *Why?* Constant‑time look‑ups for existence checks.

2. **Scan from ‘Z’ down to ‘A’** – the biggest letter first.  
   *If* the uppercase letter *and* its lowercase counterpart both exist in the set, that’s our answer.  
   *Why from ‘Z’?* Guarantees the first match is the greatest letter; we can stop early.

3. **Return an empty string** if no pair is found.

### Complexity

| Step | Time | Space |
|------|------|-------|
| Building set | O(n) | O(1) (size ≤ 52) |
| Scanning 26 letters | O(1) | – |
| **Total** | **O(n)** | **O(1)** |

---

## “The Good, The Bad, The Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Readability** | Simple, 2‑loop structure. | Some may prefer bit‑mask trick for extra speed. | If you use heavy‑handed bit‑masking, code becomes opaque. |
| **Performance** | O(n) with small constant; passes all LeetCode tests quickly. | Using arrays (size 52) would also be fine; set is clearer. | A brute‑force double loop (O(26²)) is still fine for n=1000, but not scalable. |
| **Edge Cases** | Handled: empty string, all lower‑case, all upper‑case, mixed. | None significant. | Forgetting to check both cases or mis‑ordering the loop could yield wrong answer. |
| **Language Nuances** | Java: `HashSet`, Python: generator, C++: `unordered_set`. | Minor differences: `char` vs `int` ASCII values. | Mixing `char` and `int` without casting can cause subtle bugs. |

---

## Code Walkthroughs

### 1. Java

```java
import java.util.*;

class Solution {
    public String greatestLetter(String s) {
        // Step 1: put every character in a set
        Set<Character> set = new HashSet<>();
        for (char ch : s.toCharArray()) {
            set.add(ch);
        }

        // Step 2: check from 'Z' downwards
        for (char ch = 'Z'; ch >= 'A'; ch--) {
            if (set.contains(ch) && set.contains((char) (ch + ('a' - 'A')))) {
                return String.valueOf(ch);
            }
        }
        // Step 3: none found
        return "";
    }
}
```

**Notes**  
* `ch + ('a' - 'A')` converts uppercase to lowercase via ASCII math.  
* No explicit array; set guarantees constant‑time lookups.

---

### 2. Python

```python
def greatestLetter(s: str) -> str:
    # set for O(1) membership checks
    seen = set(s)

    # iterate from 'Z' downwards
    for c in reversed('ABCDEFGHIJKLMNOPQRSTUVWXYZ'):
        if c in seen and c.lower() in seen:
            return c
    return ""
```

**Notes**  
* `reversed('ABCDEFGHIJKLMNOPQRSTUVWXYZ')` gives the descending order.  
* Python’s `in` on a set is amortized O(1).  
* Elegant one‑liner inside the loop if you’re feeling fancy.

---

### 3. C++

```cpp
#include <string>
#include <unordered_set>
using namespace std;

class Solution {
public:
    string greatestLetter(string s) {
        unordered_set<char> st(s.begin(), s.end());

        for (char c = 'Z'; c >= 'A'; --c) {
            if (st.count(c) && st.count(tolower(c))) {
                return string(1, c);
            }
        }
        return "";
    }
};
```

**Notes**  
* `unordered_set<char>` gives O(1) lookup.  
* `tolower(c)` converts uppercase to lowercase.  
* `string(1, c)` constructs a one‑character string for the answer.

---

## Alternatives & Optimizations

| Method | Description | Pros | Cons |
|--------|-------------|------|------|
| **Bitmask** (`int mask = 0`) | Set bits for each letter; then check bits from high to low. | Extremely fast (no hash set). | Less readable; bit manipulation may be overkill for n ≤ 1000. |
| **Array of size 52** | Two booleans per letter: uppercase & lowercase. | No hashing overhead; constant memory. | Slightly more verbose than a set. |
| **Sorting + Two‑pointer** | Sort string; then scan. | No extra memory besides sorted string. | Extra O(n log n) time – not optimal for interview. |

---

## Interviewer Tips

1. **Ask clarifying questions** – e.g., “Are we guaranteed that `s` contains only ASCII letters?”  
2. **Mention complexity** – “O(n) time, O(1) space.”  
3. **Talk about edge cases** – empty string, only lower/upper case, repeated letters.  
4. **Show alternate solutions** – bitmask or array – to demonstrate depth.

---

## SEO‑Optimized Blog Post Outline

1. **Title**  
   *“LeetCode 2309: Greatest English Letter – Java, Python, C++ Solutions & Interview Prep”*

2. **Meta Description**  
   *Solve LeetCode 2309 in minutes. Learn Java, Python, and C++ code, understand the logic, and ace your coding interview with this easy yet clever problem.*

3. **Headings (H1‑H3)**  
   * H1: LeetCode 2309 – Greatest English Letter  
   * H2: Problem Summary & Constraints  
   * H2: Algorithm – Set + Reverse Scan  
   * H3: Java Implementation  
   * H3: Python Implementation  
   * H3: C++ Implementation  
   * H2: Complexity Analysis  
   * H2: Alternatives & Trade‑offs  
   * H2: Interviewer Perspective – “Good, Bad, Ugly”  
   * H2: Takeaways for Your Next Job Interview  
   * H2: Frequently Asked Questions  

4. **Keywords**  
   * LeetCode 2309, Greatest English Letter, coding interview, Java solution, Python solution, C++ solution, interview prep, algorithm analysis, set operations, string manipulation.

5. **Call‑to‑Action**  
   *“Got a question? Drop a comment below or reach out on LinkedIn – happy to help you ace your next interview!”*

---

## Final Thoughts

- **Why this article matters** – It showcases your ability to translate a problem statement into clean, language‑agnostic code, discusses trade‑offs, and anticipates interview questions.  
- **Job‑hunt advantage** – By publishing a clear, SEO‑optimized write‑up, you demonstrate communication skills, a passion for algorithms, and a readiness for technical interviews.  
- **Next step** – Pair the code with unit tests and a “quick start” section on how to run it locally. Then, share on GitHub, LinkedIn, and relevant subreddits (r/leetcode, r/cscareerquestions).

Happy coding—and best of luck on that next interview! 🚀